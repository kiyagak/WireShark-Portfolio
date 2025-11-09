# Wireshark Lab: Finding and Joining a 5G Network

## Objective

This [Wireshark lab about finding and joining a 5G network](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_5G_Finding_Joining_v9.pdf) explores how a 5G device learns about and attaches to a 5G network.

---

## Trace Files

- Download the zip file: [Wireshark Traces](http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip)
- Extract three files:
  1. `trace4-scatv2-5G-NR-feb-19-2025.pcapng`
  2. `scat-v2.lua`
  3. `gNB_trace.pcap`

---

## MIB and SIB Messages

- The 5G Master Information Block (MIB) contains:
  - Network owner/operator
  - Channel bandwidth information
  - Pointer to additional information in System Information Blocks (SIBs)
- Both MIB and SIBs are broadcast on the Physical Broadcast Channel (PBCH)

---

## Wireshark Setup

1. **Install the `.lua` Plugin**
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

2. **Open the Trace in Wireshark**  
- Launch Wireshark  
- Open: `trace4-scatv2-5G-NR-feb-19-2025.pcapng`  

---

## Investigating the 5G RAN

1. **Packet 1 (MIB Message):**

Expand NR Radio Resource Control (RRC) protocol and BCCH-BCH-Message line.

- What are the subcarrier spacings (channel widths) used in this 5G network?
	- 30 or 120
 	- `scs30or120 (1)`

```
subCarrierSpacingCommon: scs30or120 (1)
```

- Is this network open for use?
	- Yes

```
cellBarred: notBarred (1)
```

- What is the value of the SFN?
	- `78`

```
systemFrameNumber: 78 [bit length 6, 2 LSB pad bits, 0111 10.. decimal value 30]
```

<img width="833" height="538" alt="Screenshot-20251109T031432" src="https://github.com/user-attachments/assets/0b5bc9ce-64ef-47b9-b789-3f58e5999217" />

```
1	0.000000	127.0.0.1	127.0.0.2	NR RRC	48	MIB

BCCH-BCH-Message
    message: mib (0)
        mib
            systemFrameNumber: 78 [bit length 6, 2 LSB pad bits, 0111 10.. decimal value 30]
            subCarrierSpacingCommon: scs30or120 (1)
            ssb-SubcarrierOffset: 0
            dmrs-TypeA-Position: pos2 (0)
            pdcch-ConfigSIB1
                controlResourceSetZero: 12
                searchSpaceZero: 4
            cellBarred: notBarred (1)
            intraFreqReselection: allowed (0)
            spare: 00 [bit length 1, 7 LSB pad bits, 0... .... decimal value 0]
```

2. **Packet 2 (SIB1 Message):**
   - Expand NR Radio Resource Control (RRC) protocol and all following lines.
   - Questions:
     - What country is the origin country for this network?
     - What company is the owner/operator of this network?
     - What is the RAN cell identifier?
     - What is the tracking area code?
     - What is the MCC value of the second provider network?
     - Is the second owner/operator name the same as the first?

---

## Wireshark Preferences

- Go to `Analyze -> Protocols` and check:
  - MAC-LTE and mac_lte_udp
  - MAC-NR and mac_nr_udp
- On Windows: `Edit/Preferences` or on macOS: `Wireshark -> Preferences`
  - Expand `Protocols`
  - Check all options for MAC-LTE and MAC-NR
  - For NAS-5GS, check “Try to detect and decode 5G-EA0 ciphered messages”
  - For RLC-LTE and RLC-NR, check all options except “May see RLC headers only”

---

## Joining the RAN

- **Packet 10 (rrcSetupRequest):**
  - Expand NR Radio Resource Control (RRC) protocol and all subtrees.
  - Questions:
    - What value does the UE enter as its ue-identity?
    - What is the declared reason for wanting to establish this session (establishmentCause)?

- **Packet 11 (rrcSetup):**
  - Questions:
    - Which four physical channels does the UE learn about from the gNB?

- **Packet 13 (rrcSetupComplete):**
  - Questions:
    - What is the Mobile Country Code and Mobile Network Code of the UE’s home network?
    - What is the UE’s MSIN?

- **Packet 14 (InitialUEMessage):**
  - This message is sent from the gNB to the AMF, initiating the process of joining the UE to the 5G Core Functions.

---

## UE, 5G Core Interaction: Identification, Authentication, Security

- **Packet 40 (ueCapabilityEnquiry):**
  - Questions:
    - What is the 5G frequency band number that will be used for this UE session?

- **Packet 43 (UE-NR-Capability):**
  - Questions:
    - Does this UE support the longDRX and ShortDRX sleep cycles?
    - Does the UE support inter-frequency handover?

- **Packet 67 (successfulOutcome):**
  - Questions:
    - What is the ID number (AMF-UE-NGAP-ID) assigned to this UE by the AMF?

---

## Setting up the Data Plane for the UE

- **Packet 161 (UplinkNASTransport):**
  - Contains the message from the UE to the AMF, triggering the creation of a PDU session.

- **Packet 165 (AMF’s response):**
  - Questions:
    - What are the declared UE’s maximum downlink and uplink bit rates for this PDU session?
    - What is the IP address associated with the tunnel for this session from the gNB to the UPF?

- **Packet 167 (rrcReconfiguration):**
  - Questions:
    - What is the value of periodicBSR-Timer?

- **Packet 170 (NGAP message):**
  - Questions:
    - What is the IP address of the other end of the gNB-to-UPF tunnel for the UE?

## What I Learned

