Here is the **Wireshark Lab: Ethernet and ARP v9** instruction set, formatted clearly and organized by section for easy reference:

---

## **Wireshark Lab: Ethernet and ARP v9**  
*Supplement to **Computer Networking: A Top-Down Approach**, 9th ed., J.F. Kurose and K.W. Ross*  
> “Tell me and I forget. Show me and I remember. Involve me and I understand.” — Chinese proverb  
> © 2005–2025, J.F. Kurose and K.W. Ross, All Rights Reserved

---

### **Objective**  
Investigate the **Ethernet protocol** and the **Address Resolution Protocol (ARP)** using Wireshark.

**Recommended Review**:  
- Section 6.4.1 (Link-layer addressing and ARP)  
- Section 6.4.2 (Ethernet)  
- RFC 826 (ARP specification): [std37.txt](ftp://ftp.rfc-editor.org/in-notes/std/std37.txt)

---

## **Part 1: Capturing and Analyzing Ethernet Frames**

### **Setup Instructions**
1. **Ensure browser cache is empty**.
2. Start **Wireshark**.
3. Open browser and visit:  
   [http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file3.html](http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file3.html)  
   → Displays the US Bill of Rights.
4. **Stop Wireshark capture**.

> *Alternative*: If no live Ethernet interface, download trace:  
> [wireshark-traces-9e.zip](http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip) → Use `ethernet-wireshark-trace1`

---

### **Questions (Ethernet Frame Analysis)**

| # | Question |
|---|---------|
| 1 | What is the **48-bit Ethernet address** of your computer? |
| 2 | What is the **48-bit destination address** in the Ethernet frame? Is this the Ethernet address of `gaia.cs.umass.edu`? *(Hint: No)* What device has this address? *(See pp. 483–484)* |
| 3 | What is the **hexadecimal value** of the **two-byte Frame type field** in the Ethernet frame carrying the HTTP GET request? What upper-layer protocol does it correspond to? |
| 4 | How many bytes from the **start of the Ethernet frame** does the ASCII **“G” in “GET”** appear? *(Do not count preamble)* |

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

---

**Resources**  
- Textbook: [http://gaia.cs.umass.edu/kurose_ross](http://gaia.cs.umass.edu/kurose_ross)  
- LMS Auto-Grading: [kurose_ross/lms.htm](http://gaia.cs.umass.edu/kurose_ross/lms.htm)  
- Wikipedia ARP: [en.wikipedia.org/wiki/Address_Resolution_Protocol](https://en.wikipedia.org/wiki/Address_Resolution_Protocol)  
- RFC 826: [datatracker.ietf.org/doc/html/rfc826](https://datatracker.ietf.org/doc/html/rfc826)

---

**Tip**: Use **color highlighting or annotations** on packet printouts to show answers (especially for submissions).
