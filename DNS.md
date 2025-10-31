# Client-Side DNS Exploration (nslookup, Caching, Wireshark)

## Objective

The goal is to follow Kuross and Ross' [DNS Wireshark lab](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_DNS_v9.pdf) to
- investigate DNS client behavior (query/response)
- use the nslookup tool
- learn about local DNS caching
- look at the DNS packet structure using Wireshark

---

### **1. nslookup Basics**
- **Purpose**: Query DNS servers for records (e.g., `Type=A` for IP, `Type=NS` for name servers, reverse lookup).
- **Command**: `nslookup [options] <name>`
  - Default: Queries local DNS server.
  - `-type=NS <domain>` → Returns authoritative name servers.
  - Reverse lookup: `nslookup <IP>` → Returns hostname.
- **Example Outputs**:
  - `www.nyu.edu` → IPv4/IPv6 addresses + responding server.
  - `nyu.edu` with `-type=NS` → Authoritative NS hosts + IPs (often non-authoritative if cached).
- **Key Observations**:
  - Local DNS server may iteratively query others (invisible to client).
  - Answers may be **non-authoritative** (from cache).

---

### **2. Hands-On nslookup Tasks**
1. **Find IP of `www.iitb.ac.in`**

       nslookup www.iitb.ac.in

- the IP address is `103.21.124.133`

2. **Identify responding DNS server IP**
- the IP address is `127.0.0.53`

3. **Authoritative or non-authoritative?**  
- The response header shows that it is **non-authoritative**.  

```
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
Name:   www.iitb.ac.in
Address: 103.21.124.133
```

4. **Find authoritative NS for `iit.ac.in`**  
   → `nslookup -type=NS iit.ac.in`  
   → To get NS IP: Query `Type=A` on returned NS hostname.

```
Server:         127.0.0.53
Address:        127.0.0.53#53

** server can't find iit.ac.in: NXDOMAIN
```

5. **Find authoritative NS for `google.com`**  

        nslookup -type=NS google.com

        Server:         127.0.0.53
        Address:        127.0.0.53#53
        
        Non-authoritative answer:
        google.com      nameserver = ns2.google.com.
        google.com      nameserver = ns4.google.com.
        google.com      nameserver = ns3.google.com.
        google.com      nameserver = ns1.google.com.

Authoritative answers can be found from:

6. **Find the NS IP for the returned NS hostname(s) for `google.com`**

        nslookup -type=A ns2.google.com

        Server:         127.0.0.53
        Address:        127.0.0.53#53
        
        Non-authoritative answer:
        Name:   ns2.google.com
        Address: 216.239.34.10

---

### **3. DNS Caching on Host**
- Hosts maintain **DNS resolver cache** (like browser cache).
- Checked **before** contacting local DNS server.
- **Clear cache**:
  - **Mac**: `sudo killall -HUP mDNSResponder`
  - **Windows**: `ipconfig /flushdns`
  - **Linux**: `sudo systemd-resolve --flush-caches` or `resolvectl flush-caches`

---

### **4. Wireshark DNS Tracing**

#### **Setup**
1. Clear DNS + browser cache.
2. Filter: `ip.addr == <your_IP>`
3. Visit: `http://gaia.cs.umass.edu/kurose_ross/`
4. Capture packets.

<img width="808" height="809" alt="image" src="https://github.com/user-attachments/assets/308a7cbb-154d-4785-b50f-005909ad48e5" />

<img width="1371" height="699" alt="image" src="https://github.com/user-attachments/assets/80e86776-863f-48c7-ae4f-f29f849d014f" />

#### **Questions (General)**
5–6. **First DNS query/response for `gaia.cs.umass.edu`**  

- Packet number of the first DNS query/response (UDP port 53) is `18`. 

7. **Ports**:

- Filter for **DNS queries** on UDP destination port 53:

      udp.dstport == 53

<img width="1359" height="643" alt="image" src="https://github.com/user-attachments/assets/cdaae3af-f5d7-4ac4-a8e9-d18a27d1e9bc" />

- Filter for **DNS responses** on UDP source port 53, the ephemeral client port

      udp.srcport == 53

<img width="1359" height="643" alt="image" src="https://github.com/user-attachments/assets/c951dff4-12d9-4cab-a138-0d74374d9a11" />

11. **Query sent to**: Local DNS server IP.

9–10. **Query message**: 1 question, 0 answers  
   **Response**: 1 question, ≥1 answers (A records).

11. **HTTP GET vs DNS**:
   - Base page → HTTP GET (packet X)
   - DNS query for `gaia.cs.umass.edu` → packet Y
   - DNS response → packet Z
   - Image GET (`header_graphic...jpg`) → packet W
   - **Second DNS query?** → **None** if cached after first resolution.

---

### **5. nslookup + Wireshark**

#### **A. Query `www.cs.umass.edu`**
12. Ports: Query dest 53, response src 53  
13. Sent to **default local DNS server**? → Yes  
14. Query: `Type=A`, 1 question, 0 answers  
15. Response: 1 question, 1+ answers

#### **B. Query `umass.edu` with `-type=NS`**
16. Sent to local DNS server  
17. Query: 1 question, 0 answers  
18. Response:
   - Multiple **NS records** (authoritative name servers)
   - Often includes **additional A records** (IPs of NS hosts)

---

### **Key Takeaways**
- DNS client is simple: **query local server → get response**.
- **nslookup** reveals record types, authority, and reverse mappings.
- **Caching** (host + local server) reduces DNS traffic.
- **Wireshark** exposes UDP-based DNS (port 53), query/response structure, and caching effects (no redundant queries).
- **Authoritative vs non-authoritative**: Critical for understanding source trust.

---

**Resources**:
- Textbook: Section 2.4
- nslookup examples: [CloudNS](https://www.cloudns.net/blog/10-most-used-nslookup-commands/)
- Trace files: `wireshark-traces-9e.zip` (for offline use)
