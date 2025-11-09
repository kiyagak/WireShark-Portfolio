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
- Place `scat-v2.lua` in the correct directory for Wireshark to find and install it.
- Official Wireshark docs: [Plugin Folders](https://www.wireshark.org/docs/wsug_html_chunked/ChPluginFolders.html)
- Reboot your computer after installing the `.lua` file.
- Open `trace4-scatv2-5G-NR-feb-19-2025.pcapng` in Wireshark.

---

## Investigating the 5G RAN

1. **Packet 1 (MIB Message):**
   - Expand NR Radio Resource Control (RRC) protocol and BCCH-BCH-Message line.
   - Questions:
     - What are the subcarrier spacings (channel widths) used in this 5G network?
     - Is this network open for use?
     - What is the value of the SFN?

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

## Attaching to the 5G RAN

- Review Section 7.4.3 of the textbook and additional resources:
  - [Discovery and attaching to the RAN/WLAN](https://gaia.cs.umass.edu/wireless_and_mobile_networks/readings/Chapter_6_Edge_topi)
  - [User identity, registration, and session establishment](https://gaia.cs.umass.edu/wireless_and_mobile_networks/readings/Chapter_8_5G_Core.p)

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

