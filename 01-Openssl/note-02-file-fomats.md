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
