# Wireshark Lab: TLS

## Objective

In this [TLS Wireshark lab](https://www-net.cs.umass.edu/wireshark-labs/Wireshark_TLS_v9.pdf), we’ll investigate Transport Layer Security (TLS) and aspects of the authentication, data integrity, and confidentiality services provided by TLS. TLS is the successor to the now-deprecated Secure Sockets Layer (SSL). We’ll investigate TLS by analyzing a Wireshark packet trace captured during the retrieval of a web page via HTTPS, which implements TLS on top of HTTP. We’ll look at TLS’s client-server handshaking protocol in detail, since that’s where most of the interesting action happens. You may want to review Section 8.6 in the text; useful online resources for learning more about TLS are here, here, and here; and, of course, in RFC 5246: ^1^.

## Capturing packets in a TLS session

- Start Wireshark and begin packet capture.
- Retrieve the homepage from https://msn.com using your browser of choice.
- Stop Wireshark packet capture.

<img width="676" height="439" alt="image" src="https://github.com/user-attachments/assets/cfecb8c2-acf4-45b4-832f-64c0f66d167e" />

## A first look at the captured trace

- Open the Terminal.  
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

<img width="916" height="570" alt="Screenshot-20251110T013302" src="https://github.com/user-attachments/assets/c1bae7f1-d357-4df5-a1aa-f66fdc134433" />

## The TLS Handshake: Client Hello Message
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

## The TLS Handshake: Server Hello Message

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

<img width="1008" height="570" alt="Screenshot-20251109T232210" src="https://github.com/user-attachments/assets/dd23e7fb-c0d0-490a-868a-5339f93d11e9" />

```
29	0.681609230	204.79.197.219	10.0.2.15	TLSv1.3	1516	Server Hello

Transport Layer Security
    TLSv1.3 Record Layer: Handshake Protocol: Server Hello
        Content Type: Handshake (22)
        Version: TLS 1.2 (0x0303)
        Handshake Protocol: Server Hello
            Handshake Type: Server Hello (2)
            Version: TLS 1.2 (0x0303)
            Random: af1e883eab5464394afdfc336b180aed2a1d4930dc55fefe925eeb5ca43451b2
            Cipher Suite: TLS_AES_256_GCM_SHA384 (0x1302)
```

## The Certificate Packet - Additional Information from the Server

- Open the Terminal.
- Filter for the `Certificate` messages:

      tls.handshake.type == 11

- Identify the packet number for the TLS message part containing the public key certificate for msn.com.
  - `1720`

- Determine if more than one certificate is returned and who they are for if not all are for msn.com.
  - More than one certificate is returned.  
  - They are not for msn.com.
  - They are for `ib.msn.com`.  

<img width="1290" height="570" alt="Screenshot-20251110T013712" src="https://github.com/user-attachments/assets/9240bc13-5b4f-405d-a1fa-723885c35a65" />

```
1720	4.391131980	68.67.176.83	10.0.2.15	TLSv1.2	202	Certificate, Server Key Exchange, Server Hello Done

Transport Layer Security
    TLSv1.2 Record Layer: Handshake Protocol: Certificate
        Content Type: Handshake (22)
        Version: TLS 1.2 (0x0303)
        Handshake Protocol: Certificate
            Handshake Type: Certificate (11)
            Certificates (2803 bytes)
                Certificate: 3082069b30820621a00302010202133300028cef291ee4e0067b82d0000000028cef300a… (id-at-commonName=ib.msn.com,id-at-organizationName=Microsoft Corporation,id-at-localityName=Redmond,id-at-stateOrProvinceName=WA,id-at-countryName=US
                Certificate: 3082044a308203d1a00302010202133300000034c732435db22a0a2b000000000034300a… (id-at-commonName=Microsoft Azure ECC TLS Issuing CA 07,id-at-organizationName=Microsoft Corporation,id-at-countryName=US)
```

- Identify the name of the certification authority that issued the certificate for msn.com.
	- `Microsoft ECC Root Certificate Authority 2017`

- Determine the digital signature algorithm used by the CA to sign this certificate.
	- ECDSA with SHA384
	- `1.2.840.10045.4.3.3 (ecdsa-with-SHA384)`

- Find the first four hexadecimal digits of the modulus of the public key being used by msn.com.
	- `041e`
	- `subjectPublicKey: 041e2cd4ca9cae9a6eb810578e90587d5f99a3f6ad415f3fe19a4f030f65ba47229f5dfcf30a10a16e64aabb84a3134c313f2e8bacd4341789ad51fcb55c412104`

- Look for messages between the client and a CA to verify the CA-signed certificate sent by the server.

```
1720	4.391131980	68.67.176.83	10.0.2.15	TLSv1.2	202	Certificate, Server Key Exchange, Server Hello Done
3517	6.239036200	20.96.153.111	10.0.2.15	TLSv1.2	673	Server Hello, Certificate, Certificate Status, Server Key Exchange, Server Hello Done
5511	9.094022080	40.126.7.32	10.0.2.15	TLSv1.2	1134	Server Hello, Certificate, Certificate Status, Server Key Exchange, Server Hello Done
```

---

```
1720	4.391131980	68.67.176.83	10.0.2.15	TLSv1.2	202	Certificate, Server Key Exchange, Server Hello Done

Transport Layer Security
    TLSv1.2 Record Layer: Handshake Protocol: Certificate
        Content Type: Handshake (22)
        Version: TLS 1.2 (0x0303)
        Length: 2810
        Handshake Protocol: Certificate
            Handshake Type: Certificate (11)
            Length: 2806
            Certificates Length: 2803
            Certificates (2803 bytes)
                Certificate Length: 1695
                Certificate: 3082069b30820621a00302010202133300028cef291ee4e0067b82d0000000028cef300a… (id-at-commonName=ib.msn.com,id-at-organizationName=Microsoft Corporation,id-at-localityName=Redmond,id-at-stateOrProvinceName=WA,id-at-countryName=US
                    signedCertificate
                        version: v3 (2)
                        serialNumber: 0x3300028cef291ee4e0067b82d0000000028cef
                        signature (ecdsa-with-SHA384)
                            Algorithm Id: 1.2.840.10045.4.3.3 (ecdsa-with-SHA384)
                        issuer: rdnSequence (0)
                            rdnSequence: 3 items (id-at-commonName=Microsoft Azure ECC TLS Issuing CA 07,id-at-organizationName=Microsoft Corporation,id-at-countryName=US)
                                RDNSequence item: 1 item (id-at-countryName=US)
                                    RelativeDistinguishedName item (id-at-countryName=US)
                                        Object Id: 2.5.4.6 (id-at-countryName)
                                        CountryName: US
                                RDNSequence item: 1 item (id-at-organizationName=Microsoft Corporation)
                                    RelativeDistinguishedName item (id-at-organizationName=Microsoft Corporation)
                                        Object Id: 2.5.4.10 (id-at-organizationName)
                                        DirectoryString: printableString (1)
                                            printableString: Microsoft Corporation
                                RDNSequence item: 1 item (id-at-commonName=Microsoft Azure ECC TLS Issuing CA 07)
                                    RelativeDistinguishedName item (id-at-commonName=Microsoft Azure ECC TLS Issuing CA 07)
                                        Object Id: 2.5.4.3 (id-at-commonName)
                                        DirectoryString: printableString (1)
                                            printableString: Microsoft Azure ECC TLS Issuing CA 07
                        validity
                            notBefore: utcTime (0)
                                utcTime: 2025-07-15 23:15:22 (UTC)
                            notAfter: utcTime (0)
                                utcTime: 2026-01-11 23:15:22 (UTC)
                        subject: rdnSequence (0)
                            rdnSequence: 5 items (id-at-commonName=ib.msn.com,id-at-organizationName=Microsoft Corporation,id-at-localityName=Redmond,id-at-stateOrProvinceName=WA,id-at-countryName=US)
                                RDNSequence item: 1 item (id-at-countryName=US)
                                    RelativeDistinguishedName item (id-at-countryName=US)
                                        Object Id: 2.5.4.6 (id-at-countryName)
                                        CountryName: US
                                RDNSequence item: 1 item (id-at-stateOrProvinceName=WA)
                                    RelativeDistinguishedName item (id-at-stateOrProvinceName=WA)
                                        Object Id: 2.5.4.8 (id-at-stateOrProvinceName)
                                        DirectoryString: printableString (1)
                                            printableString: WA
                                RDNSequence item: 1 item (id-at-localityName=Redmond)
                                    RelativeDistinguishedName item (id-at-localityName=Redmond)
                                        Object Id: 2.5.4.7 (id-at-localityName)
                                        DirectoryString: printableString (1)
                                            printableString: Redmond
                                RDNSequence item: 1 item (id-at-organizationName=Microsoft Corporation)
                                    RelativeDistinguishedName item (id-at-organizationName=Microsoft Corporation)
                                        Object Id: 2.5.4.10 (id-at-organizationName)
                                        DirectoryString: printableString (1)
                                            printableString: Microsoft Corporation
                                RDNSequence item: 1 item (id-at-commonName=ib.msn.com)
                                    RelativeDistinguishedName item (id-at-commonName=ib.msn.com)
                                        Object Id: 2.5.4.3 (id-at-commonName)
                                        DirectoryString: printableString (1)
                                            printableString: ib.msn.com
                        subjectPublicKeyInfo
                            algorithm (id-ecPublicKey)
                                Algorithm Id: 1.2.840.10045.2.1 (id-ecPublicKey)
                                ECParameters: namedCurve (1)
                                    namedCurve: 1.2.840.10045.3.1.7 (secp256r1)
                            Padding: 0
                            subjectPublicKey: 041e2cd4ca9cae9a6eb810578e90587d5f99a3f6ad415f3fe19a4f030f65ba47229f5dfcf30a10a16e64aabb84a3134c313f2e8bacd4341789ad51fcb55c412104
                        extensions: 12 items
                            Extension (SignedCertificateTimestampList)
                                Extension Id: 1.3.6.1.4.1.11129.2.4.2 (SignedCertificateTimestampList)
                                Serialized SCT List Length: 361
                                Signed Certificate Timestamp (Google 'Xenon2026h1' log)
                                    Serialized SCT Length: 118
                                    SCT Version: 0
                                    Log ID: 969764bf555897adf743876837084277e9f03ad5f6a4f3366e46a43f0fcaa9c6
                                    Timestamp: Jul 15, 2025 23:25:25.097000000 UTC
                                    Extensions length: 0
                                    Signature Algorithm: ecdsa_secp256r1_sha256 (0x0403)
                                        Signature Hash Algorithm Hash: SHA256 (4)
                                        Signature Hash Algorithm Signature: ECDSA (3)
                                    Signature Length: 71
                                    Signature: 3045022100dd2b72ec715133fc443875e5b998b03aff70f809deb994103de8917a16c7d0…
                                Signed Certificate Timestamp (DigiCert 'Wyvern2026h1')
                                    Serialized SCT Length: 118
                                    SCT Version: 0
                                    Log ID: 6411c46ca412eca7891ca2022e00bcab4f2807d41e3527abeafed503c97dcdf0
                                    Timestamp: Jul 15, 2025 23:25:25.315000000 UTC
                                    Extensions length: 0
                                    Signature Algorithm: ecdsa_secp256r1_sha256 (0x0403)
                                        Signature Hash Algorithm Hash: SHA256 (4)
                                        Signature Hash Algorithm Signature: ECDSA (3)
                                    Signature Length: 71
                                    Signature: 3045022028c2a0d8c39f2deb7d64a625719ce0b5a2121edfbce51ede90ea0a13621990b4…
                                Signed Certificate Timestamp (Sectigo 'Sabre2026h1')
                                    Serialized SCT Length: 119
                                    SCT Version: 0
                                    Log ID: 566cd5a376be83dfe342b675c49c232498a769bac382cbab49a3877d9ab32d01
                                    Timestamp: Jul 15, 2025 23:25:25.324000000 UTC
                                    Extensions length: 0
                                    Signature Algorithm: ecdsa_secp256r1_sha256 (0x0403)
                                        Signature Hash Algorithm Hash: SHA256 (4)
                                        Signature Hash Algorithm Signature: ECDSA (3)
                                    Signature Length: 72
                                    Signature: 3046022100d1a88727ef900f437c40141493d9fd751d7223fff3d3464a50e27104516ba3…
                            Extension (id-ms-application-certificate-policies)
                                Extension Id: 1.3.6.1.4.1.311.21.10 (id-ms-application-certificate-policies)
                                CertificatePoliciesSyntax: 2 items
                                    PolicyInformation
                                        policyIdentifier: 1.3.6.1.5.5.7.3.2 (id-kp-clientAuth)
                                    PolicyInformation
                                        policyIdentifier: 1.3.6.1.5.5.7.3.1 (id-kp-serverAuth)
                            Extension (id-ms-certificate-template)
                                Extension Id: 1.3.6.1.4.1.311.21.7 (id-ms-certificate-template)
                                CertificateTemplate
                                    templateID: 1.3.6.1.4.1.311.21.8.15690651.3798470.4214446.239628.16526621.93.4912048.16480415 (iso.3.6.1.4.1.311.21.8.15690651.3798470.4214446.239628.16526621.93.4912048.16480415)
                                    templateMajorVersion: 100
                                    templateMinorVersion: 45
                            Extension (id-pe-authorityInfoAccess)
                                Extension Id: 1.3.6.1.5.5.7.1.1 (id-pe-authorityInfoAccess)
                                AuthorityInfoAccessSyntax: 2 items
                                    AccessDescription
                                        accessMethod: 1.3.6.1.5.5.7.48.2 (id-ad-caIssuers)
                                        accessLocation: 6
                                            uniformResourceIdentifier: http://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20ECC%20TLS%20Issuing%20CA%2007%20-%20xsign.crt
                                    AccessDescription
                                        accessMethod: 1.3.6.1.5.5.7.48.1 (id-ad-ocsp)
                                        accessLocation: 6
                                            uniformResourceIdentifier: http://oneocsp.microsoft.com/ocsp
                            Extension (id-ce-subjectKeyIdentifier)
                                Extension Id: 2.5.29.14 (id-ce-subjectKeyIdentifier)
                                SubjectKeyIdentifier: 062c2e57775031cb0f155059c55fdfbbcc04bfb1
                            Extension (id-ce-keyUsage)
                                Extension Id: 2.5.29.15 (id-ce-keyUsage)
                                critical: True
                                Padding: 7
                                KeyUsage: 80
                                    1... .... = digitalSignature: True
                                    .0.. .... = contentCommitment: False
                                    ..0. .... = keyEncipherment: False
                                    ...0 .... = dataEncipherment: False
                                    .... 0... = keyAgreement: False
                                    .... .0.. = keyCertSign: False
                                    .... ..0. = cRLSign: False
                                    .... ...0 = encipherOnly: False
                                    0... .... = decipherOnly: False
                            Extension (id-ce-subjectAltName)
                                Extension Id: 2.5.29.17 (id-ce-subjectAltName)
                                GeneralNames: 11 items
                                    GeneralName: dNSName (2)
                                        dNSName: ib.msn.com
                                    GeneralName: dNSName (2)
                                        dNSName: nym2-ib.msn.com
                                    GeneralName: dNSName (2)
                                        dNSName: lax1-ib.msn.com
                                    GeneralName: dNSName (2)
                                        dNSName: ams3-ib.msn.com
                                    GeneralName: dNSName (2)
                                        dNSName: fra1-ib.msn.com
                                    GeneralName: dNSName (2)
                                        dNSName: sin3-ib.msn.com
                                    GeneralName: dNSName (2)
                                        dNSName: zue1-ib.msn.com
                                    GeneralName: dNSName (2)
                                        dNSName: zue2-ib.msn.com
                                    GeneralName: dNSName (2)
                                        dNSName: zuw1-ib.msn.com
                                    GeneralName: dNSName (2)
                                        dNSName: zuw3-ib.msn.com
                                    GeneralName: dNSName (2)
                                        dNSName: zwe1-ib.msn.com
                            Extension (id-ce-basicConstraints)
                                Extension Id: 2.5.29.19 (id-ce-basicConstraints)
                                critical: True
                                BasicConstraintsSyntax [0 length]
                            Extension (id-ce-cRLDistributionPoints)
                                Extension Id: 2.5.29.31 (id-ce-cRLDistributionPoints)
                                CRLDistPointsSyntax: 1 item
                                    DistributionPoint
                                        distributionPoint: fullName (0)
                                            fullName: 1 item
                                                GeneralName: uniformResourceIdentifier (6)
                                                    uniformResourceIdentifier: http://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20ECC%20TLS%20Issuing%20CA%2007.crl
                            Extension (id-ce-certificatePolicies)
                                Extension Id: 2.5.29.32 (id-ce-certificatePolicies)
                                CertificatePoliciesSyntax: 2 items
                                    PolicyInformation
                                        policyIdentifier: 1.3.6.1.4.1.311.76.509.1.1 (iso.3.6.1.4.1.311.76.509.1.1)
                                        policyQualifiers: 1 item
                                            PolicyQualifierInfo
                                                Id: 1.3.6.1.5.5.7.2.1 (id-qt-cps)
                                                DirectoryString: http://www.microsoft.com/pkiops/Docs/Repository.htm
                                    PolicyInformation
                                        policyIdentifier: 2.23.140.1.2.2 (joint-iso-itu-t.23.140.1.2.2)
                            Extension (id-ce-authorityKeyIdentifier)
                                Extension Id: 2.5.29.35 (id-ce-authorityKeyIdentifier)
                                AuthorityKeyIdentifier
                                    keyIdentifier: c35eac4076c0064de32b9499306073349829c651
                            Extension (id-ce-extKeyUsage)
                                Extension Id: 2.5.29.37 (id-ce-extKeyUsage)
                                KeyPurposeIDs: 2 items
                                    KeyPurposeId: 1.3.6.1.5.5.7.3.2 (id-kp-clientAuth)
                                    KeyPurposeId: 1.3.6.1.5.5.7.3.1 (id-kp-serverAuth)
                    algorithmIdentifier (ecdsa-with-SHA384)
                        Algorithm Id: 1.2.840.10045.4.3.3 (ecdsa-with-SHA384)
                    Padding: 0
                    encrypted: 30650231009b650c71052a29acdd4fd33773fb599d7aee973f3bee7d99100233da8503a9…
                Certificate Length: 1102
                Certificate: 3082044a308203d1a00302010202133300000034c732435db22a0a2b000000000034300a… (id-at-commonName=Microsoft Azure ECC TLS Issuing CA 07,id-at-organizationName=Microsoft Corporation,id-at-countryName=US)
                    signedCertificate
                        version: v3 (2)
                        serialNumber: 0x3300000034c732435db22a0a2b000000000034
                        signature (ecdsa-with-SHA384)
                            Algorithm Id: 1.2.840.10045.4.3.3 (ecdsa-with-SHA384)
                        issuer: rdnSequence (0)
                            rdnSequence: 3 items (id-at-commonName=Microsoft ECC Root Certificate Authority 2017,id-at-organizationName=Microsoft Corporation,id-at-countryName=US)
                                RDNSequence item: 1 item (id-at-countryName=US)
                                    RelativeDistinguishedName item (id-at-countryName=US)
                                        Object Id: 2.5.4.6 (id-at-countryName)
                                        CountryName: US
                                RDNSequence item: 1 item (id-at-organizationName=Microsoft Corporation)
                                    RelativeDistinguishedName item (id-at-organizationName=Microsoft Corporation)
                                        Object Id: 2.5.4.10 (id-at-organizationName)
                                        DirectoryString: printableString (1)
                                            printableString: Microsoft Corporation
                                RDNSequence item: 1 item (id-at-commonName=Microsoft ECC Root Certificate Authority 2017)
                                    RelativeDistinguishedName item (id-at-commonName=Microsoft ECC Root Certificate Authority 2017)
                                        Object Id: 2.5.4.3 (id-at-commonName)
                                        DirectoryString: printableString (1)
                                            printableString: Microsoft ECC Root Certificate Authority 2017
                        validity
                            notBefore: utcTime (0)
                                utcTime: 2023-05-25 23:48:00 (UTC)
                            notAfter: utcTime (0)
                                utcTime: 2028-05-25 23:48:00 (UTC)
                        subject: rdnSequence (0)
                            rdnSequence: 3 items (id-at-commonName=Microsoft Azure ECC TLS Issuing CA 07,id-at-organizationName=Microsoft Corporation,id-at-countryName=US)
                                RDNSequence item: 1 item (id-at-countryName=US)
                                    RelativeDistinguishedName item (id-at-countryName=US)
                                        Object Id: 2.5.4.6 (id-at-countryName)
                                        CountryName: US
                                RDNSequence item: 1 item (id-at-organizationName=Microsoft Corporation)
                                    RelativeDistinguishedName item (id-at-organizationName=Microsoft Corporation)
                                        Object Id: 2.5.4.10 (id-at-organizationName)
                                        DirectoryString: printableString (1)
                                            printableString: Microsoft Corporation
                                RDNSequence item: 1 item (id-at-commonName=Microsoft Azure ECC TLS Issuing CA 07)
                                    RelativeDistinguishedName item (id-at-commonName=Microsoft Azure ECC TLS Issuing CA 07)
                                        Object Id: 2.5.4.3 (id-at-commonName)
                                        DirectoryString: printableString (1)
                                            printableString: Microsoft Azure ECC TLS Issuing CA 07
                        subjectPublicKeyInfo
                            algorithm (id-ecPublicKey)
                                Algorithm Id: 1.2.840.10045.2.1 (id-ecPublicKey)
                                ECParameters: namedCurve (1)
                                    namedCurve: 1.3.132.0.34 (secp384r1)
                            Padding: 0
                            subjectPublicKey: 04e8926f61367102423da56d933d8acea9953236ae08db58e90e2ea725b1661eee1972d5…
                        extensions: 10 items
                            Extension (id-ce-keyUsage)
                                Extension Id: 2.5.29.15 (id-ce-keyUsage)
                                critical: True
                                Padding: 1
                                KeyUsage: 86
                                    1... .... = digitalSignature: True
                                    .0.. .... = contentCommitment: False
                                    ..0. .... = keyEncipherment: False
                                    ...0 .... = dataEncipherment: False
                                    .... 0... = keyAgreement: False
                                    .... .1.. = keyCertSign: True
                                    .... ..1. = cRLSign: True
                                    .... ...0 = encipherOnly: False
                                    0... .... = decipherOnly: False
                            Extension (id-ms-ca-version)
                                Extension Id: 1.3.6.1.4.1.311.21.1 (id-ms-ca-version)
                                Integer: 0
                            Extension (id-ce-subjectKeyIdentifier)
                                Extension Id: 2.5.29.14 (id-ce-subjectKeyIdentifier)
                                SubjectKeyIdentifier: c35eac4076c0064de32b9499306073349829c651
                            Extension (id-ce-certificatePolicies)
                                Extension Id: 2.5.29.32 (id-ce-certificatePolicies)
                                CertificatePoliciesSyntax: 3 items
                                    PolicyInformation
                                        policyIdentifier: 2.23.140.1.2.1 (joint-iso-itu-t.23.140.1.2.1)
                                    PolicyInformation
                                        policyIdentifier: 2.23.140.1.2.2 (joint-iso-itu-t.23.140.1.2.2)
                                    PolicyInformation
                                        policyIdentifier: 1.3.6.1.4.1.311.76.509.1.1 (iso.3.6.1.4.1.311.76.509.1.1)
                                        policyQualifiers: 1 item
                                            PolicyQualifierInfo
                                                Id: 1.3.6.1.5.5.7.2.1 (id-qt-cps)
                                                DirectoryString: http://www.microsoft.com/pkiops/Docs/Repository.htm
                            Extension (id-ce-extKeyUsage)
                                Extension Id: 2.5.29.37 (id-ce-extKeyUsage)
                                KeyPurposeIDs: 2 items
                                    KeyPurposeId: 1.3.6.1.5.5.7.3.1 (id-kp-serverAuth)
                                    KeyPurposeId: 1.3.6.1.5.5.7.3.2 (id-kp-clientAuth)
                            Extension (id-ms-certificate-template-name)
                                Extension Id: 1.3.6.1.4.1.311.20.2 (id-ms-certificate-template-name)
                                BMPString: SubCA
                            Extension (id-ce-basicConstraints)
                                Extension Id: 2.5.29.19 (id-ce-basicConstraints)
                                critical: True
                                BasicConstraintsSyntax
                                    cA: True
                                    pathLenConstraint: 0
                            Extension (id-ce-authorityKeyIdentifier)
                                Extension Id: 2.5.29.35 (id-ce-authorityKeyIdentifier)
                                AuthorityKeyIdentifier
                                    keyIdentifier: c8cb997270520cf8e6beb20457292acf4210ed35
                            Extension (id-ce-cRLDistributionPoints)
                                Extension Id: 2.5.29.31 (id-ce-cRLDistributionPoints)
                                CRLDistPointsSyntax: 1 item
                                    DistributionPoint
                                        distributionPoint: fullName (0)
                                            fullName: 1 item
                                                GeneralName: uniformResourceIdentifier (6)
                                                    uniformResourceIdentifier: http://www.microsoft.com/pkiops/crl/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crl
                            Extension (id-pe-authorityInfoAccess)
                                Extension Id: 1.3.6.1.5.5.7.1.1 (id-pe-authorityInfoAccess)
                                AuthorityInfoAccessSyntax: 2 items
                                    AccessDescription
                                        accessMethod: 1.3.6.1.5.5.7.48.2 (id-ad-caIssuers)
                                        accessLocation: 6
                                            uniformResourceIdentifier: http://www.microsoft.com/pkiops/certs/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crt
                                    AccessDescription
                                        accessMethod: 1.3.6.1.5.5.7.48.1 (id-ad-ocsp)
                                        accessLocation: 6
                                            uniformResourceIdentifier: http://oneocsp.microsoft.com/ocsp
                    algorithmIdentifier (ecdsa-with-SHA384)
                        Algorithm Id: 1.2.840.10045.4.3.3 (ecdsa-with-SHA384)
                    Padding: 0
                    encrypted: 30640230367fee7dbcc7edd2a734a28d4bf9e92262a5345a170963583d6761a02e8742a1…
```

```
3517	6.239036200	20.96.153.111	10.0.2.15	TLSv1.2	673	Server Hello, Certificate, Certificate Status, Server Key Exchange, Server Hello Done

Handshake Protocol: Certificate Status
    Handshake Type: Certificate Status (22)
    Length: 1862
    Certificate Status Type: OCSP (1)
    OCSP Response Length: 1858
    OCSP Response
        responseStatus: successful (0)
        responseBytes
            ResponseType Id: 1.3.6.1.5.5.7.48.1.1 (id-pkix-ocsp-basic)
            BasicOCSPResponse
                tbsResponseData
                    responderID: byKey (2)
                        byKey: 91b88fc60ecc1645e35da8d35451c33389c308e2
                    producedAt: Nov  3, 2025 19:53:20.000000000 GMT
                    responses: 1 item
                        SingleResponse
                            certID
                                hashAlgorithm (SHA-1)
                                issuerNameHash: 7624db6bd09c44bd8c92a5bc8159aa8199e6a50a
                                issuerKeyHash: ce15163bea02a3a66bdad92bfde58c52be7a50a8
                                serialNumber: 0x330291b1d192c5faeca0690cb200000291b1d1
                            certStatus: good (0)
                                good
                            thisUpdate: Nov  3, 2025 18:13:08.000000000 GMT
                            nextUpdate: Nov 11, 2025 18:33:08.000000000 GMT
                            singleExtensions: 1 item
                                Extension
                                    Id: 1.3.6.1.4.1.311.21.4 (id-ms-next-publish)
                                    UTCTime: 2025-11-07 18:23:08 (UTC)
                signatureAlgorithm (sha256WithRSAEncryption)
                Padding: 0
                signature: 7e516e87e8d28947727425bb9620087eaa21f7c0606f667e43574a11a7530a76e9c84179…
                certs: 1 item
```

## The TLS Handshake: Wrapping up the Handshake

- Filter for TLS handshake packets:

```
tls.handshake
```

- Identify the packet number for the TLS message part that contains the Server Hello Done TLS record.
  - `1720`

```
1720	4.391131980	68.67.176.83	10.0.2.15	TLSv1.2	202	Certificate, Server Key Exchange, Server Hello Done
```

<img width="1213" height="626" alt="Screenshot-20251110T004213" src="https://github.com/user-attachments/assets/28f3773a-16f7-4890-9652-9784291d3e88" />

- Identify the packet number for the TLS message containing the public key information, Change Cipher Spec, and Encrypted Handshake message being sent from client to server.
  - `1722`

<img width="1213" height="581" alt="Screenshot-20251110T004508" src="https://github.com/user-attachments/assets/56e0f9c0-c703-4658-85ee-e7cfdbcdb50b" />

```
1722	4.394384676	10.0.2.15	68.67.176.83	TLSv1.2	149	Client Key Exchange, Change Cipher Spec, Encrypted Handshake Message

Transport Layer Security
    TLSv1.2 Record Layer: Handshake Protocol: Client Key Exchange
        Content Type: Handshake (22)
        Version: TLS 1.2 (0x0303)
        Length: 102
        Handshake Protocol: Client Key Exchange
            Handshake Type: Client Key Exchange (16)
            Length: 98  
            EC Diffie-Hellman Client Params
                Pubkey Length: 97
                Pubkey: 0460c9ec22b128d8b8f70addea76590c3eae8c94e5255e24c49110a90bee7b6a13c2f727…
    TLSv1.2 Record Layer: Change Cipher Spec Protocol: Change Cipher Spec
        Content Type: Change Cipher Spec (20)
        Version: TLS 1.2 (0x0303)
        Length: 1
        Change Cipher Spec Message
    TLSv1.2 Record Layer: Handshake Protocol: Encrypted Handshake Message
        Content Type: Handshake (22)
        Version: TLS 1.2 (0x0303)
        Length: 40
        Handshake Protocol: Encrypted Handshake Message
```

- Check if the client provides its own CA-signed public key certificate back to the server.
	- No, it doesn't.
	- In Wireshark filter for TLS handshake type 11 (Certificate):

			tls.handshake

 	- There is no Certificate (11) TLS handshake packet immediately right after the Server Hello Done packet.
		- If there was, the Issuer field would show itself as self-signed.  

## Application Data

- In Wireshark filter for TLS traffic:

		tls

- Determine the symmetric key cryptography algorithm used by the client and server to encrypt application data.
	- `Cipher Suite: TLS_AES_256_GCM_SHA384 (0x1302)`

<img width="916" height="570" alt="Screenshot-20251110T011647" src="https://github.com/user-attachments/assets/59e02531-9a80-4bd2-b4ac-f28963079a8b" />

- Right-click the Application Data packet → Follow → TLS Stream
- Close the pop up window that appears.

<img width="609" height="294" alt="Screenshot-20251110T012523" src="https://github.com/user-attachments/assets/c6ce5328-934d-4c4b-afaf-0f70fb91062b" />

- Append a TLS filter to the existing filter:

		tcp.stream eq 1 && tls

<img width="916" height="603" alt="Screenshot-20251110T012341" src="https://github.com/user-attachments/assets/923eee0b-6cc9-4382-a671-00be1cb16096" />

- Identify in which TLS message this symmetric key cryptography algorithm is finally decided and declared.
	- `Cipher Suite: TLS_AES_256_GCM_SHA384 (0x1302)`
	- `25	0.639907048	204.79.197.219	10.0.2.15	TLSv1.3	155	Hello Retry Request, Change Cipher Spec`

<img width="916" height="603" alt="Screenshot-20251110T012705" src="https://github.com/user-attachments/assets/8b3c22a8-fe6c-4611-bce0-43cca7b618d1" />

```
25	0.639907048	204.79.197.219	10.0.2.15	TLSv1.3	155	Hello Retry Request, Change Cipher Spec

Transport Layer Security
    TLSv1.3 Record Layer: Handshake Protocol: Hello Retry Request
        Content Type: Handshake (22)
        Version: TLS 1.2 (0x0303)
        Length: 88
        Handshake Protocol: Hello Retry Request
            Handshake Type: Server Hello (2)
            Length: 84
            Version: TLS 1.2 (0x0303)
            Random: cf21ad74e59a6111be1d8c021e65b891c2a211167abb8c5e079e09e2c8a8339c (HelloRetryRequest magic)
            Session ID Length: 32
            Session ID: 5a7c87a51dcc29b211ef704d973515930e3c07282b5e792877fe6e51460c8ec4
            Cipher Suite: TLS_AES_256_GCM_SHA384 (0x1302)
            Compression Method: null (0)
            Extensions Length: 12
            Extension: supported_versions (len=2)
            Extension: key_share (len=2)
            [JA3S Fullstring: 771,4866,43-51]
            [JA3S: 15af977ce25de452b96affa2addb1036]
    TLSv1.3 Record Layer: Change Cipher Spec Protocol: Change Cipher Spec
```

- Filter for TLS packets destined for msn.com.

		tls && ip.dst == 204.79.197.219

- Identify the packet number for the first encrypted message carrying application data from client to server.
	- `35`

<img width="916" height="570" alt="Screenshot-20251110T011257" src="https://github.com/user-attachments/assets/8b6a1e86-3534-4d24-b53c-a4e1ae81d900" />

```
35	0.703720559	10.0.2.15	204.79.197.219	TLSv1.3	130	Application Data

Transport Layer Security
    TLSv1.3 Record Layer: Application Data Protocol: Hypertext Transfer Protocol
        Opaque Type: Application Data (23)
        Version: TLS 1.2 (0x0303)
        Length: 69
        Encrypted Application Data: f331924c3ac8cdb8700b18c9c396f714fbedc582f0f5c3c47847a3e4259d9690c908c55e…
        [Application Data Protocol: Hypertext Transfer Protocol]
```

- Speculate on the content of this encrypted application-data given that this trace was generated by fetching the homepage of msn.com.
	- It probably has news, the weather, sports updates, and Bing search available.  

- Identify the packet number containing the client-to-server TLS message that shuts down the TLS connection.
	- It does not appear in this packet capture.  

## What I Learned

