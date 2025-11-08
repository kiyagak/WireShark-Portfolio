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

Answer the following:

1. **What WiFi channel number is this frame being carried on?**  
   *(Refer to Figure 7.23 in textbook for 2.4/5 GHz channel mapping)*

2. **What is the frequency of this channel?** (in MHz)

3. **What is the bandwidth of this channel?** (e.g., 20 MHz, 40 MHz)

4. **What is the data rate of this channel?** (in Mbps)

5. **Which modulation technique is being used?** (e.g., OFDM, DSSS, etc.)

6. **How many antennas are on the receiver’s wireless interface?**

7. **What is the radio signal strength at the receiver’s antenna?** (in **dBm**)

8. **Convert the signal strength from dBm to milliwatts (mW)**

9. **What is the noise floor strength at the receiver’s antenna?** (in **dBm**)

10. **Convert the noise strength from dBm to milliwatts (mW)**

11. **What is the Signal-to-Noise Ratio (SNR) at the receiver’s antenna?** (in dB)

---

### **Part 2: Compare with Another Beacon (Packet #258)**

> Select **Packet 258** (another beacon from *carioca* AP)  
> Again expand **Radiotap v0** field

Answer the following:

12. **What WiFi channel number is frame 258 being carried on?**

13. **Compare signal strength (dBm) of frame 258 vs. frame 3:**  
   → How many **orders of magnitude** stronger or weaker is the received signal?

14. **Compare noise strength (dBm) of frame 258 vs. frame 3:**  
   → How many **orders of magnitude** stronger or weaker is the noise?

---

**Notes:**
- Radiotap and 802.11 Radio Information fields contain **driver-level PHY data**, not part of 802.11 protocol.
- This lab uses **pre-captured trace** — monitor mode not required.
- All answers derived from **Radiotap v0** fields (preferred over 802.11 radio info due to redundancy).

---
## What I Learned

