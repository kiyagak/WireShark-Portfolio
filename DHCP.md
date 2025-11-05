# Wireshark Lab: DHCP 

## Objective
This Wireshark [lab](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_DHCP_v9.pdf) explores the **Dynamic Host Configuration Protocol (DHCP)** by capturing and analyzing the four key DHCP messages: **Discover**, **Offer**, **Request**, and **ACK**.  
I will de-configure and re-configure a network interface to trigger DHCP, capture packets with **Wireshark**, and answer analytical questions.

---

## Gathering a Packet Trace

### **On a Mac**
1. **De-configure interface**  
   ```bash
   sudo ipconfig set enp0s3 none
   ```
   *(Replace `enp0s3` with your interface name from Wireshark → Capture → Options)*

2. **Start Wireshark** on the de-configured interface.

3. **Request new DHCP lease**  
   ```bash
   sudo ipconfig set enp0s3 dhcp
   ```

4. **Stop capture** after a few seconds.

---

### **On Linux**
1. Open the Terminal.  
2. Find your computer's network interface by listing all of its network interfaces:

```
sudo ip addr
```

```
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
```

3. Install the dhclient binary.

```
sudo apt update
sudo apt install isc-dhcp-client -y
```

4. **Release existing IP and lease**  
   ```bash
   sudo ip addr flush enp0s3
   sudo dhclient -r
   ```

5. **Start Wireshark** on the same interface.

6. **Request new DHCP lease**  
   ```bash
   sudo dhclient enp0s3
   ```

7. **Stop capture** after a few seconds.

---

### **On Windows (PC)**
1. **Release IP address**  
   ```cmd
   ipconfig /release
   ```

2. **Start Wireshark**.

3. **Renew IP address**  
   ```cmd
   ipconfig /renew
   ```

4. **Stop capture** after a few seconds.

---

### **Verify Capture**
- In Wireshark, filter with:  
  ```
  dhcp
  ```
- You should see: **Discover → Offer → Request → ACK**

<img width="1225" height="609" alt="Screenshot From 2025-11-05 18-45-32" src="https://github.com/user-attachments/assets/09fa6eb5-a41e-4449-b733-7ab311daefd9" />

---

## DHCP Questions

### **DHCP Discover Message**
1. Is the **transport protocol** TCP or UDP?
- UDP

```
4	5.380429933	0.0.0.0	255.255.255.255	DHCP	342	DHCP Discover - Transaction ID 0x1c54162d
      
Protocol: UDP (17)
```

2. **Source IP address?**
- `0.0.0.0`

Special characteristic about the source IP address?

The IPv4 address **0.0.0.0** is a special-purpose address used primarily in networking to represent an **unspecified** or **invalid** address. Here's a concise breakdown of its uses:

- **Default Route**: In routing tables, 0.0.0.0 often represents the default gateway or "catch-all" route for packets with no specific destination match.
- **Non-routable Address**: It cannot be assigned to a device as a valid IP address for communication.
- **Server Binding**: In software, binding a server to 0.0.0.0 means it listens on all available network interfaces (e.g., for web servers or applications).
- **Placeholder**: Used in configurations to indicate "no specific address" or "all addresses."

It is not a valid destination or source address for actual network communication and is distinct from other special addresses like 127.0.0.1 (localhost).

3. **Destination IP address?**  
- `255.255.255.255`

Special characteristic about the destination IP address?

The IPv4 address **255.255.255.255** is a **broadcast address** used to send data to all devices on a local network. It cannot be assigned to a specific device as a unique IP address. When a device sends a packet to this address, it is broadcast to every device within the same subnet, typically used for protocols like DHCP or ARP to communicate with all hosts. It’s defined as the **limited broadcast address** in IPv4 and does not require routing beyond the local network segment.

```
4	5.380429933	0.0.0.0	255.255.255.255	DHCP	342	DHCP Discover - Transaction ID 0x1c54162d
     
Internet Protocol Version 4, Src: 0.0.0.0, Dst: 255.255.255.255
```

4. **Transaction ID value?**
- `0x1c54162d`

         Transaction ID: 0x1c54162d

5. **Five requested/suggested options** in the **Options field** (beyond IP address)?
- Option: (53) DHCP Message Type (Discover)
- Option: (50) Requested IP Address (10.0.2.15)
- Option: (12) Host Name
- Option: (55) Parameter Request List
- Option: (255) End

<img width="1225" height="874" alt="image" src="https://github.com/user-attachments/assets/9f6828e4-c5ce-4b1c-9f86-df4ddb846018" />

```
4	5.380429933	0.0.0.0	255.255.255.255	DHCP	342	DHCP Discover - Transaction ID 0x1c54162d

Dynamic Host Configuration Protocol (Discover)
    Option: (53) DHCP Message Type (Discover)
        Length: 1
        DHCP: Discover (1)
    Option: (50) Requested IP Address (10.0.2.15)
        Length: 4
        Requested IP Address: 10.0.2.15
    Option: (12) Host Name
        Length: 6
        Host Name: debian
    Option: (55) Parameter Request List
        Length: 13
        Parameter Request List Item: (1) Subnet Mask
        Parameter Request List Item: (28) Broadcast Address
        Parameter Request List Item: (2) Time Offset
        Parameter Request List Item: (3) Router
        Parameter Request List Item: (15) Domain Name
        Parameter Request List Item: (6) Domain Name Server
        Parameter Request List Item: (119) Domain Search
        Parameter Request List Item: (12) Host Name
        Parameter Request List Item: (44) NetBIOS over TCP/IP Name Server
        Parameter Request List Item: (47) NetBIOS over TCP/IP Scope
        Parameter Request List Item: (26) Interface MTU
        Parameter Request List Item: (121) Classless Static Route
        Parameter Request List Item: (42) Network Time Protocol Servers
    Option: (255) End
        Option End: 255
```

---

### **DHCP Offer Message** *(Response to Discover above)*

6. **How do you know this Offer matches your Discover?**
- The `Transaction ID` for both the DHCP Offer and DHCP Discover match.

```
4	5.380429933	0.0.0.0	255.255.255.255	DHCP	342	DHCP Discover - Transaction ID 0x1c54162d
5	5.381324446	10.0.2.2	255.255.255.255	DHCP	590	DHCP Offer    - Transaction ID 0x1c54162d
```

7. **Source IP address?**  
- `10.0.2.2`

Special?
- My computer's local IP address at the `enp0s3` interface.  

8. **Destination IP address?**  
- `255.255.255.255`

Special?
- This is a broadcast address.  

```
5	5.381324446	10.0.2.2	255.255.255.255	DHCP	590	DHCP Offer    - Transaction ID 0x1c54162d
Internet Protocol Version 4, Src: 10.0.2.2, Dst: 255.255.255.255
```

9. **Five pieces of information** provided in the **Options field**?
- Option: (53) DHCP Message Type (Offer)
- Option: (54) DHCP Server Identifier (10.0.2.2)
- Option: (1) Subnet Mask (255.255.255.0)
- Option: (3) Router
- Option: (6) Domain Name Server
- Option: (51) IP Address Lease Time
- Option: (255) End

<img width="1225" height="874" alt="image" src="https://github.com/user-attachments/assets/264f1fca-7912-464d-86ce-e29538504093" />

```
5	5.381324446	10.0.2.2	255.255.255.255	DHCP	590	DHCP Offer    - Transaction ID 0x1c54162d

Dynamic Host Configuration Protocol (Offer)
    Option: (53) DHCP Message Type (Offer)
        Length: 1
        DHCP: Offer (2)
    Option: (54) DHCP Server Identifier (10.0.2.2)
        Length: 4
        DHCP Server Identifier: 10.0.2.2
    Option: (1) Subnet Mask (255.255.255.0)
        Length: 4
        Subnet Mask: 255.255.255.0
    Option: (3) Router
        Length: 4
        Router: 10.0.2.2
    Option: (6) Domain Name Server
        Length: 8
        Domain Name Server: 10.98.0.1
        Domain Name Server: 192.168.18.1
    Option: (51) IP Address Lease Time
        Length: 4
        IP Address Lease Time: 1 day (86400)
    Option: (255) End
        Option End: 255
```

---

### **DHCP Request Message**

10. **UDP source and destination ports?**
- Source Port: `68`
- Destination Port: `67`

11. **Source IP address?**  
- `0.0.0.0`

Special?
- This is an unspecified or invalid address used either as a
   - default route for packets with no specific destination match
   - non-routable address that can't be assigned to a device as a valid IP address
   - server binding address that listens on all available network interfaces
   - or a placeholder to indicate no specific address or all addresses

12. **Destination IP address?**  
- `255.255.255.255`

Special?
- This is a broadcast address.  

13. **Transaction ID?**  
- It matches the one found in the DHCP Discover and Offer packets.  

<img width="1225" height="824" alt="image" src="https://github.com/user-attachments/assets/80cb1e2c-4c9d-4b35-ac55-19e36343da0b" />

```
4	5.380429933	0.0.0.0	255.255.255.255	DHCP	342	DHCP Discover - Transaction ID 0x1c54162d
5	5.381324446	10.0.2.2	255.255.255.255	DHCP	590	DHCP Offer    - Transaction ID 0x1c54162d
6	5.381590039	0.0.0.0	255.255.255.255	DHCP	342	DHCP Request  - Transaction ID 0x1c54162d
```

```
6	5.381590039	0.0.0.0	255.255.255.255	DHCP	342	DHCP Request  - Transaction ID 0x1c54162d

User Datagram Protocol, Src Port: 68, Dst Port: 67
Internet Protocol Version 4, Src: 0.0.0.0, Dst: 255.255.255.255
```

14. **Parameter Request List differences** vs. Discover message?
- No difference.  

```
4	5.380429933	0.0.0.0	255.255.255.255	DHCP	342	DHCP Discover - Transaction ID 0x1c54162d

Dynamic Host Configuration Protocol (Discover)
    Option: (55) Parameter Request List
        Length: 13
        Parameter Request List Item: (1) Subnet Mask
        Parameter Request List Item: (28) Broadcast Address
        Parameter Request List Item: (2) Time Offset
        Parameter Request List Item: (3) Router
        Parameter Request List Item: (15) Domain Name
        Parameter Request List Item: (6) Domain Name Server
        Parameter Request List Item: (119) Domain Search
        Parameter Request List Item: (12) Host Name
        Parameter Request List Item: (44) NetBIOS over TCP/IP Name Server
        Parameter Request List Item: (47) NetBIOS over TCP/IP Scope
        Parameter Request List Item: (26) Interface MTU
        Parameter Request List Item: (121) Classless Static Route
        Parameter Request List Item: (42) Network Time Protocol Servers
```

```
6	5.381590039	0.0.0.0	255.255.255.255	DHCP	342	DHCP Request  - Transaction ID 0x1c54162d

Dynamic Host Configuration Protocol (Request)
    Option: (55) Parameter Request List
        Length: 13
        Parameter Request List Item: (1) Subnet Mask
        Parameter Request List Item: (28) Broadcast Address
        Parameter Request List Item: (2) Time Offset
        Parameter Request List Item: (3) Router
        Parameter Request List Item: (15) Domain Name
        Parameter Request List Item: (6) Domain Name Server
        Parameter Request List Item: (119) Domain Search
        Parameter Request List Item: (12) Host Name
        Parameter Request List Item: (44) NetBIOS over TCP/IP Name Server
        Parameter Request List Item: (47) NetBIOS over TCP/IP Scope
        Parameter Request List Item: (26) Interface MTU
        Parameter Request List Item: (121) Classless Static Route
        Parameter Request List Item: (42) Network Time Protocol Servers
```

---

### **DHCP ACK Message**

15. **Source IP address?**  
- `10.0.2.2`

Special?
- The IP address assigned to my computer's network interface that is communicating on the Internet.  

16. **Destination IP address?**  
- `255.255.255.255`

Special?
- This is a broadcast address.  

17. **Field name** containing the **assigned client IP**?
- `Your (client) IP address:`

18. **Lease time duration?**
- `1 day (86400)`

19. **Default gateway (first-hop router) IP address?**
- `10.0.2.2`

<img width="1225" height="868" alt="image" src="https://github.com/user-attachments/assets/8e17b640-b06b-4b70-81cc-12df673fe658" />

```
7	5.382144006	10.0.2.2	255.255.255.255	DHCP	590	DHCP ACK      - Transaction ID 0x1c54162d

Internet Protocol Version 4, Src: 10.0.2.2, Dst: 255.255.255.255
User Datagram Protocol, Src Port: 67, Dst Port: 68
Dynamic Host Configuration Protocol (ACK)
    Message type: Boot Reply (2)
    Hardware type: Ethernet (0x01)
    Hardware address length: 6
    Hops: 0
    Transaction ID: 0x1c54162d
    Seconds elapsed: 0
    Bootp flags: 0x0000 (Unicast)
    Client IP address: 0.0.0.0
    Your (client) IP address: 10.0.2.15
    Next server IP address: 10.0.2.2
    Relay agent IP address: 0.0.0.0
    Client MAC address: PCSSystemtec_74:fb:67 (08:00:27:74:fb:67)
    Client hardware address padding: 00000000000000000000
    Server host name not given
    Boot file name not given
    Magic cookie: DHCP
    Option: (53) DHCP Message Type (ACK)
        Length: 1
        DHCP: ACK (5)
    Option: (54) DHCP Server Identifier (10.0.2.2)
        Length: 4
        DHCP Server Identifier: 10.0.2.2
    Option: (1) Subnet Mask (255.255.255.0)
        Length: 4
        Subnet Mask: 255.255.255.0
    Option: (3) Router
        Length: 4
        Router: 10.0.2.2
    Option: (6) Domain Name Server
        Length: 8
        Domain Name Server: 10.98.0.1
        Domain Name Server: 192.168.18.1
    Option: (51) IP Address Lease Time
        Length: 4
        IP Address Lease Time: 1 day (86400)
    Option: (255) End
        Option End: 255
    Padding […]: 00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
```

---

## What I Learned

I learned
- DHCP uses Discover, Offer, Request, and ACK messages in its process of providing clients with IP addresses
- how to release and renew a DHCP address on a client computer
- DHCP uses UDP as its transport protocol
- the DHCP process uses the same Transaction ID when assigning a DHCP address to a device
- the DHCP discover is done when "no specific address" or "all addresses" send a broadcast to all subnet devices on the local network
- the DHCP discover request includes the requested IP address that the device will be assigned
- the DHCP offer message is broadcast to all subnet devices by the local network interface in response to the DHCP discover message to provide DHCP settings such as DHCP Server Identifier, subnet mask, router, DNS, and IP address lease time
- the DHCP request message uses "no specific address" or "all addresses" from UDP port 68 to send a broadcast to all subnet devices on the local network on UDP port 67
- UDP port 67 is for the DHCP server to handle client requests and send IP assignments
- UDP port 68 is for the DHCP client to send requests and receive server responses
- DHCP requests and DHCP offers use the same Parameter Request List
- the DHCP ACK message is sent from the local device network interface to broadcast to all subnet IP addresses to get information such as the assigned client IP, lease time, and default gateway
