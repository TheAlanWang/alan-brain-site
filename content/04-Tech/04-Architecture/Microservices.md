**Microservices** is an architecture where you build an application as many small, **independent** services.
### Why choose microservices over a monolith?
Microservices fit systems that need to **evolve** and **scale**. By splitting the application into small, cohesive services, teams can **deploy independently** and **scale only the bottleneck service** instead of scaling the entire system.

## Trade-offs
### Operational complexity (the big cost)
- **More communication overhead:** network latency, timeouts, partial failures
- Harder **debugging:** issues span multiple services (requires tracing)
- Harder **data** consistency: distributed data and coordination
### How to manage the trade-offs
- Strong **DevOps + platform** practices: CI/CD, monitoring/alerting, centralized logging, distributed tracing
- Clear **service boundaries** + good API contracts
- Reliability basics: timeouts, retries, circuit breakers (where appropriate)