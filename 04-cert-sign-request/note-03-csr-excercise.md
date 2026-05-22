# CSR with Internal Certificate Authorities

`fabrikam.com` is a company with its own internal Root Certificate Authority (Root CA).
There is a Sales department which wants to host a sub-domain HTTPS application:

```text
https://sales.fabrikam.com
```

They want the certificate to be signed by the internal Root CA instead of a public CA like GoDaddy or DigiCert.

---

# Assumption: Fabrikam Root CA Already Has Private Key Setup

If not, use the commands below to generate the Root CA private key.

## Generate Root CA Private Key

### RSA

```bash
openssl genrsa -out private-key-root.ca.fabrikam.com-rsa-4096.pem 4096
```

### ECC

```bash
openssl ecparam -genkey -name prime256v1 -out private-key-root.ca.fabrikam.com-ecc-prime256v1.pem -noout
```

---

# Create Root CA X509 Certificate

## RSA Root Certificate

```bash
openssl req -x509 -sha256 -days 365 \
-key private-key-root.ca.fabrikam.com-rsa-4096.pem \
-out ca-root-cert.rsa.pem \
-config fabrikam.com.conf
```

## ECC Root Certificate

```bash
openssl req -x509 -sha256 -days 365 \
-key private-key-root.ca.fabrikam.com-ecc-prime256v1.pem \
-out ca-root-cert.ecc.pem \
-config fabrikam.com.conf
```

---

# CSR Request Generation from Fabrikam Sales Team

The Sales department first generates its own private key.

---

# Step 1: Create Private Key at Sales Department Side

## RSA

```bash
openssl genrsa -out private-key-sales.fabrikam.com-rsa-4096.pem 4096
```

## ECC

```bash
openssl ecparam -genkey -name prime256v1 \
-out private-key-sales.fabrikam.com-ecc-prime256v1.pem \
-noout
```

---

# Step 2: Generate CSR Request

Generate the CSR using the `openssl req` command.

## RSA CSR

```bash
openssl req \
-key private-key-sales.fabrikam.com-rsa-4096.pem \
-new \
-out csr-rsa.pem \
-config sales.fabrikam.com.conf
```

## ECC CSR

```bash
openssl req \
-key private-key-sales.fabrikam.com-ecc-prime256v1.pem \
-new \
-out csr-ecc.pem \
-config sales.fabrikam.com.conf
```

---

# Step 3: Inspect the CSR

You can inspect the CSR using:

## Online Decoder

[CertLogik CSR Decoder](https://certlogik.com/decoder?utm_source=chatgpt.com)

Upload:

* `csr-rsa.pem`
* `csr-ecc.pem`

---

## Inspect Using OpenSSL

```bash
openssl req -in csr-ecc.pem -text

openssl req -in csr-rsa.pem -text
```

---

# Example CSR Output

```text
-----BEGIN CERTIFICATE REQUEST-----
MIIBWTCB/wIBADCBnDELMAkGA1UEBhMCSU4xFDASBgNVBAgMC01haGFyYXNodHJh
MQ4wDAYDVQQHDAVUaGFuZTEnMCUGA1UECgweU2FsZXMgRGVwYXJ0bWVudCwgRmFi
cmlrYW0gT3JnMRswGQYDVQQDDBJzYWxlcy5mYWJyaWthbS5jb20xITAfBgkqhkiG
9w0BCQEWEnNhbGVzQGZhYnJpa2FtLmNvbTBZMBMGByqGSM49AgEGCCqGSM49AwEH
A0IABC1R47Cl5WSLnYAB3wie5padrPPVQOCid5XalGFnjkBToqmUBcAxs+bAMERT
zppX5oivYgfZxLLzJ7K97tR8vSygADAKBggqhkjOPQQDAgNJADBGAiEAuSpMyHwe
y/NR+/k6REXX/beOeynb6JxqqH64uAEfweACIQC0nXgvqWwXjbr3yc5ELS39iG2r
SoJFwl+yaEXYToK8ZA==
-----END CERTIFICATE REQUEST-----
```

This is the exact content that needs to be sent to the Root CA for signing.

The Sales department can:

* ZIP the CSR file
* Protect it with a password
* Send it securely to the Root CA administrators

This prevents accidental modification during transfer.

---

# At Root CA Side

The Root CA administrators first inspect the CSR contents.

## Validate CSR

```bash
openssl req -in csr-ecc.pem -text

openssl req -in csr-rsa.pem -text
```

They verify:

* Subject details
* Common Name (CN)
* Organization
* Email
* Public key
* Requested attributes

---

# Root CA Signs the CSR

The Root CA uses:

* Its own private key
* Its Root CA certificate

to generate a signed certificate.

---

# RSA Signing

```bash
openssl x509 -req \
-CA ca-root-cert.rsa.pem \
-CAkey private-key-root.ca.fabrikam.com-rsa-4096.pem \
-in "../CSR-Workbench/csr-rsa.pem" \
-out intermediate-cert.rsa.pem \
-days 365
```

---

# ECC Signing

```bash
openssl x509 -req \
-CA ca-root-cert.ecc.pem \
-CAkey private-key-root.ca.fabrikam.com-ecc-prime256v1.pem \
-in "../CSR-Workbench/csr-ecc.pem" \
-out intermediate-cert.ecc.pem \
-days 365
```

---

# Root CA Sends Back the Signed Certificate

The signed certificate files returned are:

```text
intermediate-cert.rsa.pem
```

or

```text
intermediate-cert.ecc.pem
```

---

# Inspect the Signed Certificate

## ECC

```bash
openssl x509 -in intermediate-cert.ecc.pem -text -noout
```

## RSA

```bash
openssl x509 -in intermediate-cert.rsa.pem -text -noout
```

---

# Notice the Issuer and Subject

```text
Issuer: C=IN, ST=Maharashtra, L=Mumbai, O=Fabrikam Org, CN=fabrikam.com, emailAddress=rootca@fabrikam.com

Subject: C=IN, ST=Maharashtra, L=Thane, O=Sales Department, Fabrikam Org, CN=sales.fabrikam.com, emailAddress=sales@fabrikam.com
```

## Important Observation

* `Issuer` = Root CA
* `Subject` = Sales Department Certificate

This proves that the certificate was signed by the internal Root CA.

---

# Export Certificate to DER Format

Windows can easily display certificate details from `.der` files.

## RSA DER Export

```bash
openssl x509 -in intermediate-cert.rsa.pem \
-out intermediate-cert.rsa.der \
-outform DER
```

## ECC DER Export

```bash
openssl x509 -in intermediate-cert.ecc.pem \
-out intermediate-cert.ecc.der \
-outform DER
```

Now double-click the `.der` file in Windows to inspect the certificate using the Certificate Viewer.

---

# Final Flow Summary

```text
Sales Team
   |
   |---- Generate Private Key
   |
   |---- Generate CSR
   |
   |---- Send CSR to Root CA
                |
                |---- Root CA validates CSR
                |
                |---- Root CA signs CSR using Root Private Key
                |
                |---- Sends signed certificate back
   |
Sales Team receives signed certificate
   |
Deploy HTTPS Application
```
