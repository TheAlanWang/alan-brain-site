### API Definition
An Application Protocol Interface (API) Definition as a **Contract**
- Operations and Payloads: use APIs to outline **operations** (like "login" or "transfer") and the data **payloads** a server must process.
- [[04-Tech/01-Fundations/Network/Network Basic/API#Application-Specific Protocols|Application-Specific Protocols]]: By defining these operations and payloads, the API is essentially used to create an application-specific protocol.
- Layer Placement: In the network stack, these protocols and their APIs reside in the [[TCP-IP#Application layer|Application Layer]], which handles high-level protocols like HTTP and FTP

### Application-Specific Protocols
An application-specific protocol is essentially a set of custom-defined request and response rules, usually represented using text formats (such as JSON) or binary formats.
> Definition: An Application Protocol Interface (**API**) is used to define a specific protocol for an application by outlining the various **operations** a server supports (such as "login," "transfer," "balance," or "withdraw") and the **payload** the server must process to fulfill those requests

### Evolution from RPC API to HTTP API
**RMI/RPC APIs** are often **tightly coupled**, meaning any change to the API requires all clients to be recompiled.

**HTTP APIs** (which have largely replaced RMI in practice) are **loosely coupled** and **language-independent**. Changing an HTTP API does not require recompiling all clients, as the client is not directly invoking an internal server method.

• **Level of Abstraction:** While APIs for standard transport protocols like **TCP and UDP** are widely available in programming languages, they are frequently hidden from applications by higher-level abstractions such as **HTTP** or **WebSockets**.