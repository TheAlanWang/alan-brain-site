## IP Suite Layer (TCP/IP Model)
### Data link layer
- specifies communications across a single network segment. Implemented by device drivers and network cards inside nodes.
- (e.g., Ethernet, Wi-Fi)
### Internet (IP) layer
- specifies addressing and routing protocols for traffic to traverse the independently managed networks that comprise the internet.
### Transport (TCP, UDP) layer
- specifies protocols for reliable (TCP) and best-effort (UDP) host-to-host communications.
### Application layer
- specifies application-level protocols
- (e.g., HTTP/HTTPS, DNS, FTP, SMTP)

Application layer → Transport (TCP, UDP) layer →Internet (IP) layer → Data link layer


### TCP VS USP (Transport layer)
| Feature            | TCP (Transmission Control Protocol)                                                 | UDP (User Datagram Protocol)                                          |
| ------------------ | ----------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| Reliability        | Reliable; guarantees delivery and in-order arrival                                  | Best-effort; no delivery or ordering guarantees                       |
| Connection state   | Connection-oriented; requires a [[TCP-IP#three-way handshake\|three-way handshake]] | Connectionless; no handshake                                          |
| Control mechanisms | Flow control and congestion control                                                 | No built-in flow control or congestion control                        |
| Overhead           | Higher overhead; prioritizes reliability                                            | Lower overhead; lightweight and often lower latency                   |
| Typical use cases  | File transfer, email, web documents (HTTP/1.1, HTTP/2)                              | Streaming media, video calls, online games (and QUIC/HTTP/3 over UDP) |

#### Encapsulation and Delivery (TCP/IP Model)
##### Data Units by Layer
App message → TCP segment / UDP datagram → IP packet → frame
- **Application:** message (e.g., HTTP, DNS)
- **Transport:**
    - **TCP:** segment (byte-stream; reliable)
    - **UDP:** UDP datagram (message-oriented; best-effort)
- **Internet:** **IP packet**
- **Link:** frame (per hop)
##### TCP Path (Reliable, ordered byte stream)
TCP (Transmission Control Protocol)
1. Application creates a **message**.
2. TCP treats data as a **byte stream** and **segments** it (often sized by **MSS**).
3. Each TCP segment is encapsulated into an IP packet.
4. On each hop, the IP packet is carried inside a **link-layer frame** (MAC addresses change per hop).
5. At the destination:
    - IP passes payload to TCP.
    - TCP **reassembles**, ensures **reliability + ordering** (retransmissions if needed), then delivers bytes to the application.

**Key idea:** Routers forward **IP packets**; reliability/ordering is **end-to-end (TCP)**, not done by routers.
##### UDP Path (Best-effort message delivery)
UDP (User Datagram Protocol)
1. Application creates a **message**.
2. UDP wraps it into a **UDP datagram** (no retransmission/ordering guarantees).
3. The UDP datagram is carried inside an IP packet and forwarded hop-by-hop.
4. If it’s too large for the path MTU:
    - **IPv4:** fragmentation may occur (sometimes by routers).
    - **IPv6:** routers don’t fragment; the sender must avoid oversized packets.
    - If any fragment is missing, reassembly fails and the **whole packet is dropped**.

**Key idea:** UDP keeps transport simple; apps must tolerate loss (or implement reliability at the app layer, e.g., QUIC over UDP).

## TCP Connection Establishment
### three-way handshake
1) Client → Server: SYN
	Client sends: `SYN = 1`, `seq = x` (client’s ISN)
	- optional TCP options: MSS, window scale, SACK permitted, timestamps
		- Client state: **CLOSED → SYN-SENT**
	- Meaning: “I want to connect. My starting sequence number is x.”
2) Server → Client: SYN + ACK
	- Server replies: `SYN = 1`, `ACK = 1`, `seq = y` (server’s ISN), `ack = x + 1` (acknowledges client’s SYN)
	- optional TCP options
		- Server state: **LISTEN → SYN-RECEIVED**
	- Meaning: “I got your SYN (ack = x+1). I’m ready too. My starting sequence number is y.”
3) Client → Server: ACK
	- Client sends: `ACK = 1` `seq = x + 1` (because SYN used up 1), `ack = y + 1` (acknowledges server’s SYN)
		- Client state: **SYN-SENT → ESTABLISHED**

When the server receives this ACK:  Server state: SYN-RECEIVED → ESTABLISHED
Meaning: “I got your SYN as well (ack = y+1). Connection is established.”

```pgsql
Client                              Server
  | -- SYN, seq=x -----------------> |
  | <--- SYN+ACK, seq=y, ack=x+1 --- |
  | -- ACK, seq=x+1, ack=y+1 ------> |
         (ESTABLISHED on both sides)
eg:
- Client → Server: `SYN seq=1000`
- Server → Client: `SYN+ACK ack=1001` 
  SYN consumes 1 sequence number, so `ack = x + 1` is expected.
```

