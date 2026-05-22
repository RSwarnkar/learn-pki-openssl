# Learn PKI and OpenSSL

Understand Public Key Infrastructure (PKI), OpenSSL, CSR, Certificates, Public Key Cryptosystems (RSA and ECC), SSL/TLS certificate and HTTP to HTTPS

Welcome to the PKI and OpenSSL learning repository. This repository contains notes, exercises, certificate examples, and cryptography experiments related to:

* OpenSSL
* RSA & ECC cryptography
* Certificate Authorities (CA)
* Certificate Signing Requests (CSR)
* Certificate formats
* PGP & mTLS
* PKI trust chains

---

# Repository Structure

## 01 - OpenSSL Basics

Core OpenSSL commands and cryptography fundamentals.

* [RSA Basics](./01-Openssl/note-01-openssl-rsa.md)
* [ECC Basics](./01-Openssl/note-02-openssl-ecc.md)
* [Certificate & Key File Formats](./01-Openssl/note-03-file-fomats.md)

### Data Folder

Contains:

* RSA private/public keys
* ECC keys
* Encryption/decryption sample files

Path:

```text
./01-Openssl/data/
```

---

## 02 - Cryptography APIs

Notes about cryptography libraries and algorithms.

* [Bouncy Castle Cryptography APIs](./02-cypto-api/note-01-bouncy.md)
* [ECDSA Overview](./02-cypto-api/note-02-ecdsa.md)

---

## 03 - Root Certificates & Certificate Generation

Working with root certificates and generating certificates using OpenSSL.

* [Generate Root Certificates](./03-root-certificates/note-01-gen-root.md)
* [Inspect Certificates](./03-root-certificates/note-02-inpsect-cert.md)
* [Generate Certificates with OpenSSL Config File](./03-root-certificates/note-03-cert-get-with-conf.md)

### Data Folder

Contains:

* PEM certificates
* DER certificates
* OpenSSL config files
* Root CA examples

Path:

```text
./03-root-certificates/data/
```

---

## 04 - Certificate Signing Requests (CSR)

CSR concepts, scenarios, and exercises.

* [What is CSR?](./04-cert-sign-request/note-01-csr.md)
* [CSR Scenarios](./04-cert-sign-request/note-02-csr-scenario.md)
* [CSR Exercise](./04-cert-sign-request/note-03-csr-excercise.md)

### Exercise Structure

#### Intermediate CA

Path:

```text
./04-cert-sign-request/data/excercise/Intermediate-CA/
```

Contains:

* RSA CSR
* ECC CSR
* Intermediate CA private keys
* OpenSSL configuration

#### Root CA

Path:

```text
./04-cert-sign-request/data/excercise/Root-CA/
```

Contains:

* Root CA private keys
* Signed intermediate certificates
* CSR workbench
* DER and PEM certificates

---

## 99 - Miscellaneous PKI Notes

Additional PKI concepts and supporting topics.

* [Why PKI Matters](./99-misc/note-01-why.md)
* [PGP and mTLS](./99-misc/note-02-pgp-mtls.md)
* [Certificates Overview](./99-misc/note-03-certs.md)

### Images & Diagrams

Contains:

* Chain of trust diagrams
* PKCS reference images
* Certificate wizard screenshots

Path:

```text
./99-misc/
```

---

# Learning Flow Recommendation

Suggested reading order:

1. OpenSSL Basics
2. RSA & ECC
3. Certificate Formats
4. Root Certificates
5. CSR Concepts
6. Certificate Signing Process
7. PKI Trust Chains
8. mTLS & PGP

---

# Useful Topics Covered

* RSA key generation
* ECC key generation
* Public/private keys
* PEM vs DER
* CSR generation
* Root CA signing
* Internal PKI
* Certificate trust chains
* OpenSSL configuration files
* ECDSA
* mTLS
* PGP
* PKCS standards

---

# Repository Notes

* Most examples use OpenSSL CLI.
* Both RSA and ECC workflows are demonstrated.
* Examples include internal enterprise PKI scenarios.
* Several files are intentionally kept for inspection/testing purposes.

---

# Quick Start

Example RSA key generation:

```bash
openssl genrsa -out private-key-2048.pem 2048
```

Example ECC key generation:

```bash
openssl ecparam -genkey -name prime256v1 -out private-key-ecc.pem -noout
```

Example CSR generation:

```bash
openssl req -new -key private-key.pem -out csr.pem
```

Example self-signed certificate:

```bash
openssl req -x509 -days 365 -key private-key.pem -out cert.pem
```

---

# Author Notes

This repository is intended as a hands-on PKI and OpenSSL learning workspace with practical examples, enterprise-style CA hierarchy simulations, and certificate lifecycle exercises.
