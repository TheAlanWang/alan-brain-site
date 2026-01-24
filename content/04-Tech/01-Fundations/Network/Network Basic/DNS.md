### DNS (Domain Naming System)
> Definition: Domain Name System is a **distributed**, hierarchical naming system that translates human-readable domain names (like `www.google.com`) into **IP** addresses (like `142.250.x.x`) and other records (e.g., MX, CNAME), so clients can locate services on the Internet.
- Layer Placement: [[TCP-IP#Application layer|Application Layer]]

### DNS Lookup
When your computer needs to resolve a domain name (for example, `www.google.com`) and no cached result is available, it performs a **recursive DNS lookup**.

Your computer does **not** contact many DNS servers by itself. Instead, it sends the request to a **recursive resolver** (such as your ISP’s DNS server or a public resolver like `8.8.8.8`). The resolver does the work on your behalf:

1. Query the **Root DNS servers**  
    The resolver asks:  “Which servers handle the `.com` domain?”
    The root servers reply with a list of TLD (.com) DNS servers.
2. Query the `.com` **TLD DNS servers**  
    The resolver asks:  “Which authoritative DNS servers are responsible for `google.com`?”
    The TLD servers reply with the **authoritative name servers** for `google.com`.
3. Query the **Authoritative DNS server**
    The resolver asks:  “What is the IP address (A/AAAA record) for `www.google.com`?”
    The authoritative server replies with one or more IP addresses.
4. **Return and cache** the result
    The resolver returns the IP address to your computer and **caches the result** according to its TTL (Time To Live), so future lookups are faster.

### `nslookup` Tool

```zsh
alanwang@Alans-MacBook-Pro ~ % nslookup www.google.com
Server:		2600:1700:65a0:10a0::1
Address:	2600:1700:65a0:10a0::1#53

Non-authoritative answer:
Name:	www.google.com
Address: 64.233.185.106
Name:	www.google.com
Address: 64.233.185.147
```