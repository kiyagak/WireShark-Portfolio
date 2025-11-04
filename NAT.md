# Wireshark Lab: NAT v9  
**Supplement to *Computer Networking: A Top-Down Approach*, 9th ed., J.F. Kurose and K.W. Ross**  

> “Tell me and I forget. Show me and I remember. Involve me and I understand.” — *Chinese proverb*  
> © 2005–2025, J.F. Kurose and K.W. Ross, All Rights Reserved  

---

## Overview  
In this lab, we investigate the behavior of a **NAT (Network Address Translation) router**. Unlike other Wireshark labs that capture packets at a single point, this lab requires **two capture points**:  
- **Inside** the home LAN  
- **Outside** on the Internet-facing side of the NAT  

We use **pre-captured trace files** (no live capture needed):  
- `nat-inside-wireshark-trace1-1.pcapng` → LAN side  
- `nat-outside-wireshark-trace1-1.pcapng` → Internet side  

**Prerequisite**: Review **Section 4.3.3 (NAT)** in the textbook.

---

## NAT Measurement Scenario  

```
[Home LAN: 192.168.10.0/24] → [NAT Router] → [Internet] → [Web Server: 138.76.29.8]
       ↑                            ↑
  Capture Point 1              Capture Point 2
  (inside trace)               (outside trace)
```

A client in the home network sends an **HTTP GET** request to `138.76.29.8`.  
The server responds with **HTTP 200 OK**.  
We analyze how **NAT modifies IP addresses and port numbers** during traversal.

---

## Trace Files  
| File | Location | Description |
|------|---------|-----------|
| `nat-inside-wireshark-trace1-1.pcapng` | LAN side of NAT | Packets before/after NAT translation |
| `nat-outside-wireshark-trace1-1.pcapng` | Internet side | Packets after NAT (outgoing), before NAT (incoming) |

> **Download**:  
> `http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip`

---

## Lab Instructions & Questions  

### Part 1: Analyze LAN-Side Trace (`nat-inside-wireshark-trace1-1.pcapng`)

1. **What is the IP address of the client** that sends the HTTP GET request?  
   → **Source IP**:  
   → **Source Port**:  
   → **Destination IP**: `138.76.29.8`  
   → **Destination Port**:  

2. **At what time** (relative to trace start) is the **HTTP 200 OK** message **forwarded by the NAT router to the client** on the LAN side?  
   → **Time**:  

3. For the **IP datagram carrying the HTTP 200 OK** on the LAN side:  
   - **Source IP**:  
   - **Source Port**:  
   - **Destination IP**:  
   - **Destination Port**:  

---

### Part 2: Analyze Internet-Side Trace (`nat-outside-wireshark-trace1-1.pcapng`)  

> **Note**: Timestamps are **not synchronized** between the two trace files.  
> Locate the **same HTTP GET** sent at `t = 0.27362245` in the *inside* trace.

4. **At what time** does this **HTTP GET** appear in the *outside* trace?  
   → **Time**:  

5. For the **outgoing IP datagram carrying the HTTP GET** (Internet side):  
   - **Source IP**:  
   - **Source Port**:  
   - **Destination IP**: `138.76.29.8`  
   - **Destination Port**:  

6. **Which of the four fields** (Src IP, Src Port, Dst IP, Dst Port) **differ** from your answer in **Question 1**?  
   → List changed fields:  

7. **Are any fields in the HTTP GET message itself changed?**  
   → Yes / No → If yes, which?  

8. Which **IP header fields** are changed from inside → outside?  
   - [ ] Version  
   - [ ] Header Length  
   - [ ] Flags  
   - [ ] Checksum  

---

### Part 3: Analyze HTTP 200 OK on Internet Side  

9. **At what time** does the **HTTP 200 OK** reply appear in the *outside* trace?  
   → **Time**:  

10. For the **incoming IP datagram carrying HTTP 200 OK** (Internet → NAT):  
    - **Source IP**: `138.76.29.8`  
    - **Source Port**:  
    - **Destination IP**:  
    - **Destination Port**:  

---

### Part 4: Predict NAT Translation (No Trace Needed)  

11. After NAT translates the **HTTP 200 OK** datagram, what are the fields **on the LAN side** (forwarded to client)?  
    - **Source IP**:  
    - **Source Port**:  
    - **Destination IP**:  
    - **Destination Port**:  

> **Verify**: Open `nat-inside-wireshark-trace1-1.pcapng` and confirm your answer matches the actual packet.

---

## Final Check  
- [ ] All answers match observed packets in both trace files  
- [ ] You understand how NAT rewrites **source IP/port** on outbound and **destination IP/port** on inbound  

---

**That’s it!**  
A short, insightful lab to **see NAT in action** using Wireshark.  

---  
*Textbook Website*: [http://gaia.cs.umass.edu/kurose_ross](http://gaia.cs.umass.edu/kurose_ross)  
*LMS Auto-Graded Labs*: [http://gaia.cs.umass.edu/kurose_ross/lms.htm](http://gaia.cs.umass.edu/kurose_ross/lms.htm)
