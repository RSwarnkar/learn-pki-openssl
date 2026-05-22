# what are the certificate standards ?

There are several certificate standards in PKI, but the dominant one everywhere today is the X.509 standard.

When people say “SSL certificate,” “TLS certificate,” “server certificate,” etc., they almost always mean an X.509 certificate.

---

# Main Certificate Standards

## 1. X.509 — The Industry Standard

Defined originally by ITU-T.

Used in:

* HTTPS/TLS
* VPNs
* Enterprise PKI
* Smart cards
* Wi-Fi authentication
* Code signing
* Azure/AWS/GCP
* Active Directory Certificate Services

This standard defines:

* Certificate structure
* Subject identity
* Public key embedding
* Extensions
* CA signatures
* Trust chains

---

## Typical X.509 Certificate Fields

```text id="ux1fj7"
Version
Serial Number
Signature Algorithm
Issuer
Validity
Subject
Subject Public Key Info
Extensions
Signature
```

---

## X.509 Visualization

The core idea is:

\text{Certificate} = \text{Identity} + \text{Public Key} + \text{CA Signature}

---

# Related PKI Standards

## 2. PKCS Standards

Created originally by RSA Laboratories.

These are companion standards around certificates and keys.

Common ones:

| Standard | Purpose               |
| -------- | --------------------- |
| PKCS#1   | RSA cryptography      |
| PKCS#7   | Signed/enveloped data |
| PKCS#8   | Private key format    |
| PKCS#10  | CSR format            |
| PKCS#12  | PFX/P12 bundles       |

---

## 3. CSR Standard — PKCS#10

Certificate Signing Request format.

Used when requesting a certificate from a CA.

Contains:

* Public key
* Subject info
* Requested attributes
* Signature from requester

Example:

```pem id="z8pw8m"
-----BEGIN CERTIFICATE REQUEST-----
...
-----END CERTIFICATE REQUEST-----
```

---

## 4. PKCS#12 (.pfx / .p12)

Bundle/container format.

Can store:

* Certificate
* Private key
* CA chain

Common in:

* Windows
* Enterprise environments
* IIS
* Azure App Gateway imports

---

# Certificate Encoding Standards

These define how certificates are stored/transmitted.

| Format  | Meaning                       |
| ------- | ----------------------------- |
| PEM     | Base64 encoded text           |
| DER     | Binary ASN.1 encoding         |
| CER     | Generic certificate extension |
| CRT     | Generic certificate extension |
| P7B     | PKCS#7 certificate bundle     |
| PFX/P12 | PKCS#12 archive               |

---

# Trust/Validation Standards

## 5. RFC 5280

This is effectively the operational rulebook for X.509 PKI.

Defines:

* Path validation
* Extensions
* Name constraints
* CA behavior
* Chain building

Very important standard.

---

# Modern Protocol Standards Using Certificates

| Protocol     | Uses Certificates? |
| ------------ | ------------------ |
| TLS/HTTPS    | Yes                |
| mTLS         | Yes                |
| S/MIME       | Yes                |
| IPsec        | Yes                |
| LDAPS        | Yes                |
| 802.1X       | Yes                |
| Code Signing | Yes                |

---

# Enterprise PKI Reality

In real enterprise environments:

* Microsoft ADCS issues X.509 certificates
* Linux/OpenSSL consumes X.509
* Browsers trust X.509 roots
* Kubernetes cert-manager manages X.509
* Azure Key Vault stores X.509 certs
* VPN appliances use X.509

So practically:

> X.509 became the universal identity container for public keys.

---

# Simplified Mental Model

```text id="tv6pof"
RSA/ECC = cryptography algorithms
X.509 = certificate identity format
PKCS = storage/request/container standards
TLS = protocol using certificates
```
