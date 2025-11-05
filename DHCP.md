**Wireshark Lab: DHCP v9 – Summary**

This lab explores the **Dynamic Host Configuration Protocol (DHCP)** using **Wireshark** to capture and analyze the four key DHCP messages: **Discover, Offer, Request, ACK** (as shown in textbook Figure 4.24, Section 4.4.3).

---

### **Objective**
Capture a full DHCP transaction by forcing a client to release and renew its IP address, then analyze the resulting packets in Wireshark.

---

### **Packet Capture Instructions**

#### **Mac**
```bash
sudo ipconfig set en0 none    # Release IP
# Start Wireshark on en0
sudo ipconfig set en0 dhcp    # Renew via DHCP
# Stop capture after a few seconds
```

#### **Linux**
```bash
sudo ip addr flush en0
sudo dhclient -r              # Release lease
# Start Wireshark on en0
sudo dhclient en0             # Request new lease
# Stop capture
```

#### **Windows (PC)**
```cmd
ipconfig /release             # Release IP
# Start Wireshark
ipconfig /renew               # Renew via DHCP
# Stop capture
```

> **Tip**: Filter with `dhcp` in Wireshark to see only DHCP packets.  
> **Alternative**: Use provided trace file: `dhcp-wireshark-trace1-1.pcapng`

---

### **DHCP Questions Summary**

#### **1. DHCP Discover**
1. **Transport**: UDP  
2. **Src IP**: `0.0.0.0` *(client has no IP yet)*  
3. **Dst IP**: `255.255.255.255` *(broadcast)*  
4. **Transaction ID**: Unique 32-bit identifier (e.g., `0x...`)  
5. **Options Requested (5 examples)**:  
   - Subnet Mask  
   - Router (default gateway)  
   - Domain Name Server  
   - Domain Name  
   - IP Address Lease Time  

#### **2. DHCP Offer**
6. **Matched by**: Same **Transaction ID** as Discover  
7. **Src IP**: DHCP server’s IP  
8. **Dst IP**: Client’s **offered IP** (unicast) or `255.255.255.255` *(may vary; see RFC 2131, p.24)*  
9. **Options Provided (5 examples)**:  
   - Offered IP Address (`yiaddr`)  
   - Subnet Mask  
   - Lease Time  
   - Router  
   - DNS Server  

#### **3. DHCP Request**
10. **UDP Ports**: Src: `68` (bootpc), Dst: `67` (bootps)  
11. **Src IP**: `0.0.0.0` *(still no IP)*  
12. **Dst IP**: `255.255.255.255` *(broadcast)*  
13. **Transaction ID**: **Same** as Discover & Offer  
14. **Parameter Request List**: Same as Discover *(no change)*  

#### **4. DHCP ACK**
15. **Src IP**: DHCP server’s IP  
16. **Dst IP**: Client’s **assigned IP** (unicast)  
17. **Field with Client IP**: `Your (client) IP address` (`yiaddr`)  
18. **Lease Time**: In **DHCP Option: IP Address Lease Time** (seconds)  
19. **Default Gateway**: In **DHCP Option: Router**  

---

### **Key Takeaways**
- DHCP uses **UDP ports 67 (server) and 68 (client)**.
- **Discover & Request** are **broadcast** (`0.0.0.0 → 255.255.255.255`).
- **Offer & ACK** are typically **unicast** to the offered/assigned IP.
- **Transaction ID** links all four messages.
- Clients request parameters; servers provide them in Offer/ACK.

---

**Resources**:
- Textbook: Section 4.4.3, Figure 4.24
- RFC 2131: https://www.ietf.org/rfc/rfc2131.txt
- Trace file: http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip
