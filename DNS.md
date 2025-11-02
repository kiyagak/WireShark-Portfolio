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

#### **Questions (General)**

5. Locate the first DNS query message resolving the name gaia.cs.umass.edu.
  - What is the packet number in the trace for the DNS query message?
    - 27
  
  - Is this query message sent over UDP or TCP?
    - UDP
   
          User Datagram Protocol, Src Port: 51183, Dst Port: 53

<img width="1359" height="643" alt="image" src="https://github.com/user-attachments/assets/02ad2a41-75b9-43de-967e-adb5b85016d9" />

6. Now locate the corresponding DNS response to the initial DNS query.
- What is the packet number in the trace for the DNS response message?
  - 30

- Is this response message received via UDP or TCP?
  - UDP

        User Datagram Protocol, Src Port: 53, Dst Port: 51183

<img width="1359" height="643" alt="image" src="https://github.com/user-attachments/assets/5c7491fa-671f-4d97-8620-4352474c5fe0" />

7. What is the destination port for the DNS query message?
    - 53

- What is the source port of the DNS response message?
  - 53

8. To what IP address is the DNS query message sent?

        Internet Protocol Version 4, Src: 10.98.0.5, Dst: 10.98.0.1

9. Examine the DNS query message. 
- How many “questions” does this DNS message contain?
  - 1

- How many “answers” answers does it contain?
  - 0

<img width="1359" height="703" alt="image" src="https://github.com/user-attachments/assets/d2deb45d-5683-4b7e-bbd0-513f6a46b1b4" />

10. Examine the DNS response message to the initial query message. 
- How many “questions” does this DNS message contain?
  - 1

- How many “answers” answers does it contain?
  - 0

<img width="1359" height="703" alt="image" src="https://github.com/user-attachments/assets/57a6bad6-cc28-4260-bd5a-4ee8b16104cf" />

11. The web page for the base file http://gaia.cs.umass.edu/kurose_ross/ references the image object http://gaia.cs.umass.edu/kurose_ross/header_graphic_book_8E_2.jpg , which, like the base webpage, is on gaia.cs.umass.edu.

This question will be answered using `dns-wireshark-trace1-1.pcapng` found in this Wireshark lab's [trace file zip](http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip).  

- What is the packet number in the trace for the initial HTTP GET request for the base file http://gaia.cs.umass.edu/kurose_ross/?
  - 22

- What is the packet number in the trace of the DNS query made to resolve gaia.cs.umass.edu so that this initial HTTP request can be sent to the gaia.cs.umass.edu IP address?
  - 15

- What is the packet number in the trace of the received DNS response?
  - 17

- What is the packet number in the trace for the HTTP GET request for the image object http://gaia.cs.umass.edu/kurose_ross/header_graphic_book_8E2.jpg?
  - 205

- What is the packet number in the DNS query made to resolve gaia.cs.umass.edu so that this second HTTP request can be sent to the gaia.cs.umass.edu IP address?
  - **No second HTTP request was sent** because **DNS caching blocks it**.  

---

### **5. Capture nslookup Queries with Wireshark**

Now let’s play with nslookup.
- Start packet capture.
- Do an nslookup on www.cs.umass.edu
- Stop packet capture.

12. What is the destination port for the DNS query message? 
- 53

        User Datagram Protocol, Src Port: 52103, Dst Port: 53

What is the source port of the DNS response message?
- 53

      User Datagram Protocol, Src Port: 53, Dst Port: 52103

13. To what IP address is the DNS query message sent? 

        Internet Protocol Version 4, Src: 10.98.0.19, Dst: 10.98.0.1

Is this the IP address of your default local DNS server?
- Yes

```
nmcli device show | grep IP4.DNS
IP4.DNS[1]:                             10.98.0.1
```

14. Examine the DNS query message. 

What “Type” of DNS query is it? 
- A

```
www.cs.umass.edu: type A, class IN
    Name: www.cs.umass.edu
    [Name Length: 16]
    [Label Count: 4]
    Type: A (1) (Host Address)
    Class: IN (0x0001)
```

Does the query message contain any “answers”?
- No

      Answer RRs: 0

15. Examine the DNS response message to the query message. 

How many “questions” does this DNS response message contain? 
- 1

      Questions: 1

How many “answers”?
- 1

      Answer RRs: 1

<img width="1268" height="968" alt="image" src="https://github.com/user-attachments/assets/b2f398e5-2eeb-4db4-8663-3ebbb6db195c" />

<img width="1268" height="968" alt="image" src="https://github.com/user-attachments/assets/50b4e9d9-0e79-4da8-8eef-48d9abf0ce97" />

Last, let’s use nslookup to issue a command that will return a type NS DNS record,

- Start a Wireshark capture.  
- Enter the following command:

    nslookup -type=NS umass.edu

- Stop the Wireshark capture.  

```
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
umass.edu       nameserver = ns3.umass.edu.
umass.edu       nameserver = ns1.umass.edu.
umass.edu       nameserver = ns2.umass.edu.

Authoritative answers can be found from:

```

Answer the following questions:

16. To what IP address is the DNS query message sent? 
- 10.98.0.1

        Internet Protocol Version 4, Src: 10.98.0.7, Dst: 10.98.0.1

Is this the IP address of your default local DNS server?
- Yes

```
nmcli device show | grep IP4.DNS
IP4.DNS[1]:                             10.98.0.1
```

17. Examine the DNS query message. 

How many questions does the query have? 
- 1

      Questions: 1

Does the query message contain any “answers”?
- No

      Answer RRs: 0

18. Examine the DNS response message (in particular the DNS response message that has type “**NS**”). 

How many answers does the response have? 
- 3

What information is contained in the answers? 

```
Answers
    umass.edu: type NS, class IN, ns ns3.umass.edu
        Name: umass.edu
        Type: NS (2) (authoritative Name Server)
        Class: IN (0x0001)
        Time to live: 3180 (53 minutes)
        Data length: 6
        Name Server: ns3.umass.edu
    umass.edu: type NS, class IN, ns ns1.umass.edu
        Name: umass.edu
        Type: NS (2) (authoritative Name Server)
        Class: IN (0x0001)
        Time to live: 3180 (53 minutes)
        Data length: 6
        Name Server: ns1.umass.edu
    umass.edu: type NS, class IN, ns ns2.umass.edu
        Name: umass.edu
        Type: NS (2) (authoritative Name Server)
        Class: IN (0x0001)
        Time to live: 3180 (53 minutes)
        Data length: 6
        Name Server: ns2.umass.edu
```

How many additional resource records are returned?
- 1

What additional information is included in these additional resource records (if additional information is returned)?

```
Additional records
    <Root>: type OPT
        Name: <Root>
        Type: OPT (41) 
        UDP payload size: 1232
        Higher bits in extended RCODE: 0x00
        EDNS0 version: 0
        Z: 0x0000
            0... .... .... .... = DO bit: Cannot handle DNSSEC security RRs
            .000 0000 0000 0000 = Reserved: 0x0000
        Data length: 0
```

<img width="1268" height="1050" alt="image" src="https://github.com/user-attachments/assets/25e8e2cb-e171-4ebe-8dda-7fade18d5844" />

<img width="2006" height="1210" alt="image" src="https://github.com/user-attachments/assets/e3e5b96e-c9b2-46c8-a60e-82f31691da3e" />

---

## What I Learned
I learned how to
- see that DNS works on UDP port 53, by default
- know that UDP is the default for quick DNS queries and responses
- know that TCP is used for larger DNS transfers or when reliability is critical
- use the nslookup command to query DNS servers for records
- use the nslookup command to find the responding DNS server IP
- to distinguish authoritative or non-authoritative DNS servers
- find the NS (nameserver) IP addresses for a domain
- clear DNS cache on a host
- use Wireshark to trace DNS information, such as source and destination IP and port, and protocol
- the role of DNS query and response questions and answers
- how DNS caching blocks a follow-up HTTP request to a web server
- how to find the IP address of my default local DNS server
- use Wireshark to gather more info about DNS queries for NS records
