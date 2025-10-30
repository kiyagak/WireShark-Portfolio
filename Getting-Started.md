## Objectives

The goal is to [get started](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_Intro_v9.0.pdf) with Wireshark, a network packet analyzer.

## Instructions

**Important Considerations:**

*   Disable any VPN services, as they encrypt upper-layer protocol information (HTTP, TCP), hindering Wireshark analysis.
*   Disable HTTP/3 and QUIC protocols in your browser, as they also encrypt upper-layer information. Instructions for disabling these protocols in Chrome, Edge, and Firefox are available at [https://techysnoop.com/disable-quic-protocol-in-chrome-edge-firefox/](https://techysnoop.com/disable-quic-protocol-in-chrome-edge-firefox/).
*   Ensure URLs start with "http://" instead of "https://" to avoid frame encryption.
*   Turn off privacy and browser privacy settings.
*   Clear your browser cache and history before starting.

## Disable QUIC in Chrome

1.  Open Chrome and type `chrome://flags` in the address bar.
2.  Search for "QUIC."
3.  Disable the "Experimental QUIC Protocol" option. (Alternatively, go to `chrome://flags/#enable-quic`)
4.  Relaunch Chrome.

<img width="857" height="512" alt="image" src="https://github.com/user-attachments/assets/d1949f9d-e4d5-4a10-a1f8-21d5f0abe15d" />

## Disable QUIC in Firefox

1.  Open Firefox and type `about:config` in the address bar.
2.  Accept the risk and continue.
3.  Search for `network.http.http3.enable`.
4.  Double-click `network.http.http3.enable`, `network.http.http3.enable_0rtt`, and `network.http.http3.enable_qlog` to set their values to `false`.
5.  Restart Firefox.

## Disable QUIC in Edge

1.  Open Edge and type `edge://flags` in the address bar.
2.  Search for "QUIC."
3.  Disable the "Experimental QUIC Protocol" option.
4.  Restart Edge.

**Wireshark Interface Overview:**

The Wireshark interface consists of five main components:

*   **Command Menus:** Standard pulldown menus (File, Capture). File allows saving/opening packet data and exiting. Capture allows starting packet capture.
*   **Packet-Listing Window:** Displays a one-line summary of each captured packet (packet number, time, source/destination IP addresses, upper-layer protocol, protocol-specific information). Sortable by column.
*   **Packet-Header Details Window:** Provides detailed information about the selected packet, including Ethernet frame, IP datagram, TCP/UDP details, and highest-level protocol details. Expandable/collapsible.
*   **Packet-Contents Window:** Displays the entire contents of the captured frame in ASCII and hexadecimal format.
*   **Packet Display Filter Field:** Allows filtering displayed packets based on protocol name or other information.

**Test Run Instructions:**

1.  Start your web browser.
2.  Start Wireshark.
```bash
sudo wireshark &
```
4.  Select "Capture" -> "Interfaces" (or "Options" on Mac).
5.  Select the appropriate interface (e.g., Wi-Fi or Ethernet) and start packet capture.

<img width="770" height="490" alt="image" src="https://github.com/user-attachments/assets/e99e87e7-f120-4dab-be83-8ff77f53e32b" />

5.  While Wireshark is running, enter the URL `http://gaia.cs.umass.edu/wireshark-labs/INTRO-wireshark-file1.html` in your browser.

<img width="789" height="171" alt="Screenshot From 2025-10-30 17-48-16" src="https://github.com/user-attachments/assets/ff9d29f6-7834-410c-85b1-3387e8b84d24" />

6.  Stop Wireshark packet capture.
7.  Type "http" in the display filter field and apply the filter.
9.  Find the HTTP GET message sent to gaia.cs.umass.edu.

<img width="1394" height="735" alt="Screenshot From 2025-10-30 17-56-09" src="https://github.com/user-attachments/assets/4a0bb35b-c295-4b85-b573-f4bf6694512c" />

10.  Minimize Frame, Ethernet, IP, and TCP details; maximize HTTP protocol information.
11. Exit Wireshark.

<img width="1394" height="735" alt="Screenshot From 2025-10-30 17-56-09" src="https://github.com/user-attachments/assets/1035cf1d-7aac-4bfc-b3cd-940f2ecc525b" />
<img width="1348" height="665" alt="Screenshot From 2025-10-30 17-53-55" src="https://github.com/user-attachments/assets/164202fd-0e35-44be-817a-de1165ed1aed" />

## What I Learned

I learned that
- it took just under two (`0.177781925`) seconds between the HTTP GET message being sent until the HTTP OK reply was received
- the Internet address of gaia.cs.umass.edu is `128.119.245.12`
- the captured address of my device is `10.0.22.190`
- The type of web browser that issued the request is: `Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/142.0.0.0 Safari/537.36`

**Note:** A sample packet trace file is available for download at [http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip](http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip) for use if live capture is not possible.
