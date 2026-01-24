 Statelessness
## Definition
**Statelessness** means the server **does not store client session state between requests**.  
Each request must include **all information needed** to process it (e.g., auth token, parameters).

## Why it matters
- Easier **horizontal scaling** (any request can go to any server)
- Better reliability and simpler load balancing
- Encourages clear API boundaries