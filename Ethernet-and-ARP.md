# Wireshark Lab: Ethernet and ARP

## Objective 
The goal in this Ethernet and ARP Wireshark lab is to investigate the **Ethernet protocol** and the **Address Resolution Protocol (ARP)** using Wireshark.

---

### **Pre-Lab Setup**

> **Warning: Do this before every capture to ensure unencrypted HTTP traffic**

- **Disable VPN** (encrypts HTTP/TCP)  
- **Disable HTTP/3 & QUIC** in browser (default in 2025 browsers)  
  → Guide: [https://techysnoop.com/disable-quic-protocol-in-chrome-edge-firefox/](https://techysnoop.com/disable-quic-protocol-in-chrome-edge-firefox/)  
- Use **`http://`** (not `https://`) to avoid encryption  
- **Turn off** privacy settings  
- **Clear browser cache & history** before each section

---

## Disable QUIC in Chrome

1. Open Chrome → type `chrome://flags` in the address bar  
2. Search for **"QUIC"**  
3. Disable **"Experimental QUIC Protocol"**  
   *(Alternatively: `chrome://flags/#enable-quic`)*  
4. Relaunch Chrome

![Disable QUIC in Chrome](https://github.com/user-attachments/assets/d1949f9d-e4d5-4a10-a1f8-21d5f0abe15d)

---

## Disable Caching in Chrome

1. Right-click on the web page → **Inspect**  
2. Go to the **Network** tab  
3. Check **Disable cache**

![Disable cache in Chrome DevTools](https://github.com/user-attachments/assets/ca8f60e3-b98a-4057-bcab-9401ad34654f)

---

## Disable QUIC in Firefox

1. Open Firefox → type `about:config`  
2. Accept the risk and continue  
3. Search for:  
   - `network.http.http3.enable`  
   - `network.http.http3.enable_0rtt`  
   - `network.http.http3.enable_qlog`  
4. Double-click each to set value to **`false`**  
5. Restart Firefox

---

## Disable QUIC in Edge

1. Open Edge → type `edge://flags`  
2. Search for **"QUIC"**  
3. Disable **"Experimental QUIC Protocol"**  
4. Restart Edge

---

## **Part 1: Capturing and Analyzing Ethernet Frames**

### **Setup Instructions**
1. **Ensure browser cache is empty**.
2. Start **Wireshark**.
3. Open browser and visit:  
   [http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file3.html](http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file3.html)  
   → Displays the US Bill of Rights.
4. **Stop Wireshark capture**.

---

### **Questions (Ethernet Frame Analysis)**

| # | Question |
|---|---------|
| 1 | What is the **48-bit Ethernet address** of your computer? |
| 2 | What is the **48-bit destination address** in the Ethernet frame? Is this the Ethernet address of `gaia.cs.umass.edu`? *(Hint: No)* What device has this address?
| 3 | What is the **hexadecimal value** of the **two-byte Frame type field** in the Ethernet frame carrying the HTTP GET request? What upper-layer protocol does it correspond to? |
| 4 | How many bytes from the **start of the Ethernet frame** does the ASCII **“G” in “GET”** appear? *(Do not count preamble)* |

48-bit Ethernet address** of your computer?
- 

48-bit destination address in the Ethernet frame? 
- 

Is this the Ethernet address of `gaia.cs.umass.edu`?
- 

What device has this address?
- 

What is the **hexadecimal value** of the **two-byte Frame type field** in the Ethernet frame carrying the HTTP GET request? 
- 

What upper-layer protocol does it correspond to? 
- 

How many bytes from the **start of the Ethernet frame** does the ASCII **“G” in “GET”** appear? 
- 

```
83	1.644124351	10.0.2.15	128.119.245.12	TCP	74	60588 → 443 [SYN] Seq=0 Win=64240 Len=0 MSS=1460 SACK_PERM TSval=1477112366 TSecr=0 WS=128


```

---

### **HTTP Response Frame Questions**

| # | Question |
|---|---------|
| 5 | What is the **Ethernet source address**? Is it your computer or `gaia.cs.umass.edu`? *(Hint: No)* What device has this address? |
| 6 | What is the **destination address** in the Ethernet frame? Is this your computer’s Ethernet address? |
| 7 | What is the **hex value** of the **Frame type field**? What upper-layer protocol? |
| 8 | How many bytes from the **start of the frame** does the ASCII **“O” in “OK”** (HTTP 200) appear? *(No preamble)* |
| 9 | How many **Ethernet frames** (each with IP → TCP → HTTP data) carry the complete **“OK 200 ...”** reply message? |

---

## **Part 2: The Address Resolution Protocol (ARP)**

> **Review**: Section 6.4.1 in textbook

---

### **ARP Caching**

- **ARP cache** stores IP → Ethernet address mappings.
- Use command:  
  ```bash
  arp -a
  ```
  *(Works on Windows, macOS, Linux)*

---

### **Pre-Lab Questions (ARP Cache)**

| # | Question |
|---|---------|
| 10 | How many entries are in your **ARP cache**? |
| 11 | What information is displayed in **each ARP cache entry**? |

---

### **Clear ARP Cache**
```bash
arp -d -a
```
> *macOS/Linux*: Use `sudo arp -d -a`  
> *No root access?* → Use provided trace file

---

### **Capture ARP in Action**

1. **Clear ARP cache** and **browser cache**.
2. Start **Wireshark**.
3. Visit:  
   [http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-lab-file3.html](http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-lab-file3.html)
4. **Stop capture**.
5. Filter: `arp`

---

### **ARP Packet Analysis Questions**

#### **ARP Request (Sent by Your Computer)**

| # | Question |
|---|---------|
| 12 | **Hex source address** in Ethernet frame containing ARP request? |
| 13 | **Hex destination address** in Ethernet frame? What device uses it? *(client, server, router, switch, etc.)* |
| 14 | **Hex value** of **Ethernet Frame type field**? What upper-layer protocol? |

#### **Inside the ARP Request Message**

| # | Question |
|---|---------|
| 15 | How many bytes from **start of Ethernet frame** does the **ARP opcode field** begin? |
| 16 | What is the **opcode value** in the ARP request? |
| 17 | Does the ARP request include the **sender’s IP address**? If yes, what is it? |
| 18 | What is the **target IP address** being resolved in the ARP request? |

#### **ARP Reply (Received by Your Computer)**

| # | Question |
|---|---------|
| 19 | What is the **opcode value** in the ARP reply? |
| 20 | What is the **Ethernet address** corresponding to the IP in the ARP request (Q18)? |

---

### **Additional Observation**

| # | Question |
|---|---------|
| 21 | Why are there **ARP requests from other devices** in the trace, but **no corresponding ARP replies**? |

---

## **Extra Credit**

| # | Task |
|---|------|
| **EX-1** | Using:  
```bash
arp -s <IP> <Wrong-MAC>
```  
What happens if you add a **correct IP** but **wrong Ethernet address**?  
→ This enables **ARP poisoning** ([varonis.com/blog/arp-poisoning](https://www.varonis.com/blog/arp-poisoning/)) |
| **EX-2** | What is the **default ARP cache entry timeout**?  
Determine via:  
- Monitoring `arp -a` over time  
- OS documentation  
→ State **how/where** you found the value |
