### Wireshark Lab: UDP v9 – Summary

## Objective

Explore the [UDP transport protocol](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_UDP_v9.pdf) using Wireshark by capturing and analyzing UDP packets (e.g., via DNS queries with `nslookup`).

---

### **Lab Steps**
1. **Capture UDP Packets**:
   - Start Wireshark and capture packets.
   - Run `nslookup <hostname>` (e.g., `nslookup www.nyu.edu`) to generate DNS/UDP traffic.
   - Stop capture and filter for UDP packets from your host.
   - *Alternative*: Use provided trace file if live capture isn’t possible.

2. **Analyze First UDP Segment**:
   - Select the first UDP packet in the trace.
   - Answer:
     1. **Packet number** in Wireshark.
     2. **Application-layer protocol** (e.g., DNS).
     3. **Number of UDP header fields** and their **names** (observe directly in Wireshark).
     4. **Length (in bytes)** of each UDP header field.
     5. **Length field meaning**: Total length of UDP header + payload. Verify with packet.
     6. **Max UDP payload size**: Derived from total max UDP datagram size minus header.
     7. **Max source port number**: Based on 16-bit port field (0–65535).
     8. **UDP protocol number** in IP header: Look at IP datagram’s Protocol field (decimal).



1. Select the first UDP segment in your trace. What is the packet number4 of this segment in the trace file? What type of application-layer payload or protocol message is being carried in this UDP segment? Look at the details of this packet in Wireshark. How many fields there are in the UDP header? (You shouldn’t look in the textbook! Answer these questions directly from what you observe in the packet trace.) What are the names of these fields?  



2. By consulting the displayed information in Wireshark’s packet content field for this packet (or by consulting the textbook), what is the length (in bytes) of each of the UDP header fields?



3. The value in the Length field is the length of what? (You can consult the text for this answer). Verify your claim with your captured UDP packet. 



4. What is the maximum number of bytes that can be included in a UDP payload? (Hint: the answer to this question can be determined by your answer to 2. above)



5. What is the largest possible source port number? (Hint: see the hint in 4.)



6. What is the protocol number for UDP? Give your answer in decimal notation. To answer this question, you’ll need to look into the Protocol field of the IP datagram containing this UDP segment (see Figure 4.13 in the text, and the discussion of IP header fields).



7. Examine the pair of UDP packets in which your host sends the first UDP packet and the second UDP packet is a reply to this first UDP packet. (Hint: for a second packet to be sent in response to a first packet, the sender of the first packet should be the destination of the second packet). What is the packet number5 of the first of these two UDP segments in the trace file? What is the value in the source port field in this UDP segment? What is the value in the destination port field in this UDP segment? What is the packet number6 of the second of these two UDP segments in the trace file? What is the value in the source port field in this second UDP segment? What is the value in the destination port field in this second UDP segment? Describe the relationship between the port numbers in the two packets.



3. **Examine UDP Request-Response Pair**:
   - Identify a UDP query (e.g., DNS request) and its reply.
   - For both packets, record:
     - **Packet numbers** (Wireshark-assigned).
     - **Source and destination port numbers**.
   - **Describe port relationship**: Source port of request = destination port of reply (and vice versa).

---

### **What I Learned**
- UDP is simple: minimal header with **4 fields** (Source Port, Destination Port, Length, Checksum).
- DNS commonly uses UDP (port 53).

**Quick lab (~30 mins)** focused on direct observation of UDP structure and behavior in real traffic.
