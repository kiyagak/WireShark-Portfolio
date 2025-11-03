# Wireshark - Capture TCP File Transfers & TCP Segments

## Objective

This Wireshark Lab investigates TCP behavior by analyzing a packet trace of a ~150KB file transfer (Alice’s Adventures in Wonderland) from your computer to gaia.cs.umass.edu using HTTP POST. You will examine TCP’s reliable data transfer (sequence/acknowledgment numbers), congestion control (slow start, congestion avoidance), flow control, connection setup, and performance (throughput, RTT). Review text sections 3.5 and 3.7 before starting.

**Steps:**

1. **Capture a bulk TCP transfer:**
   - Download `alice.txt` from http://gaia.cs.umass.edu/wireshark-labs/alice.txt.
   - Go to http://gaia.cs.umass.edu/wireshark-labs/TCP-wireshark-file1.html.
   - Use "Browse" to select the local `alice.txt` file.
   - Start Wireshark and begin packet capture.
   - Click “Upload alice.txt file” to send the file via HTTP POST.
   - Stop capture after upload completes.
   - (Alternative: Download pre-captured trace `tcp-wireshark-trace1-1` from wireshark-traces-9e.zip if live capture isn’t possible.)

2. **Initial trace analysis:**
   - Filter for `tcp` to view TCP segments.
   - Locate the HTTP POST message (spread across ~106 TCP segments).
   - Identify the TCP three-way handshake (SYN, SYNACK, ACK) and first data segment.

3. **Use Wireshark tools:**
   - Apply `tcp` filter to focus on TCP segments.
   - Use Statistics → TCP Stream Graph → Round Trip Time Graph for RTT analysis.
   - Use Statistics → TCP Stream Graph → Time-Sequence-Graph (Stevens) for congestion control analysis.

**Questions (left exactly as in the original):**

1. What is the IP address and TCP port number used by the client computer (source) that is transferring the alice.txt file to gaia.cs.umass.edu? 

To answer this question, it’s probably easiest to select an HTTP message and explore the details of the TCP packet used to carry this HTTP message, using the “details of the selected packet header window” (refer to Figure 2 in the “Getting Started with Wireshark” Lab if you’re uncertain about the Wireshark windows).



2. What is the IP address of gaia.cs.umass.edu? 

On what port number is it sending and receiving TCP segments for this connection?

3. What is the sequence number of the TCP SYN segment that is used to initiate the TCP connection between the client computer and gaia.cs.umass.edu? 

(Note: this is the “raw” sequence number carried in the TCP segment itself; it is NOT the packet # in the “No.” column in the Wireshark window. 

Remember there is no such thing as a “packet number” in TCP or UDP; as you know, there are sequence numbers in TCP and that’s what we’re after here. 

Also note that this is not the relative sequence number with respect to the starting sequence number of this TCP session.). 

What is it in this TCP segment that identifies the segment as a SYN segment? 

Will the TCP receiver in this session be able to use Selective Acknowledgments (allowing TCP to function a bit more like a “selective repeat” receiver, see section 3.4.5 in the text)?



4. What is the sequence number of the SYNACK segment sent by gaia.cs.umass.edu to the client computer in reply to the SYN? 

What is it in the segment that identifies the segment as a SYNACK segment? What is the value of the Acknowledgement field in the SYNACK segment? 


How did gaia.cs.umass.edu determine that value?


5. What is the sequence number of the TCP segment containing the header of the HTTP POST command? 

Note that in order to find the POST message header, you’ll need to dig into the packet content field at the bottom of the Wireshark window, 

looking for a segment with the ASCII text “POST” within its DATA field4,5. How many bytes of data are contained in the payload (data) field of this TCP segment? 

Did all of the data in the transferred file alice.txt fit into this single segment?


6. Consider the TCP segment containing the HTTP “POST” as the first segment in the data transfer part of the TCP connection.
• At what time was the first segment (the one containing the HTTP POST) in the data-transfer part of the TCP connection sent?
• At what time was the ACK for this first data-containing segment received?
• What is the RTT for this first data-containing segment?
• What is the RTT value the second data-carrying TCP segment and its ACK?
• What is the EstimatedRTT value (see Section 3.5.3, in the text) after the ACK for the second data-carrying segment is received? 
	Assume that in making this calculation after the received of the ACK for the second segment, that the initial value of EstimatedRTT is equal to the measured RTT for the first segment, and then is computed using the EstimatedRTT equation on page 242, and a value of a = 0.125.

7. What is the length (header plus payload) of each of the first four data-carrying TCP segments?



8. What is the minimum amount of available buffer space advertised to the client by gaia.cs.umass.edu among these first four data-carrying TCP segments7? 

Does the lack of receiver buffer space ever throttle the sender for these first four data-carrying segments?



9. Are there any retransmitted segments in the trace file? 

What did you check for (in the trace) in order to answer this question?



10. How much data does the receiver typically acknowledge in an ACK among the first ten data-carrying segments sent from the client to gaia.cs.umass.edu? 

Can you identify cases where the receiver is ACKing every other received segment (see Table 3.2 in the text) among these first ten data-carrying segments?



11. What is the throughput (bytes transferred per unit time) for the TCP connection? 

Explain how you calculated this value.



12. Use the Time-Sequence-Graph(Stevens) plotting tool to view the sequence number versus time plot of segments being sent from the client to the gaia.cs.umass.edu server. 

Consider the “fleets” of packets sent around t = 0.025, t = 0.053, t = 0.082 and t = 0.1. 

Comment on whether this looks as if TCP is in its slow start phase, congestion avoidance phase or some other phase. 

Figure 6 shows a slightly different view of this data.



13. These “fleets” of segments appear to have some periodicity. 

What can you say about the period?



14. Answer each of two questions above for the trace that you have gathered when you transferred a file from your computer to gaia.cs.umass.edu

## What I Learned
