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

---

## **Section 4: Disassociation / Authentication / Association**

**Timeline**:  
- `t = 49.5836`: Host disconnects from **30 Munroe St**  
- `t = 63.0592`: Host re-associates

11. **Just after `t = 49`**, what **two actions** does the host take to end association?  
    - One is **IP-layer**  
    - One is **802.11-layer**

---

### **Authentication (Filter: `wlan.fc.subtype == 11`)**

12. At `t = 63.1680`, host sends **Authentication Request** → AP  
    → What **authentication algorithm** is requested?

13. What is the **Authentication Sequence Number (SEQ)** in this frame (host → AP)?

14. At `t = 63.1690`, AP sends **Authentication Response**  
    → Was the requested authentication **accepted**?

15. What is the **Authentication SEQ** in the AP → host response?

---

### **Association (Filter: `wlan.fc.subtype < 2 && wlan.fc.type == 0`)**

16. **Association Request** sent at `t = 63.1699`  
    → List **Supported Rates** (exclude Extended Supported Rates).

17. **Association Response** received at `t = 66.1921`  
    → Is the status: **Successful** or **Unsuccessful**?

18. Compare **Extended Supported Rates**:  
    → Does the **fastest rate offered by the host** match the **fastest rate the AP supports**?

---

## **Reference Resources**

| Purpose | Link |
|-------|------|
| 802.11 Tutorial | http://www.sss-mag.com/pdf/802_11tut.pdf |
| IEEE 802.11-2020 Standard (excerpt) | https://gaia.cs.umass.edu/wireshark-labs/80211-2020.pdf |
| Spec + Filter Cheat Sheet | https://gaia.cs.umass.edu/wireshark-labs/802.11-9.2.4.1_spec+wireshark_filters.pdf |
| Wireshark 802.11 Filters Reference | https://gaia.cs.umass.edu/wireshark-labs/wireshark_802.11_filters_-_reference_sheet.pdf |
| Trace File | http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip |

---

**Tip**: Use **coloring rules** or **annotations** when submitting answers (highlight GET/response messages, mark fields in frames).  
**LMS auto-grading available** at: http://gaia.cs.umass.edu/kurose_ross/lms.htm

--- 

*End of Lab Instructions*
