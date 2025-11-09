# Wireshark Lab: TLS

## Objective

In this [TLS Wireshark lab](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_TLS_v9.pdf), we’ll investigate Transport Layer Security (TLS) and aspects of the authentication, data integrity, and confidentiality services provided by TLS. TLS is the successor to the now-deprecated Secure Sockets Layer (SSL). We’ll investigate TLS by analyzing a Wireshark packet trace captured during the retrieval of a web page via HTTPS, which implements TLS on top of HTTP. We’ll look at TLS’s client-server handshaking protocol in detail, since that’s where most of the interesting action happens. You may want to review Section 8.6 in the text; useful online resources for learning more about TLS are here, here, and here; and, of course, in RFC 5246: ^1^.

### **Pre-Lab Setup**

> **Warning: Do this before every capture to ensure unencrypted HTTP traffic**

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

## Capturing packets in a TLS session:

- Start Wireshark and begin packet capture.
- Retrieve the homepage from https://www.cics.umass.edu using your browser of choice.
- Stop Wireshark packet capture.

**2. A first look at the captured trace:**
- Set Wireshark’s display to show only packets to and from www.cics.umass.edu (IP address 128.119.240.84).
- Enter `ip.addr == 128.119.240.84` in Wireshark’s display filter window.

**3. The TLS Handshake: Client Hello message:**
- Identify the packet number containing the TLS Client Hello message.
- Determine the version of TLS your client is running, as declared in the Client Hello message.
- Count the number of cipher suites supported by your client, as declared in the Client Hello message.
- Find the first two hexadecimal digits in the random bytes field of the Client Hello message.
- Understand the purpose(s) of the “random bytes” field in the Client Hello message.

**4. The TLS Handshake: Server Hello message:**
- Identify the packet number containing the TLS Server Hello message.
- Determine which cipher suite has been chosen by the server from those offered in the Client Hello message.
- Check if the Server Hello message contains random bytes and understand their purpose(s).

**5. Additional Information from the Server:**
- Identify the packet number for the TLS message part containing the public key certificate for www.cics.umass.edu.
- Determine if more than one certificate is returned and who they are for if not all are for www.cs.umass.edu.
- Identify the name of the certification authority that issued the certificate for www.cs.umass.edu.
- Determine the digital signature algorithm used by the CA to sign this certificate.
- Find the first four hexadecimal digits of the modulus of the public key being used by www.cics.umass.edu.
- Look for messages between the client and a CA to verify the CA-signed certificate sent by the server.

**6. The TLS Handshake: wrapping up the handshake:**
- Identify the packet number for the TLS message part that contains the Server Hello Done TLS record.
- Identify the packet number for the TLS message containing the public key information, Change Cipher Spec, and Encrypted Handshake message being sent from client to server.
- Check if the client provides its own CA-signed public key certificate back to the server.

**7. Application data:**
- Determine the symmetric key cryptography algorithm used by the client and server to encrypt application data.
- Identify in which TLS message this symmetric key cryptography algorithm is finally decided and declared.
- Identify the packet number for the first encrypted message carrying application data from client to server.
- Speculate on the content of this encrypted application-data given that this trace was generated by fetching the homepage of www.cics.umass.edu.
- Identify the packet number containing the client-to-server TLS message that shuts down the TLS connection.

## What I Learned

