
# OpenSSL Certificate Generation Using a Config File

## Generate a Self-Signed Certificate Using an OpenSSL Config File

```bash
openssl req -x509 -days 365 \
  -key private-key-512.pem \
  -config coolware.conf \
  -out coolware-cert.pem
```

---

# What This Command Does

This command generates a **self-signed X.509 certificate** using:

* An existing private key
* An OpenSSL configuration file
* A validity period of 365 days

The generated certificate is saved as:

```text
coolware-cert.pem
```

---

# Command Breakdown

| Option                     | Meaning                                                           |
| -------------------------- | ----------------------------------------------------------------- |
| `req`                      | Starts the certificate request and certificate generation utility |
| `-x509`                    | Generates a self-signed X.509 certificate instead of a CSR        |
| `-days 365`                | Certificate validity period                                       |
| `-key private-key-512.pem` | Private key used to sign the certificate                          |
| `-config coolware.conf`    | OpenSSL configuration file containing certificate details         |
| `-out coolware-cert.pem`   | Output certificate file                                           |

---

# Example `coolware.conf`

```ini
[ req ]
default_bits       = 2048
prompt             = no
default_md         = sha256
distinguished_name = dn
x509_extensions    = v3_req

[ dn ]
C  = IN
ST = Delhi
L  = New Delhi
O  = CoolWare
OU = Engineering
CN = coolware.local

[ v3_req ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = coolware.local
DNS.2 = api.coolware.local
```

---

# Why Use a Config File?

Using a config file allows you to:

* Automate certificate generation
* Avoid interactive prompts
* Add extensions like:

  * Subject Alternative Names (SAN)
  * Key Usage
  * Extended Key Usage
* Reuse standard certificate templates

This is extremely common in:

* Enterprise PKI
* Kubernetes
* DevOps pipelines
* Internal TLS setups
* Automation scripts

---

# Inspect the Generated Certificate

```bash
openssl x509 -in coolware-cert.pem -text -noout
```

---

# Convert PEM Certificate to DER Format

```bash
openssl x509 -outform der \
  -in coolware-cert.pem \
  -out coolware-cert.der
```

You can then open the `.der` file directly in Windows Certificate Viewer.

---

# Important Note About 512-bit RSA

```text
private-key-512.pem
```

A 512-bit RSA key is considered insecure today and should only be used for:

* Learning
* Testing
* Demonstrations

Recommended minimum sizes today:

| Usage | Recommended Size       |
| ----- | ---------------------- |
| RSA   | 2048 or 3072           |
| ECC   | prime256v1 / secp384r1 |

---

# Typical Flow

```text
Private Key
    ↓
OpenSSL Config File
    ↓
Self-Signed Certificate
    ↓
TLS / HTTPS / mTLS Usage
```
