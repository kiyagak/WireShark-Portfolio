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

Expand all of the subtrees of packet 42's NR Radio Resource Control (RRC) field:
- Choose packet 42.
- Click the `NR Radio Resource Control (RRC)` in the packet details pane.
- Right-click it → Expand Subtrees. 

<img width="756" height="699" alt="image" src="https://github.com/user-attachments/assets/b263f473-d5f3-4515-b189-804d55f1cd78" />

1. **Frequency Band**  
   → What is the **frequency band** the UE will use to communicate with the gNodeB?

2. **Frequency Range**  
   → What **range of frequencies (in MHz)** does this band occupy?  
   *(Hint: See `channel.pptx` from class – not in trace)*

3. **SSB Carrier Frequency**  

What is the **central frequency (in MHz)** of the carrier carrying the **Synchronization Signal Block (SSB)**?  
- `520.11` MHz
- The value `520110` is in kHz and converts to `520.11` MHz
- Field: `ssbFrequency`  

```
NR Radio Resource Control (RRC) protocol
    DL-DCCH-Message
        message: c1 (0)
            c1: rrcReconfiguration (0)
                rrcReconfiguration
                    rrc-TransactionIdentifier: 0
                    criticalExtensions: rrcReconfiguration (0)
                        rrcReconfiguration
                            measConfig
                                measObjectToAddModList: 1 item
                                    Item 0
                                        MeasObjectToAddMod
                                            measObjectId: 1
                                            measObject: measObjectNR (0)
                                                measObjectNR
                                                    ssbFrequency: 520110
```   

5. **Modulation and Coding Scheme (MCS)**  

What **MCS** is used for the **physical channel carrying user data**?
- `qam256 (0)`

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
                            nonCriticalExtension
                                masterCellGroup: 5402b0911f7afc701065e600b14023c55f9a120cc8c814a77d5514a98f022e40844c2124…
                                    CellGroupConfig
                                        spCellConfig
                                            spCellConfigDedicated
                                                downlinkBWP-ToAddModList: 1 item
                                                    Item 0
                                                        BWP-Downlink
                                                            bwp-Id: 1
                                                            bwp-Dedicated
                                                                pdsch-Config: setup (1)
                                                                    setup
                                                                        mcs-Table: qam256 (0)
```

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
                                                    freqBandIndicatorNR: 41
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
                                measIdToAddModList: 2 items
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

---

### **Packet 114 – Measurement Report (Uplink)**

Select packet 114.  

> UE reports channel quality based on the SSB reference signal

5. **RSRP (Reference Signal Received Power)**  

What is the **average RSRP** in **dBm**?
- `115`

The given RSRP range is:

$-115 \, \text{dBm} \leq \text{SS-RSRP} < -114 \, \text{dBm}$

This is a half-open interval from $-115$ to $-114$ dBm (includes $-115$, excludes $-114$).

The interval -115 ≤ SS-RSRP < -114 is half-open because it includes -115 dBm but excludes -114 dBm, written as [-115, -114). This prevents overlap with the next bin and ensures only -115 dBm (42 samples) belongs here.

Since RSRP is typically reported in integer dBm values, the only possible integer value in this range is:

$\boxed{-115}$

Thus, the average RSRP in this bin is:

$\boxed{-115 \, \text{dBm}}$

```
rsrp: -115dBm <= SS-RSRP < -114dBm (42)
```

6. **RSRQ (Reference Signal Received Quality)**  

What is the **average RSRQ** in **dB**?
- `-13.25dB`

Take the interval's midpoint, assuming a uniform distribution within that range.

Given interval:
- Lower bound: -13.5 dB
- Upper bound: -13.0 dB

Average RSRQ (approximate) = (Lower bound + Upper bound) / 2  
= (-13.5 + -13.0) / 2  
= (-26.5) / 2  
= -13.25 dB

7. **SINR (Signal-to-Interference-plus-Noise Ratio)**  

What is the **average SINR** of the reference signal in **dB**?
- `0.75` dB

The SINR values in the given bin range from -1.0 dB to -0.5 dB (excluding -0.5 dB), with 45 samples in this interval.

Calculate the bin's midpoint:

$\text{Average SINR (dB)} = \frac{-1.0 + (-0.5)}{2} = \frac{-1.5}{2} = \boxed{-0.75}$

```
114	11.819632	127.0.0.1	127.0.0.2	NR RRC	53	Measurement Report

NR Radio Resource Control (RRC) protocol
    UL-DCCH-Message
        message: c1 (0)
            c1: measurementReport (0)
                measurementReport
                    criticalExtensions: measurementReport (0)
                        measurementReport
                            measResults
                                measId: 1
                                measResultServingMOList: 1 item
                                    Item 0
                                        MeasResultServMO
                                            servCellId: 0
                                            measResultServingCell
                                                physCellId: 540
                                                measResult
                                                    cellResults
                                                        resultsSSB-Cell
                                                            rsrp: -115dBm <= SS-RSRP < -114dBm (42)
                                                            rsrq: -13.5dB <= SS-RSRQ < -13.0dB (60)
                                                            sinr: -1.0dB <= SS-SINR < -0.5dB (45)
```

---

### **Packet 26 – UE Capability Information (Uplink)**

> Expand **RF Parameters** → Focus on **MIMO and Power Class** (see **Figure 6**)

8. **UE Power Class (Band 41)**  
   → Based on **MIMO parameters for band 41**, what **power class** does the UE support?

9. **Maximum Transmit Power**  
   → According to the **5G NR standard**, what is the **maximum transmit power (in dBm)** for this power class?  
   *(Search online: “UE power classes”)*


```

```

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
