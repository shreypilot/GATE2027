# Computer Networks — Complete GATE Syllabus & Overview

**Typical GATE Weightage:** ~7 – 9 Marks

---

## 📌 Core Modules & Key High-Yield Topics

### Module 1: Physical Layer & Transmission Fundamentals
* Network Topologies: Star, Ring, Mesh, Bus.
* Delay Calculations:
  $$\text{Total Latency} = \text{Transmission Delay } (L/B) + \text{Propagation Delay } (d/v) + \text{Queuing Delay} + \text{Processing Delay}$$
* Bandwidth-Delay Product (BDP) and Channel Capacity (Nyquist Bit Rate & Shannon Capacity).

### Module 2: Data Link Layer & MAC Sublayer
* **Framing & Error Detection:** Bit stuffing, Byte stuffing, Checksum, CRC (Cyclic Redundancy Check polynomial division).
* **Flow Control (Sliding Window Protocols):**
  * Stop-and-Wait: Efficiency $\eta = \frac{1}{1 + 2a}$, where $a = \frac{T_p}{T_t}$.
  * Go-Back-N (GBN): Window size $N$, sequence numbers $\ge N + 1$.
  * Selective Repeat (SR): Window size $N$, sequence numbers $\ge 2N$.
* **Medium Access Control (MAC):**
  * Pure ALOHA ($\eta_{max} = 18.4\%$) vs Slotted ALOHA ($\eta_{max} = 36.8\%$).
  * CSMA/CD: Minimum frame size condition $L_{min} = 2 \times T_p \times B$. Exponential backoff algorithm.

### Module 3: Network Layer & IP Addressing
* **IPv4 Header:** Identification, Flags (DF, MF), Fragment Offset (in units of 8 bytes), TTL.
* **IP Addressing & Subnetting:**
  * Classful vs Classless (CIDR) Addressing.
  * Subnet masks, network ID, direct broadcast address, usable host addresses.
  * Supernetting / Route Aggregation.
* **Routing Algorithms:**
  * Distance Vector Routing (Bellman-Ford, Count-to-Infinity problem, Split Horizon).
  * Link State Routing (Dijkstra's Algorithm, OSPF).
  * Border Gateway Protocol (BGP) concepts.
* **Protocols:** ARP, RARP, ICMP, DHCP, NAT.

### Module 4: Transport Layer
* **TCP vs UDP:** Connection-oriented vs Connectionless, Header formats.
* **TCP Connection Management:** 3-Way Handshake, SYN/ACK packets, Connection termination.
* **TCP Congestion Control:**
  * Slow Start (Exponential growth).
  * Congestion Avoidance (Additive Increase).
  * Fast Retransmit and Fast Recovery (Triple duplicate ACKs vs Timeout).

### Module 5: Application Layer & Network Security
* **Application Protocols:** DNS (Iterative vs Recursive queries), HTTP/HTTPS, SMTP, POP3, IMAP, FTP.
* **Cryptography & Security:**
  * Symmetric vs Asymmetric Encryption.
  * RSA Algorithm: Public/Private key generation, modular arithmetic.
  * Digital Signatures & Message Digests (SHA, MD5).
