# Wireshark Lab: DHCP 

## Objective
This Wireshark [lab](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_DHCP_v9.pdf) explores the **Dynamic Host Configuration Protocol (DHCP)** by capturing and analyzing the four key DHCP messages: **Discover**, **Offer**, **Request**, and **ACK**.  
You will de-configure and re-configure a network interface to trigger DHCP, capture packets with **Wireshark**, and answer analytical questions.

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
1. Find your computer's network interface by listing all  of its network interfaces:

         sudo ip addr

```
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
```

2. **Release existing IP and lease**  
   ```bash
   sudo ip addr flush enp0s3
   sudo dhclient -r
   ```

3. **Start Wireshark** on the same interface.

4. **Request new DHCP lease**  
   ```bash
   sudo dhclient enp0s3
   ```

5. **Stop capture** after a few seconds.

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

> **Alternative**: Use provided trace file:  
> `dhcp-wireshark-trace1-1.pcapng`  
> Download from: [http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip](http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip)

---

## DHCP Questions

### **DHCP Discover Message**
1. **Transport protocol?**  
   → UDP or TCP?

2. **Source IP address?**  
   → Special characteristic?

3. **Destination IP address?**  
   → Special characteristic?

4. **Transaction ID value?**

5. **Five requested/suggested options** in the **Options field** (beyond IP address)?

---

### **DHCP Offer Message** *(Response to Discover above)*
6. **How do you know this Offer matches your Discover?**

7. **Source IP address?**  
   → Special?

8. **Destination IP address?**  
   → Special? *(Hint: May differ from textbook Figure 4.24; see RFC 2131, p. 24)*

9. **Five pieces of information** provided in the **Options field**?

---

### **DHCP Request Message**
10. **UDP source and destination ports?**

11. **Source IP address?**  
    → Special?

12. **Destination IP address?**  
    → Special?

13. **Transaction ID?**  
    → Matches Discover and Offer?

14. **Parameter Request List differences** vs. Discover message?

---

### **DHCP ACK Message**
15. **Source IP address?**  
    → Special?

16. **Destination IP address?**  
    → Special?

17. **Field name** containing the **assigned client IP**?

18. **Lease time duration?**

19. **Default gateway (first-hop router) IP address?**

---

## References
- **Textbook**: Section 4.4.3, Figure 4.24  
- **DHCP RFC**: [https://www.ietf.org/rfc/rfc2131.txt](https://www.ietf.org/rfc/rfc2131.txt)  
- **Trace Files & LMS**: [http://gaia.cs.umass.edu/kurose_ross](http://gaia.cs.umass.edu/kurose_ross)

---

> **Tip**: Print or annotate packets (highlight fields) when submitting answers.  
> Use colored text or pen marks to show where you found each answer in Wireshark.
