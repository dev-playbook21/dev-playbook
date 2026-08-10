## Interview Questions

**1. How did you identify the bottleneck?**
By first ruling out the business services — their logs showed no equivalent failures — and then examining the Zipkin process logs directly, which showed a repeated `OutOfMemoryError: Java heap space` that correlated in time with the observed instability.

**2. Why was Zipkin consuming so much memory?**
Because every request through the four-service chain generates multiple spans, and under the concurrency k6 was driving, the volume of spans Zipkin had to hold and process in memory exceeded what its configured JVM heap could sustain.

**3. Why heap tuning as the fix, rather than something else first?**
Because the root cause was identified as a memory capacity mismatch, not a structural or logical flaw in the tracing pipeline. Heap tuning is a low-risk, fast, reversible change that directly targets that root cause and can be validated immediately with the same test that exposed the problem.

**4. Would horizontal scaling of Zipkin have solved this instead?**
It could plausibly help by distributing span ingestion load across multiple instances, but it is a larger architectural change with more moving parts (load balancing, shared storage considerations) than a heap adjustment. Since the simpler fix directly addressed the confirmed root cause, it was tried first; horizontal scaling remains a reasonable next step if load grows beyond what vertical heap tuning can sustain.

**5. Why not just remove or reduce tracing to avoid this problem entirely?**
Tracing is what gives the team visibility into request flow, latency attribution, and failure propagation across the service chain — removing it reintroduces exactly the debugging blind spots tracing was built to solve. The incident was caused by under-provisioning the tracing infrastructure, not by tracing being inherently unnecessary; the correct fix is sizing the infrastructure to the load, not eliminating the capability.

**6. How would Kubernetes resource limits affect this scenario?**
If Zipkin were running in Kubernetes with a memory limit set lower than what `-Xmx` allows the JVM to request, the container could be OOM-killed by Kubernetes independently of (or in addition to) the JVM's own OutOfMemoryError — two different mechanisms hitting a similar symptom. Any heap tuning done here would need to be paired with a container memory limit set comfortably above `-Xmx` (accounting for non-heap JVM memory), or the same instability could resurface at the container orchestration layer even after JVM-level tuning.

**7. What is the difference between `-Xms` and `-Xmx`?**
`-Xms` sets the heap size the JVM allocates at startup; `-Xmx` sets the maximum the heap is allowed to grow to. Setting both explicitly avoids relying on platform-default sizing and gives predictable, deliberate memory behavior.

**8. Why did average latency increase slightly after the fix?**
A larger heap means the JVM can, and sometimes will, spend more time per garbage collection cycle when collections occur, even though those cycles happen less often and less catastrophically than under the previous undersized configuration. This shows up as a small latency cost in exchange for eliminating outright failures.

**9. Why do production systems generally accept that kind of latency tradeoff?**
Because a slightly slower successful request is almost always preferable to a failed one, especially when the failure mode is a crash in shared infrastructure that affects every request passing through it, not just an isolated slow request.

**10. How did you distinguish an application bottleneck from an infrastructure bottleneck here?**
By checking whether the business services (User Service, Rating Service, Hotel Service) showed any equivalent errors or degradation in their own logs during the same test window. They did not — only Zipkin did — which pointed the investigation at the observability tier specifically.

**11. What would have happened if you had tuned the business services' JVM heap instead?**
It likely would not have resolved the incident, since those services were not the ones exhausting memory. It would have consumed investigation and deployment effort without addressing the actual root cause, which is why confirming where the bottleneck lived before acting was an important step.

**12. Why does span volume increase memory pressure on Zipkin specifically?**
Zipkin has to receive, process, and (depending on storage backend) hold spans in memory as part of ingesting them. Higher concurrency in the system produces more spans per unit time, and if the ingesting process's heap is not sized for that throughput, memory pressure builds until the JVM can no longer allocate what it needs.

**13. Was this incident caused by a memory leak in Zipkin?**
No evidence pointed to a leak specifically — the pattern (progressive degradation under rising concurrency, resolved by increasing available heap) is consistent with a capacity/throughput mismatch rather than memory that should have been reclaimed but wasn't. A leak would typically continue growing even under steady, non-increasing load, which was not the observed pattern.

**14. How would you monitor for this proactively in the future, instead of discovering it via an OOM error?**
By instrumenting the Zipkin process itself with JVM memory metrics (heap usage, GC frequency/duration) exported to a metrics system, with alerting on sustained high heap utilization — turning this from a reactive log-discovery problem into a proactive, dashboard-visible one.

**15. What role did k6 play in surfacing this issue?**
k6 was used to drive realistic concurrent load against the platform. The incident only manifested at higher concurrency, which is exactly the condition k6's load profile was designed to simulate — without that load testing, this capacity issue would likely have surfaced first in production instead.

**16. Why is it important that the same k6 test profile was used for both the original failure and the validation run?**
Using an identical test profile isolates the heap change as the variable that changed between the "before" and "after" results. Comparing against a different or lighter load would not have validated that the fix actually holds under the same conditions that caused the original failure.

**17. What are the risks of setting `-Xmx` too high?**
An excessively high maximum heap can lead to longer individual garbage collection pauses when large collections do occur, and can also over-commit memory relative to what the host or container actually has available, risking the process (or others sharing the host) being starved or OOM-killed at the OS/container level instead of the JVM level.

**18. If this had happened in production instead of a load test, what would the immediate impact have been?**
Zipkin becoming unresponsive would degrade or interrupt tracing visibility platform-wide, and depending on how tightly span reporting is coupled to request handling in each service's configuration, it could also risk contributing to request failures — making the underlying business incident harder to diagnose at the exact moment observability was most needed.

**19. How does this incident change how you'd approach capacity planning going forward?**
Supporting infrastructure — tracing backends, log aggregators, metrics collectors — needs to be included in load testing and capacity planning explicitly, rather than assumed to scale adequately alongside the business services simply because it was configured with reasonable-looking defaults.

**20. What would you do next if the same heap ceiling was exceeded again at even higher concurrency?**
Revisit the architectural options that were deferred in favor of the faster fix here — horizontal scaling of Zipkin, introducing a buffering/queueing layer in front of span ingestion, or evaluating an alternate storage backend — since repeated heap exhaustion at higher load would indicate the vertical tuning approach has reached its practical ceiling.
