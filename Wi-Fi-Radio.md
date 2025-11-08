# Wireshark Lab: WiFi Radio

## Objective

The goal in this [Wi-Fi radio Wireshark lab](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_WiFi_Radio_v9.pdf) is to analyze **radio (physical layer) information** in captured 802.11 frames using **Radiotap v0 header**. 

### **Lab Overview & Resources**
- **Trace File to Use:**
  - Download: [http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip](http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip)
  - Extract: `carioca_Iphone_association_selected_packets_only.pcapng`

---

### **Part 1: Analyze Beacon Frame (Packet #3)**

> Select **Packet 3** (beacon frame from *carioca* AP).  
> Expand **Radiotap v0** field in packet details.  
> Use: [https://www.radiotap.org/fields/defined](https://www.radiotap.org/fields/defined)

<img width="756" height="575" alt="Screenshot-20251108T222403" src="https://github.com/user-attachments/assets/a54b6b68-bbb3-4cff-a839-588dcdd2db14" />

```
3	0.027048	Netgear_4c:14:6b	Broadcast	802.11	468	Beacon frame, SN=3593, FN=0, Flags=........C, BI=104, SSID="carioca"

Radiotap Header v0, Length 56
    Header revision: 0
    Header pad: 0
    Header length: 56
    Present flags
    MAC timestamp: 84048
    Flags: 0x12
    Data Rate: 6.0 Mb/s
    Channel frequency: 5200 [A 40]
    Channel flags: 0x0140, Orthogonal Frequency-Division Multiplexing (OFDM), 5 GHz spectrum
    Antenna signal: -81 dBm
    Antenna noise: -93 dBm
    Antenna: 1
    Vendor namespace: Broadcom-0
    Vendor namespace: Broadcom-3
```

Answer the following:

1. **What WiFi channel number is this frame being carried on?**  
- `40`

2. **What is the frequency of this channel?** (in MHz)
- `5200`

```
Channel frequency: 5200 [A 40]
```

3. **What is the bandwidth of this channel?** (e.g., 20 MHz, 40 MHz)
- `20 MHz`

4. **What is the data rate of this channel?** (in Mbps)
- `6.0 Mb/s`

```
Data Rate: 6.0 Mb/s
```

5. **Which modulation technique is being used?** (e.g., OFDM, DSSS, etc.)
- `Orthogonal Frequency-Division Multiplexing (OFDM)`

```
Channel flags: 0x0140, Orthogonal Frequency-Division Multiplexing (OFDM), 5 GHz spectrum
```

6. **How many antennas are on the receiver’s wireless interface?**
- `1`

```
Antenna: 1
```

7. **What is the radio signal strength at the receiver’s antenna?** (in **dBm**)
- `-81`

Antenna signal: -81 dBm

8. **Convert the signal strength from dBm to milliwatts (mW)**
- `0.0000794 mW` (or **7.94 × 10⁻⁵ mW**)

**Calculation:**  
Signal strength = **-81 dBm**

Convert dBm to mW using the formula:  

P(mW) = 10($ P(\text{mW}) = 10^{(\text{dBm}/10)} $)

$ P(\text{mW}) = 10^{(-81)/10} = 10^{-8.1} = 0.000000079433 ≈ 7.94 \times 10^{-8} \text{ W} = 0.0000794 \text{ mW} $


**Final Answer: 0.0000794 mW**

9. **What is the noise floor strength at the receiver’s antenna?** (in **dBm**)

Antenna noise: -93 dBm

10. **Convert the noise strength from dBm to milliwatts (mW)**

**0.00000501 mW** (or **5.01 × 10⁻⁶ mW**)

**Calculation:**  
Noise strength = **-93 dBm**

Convert dBm to mW using the formula:  
\[ P(\text{mW}) = 10^{\frac{\text{dBm}}{10}} \]

\[
P(\text{mW}) = 10^{\frac{-93}{10}} = 10^{-9.3} = 0.00000000501187 \approx 5.01 \times 10^{-6}~\text{mW}
\]

**Final Answer: 0.00000501 mW**

11. **What is the Signal-to-Noise Ratio (SNR) at the receiver’s antenna?** (in dB)
- `12`

Signal-to-noise (SNR) ratio = `Antenna signal` - `Antenna noise` = (-81 dBm) - (-93 dBm) = `12`

---

### **Part 2: Compare with Another Beacon (Packet #258)**

> Select **Packet 258** (another beacon from *carioca* AP)  
> Again expand **Radiotap v0** field

Answer the following:

12. **What WiFi channel number is frame 258 being carried on?**
- `40`

```
Channel frequency: 5200 [A 40]
```

13. **Compare signal strength (dBm) of frame 258 vs. frame 3:**  

```
3	0.027048	Netgear_4c:14:6b	Broadcast	802.11	468	Beacon frame, SN=3593, FN=0, Flags=........C, BI=104, SSID="carioca"

Radiotap Header v0, Length 56
    Antenna signal: -81 dBm

258	5.176765	Netgear_4c:14:d7	Broadcast	802.11	468	Beacon frame, SN=525, FN=0, Flags=........C, BI=104, SSID="carioca"

Radiotap Header v0, Length 56
    Antenna signal: -57 dBm
```

How many **orders of magnitude** stronger or weaker is the received signal?
- `24` dBm stronger
- `-57` dBm - `-81` dBm = `24` dBm

14. **Compare noise strength (dBm) of frame 258 vs. frame 3:**  

```
258	5.176765	Netgear_4c:14:d7	Broadcast	802.11	468	Beacon frame, SN=525, FN=0, Flags=........C, BI=104, SSID="carioca"

Radiotap Header v0, Length 56
    Antenna noise: -93 dBm

3	0.027048	Netgear_4c:14:6b	Broadcast	802.11	468	Beacon frame, SN=3593, FN=0, Flags=........C, BI=104, SSID="carioca"

Radiotap Header v0, Length 56
    Antenna noise: -93 dBm
```

How many **orders of magnitude** stronger or weaker is the noise?
- `0`

`-93` dBm - `-93` dBm = `0`

<img width="756" height="575" alt="Screenshot-20251108T230949" src="https://github.com/user-attachments/assets/7780e15f-9ec9-443b-ab86-f687bab41de7" />




---

**Notes:**
- Radiotap and 802.11 Radio Information fields contain **driver-level PHY data**, not part of 802.11 protocol.
- This lab uses **pre-captured trace** — monitor mode not required.
- All answers derived from **Radiotap v0** fields (preferred over 802.11 radio info due to redundancy).

---
## What I Learned

I learned
- how to analyze a Wi-Fi radio beacon frame to identify
	- Wi-Fi channel number
	- channel frequency
	- channel bandwdith
	- channel data rate
	- modulation technique
	- number of antennas on the receiver wireless interface
	- radio signal strength at the receiver’s antenna
	- the formula to convert the signal strength and noise floor strength from dBm to milliwatts (mW)
	- noise floor strength at the receiver’s antenna
	- how to calculate the Signal-to-Noise Ratio (SNR)
	- how to compare different packets' differences in signal strength or noise floor strength
