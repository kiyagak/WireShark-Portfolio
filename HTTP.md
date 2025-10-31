# Wireshark Lab: HTTP

## Objectives

The goal is to use Wireshark to [analyze HTTP protocol behavior](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_HTTP_v9.0.pdf): GET/response, message formats, large files, embedded objects, conditional GET, and authentication.

---

### **Pre-Lab Setup (Critical)**
- **Disable VPN** (encrypts HTTP/TCP).
- **Disable HTTP/3 & QUIC** in browser (default in 2025 browsers).  
  → Guide: https://techysnoop.com/disable-quic-protocol-in-chrome-edge-firefox/
- Use **http://** (not https://) to avoid encryption.
- **Turn off** privacy settings.
- **Clear browser cache & history** before each section.

## Disable QUIC in Chrome

1.  Open Chrome and type `chrome://flags` in the address bar.
2.  Search for "QUIC."
3.  Disable the "Experimental QUIC Protocol" option. (Alternatively, go to `chrome://flags/#enable-quic`)
4.  Relaunch Chrome.

<img width="857" height="512" alt="image" src="https://github.com/user-attachments/assets/d1949f9d-e4d5-4a10-a1f8-21d5f0abe15d" />

## Disable Caching in Chrome

1. Right click anywhere on the web page.
2. Click **Inspect**.
3. Click the **Network** tab.
4. Click the checkbox for **Disable cache**.  

<img width="1404" height="799" alt="image" src="https://github.com/user-attachments/assets/ca8f60e3-b98a-4057-bcab-9401ad34654f" />

## Disable QUIC in Firefox

1.  Open Firefox and type `about:config` in the address bar.
2.  Accept the risk and continue.
3.  Search for `network.http.http3.enable`.
4.  Double-click `network.http.http3.enable`, `network.http.http3.enable_0rtt`, and `network.http.http3.enable_qlog` to set their values to `false`.
5.  Restart Firefox.

<img width="1024" height="435" alt="image" src="https://github.com/user-attachments/assets/8e1ce0f1-37a0-4bf9-96e7-1331c4ca763f" />

<img width="1024" height="506" alt="image" src="https://github.com/user-attachments/assets/08781afc-a409-4f3c-adc9-7e6abb1ec85a" />


## Disable QUIC in Edge

1.  Open Edge and type `edge://flags` in the address bar.
2.  Search for "QUIC."
3.  Disable the "Experimental QUIC Protocol" option.
4.  Restart Edge.

---

### **1. Basic HTTP GET/Response**
**Steps:**
1. Start your web browser and Wireshark.
    
    	sudo wireshark &
    
2. Filter for only HTTP within Wireshark: http
3. Wait 1+ min → Capture → Visit:

		http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file1.html

4. Stop capture.

<img width="772" height="149" alt="Screenshot From 2025-10-30 23-20-32" src="https://github.com/user-attachments/assets/6c0cb2de-4f29-4cfa-825b-5c78ab7b5158" />

<img width="1969" height="716" alt="Screenshot From 2025-10-30 23-17-46" src="https://github.com/user-attachments/assets/29c7efc5-d0b1-442b-981f-71193d696eaf" />

<img width="1969" height="900" alt="Screenshot From 2025-10-30 23-18-19" src="https://github.com/user-attachments/assets/bddbf46d-7d92-4001-8c88-a6534cf39428" />


**Questions:**

HTTP version (client & server)?
- Request Version: HTTP/1.1
- Response Version: HTTP/1.1

Accepted languages?
- Accept-Language: en-US,en;q=0.9
	
IP addresses (client & server)?
- Server: 128.119.245.12
- Client: 10.0.22.190

Status code?
- 200

Last-Modified time?
- Tue, 28 Oct 2025 05:59:01 GMT
	
Bytes returned?
- 128 bytes

By inspecting the raw data in the packet content window, do you see any headers within the data that are not displayed in the packet-listing window? If so, name one.
- No

> *Note:* File’s Last-Modified updates every minute (server-side).

---

### **2. HTTP Conditional GET**
**Steps:**
- Start up your web browser, and clear your browser cache/history.
- Open Wireshark.
- Start a Wireshark capture.
- Browse to the following web page:

  		http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file2.html

- Refresh the page.  
- Stop the capture.  
- In Wireshark filter for HTTP: `http`

<img width="767" height="242" alt="Screenshot From 2025-10-30 23-25-02" src="https://github.com/user-attachments/assets/e6c35713-a3ca-40ba-94b3-1472f4150180" />

<img width="1296" height="690" alt="image" src="https://github.com/user-attachments/assets/6eff17c4-f9fe-4f56-b918-f949158fcf17" />

<img width="1296" height="690" alt="Screenshot From 2025-10-31 01-12-03" src="https://github.com/user-attachments/assets/e876a316-acec-44f9-b49a-c19abfa7262e" />

**Questions:**

- First GET: `If-Modified-Since` present?
	- No
- Server response: File returned?
	- Yes 
- Second GET: `If-Modified-Since` header? 
	- Yes
 - Value?
 	- If-Modified-Since: Sat, 30 Jan 2021 06:59:02 GMT
- Status code?
	- Status Code: 304
- File sent?
 	- Yes

> *Hint:* Chrome reliably uses conditional GET; Safari/Firefox may not.

---

### **3. Retrieving Long Documents**
**Steps:**
- Clear cache → Capture → Load `HTTP-wireshark-file3.html` (US Bill of Rights)
- **Clear filter** to see all TCP segments.

**Questions:**
12. # of GET requests? Packet # of GET?
13. Packet # with status code?
14. Status code/phrase?
15. # of TCP segments for HTTP response?

> *Note:* Large HTML → split into multiple TCP segments (Wireshark shows “TCP segment of a reassembled PDU”).

---

### **4. HTML with Embedded Objects**
**Steps:**
- Clear cache → Capture → Load `HTTP-wireshark-file4.html`  
  (Contains 2 images: one local, one from France)

**Questions:**
16. # of GET requests? Destination IPs?
17. Images downloaded **serially or in parallel**?

---

### **5. HTTP Authentication**
**Steps:**
- Clear cache → Restart browser → Capture
- Visit: `http://gaia.cs.umass.edu/wireshark-labs/protected_pages/HTTP-wireshark-file5.html`  
  **Username:** `wireshark-students`  
  **Password:** `network`

**Questions:**
18. Server’s first response (status code/phrase)?
19. Second GET: New header? (→ `Authorization: Basic ...`)

> **Security Note:**  
> Credentials sent in **Base64** (not encrypted).  
> `d2lyZXNoYXJrLXN0dWRlbnRzOm5ldHdvcms=` → decodes to `wireshark-students:network`  
> → **Anyone with Wireshark can read it!**

---

### **Packet Traces (if live capture fails)**
Download: `http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip`  
Use:
- `http-wireshark-trace1-1` → Section 1
- `http-wireshark-trace2-1` → Section 2
- `http-wireshark-trace3-1` → Section 3
- `http-wireshark-trace4-1` → Section 4
- `http-wireshark-trace5-1` → Section 5

---

### **Key Takeaways**
- HTTP runs over TCP; large responses are segmented.
- Conditional GET saves bandwidth.
- Embedded objects → multiple GETs (often parallel).
- Basic Auth is **insecure** (Base64 ≠ encryption).
- Use `http://`, disable QUIC/HTTP3, clear cache for accurate lab results.

**Textbook Reference:** Section 2.2 (9th ed.)  
**Authors’ Site:** http://gaia.cs.umass.edu/kurose_ross

## What I Learned
