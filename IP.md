# Wireshark Lab: IP

## Objective

The goal is to analyze **IPv4** and **IPv6** datagrams uasing Wireshark, focusing on:
1. **Traceroute** behavior (TTL, ICMP responses)
2. **IP fragmentation**
3. **IPv6 structure**

---

## **Lab Structure (3 Parts)**

### **Part 1: Basic IPv4 (Traceroute Analysis)**
**Goal:** Examine IPv4 datagrams sent/received during `traceroute`.

#### **Steps:**
1. Open the Terminal.
2. Install the `traceroute` package.

         sudo apt install traceroute

3. Run Wireshark → Start capture.
4. Execute:
   ```bash
   traceroute gaia.cs.umass.edu 56
   traceroute gaia.cs.umass.edu 3000
   ```
   *(Windows: use `tracert` for small packets only)*
5. Stop capture.

#### **Key Questions (1–12):**
- Source IP, TTL, protocol field, header/payload size
- Fragmentation check
- Fields that **change** (e.g., TTL, Identification) vs. **stay constant** (e.g., src/dst IP)
- ICMP TTL-exceeded replies from routers
- Use filters:
  - `udp || icmp`
  - `ip.src==yourIP && ip.dst==128.119.245.12 && udp && !icmp`
  - `ip.dst==yourIP && icmp`

---

### **Part 2: IP Fragmentation**
**Goal:** Study fragmentation of a **3000-byte** traceroute packet.

#### **Steps:**
- Sort packets by time.
- Locate first large UDP segment (e.g., packet 179 in trace file).
- Observe **3 fragments** (packets 179–181).

#### **Key Questions (13–19):**
- Fragmentation flag (`More Fragments` bit)
- `Fragment Offset` field
- `Total Length`, `Identification`, changes between fragments
- Identify **first**, **middle**, and **last** fragment

> *Note: 3000-byte packet exceeds typical MTU → forces fragmentation.*

---

### **Part 3: IPv6**
**Goal:** Explore IPv6 using pre-captured trace (`ip-wireshark-trace2-1.pcapng`).

#### **Focus:**
- DNS AAAA query (packet 20) to resolve `youtube.com` → IPv6
- IPv6 response with multiple AAAA records

#### **Key Questions (20–26):**
- Source & destination **IPv6 addresses** (exact format)
- **Flow Label**, **Payload Length**, **Next Header**
- Number of IPv6 addresses returned
- First (smallest) IPv6 address in shorthand notation

---

## **Resources & Tips**
- **Review**: Textbook §4.3 (IP), §4.3.4 (IPv6), RFC 791, RFC 8200
- **Fragmentation**: See 7th ed. §4.3.2 (PDF link provided)
- **Trace Files** (download if needed):
  - `ip-wireshark-trace1-1.pcapng` → Parts 1 & 2
  - `ip-wireshark-trace2-1.pcapng` → Part 3
- **Filters** are critical for navigation

---

## What I Learned
