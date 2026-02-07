**99ile** means the **99th percentile**, usually written as **p99**.

### What it means in load testing
If sort all request latencies from fastest to slowest:
- p99 latency is the value where **99% of requests are at or below that latency**
- The remaining **1%** are slower (the “tail”)
### Example
If send 100,000 requests and **p99 = 120 ms**, that means:
- at least 99,000 requests finished in ≤ 120 ms
- the slowest 1,000 requests took > 120 ms
### Why it matters
- **p99** captures those “occasionally very slow” experiences (tail latency), which is important for real users.
- Reveal if your system is **stable** under load or if it has “random slow spikes,”
- Compare to **average (mean)** can look fine even if a small number of requests are very slow.  