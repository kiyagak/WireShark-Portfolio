# Wireshark Lab: IP

## Objective

The goal is to analyze [**IPv4** and **IPv6** datagrams](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_IP_v9.pdf) uasing Wireshark, focusing on:
1. **Traceroute** behavior (TTL, ICMP responses)
2. **IP fragmentation**
3. **IPv6 structure**

---

## **Lab Structure (3 Parts)**

### **Part 1: Basic IPv4 (Traceroute Analysis)**
**Goal:** Examine IPv4 datagrams sent/received during `traceroute`.

#### **Steps:**
1. Open the Terminal.
2. Install the `traceroute` package.

         sudo apt install traceroute

3. Run Wireshark → Start capture.
4. Execute:
   ```bash
   traceroute gaia.cs.umass.edu 56
   traceroute gaia.cs.umass.edu 3000
   ```
   *(Windows: use `tracert` for small packets only)*
5. Stop capture.

---

### **Wireshark Lab: UDP and Traceroute Analysis**

1. **Select the first UDP segment sent by your computer via the `traceroute` command to `gaia.cs.umass.edu`**.
   Expand the **Internet Protocol** part of the packet in the packet details window.  

		25	6.054950152	10.98.0.5	10.98.0.1	DNS	74	Standard query 0xaa57 A gaia.cs.umass.edu OPT

```
Internet Protocol Version 4, Src: 10.98.0.5, Dst: 10.98.0.1
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
    Total Length: 74
    Identification: 0xb81a (47130)
    000. .... = Flags: 0x0
    ...0 0000 0000 0000 = Fragment Offset: 0
    Time to Live: 64
    Protocol: UDP (17)
    Header Checksum: 0xadbf [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 10.98.0.5
    Destination Address: 10.98.0.1
```

<img width="1242" height="823" alt="image" src="https://github.com/user-attachments/assets/eb585a85-d4e0-4d98-8573-0a2d90047c0f" />

### **Wireshark Lab: UDP and Traceroute Analysis**

1. **Select the first UDP segment sent by your computer via the `traceroute` command to `gaia.cs.umass.edu`**.
   Expand the **Internet Protocol** part of the packet in the packet details window.  

   **What is the IP address of your computer?**
   - `10.98.0.5`

2. **What is the value in the Time-to-Live (TTL) field** in this IPv4 datagram’s header?
- `64`

3. **What is the value in the Upper Layer Protocol field** in this IPv4 datagram’s header?  
   *[Note: the answers for Linux/MacOS differ from Windows here.]*
- `UDP (17)`

4. **How many bytes are in the IP header?**
- `20 bytes (5)`

5. **How many bytes are in the payload of the IP datagram?**
- `54`

Explain how you determined the number of payload bytes.
- IP datagram payload bytes = IP total length - IP header length
- IP datagram payload bytes = `74` - `20` = `54`

6. **Has this IP datagram been fragmented?**
- No

#### How to Tell if an IP Datagram Has Been Fragmented

- **Fragmentation check**: non-fragmented packet (single fragment, offset 0, MF=0).  

- **Flags**: `000. .... = Flags: 0x0`  
  - Bit 0 (Reserved): 0  
  - Bit 1 (**Don't Fragment - DF**): **0** → **Fragmentation allowed**  
  - Bit 2 (**More Fragments - MF**): **0** → **No more fragments**  

- **Fragment Offset**: `...0 0000 0000 0000 = Fragment Offset: 0`  
  → Offset = **0** (first or only fragment)

**Wireshark would display**:  
`[IP Fragmentation: Not fragmented]` or simply no fragmentation flags set.

#### Why Wireshark Does Not Display the IP Fragmentation Field

Wireshark **does not always display** the explicit text `[IP Fragmentation: Not fragmented]` in the **packet details pane** — even when the packet is clearly **not fragmented**. Wireshark **only adds the summary tag** `[IP Fragmentation: Not fragmented]` **under specific conditions**:

---

##### When Wireshark **Does** Show `[IP Fragmentation: Not fragmented]`

This happens when:
- The packet is **part of a conversation** where **fragmentation has occurred elsewhere**.
- Wireshark is **reassembling fragments** in the background.
- The UI is in **"Analyze → Enabled Protocols"** with IP reassembly enabled.
- You're viewing the **reassembled packet** (not the individual fragment).

---

##### When it **does NOT** show the tag (your case):
- **Single, small packet** (74 bytes total — far below Maximum Transmission Unit (MTU), which is normally `1500`)
- **No other fragments** in the capture with the same **Identification** (`0xb81a`)
- Wireshark sees **no need to reassemble** → no reassembly metadata → **no tag**

> Think of it like this:  
> Wireshark only says *"Hey, this was fragmented but I put it back together"* when reassembly actually happens.  
> If there's **nothing to reassemble**, it stays silent.

---

#### How to **force** Wireshark to show fragmentation status:
1. **Enable IP reassembly** (default: on)  
   `Edit → Preferences → Protocols → IPv4 → Reassemble fragmented IPv4 datagrams` ✅

<img width="950" height="758" alt="image" src="https://github.com/user-attachments/assets/f55426fa-6d71-46c0-a684-1ea8c01c0b96" />
   
3. **Use the display filter**:  
   ```
   ip.flags.mf == 0 && ip.frag_offset == 0 && ip.len > 0
   ```
   This shows **all non-fragmented packets**.
4. **Check the "IP" summary line** in the packet list — it will show:  
   `10.98.0.5 → 10.98.0.1 Len=54` (no "Frag" indicator)
---

### UDP Fragmented Segments

**Next: Analyze the sequence of UDP segments sent from your computer to `128.119.245.12` using the display filter:**  
`ip.src==[your local IP address] and ip.dst==128.119.245.12 and udp and !icmp`

`ip.src==10.98.0.5 and ip.dst==128.119.245.12 and udp and !icmp`

<img width="1242" height="823" alt="image" src="https://github.com/user-attachments/assets/d2c10387-cbd6-4b7f-9ac8-240edd472abd" />

```
272	21.920297823	10.98.0.5	128.119.245.12	UDP	40	33066 → 33434 Len=2972

Internet Protocol Version 4, Src: 10.98.0.5, Dst: 128.119.245.12
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
    Total Length: 40
    Identification: 0xa719 (42777)
    000. .... = Flags: 0x0
    ...0 0001 0111 0010 = Fragment Offset: 2960
    Time to Live: 1
    Protocol: UDP (17)
    Header Checksum: 0x914f [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 10.98.0.5
    Destination Address: 128.119.245.12
    [3 IPv4 Fragments (2980 bytes): #270(1480), #271(1480), #272(20)]
 
 

275	21.920332460	10.98.0.5	128.119.245.12	UDP	40	49436 → 33435 Len=2972
 
Internet Protocol Version 4, Src: 10.98.0.5, Dst: 128.119.245.12
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
    Total Length: 40
    Identification: 0x11d9 (4569)
    000. .... = Flags: 0x0
    ...0 0001 0111 0010 = Fragment Offset: 2960
    Time to Live: 1
    Protocol: UDP (17)
    Header Checksum: 0x2690 [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 10.98.0.5
    Destination Address: 128.119.245.12
    [3 IPv4 Fragments (2980 bytes): #273(1480), #274(1480), #275(20)]
 
 

278	21.920364889	10.98.0.5	128.119.245.12	UDP	40	57410 → 33436 Len=2972
 
Internet Protocol Version 4, Src: 10.98.0.5, Dst: 128.119.245.12
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
    Total Length: 40
    Identification: 0xfc28 (64552)
    000. .... = Flags: 0x0
    ...0 0001 0111 0010 = Fragment Offset: 2960
    Time to Live: 1
    Protocol: UDP (17)
    Header Checksum: 0x3c40 [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 10.98.0.5
    Destination Address: 128.119.245.12
    [3 IPv4 Fragments (2980 bytes): #276(1480), #277(1480), #278(20)]
```

7. **Which fields in the IP datagram always change from one datagram to the next** within this series of UDP segments sent via `traceroute` to `128.119.245.12`?

- **Identification**
	
		Identification: 0xa719 (42777)
		Identification: 0x11d9 (4569)
		Identification: 0xfc28 (64552)

- **Header Checksum**

		Header Checksum: 0x914f [validation disabled]
		Header Checksum: 0x2690 [validation disabled]
		Header Checksum: 0x3c40 [validation disabled]

**Why?**
-  The **Identification** field is different for each datagram because it is needed for fragmentation/reassembly.  
- The **Header Checksum** field changes because it is computed over the entire IP header, including the changing Identification field.

8. **Which fields in this sequence of IP datagrams (containing UDP segments) stay constant?**

1. **Version**: `4`  
2. **Header Length**: `20 bytes (5)`
3. **Differentiated Services Field**: `0x00`
4. **Flags**: `0x0`
5. **Fragment Offset**: `2960` *(all are the third or **last** fragment of their three respective datagrams)*  
6. **Time to Live (TTL)**: `1` 
7. **Protocol**: `UDP (17)`
8. **Source Address**: `10.98.0.5`
9. **Destination Address**: `128.119.245.12`
10. **Total Length**: `40`

   **Why?**

These datagrams are **fragments of the same original UDP datagram**, sent from the same source to the same destination, using the same IP options and policies. Even though they are fragmented, certain IP header fields remain identical across all fragments:

| Field | Why these fields stay constant |
|------|------------------------|
| **Version** | All are IPv4 datagrams. |
| **Header Length** | Fixed 20-byte header. |
| **Differentiated Services** | Same QoS/treatment applied to all fragments of the same packet. |
| **Flags** | All are the third or **last** fragment of their three respective datagrams. |
| **Fragment Offset** | All are the third or **last** fragment of their three respective datagrams. |
| **Time to Live (TTL)** | Starts at 1 in all fragments because it is local/test scenario. |
| **Protocol** | All carry UDP segments. |
| **Source Address** | Same sender (`10.98.0.5`) for the entire original datagram. |
| **Destination Address** | Same final recipient (`128.119.245.12`) for the entire original datagram. |
| **Total Length** | These three packets (272, 275, 278) are the last 40 byte fragments of a 2960 byte IP datagram.  This is assuming the MTU for IP packets is 1500. The first two packets were 1480 bytes long. |

9. **Describe the pattern you see in the values in the Identification field** of the IP datagrams being sent by your computer.
- The numbers are 16-bit decimal numbers that are under 65536.  
- Five-digit decimal number → four-digit decimal number → five-digit decimal number.  

		Identification: 0xa719 (42777)
		Identification: 0x11d9 (4569)
		Identification: 0xfc28 (64552)

---

### ICMP Time Exceeded

The instructions explain how to view **ICMP "Time Exceeded"** packets sent back by routers when the IP TTL reaches zero during a traceroute. Use the Wireshark display filter to show ICMP packets destined to your machine (192.168.86.61), showing only the TTL-expired error messages from intermediate routers:

```
ip.dst==[your IP address] and icmp
ip.dst==10.98.0.5 and icmp
```

10. **What is the upper layer protocol specified in the IP datagrams returned from the routers?**  
   *[Note: the answers for Linux/MacOS differ from Windows here].*

		Protocol: ICMP (1)

11. **Are the values in the Identification fields (across the sequence of all of ICMP packets from all of the routers) similar in behavior to your answer to question 9 above?**
- Yes

```
46	6.341110382	10.98.0.1	10.98.0.5	ICMP	84	Time-to-live exceeded (Time to live exceeded in transit)
Identification: 0xd6f1 (55025)

47	6.341157269	10.98.0.1	10.98.0.5	ICMP	84	Time-to-live exceeded (Time to live exceeded in transit)
Identification: 0xd6f2 (55026)

48	6.341173918	10.98.0.1	10.98.0.5	ICMP	84	Time-to-live exceeded (Time to live exceeded in transit)
Identification: 0xd6f3 (55027)

50	6.342498147	149.40.62.126	10.98.0.5	ICMP	84	Time-to-live exceeded (Time to live exceeded in transit)
Identification: 0x3787 (14215)

51	6.342533296	149.40.62.126	10.98.0.5	ICMP	84	Time-to-live exceeded (Time to live exceeded in transit)
Identification: 0x3788 (14216)
```


12. **Are the values of the TTL fields similar, across all of ICMP packets from all of the routers?**
- Yes, they either start at `64` or `252` and after after **three** consecutive Time Exceeded packets the TTL value decreases by 1.  

```
46	6.341110382	10.98.0.1	10.98.0.5	ICMP	84	Time-to-live exceeded (Time to live exceeded in transit)
Time to Live: 64

47	6.341157269	10.98.0.1	10.98.0.5	ICMP	84	Time-to-live exceeded (Time to live exceeded in transit)
Time to Live: 64

48	6.341173918	10.98.0.1	10.98.0.5	ICMP	84	Time-to-live exceeded (Time to live exceeded in transit)
Time to Live: 64

50	6.342498147	149.40.62.126	10.98.0.5	ICMP	84	Time-to-live exceeded (Time to live exceeded in transit)
Time to Live: 63

51	6.342533296	149.40.62.126	10.98.0.5	ICMP	84	Time-to-live exceeded (Time to live exceeded in transit)
Time to Live: 63

52	6.342548484	149.40.62.126	10.98.0.5	ICMP	84	Time-to-live exceeded (Time to live exceeded in transit)
Time to Live: 63
```

<img width="1242" height="823" alt="image" src="https://github.com/user-attachments/assets/f504381a-d890-4e72-9e42-94b7b92ca771" />

```
53	6.363686996	154.54.167.145	10.98.0.5	ICMP	96	Time-to-live exceeded (Time to live exceeded in transit)
Time to Live: 252

54	6.363699234	154.54.86.109	10.98.0.5	ICMP	96	Time-to-live exceeded (Time to live exceeded in transit)
Time to Live: 252

55	6.363702547	154.54.86.109	10.98.0.5	ICMP	96	Time-to-live exceeded (Time to live exceeded in transit)
Time to Live: 252

57	6.412792239	154.54.88.222	10.98.0.5	ICMP	96	Time-to-live exceeded (Time to live exceeded in transit)
Time to Live: 251

58	6.412822139	154.54.88.206	10.98.0.5	ICMP	96	Time-to-live exceeded (Time to live exceeded in transit)
Time to Live: 251

59	6.412832107	154.54.88.206	10.98.0.5	ICMP	96	Time-to-live exceeded (Time to live exceeded in transit)
Time to Live: 251

60	6.412840207	154.54.163.249	10.98.0.5	ICMP	96	Time-to-live exceeded (Time to live exceeded in transit)
Time to Live: 250

77	6.556535673	154.54.163.249	10.98.0.5	ICMP	96	Time-to-live exceeded (Time to live exceeded in transit)
Time to Live: 250

78	6.556584960	154.54.163.249	10.98.0.5	ICMP	96	Time-to-live exceeded (Time to live exceeded in transit)
Time to Live: 250
```

<img width="1242" height="823" alt="image" src="https://github.com/user-attachments/assets/2c3458e5-72da-4f5c-9f52-052e07d6f3e7" />

---

### **Part 3: IPv6**

- Download the Kuross and Ross Wireshark traces zip file from here:
	- http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip
- Extract the zip file.  
- Open the pre-captured trace `ip-wireshark-trace2-1.pcapng` in Wireshark.  

#### **Focus:**
- DNS AAAA query (packet 20) to resolve `youtube.com` → IPv6
- IPv6 response with multiple AAAA records

#### **Key Questions (20–26):**

20. **What is the IPv6 address of the computer making the DNS AAAA request?**  
- `2601:193:8302:4620:215c:f5ae:8b40:a27a`

		20	3.814489	2601:193:8302:4620:215c:f5ae:8b40:a27a	2001:558:feed::1	DNS	91	Standard query 0x920d AAAA youtube.com
		Source Address: 2601:193:8302:4620:215c:f5ae:8b40:a27a

22. **What is the IPv6 destination address for this datagram?**  
   - `2001:558:feed::1`

	20	3.814489	2601:193:8302:4620:215c:f5ae:8b40:a27a	2001:558:feed::1	DNS	91	Standard query 0x920d AAAA youtube.com
	Destination Address: 2001:558:feed::1

23. **What is the value of the flow label for this datagram?**
- `0x63ed0`

		.... 0110 0011 1110 1101 0000 = Flow Label: 0x63ed0
	
24. **How much payload data is carried in this datagram?**
- `37` bytes

		Payload Length: 37

25. **What is the upper layer protocol to which this datagram’s payload will be delivered at the destination?**
- `UDP`

		Next Header: UDP (17)

<img width="1242" height="823" alt="image" src="https://github.com/user-attachments/assets/50ce3912-6466-43bc-b99e-e9d82ba88cbd" />

---

**DNS AAAA Response Analysis**  
*(Find the response to the AAAA request from packet 20, containing IPv6 addresses for youtube.com)*

- Select packet 20.
- Expand `Domain Name System (query)`.
- Click the field that says `[Response In: __]`.

		[Response In: 27]

<img width="1242" height="766" alt="image" src="https://github.com/user-attachments/assets/41f99a4e-46bb-4bbb-96f4-b56814542eda" />

25. **How many IPv6 addresses are returned in the response to this AAAA request?**
- 1
		
		Answers
		    youtube.com: type AAAA, class IN, addr 2607:f8b0:4006:815::200e

26. **What is the first of the IPv6 addresses returned by the DNS for youtube.com?**  
- `2607:f8b0:4006:815::200e`
- This is the **numerically smallest** address in the response (in the trace file *ip-wireshark-trace2-1.pcapng*).  

---

## What I Learned
