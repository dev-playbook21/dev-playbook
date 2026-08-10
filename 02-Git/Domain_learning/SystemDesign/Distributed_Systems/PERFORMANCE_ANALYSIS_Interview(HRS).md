## Interview Questions

### Architecture & Design

**1. Why does this system use Eureka for service discovery instead of hardcoded service URLs?**
Hardcoded URLs couple the caller to a specific instance's network location, which breaks under horizontal scaling, instance replacement, or dynamic infrastructure (containers, autoscaling). Eureka allows services to register themselves and be discovered by logical name, with the client-side load balancer distributing calls across all healthy registered instances. The tradeoff is added operational complexity (running and maintaining a highly-available registry) and a staleness window between an instance failing and it being evicted from the registry.

**2. What is the role of the Config Server, and what happens if it is unavailable at service startup?**
The Config Server centralizes externalized configuration so that config changes don't require rebuilding service artifacts. If unavailable at startup, a dependent service will typically fail to start (or start with defaults/fail health checks) depending on `fail-fast` configuration — this is a deliberate reliability tradeoff: centralizing config creates a new critical dependency that must itself be made highly available.

**3. Why does each service have its own database instead of a shared database?**
Database-per-service enforces service autonomy — each team can evolve their schema independently and choose the most appropriate storage engine — but sacrifices the ability to do cross-service JOINs, pushing aggregation logic into the application layer, and introduces the possibility of transient data inconsistency across services (eventual rather than immediate consistency for cross-service views).

**4. Why is MongoDB used for the Rating Service specifically, rather than a relational database?**
Rating/review data tends to be semi-structured and may vary in shape (different rating dimensions, free-text reviews, varying metadata), which fits a document model more naturally than a rigid relational schema, and MongoDB's write-heavy, flexible-schema characteristics suit a service that primarily ingests and serves discrete review documents rather than performing complex relational queries.

**5. How would you redesign `GET /users` to improve performance?**
The most direct redesign is to parallelize the Rating Service and Hotel Service calls (via `CompletableFuture` or a reactive stack) so total downstream latency becomes bounded by the slowest call rather than the sum of both. A further redesign would introduce caching for relatively static hotel metadata, and consider whether the aggregation should happen at the gateway/BFF layer rather than inside User Service, to avoid coupling User Service to the response shapes of two unrelated domains.

### Concurrency & Performance

**6. Why is sequential aggregation slower than parallel aggregation?**
Because sequential calls execute one after another on the same thread, total latency is the sum of each call's duration. Parallel calls execute concurrently (on separate threads or via non-blocking I/O), so total latency is bounded by the single slowest call rather than the cumulative total — the gap between these models widens as more independent dependencies are added.

**7. Would `CompletableFuture` help here, and what would you watch out for?**
Yes — wrapping the Rating and Hotel Service Feign calls in `CompletableFuture.supplyAsync()` and combining them with `thenCombine`/`allOf` would let both calls execute concurrently. The key risk is using the default `ForkJoinPool.commonPool()` for this, which is shared across the JVM and can starve other async work; a dedicated, appropriately-sized executor should back these futures instead.

**8. Would Virtual Threads (Project Loom) improve this system's performance?**
Virtual threads primarily help with *throughput under high concurrency* by making blocking calls cheap (a blocked virtual thread doesn't pin a scarce platform/OS thread), rather than reducing the latency of any single request. They would meaningfully raise the concurrency ceiling described in the Sequential Aggregation Analysis without requiring a rewrite to reactive style, but they do not, by themselves, parallelize the two sequential downstream calls — that still requires explicit concurrent invocation.

**9. Would migrating to WebFlux (reactive) improve performance, and what's the cost?**
WebFlux's non-blocking I/O model would free up threads during I/O waits, improving throughput under high concurrency similarly to virtual threads, and would naturally express parallel composition via `Mono.zip`/`Flux` operators. The cost is a steep shift in programming model (functional, non-blocking style throughout the call chain, including database drivers), increased debugging complexity, and a requirement that every dependency in the chain also be non-blocking to realize the full benefit — partial adoption yields partial benefit.

**10. What is the risk of increasing thread pool size to handle more concurrent requests under the current blocking model?**
It delays, but does not remove, the scalability ceiling — more threads can be in-flight simultaneously, but each is still occupied for the full duration of the sequential downstream chain, and excessive thread counts introduce their own costs (memory per thread stack, increased context-switching, and higher pressure on downstream services which must now handle more concurrent connections).

**11. How would you identify whether the User Service, Rating Service, or Hotel Service is the actual latency contributor in a slow composite request?**
Distributed tracing (Zipkin) is designed exactly for this: each span in a trace records the duration of an individual hop, so the trace waterfall for a slow request would show which specific downstream span dominates total duration, distinguishing "slow because of Rating Service" from "slow because of Hotel Service" from "slow because of network/serialization overhead between them."

### Caching & Data

**12. When should caching be introduced into this system, and where?**
Caching is appropriate when data is read far more often than it changes and slight staleness is tolerable — hotel metadata (rarely changes) and aggregate rating scores (can tolerate short staleness) are strong candidates. It should be introduced at the point of read (e.g. in front of the Feign call, or within Hotel/Rating Service themselves) once profiling shows repeat reads of the same entities are a meaningful share of traffic — introducing it prematurely, without evidence of read-heavy repeat access, adds invalidation complexity for little gain.

**13. What cache invalidation strategy would you use for hotel metadata, and why?**
A time-based expiry (TTL) is simplest and appropriate for data that changes infrequently and where brief staleness is acceptable; an event-driven invalidation (publishing a "hotel updated" event that evicts the relevant cache entry) is more precise but requires an event/message infrastructure. The choice depends on how strict the staleness tolerance is versus the cost of building event-driven invalidation.

**14. Would bulk retrieval endpoints help, and when would you introduce them?**
They become necessary specifically when a single caller needs data for *multiple* entities at once (e.g. a list view showing many users' composite profiles) — without a bulk endpoint, this degenerates into an N+1 pattern of one remote call per entity, which multiplies the existing sequential-aggregation cost by N. A batch endpoint accepting a list of IDs and returning results in one round-trip avoids this.

**15. How would introducing a read replica for the Hotel Service's PostgreSQL database affect performance?**
It would offload read traffic from the primary, improving read latency and throughput under read-heavy load, at the cost of potential replication lag (read-after-write inconsistency if a client reads from a replica immediately after a write to the primary) and additional operational complexity in routing reads vs. writes correctly.

### Messaging & Async Communication

**16. When would you introduce Kafka (or another message broker) into this system?**
When a piece of work does not need to complete before a response is returned to the client — for example, recording a "view" event for analytics, sending a notification, or updating a denormalized read-model asynchronously. Introducing a broker decouples the producer from needing the consumer to be available or fast, improving both latency and resilience for those flows, but is not a fit for the core synchronous `GET /users` read path, which needs an immediate response.

**17. What is the difference between using Resilience4j's circuit breaker and using async messaging to handle a slow downstream dependency?**
A circuit breaker changes how the caller *reacts* to a slow/failing synchronous dependency (fail fast instead of hanging) but does not change the fact that the call is on the critical path when the circuit is closed. Async messaging removes the dependency from the critical path entirely by not requiring a synchronous response at all — they solve different problems and are often used together for different parts of a system.

**18. Would GraphQL help this system's aggregation problem?**
GraphQL would let the client specify exactly which fields it needs across the User/Rating/Hotel domains in one request, and a well-implemented GraphQL layer with data loaders can batch and parallelize the underlying resolver calls, addressing some of the same N+1 and over-fetching problems. It does not, however, automatically solve the sequential-aggregation problem — that still depends on how resolvers are implemented (parallel batching via DataLoader vs. naive sequential resolution) — and it introduces its own complexity (schema design, resolver N+1 pitfalls, caching complexity at the field level).

### Resilience & Fault Tolerance

**19. What does the circuit breaker in Resilience4j actually protect against, and what doesn't it fix?**
It protects the calling service from wasting resources (threads, connections) on calls to a downstream service that is known to be failing, by "opening" after a failure threshold and failing fast instead of waiting for the timeout on every call. It does not fix the underlying performance/scalability issue of sequential aggregation, nor does it fix the downstream service's actual problem — it only limits blast radius on the caller's side.

**20. How would you configure retries in this system without making things worse under load?**
Retries should be bounded (limited attempts), use exponential backoff with jitter to avoid synchronized retry storms across many callers, and should be paired with a circuit breaker so that retries stop entirely once a dependency is confirmed to be failing — naive unbounded or tight-loop retries under load can amplify load on an already-struggling downstream service and worsen an outage.

**21. Why is distributed tracing (Zipkin) particularly important in this architecture compared to a monolith?**
In a monolith, a stack trace and application logs are usually sufficient to localize a performance problem, because everything executes in one process. In a distributed system, a single logical request spans multiple processes and network calls, so without a trace correlating all the spans under one trace ID, it's very difficult to determine which service or hop is responsible for latency or failure in a given request.

### Scaling & Infrastructure

**22. How would Kubernetes change the scaling story for this system?**
Kubernetes would automate what Eureka-based discovery handles more manually — pod autoscaling (HPA) based on CPU/memory/custom metrics could scale service instance counts up or down automatically in response to load, and Kubernetes' own service discovery/DNS could potentially replace or complement Eureka. It would also standardize deployment, health-checking, and rolling updates, though the core sequential-aggregation latency problem would remain unaffected by infrastructure changes alone — it requires the application-level fixes described in the roadmap.

**23. If this system needed to serve 100x its current traffic, what would you change first, and why?**
Parallel aggregation first, since it is the lowest-effort, highest-impact change and directly attacks the bottleneck that scales worst with load (thread occupancy under sequential blocking calls). After that, caching for read-heavy, low-volatility data (hotel metadata) to reduce the number of downstream calls entirely, followed by horizontal scaling of each service now that per-request thread occupancy is reduced.

**24. What's the difference between scaling this system vertically vs. horizontally, and which is more appropriate here?**
Vertical scaling (bigger instances) increases the resources available to a single instance but does nothing to address the structural thread-occupancy problem under sequential blocking calls, and has a hard ceiling. Horizontal scaling (more instances behind the load balancer) is more appropriate for a stateless microservices architecture like this one, since it aligns with how Eureka-based discovery and client-side load balancing already work — but its benefit is capped until the sequential-aggregation bottleneck is addressed at the application level.

**25. How would you decide between fixing sequential aggregation in code versus just adding more service instances?**
Adding instances (horizontal scaling) increases total capacity but does not change the *per-request* latency or the fact that each instance's threads are inefficiently occupied waiting on sequential calls — it's treating a symptom. Fixing sequential aggregation in code (parallel calls, async, virtual threads) improves the efficiency of every existing instance, which is both cheaper (no additional infrastructure cost) and addresses the root cause; the two are complementary, but code-level fixes should generally come first since they improve the economics of any subsequent scaling.
