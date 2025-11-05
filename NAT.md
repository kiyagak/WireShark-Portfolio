# Wireshark Lab: NAT

## Objective

The goal is to
- investigate the behavior of a **NAT (Network Address Translation) router**.
- use **two capture points**
	- **Inside** the home LAN  
	- **Outside** on the Internet-facing side of the NAT  
- use **pre-captured trace files**
	- `nat-inside-wireshark-trace1-1.pcapng` → LAN side  
	- `nat-outside-wireshark-trace1-1.pcapng` → Internet side  
---

## Trace Files  
| File | Location | Description |
|------|---------|-----------|
| `nat-inside-wireshark-trace1-1.pcapng` | LAN side of NAT | Packets before/after NAT translation |
| `nat-outside-wireshark-trace1-1.pcapng` | Internet side | Packets after NAT (outgoing), before NAT (incoming) |

> **Download**:  
> `http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip`

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
Let's see how **NAT changes IP addresses and port numbers** in transit.

---

## Lab Instructions & Questions  

### Part 1: Analyze LAN-Side Trace (`nat-inside-wireshark-trace1-1.pcapng`)

1. **What is the IP address of the client** that sends the HTTP GET request?  
- **Source IP**:  `192.168.10.11`
- **Source Port**:  `53924`
- **Destination IP**: `138.76.29.8`  
- **Destination Port**:  `80`
       
       Internet Protocol Version 4, Src: 192.168.10.11, Dst: 138.76.29.8
           Source Address: 192.168.10.11
           Destination Address: 138.76.29.8
       
       Transmission Control Protocol, Src Port: 53924, Dst Port: 80, Seq: 1, Ack: 1, Len: 330
           Source Port: 53924
           Destination Port: 80

<img width="1242" height="766" alt="image" src="https://github.com/user-attachments/assets/b106a187-147c-479a-b21d-ae952aaa7684" />

2. **At what time** (relative to trace start) is the **HTTP 200 OK** message **forwarded by the NAT router to the client** on the LAN side?  
- **Time**:  `0.003309856` seconds

       [Time since request: 0.003309856 seconds]

<img width="1242" height="766" alt="image" src="https://github.com/user-attachments/assets/4ad222e2-91eb-4f1f-bce6-cd021367d9ef" />

3. For the **IP datagram carrying the HTTP 200 OK** on the LAN side:  
- **Source IP**: `138.76.29.8`
- **Source Port**: `80`
- **Destination IP**: ` 192.168.10.11`
- **Destination Port**: `53924`

       Internet Protocol Version 4, Src: 138.76.29.8, Dst: 192.168.10.11
           Source Address: 138.76.29.8
           Destination Address: 192.168.10.11
       
       Transmission Control Protocol, Src Port: 80, Dst Port: 53924, Seq: 1, Ack: 331, Len: 547
           Source Port: 80
           Destination Port: 53924

<img width="1242" height="766" alt="image" src="https://github.com/user-attachments/assets/d3d944ce-d783-4940-a799-485da720c0cd" />

---

### Part 2: Analyze Internet-Side Trace (`nat-outside-wireshark-trace1-1.pcapng`)  

> **Note**: Timestamps are **not synchronized** between the two trace files.  
> Locate the **same HTTP GET** sent at `t = 0.27362245` in the *inside* trace.

4. **At what time** does this **HTTP GET** appear in the *outside* trace?  
- **Time**: `0.027356291`

6. For the **outgoing IP datagram carrying the HTTP GET** (Internet side):  
- **Source IP**: `10.0.1.254` 
- **Source Port**: `53924`
- **Destination IP**: `138.76.29.8`  
- **Destination Port**: `80`

       4	0.027356291	10.0.1.254	138.76.29.8	HTTP	396	GET / HTTP/1.1 
       
       Internet Protocol Version 4, Src: 10.0.1.254, Dst: 138.76.29.8
           Source Address: 10.0.1.254
           Destination Address: 138.76.29.8
       
       Transmission Control Protocol, Src Port: 53924, Dst Port: 80, Seq: 1, Ack: 1, Len: 330
           Source Port: 53924
           Destination Port: 80

7. **Which of the four fields** (Src IP, Src Port, Dst IP, Dst Port) **differ** from your answer in **Question 1**?  
- The most notable field that has changed is the **source IP** address, with the IP address of `10.0.1.254`.  
       - Source IP: `138.76.29.8` → `10.0.1.254`
       - Source Port: `80` → `53924`
       - Destination IP: ` 192.168.10.11` → `138.76.29.8`
       - Destination Port: `53924` → `80`

9. **Are any fields in the HTTP GET message itself changed?**  
- No

       4	0.027362245	192.168.10.11	138.76.29.8	HTTP	396	GET / HTTP/1.1

       Hypertext Transfer Protocol
           GET / HTTP/1.1\r\n
               [Expert Info (Chat/Sequence): GET / HTTP/1.1\r\n]
               Request Method: GET
               Request URI: /
               Request Version: HTTP/1.1
           Host: 138.76.29.8\r\n
           User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:75.0) Gecko/20100101 Firefox/75.0\r\n
           Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8\r\n
           Accept-Language: en-US,en;q=0.5\r\n
           Accept-Encoding: gzip, deflate\r\n
           Connection: keep-alive\r\n
           Upgrade-Insecure-Requests: 1\r\n
           \r\n
           [Full request URI: http://138.76.29.8/]
           [HTTP request 1/2]
           [Response in frame: 6]
           [Next request in frame: 8]

       4	0.027356291	10.0.1.254	138.76.29.8	HTTP	396	GET / HTTP/1.1 
       
       Hypertext Transfer Protocol
           GET / HTTP/1.1\r\n
               [Expert Info (Chat/Sequence): GET / HTTP/1.1\r\n]
               Request Method: GET
               Request URI: /
               Request Version: HTTP/1.1
           Host: 138.76.29.8\r\n
           User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:75.0) Gecko/20100101 Firefox/75.0\r\n
           Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8\r\n
           Accept-Language: en-US,en;q=0.5\r\n
           Accept-Encoding: gzip, deflate\r\n
           Connection: keep-alive\r\n
           Upgrade-Insecure-Requests: 1\r\n
           \r\n
           [Full request URI: http://138.76.29.8/]
           [HTTP request 1/2]
           [Response in frame: 6]
           [Next request in frame: 8]


9. Which **IP header fields** are changed from inside → outside?
- [ ] Version  
- [ ] Header Length  
- [ ] Flags  
- [x] Checksum  

       4	0.027362245	192.168.10.11	138.76.29.8	HTTP	396	GET / HTTP/1.1 
       
       Internet Protocol Version 4, Src: 192.168.10.11, Dst: 138.76.29.8
           0100 .... = Version: 4
           .... 0101 = Header Length: 20 bytes (5)
           010. .... = Flags: 0x2, Don't fragment
           Header Checksum: 0x64dc [validation disabled]
       
       4	0.027356291	10.0.1.254	138.76.29.8	HTTP	396	GET / HTTP/1.1 
       
       Internet Protocol Version 4, Src: 10.0.1.254, Dst: 138.76.29.8
           0100 .... = Version: 4
           .... 0101 = Header Length: 20 bytes (5)
           010. .... = Flags: 0x2, Don't fragment
           Header Checksum: 0x2492 [validation disabled]

---

### Part 3: Analyze HTTP 200 OK on Internet Side  

9. **At what time** does the **HTTP 200 OK** reply appear in the *outside* trace?  
   → **Time**: `0.030625966`

10. For the **incoming IP datagram carrying HTTP 200 OK** (Internet → NAT):  
    - **Source IP**: `138.76.29.8`  
    - **Source Port**: `80`
    - **Destination IP**: `10.0.1.254`
    - **Destination Port**: `53924`

			6	0.030625966	138.76.29.8	10.0.1.254	HTTP	613	HTTP/1.1 200 OK  (text/html)
			Internet Protocol Version 4, Src: 138.76.29.8, Dst: 10.0.1.254
			Transmission Control Protocol, Src Port: 80, Dst Port: 53924, Seq: 1, Ack: 331, Len: 547

---

### Part 4: Predict NAT Translation (No Trace Needed)  

11. After NAT translates the **HTTP 200 OK** datagram, what are the fields **on the LAN side** (forwarded to client)?  
    - **Source IP**: `138.76.29.8`
    - **Source Port**: `80`
    - **Destination IP**: `192.168.10.11`
    - **Destination Port**: `53924`

> **Verify**: Open `nat-inside-wireshark-trace1-1.pcapng` and confirm your answer matches the actual packet.

<img width="1242" height="766" alt="image" src="https://github.com/user-attachments/assets/105dd6d2-9024-479d-a9f9-84c1afa03da3" />

---

## What I Learned

I learned that
- NAT rewrites **source IP/port** on outbound and **destination IP/port** on inbound  
