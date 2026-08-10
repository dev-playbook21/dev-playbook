## Interview Discussion

**1. Why was Zipkin chosen as the tracing backend?**
Zipkin integrates directly with Micrometer Tracing's Brave bridge with minimal configuration, is lightweight to run alongside a small set of Spring Boot services, and provides a clear waterfall/trace-tree UI that was sufficient for the visibility this project needed. For a project of this scale, a heavier collector/backend stack was not justified.

**2. Why Micrometer Tracing instead of instrumenting with OpenTelemetry directly?**
Micrometer Tracing is Spring Boot's native tracing abstraction and is auto-configured by Spring Boot starters, meaning it integrates with Spring MVC, WebClient, and OpenFeign with almost no manual setup. Since the stack is Spring Boot end-to-end, using the framework's native tracing facade was the lower-friction, better-supported choice over wiring OpenTelemetry's SDK independently.

**3. Why Brave specifically, rather than another tracer implementation?**
Brave is the default bridge Micrometer Tracing uses for B3 propagation and Zipkin reporting. It required no additional bridging code and is the well-trodden path for Spring Boot plus Zipkin, which reduced integration risk.

**4. What is the difference between logs and traces?**
Logs are discrete, timestamped statements emitted by a single process with no inherent structural relationship to other processes' logs. Traces are structured, hierarchical records of a request's execution across multiple processes, explicitly linked by Trace ID and parent-child Span IDs. Logs tell you *what happened* inside one service; traces tell you *how a request moved through the whole system*.

**5. What is a Trace ID?**
A single identifier assigned to a request the first time it enters the system, preserved unchanged across every service it touches, used to group all spans belonging to that one logical request in Zipkin.

**6. What is a Span ID?**
An identifier for one discrete unit of work — such as one inbound HTTP request or one outbound Feign call — within a trace. A single service typically produces multiple spans per request handled.

**7. What is the difference between a parent span and a child span?**
A parent span represents the caller's unit of work; a child span represents the work done by whatever the parent called. For example, the User Service's outbound Feign call to Rating Service is the parent of the span Rating Service creates to handle that inbound request.

**8. How does trace context actually propagate between services in this project?**
Brave's HTTP client/server instrumentation injects B3 headers (Trace ID, Span ID, sampling flag) into outbound Feign requests automatically, and reads those same headers on the receiving side to join the existing trace rather than starting a new one. This happens at the instrumentation layer, not in application code.

**9. Why does OpenFeign specifically need tracing instrumentation, rather than it working automatically?**
Because Feign is an HTTP client, and trace context does not survive a network hop unless something explicitly serializes it into request headers on the way out and reads it back in on the way in. Micrometer/Brave's Feign instrumentation is what performs this injection and extraction automatically.

**10. What would happen if one service in the chain were not instrumented?**
The trace would fragment. Any service upstream of the uninstrumented one would produce a trace that stops there, and the uninstrumented service's downstream calls (if any) would start a brand-new, disconnected trace rather than continuing the original one, since it never received or forwarded the original Trace ID.

**11. What performance overhead does tracing introduce, and why is it acceptable?**
Overhead comes from span creation, header propagation on existing calls, and reporting to Zipkin. Reporting is asynchronous and does not block the request path, and propagation only adds a small amount of header data to calls that were already happening. Exact overhead was not formally benchmarked in isolation, but the asynchronous design keeps it from being visible on the client-facing latency path, which is why the tradeoff is acceptable for production use.

**12. Why does tracing matter specifically in a production environment, rather than just in development?**
In development, issues can usually be reproduced locally with a debugger attached. In production, the request that failed is gone by the time you're investigating, traffic is concurrent, and you cannot attach a debugger to a live service. Tracing gives you a durable, queryable record of exactly what happened for that specific request, after the fact.

**13. What specific bottlenecks or issues did tracing reveal in this project?**
The clearest example was during the Hotel Service failure test: tracing made it possible to see precisely how much time was consumed by retries before the circuit breaker opened, and to confirm that the breaker's short-circuit behavior was actually taking effect, rather than inferring it indirectly from response codes alone.

**14. How does tracing interact with the OAuth2 resource server layer at the Gateway?**
Token validation happens at the Gateway before routing. Since tracing spans are only created for requests that proceed past that point, a rejected/unauthenticated request naturally does not generate a full downstream trace — the root span at the Gateway reflects the rejection, and no child spans exist for services the request never reached.

**15. How does the circuit breaker's open state show up in a trace?**
Once Resilience4j's circuit breaker opens, subsequent calls to the failing dependency short-circuit immediately rather than attempting the network call and waiting for a timeout. This appears in the trace as the corresponding span duration dropping sharply and consistently, since the call is no longer actually being attempted.

**16. How are retries represented in the trace tree, and why does that matter?**
Each retry attempt by Resilience4j produces its own span nested under the same parent Feign call span. This makes retry behavior directly observable — you can see exactly how many attempts were made and how long each took — rather than having to infer retry counts from log line frequency.

**17. What is the practical difference between network latency and processing time, and how is it identified in a trace?**
Network latency is the gap between when the calling service's span records the call as sent and when the receiving service's span records it as received; processing time is the duration of the receiving service's own span. Separating these matters because they point to different fixes — infrastructure/network tuning versus application-level optimization.

**18. Why is asynchronous span reporting to Zipkin important architecturally?**
If reporting were synchronous, every request would incur additional latency proportional to the time it takes to send span data to Zipkin, and a slow or unavailable Zipkin instance could directly degrade client-facing performance. Asynchronous reporting decouples observability infrastructure health from request-serving performance.

**19. How would you explain the value of this tracing setup to someone who has never seen it, in one sentence?**
It turns "the `/users` endpoint is slow" into a specific, per-request answer for exactly which service and which call within the Gateway → User Service → Rating Service → Hotel Service chain is responsible, without needing to add logging or reproduce the issue.

**20. What would be the next step to make this observability setup more production-grade?**
Layering in metrics (Prometheus/Grafana) and centralized logging alongside the existing traces, so that traces, metrics, and logs can be correlated together rather than tracing being the only signal available — covered further below.
