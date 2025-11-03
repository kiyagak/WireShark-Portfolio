# Wireshark - Capture TCP File Transfers & TCP Segments

## Objective

This [TCP Wireshark Lab](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_TCP_v9.pdf) investigates TCP behavior by analyzing a packet trace of a ~150KB file transfer (Alice’s Adventures in Wonderland) from your computer to gaia.cs.umass.edu using HTTP POST. You will examine TCP’s reliable data transfer (sequence/acknowledgment numbers), congestion control (slow start, congestion avoidance), flow control, connection setup, and performance (throughput, RTT). Review text sections 3.5 and 3.7 before starting.

**Steps:**

1. **Capture a bulk TCP transfer:**
   - Download `alice.txt` from http://gaia.cs.umass.edu/wireshark-labs/alice.txt.
   	- Right-click on the page
   	- Click `Save as...`
   	- Click `Save`
  
<img width="913" height="634" alt="Screenshot_20251103_001628" src="https://github.com/user-attachments/assets/d40dc25c-eab6-4bf1-baa7-7b474adbaeca" />

   - Go to http://gaia.cs.umass.edu/wireshark-labs/TCP-wireshark-file1.html.
   - Use "Browse" to select the local `alice.txt` file.

<img width="945" height="501" alt="Screenshot_20251103_002111" src="https://github.com/user-attachments/assets/07efedcf-960e-4e97-ab4c-61d787b722dc" />

   - Start Wireshark and begin packet capture.
   - Click `Upload alice.txt file` to send the file via HTTP POST.
   - Stop the Wireshark capture after upload completes.

   - (Alternative: Download pre-captured trace `tcp-wireshark-trace1-1` from wireshark-traces-9e.zip if live capture isn’t possible.)

<img width="1018" height="650" alt="Screenshot_20251103_002417" src="https://github.com/user-attachments/assets/2ba85f39-5f85-4ebf-8dae-7da52643f6e7" />

2. **Initial trace analysis:**
   - Filter for `tcp` to view TCP segments.
   - Locate the HTTP POST message (spread across ~106 TCP segments).
   - Identify the TCP three-way handshake (SYN, SYNACK, ACK) and first data segment.

<img width="1484" height="745" alt="Screenshot_20251103_002418" src="https://github.com/user-attachments/assets/35216d18-65bf-44d5-bd78-62ba9cf88206" />

<img width="1380" height="1068" alt="Screenshot_20251103_002419" src="https://github.com/user-attachments/assets/8648b499-1eae-4ceb-a001-dbbd56db4688" />

<img width="1247" height="740" alt="Screenshot_20251103_002421" src="https://github.com/user-attachments/assets/fa963d75-762c-4be3-a33b-958574ba00db" />

<img width="1251" height="643" alt="Screenshot_20251103_002422" src="https://github.com/user-attachments/assets/835a0411-6797-4812-a539-c72471f15238" />

3. **Use Wireshark tools:**
   - Apply `tcp` filter to focus on TCP segments.
   - Use Statistics → TCP Stream Graph → Round Trip Time Graph  → Switch Direction for RTT analysis.
   - Use Statistics → TCP Stream Graph → Time-Sequence-Graph (Stevens) for congestion control analysis.

<img width="1118" height="864" alt="image" src="https://github.com/user-attachments/assets/93de64bd-e38b-4cf3-b13a-e65ee72e2e87" />

<img width="878" height="830" alt="image" src="https://github.com/user-attachments/assets/deec53fc-998d-4b88-9ed4-69731ffa8353" />

<img width="1118" height="864" alt="image" src="https://github.com/user-attachments/assets/5c36bedf-2679-4c23-b09c-5cac992ccb59" />

<img width="878" height="830" alt="image" src="https://github.com/user-attachments/assets/e349177b-f059-4691-8642-098b276c1ae9" />


**Questions (left exactly as in the original):**

1. What is the IP address and TCP port number used by the client computer (source) that is transferring the alice.txt file to gaia.cs.umass.edu? 
- Source/client IP address: 10.98.0.7
- Source/client TCP port number: 36244

		Internet Protocol Version 4, Src: 10.98.0.7, Dst: 128.119.245.12
		Transmission Control Protocol, Src Port: 36244, Dst Port: 80, Seq: 148367, Ack: 1, Len: 959

2. What is the IP address of gaia.cs.umass.edu?
- 128.119.245.12

On what port number is it sending and receiving TCP segments for this connection?
- TCP port 80

3. What is the sequence number of the TCP SYN segment that is used to initiate the TCP connection between the client computer and gaia.cs.umass.edu? 
- `1797965661`
- This is the **“raw” sequence number** carried in the TCP segment itself

		Sequence Number (raw): 1797965661

- It is NOT the packet number in the “No.” column in the Wireshark window as there is no such thing as a “packet number” in TCP or UDP. 
- It is also not the relative sequence number with respect to the starting sequence number of this TCP session.

<img width="1251" height="916" alt="image" src="https://github.com/user-attachments/assets/b7c82b40-8e63-4b84-b3dc-775900b9f622" />

What is in this TCP segment that identifies the segment as a SYN segment? 
- SYN flag

```
4	0.007973035	10.98.0.7	128.119.245.12	TCP	60	36342 → 80 [SYN] Seq=0 Win=64240 Len=0 MSS=1460 SACK_PERM TSval=1912101104 TSecr=0 WS=128
...
Flags: 0x002 (SYN)
  ```

Will the TCP receiver in this session be able to use **Selective Acknowledgments** (**SACK**) (allowing TCP to function a bit more like a “selective repeat” receiver)?
- Yes

		Options: (20 bytes), Maximum segment size, SACK permitted, Timestamps, No-Operation (NOP), Window scale


4. What is the sequence number of the SYNACK segment sent by gaia.cs.umass.edu to the client computer in reply to the SYN? 
- `4001501217`

		Sequence Number (raw): 4001501217


What is it in the segment that identifies the segment as a SYNACK segment? 
- SYN/ACK flag

```
7	0.028505917	128.119.245.12	10.98.0.7	TCP	60	80 → 36342 [SYN, ACK] Seq=0 Ack=1 Win=65160 Len=0 MSS=1360 SACK_PERM TSval=2486358930 TSecr=1912101104 WS=256
...
Flags: 0x012 (SYN, ACK)
```

What is the value of the Acknowledgement field in the SYNACK segment? 
- `1797965662`

<img width="1259" height="643" alt="image" src="https://github.com/user-attachments/assets/7460ac90-6078-420d-a891-8e788699b6cd" />

How did gaia.cs.umass.edu determine that value?
- Viewing the **raw acknowledgement number** instead of the relative acknowledgement number.

		Acknowledgment number (raw): 1797965662

5. What is the sequence number of the TCP segment containing the header of the HTTP POST command? 

		Sequence Number (raw): 3843511335

<img width="1259" height="643" alt="image" src="https://github.com/user-attachments/assets/b4c9e889-052e-4a65-bcee-18d8c2bf499e" />

To find the POST message header, you must
- dig into the packet content field at the bottom of the Wireshark window
- looking for a segment with the ASCII text “POST” within its DATA field 4, 5.

<img width="1259" height="977" alt="image" src="https://github.com/user-attachments/assets/cc8a54a9-01d4-408d-bd6d-981aaef2f705" />

How many bytes of data are contained in the payload (data) field of this TCP segment? 
- 148723 bytes

		File Data: 148723 bytes

Did all of the data in the transferred file alice.txt fit into this single segment?
- No

		[ [truncated]113 Reassembled TCP Segments (149325 bytes): #16(602), #17(1348), #18(1348), #19(1348), #20(1348), #21(1348), #22(1348), #23(1348), #24(1348), #25(1348), #27(1348), #28(1348), #30(1348), #31(1348), #33(1348), #34(1348), #36(134]

6. Consider the TCP segment containing the HTTP “POST” as the first segment in the data transfer part of the TCP connection.

- At what time was the first segment (the one containing the HTTP POST) in the data-transfer part of the TCP connection sent?
	- Nov  3, 2025 00:24:23.209028537 UTC

```
16	11.521331138	10.98.0.7	128.119.245.12	TCP	654	36244 → 80 [PSH, ACK] Seq=1 Ack=1 Win=502 Len=602 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]
...
Arrival Time: Nov  3, 2025 00:24:23.209028537 UTC
```

<img width="1500" height="992" alt="image" src="https://github.com/user-attachments/assets/ac1f9963-71c9-4216-a548-58e999820b28" />

- At what time was the ACK for this first data-containing segment received?
	- Nov  3, 2025 00:24:23.209142563 UTC

```
17	11.521445164	10.98.0.7	128.119.245.12	TCP	1400	36244 → 80 [ACK] Seq=603 Ack=1 Win=502 Len=1348 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]
...
Arrival Time: Nov  3, 2025 00:24:23.209142563 UTC
```

<img width="1500" height="993" alt="image" src="https://github.com/user-attachments/assets/714df6eb-043c-4674-8d1a-a4a97bdfd732" />

- What is the RTT for this first data-containing segment?
	- RTT: `19.9` ms

<img width="878" height="830" alt="image" src="https://github.com/user-attachments/assets/78247637-a97a-464a-9049-f183a26a153d" />

- What is the RTT value the second data-carrying TCP segment and its ACK?
	- RTT:`19.85` ms
 	- Acknowledgment number (raw): `508176412`

<img width="878" height="830" alt="image" src="https://github.com/user-attachments/assets/f7f225ef-9293-464d-9e10-c1198ab0ddd4" />

- What is the Estimated RTT value (see Section 3.5.3, in the text) after the ACK for the second data-carrying segment is received? 
	- `19.9` ms

- Estimated RTT formula:
	- ( `19.9` + `19.85` ) / 2
 		- `19.9` ms is the RTT of the packet of 1st TCP segment
   		- `19.85` ms is the RTT of the packet of 2nd TCP segment
 	- 39.75 / 2 = `19.875`
	- Wireshark rounds RTT to one decimal place, making the estimated RTT `19.9` ms

7. What is the length (header plus payload) of each of the first four data-carrying TCP segments?

```
16	11.521331138	10.98.0.7	128.119.245.12	TCP	654	36244 → 80 [PSH, ACK] Seq=1 Ack=1 Win=502 Len=602 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]
Frame Length: 654 bytes (5232 bits)

17	11.521445164	10.98.0.7	128.119.245.12	TCP	1400	36244 → 80 [ACK] Seq=603 Ack=1 Win=502 Len=1348 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]
Frame Length: 1400 bytes (11200 bits)

18	11.521448062	10.98.0.7	128.119.245.12	TCP	1400	36244 → 80 [PSH, ACK] Seq=1951 Ack=1 Win=502 Len=1348 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]
Frame Length: 1400 bytes (11200 bits)

20	11.521452771	10.98.0.7	128.119.245.12	TCP	1400	36244 → 80 [PSH, ACK] Seq=4647 Ack=1 Win=502 Len=1348 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]
Frame Length: 1400 bytes (11200 bits)
```

<img width="1315" height="712" alt="image" src="https://github.com/user-attachments/assets/a59a47ea-3f70-443a-a762-70cd4761ec3a" />

8. What is the minimum amount of available buffer space advertised to the client by gaia.cs.umass.edu among these first four data-carrying TCP segments7?
- `502`

For each of the four packets:

- Click the packet in the list.
- In the packet details pane, expand:

```
Transmission Control Protocol
  ...
  Window size value: XXXXX
  [Calculated window size: XXXXX]
```

```
16	11.521331138	10.98.0.7	128.119.245.12	TCP	654	36244 → 80 [PSH, ACK] Seq=1 Ack=1 Win=502 Len=602 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]
[Calculated window size: 502]

17	11.521445164	10.98.0.7	128.119.245.12	TCP	1400	36244 → 80 [ACK] Seq=603 Ack=1 Win=502 Len=1348 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]
[Calculated window size: 502]

18	11.521448062	10.98.0.7	128.119.245.12	TCP	1400	36244 → 80 [PSH, ACK] Seq=1951 Ack=1 Win=502 Len=1348 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]
[Calculated window size: 502]

20	11.521452771	10.98.0.7	128.119.245.12	TCP	1400	36244 → 80 [PSH, ACK] Seq=4647 Ack=1 Win=502 Len=1348 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]
[Calculated window size: 502]
```

Does the lack of receiver buffer space ever throttle the sender for these first four data-carrying segments?
- No

```
16	11.521331138	10.98.0.7	128.119.245.12	TCP	654	36244 → 80 [PSH, ACK] Seq=1 Ack=1 Win=502 Len=602 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]

Transmission Control Protocol, Src Port: 36244, Dst Port: 80, Seq: 1, Ack: 1, Len: 602
    Sequence Number (raw): 3843362969
    [Next Sequence Number: 603    (relative sequence number)]
    Acknowledgment Number: 1    (relative ack number)
    Window: 502
    [Calculated window size: 502]
    TCP payload (602 bytes)
```

```
17	11.521445164	10.98.0.7	128.119.245.12	TCP	1400	36244 → 80 [ACK] Seq=603 Ack=1 Win=502 Len=1348 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]

Transmission Control Protocol, Src Port: 36244, Dst Port: 80, Seq: 603, Ack: 1, Len: 1348
    Sequence Number (raw): 3843363571
    [Next Sequence Number: 1951    (relative sequence number)]
    Acknowledgment Number: 1    (relative ack number)
    Window: 502
    [Calculated window size: 502]
    TCP payload (1348 bytes)
```

```
18	11.521448062	10.98.0.7	128.119.245.12	TCP	1400	36244 → 80 [PSH, ACK] Seq=1951 Ack=1 Win=502 Len=1348 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]

Transmission Control Protocol, Src Port: 36244, Dst Port: 80, Seq: 1951, Ack: 1, Len: 1348
    Sequence Number (raw): 3843364919
    [Next Sequence Number: 3299    (relative sequence number)]
    Acknowledgment Number: 1    (relative ack number)
    Window: 502
    [Calculated window size: 502]
    TCP payload (1348 bytes)
```

```
20	11.521452771	10.98.0.7	128.119.245.12	TCP	1400	36244 → 80 [PSH, ACK] Seq=4647 Ack=1 Win=502 Len=1348 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]

Transmission Control Protocol, Src Port: 36244, Dst Port: 80, Seq: 4647, Ack: 1, Len: 1348
    Sequence Number (raw): 3843367615
    [Next Sequence Number: 5995    (relative sequence number)]
    Acknowledgment Number: 1    (relative ack number)
    Window: 502
    [Calculated window size: 502]
    TCP payload (1348 bytes)
```

9. Are there any retransmitted segments in the trace file? 
- No

What did you check for (in the trace) in order to answer this question?
- Used this filter to show all retransmissions:

		tcp.analysis.retransmission

<img width="1304" height="643" alt="image" src="https://github.com/user-attachments/assets/ca63e0d3-17b6-45e1-ac5b-c2790ae58265" />

10. How much data does the receiver typically acknowledge in an ACK among the first ten data-carrying segments sent from the client to gaia.cs.umass.edu?

```
Formula:
Data acknowledged in this ACK = Current Ack − Previous Ack


17	11.521445164	10.98.0.7	128.119.245.12	TCP	1400	36244 → 80 [ACK] Seq=603 Ack=1 Win=502 Len=1348 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]

Acknowledgment Number: 1    (relative ack number)
Acknowledgment number (raw): 508176412

19	11.521452006	10.98.0.7	128.119.245.12	TCP	1400	36244 → 80 [ACK] Seq=3299 Ack=1 Win=502 Len=1348 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]

Acknowledgment Number: 1    (relative ack number)
Acknowledgment number (raw): 508176412

21	11.521455204	10.98.0.7	128.119.245.12	TCP	1400	36244 → 80 [ACK] Seq=5995 Ack=1 Win=502 Len=1348 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]

Acknowledgment Number: 1    (relative ack number)
Acknowledgment number (raw): 508176412

23	11.521458767	10.98.0.7	128.119.245.12	TCP	1400	36244 → 80 [ACK] Seq=8691 Ack=1 Win=502 Len=1348 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]

Acknowledgment Number: 1    (relative ack number)
Acknowledgment number (raw): 508176412

25	11.521461628	10.98.0.7	128.119.245.12	TCP	1400	36244 → 80 [ACK] Seq=11387 Ack=1 Win=502 Len=1348 TSval=1912112618 TSecr=2486330902 [TCP segment of a reassembled PDU]

Acknowledgment Number: 1    (relative ack number)
Acknowledgment number (raw): 508176412
```



Can you identify cases where the receiver is ACKing every other received segment (see Table 3.2 in the text) among these first ten data-carrying segments?
- Yes

<img width="1304" height="643" alt="image" src="https://github.com/user-attachments/assets/1aa19f1c-5f5e-49b5-802f-f863430417cb" />

11. What is the throughput (bytes transferred per unit time) for the TCP connection? 
- `1.2`

Explain how you calculated this value.
- In Wireshark at the top-bar Menu I clicked `Statistics` → `TCP Stream Graphs` → `Throughput`.

<img width="1174" height="864" alt="image" src="https://github.com/user-attachments/assets/7377eb48-70a2-4d9d-b317-1b3047a6c57e" />

<img width="878" height="830" alt="image" src="https://github.com/user-attachments/assets/f94a794f-3d9f-4e5c-bf30-d0f8814a8369" />

12. Use the Time-Sequence-Graph(Stevens) plotting tool to view the sequence number versus time plot of segments being sent from the client to the gaia.cs.umass.edu server. 
- In Wireshark at the top-bar Menu I clicked `Statistics` → `TCP Stream Graphs` → ` Time Sequence (Stevens)`.

<img width="1174" height="864" alt="image" src="https://github.com/user-attachments/assets/73cd4bee-de7b-46aa-8ca3-4a7b4a2a0e67" />

<img width="878" height="830" alt="image" src="https://github.com/user-attachments/assets/98b7fa3a-e8db-40bc-a68f-fe107bd5c66d" />

Consider the “fleets” of packets sent around t = 0.025, t = 0.053, t = 0.082 and t = 0.1. 

Comment on whether this looks as if TCP is in its slow start phase, congestion avoidance phase or some other phase. 
- Looks like the slow start phase.  

13. These “fleets” of segments appear to have some periodicity. 
- The following number of packets are simultaneously processed, followed by 0.2 seconds where sequence is paused:
	- 9
	- 22
	- 29
	- 39
	- 13

## What I Learned
