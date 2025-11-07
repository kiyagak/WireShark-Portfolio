# Wireshark Lab: Ethernet and ARP

## Objective 
The goal in this [Ethernet and ARP Wireshark lab](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_Ethernet_ARP_v9.pdf) is to investigate the **Ethernet protocol** and the **Address Resolution Protocol (ARP)** using Wireshark.

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
- `52:54:00:12:34:56`

48-bit destination address in the Ethernet frame? 
- `52:55:0a:00:02:02`

Is this the Ethernet address of `gaia.cs.umass.edu`?
- Yes

What device has this address?
- `52:55:0a:00:02:02`

What is the **hexadecimal value** of the two-byte Frame **Type** field in the Ethernet frame carrying the HTTP GET request? 
- `0x0800`

What upper-layer protocol does it correspond to? 
- `IPv4`

How many bytes from the **start of the Ethernet frame** does the ASCII **“G” in “GET”** appear? 
- `55`
- An Ethernet frame starts with a `54` byte header made up of
  - a 14 byte Ethernet header
  - a 20 byte IP header
  - a 20 byte TCP header
- the "G" character is found at byte `55` because that is where the TCP payload for the HTTP request begins
- This is because byte counting starts at 1 for the first byte of the Ethernet frame

```
12	0.285736288	10.0.2.15	128.119.245.12	HTTP	516	GET /wireshark-labs/HTTP-wireshark-file3.html HTTP/1.1 

Ethernet II, Src: RealtekU_12:34:56 (52:54:00:12:34:56), Dst: 52:55:0a:00:02:02 (52:55:0a:00:02:02) (14 bytes)
    Destination: 52:55:0a:00:02:02 (52:55:0a:00:02:02)
    Source: RealtekU_12:34:56 (52:54:00:12:34:56)
    Type: IPv4 (0x0800)
Internet Protocol Version 4, Src: 10.0.2.15, Dst: 128.119.245.12
    .... 0101 = Header Length: 20 bytes (5)
Transmission Control Protocol, Src Port: 53970, Dst Port: 80, Seq: 1, Ack: 1, Len: 462
    0101 .... = Header Length: 20 bytes (5)
Hypertext Transfer Protocol
```

<img width="974" height="531" alt="image" src="https://github.com/user-attachments/assets/a0e34cb7-ed67-4df3-9ab0-75117571e1b0" />

---

### **HTTP Response Frame Questions**

| # | Question |
|---|---------|
| 5 | What is the **Ethernet source address**? Is it your computer or `gaia.cs.umass.edu`? *(Hint: No)* What device has this address? |
| 6 | What is the **destination address** in the Ethernet frame? Is this your computer’s Ethernet address? |
| 7 | What is the **hex value** of the **Frame type field**? What upper-layer protocol? |
| 8 | How many bytes from the **start of the frame** does the ASCII **“O” in “OK”** (HTTP 200) appear? *(No preamble)* |
| 9 | How many **Ethernet frames** (each with IP → TCP → HTTP data) carry the complete **“OK 200 ...”** reply message? |

What is the **Ethernet source address**? 
- `52:55:0a:00:02:02`

Is it your computer or `gaia.cs.umass.edu`?
- `gaia.cs.umass.edu`

What device has this address?
- `52:55:0a:00:02:02`

What is the **destination address** in the Ethernet frame? 
- `52:54:00:12:34:56`

Is this your computer’s Ethernet address?
- Yes

What is the **hex value** of the ** type field**? 
- `0x0800`

What upper-layer protocol?
- `IPv4`

How many bytes from the **start of the frame** does the ASCII **“M” in “Moved Permanently”** (HTTP 301) appear? (No preamble)
- `55`

How many **Ethernet frames** (each with IP → TCP → HTTP data) carry the complete **“HTTP 301 Moved Permanently”** reply message?
- `1`

```
14	0.443582658	128.119.245.12	10.0.2.15	HTTP	699	HTTP/1.1 301 Moved Permanently  (text/html)
```

<img width="974" height="470" alt="image" src="https://github.com/user-attachments/assets/1fc99fea-0a33-4b56-b1a8-e796017d6ecc" />

---

## **Part 2: The Address Resolution Protocol (ARP)**

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

How many entries are in your **ARP cache**?
- `2`

What information is displayed in **each ARP cache entry**?
- the hostname and IP address
- MAC address
- the ARP-entry type
- the interface

```
arp -a
? (10.0.2.3) at 52:55:0a:00:02:03 [ether] on eth0
? (10.0.2.2) at 52:55:0a:00:02:02 [ether] on eth0
```

| Hostname / IP address | Keyword | MAC address          | Type     | Keyword | Interface |
|-----------------------|---------|----------------------|----------|---------|-----------|
| ? (10.0.2.3)          | at      | 52:55:0a:00:02:03    | [ether]  | on      | eth0      |
| ? (10.0.2.2)          | at      | 52:55:0a:00:02:02    | [ether]  | on      | eth0      |.

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

**Hex source address** in Ethernet frame containing ARP request?
- `52:54:00:12:34:56`

**Hex destination address** in Ethernet frame? What device uses it?
- `52:55:0a:00:02:03`

**Hex value** of Ethernet frame **Type** field?
- `0x0806`

What upper-layer protocol?
- `ARP`

#### **Inside the ARP Request Message**

| # | Question |
|---|---------|
| 15 | How many bytes from **start of Ethernet frame** does the **ARP opcode field** begin? |
| 16 | What is the **opcode value** in the ARP request? |
| 17 | Does the ARP request include the **sender’s IP address**? If yes, what is it? |
| 18 | What is the **target IP address** being resolved in the ARP request? |

How many bytes from **start of Ethernet frame** does the **ARP opcode field** begin?
- 20 to 21

- The byte count starts at 1 for the first byte of the Ethernet frame
- The Ethernet header is 14 bytes (0-13)
- The ARP message starts at byte 14
  - Hardware type: bytes 14–15 (2 bytes)
  - Protocol type: bytes 16–17 (2 bytes)
  - Hardware size: byte 18 (1 byte)
  - Protocol size: byte 19 (1 byte)
  - Opcode: bytes 20–21 (2 bytes)

What is the **opcode value** in the ARP request?
  - `request (1)`

Does the ARP request include the **sender’s IP address**?
- Yes

If yes, what is it?
- `10.0.2.15`

What is the **target IP address** being resolved in the ARP request?
- `10.0.2.3`

```
60	12.800005420	RealtekU_12:34:56	52:55:0a:00:02:03	ARP	42	Who has 10.0.2.3? Tell 10.0.2.15

Ethernet II, Src: RealtekU_12:34:56 (52:54:00:12:34:56), Dst: 52:55:0a:00:02:03 (52:55:0a:00:02:03)
    Destination: 52:55:0a:00:02:03 (52:55:0a:00:02:03)
    Source: RealtekU_12:34:56 (52:54:00:12:34:56)
    Type: ARP (0x0806)
Address Resolution Protocol (request)
    Hardware type: Ethernet (1)
    Protocol type: IPv4 (0x0800)
    Hardware size: 6
    Protocol size: 4
    Opcode: request (1)
    Sender MAC address: RealtekU_12:34:56 (52:54:00:12:34:56)
    Sender IP address: 10.0.2.15
    Target MAC address: 00:00:00_00:00:00 (00:00:00:00:00:00)
    Target IP address: 10.0.2.3
```

<img width="822" height="575" alt="image" src="https://github.com/user-attachments/assets/ab23501c-d8d7-4f52-adfd-ae76a69ae34a" />

#### **ARP Reply (Received by Your Computer)**

| # | Question |
|---|---------|
| 19 | What is the **opcode value** in the ARP reply? |
| 20 | What is the **Ethernet address** corresponding to the IP in the ARP request (Q18)? |

What is the **opcode value** in the ARP reply?
- `reply (2)`

What is the **Ethernet address** corresponding to the IP in the ARP request (Q18)?
- `52:55:0a:00:02:03`

```
61	12.800954056	52:55:0a:00:02:03	RealtekU_12:34:56	ARP	64	10.0.2.3 is at 52:55:0a:00:02:03

Ethernet II, Src: 52:55:0a:00:02:03 (52:55:0a:00:02:03), Dst: RealtekU_12:34:56 (52:54:00:12:34:56)
    Destination: RealtekU_12:34:56 (52:54:00:12:34:56)
    Source: 52:55:0a:00:02:03 (52:55:0a:00:02:03)
    Type: ARP (0x0806)
    Padding: 000000000000000000000000000000000000
    Frame check sequence: 0x00000000 [unverified]
    [FCS Status: Unverified]
Address Resolution Protocol (reply)
    Hardware type: Ethernet (1)
    Protocol type: IPv4 (0x0800)
    Hardware size: 6
    Protocol size: 4
    Opcode: reply (2)
    Sender MAC address: 52:55:0a:00:02:03 (52:55:0a:00:02:03)
    Sender IP address: 10.0.2.3
    Target MAC address: RealtekU_12:34:56 (52:54:00:12:34:56)
    Target IP address: 10.0.2.15
```

---

### **Additional Observation**

| # | Question |
|---|---------|
| 21 | Why are there **ARP requests from other devices** in the trace, but **no corresponding ARP replies**? |

Why are there **ARP requests from other devices** in the trace, but **no corresponding ARP replies**?
- ARP requests are broadcast to all subnet devices, even if it's not the target.  Replies are unicasts, a one-to-one communication, that was not sent to you.  

---

## ARP Poisoning

Using:  
```bash
arp -s <IP> <Wrong-MAC>
```

What happens if you add a **correct IP** but **wrong Ethernet address**?  
→ This enables **ARP poisoning** ([varonis.com/blog/arp-poisoning](https://www.varonis.com/blog/arp-poisoning/)) |

## ARP Cache Entry Timeout

### Linux

What is the **default ARP cache entry timeout**?  

- Linux: `60` seconds

```
cat /proc/sys/net/ipv4/neigh/default/gc_stale_time
60
```

### macOS

Check current ARP cache and observe timeouts using:

```bash
arp -a -n
```

For more detail (including expiration):

```bash
arp -an       # For IPv4
sudo ndp -an  # For IPv6 (NDP is the IPv6 equivalent of ARP)
```

### Windows

To view the current **[Reachable Time](https://learn.microsoft.com/en-us/troubleshoot/windows-server/networking/address-resolution-protocol-arp-caching-behavior)** value on a Windows system:

1. Open Command Prompt (search `cmd` in Start).
2. Run:  
   ```
   netsh interface ipv4 show interfaces
   ```
   - Note the **Idx** of your network interface (e.g., `9` for "Local Area Connection").

3. Then run:  
   ```
   netsh interface ipv4 show interface <Idx>
   ```
   (Replace `<Idx>` with the number, e.g., `9`.)

**Example output includes:**  
```
Reachable Time : 19000 ms
Base Reachable Time : 30000 ms
```

This shows the default ARP cach entry timeout (default base: 30 sec, often randomized to ~19 sec).

## What I Learned

I learned
- how to find the source and destination Ethernet address of an ARP request and reply
- how to identify the device associated with an Ethernet addres
- how the layer 2 Type field specifies the upper-layer protocol that is used
- the first byte of a TCP payload of an HTTP GET request and response is the 55th byte
- that the first 54 bytes of an Ethernet frame for a HTTP GET are the 
  - a 14 byte Ethernet header
  - a 20 byte IP header
  - a 20 byte TCP header
- ARP is a layer 2 protocol that maps layer 3 IP addresses to layer 2 MAC/Ethernet addresses
- to use the `arp -a command to show my device's ARP cache entries
- ARP cache entry info includes
	- the hostname and IP address
	- MAC address
	- the ARP-entry type
	- the interface
- how to clear ARP cache entries
- APR requests and replies show the layer 2 Type field's upper-layer protocol as ARP
- the ARP Opcode field begins between 20 to 21 bytes from the start of the Ethernet frame
- the ARP Opcode value shows that ARP is doing, such as a request or reply
- ARP requests include the IP addresses of the sender and the target that is being resolved
- ARP rpplies return the MAC/Ethernet address that matches the ARP request's IP address
- ARP requests from other devices may appear,  but no replies will be seen because replies are one-to-one unicast communications that are not sent to my device
- how ARP poisoning works
- the default ARP cache entry timeout durations for macOS, Linux, and Windows
