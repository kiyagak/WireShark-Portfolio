# Wireshark Lab: 802.11 Wi-Fi - Basic

## Objective

The goal in this [basic 802.11 Wi-Fi Wireshark lab](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_802.11_v9.pdf) is to learn about Wi-Fi beacon frames, data transfers, and the disassociation/authentication/association process. 

---

## Disable QUIC in Chrome

1.  Open Chrome and type `chrome://flags` in the address bar.
2.  Search for "QUIC."
3.  Disable the "Experimental QUIC Protocol" option. (Alternatively, go to `chrome://flags/#enable-quic`)
4.  Relaunch Chrome.

<img width="857" height="512" alt="image" src="https://github.com/user-attachments/assets/d1949f9d-e4d5-4a10-a1f8-21d5f0abe15d" />

## Disable QUIC in Firefox

1.  Open Firefox and type `about:config` in the address bar.
2.  Accept the risk and continue.
3.  Search for `network.http.http3.enable`.
4.  Double-click `network.http.http3.enable`, `network.http.http3.enable_0rtt`, and `network.http.http3.enable_qlog` to set their values to `false`.
5.  Restart Firefox.

## Disable QUIC in Edge

1.  Open Edge and type `edge://flags` in the address bar.
2.  Search for "QUIC."
3.  Disable the "Experimental QUIC Protocol" option.
4.  Restart Edge.

## **Lab Setup Instructions**

1. **Ensure you are connected to a WiFi network (your home network)** when trace collection starts.
2. Perform the following actions in order:
   1. Make an HTTP request to:  
      `http://gaia.cs.umass.edu/wireshark-labs/alice.txt`
   2. Make an HTTP request to:  
      `http://www.cs.umass.edu`
   3. **Disconnect** from your home network.
   4. *(Optional)* Try to connect to another 802.11 network (whose beacons are visible) **for which you do not have access** → connection will fail.
   5. **Reconnect successfully** to your home network.

3. **Use provided trace file if unable to capture live**:  
   Download: `Wireshark_801_11.pcapng` from  
   `http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip`
4. **Open trace in Wireshark** via:  
   `File → Open → Wireshark_801_11.pcapng`
5. **Use display filters** to isolate frame types (refer to cheat sheet):  
   `https://gaia.cs.umass.edu/wireshark-labs/802.11-9.2.4.1_spec+wireshark_filters.pdf`

<img width="687" height="436" alt="image" src="https://github.com/user-attachments/assets/865c1ce5-5fb0-4558-90df-a92e75366660" />

<img width="506" height="657" alt="image" src="https://github.com/user-attachments/assets/c78377fb-a978-4331-ba3c-f72fd3897843" />

---

## **Section 2: Beacon Frames**

**Filters**: 
`wlan.fc.type_subtype == 8`
`wlan.fc.type_subtype == 8 && wlan.ssid != "30 Munroe St"`

1. What are the **SSIDs** of the two access points issuing **most beacon frames** in this trace?  
- `30 Munroe St` and `linksys12`

2. What **802.11 channel** is being used by **both** of these access points?  
- `6`

```
1	0.000000	Cisco-Li_f7:1d:51	Broadcast	802.11	183	Beacon frame, SN=2854, FN=0, Flags=........C, BI=100, SSID="30 Munroe St"

802.11 radio information
    PHY type: 802.11b (HR/DSSS) (4)
    Short preamble: False
    Data rate: 1.0 Mb/s
    Channel: 6
    Frequency: 2437MHz
    Signal strength (dB): 71 dB
    Signal strength (dBm): -29 dBm
    Noise level (dBm): -100 dBm
    Signal/noise ratio (dB): 71 dB
    [Duration: 1464µs]

14	0.499197	LinksysG_67:22:94	Broadcast	802.11	90	Beacon frame, SN=3074, FN=0, Flags=........C, BI=100, SSID="linksys12"

802.11 radio information
    PHY type: 802.11b (HR/DSSS) (4)
    Short preamble: False
    Data rate: 2.0 Mb/s
    Channel: 6
    Frequency: 2437MHz
    Signal strength (dB): 11 dB
    Signal strength (dBm): -89 dBm
    Noise level (dBm): -100 dBm
    Signal/noise ratio (dB): 11 dB
    [Duration: 456µs]
```

<img width="1193" height="570" alt="Screenshot-20251107T211302" src="https://github.com/user-attachments/assets/1d34334e-c811-46a6-bd8e-6d70d9fc11a1" />

**Now analyze the beacon frame at `t = 0.085474`:**

3. What is the **interval between beacon frame transmissions** from this AP?  
- `1.0 Mb/s`

4. What is the **source MAC address** (in hex) on the beacon frame from this AP?
- `00:16:b6:f7:1d:51`

5. What is the **destination MAC address** (in hex) on the beacon frame from **30 Munroe St**?
- `ff:ff:ff:ff:ff:ff`

6. What is the **BSS ID (MAC)** (in hex) on the beacon frame from **30 Munroe St**?
- `00:16:b6:f7:1d:51`

```
3	0.085474	Cisco-Li_f7:1d:51	Broadcast	802.11	183	Beacon frame, SN=2855, FN=0, Flags=........C, BI=100, SSID="30 Munroe St"

802.11 radio information
    Data rate: 1.0 Mb/s
    Channel: 6
IEEE 802.11 Beacon frame, Flags: ........C
    Destination address: Broadcast (ff:ff:ff:ff:ff:ff)
    Source address: Cisco-Li_f7:1d:51 (00:16:b6:f7:1d:51)
    BSS Id: Cisco-Li_f7:1d:51 (00:16:b6:f7:1d:51)
```

7. The **30 Munroe St** AP advertises:
   - **4 supported data rates**
   - **8 extended supported rates**  
   → List **all 12 rates** (in Mbps).

```
3	0.085474	Cisco-Li_f7:1d:51	Broadcast	802.11	183	Beacon frame, SN=2855, FN=0, Flags=........C, BI=100, SSID="30 Munroe St"

IEEE 802.11 Wireless Management
    Tagged parameters (119 bytes)
        Tag: Supported Rates 1(B), 2(B), 5.5(B), 11(B), [Mbit/sec]
            Tag Number: Supported Rates (1)
            Tag length: 4
            Supported Rates: 1(B) (0x82)
            Supported Rates: 2(B) (0x84)
            Supported Rates: 5.5(B) (0x8b)
            Supported Rates: 11(B) (0x96)
        Tag: Extended Supported Rates 6(B), 9, 12(B), 18, 24(B), 36, 48, 54, [Mbit/sec]
            Tag Number: Extended Supported Rates (50)
            Tag length: 8
            Extended Supported Rates: 6(B) (0x8c)
            Extended Supported Rates: 9 (0x12)
            Extended Supported Rates: 12(B) (0x98)
            Extended Supported Rates: 18 (0x24)
            Extended Supported Rates: 24(B) (0xb0)
            Extended Supported Rates: 36 (0x48)
            Extended Supported Rates: 48 (0x60)
            Extended Supported Rates: 54 (0x6c)
```

<img width="1193" height="900" alt="Screenshot-20251107T211840" src="https://github.com/user-attachments/assets/7e18960e-b974-4b5e-9893-4044579817b3" />

---

## **Section 3: Data Transfer**

**Context**:  
- Host already associated with AP at start  
- `t = 24.8282`: HTTP GET → `alice.txt` (128.119.245.12)  
- `t = 32.8259`: HTTP GET → `www.cs.umass.edu` (128.119.240.19)

8. **Find the 802.11 frame at `t = 24.8110`** containing the **TCP SYN** for `alice.txt`:  
- List the **three MAC addresses** in the 802.11 frame.
   - Cisco-Li_f7:1d:51 (`00:16:b6:f7:1d:51`)
   - IntelCor_d1:b6:4f (`00:13:02:d1:b6:4f`)
   - Cisco-Li_f4:eb:a8 (`00:16:b6:f4:eb:a8`)

- Which is the **wireless host**? (**hex**)  
   - IntelCor_d1:b6:4f (`00:13:02:d1:b6:4f`)
   - The STA address is the station address of the wireless host or client.  

         STA address: IntelCor_d1:b6:4f (00:13:02:d1:b6:4f)

- Which is the **access point**? (**hex**)
   - Cisco-Li_f7:1d:51 (`00:16:b6:f7:1d:51`)

         BSS Id: Cisco-Li_f7:1d:51 (00:16:b6:f7:1d:51)

- Which is the **first-hop router**? (**hex**)
   - Cisco-Li_f7:1d:51 (`00:16:b6:f7:1d:51`)
   - It gets the STA's frame and forwards the encapsulated IP packet to the destination address.  

```
Destination address: Cisco-Li_f4:eb:a8 (00:16:b6:f4:eb:a8)
BSS Id: Cisco-Li_f7:1d:51 (00:16:b6:f7:1d:51)
STA address: IntelCor_d1:b6:4f (00:13:02:d1:b6:4f)
```

- What is the **source IP** of the TCP SYN?
   - `192.168.1.109`

- What is the **destination IP**?
   - `128.119.245.12`

9. Does the **destination IP** of this TCP SYN belong to:  
   - Wireless host  
   - Access point  
   - First-hop router  
   - Web server (`gaia.cs.umass.edu`)?

```
474	24.811093	192.168.1.109	128.119.245.12	TCP	110	2538 → 80 [SYN] Seq=0 Win=16384 Len=0 MSS=1460 SACK_PERM

802.11 radio information
IEEE 802.11 QoS Data, Flags: .......TC
    Type/Subtype: QoS Data (0x0028)
    Frame Control Field: 0x8801
    .000 0000 0010 1100 = Duration: 44 microseconds
    Receiver address: Cisco-Li_f7:1d:51 (00:16:b6:f7:1d:51)
    Transmitter address: IntelCor_d1:b6:4f (00:13:02:d1:b6:4f)
    Destination address: Cisco-Li_f4:eb:a8 (00:16:b6:f4:eb:a8)
    Source address: IntelCor_d1:b6:4f (00:13:02:d1:b6:4f)
    BSS Id: Cisco-Li_f7:1d:51 (00:16:b6:f7:1d:51)
    STA address: IntelCor_d1:b6:4f (00:13:02:d1:b6:4f)
    .... .... .... 0000 = Fragment number: 0
    0000 0011 0001 .... = Sequence number: 49
    Frame check sequence: 0xad57fce0 [unverified]
    [FCS Status: Unverified]
    Qos Control: 0x0000
Internet Protocol Version 4, Src: 192.168.1.109, Dst: 128.119.245.12
    Source Address: 192.168.1.109
    Destination Address: 128.119.245.12
```

<img width="1193" height="570" alt="Screenshot-20251107T211028" src="https://github.com/user-attachments/assets/7d3a2b9f-1bdb-42ad-ab0a-a37d4fb79e02" />

10. **Find the 802.11 frame at `t = 24.8277`** containing the **TCP SYN-ACK**:  
- List the **three MAC addresses**.
   - Cisco-Li_f4:eb:a8 (`00:16:b6:f4:eb:a8`)
   - 91:2a:b0:49:b6:4f (`91:2a:b0:49:b6:4f`)
   - Cisco-Li_f7:1d:51 (`00:16:b6:f7:1d:51`)

```
Source address: Cisco-Li_f4:eb:a8 (00:16:b6:f4:eb:a8)
BSS Id: Cisco-Li_f7:1d:51 (00:16:b6:f7:1d:51)
STA address: 91:2a:b0:49:b6:4f (91:2a:b0:49:b6:4f)
```

- Identify: **host**, **AP**, **router**.
   - Host: 91:2a:b0:49:b6:4f (`91:2a:b0:49:b6:4f`)
   - AP: Cisco-Li_f7:1d:51 (`00:16:b6:f7:1d:51`)

         BSS Id: Cisco-Li_f7:1d:51 (00:16:b6:f7:1d:51)

   - Router: Cisco-Li_f4:eb:a8 (`00:16:b6:f4:eb:a8`)
   - Source address in the 802.11 header
   - The router is the original source of the frame on the wired side, forwarding the packet from 128.119.245.12 to the wireless client via the AP.

```
Source address: Cisco-Li_f4:eb:a8 (00:16:b6:f4:eb:a8)
Source Address: 128.119.245.12
```

- Does the **sender MAC** (source address) in the 802.11 frame match the **IP source** of the TCP segment inside?  
   - No

- Who sent the TCP [SYN,ACK]?
   - `128.119.245.12`

- Who transmitted the 802.11 frame?
   - **Access Point** (`TA = 00:16:b6:f7:1d:51`)

- What is `00:16:b6:f4:eb:a8`?
   - **Wired-side MAC** that sent the packet into the AP. 

```
476	24.827751	128.119.245.12	192.168.1.109	TCP	110	80 → 2538 [SYN, ACK] Seq=0 Ack=1 Win=5840 Len=0 SACK_PERM

IEEE 802.11 QoS Data, Flags: ..mP..F.C
    Type/Subtype: QoS Data (0x0028)
    Frame Control Field: 0x8832
    Duration/ID: 11560 (reserved)
    Receiver address: 91:2a:b0:49:b6:4f (91:2a:b0:49:b6:4f)
    Transmitter address: Cisco-Li_f7:1d:51 (00:16:b6:f7:1d:51)
    Destination address: 91:2a:b0:49:b6:4f (91:2a:b0:49:b6:4f)
    Source address: Cisco-Li_f4:eb:a8 (00:16:b6:f4:eb:a8)
    BSS Id: Cisco-Li_f7:1d:51 (00:16:b6:f7:1d:51)
    STA address: 91:2a:b0:49:b6:4f (91:2a:b0:49:b6:4f)
    .... .... .... 0000 = Fragment number: 0
    1100 0011 0100 .... = Sequence number: 3124
    Frame check sequence: 0xecdc407d [unverified]
    [FCS Status: Unverified]
    Qos Control: 0x0100
Internet Protocol Version 4, Src: 128.119.245.12, Dst: 192.168.1.109
    Source Address: 128.119.245.12
    Destination Address: 192.168.1.109
```

<img width="1193" height="570" alt="Screenshot-20251107T211146" src="https://github.com/user-attachments/assets/59d7a700-b364-4cb8-8b91-7c2c22b317c0" />

---

## **Section 4: Disassociation / Authentication / Association**

**Timeline**:  
- `t = 49.5836`: Host disconnects from **30 Munroe St**  
- `t = 63.0592`: Host re-associates

11. **Just after `t = 49`**, what **two actions** does the host take to end association?  

One is **802.11-layer**:
- A QoS Null data (no data) with PM=1 shows the client wants to disassociate or disconnect from the access point. 
- with Power Management (PM) bit set to `1` (`Flags=.......TC`)
- To DS (Distribution System) = 1 (frame going to the AP)
- CF-Poll (Contention-Free Poll) = 1 (this is a QoS CF-Poll frame)

```
1712	49.020125	IntelCor_d1:b6:4f	Cisco-Li_f7:1d:51	802.11	54	QoS Null function (No data), SN=1599, FN=0, Flags=.......TC

IEEE 802.11 QoS Null function (No data), Flags: .......TC
    Type/Subtype: QoS Null function (No data) (0x002c)
```

<img width="1193" height="570" alt="Screenshot-20251107T205701" src="https://github.com/user-attachments/assets/55f22296-b5c5-4728-8529-40ed88459aee" />

One is **IP-layer**:
- [SYN, PSH, ECE, AE] action makes the client to close its TCP session cleanly
- It is sent from the server (128.119.101.5) to the client (192.168.1.109) on an ephemeral port (2543).
- An ephemeral port is a temporary transport-layer port number automatically assigned automatically by the OS to a client app.

```
1714	49.020356	128.119.101.5	192.168.1.109	TCP	108	80 → 2543 [SYN, PSH, ECE, AE] Seq=2758133200 Win=7504[Malformed Packet]
```

<img width="1193" height="570" alt="Screenshot-20251107T205933" src="https://github.com/user-attachments/assets/4d4ca700-043e-415f-9d5c-58ad18b1d470" />

---

### **Authentication (Filter: `wlan.fc.subtype == 11`)**

12. At `t = 63.1680`, host sends **Authentication Request** → AP  
- What **authentication algorithm** is requested?
   - Open System (0)

```
Authentication Algorithm: Open System (0)
```

13. What is the **Authentication Sequence Number (SEQ)** in this frame (host → AP)?
- 1 (`0x0001` in hexadecimal)

```
Authentication SEQ: 0x0001
```

```
2156	63.168087	IntelCor_d1:b6:4f	Cisco-Li_f7:1d:51	802.11	58	Authentication, SN=1647, FN=0, Flags=........C

IEEE 802.11 Wireless Management
    Fixed parameters (6 bytes)
        Authentication Algorithm: Open System (0)
        Authentication SEQ: 0x0001
        Status code: Successful (0x0000)
```

<img width="1193" height="570" alt="Screenshot-20251107T210357" src="https://github.com/user-attachments/assets/822e6e92-eb31-4699-9a0b-7574343058d3" />

14. At `t = 63.1690`, AP sends **Authentication Response**  

Was the requested authentication **accepted**?
- Yes

```
Status code: Successful (0x0000)
```

15. What is the **Authentication SEQ** in the AP → host response?
- 2 (`0x0002` in hexadecimal)

```
Authentication SEQ: 0x0002
```

```
2158	63.169071	Cisco-Li_f7:1d:51	IntelCor_d1:b6:4f	802.11	58	Authentication, SN=3726, FN=0, Flags=........C

IEEE 802.11 Wireless Management
    Fixed parameters (6 bytes)
        Authentication Algorithm: Open System (0)
        Authentication SEQ: 0x0002
        Status code: Successful (0x0000)
```

<img width="1193" height="570" alt="Screenshot-20251107T205233" src="https://github.com/user-attachments/assets/9a05f987-8c38-451d-b9bc-423d8ce9b5ab" />

---

### **Association (Filter: `wlan.fc.subtype < 2 && wlan.fc.type == 0`)**

16. **Association Request** sent at `t = 63.1699`  
    → List **Supported Rates** (exclude Extended Supported Rates).

```
2162	63.169910	IntelCor_d1:b6:4f	Cisco-Li_f7:1d:51	802.11	89	Association Request, SN=1648, FN=0, Flags=........C, SSID="30 Munroe St"

IEEE 802.11 Wireless Management
    Tagged parameters (33 bytes)
        Tag: Supported Rates 1(B), 2(B), 5.5(B), 11(B), 6(B), 9, 12(B), 18, [Mbit/sec]
            Tag Number: Supported Rates (1)
            Tag length: 8
            Supported Rates: 1(B) (0x82)
            Supported Rates: 2(B) (0x84)
            Supported Rates: 5.5(B) (0x8b)
            Supported Rates: 11(B) (0x96)
            Supported Rates: 6(B) (0x8c)
            Supported Rates: 9 (0x12)
            Supported Rates: 12(B) (0x98)
            Supported Rates: 18 (0x24)
```

<img width="1193" height="570" alt="Screenshot-20251107T204304" src="https://github.com/user-attachments/assets/9df61e9b-e6c1-4767-afde-6f230416d2ea" />

17. **Association Response** received at `t = 63.1921`  
- Is the status: **Successful** or **Unsuccessful**?
   - Successful

```
2166	63.192101	Cisco-Li_f7:1d:51	IntelCor_d1:b6:4f	802.11	94	Association Response, SN=3728, FN=0, Flags=........C

IEEE 802.11 Wireless Management
    Fixed parameters (6 bytes)
        Status code: Successful (0x0000)
```

<img width="1193" height="570" alt="Screenshot-20251107T204514" src="https://github.com/user-attachments/assets/822cc4d0-4ae2-424e-9e08-c7f1b85a8d71" />

18. Compare **Extended Supported Rates**:  

Does the **fastest rate offered by the host** match the **fastest rate the AP supports**?
- No

```
2162	63.169910	IntelCor_d1:b6:4f	Cisco-Li_f7:1d:51	802.11	89	Association Request, SN=1648, FN=0, Flags=........C, SSID="30 Munroe St"

IEEE 802.11 Wireless Management
    Tagged parameters (33 bytes)
        Tag: Supported Rates 1(B), 2(B), 5.5(B), 11(B), 6(B), 9, 12(B), 18, [Mbit/sec]
            Tag Number: Supported Rates (1)
            Tag length: 8
            Supported Rates: 1(B) (0x82)
            Supported Rates: 2(B) (0x84)
            Supported Rates: 5.5(B) (0x8b)
            Supported Rates: 11(B) (0x96)
            Supported Rates: 6(B) (0x8c)
            Supported Rates: 9 (0x12)
            Supported Rates: 12(B) (0x98)
            Supported Rates: 18 (0x24)

2166	63.192101	Cisco-Li_f7:1d:51	IntelCor_d1:b6:4f	802.11	94	Association Response, SN=3728, FN=0, Flags=........C

IEEE 802.11 Wireless Management
    Tagged parameters (36 bytes)
        Tag: Extended Supported Rates 6(B), 9, 12(B), 18, 24(B), 36, 48, 54, [Mbit/sec]
            Tag Number: Extended Supported Rates (50)
            Tag length: 8
            Extended Supported Rates: 6(B) (0x8c)
            Extended Supported Rates: 9 (0x12)
            Extended Supported Rates: 12(B) (0x98)
            Extended Supported Rates: 18 (0x24)
            Extended Supported Rates: 24(B) (0xb0)
            Extended Supported Rates: 36 (0x48)
            Extended Supported Rates: 48 (0x60)
            Extended Supported Rates: 54 (0x6c)
```

<img width="1193" height="582" alt="Screenshot-20251107T204049" src="https://github.com/user-attachments/assets/56c3d61d-dc8c-4514-af10-fb08c01ac7ee" />

<img width="1193" height="662" alt="Screenshot-20251107T203720" src="https://github.com/user-attachments/assets/18b4152a-2b44-4911-bbb9-409f41fdb737" />


---

## What I Learned

## What I Learned

I learned
- how Wi-Fi beacon frames work
- how to filter for them in Wireshark
- how to filter for a specific SSID
- that the beacon frames' 802.11 channel is the same for both access points
- how to find the beacon frame's data rate
- that the beacon frame's data rate is 1.0 Mbp/s
- how to find the source, destination, and router MAC/Ethernet addresses
- how to find the supported and extended data rates of a beacon frame
- how data transfers happen
- how to distinguish the wireless host, access point, and first-hop router within a 802.11 frame that is doing a TCP SYN action
- the STA address identifies the wireless host or client
- how to map MAC/Ethernet addresses to their source or destination IP address
- TCP SYN/ACK action
- Hard-wired devices can send packets to access points
- the disassociation, authentication, and association processes
- the disassocation is done with these steps:
	- the first step is a QoS Null data (no data) to disconnect from the access point at the 802.11-layer
	- the second step is [SYN, PSH, ECE, AE] action makes the client to close its TCP session cleanly, done at the IP layer
- the authentication process 
	- used the Open System (0) authentication algorithm
	- uses sequence numbers for each step involved with authentication
	- the status code field shows if the authentication worked or not
- association is the process of connecting to an access point
	- an assocation is requested
	- the access point responds with permission or denial, confirmed by Wireshark's status code field	
- fastest rate offered by the host requesting association was 18Mbps, unlike the fastest rate the AP supports, which is 54Mbps
