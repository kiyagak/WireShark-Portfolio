# Wireshark Lab: TLS

## Objective

In this [TLS Wireshark lab](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_TLS_v9.pdf), we’ll investigate Transport Layer Security (TLS) and aspects of the authentication, data integrity, and confidentiality services provided by TLS. TLS is the successor to the now-deprecated Secure Sockets Layer (SSL). We’ll investigate TLS by analyzing a Wireshark packet trace captured during the retrieval of a web page via HTTPS, which implements TLS on top of HTTP. We’ll look at TLS’s client-server handshaking protocol in detail, since that’s where most of the interesting action happens. You may want to review Section 8.6 in the text; useful online resources for learning more about TLS are here, here, and here; and, of course, in RFC 5246: ^1^.

## Capturing packets in a TLS session:

- Start Wireshark and begin packet capture.
- Retrieve the homepage from https://msn.com using your browser of choice.
- Stop Wireshark packet capture.

**2. A first look at the captured trace:**
- Find the homepage's IP address using:

```
nslookup msn.com
Server:    10.0.2.3
Address 1: 10.0.2.3

Name:      msn.com
Address 1: 204.79.197.219
```

- The IP address is `204.79.197.219`.  
- Set Wireshark’s display to show only packets to and from msn.com (IP address 204.79.197.219).
- Enter `ip.addr == 204.79.197.219` in Wireshark’s display filter window.

<img width="1314" height="532" alt="image" src="https://github.com/user-attachments/assets/0937a155-5cca-4c48-afd2-f218b31d46a8" />

**3. The TLS Handshake: Client Hello message:**
- Identify the packet number containing the TLS Client Hello message.
  - `22`
 
- Determine the version of TLS your client is running, as declared in the Client Hello message.
  - `1.3`
 
- Count the number of cipher suites supported by your client, as declared in the Client Hello message.
  - `16`
 
- Find the first two hexadecimal digits in the random bytes field of the Client Hello message.
  - `7b`
 
- Understand the purpose(s) of the “random bytes” field in the Client Hello message.
  - Uniqueness: Prevents session replay by making every handshake unique. 
  - Key Derivation: Added to PRF for master secret
    - Combined with the Server Random (from Server Hello) and pre-master secret to derive:
      - Master secret
      - Session keys (encryption, MAC, etc.)
  - Entropy: Adds randomness to cryptography for forward secrecy and resistance to prediction attacks.

```
22	0.590052822	10.0.2.15	204.79.197.219	TLSv1.3	1773	Client Hello

Transport Layer Security
    TLSv1.3 Record Layer: Handshake Protocol: Client Hello
        Content Type: Handshake (22)
        Version: TLS 1.0 (0x0301)
        Handshake Protocol: Client Hello
            Handshake Type: Client Hello (1)
            Version: TLS 1.2 (0x0303)
            Random: 7b307adbf47a4fdca1f636fd73a10fb254e672c8f1515b6ff64c06625d38c5fe
            Cipher Suites Length: 32
            Cipher Suites (16 suites)
```

**4. The TLS Handshake: Server Hello message:**

- Identify the packet number containing the TLS Server Hello message.
  - `29`
  - `29	0.681609230	204.79.197.219	10.0.2.15	TLSv1.3	1516	Server Hello`
 
- Determine which cipher suite has been chosen by the server from those offered in the Client Hello message.
  - `TLS_AES_256_GCM_SHA384 (0x1302)`
  - `Cipher Suite: TLS_AES_256_GCM_SHA384 (0x1302)`

- Check if the Server Hello message contains random bytes and understand their purpose(s).
  - Yes, it does.
  - `Random: af1e883eab5464394afdfc336b180aed2a1d4930dc55fefe925eeb5ca43451b2`
  - Purposes
    - Uniqueness: Prevents session replay by making every handshake unique. 
    - Key Derivation: Added to PRF for master secret
      - Combined with the Server Random (from Server Hello) and pre-master secret to derive:
        - Master secret
        - Session keys (encryption, MAC, etc.)
    - Entropy: Adds randomness to cryptography for forward secrecy and resistance to prediction attacks.

<img width="1314" height="772" alt="image" src="https://github.com/user-attachments/assets/c5d35428-c6d3-4d43-aa11-9efa7c2a21ce" />

```
29	0.681609230	204.79.197.219	10.0.2.15	TLSv1.3	1516	Server Hello

Transport Layer Security
    TLSv1.3 Record Layer: Handshake Protocol: Server Hello
        Content Type: Handshake (22)
        Version: TLS 1.2 (0x0303)
        Length: 187
        Handshake Protocol: Server Hello
            Handshake Type: Server Hello (2)
            Length: 183
            Version: TLS 1.2 (0x0303)
            Random: af1e883eab5464394afdfc336b180aed2a1d4930dc55fefe925eeb5ca43451b2
            Session ID Length: 32
            Session ID: 5a7c87a51dcc29b211ef704d973515930e3c07282b5e792877fe6e51460c8ec4
            Cipher Suite: TLS_AES_256_GCM_SHA384 (0x1302)
            Compression Method: null (0)
            Extensions Length: 111
            Extension: supported_versions (len=2)
            Extension: key_share (len=101)
            [JA3S Fullstring: 771,4866,43-51]
            [JA3S: 15af977ce25de452b96affa2addb1036]
```

**5. Additional Information from the Server:**
- Identify the packet number for the TLS message part containing the public key certificate for www.cics.umass.edu.
  - ``

- Determine if more than one certificate is returned and who they are for if not all are for www.cs.umass.edu.
- ``

- Identify the name of the certification authority that issued the certificate for www.cs.umass.edu.
- ``

- Determine the digital signature algorithm used by the CA to sign this certificate.
- ``

- Find the first four hexadecimal digits of the modulus of the public key being used by www.cics.umass.edu.
- ``

- Look for messages between the client and a CA to verify the CA-signed certificate sent by the server.
- ``


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

