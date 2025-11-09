# Wireshark Lab: TLS

## Objective

In this [TLS Wireshark lab](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_TLS_v9.pdf), we’ll investigate Transport Layer Security (TLS) and aspects of the authentication, data integrity, and confidentiality services provided by TLS. TLS is the successor to the now-deprecated Secure Sockets Layer (SSL). We’ll investigate TLS by analyzing a Wireshark packet trace captured during the retrieval of a web page via HTTPS, which implements TLS on top of HTTP. We’ll look at TLS’s client-server handshaking protocol in detail, since that’s where most of the interesting action happens. You may want to review Section 8.6 in the text; useful online resources for learning more about TLS are here, here, and here; and, of course, in RFC 5246: ^1^.

## Capturing packets in a TLS session:

- Start Wireshark and begin packet capture.
- Retrieve the homepage from https://www.cics.umass.edu using your browser of choice.
- Stop Wireshark packet capture.

**2. A first look at the captured trace:**
- Find the homepage's IP address using:

```
nslookup www.cics.umass.edu
Server:    10.0.2.3
Address 1: 10.0.2.3

Name:      www.cics.umass.edu
Address 1: 34.227.156.202 ec2-34-227-156-202.compute-1.amazonaws.com
```

- The IP address is `34.227.156.202`.  
- Set Wireshark’s display to show only packets to and from www.cics.umass.edu (IP address 34.227.156.202).
- Enter `ip.addr == 34.227.156.202` in Wireshark’s display filter window.

<img width="1314" height="532" alt="image" src="https://github.com/user-attachments/assets/0937a155-5cca-4c48-afd2-f218b31d46a8" />

**3. The TLS Handshake: Client Hello message:**
- Identify the packet number containing the TLS Client Hello message.
  - `60`
 
- Determine the version of TLS your client is running, as declared in the Client Hello message.
  - `1.3`
 
- Count the number of cipher suites supported by your client, as declared in the Client Hello message.
  - `16`
 
- Find the first two hexadecimal digits in the random bytes field of the Client Hello message.
  - `2c`
 
- Understand the purpose(s) of the “random bytes” field in the Client Hello message.
  - Uniqueness: Prevents session replay by making every handshake unique. 
  - Key Derivation: Added to PRF for master secret
    - Combined with the Server Random (from Server Hello) and pre-master secret to derive:
      - Master secret
      - Session keys (encryption, MAC, etc.)
  - Entropy: Adds randomness to cryptography for forward secrecy and resistance to prediction attacks.

```
60	0.924121709	10.0.2.15	34.227.156.202	TLSv1.3	1878	Client Hello

Transport Layer Security
    TLSv1.3 Record Layer: Handshake Protocol: Client Hello
        Content Type: Handshake (22)
        Version: TLS 1.0 (0x0301)
        Length: 1819
        Handshake Protocol: Client Hello
            Handshake Type: Client Hello (1)
            Length: 1815
            Version: TLS 1.2 (0x0303)
            Random: 2c4987294e6f3a0ee25089a0230840d23db328b220ecb1fdeb63f2b926ddccc7
            Session ID Length: 32
            Session ID: ae31a565e2c48e64ab9961e806c0028f1c6d0a19665e71ce222e359f32261348
            Cipher Suites Length: 32
            Cipher Suites (16 suites)
            Compression Methods Length: 1
            Compression Methods (1 method)
```

**4. The TLS Handshake: Server Hello message:**
- Identify the packet number containing the TLS Server Hello message.
  - `69`
  - `69	1.015090626	34.227.156.202	10.0.2.15	TLSv1.3	1402	Server Hello, Change Cipher Spec, Application Data`
 
- Determine which cipher suite has been chosen by the server from those offered in the Client Hello message.
  - `TLS_AES_256_GCM_SHA384`
  - `Cipher Suite: TLS_AES_256_GCM_SHA384 (0x1302)`

- Check if the Server Hello message contains random bytes and understand their purpose(s).
  - Yes, it does.
  - `Random: d08dab9538c43970c97ff07eca27e84dd548fce8d0d4ca3e7e7734340fc45848`
  - Purposes
    - Uniqueness: Prevents session replay by making every handshake unique. 
    - Key Derivation: Added to PRF for master secret
      - Combined with the Server Random (from Server Hello) and pre-master secret to derive:
        - Master secret
        - Session keys (encryption, MAC, etc.)
    - Entropy: Adds randomness to cryptography for forward secrecy and resistance to prediction attacks.

<img width="1314" height="772" alt="image" src="https://github.com/user-attachments/assets/c5d35428-c6d3-4d43-aa11-9efa7c2a21ce" />

```
69	1.015090626	34.227.156.202	10.0.2.15	TLSv1.3	1402	Server Hello, Change Cipher Spec, Application Data

Transport Layer Security
    TLSv1.3 Record Layer: Handshake Protocol: Server Hello
        Content Type: Handshake (22)
        Version: TLS 1.2 (0x0303)
        Length: 122
        Handshake Protocol: Server Hello
            Handshake Type: Server Hello (2)
            Random: d08dab9538c43970c97ff07eca27e84dd548fce8d0d4ca3e7e7734340fc45848
            Cipher Suite: TLS_AES_256_GCM_SHA384 (0x1302)
```

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

