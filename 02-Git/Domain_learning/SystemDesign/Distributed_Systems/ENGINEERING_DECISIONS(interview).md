## Interview Questions

The following questions are organized by the same topics covered above. Each answer is written as an implementation-level engineering response, not a textbook definition.

### Microservices & System Design

**1. Why did you split this system into services instead of building a modular monolith?**
Because the three domains (identity, catalog, reviews) have materially different scaling profiles and change rates, and because a modular monolith only *simulates* boundary enforcement through code convention — nothing stops a developer from importing across "module" boundaries in a single deployable. A network boundary between services is a boundary that must be deliberately crossed via an API contract, which is a stronger guarantee than a package-private convention.

**2. How do you decide where a service boundary should go?**
Along the same axis as data ownership: each service should own the data it is the single source of truth for, and boundaries should follow domains that change independently. Here, ratings, hotel catalog data, and user identity each have distinct read/write patterns and distinct reasons to change, which is why they're separate services rather than, say, splitting by technical layer (e.g., a "read service" vs. "write service").

**3. What's the biggest risk you accepted by choosing microservices for this system at this scale?**
Operational overhead disproportionate to current load — six components to run, monitor, and deploy for a system that, at low traffic, a monolith could serve with a fraction of the operational burden. That tradeoff was accepted deliberately because the goal includes demonstrating distributed-systems engineering competence, not purely minimizing operational cost at current scale.

**4. How would you migrate this system back toward a monolith if traffic never justified the microservices overhead?**
Because each service already exposes a clean API boundary and owns its own data, the services could be consolidated into fewer deployables by combining their code and, more carefully, their data (which requires a real data migration, not just code consolidation) — the API contracts between the former services can be kept internally as module boundaries even after consolidation, preserving some of the original discipline.

### API Gateway

**5. Why not let clients call each service directly?**
Because that exposes internal topology to every client, meaning any internal change (adding a service, relocating one, changing instance counts) becomes a client-facing breaking change, and it forces every client to independently implement auth handling correctly rather than trusting one centrally maintained enforcement point.

**6. What happens if the Gateway goes down — isn't that a worse single point of failure than a monolith?**
Yes, in principle, which is why the Gateway itself must run as multiple instances behind its own load balancing rather than as a single process — the difference from a monolith is that the Gateway is thin (routing and cross-cutting concerns only), so it's cheaper to make highly available than an entire monolith would be, and its failure mode is "system unreachable" rather than "system is running incorrect business logic."

**7. Where should authentication be enforced — Gateway, individual services, or both?**
Both, as defense-in-depth: the Gateway performs an initial check so obviously invalid requests are rejected before consuming any backend resources, but each resource server independently validates the JWT as well, because trusting the Gateway's word alone would mean a single bypass at the Gateway compromises every service behind it.

**8. How would you implement rate limiting at the Gateway without it becoming a bottleneck itself?**
Use Resilience4j's `RateLimiter` (or an equivalent) configured per-client or per-route rather than a single global limiter, and keep the rate-limiting state itself either in-memory per Gateway instance (fast, but not perfectly accurate across instances) or in a fast shared store like Redis if precise cross-instance limits are required — the choice depends on whether approximate limiting is acceptable for the use case.

### Service Discovery (Eureka)

**9. Why does the system need Eureka instead of static configuration, given how few services there are?**
Even with few services, any service that can be redeployed, rescaled, or moved to a new host breaks static configuration the moment that happens — the value of Eureka isn't proportional to service *count*, it's proportional to how often instance locations change, which happens on every deployment regardless of how many services exist.

**10. Eureka is AP, not CP — what does that mean practically for this system?**
It means Eureka favors staying available and returning a (possibly slightly stale) list of instances over refusing to answer during a network partition; practically, this means a caller might occasionally be routed to an instance that has just gone down, which is why callers must not treat "Eureka said this instance is healthy" as a guarantee — they must still handle call failures via Resilience4j regardless.

**11. What happens if a service instance crashes without deregistering cleanly?**
Eureka relies on the heartbeat mechanism, not a graceful shutdown signal, to detect this — after a configured number of missed heartbeats, the instance is evicted from the registry. Until eviction completes, some requests may still be routed to the dead instance and will fail, which is exactly the gap that client-side timeouts and retries need to cover.

**12. How would you make Eureka itself highly available?**
Run a cluster of Eureka nodes that peer-replicate their registries with each other, and point every client (via configuration) at the full list of Eureka nodes rather than a single instance, so client-side failover to another Eureka node happens automatically if one node is unreachable.

### Configuration (Spring Cloud Config)

**13. Why not just use environment variables for configuration instead of a Config Server?**
Environment variables work for a handful of values but don't scale to structured, hierarchical configuration, have no version history or diffing, and can't be centrally audited — Config Server gives every service structured config with the same review and rollback discipline as source code, because it's backed by Git.

**14. What happens if the Config Server is down when a service tries to start?**
Without additional handling, the dependent service fails to start, since it has no configuration to boot with — this is why production deployments typically configure a local fallback/cached config or a fail-fast-with-retry startup strategy rather than a hard, unretried dependency on Config Server's availability at the exact moment of boot.

**15. How do you handle a configuration change that should take effect without restarting a service?**
Use `@RefreshScope` on the beans that consume the configuration, and trigger a refresh (via the Actuator `/actuator/refresh` endpoint or, at scale, Spring Cloud Bus broadcasting the refresh event to every instance) — without this, a running service will keep using the configuration it loaded at startup even after the underlying Git value changes.

**16. Why is it wrong to store database passwords directly in the Config Server's Git repository?**
Because Git history retains every value ever committed, even after a later commit changes or removes it — a leaked or rotated secret can't simply be "removed" from the repository's history without a disruptive history rewrite, so secrets belong in a system designed for that (a vault with proper access control and rotation), not in version-controlled plaintext.

### Inter-Service Communication (OpenFeign)

**17. Why Feign instead of just using `RestTemplate` directly in each service?**
`RestTemplate` requires hand-writing URL construction, serialization, and error handling for every call site, none of which is reusable without building an abstraction — which is what Feign already is. Feign turns an inter-service call into an interface method, keeping business logic free of HTTP plumbing.

**18. What's the risk of treating a Feign call exactly like a local method call in your code?**
The risk is forgetting it can fail in ways a local call cannot — timeout, partial response, connection refusal — and writing calling code that assumes it always returns quickly and successfully; this is exactly why Feign clients here are wrapped with Resilience4j rather than called bare.

**19. How does trace context survive a Feign call from Rating Service to User Service?**
The tracing instrumentation registers an interceptor on outgoing Feign requests that injects the current trace and span headers before the request is sent, and the receiving service's instrumentation reads those headers to continue the same trace rather than starting a new one — this is what keeps a single logical request visible as one connected trace in Zipkin across the hop.

**20. What happens if User Service changes its response schema in a way Rating Service's Feign client doesn't expect?**
Deserialization fails or silently drops fields at runtime, not compile time, since the Feign client interface is only checked against the code, not against the real live contract of the service it calls — this gap is the argument for consumer-driven contract testing as a future improvement, so a breaking schema change is caught in CI before deployment.

### Resilience (Resilience4j)

**21. Walk through what happens end-to-end when User Service becomes slow.**
Rating Service's Feign calls to User Service start taking longer; if a per-call timeout is configured, calls eventually time out rather than hanging indefinitely; repeated failures within the sliding window push the circuit breaker's failure rate above its threshold, opening the circuit; once open, further calls fail fast (or return a fallback) without attempting the network call at all, protecting Rating Service's own thread pool from being exhausted by slow calls; after a configured wait duration, the breaker moves to half-open and allows a small number of trial calls to check if User Service has recovered.

**22. Why not retry every failed call automatically?**
Because retrying a non-idempotent operation (e.g., a call that creates a resource) can cause duplicate side effects if the original call actually succeeded but the response was lost — retry policies must be scoped to calls that are safe to repeat, and backoff must be used so retries don't amplify load on an already-struggling dependency.

**23. What's the difference between what Retry and Circuit Breaker each protect against, and why do you need both?**
Retry handles transient, short-lived failures (a single dropped packet, a momentary blip) by trying again quickly; Circuit Breaker handles sustained failure by stopping calls altogether once failures are no longer transient — using only Retry against a genuinely down dependency just multiplies load against it, while using only Circuit Breaker means a single transient blip could unnecessarily trip toward failure without ever getting a quick second attempt.

**24. How do you decide the circuit breaker's failure-rate threshold and wait duration for a given client?**
Based on the call's criticality and the downstream service's normal failure baseline: a threshold set too low trips on normal transient noise (false positives), one set too high fails to protect the caller until real damage is already occurring; the wait duration should be long enough to give the downstream service a real chance to recover but short enough that the caller isn't unnecessarily degraded longer than needed — this is tuned empirically against observed traffic and failure patterns, not chosen arbitrarily.

**25. What should a Feign client's fallback actually return when the circuit is open?**
That depends on the call, and is a product decision as much as an engineering one: for a non-critical enrichment call (e.g., fetching a user's display name to attach to a review), a sensible fallback might be a placeholder or cached value so the primary operation still succeeds; for a critical call (e.g., verifying the user exists before creating a review), the fallback may need to be a clear failure rather than silently proceeding with unverified data.

### Observability (Zipkin)

**26. How would you find the root cause of a slow "submit review" request using this system's tracing setup?**
Look up the trace for that request in Zipkin, which shows every span across the Gateway, Rating Service, and any downstream call (e.g., to User Service) with its individual duration — the span with disproportionate duration relative to the others identifies which specific hop is the bottleneck, rather than having to guess or manually correlate separate service logs.

**27. What's the tradeoff of tracing 100% of requests versus sampling?**
100% tracing gives complete visibility but has real cost — both the overhead of exporting every span and the storage cost of retaining that trace volume — while sampling (tracing only a percentage of requests) reduces that cost but means some individual incidents may not have a corresponding trace, which is a real limitation during a low-frequency, hard-to-reproduce issue.

### Authentication (OAuth2 Resource Server)

**28. Why does each service validate the JWT itself instead of asking a central auth service on every request?**
Because a network round-trip to a central auth service on every single request would reintroduce the exact synchronous, availability-coupled dependency that stateless JWT validation is meant to avoid — by validating the token's signature locally (against a cached public key/JWKS), each service can authenticate a request without any additional network call, keeping the system resilient to the identity provider being briefly unavailable for already-issued tokens.

**29. How do you handle revoking access for a user immediately, given that JWTs are stateless?**
Pure stateless JWTs can't be revoked before expiry by design, so immediate revocation requires an additional mechanism layered on top — commonly, short-lived access tokens paired with a refresh token that can be invalidated centrally, or a fast-lookup revocation/denylist check for genuinely high-sensitivity actions where the statelessness tradeoff isn't acceptable.

### Data Layer (Polyglot Persistence)

**30. How do you implement a query like "average rating per hotel" when ratings live in MongoDB and hotels live in PostgreSQL?**
It can't be done as a single database join across engines — it has to be composed at the application layer: Rating Service computes or exposes aggregate rating data per hotel ID via its own API, and the caller (e.g., Hotel Service or a client) joins that with hotel data in memory, or, at higher scale, a dedicated read-optimized aggregation store is built specifically for this cross-domain query rather than computing it live on every request.

**31. What happens to referential integrity between a hotel ID stored in Rating Service and the actual hotel record in Hotel Service?**
There is no database-level foreign key across separate database engines, so referential integrity becomes an application-level concern: Rating Service must validate a hotel ID exists (via a call to Hotel Service, ideally resilience-wrapped) before accepting a rating for it, and the system must accept that this validation happens at write time rather than being continuously enforced by the database itself.
