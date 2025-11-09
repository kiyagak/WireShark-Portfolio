# Wireshark Lab: 5G Radio

## Objective

The goal of this [5G radio Wireshark lab](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_5G_Radio_v9.pdf) is to examines **5G radio characteristics** — frequency, modulation, signal quality, and UE power limits.

---

## **Lab Setup Instructions**

1. **Download Required Files**  
   - URL: [http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip](http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip)  
   - Extract:  
     - `trace4-scatv2-5G-NR-feb-19-2025.pcapng`  
     - `scat-v2.lua` (Wireshark dissector plugin)

2. **Install the `.lua` Plugin**
- Find the **Personal Lua Plugins Folder**
  - Open Wireshark → Help → About Wireshark → Folders

| OS | Personal Plugin Folder |
|-----|------------------------|
| **Windows** | `%APPDATA%\Wireshark\plugins` |
| **macOS / Linux** | `~/.local/lib/wireshark/plugins` |

<img width="756" height="575" alt="image" src="https://github.com/user-attachments/assets/d3285a67-f19e-4e22-94b6-5c43e789378b" />

<img width="746" height="675" alt="image" src="https://github.com/user-attachments/assets/1512b4e3-ed3c-45ad-b462-4ec70f1e804d" />

- Place `scat-v2.lua` in the appropriate Wireshark plugin folder:
- **Reboot your computer** after placing the file.
- Verify that the scat-v2.lua plugin is enabled in Wireshark:
  - Open Wireshark → Help → About Wireshark → Plugins

<img width="746" height="675" alt="image" src="https://github.com/user-attachments/assets/6fe87007-a4cd-49c2-a350-a9d860ad2562" />
  
3. **Open the Trace in Wireshark**  
   - Launch Wireshark  
   - Open: `trace4-scatv2-5G-NR-feb-19-2025.pcapng`  

---

## **Lab Questions**

### **Packet 42 – RRC Reconfiguration (Downlink)**

> Expand **NR Radio Resource Control (RRC)** → All subfields (see **Figure 2**)

1. **Frequency Band**  
   → What is the **frequency band** the UE will use to communicate with the gNodeB?

2. **Frequency Range**  
   → What **range of frequencies (in MHz)** does this band occupy?  
   *(Hint: See `channel.pptx` from class – not in trace)*

3. **SSB Carrier Frequency**  
   → What is the **central frequency (in MHz)** of the carrier carrying the **Synchronization Signal Block (SSB)**?  
   - Field: `ssbFrequency`  
   - Use: [https://www.cellmapper.net/arfcn](https://www.cellmapper.net/arfcn)

4. **Modulation and Coding Scheme (MCS)**  
   → What **MCS** is used for the **physical channel carrying user data**?

---

### **Packet 114 – Measurement Report (Uplink)**

> UE reports channel quality based on the SSB reference signal

5. **RSRP (Reference Signal Received Power)**  
   → What is the **average RSRP** in **dBm**?

6. **RSRQ (Reference Signal Received Quality)**  
   → What is the **average RSRQ** in **dB**?

7. **SINR (Signal-to-Interference-plus-Noise Ratio)**  
   → What is the **average SINR** of the reference signal in **dB**?

---

### **Packet 26 – UE Capability Information (Uplink)**

> Expand **RF Parameters** → Focus on **MIMO and Power Class** (see **Figure 6**)

8. **UE Power Class (Band 41)**  
   → Based on **MIMO parameters for band 41**, what **power class** does the UE support?

9. **Maximum Transmit Power**  
   → According to the **5G NR standard**, what is the **maximum transmit power (in dBm)** for this power class?  
   *(Search online: “UE power classes”)*

---

## **Helpful Resources**

| Purpose | Link |
|-------|------|
| RRC Reconfiguration Message Structure | [howltestuffworks.blogspot.com](https://howltestuffworks.blogspot.com/2020/02/5g-nr-measurement-configuration.html) |
| ARFCN → Frequency Calculator | [cellmapper.net/arfcn](https://www.cellmapper.net/arfcn) |
| Wireshark Plugin Folder Docs | [wireshark.org/docs](https://www.wireshark.org/docs/wsug_html_chunked/ChPluginFolders.html) |

---

**Tip**: Use **Ctrl+F** in browser resources to quickly find field names.

--- 

**Done!** Now analyze the trace and answer all 9 questions.
