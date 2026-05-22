# File formats:

There are quite a few certificate / private key container formats, and the naming gets confusing because some are **encoding formats**, some are **certificate formats**, and some are **container standards**.

Here’s a practical breakdown of the most common PKI-related file formats you’ll encounter.

---

# 1. PEM (`.pem`)

Most common text-based format.

* Base64 encoded
* Human-readable
* Wrapped with headers like:

```text
-----BEGIN CERTIFICATE-----
-----END CERTIFICATE-----
```

Can contain:

* Certificates
* Private keys
* Certificate chains
* CSRs

Common extensions:

* `.pem`
* `.crt`
* `.cer`
* `.key`

Example:

```bash
openssl x509 -in cert.pem -text
```

---

# 2. DER (`.der`)

Binary form of X.509 certificate.

* Not human-readable
* Compact binary encoding
* Often used by Java, Windows, embedded devices

Usually contains:

* Single certificate
* Sometimes private key

Convert:

```bash
openssl x509 -inform der -in cert.der -out cert.pem
```

---

# 3. CRT (`.crt`)

Not actually a unique format.

Can be:

* PEM encoded
* DER encoded

Usually means:

* Certificate only

Linux commonly uses:

* `.crt`

Need to inspect:

```bash
file cert.crt
```

---

# 4. CER (`.cer`)

Same story as `.crt`.

Can contain:

* PEM
* DER

Mostly Windows naming convention.

---

# 5. KEY (`.key`)

Usually private key file.

May contain:

* RSA private key
* EC private key
* PKCS#8 key

Examples:

```text
-----BEGIN PRIVATE KEY-----
```

or

```text
-----BEGIN RSA PRIVATE KEY-----
```

---

# 6. PFX / P12 (`.pfx`, `.p12`)

PKCS#12 container format.

Binary encrypted bundle containing:

* Certificate
* Private key
* Intermediate certs
* Full chain

Password protected.

Very common in:

* Windows
* IIS
* Azure
* Java imports
* Kubernetes TLS secrets

Export example:

```bash
openssl pkcs12 -export -out cert.pfx \
  -inkey key.pem \
  -in cert.pem
```

---

# 7. PKCS#7 / P7B (`.p7b`, `.p7c`)

Certificate chain container.

Contains:

* Certificates
* Intermediate CAs

Does NOT contain:

* Private keys

Common in:

* Microsoft environments
* Java trust chains

Example:

```text
-----BEGIN PKCS7-----
```

---

# 8. CSR (`.csr`)

Certificate Signing Request.

Sent to CA to request a certificate.

Contains:

* Public key
* Subject info
* Signature

No certificate yet.

Example:

```text
-----BEGIN CERTIFICATE REQUEST-----
```

Generate:

```bash
openssl req -new -key key.pem -out req.csr
```

---

# 9. PKCS#8 (`.p8`, `.pk8`)

Private key storage standard.

Modern standard for private keys.

Can store:

* RSA
* EC
* Ed25519

Usually:

```text
-----BEGIN PRIVATE KEY-----
```

Encrypted variant:

```text
-----BEGIN ENCRYPTED PRIVATE KEY-----
```

---

# 10. JKS (`.jks`)

Java KeyStore.

Java-specific keystore format.

Contains:

* Certificates
* Private keys
* Trusted roots

Managed with:

```bash
keytool
```

---

# 11. BKS (`.bks`)

BouncyCastle KeyStore.

Used in:

* Android
* Java apps using BouncyCastle

---

# 12. KDB (`.kdb`)

IBM GSKit database.

Used by:

* IBM MQ
* WebSphere

---

# 13. SPC (`.spc`)

Software Publisher Certificate.

Old Microsoft Authenticode-related format.

---

# 14. SST (`.sst`)

Microsoft Serialized Certificate Store.

Windows certificate bundle export.

---

# 15. PVK (`.pvk`)

Microsoft private key format.

Older Windows systems / Authenticode.

---

# 16. PEM Chain Variants

You’ll also see:

| Extension        | Meaning            |
| ---------------- | ------------------ |
| `.ca-bundle`     | CA chain           |
| `.chain.pem`     | intermediate chain |
| `.fullchain.pem` | cert + chain       |
| `.bundle.pem`    | combined bundle    |

Common in:

* NGINX
* Apache
* Let’s Encrypt

---

# Important Distinction

## Encoding vs Container

| Type                 | Examples         |
| -------------------- | ---------------- |
| Encoding format      | PEM, DER         |
| Certificate standard | X.509            |
| Container format     | PFX, PKCS#7, JKS |
| Private key standard | PKCS#1, PKCS#8   |

---

# Real-World Common Usage

| Platform          | Common Format |
| ----------------- | ------------- |
| Linux / NGINX     | PEM           |
| Apache            | PEM           |
| Windows IIS       | PFX           |
| Azure App Gateway | PFX           |
| Kubernetes TLS    | PEM           |
| Java              | JKS / PKCS12  |
| AWS ACM import    | PEM           |
| cert-manager      | PEM           |
| F5 Load Balancer  | PEM           |
| Citrix ADC        | PEM / PFX     |

---

# Useful OpenSSL Inspection Commands

Inspect certificate:

```bash
openssl x509 -in cert.pem -text -noout
```

Inspect PFX:

```bash
openssl pkcs12 -info -in cert.pfx
```

Inspect CSR:

```bash
openssl req -text -noout -in req.csr
```

Inspect private key:

```bash
openssl rsa -in key.pem -check
```

Inspect PKCS7:

```bash
openssl pkcs7 -print_certs -in cert.p7b
```

---

# Quick Mental Model

* PEM = text wrapper
* DER = binary wrapper
* CRT/CER = usually cert
* KEY = private key
* CSR = request for cert
* PFX/P12 = everything bundled
* P7B = cert chain only
* JKS = Java keystore


# what are the file extensions used for certificates? 

Certificate-related files use a lot of different extensions, but many of them are just different encodings or containers around the same X.509 certificate data.

Here’s the practical breakdown:

---

# Common Certificate File Extensions

| Extension     | What it Usually Contains    | Format      | Private Key Included?   | Common Usage          |
| ------------- | --------------------------- | ----------- | ----------------------- | --------------------- |
| `.crt`        | Certificate                 | PEM or DER  | No                      | Linux, Apache, Nginx  |
| `.cer`        | Certificate                 | PEM or DER  | No                      | Windows               |
| `.pem`        | Certificate, key, or bundle | Base64 PEM  | Sometimes               | OpenSSL, Linux        |
| `.der`        | Certificate                 | Binary DER  | No                      | Java, Windows         |
| `.p7b`        | Certificate chain           | PKCS#7      | No                      | Windows/IIS           |
| `.p7c`        | Certificate chain           | PKCS#7      | No                      | Similar to P7B        |
| `.pfx`        | Certificate + private key   | PKCS#12     | Yes                     | Windows export/import |
| `.p12`        | Certificate + private key   | PKCS#12     | Yes                     | Cross-platform        |
| `.csr`        | Certificate Signing Request | PEM/DER     | No                      | Sent to CA            |
| `.key`        | Private key                 | PEM/DER     | Yes (key only)          | OpenSSL/Linux         |
| `.pub`        | Public key                  | Various     | Public key only         | SSH/OpenSSL           |
| `.jks`        | Java keystore               | Java format | Can contain keys        | Java apps             |
| `.keystore`   | Java keystore               | Java format | Can contain keys        | Java/Tomcat           |
| `.truststore` | Trusted CA certs            | Java format | Usually no private keys | Java trust validation |

---

# Important Concept

The **extension itself does NOT guarantee the format**.

For example:

* `server.crt`
* `server.cer`
* `server.pem`

…could all contain the exact same certificate.

The real difference is usually:

* **Encoding**

  * PEM (Base64 text)
  * DER (binary)

* **Container type**

  * X.509
  * PKCS#7
  * PKCS#12

---

# PEM vs DER

## PEM (text format)

Looks like this:

```pem
-----BEGIN CERTIFICATE-----
MIID....
-----END CERTIFICATE-----
```

Human-readable Base64 text.

Common extensions:

* `.pem`
* `.crt`
* `.cer`
* `.key`

---

## DER (binary format)

Binary encoded.

Not human-readable.

Common extensions:

* `.der`
* `.cer`
* `.crt`

---

# PKCS Standards Behind These

| Standard | Used By              |
| -------- | -------------------- |
| PKCS#1   | RSA keys             |
| PKCS#7   | `.p7b`, `.p7c`       |
| PKCS#8   | Generic private keys |
| PKCS#10  | CSR (`.csr`)         |
| PKCS#12  | `.pfx`, `.p12`       |

---

# Typical Enterprise Examples

## Linux Web Server

```text
server.crt   -> certificate
server.key   -> private key
ca-bundle.crt -> intermediate/root chain
```

---

## Windows / IIS

```text
server.pfx
```

Contains:

* certificate
* private key
* certificate chain

All in one file.

---

## Kubernetes TLS Secret

Usually:

```text
tls.crt
tls.key
```

---

## Java Applications

```text
keystore.jks
truststore.jks
```

---

# Quick Rule of Thumb

## Usually public cert only

* `.crt`
* `.cer`
* `.der`

## Usually private key included

* `.pfx`
* `.p12`
* `.key`

## Usually chain/bundle

* `.p7b`
* `.p7c`

---

# Useful OpenSSL Commands

## View certificate

```bash
openssl x509 -in cert.pem -text -noout
```

---

## Detect if PEM

```bash
cat cert.pem
```

If you see:

```text
-----BEGIN CERTIFICATE-----
```

…it is PEM.

---

## Convert PEM → DER

```bash
openssl x509 -outform der -in cert.pem -out cert.der
```

---

## Convert PFX → PEM

```bash
openssl pkcs12 -in cert.pfx -out cert.pem
```

---

# One More Important Distinction

A **certificate** is NOT the same as a **public key**.

Certificate contains:

* Public key
* Subject name
* Issuer
* Validity dates
* Signature from CA
* Extensions/SANs

Whereas a raw public key file (`.pub`) only contains the key material.
