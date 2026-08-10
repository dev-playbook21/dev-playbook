# Load Testing --- Interview Case Handling

## If the interviewer asks: "How did you determine the system's capacity?"

I did not treat the load test as a claim of absolute system capacity. I
treated it as a controlled benchmark campaign to observe how the system
behaved as concurrency and execution duration increased.

The benchmark covered 30, 100, 200, 250, 275 and 300 VUs with multiple
durations.

### What we observed

  Workload            Avg      P95   Success   Failed
  --------------- ------- -------- --------- --------
  30 VUs / 10s      0.63s   0.993s      100%        0
  100 VUs / 30s     1.83s    4.55s      100%        0
  200 VUs / 30s     3.41s    4.30s      100%        0
  250 VUs / 60s     4.53s    5.14s      100%        0
  250 VUs / 80s     4.19s    5.27s    90.69%      456
  275 VUs / 30s     5.14s    7.19s      100%        0
  275 VUs / 60s     4.98s    5.90s    98.63%       47
  275 VUs / 80s     4.35s    5.49s    90.21%      507
  300 VUs / 30s     4.82s    7.88s    88.28%      236
  300 VUs / 60s     5.27s    6.10s    96.87%      112
  300 VUs / 80s     4.86s    6.18s    97.59%      122

## The key conclusion

The important observation was not simply that latency increased.

The system maintained 100% success through the lower and mid-range
workloads, but reliability degraded as the workload became more
demanding. The first observed failure in the campaign occurred at **250
VUs for 80 seconds**.

At **300 VUs**, failures were already present in the 30-second run.

Therefore, I would describe the result as:

> "In this benchmark campaign, the system demonstrated reliable
> behaviour through the tested 250 VU / 60s configuration. Longer
> execution at 250 VUs introduced failures, while 300 VUs produced
> failures even at 30 seconds. I would not call these absolute capacity
> limits because each configuration was executed once; they are observed
> operating points from this benchmark."

## If asked: "Why did you use both VUs and duration?"

Because concurrency and duration expose different behaviour.

-   Increasing **VUs** increases concurrent pressure.
-   Increasing **duration** tests whether the system remains stable
    under sustained pressure.

For example, 275 VUs remained at 100% success for 30 seconds but dropped
to 98.63% at 60 seconds and 90.21% at 80 seconds. That showed that
duration itself was an important part of the failure behaviour.

## If asked: "Why are there graphs?"

The graphs make the benchmark easier to reason about:

-   **Average response time** --- overall latency movement.
-   **P95 latency** --- tail-latency behaviour.
-   **Success rate** --- reliability degradation.
-   **Failed requests** --- magnitude of observed failures.

They are not decorative charts; each one answers a different engineering
question.

## If asked: "What did you do after seeing failures?"

The load test showed **where** reliability degraded, but it did not by
itself explain **why**.

The next step was therefore distributed tracing and runtime
investigation:

**Load test → identify degradation → inspect Zipkin traces → inspect
runtime behaviour → correlate evidence.**

That is why the benchmark section is followed by the tracing and runtime
analysis.

## Important interview discipline

Do not say:

> "The system supports 250 VUs."

Say:

> "The system maintained 100% success in the tested 250 VU / 60s
> configuration."

Do not say:

> "300 VUs is the maximum capacity."

Say:

> "300 VUs produced failures in every tested duration in the campaign,
> so it was outside the reliably observed operating range."

### One-line summary

> **"The benchmark was used to find the system's observed degradation
> point, not to claim a theoretical maximum capacity; once degradation
> appeared, I used tracing and runtime evidence to investigate the
> underlying behaviour."**
