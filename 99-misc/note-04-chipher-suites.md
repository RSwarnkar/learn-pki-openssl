# What is a cipher suite? 

Cipher suites are predefined combinations of cryptographic algorithms used by protocols like OpenSSL and TLS/SSL to secure network communication.
A cipher suite typically defines:

* Key exchange algorithm
* Authentication algorithm
* Encryption algorithm
* Message authentication / integrity algorithm
* TLS version compatibility

---

# Common TLS Cipher Suites

## Modern TLS 1.3 Cipher Suites

TLS 1.3 simplified cipher suites heavily.

| Cipher Suite                   | Encryption        | Hash   |
| ------------------------------ | ----------------- | ------ |
| `TLS_AES_128_GCM_SHA256`       | AES-128-GCM       | SHA256 |
| `TLS_AES_256_GCM_SHA384`       | AES-256-GCM       | SHA384 |
| `TLS_CHACHA20_POLY1305_SHA256` | ChaCha20-Poly1305 | SHA256 |
| `TLS_AES_128_CCM_SHA256`       | AES-128-CCM       | SHA256 |
| `TLS_AES_128_CCM_8_SHA256`     | AES-128-CCM-8     | SHA256 |

---

# Common TLS 1.2 Cipher Suites

## RSA-based

| Cipher Suite                      | Meaning                              |
| --------------------------------- | ------------------------------------ |
| `TLS_RSA_WITH_AES_128_CBC_SHA`    | RSA key exchange + AES128 CBC + SHA1 |
| `TLS_RSA_WITH_AES_256_CBC_SHA`    | RSA + AES256 CBC + SHA1              |
| `TLS_RSA_WITH_AES_128_GCM_SHA256` | RSA + AES128 GCM + SHA256            |
| `TLS_RSA_WITH_AES_256_GCM_SHA384` | RSA + AES256 GCM + SHA384            |

---

## ECDHE (Preferred Modern Suites)

These provide **Forward Secrecy**.

| Cipher Suite                                    | Meaning                       |
| ----------------------------------------------- | ----------------------------- |
| `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`         | ECDHE + RSA cert + AES128 GCM |
| `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`         | ECDHE + RSA cert + AES256 GCM |
| `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`       | ECDHE + ECDSA cert            |
| `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`       | ECDHE + ECDSA cert            |
| `TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256`   | ECDHE + RSA + ChaCha20        |
| `TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256` | ECDHE + ECDSA + ChaCha20      |

---

# Older / Deprecated Cipher Suites

These are generally considered insecure now.

| Cipher Suite                    | Problem         |
| ------------------------------- | --------------- |
| `TLS_RSA_WITH_3DES_EDE_CBC_SHA` | Weak 3DES       |
| `TLS_RSA_WITH_RC4_128_SHA`      | RC4 broken      |
| `TLS_DHE_RSA_WITH_DES_CBC_SHA`  | DES insecure    |
| `TLS_RSA_WITH_NULL_SHA`         | No encryption   |
| `SSL_RSA_WITH_RC4_128_MD5`      | SSL + RC4 + MD5 |

---

# Structure of a Cipher Suite Name

Example:

```text
TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
```

Breakdown:

| Part          | Meaning                                 |
| ------------- | --------------------------------------- |
| `TLS`         | TLS protocol                            |
| `ECDHE`       | Ephemeral Elliptic Curve Diffie-Hellman |
| `RSA`         | RSA certificate/authentication          |
| `AES_256_GCM` | Encryption algorithm                    |
| `SHA384`      | Hash/integrity algorithm                |

---

# List Cipher Suites Using OpenSSL

## Show all supported cipher suites

```bash
openssl ciphers
```

---

## Show verbose details

```bash
openssl ciphers -v
```

---

## Show only TLS 1.3 suites

```bash
openssl ciphers -v -tls1_3
```

---

## Show strong suites only

```bash
openssl ciphers 'HIGH'
```

---

# Recommended Modern Cipher Suites

For modern servers:

## TLS 1.3

```text
TLS_AES_256_GCM_SHA384
TLS_CHACHA20_POLY1305_SHA256
TLS_AES_128_GCM_SHA256
```

---

## TLS 1.2

```text
ECDHE-ECDSA-AES256-GCM-SHA384
ECDHE-RSA-AES256-GCM-SHA384
ECDHE-ECDSA-CHACHA20-POLY1305
ECDHE-RSA-CHACHA20-POLY1305
```

---

# Important Concepts

## Forward Secrecy

Provided by:

* `ECDHE`
* `DHE`

This ensures:

> Even if the server private key is compromised later, old traffic cannot be decrypted.

---

## AEAD Ciphers (Preferred)

Modern authenticated encryption:

* AES-GCM
* ChaCha20-Poly1305

Avoid:

* CBC mode ciphers
* RC4
* DES
* 3DES

---

# View Cipher Used By Website

```bash
openssl s_client -connect example.com:443
```

Look for:

```text
Cipher    : TLS_AES_256_GCM_SHA384
```

---

# Real-World Example

When your browser connects to:

* Google
* Microsoft
* Cloudflare

They usually negotiate:

* TLS 1.3
* AES-GCM or ChaCha20
* ECDHE key exchange

for high performance and strong security.
