# Wireshark Lab: UDP

## Objective

Explore the [UDP transport protocol](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_UDP_v9.pdf) using Wireshark by capturing and analyzing UDP packets (e.g., via DNS queries with `nslookup`).

## The OSI Model

The **OSI (Open Systems Interconnection) model** is a conceptual framework developed by the International Organization for Standardization (ISO) in 1984 to standardize network communication. It divides network functions into **7 layered layers**, each responsible for specific tasks, enabling interoperability between diverse systems.

### **7 Layers of the OSI Model (Bottom to Top)**

| Layer | Name | Function | Key Protocols/Examples | Data Unit |
|-------|------|----------|------------------------|-----------|
| **7** | **Application** | Provides network services directly to end-user applications (e.g., web browsing, email). | HTTP, HTTPS, FTP, SMTP, DNS | Data |
| **6** | **Presentation** | Translates data between the network and application (encryption, compression, format conversion). | SSL/TLS, JPEG, MPEG | Data |
| **5** | **Session** | Manages communication sessions (establish, maintain, terminate connections). | NetBIOS, RPC, SIP | Data |
| **4** | **Transport** | Ensures end-to-end data delivery, error correction, flow control. | TCP (reliable), UDP (fast) | Segment/Datagram |
| **3** | **Network** | Handles logical addressing and routing across networks. | IP (IPv4/IPv6), ICMP, IPsec | Packet |
| **2** | **Data Link** | Provides node-to-node data transfer, error detection, MAC addressing. | Ethernet, Wi-Fi (802.11), PPP, Switch | Frame |
| **1** | **Physical** | Transmits raw bit streams over physical media (cables, wireless). | USB, Ethernet cables, Bluetooth, Fiber | Bits |

---

## Mapping OSI Model Layers to the Layers of the Wireshark Packet Details Pane

Below is a typical **Wireshark Packet Details pane** layout for a packet.  
Each expandable row corresponds to a layer in the **OSI model** (Layers 1–7).  
This shows the exact hierarchy Wireshark uses.

### OSI-to-Wireshark Mapping

| OSI Layer | Name | Wireshark Tree Node | Typical Fields |
|-----------|------|---------------------|----------------|
| **1** | **Physical** | `Frame` (top-level) | Arrival time, frame length, coloring rules |
| **2** | **Data Link** | `Ethernet II`, `802.1Q VLAN`, `PPP`, etc. | MAC addresses, EtherType/VLAN ID |
| **3** | **Network** | `Internet Protocol Version 4/6`, `ARP`, `ICMP` | IP addresses, TTL, protocol, fragmentation |
| **4** | **Transport** | `Transmission Control Protocol`, `User Datagram Protocol` | Ports, sequence/ack numbers, flags, window |
| **5** | **Session** | *Rarely a distinct node* (sometimes `TLS Record Layer` for session setup) | Session IDs in higher-layer protocols |
| **6** | **Presentation** | `TLS`, `ASN.1 BER`, `X.509`, `JPEG`, etc. | Encryption, compression, data formatting |
| **7** | **Application** | `HTTP`, `DNS`, `SMTP`, `FTP`, `SMB`, etc. | Request/response verbs, headers, payloads |

---

## UDP Datagram Packet Structure

Note: `1 byte` is `8 bits`.  

**Header** (**fixed length of 8 bytes**):
   - **Source Port** (16 bits or **2 bytes**): Identifies the sending application's port. 
   - **Destination Port** (16 bits or **2 bytes**): Identifies the receiving application's port. 
   - **Length** (16 bits or **2 bytes**): Specifies the total length of the UDP packet (header + data) in bytes. 
   - **Checksum** (16 bits or **2 bytes**): Used for error detection. It is optional in IPv4 but mandatory in IPv6. 

Data (**variable length**): The actual data payload being sent from source to destination. 

---

## Lab Steps: Capture UDP Packets

- Start Wireshark and capture packets.
- Open the Terminal
- Run the following command to to generate DNS/UDP traffic:

   `nslookup www.nyu.edu`

- Stop capture and filter for UDP packets from your host.

## Analyze First UDP Segment
- Pick the first UDP packet in the trace.

      1	0.000000000	10.98.0.7	10.98.0.1	DNS	68	Standard query 0xecda A www.nyu.edu OPT

1. **Packet number** in Wireshark.
- `1`
2. **Application-layer protocol**.
- DNS

      Domain Name System (query)

3. **Number of UDP header fields** and their **names** (observe directly in Wireshark).
- 4: Source Port, Destination Port, Length, and Checksum.  
- The standard UDP header structure in Wireshark displays four fields: Source Port, Destination Port, Length, and Checksum.
- The rest of the information is not part of the UDP header itself.

```
User Datagram Protocol, Src Port: 49684, Dst Port: 53
    Source Port: 49684
    Destination Port: 53
    Length: 48
    Checksum: 0xd306 [unverified]

    [Checksum Status: Unverified]
    [Stream index: 0]
    [Timestamps]
    UDP payload (40 bytes)
```

4. **Length (in bytes)** of each UDP header field.
- `2` bytes
- All four UDP headers combine for a total of `8 bytes`.  

5. **Length field meaning** 
- This field is the sum of the `UDP payload` length in bytes and the `UDP header` size in bytes.
   - UDP payload length in bytes: `40`
   - UDP header lengh in bytes: `8`

         Length: 48
         UDP payload (40 bytes)

6. **Max UDP payload size**: Derived from total max UDP datagram size minus header.
- `65507` bytes.
- This is calculated by taking the maximum IP packet size (65,535 bytes) and subtracting the 20-byte IPv4 header and the 8-byte UDP header.

7. **What is the largest possible source port number?**
- `65535`
- The [total range for UDP ports](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml) is 0 to **65,535** based on 16-bit port field (0–65535).

8. **UDP protocol number** in IP header: Look at IP datagram’s Protocol field (decimal).
- The number `17` is the protocol number assigned to UDP by the Internet Assigned Numbers Authority (IANA).

      Protocol: UDP (17)

<img width="1259" height="709" alt="image" src="https://github.com/user-attachments/assets/f1aa8cc9-28cc-46b4-ab53-d782cddb09e8" />

<img width="1259" height="709" alt="image" src="https://github.com/user-attachments/assets/42d8155e-36f8-4989-af60-b926b3c42b6b" />

## Examine UDP Request-Response Pair

- Identify a UDP query (e.g., DNS request) and its reply.

      1	0.000000000	10.98.0.7	10.98.0.1	DNS	68	Standard query 0xecda A www.nyu.edu OPT
      2	0.160466202	10.98.0.1	10.98.0.7	DNS	175	Standard query response 0xecda A www.nyu.edu CNAME d1q5ku5vnwkd2k.cloudfront.net A 3.164.255.11 A 3.164.255.18 A 3.164.255.21 A 3.164.255.5 OPT

- For both packets, record:
  - **Packet numbers** (Wireshark-assigned).
     - `1` and `2`

  - **Source and destination port numbers**.

```
1	0.000000000	10.98.0.7	10.98.0.1	DNS	68	Standard query 0xecda A www.nyu.edu OPT

User Datagram Protocol, Src Port: 49684, Dst Port: 53
Source Port: 49684
Destination Port: 53
```

```
2	0.160466202	10.98.0.1	10.98.0.7	DNS	175	Standard query response 0xecda A www.nyu.edu CNAME d1q5ku5vnwkd2k.cloudfront.net A 3.164.255.11 A 3.164.255.18 A 3.164.255.21 A 3.164.255.5 OPT

User Datagram Protocol, Src Port: 53, Dst Port: 49684
Source Port: 53
Destination Port: 49684
```

- **Describe port relationship**.
   - Source port of request is the destination port of reply.
   - Destination port of reply is the source port of the request.

<img width="1259" height="709" alt="image" src="https://github.com/user-attachments/assets/16a697fd-da67-4ebc-af12-e3540b60aaaf" />

## **What I Learned**

I learned that
- Wireshark's packet details pane shows the OSI model from top-to-bottom in descending order, listing the layer 1 at the top and ending with layer 7 at the bottom.  
- UDP datagrams or packets are structured with
   - a header with 4 fields, totalling `8` bytes,
   - a 20-byte IPv4 header, 
   - and a variable data payload with a maximum size of `65507` bytes.
- the source port of a DNS request is the destination port of its DNS reply (and vice versa).  
