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
intraFreqReselection: allowed (0)
```

- What is the value of the SFN?
	- `30`
 	- `decimal value 30`

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

Expand NR Radio Resource Control (RRC) protocol and all following lines.

- What country is the origin country for this network?
	- [United States of America](https://mcc-mnc.com/)
 	- Mobile Country Code (MCC): 310

- What company is the owner/operator of this network?
	- T-Mobile
 	- Mobile Network Code (MNC): 260

<img width="783" height="136" alt="image" src="https://github.com/user-attachments/assets/05cf3ab2-35cc-488c-98b9-cf51100e4a4e" />

```
2	0.000799	127.0.0.1	127.0.0.2	NR RRC	167	SIB1

NR Radio Resource Control (RRC) protocol
    BCCH-DL-SCH-Message
        message: c1 (0)
            c1: systemInformationBlockType1 (1)
                systemInformationBlockType1
                    cellSelectionInfo
                        q-RxLevMin: -118dBm (-59)
                    cellAccessRelatedInfo
                        plmn-IdentityInfoList: 2 items
                            Item 0
                                PLMN-IdentityInfo
                                    plmn-IdentityList: 1 item
                                        Item 0
                                            PLMN-Identity
                                                mcc: 3 items
                                                    Item 0
                                                        MCC-MNC-Digit: 3
                                                    Item 1
                                                        MCC-MNC-Digit: 1
                                                    Item 2
                                                        MCC-MNC-Digit: 0
                                                mnc: 3 items
                                                    Item 0
                                                        MCC-MNC-Digit: 2
                                                    Item 1
                                                        MCC-MNC-Digit: 6
                                                    Item 2
                                                        MCC-MNC-Digit: 0
```

- What is the RAN cell identifier?
	- `5465887033`
 	- The decimal value of the `cellIdentity` field.  

- What is the tracking area code?
	- `5436160`
 	- The decimal value of the `trackingAreaCode` field.  

```
2	0.000799	127.0.0.1	127.0.0.2	NR RRC	167	SIB1

NR Radio Resource Control (RRC) protocol
    BCCH-DL-SCH-Message
        message: c1 (0)
            c1: systemInformationBlockType1 (1)
                systemInformationBlockType1
                    cellAccessRelatedInfo
                        plmn-IdentityInfoList: 2 items
                            Item 0
                                PLMN-IdentityInfo
									trackingAreaCode: 52f300 [bit length 24, 0101 0010  1111 0011  0000 0000 decimal value 5436160]
                                    cellIdentity: 145cad1390 [bit length 36, 4 LSB pad bits, 0001 0100  0101 1100  1010 1101  0001 0011  1001 .... decimal value 5465887033]
```

- What is the MCC value of the second provider network?
	- `311`

```
2	0.000799	127.0.0.1	127.0.0.2	NR RRC	167	SIB1

NR Radio Resource Control (RRC) protocol
    BCCH-DL-SCH-Message
        message: c1 (0)
            c1: systemInformationBlockType1 (1)
                systemInformationBlockType1
                    cellSelectionInfo
                        q-RxLevMin: -118dBm (-59)
                    cellAccessRelatedInfo
                        plmn-IdentityInfoList: 2 items
                            Item 1
                                PLMN-IdentityInfo
                                    plmn-IdentityList: 1 item
                                        Item 0
                                            PLMN-Identity
                                                mcc: 3 items
                                                    Item 0
                                                        MCC-MNC-Digit: 3
                                                    Item 1
                                                        MCC-MNC-Digit: 1
                                                    Item 2
                                                        MCC-MNC-Digit: 1
                                                mnc: 3 items
                                                    Item 0
                                                        MCC-MNC-Digit: 4
                                                    Item 1
                                                        MCC-MNC-Digit: 9
                                                    Item 2
                                                        MCC-MNC-Digit: 0
```

- Is the second owner/operator name the same as the first?
	- Yes.
  	- Mobile Network Code (MNC): `490` = T-Mobile
 	- Mobile Country Code (MCC): `311` = United States of America

<img width="853" height="125" alt="image" src="https://github.com/user-attachments/assets/c8b5d69d-3593-434a-8b9a-46fb26bddb56" />

---

## Wireshark Preferences

- Go to `Analyze -> Protocols` and check:
  - MAC-LTE and mac_lte_udp
  - MAC-NR and mac_nr_udp
 
<img width="833" height="538" alt="Screenshot-20251109T035046" src="https://github.com/user-attachments/assets/680d1fd4-b83e-4bae-9176-a1180fe0fe25" />

<img width="993" height="620" alt="Screenshot-20251109T035908" src="https://github.com/user-attachments/assets/1583dfe8-dc2c-47cc-a5f3-0d39976ca011" />

- On Windows: `Edit/Preferences` or on macOS: `Wireshark -> Preferences`
  - Expand `Protocols`
  - Check all options for MAC-LTE and MAC-NR
  - For NAS-5GS, check “Try to detect and decode 5G-EA0 ciphered messages”
  - For RLC-LTE and RLC-NR, check all options except “May see RLC headers only”

---

## Joining the RAN

### **Packet 8 (rrcSetupRequest):**

Expand NR Radio Resource Control (RRC) protocol and all subtrees.

- What value does the UE enter as its ue-identity?
	- `222662597524`
 	- The decimal value of `67af733728`

```
8	0.203225	127.0.0.1	127.0.0.2	NR RRC	50	RRC Setup Request

ue-Identity: randomValue (1)
	randomValue: 67af733728 [bit length 39, 1 LSB pad bits, 0110 0111  1010 1111  0111 0011  0011 0111  0010 100. decimal value 222662597524]
```

- What is the declared reason for wanting to establish this session (establishmentCause)?
	- `mo-Signalling (3)`

```
8	0.203225	127.0.0.1	127.0.0.2	NR RRC	50	RRC Setup Request

establishmentCause: mo-Signalling (3)
```

```
8	0.203225	127.0.0.1	127.0.0.2	NR RRC	50	RRC Setup Request

NR Radio Resource Control (RRC) protocol
    UL-CCCH-Message
        message: c1 (0)
            c1: rrcSetupRequest (0)
                rrcSetupRequest
                    rrcSetupRequest
                        ue-Identity: randomValue (1)
                            randomValue: 67af733728 [bit length 39, 1 LSB pad bits, 0110 0111  1010 1111  0111 0011  0011 0111  0010 100. decimal value 222662597524]
                        establishmentCause: mo-Signalling (3)
                        spare: 00 [bit length 1, 7 LSB pad bits, 0... .... decimal value 0]
```

### **Packet 9 (rrcSetup):**

Which four physical channels does the UE learn about from the gNB?
- PDCCH
	- UE monitors PDCCH to receive DCI (scheduling, paging, etc.)
- PDSCH
	- Carries downlink data, SIBs, RRC messages (including this RRCSetup packet)
- PUCCH
	- UE sends UCI (HARQ-ACK, SR, CSI) on PUCCH
- PUSCH
	- UE sends uplink data, UCI (when scheduled), Msg3 (in RA), etc.

```
9	0.394926	127.0.0.1	127.0.0.2	NR RRC	514	RRC Setup

NR Radio Resource Control (RRC) protocol
    DL-CCCH-Message
        message: c1 (0)
            c1: rrcSetup (1)
                rrcSetup
                    criticalExtensions: rrcSetup (0)
                        rrcSetup
                            masterCellGroup: 5c00b0011f7afc701061e6007c22405d079b40008219300cb02259c7cdc2008000004200…
                                    spCellConfig
                                        spCellConfigDedicated
                                            downlinkBWP-ToAddModList: 1 item
                                                Item 0
                                                    BWP-Downlink
                                                        bwp-Common
                                                            pdcch-ConfigCommon: setup (1)
                                                            pdsch-ConfigCommon: setup (1)
                                                uplinkBWP-ToAddModList: 1 item
                                                    Item 0
                                                        BWP-Uplink
                                                            bwp-Common
                                                                pusch-ConfigCommon: setup (1)
                                                                pucch-ConfigCommon: setup (1)
```

### **Packet 10 (rrcSetupComplete):**

What is the Mobile Country Code and Mobile Network Code of the UE’s home network?
- Mobile Country Code (MCC): United States (`310`)
- Mobile Network Code (MNC): T-Mobile USA (`260`)

What is the UE’s MSIN?
- It is not listed in this packet.  

```
10	0.395580	127.0.0.1	127.0.0.2	NR RRC/NAS-5GS	127	RRC Setup Complete, Registration request

NR Radio Resource Control (RRC) protocol
    UL-DCCH-Message
        message: c1 (0)
            c1: rrcSetupComplete (2)
                rrcSetupComplete
                    rrc-TransactionIdentifier: 0
                    criticalExtensions: rrcSetupComplete (0)
                        rrcSetupComplete
                            selectedPLMN-Identity: 1
                            registeredAMF
                                amf-Identifier: 464008 [bit length 24, 0100 0110  0100 0000  0000 1000 decimal value 4603912]
                            guami-Type: native (0)
                            dedicatedNAS-Message: 7e0152c44591067e004149000bf2130062464008c0a4025a2e02f07071002d2e3fad6b9e…
                                Non-Access-Stratum 5GS (NAS)PDU
                                    Security protected NAS 5GS message
                                        Extended protocol discriminator: 5G mobility management messages (126)
                                        0000 .... = Spare Half Octet: 0
                                        .... 0001 = Security header type: Integrity protected (1)
                                        Message authentication code: 0x52c44591
                                        Sequence number: 6
                                    Plain NAS 5GS Message
                                        Extended protocol discriminator: 5G mobility management messages (126)
                                        0000 .... = Spare Half Octet: 0
                                        .... 0000 = Security header type: Plain NAS message, not security protected (0)
                                        Message type: Registration request (0x41)
                                        5GS registration type
                                            .... 1... = Follow-On Request bit (FOR): Follow-on request pending
                                            .... .001 = 5GS registration type: initial registration (1)
                                        NAS key set identifier
                                            0... .... = Type of security context flag (TSC): Native security context (for KSIAMF)
                                            .100 .... = NAS key set identifier: 4
                                        5GS mobile identity
                                            Length: 11
                                            1... .... = Spare: 1
                                            .1.. .... = Spare: 1
                                            ..1. .... = Spare: 1
                                            ...1 .... = Spare: 1
                                            .... 0... = Spare: 0
                                            .... .010 = Type of identity: 5G-GUTI (2)
                                            Mobile Country Code (MCC): United States (310)
                                            Mobile Network Code (MNC): T-Mobile USA (260)
```

### **Packet 14 (InitialUEMessage):**

This message is sent from the gNB to the AMF, initiating the process of joining the UE to the 5G Core Functions.

---

## UE, 5G Core Interaction: Identification, Authentication, Security

- **Packet 40 (ueCapabilityEnquiry):**

- What is the 5G frequency band number that will be used for this UE session?
	- `71`

```
40	0.860981	127.0.0.1	127.0.0.2	NR RRC	160	System Information [ SIB2 SIB4 SIB5 ]

NR Radio Resource Control (RRC) protocol
    BCCH-DL-SCH-Message
        message: c1 (0)
            c1: systemInformation (0)
                systemInformation
                    criticalExtensions: systemInformation (0)
                        systemInformation
                            sib-TypeAndInfo: 3 items
                                Item 1
                                    sib-TypeAndInfo item: sib4 (2)
                                        sib4
                                            interFreqCarrierFreqList: 4 items
                                                Item 0
                                                    InterFreqCarrierFreqInfo
                                                        dl-CarrierFreq: 125530
                                                        frequencyBandList: 1 item
                                                            Item 0
                                                                NR-MultiBandInfo
                                                                    freqBandIndicatorNR: 71
```

- **Packet 42 (UE-NR-Capability / RRC Reconfiguration):**

- Does this UE support the longDRX and ShortDRX sleep cycles?
	- Yes.
 	- The `drx-LongCycleStartOffset` parameter being there means the UE is configured for a long DRX cycle.
	- The `drx-onDurationTimer` and `drx-InactivityTimer` parameters are used to set up short DRX cycles.

- Does the UE support inter-frequency handover?
	- Yes.
 	- There are measurement configurations and event triggers related to it.
	- Measurement configuration
 		- `measObjectId: 1`: This object is of type `measObjectNR`, which means it is configured for NR (New Radio) measurements.
		- Report configurations
			- `eventA2 (1)` is set up to trigger a report when the signal quality of the serving cell falls below a certain threshold (`a2-Threshold: rsrp (0)` with `rsrp: -110dBm <= SS-RSRP < -109dBm (47)`).
			- `eventA3 (1)` is set up to trigger a report when a neighboring cell's signal quality is better than the serving cell by a certain offset (`a3-Offset: rsrp (0)` with `rsrp: 3.0dB (6)`).
		- Measurement ID configuration
			- The `measIdToAddModList` added two entries, each linked to measurement and report configurations.
		- Frequency band indicator
			- `freqBandIndicatorNR: 41` means that the UE is configured to measure and report to the n41 band.

```
42	1.444870	127.0.0.1	127.0.0.2	NR RRC/NAS-5GS	428	RRC Reconfiguration

NR Radio Resource Control (RRC) protocol
    DL-DCCH-Message
        message: c1 (0)
            c1: rrcReconfiguration (0)
                rrcReconfiguration
                    rrc-TransactionIdentifier: 0
                    criticalExtensions: rrcReconfiguration (0)
                        rrcReconfiguration
                            radioBearerConfig
                                srb-ToAddModList: 1 item
                                    Item 0	
                                        SRB-ToAddMod
                                            srb-Identity: 2
                                drb-ToAddModList: 1 item
                                    Item 0
                                        DRB-ToAddMod
                                            cnAssociation: sdap-Config (1)
                                                sdap-Config
                                                    pdu-Session: 1
                                                    sdap-HeaderDL: absent (1)
                                                    sdap-HeaderUL: present (0)
                                                    ..1. .... defaultDRB: True
                                                    mappedQoS-FlowsToAdd: 1 item
                                                        Item 0
                                                            QFI: 1
                                            drb-Identity: 1
                                            pdcp-Config
                                                drb
                                                    discardTimer: infinity (15)
                                                    pdcp-SN-SizeUL: len12bits (0)
                                                    pdcp-SN-SizeDL: len12bits (0)
                                                    headerCompression: notUsed (0)
                                                        notUsed: NULL
                                                t-Reordering: ms200 (19)
                                securityConfig
                                    securityAlgorithmConfig
                                        cipheringAlgorithm: nea2 (2)
                                        integrityProtAlgorithm: nia2 (2)
                                    keyToUse: master (0)
                            measConfig
                                measObjectToAddModList: 1 item
                                    Item 0
                                        MeasObjectToAddMod
                                            measObjectId: 1
                                            measObject: measObjectNR (0)
                                                measObjectNR
                                                    ssbFrequency: 520110
                                                    ssbSubcarrierSpacing: kHz30 (1)
                                                    smtc1
                                                        periodicityAndOffset: sf20 (2)
                                                            sf20: 0
                                                        duration: sf1 (0)
                                                    referenceSignalConfig
                                                        ssb-ConfigMobility
                                                            .... 1... deriveSSB-IndexFromCell: True
                                                    quantityConfigIndex: 1
                                                    offsetMO
														
                                reportConfigToAddModList: 2 items
                                    Item 0
                                        ReportConfigToAddMod
                                            reportConfigId: 1
                                            reportConfig: reportConfigNR (0)
                                                reportConfigNR
                                                    reportType: eventTriggered (1)
                                                        eventTriggered
                                                            eventId: eventA2 (1)
                                                                eventA2
                                                                    a2-Threshold: rsrp (0)
                                                                        rsrp: -110dBm <= SS-RSRP < -109dBm (47)
                                                                    .... 0... reportOnLeave: False
                                                                    hysteresis: 2dB (4)
                                                                    timeToTrigger: ms320 (8)
                                                            rsType: ssb (0)
                                                            reportInterval: ms5120 (6)
                                                            reportAmount: r1 (0)
                                                            reportQuantityCell
                                                                .... ..1. rsrp: True
                                                                .... ...1 rsrq: True
                                                                1... .... sinr: True
                                                            maxReportCells: 1
                                                            .... 0... includeBeamMeasurements: False
                                    Item 1
                                        ReportConfigToAddMod
                                            reportConfigId: 2
                                            reportConfig: reportConfigNR (0)
                                                reportConfigNR
                                                    reportType: eventTriggered (1)
                                                        eventTriggered
                                                            eventId: eventA3 (2)
                                                                eventA3
                                                                    a3-Offset: rsrp (0)
                                                                        rsrp: 3.0dB (6)
                                                                    .... ..0. reportOnLeave: False
                                                                    hysteresis: 1dB (2)
                                                                    timeToTrigger: ms640 (11)
                                                                    0... .... useAllowedCellList: False
                                                            rsType: ssb (0)
                                                            reportInterval: ms480 (2)
                                                            reportAmount: infinity (7)
                                                            reportQuantityCell
                                                                .1.. .... rsrp: True
                                                                ..1. .... rsrq: True
                                                                ...1 .... sinr: True
                                                            maxReportCells: 1
                                                            .... ...0 includeBeamMeasurements: False
                                                            reportAddNeighMeas: setup (0)
                                freqBandIndicatorNR: 2 items
                                    Item 0
                                        MeasIdToAddMod
                                            measId: 1
                                            measObjectId: 1
                                            reportConfigId: 1
                                    Item 1
                                        MeasIdToAddMod
                                            measId: 2
                                            measObjectId: 1
                                            reportConfigId: 2
                                s-MeasureConfig: ssb-RSRP (0)
                                    ssb-RSRP: infinity (127)
                                quantityConfig
                                    quantityConfigNR-List: 2 items
                                        Item 0
                                            QuantityConfigNR
                                                quantityConfigCell
                                                    ssb-FilterConfig
                                                        filterCoefficientRSRQ: fc11 (10)
                                                    csi-RS-FilterConfig
                                                        filterCoefficientRSRQ: fc11 (10)
                                        Item 1
                                            QuantityConfigNR
                                                quantityConfigCell
                                                    ssb-FilterConfig
                                                        filterCoefficientRSRQ: fc11 (10)
                                                    csi-RS-FilterConfig
                                                        filterCoefficientRSRQ: fc11 (10)
                            nonCriticalExtension
                                masterCellGroup: 5402b0911f7afc701065e600b14023c55f9a120cc8c814a77d5514a98f022e40844c2124…
                                    CellGroupConfig
                                        cellGroupId: 0
                                        rlc-BearerToAddModList: 2 items
                                            Item 0
                                                RLC-BearerConfig
                                                    logicalChannelIdentity: 2
                                                    servedRadioBearer: srb-Identity (0)
                                                        srb-Identity: 2
                                                    rlc-Config: am (0)
                                                        am
                                                            ul-AM-RLC
                                                                sn-FieldLength: size12 (0)
                                                                t-PollRetransmit: ms80 (15)
                                                                pollPDU: infinity (23)
                                                                pollByte: infinity (43)
                                                                maxRetxThreshold: t32 (7)
                                                            dl-AM-RLC
                                                                sn-FieldLength: size12 (0)
                                                                t-Reassembly: ms35 (7)
                                                                t-StatusProhibit: ms0 (0)
                                                    mac-LogicalChannelConfig
                                                        ul-SpecificParameters
                                                            priority: 3
                                                            prioritisedBitRate: infinity (15)
                                                            bucketSizeDuration: ms50 (3)
                                                            logicalChannelGroup: 0
                                                            schedulingRequestID: 0
                                                            .... .0.. logicalChannelSR-Mask: False
                                                            .... ..0. logicalChannelSR-DelayTimerApplied: False
                                            Item 1
                                                RLC-BearerConfig
                                                    logicalChannelIdentity: 3
                                                    servedRadioBearer: drb-Identity (1)
                                                        drb-Identity: 1
                                                    rlc-Config: am (0)
                                                        am
                                                            ul-AM-RLC
                                                                sn-FieldLength: size12 (0)
                                                                t-PollRetransmit: ms80 (15)
                                                                pollPDU: p16 (2)
                                                                pollByte: infinity (43)
                                                                maxRetxThreshold: t32 (7)
                                                            dl-AM-RLC
                                                                sn-FieldLength: size12 (0)
                                                                t-Reassembly: ms65 (13)
                                                                t-StatusProhibit: ms10 (2)
                                                    mac-LogicalChannelConfig
                                                        ul-SpecificParameters
                                                            priority: 4
                                                            prioritisedBitRate: kBps16 (2)
                                                            bucketSizeDuration: ms50 (3)
                                                            logicalChannelGroup: 1
                                                            schedulingRequestID: 0
                                                            0... .... logicalChannelSR-Mask: False
                                                            .0.. .... logicalChannelSR-DelayTimerApplied: False
                                        mac-CellGroupConfig
                                            drx-Config: setup (1)
                                                setup
                                                    drx-onDurationTimer: milliSeconds (1)
                                                        milliSeconds: ms10 (7)
                                                    drx-InactivityTimer: ms100 (15)
                                                    drx-HARQ-RTT-TimerDL: 42
                                                    drx-HARQ-RTT-TimerUL: 42
                                                    drx-RetransmissionTimerDL: sl8 (5)
                                                    drx-RetransmissionTimerUL: sl8 (5)
                                                    drx-LongCycleStartOffset: ms160 (9)
                                                        ms160: 143
                                                    drx-SlotOffset: 0 ms (0)
                                            bsr-Config
                                                periodicBSR-Timer: sf5 (1)
                                                retxBSR-Timer: sf1280 (7)
                                                logicalChannelSR-DelayTimer: sf40 (1)
                                            ..0. .... skipUplinkTxDynamic: False
                                        spCellConfig
                                            spCellConfigDedicated
                                                downlinkBWP-ToAddModList: 1 item
                                                    Item 0
                                                        BWP-Downlink
                                                            bwp-Id: 1
                                                            bwp-Dedicated
                                                                pdsch-Config: setup (1)
                                                                    setup
                                                                        resourceAllocation: resourceAllocationType0 (0)
                                                                        rbg-Size: config1 (0)
                                                                        mcs-Table: qam256 (0)
                                                                        maxNrofCodeWordsScheduledByDCI: n1 (0)
                                                                        prb-BundlingType: staticBundling (0)
                                                                            staticBundling
                                                                                bundleSize: n4 (0)
                                                                        zp-CSI-RS-ResourceToAddModList: 1 item
                                                                            Item 0
                                                                                ZP-CSI-RS-Resource
                                                                                    zp-CSI-RS-ResourceId: 13
                                                                                    resourceMapping
                                                                                        frequencyDomainAllocation: other (3)
                                                                                            other: 3c [bit length 6, 2 LSB pad bits, 0011 11.. decimal value 15]
                                                                                        nrofPorts: p8 (3)
                                                                                        firstOFDMSymbolInTimeDomain: 5
                                                                                        cdm-Type: fd-CDM2 (1)
                                                                                        density: one (1)
                                                                                            one: NULL
                                                                                        freqBand
                                                                                            startingRB: 0
                                                                                            nrofRBs: 276
                                                                                    periodicityAndOffset: slots40 (7)
                                                                                        slots40: 18
                                                uplinkConfig
                                                    uplinkBWP-ToAddModList: 1 item
                                                        Item 0
                                                            BWP-Uplink
                                                                bwp-Id: 1
                                                                bwp-Dedicated
                                                                    pusch-Config: setup (1)
                                                                        setup
                                                                            txConfig: codebook (0)
                                                                            pusch-PowerControl
                                                                                p0-AlphaSets: 1 item
                                                                                    Item 0
                                                                                        P0-PUSCH-AlphaSet
                                                                                            p0-PUSCH-AlphaSetId: 0
                                                                                            p0: 0 dB
                                                                                            alpha: alpha1 (7)
                                                                            resourceAllocation: resourceAllocationType1 (1)
                                                                            mcs-Table: qam256 (0)
                                                                            mcs-TableTransformPrecoder: qam256 (0)
                                                                            transformPrecoder: enabled (0)
                                                                            codebookSubset: nonCoherent (2)
                                                                            maxRank: 1
                                                                            uci-OnPUSCH: setup (1)
                                                                                setup
                                                                                    betaOffsets: semiStatic (1)
                                                                                        semiStatic
                                                                                            betaOffsetACK-Index1: 8
                                                                                            betaOffsetACK-Index2: 10
                                                                                            betaOffsetACK-Index3: 7
                                                                                            betaOffsetCSI-Part1-Index1: 7
                                                                                            betaOffsetCSI-Part1-Index2: 6
                                                                                            betaOffsetCSI-Part2-Index1: 5
                                                                                            betaOffsetCSI-Part2-Index2: 4
                                                                                    scaling: f1 (3)
                                                pdsch-ServingCellConfig: setup (1)
                                                    setup
                                                        nrofHARQ-ProcessesForPDSCH: n16 (5)
                                                        maxMIMO-Layers: 4
                                                csi-MeasConfig: setup (1)
                                                    setup
                                                        nzp-CSI-RS-ResourceToAddModList: 1 item
                                                            Item 0
                                                                NZP-CSI-RS-Resource
                                                                    nzp-CSI-RS-ResourceId: 5
                                                                    resourceMapping
                                                                        frequencyDomainAllocation: other (3)
                                                                            other: 3c [bit length 6, 2 LSB pad bits, 0011 11.. decimal value 15]
                                                                        nrofPorts: p32 (7)
                                                                        firstOFDMSymbolInTimeDomain: 5
                                                                        firstOFDMSymbolInTimeDomain2: 8
                                                                        cdm-Type: fd-CDM2 (1)
                                                                        density: one (1)
                                                                            one: NULL
                                                                        freqBand
                                                                            startingRB: 0
                                                                            nrofRBs: 276
                                                                    powerControlOffset: 6 dB
                                                                    scramblingID: 540
                                                                    periodicityAndOffset: slots40 (7)
                                                                        slots40: 8
                                                                    qcl-InfoPeriodicCSI-RS: 1
                                                        nzp-CSI-RS-ResourceToReleaseList: 1 item
                                                            Item 0
                                                                NZP-CSI-RS-ResourceId: 4
                                                        nzp-CSI-RS-ResourceSetToAddModList: 1 item
                                                            Item 0
                                                                NZP-CSI-RS-ResourceSet
                                                                    nzp-CSI-ResourceSetId: 2
                                                                    nzp-CSI-RS-Resources: 1 item
                                                                        Item 0
                                                                            NZP-CSI-RS-ResourceId: 5
                                                        nzp-CSI-RS-ResourceSetToReleaseList: 1 item
                                                            Item 0
                                                                NZP-CSI-RS-ResourceSetId: 1
                                                        csi-ResourceConfigToAddModList: 1 item
                                                            Item 0
                                                                CSI-ResourceConfig
                                                                    csi-ResourceConfigId: 2
                                                                    csi-RS-ResourceSetList: nzp-CSI-RS-SSB (0)
                                                                        nzp-CSI-RS-SSB
                                                                            nzp-CSI-RS-ResourceSetList: 1 item
                                                                                Item 0
                                                                                    NZP-CSI-RS-ResourceSetId: 2
                                                                    bwp-Id: 1
                                                                    resourceType: periodic (2)
                                                        csi-ResourceConfigToReleaseList: 1 item
                                                            Item 0
                                                                CSI-ResourceConfigId: 1
                                                        csi-ReportConfigToAddModList: 1 item
                                                            Item 0
                                                                CSI-ReportConfig
                                                                    reportConfigId: 0
                                                                    resourcesForChannelMeasurement: 2
                                                                    csi-IM-ResourcesForInterference: 3
                                                                    reportConfigType: aperiodic (3)
                                                                        aperiodic
                                                                            reportSlotOffsetList: 8 items
                                                                                Item 0
                                                                                    reportSlotOffsetList item: 6
                                                                                Item 1
                                                                                    reportSlotOffsetList item: 6
                                                                                Item 2
                                                                                    reportSlotOffsetList item: 6
                                                                                Item 3
                                                                                    reportSlotOffsetList item: 6
                                                                                Item 4
                                                                                    reportSlotOffsetList item: 6
                                                                                Item 5
                                                                                    reportSlotOffsetList item: 6
                                                                                Item 6
                                                                                    reportSlotOffsetList item: 6
                                                                                Item 7
                                                                                    reportSlotOffsetList item: 6
                                                                    reportQuantity: cri-RI-PMI-CQI (1)
                                                                        cri-RI-PMI-CQI: NULL
                                                                    reportFreqConfiguration
                                                                        cqi-FormatIndicator: widebandCQI (0)
                                                                        pmi-FormatIndicator: widebandPMI (0)
                                                                        csi-ReportingBand: subbands9 (6)
                                                                            subbands9: ff80 [bit length 9, 7 LSB pad bits, 1111 1111  1... .... decimal value 511]
                                                                    timeRestrictionForChannelMeasurements: notConfigured (1)
                                                                    timeRestrictionForInterferenceMeasurements: notConfigured (1)
                                                                    codebookConfig
                                                                        codebookType: type1 (0)
                                                                            type1
                                                                                subType: typeI-SinglePanel (0)
                                                                                    typeI-SinglePanel
                                                                                        nrOfAntennaPorts: moreThanTwo (1)
                                                                                            moreThanTwo
                                                                                                n1-n2: eight-two-TypeI-SinglePanel-Restriction (11)
                                                                                                    eight-two-TypeI-SinglePanel-Restriction: ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff [bit length 256]
                                                                                        typeI-SinglePanel-ri-Restriction: 0f [bit length 8, 0000 1111 decimal value 15]
                                                                                codebookMode: 1
                                                                    dummy: n1 (0)
                                                                    groupBasedBeamReporting: disabled (1)
                                                                        disabled
                                                                            nrofReportedRS: n1 (0)
                                                                    cqi-Table: table2 (1)
                                                                    subbandSize: value2 (1)
                                                        aperiodicTriggerStateList: setup (1)
                                                            setup: 1 item
                                                                Item 0
                                                                    CSI-AperiodicTriggerState
                                                                        associatedReportConfigInfoList: 1 item
                                                                            Item 0
                                                                                CSI-AssociatedReportConfigInfo
                                                                                    reportConfigId: 0
                                                                                    resourcesForChannel: nzp-CSI-RS (0)
                                                                                        nzp-CSI-RS
                                                                                            resourceSet: 1
                                                                                    csi-IM-ResourcesForInterference: 1
                                                tag-Id: 0
                                                servingCellMO: 1
                                dedicatedNAS-MessageList: 1 item
                                    Item 0
                                        DedicatedNAS-Message: 7e02011004280300ef1e9e3b652eecfc8986d9db9746880a858ca611e15a0057cebf687b…
                                            Non-Access-Stratum 5GS (NAS)PDU
                                                Security protected NAS 5GS message
                                                    Extended protocol discriminator: 5G mobility management messages (126)
                                                    0000 .... = Spare Half Octet: 0
                                                    .... 0010 = Security header type: Integrity protected and ciphered (2)
                                                    Message authentication code: 0x01100428
                                                    Sequence number: 3
                                                Encrypted data
```

- **Packet 67 (successfulOutcome):**

What is the ID number (AMF-UE-NGAP-ID) assigned to this UE by the AMF?
- It is not listed.  

```
67	2.609720	fd00:976a:2:249::6	2607:fc20:3ce0:970c:78f1:1eff:fe49:5cab	ESP	116	ESP (SPI=0x5135a33e)

Encapsulating Security Payload
    ESP SPI: 0x5135a33e (1362469694)
    ESP Sequence: 3
```

---

## Setting up the Data Plane for the UE

- **Packet 161 (UplinkNASTransport):**

Contains the message from the UE to the AMF, triggering the creation of a PDU session.

- **Packet 165 (AMF’s response):**

What are the declared UE’s maximum downlink and uplink bit rates for this PDU session?
- Is it not listed in the packet capture.  

What is the IP address associated with the tunnel for this session from the gNB to the UPF?
- Is it not listed in the packet capture.  

- **Packet 178 (rrcReconfiguration):**
 
 What is the value of periodicBSR-Timer?
- `sf5 (1)`

```
178	37.062107	127.0.0.1	127.0.0.2	NR RRC	268	RRC Reconfiguration

NR Radio Resource Control (RRC) protocol
    DL-DCCH-Message
        message: c1 (0)
            c1: rrcReconfiguration (0)
                rrcReconfiguration
                    criticalExtensions: rrcReconfiguration (0)
                        rrcReconfiguration
                            nonCriticalExtension
                                masterCellGroup: 5404b0911f7afc701065e600b14023c55f9a120cc8c816384678abf94241d89c0294efaa…
                                    CellGroupConfig
                                        mac-CellGroupConfig
                                            bsr-Config
                                                periodicBSR-Timer: sf5 (1)
```

- **Packet 170 (NGAP message):**

What is the IP address of the other end of the gNB-to-UPF tunnel for the UE?
- It is not listed because this pcapng does not have any NGAP packets.
- A MIB message was returned instead.  

```
170	35.158365	127.0.0.1	127.0.0.2	NR RRC	48	MIB

NR Radio Resource Control (RRC) protocol
    BCCH-BCH-Message
        message: mib (0)
            mib
                systemFrameNumber: 00 [bit length 6, 2 LSB pad bits, 0000 00.. decimal value 0]
                subCarrierSpacingCommon: scs15or60 (0)
                ssb-SubcarrierOffset: 0
                dmrs-TypeA-Position: pos2 (0)
                pdcch-ConfigSIB1
                    controlResourceSetZero: 0
                    searchSpaceZero: 0
                cellBarred: barred (0)
                intraFreqReselection: allowed (0)
                spare: 00 [bit length 1, 7 LSB pad bits, 0... .... decimal value 0]
```

## What I Learned

