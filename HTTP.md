# Wireshark Lab: HTTP

## Objectives

The goal is to use Wireshark to [analyze HTTP protocol behavior](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_HTTP_v9.0.pdf):  
- GET/response interaction  
- Message formats  
- Retrieval of large files  
- Embedded objects  
- Conditional GET  
- Authentication

---

### **Pre-Lab Setup (Critical)**

> **Warning: Must be done before every capture to ensure unencrypted HTTP traffic**

- **Disable VPN** (encrypts HTTP/TCP)  
- **Disable HTTP/3 & QUIC** in browser (default in 2025 browsers)  
  → Guide: [https://techysnoop.com/disable-quic-protocol-in-chrome-edge-firefox/](https://techysnoop.com/disable-quic-protocol-in-chrome-edge-firefox/)  
- Use **`http://`** (not `https://`) to avoid encryption  
- **Turn off** privacy settings  
- **Clear browser cache & history** before each section

---

## Disable QUIC in Chrome

1. Open Chrome → type `chrome://flags` in the address bar  
2. Search for **"QUIC"**  
3. Disable **"Experimental QUIC Protocol"**  
   *(Alternatively: `chrome://flags/#enable-quic`)*  
4. Relaunch Chrome

![Disable QUIC in Chrome](https://github.com/user-attachments/assets/d1949f9d-e4d5-4a10-a1f8-21d5f0abe15d)

---

## Disable Caching in Chrome

1. Right-click on the web page → **Inspect**  
2. Go to the **Network** tab  
3. Check **Disable cache**

![Disable cache in Chrome DevTools](https://github.com/user-attachments/assets/ca8f60e3-b98a-4057-bcab-9401ad34654f)

---

## Disable QUIC in Firefox

1. Open Firefox → type `about:config`  
2. Accept the risk and continue  
3. Search for:  
   - `network.http.http3.enable`  
   - `network.http.http3.enable_0rtt`  
   - `network.http.http3.enable_qlog`  
4. Double-click each to set value to **`false`**  
5. Restart Firefox

---

## Disable QUIC in Edge

1. Open Edge → type `edge://flags`  
2. Search for **"QUIC"**  
3. Disable **"Experimental QUIC Protocol"**  
4. Restart Edge

---

### **1. Basic HTTP GET/Response**

**Steps:**

1. Start your web browser and Wireshark  
   ```bash
   sudo wireshark &
   ```
2. In Wireshark, apply filter:  
   ```
   http
   ```
3. Wait 1+ minute → Visit:  
   [http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file1.html](http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file1.html)  
4. Stop capture

![HTTP Filter](https://github.com/user-attachments/assets/6c0cb2de-4f29-4cfa-825b-5c78ab7b5158)  
![GET Request Packet](https://github.com/user-attachments/assets/29c7efc5-d0b1-442b-981f-71193d696eaf)  
![Response Packet](https://github.com/user-attachments/assets/bddbf46d-7d92-4001-8c88-a6534cf39428)

#### **Questions**

| Question | Answer |
|--------|--------|
| **HTTP version (client & server)?** | Request: **HTTP/1.1**<br>Response: **HTTP/1.1** |
| **Accepted languages?** | `Accept-Language: en-US,en;q=0.9` |
| **IP addresses (client & server)?** | Server: `128.119.245.12`<br>Client: `10.0.22.190` |
| **Status code?** | **200 OK** |
| **Last-Modified time?** | `Tue, 28 Oct 2025 05:59:01 GMT` |
| **Bytes returned?** | **128 bytes** |
| **Any headers in raw data not shown in packet list?** | **No** |

> **Note:** File’s `Last-Modified` updates every minute (server-side)

---

### **2. HTTP Conditional GET**

**Steps:**

1. Clear browser cache/history  
2. Open Wireshark → Start capture  
3. Browse to:  
   [http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file2.html](http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file2.html)  
4. **Refresh** the page  
5. Stop capture  
6. Filter: `http`

![Conditional GET Overview](https://github.com/user-attachments/assets/e6c35713-a3ca-40ba-94b3-1472f4150180)  
![First GET](https://github.com/user-attachments/assets/6eff17c4-f9fe-4f56-b918-f949158fcf17)  
![Second GET with 304](https://github.com/user-attachments/assets/e876a316-acec-44f9-b49a-c19abfa7262e)

#### **Questions**

| Question | Answer |
|--------|--------|
| **First GET: `If-Modified-Since` present?** | **No** |
| **Server response: File returned?** | **Yes** |
| **Second GET: `If-Modified-Since` header?** | **Yes** |
| **Value of `If-Modified-Since`?** | `Sat, 30 Jan 2021 06:59:02 GMT` |
| **Status code?** | **304 Not Modified** |
| **Was the file sent again?** | **No** |

> **Hint:** Chrome reliably uses conditional GET; Safari/Firefox may not

---

### **3. Retrieving Long Documents**

**Steps:**

1. Clear cache → Start capture  
2. Load:  
   [http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file3.html](http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file3.html) *(US Bill of Rights)*  
3. **Clear filter** to see all TCP segments

#### **Questions**

| # | Question | Answer |
|---|--------|--------|
| 12 | **Number of GET requests?** | **2**<br>Filter: `http.request.method == "GET"` |
| 13 | **GET Request Packet Number?** | **534** |
| 14 | **Packet # with status code?** | **551** |
| 15 | **Status code/phrase?** | **200 OK** |

![GET Requests Filter](https://github.com/user-attachments/assets/3669d502-5178-4c1a-8ceb-0da33f39b924)  
![Response Packet](https://github.com/user-attachments/assets/c1928786-2a10-4809-bd30-80722e3f17df)

#### **16. Number of TCP segments for HTTP response**

- Filter: `http.response`  
- Right-click response → **Follow → TCP Stream**  
- Note stream index (e.g., `tcp.stream == 15`)  
- **TCP stream number:** `15`

![Follow TCP Stream](https://github.com/user-attachments/assets/087ebecc-c568-4e75-a980-ae775e20631e)  
![Reassembled PDU](https://github.com/user-attachments/assets/8a7e48c1-cdfa-47b8-9905-541e575ff5ee)

> **Note:** Large HTML → split into multiple TCP segments  
> Wireshark shows: *“TCP segment of a reassembled PDU”*

---

### **4. HTML with Embedded Objects**

**Steps:**

1. Clear cache/history  
2. Start Wireshark capture  
3. Visit:  
   [http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file4.html](http://gaia.cs.umass.edu/wireshark-labs/HTTP-wireshark-file4.html)

#### **Questions**

| # | Question | Answer |
|---|--------|--------|
| 16 | **Number of GET requests?** | **4** |
| 18 | **Destination IPs?** | `128.119.245.12`, `2.56.99.24` |
| 19 | **Images downloaded serially or in parallel?** | **Serially** |

![Packet List](https://github.com/user-attachments/assets/0d055e01-d86e-4c77-9783-ba29c4b30842)  
![Multiple GETs](https://github.com/user-attachments/assets/07379973-70c5-4c40-96d1-dc1c04385280)

---

### **5. HTTP Authentication**

**Steps:**

1. Clear cache → Restart browser → Start capture  
2. Visit:  
   [http://gaia.cs.umass.edu/wireshark-labs/protected_pages/HTTP-wireshark-file5.html](http://gaia.cs.umass.edu/wireshark-labs/protected_pages/HTTP-wireshark-file5.html)  
   **Username:** `wireshark-students`  
   **Password:** `network`

![Auth Prompt](https://github.com/user-attachments/assets/6d4bc20a-755c-4aca-adc6-1d80f8aec002)  
![401 Unauthorized](https://github.com/user-attachments/assets/b19f3c67-9855-4726-8915-240c3b1b7894)  
![Authorization Header](https://github.com/user-attachments/assets/251c0e54-7cf1-4b60-9c41-49fba2b72c55)

#### **Questions**

| # | Question | Answer |
|---|--------|--------|
| 20 | **Server’s first response (status code/phrase)?** | **401 Unauthorized** |
| 21 | **Second GET: New header?** | `Authorization: Basic d2lyZXNoYXJrLXN0dWRlbnRzOm5ldHdvcms=` |

> **Security Note:**  
> Credentials sent in **Base64** (not encrypted)  
> `d2lyZXNoYXJrLXN0dWRlbnRzOm5ldHdvcms=` → decodes to `wireshark-students:network`  
> **Anyone with Wireshark can read it!**

---

### **Packet Traces (if live capture fails)**

Download: [wireshark-traces-9e.zip](http://gaia.cs.umass.edu/wireshark-labs/wireshark-traces-9e.zip)

| File | Section |
|------|---------|
| `http-wireshark-trace1-1` | Section 1 |
| `http-wireshark-trace2-1` | Section 2 |
| `http-wireshark-trace3-1` | Section 3 |
| `http-wireshark-trace4-1` | Section 4 |
| `http-wireshark-trace5-1` | Section 5 |

---

## What I Learned

I learned to use **Wireshark** to sniff and analyze HTTP traffic. Key takeaways:

- HTTP runs over **TCP** and large responses are segmented  
- Web pages with **embedded objects** → multiple GET requests  
- **Conditional GET** saves bandwidth using `If-Modified-Since`  
- **Basic Authentication is insecure** — Base64 ≠ encryption  
  → Credentials are **readable in plaintext** with packet capture

--- 

*Lab completed on October 31, 2025*
```
