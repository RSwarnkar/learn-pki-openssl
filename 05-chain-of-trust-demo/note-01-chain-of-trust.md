# Full OpenSSL PKI Demo

## Root CA → Intermediate CA → Web Server Certificate

This demo creates the following trust chain:

```text
root.org (Root CA)
    └── peddler.io (Intermediate CA)
            └── webapp.litware.com (TLS Server Certificate)
```

The goal is to demonstrate:

* Root CA issuing Intermediate CA certificate
* Intermediate CA issuing Web Server certificate
* Proper certificate chain validation
* Clean folder structure
* Correct OpenSSL extensions

---

# Final Folder Structure

```text
learn-pki-demo/
│
├── 01-root.org/
│   ├── private/
│   ├── certs/
│   ├── csr/
│   ├── config/
│   └── serial
│
├── 02-peddler.io/
│   ├── private/
│   ├── certs/
│   ├── csr/
│   ├── config/
│   └── serial
│
├── 03-litware.com/
│   ├── private/
│   ├── certs/
│   ├── csr/
│   └── config/
│
└── chain/
```

---

# STEP 1 — Create Root CA (root.org)

---

## Create folders

```bash
mkdir -p learn-pki-demo

cd learn-pki-demo

mkdir -p 01-root.org/{private,certs,csr,config}
mkdir -p 02-peddler.io/{private,certs,csr,config}
mkdir -p 03-litware.com/{private,certs,csr,config}
mkdir -p chain
```

---

## Create serial file

```bash
echo 1000 > 01-root.org/serial
```

---

## Create Root CA OpenSSL config

Create file:

```text
01-root.org/config/root-openssl.cnf
```

```ini
[ req ]
default_bits = 4096
prompt = no
distinguished_name = dn
x509_extensions = v3_ca

[ dn ]
C = IN
ST = Maharashtra
L = Thane
O = root.org
OU = Root Certificate Authority
CN = root.org Root CA

[ v3_ca ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true
keyUsage = critical, keyCertSign, cRLSign
```

---

## Generate Root CA private key

```bash
openssl genrsa \
-out 01-root.org/private/root.org.key.pem \
4096
```

---

## Generate self-signed Root CA certificate

```bash
openssl req \
-x509 \
-new \
-nodes \
-key 01-root.org/private/root.org.key.pem \
-sha256 \
-days 3650 \
-out 01-root.org/certs/root.org.cert.pem \
-config 01-root.org/config/root-openssl.cnf
```

---

# STEP 2 — Create Intermediate CA (peddler.io)

---

## Create serial file

```bash
echo 2000 > 02-peddler.io/serial
```

---

## Create Intermediate CA config

Create file:

```text
02-peddler.io/config/peddler-openssl.cnf
```

```ini
[ req ]
default_bits = 4096
prompt = no
distinguished_name = dn
req_extensions = v3_intermediate_ca

[ dn ]
C = IN
ST = Maharashtra
L = Thane
O = peddler.io
OU = Intermediate Certificate Authority
CN = peddler.io Intermediate CA

[ v3_intermediate_ca ]
subjectKeyIdentifier = hash
basicConstraints = critical, CA:true, pathlen:0
keyUsage = critical, keyCertSign, cRLSign
```

---

## Generate Intermediate private key

```bash
openssl genrsa \
-out 02-peddler.io/private/peddler.io.key.pem \
4096
```

---

## Generate Intermediate CSR

```bash
openssl req \
-new \
-key 02-peddler.io/private/peddler.io.key.pem \
-out 02-peddler.io/csr/peddler.io.csr.pem \
-config 02-peddler.io/config/peddler-openssl.cnf
```

---

## Root CA signs Intermediate CSR

```bash
openssl x509 \
-req \
-in 02-peddler.io/csr/peddler.io.csr.pem \
-CA 01-root.org/certs/root.org.cert.pem \
-CAkey 01-root.org/private/root.org.key.pem \
-CAserial 01-root.org/serial \
-out 02-peddler.io/certs/peddler.io.cert.pem \
-days 1825 \
-sha256 \
-extfile 02-peddler.io/config/peddler-openssl.cnf \
-extensions v3_intermediate_ca
```

---

# STEP 3 — Create Web Server Certificate (webapp.litware.com)

---

## Create Web Server config

Create file:

```text
03-litware.com/config/webapp-openssl.cnf
```

```ini
[ req ]
default_bits = 2048
prompt = no
distinguished_name = dn
req_extensions = v3_req

[ dn ]
C = IN
ST = Maharashtra
L = Thane
O = Litware
OU = Web Applications
CN = webapp.litware.com

[ v3_req ]
subjectAltName = @alt_names
basicConstraints = critical, CA:false
keyUsage = critical, digitalSignature, keyEncipherment
extendedKeyUsage = serverAuth

[ alt_names ]
DNS.1 = webapp.litware.com
DNS.2 = www.webapp.litware.com
```

---

## Generate Web Server private key

```bash
openssl genrsa \
-out 03-litware.com/private/webapp.litware.com.key.pem \
2048
```

---

## Generate Web Server CSR

```bash
openssl req \
-new \
-key 03-litware.com/private/webapp.litware.com.key.pem \
-out 03-litware.com/csr/webapp.litware.com.csr.pem \
-config 03-litware.com/config/webapp-openssl.cnf
```

---

## Intermediate CA signs Web Server CSR

```bash
openssl x509 \
-req \
-in 03-litware.com/csr/webapp.litware.com.csr.pem \
-CA 02-peddler.io/certs/peddler.io.cert.pem \
-CAkey 02-peddler.io/private/peddler.io.key.pem \
-CAserial 02-peddler.io/serial \
-out 03-litware.com/certs/webapp.litware.com.cert.pem \
-days 825 \
-sha256 \
-extfile 03-litware.com/config/webapp-openssl.cnf \
-extensions v3_req
```

---

# STEP 4 — Build Certificate Chain

---

## Create full chain file

```bash
cat \
02-peddler.io/certs/peddler.io.cert.pem \
01-root.org/certs/root.org.cert.pem \
> chain/full-chain.pem
```

---

# STEP 5 — Verify Certificate Chain

---

## Verify Intermediate against Root

```bash
openssl verify \
-CAfile 01-root.org/certs/root.org.cert.pem \
02-peddler.io/certs/peddler.io.cert.pem
```

Expected:

```text
OK
```

---

## Verify Web Server certificate against full chain

```bash
openssl verify \
-CAfile 01-root.org/certs/root.org.cert.pem \
-untrusted 02-peddler.io/certs/peddler.io.cert.pem \
03-litware.com/certs/webapp.litware.com.cert.pem
```

Expected:

```text
OK
```

---

# STEP 6 — Inspect Certificates

---

## Root Certificate

```bash
openssl x509 \
-in 01-root.org/certs/root.org.cert.pem \
-text \
-noout
```

---

## Intermediate Certificate

```bash
openssl x509 \
-in 02-peddler.io/certs/peddler.io.cert.pem \
-text \
-noout
```

---

## Web Server Certificate

```bash
openssl x509 \
-in 03-litware.com/certs/webapp.litware.com.cert.pem \
-text \
-noout
```

---

# What This Demonstrates

| Entity             | Role                               |
| ------------------ | ---------------------------------- |
| root.org           | Root Certificate Authority         |
| peddler.io         | Intermediate Certificate Authority |
| webapp.litware.com | TLS Server Certificate             |

---

# Important PKI Concepts Demonstrated

| Concept           | Description                      |
| ----------------- | -------------------------------- |
| Root CA           | Self-signed trust anchor         |
| Intermediate CA   | Delegated certificate issuer     |
| CSR               | Certificate Signing Request      |
| Certificate Chain | Root → Intermediate → Server     |
| SAN               | Subject Alternative Name         |
| keyUsage          | Defines cryptographic operations |
| basicConstraints  | Defines CA vs non-CA             |
| extendedKeyUsage  | Defines TLS server auth          |

---

# Real World Analogy

```text
DigiCert Root CA
    └── DigiCert TLS Intermediate
            └── github.com certificate
```

Your demo mirrors how public PKI works on the internet.
