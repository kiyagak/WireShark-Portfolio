# Wireshark Lab: 802.11 Wi-Fi - Basic

## Objective

The goal in this [basic 802.11 Wi-Fi Wireshark lab](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_802.11_v9.pdf) is to learn about Wi-Fi beacon frames, data transfers, and the disassociation/authentication/association process. 

---

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

**Filter**: `wlan.fc.type_subtype == 8`

1. What are the **SSIDs** of the two access points issuing **most beacon frames** in this trace?  
   *(Look in the Info column)*

2. What **802.11 channel** is being used by **both** of these access points?  
   *(Check radio information in beacon frame)*

**Now analyze the beacon frame at `t = 0.085474`:**

3. What is the **interval between beacon frame transmissions** from this AP?  
   *(Field inside the beacon frame)*

4. What is the **source MAC address** (in hex) on the beacon frame from this AP?

5. What is the **destination MAC address** (in hex) on the beacon frame from **30 Munroe St**?

6. What is the **BSS ID (MAC)** (in hex) on the beacon frame from **30 Munroe St**?

7. The **30 Munroe St** AP advertises:
   - **4 supported data rates**
   - **8 extended supported rates**  
   → List **all 12 rates** (in Mbps).

---

## **Section 3: Data Transfer**

**Context**:  
- Host already associated with AP at start  
- `t = 24.8282`: HTTP GET → `alice.txt` (128.119.245.12)  
- `t = 32.8259`: HTTP GET → `www.cs.umass.edu` (128.119.240.19)

8. **Find the 802.11 frame at `t = 24.8110`** containing the **TCP SYN** for `alice.txt`:  
   - List the **three MAC addresses** in the 802.11 frame.  
   - Which is the **wireless host**? (**hex**)  
   - Which is the **access point**? (**hex**)  
   - Which is the **first-hop router**? (**hex**)  
   - What is the **source IP** of the TCP SYN?  
   - What is the **destination IP**?

9. Does the **destination IP** of this TCP SYN belong to:  
   - Wireless host  
   - Access point  
   - First-hop router  
   - Web server (`gaia.cs.umass.edu`)?

10. **Find the 802.11 frame at `t = 24.8277`** containing the **TCP SYN-ACK**:  
    - List the **three MAC addresses**.  
    - Identify: **host**, **AP**, **router**.  
    - Does the **sender MAC** in the 802.11 frame match the **IP source** of the TCP segment inside?  
      *(Review Figure 6.19 in textbook)*

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
