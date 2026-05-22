# OpenSSL RSA Basic Commands

## Introduction to RSA

RSA is an **asymmetric encryption algorithm** that uses:

* **Public Key** → shared openly
* **Private Key** → kept secret

Data encrypted with the **public key** can only be decrypted with the **private key**.

RSA is commonly used for:

* HTTPS / TLS certificates
* SSH authentication
* Digital signatures
* Secure key exchange

---

# Generate RSA Private Key

## Commands

```bash
# Default key size = 2048 bits
openssl genrsa -out private-key.pem

# Generate specific key lengths
openssl genrsa -out private-key-512.pem 512
openssl genrsa -out private-key-1024.pem 1024
openssl genrsa -out private-key-2048.pem 2048
openssl genrsa -out private-key-3072.pem 3072
openssl genrsa -out private-key-4096.pem 4096
openssl genrsa -out private-key-8192.pem 8192
openssl genrsa -out private-key-16384.pem 16384
```


---

## Command Explanation

Example:

```bash
openssl genrsa -out private-key-4096.pem 4096
```

| Part                        | Meaning                  |
| --------------------------- | ------------------------ |
| `openssl`                   | Launch OpenSSL utility   |
| `genrsa`                    | Generate RSA private key |
| `-out private-key-4096.pem` | Output file name         |
| `4096`                      | Key length in bits       |

---

## Key Size Recommendations

| Key Size | Usage                                 |
| -------- | ------------------------------------- |
| 512      | Insecure, testing only                |
| 1024     | Deprecated                            |
| 2048     | Minimum recommended                   |
| 3072     | Strong security                       |
| 4096     | Very strong, common for high security |
| 8192     | Extremely large and slow              |
| 16384    | Mostly experimental                   |

---

## Example Generated File

```text
-----BEGIN PRIVATE KEY-----
MIIEvQIBADANBgkqhkiG9w0BAQEFAASC...
...
-----END PRIVATE KEY-----
```

---

## BEGIN and END Markers Explained

These markers indicate the file contains **PEM encoded cryptographic data**.

| Marker                        | Meaning              |
| ----------------------------- | -------------------- |
| `-----BEGIN PRIVATE KEY-----` | Start of private key |
| `-----END PRIVATE KEY-----`   | End of private key   |

### Important Notes

* PEM format is Base64 encoded text.
* Easy to store and transfer.
* Widely used in Linux, HTTPS, SSH, Kubernetes, etc.

### Common PEM Types

| Marker                  | Meaning                     |
| ----------------------- | --------------------------- |
| `BEGIN PRIVATE KEY`     | PKCS#8 private key          |
| `BEGIN RSA PRIVATE KEY` | Traditional RSA private key |
| `BEGIN PUBLIC KEY`      | Public key                  |
| `BEGIN CERTIFICATE`     | X.509 certificate           |

---

# Generate RSA Public Key from Existing Private Key

## Commands

```bash
openssl rsa -in private-key-512.pem -pubout -out public-key-512.pub.pem

openssl rsa -in private-key-2048.pem -pubout -out public-key-2048.pub.pem

openssl rsa -in private-key-4096.pem -pubout -out public-key-4096.pub.pem
```

---

## Command Explanation

Example:

```bash
openssl rsa -in private-key-4096.pem -pubout -out public-key-4096.pub.pem
```

| Part                           | Meaning                    |
| ------------------------------ | -------------------------- |
| `openssl`                      | Launch OpenSSL             |
| `rsa`                          | RSA key processing utility |
| `-in private-key-4096.pem`     | Input private key          |
| `-pubout`                      | Extract public key         |
| `-out public-key-4096.pub.pem` | Output public key file     |

---

## Example Public Key File

```text
-----BEGIN PUBLIC KEY-----
MIICIjANBgkqhkiG9w0BAQEFAAOCAg8A...
...
-----END PUBLIC KEY-----
```

---

## BEGIN and END Markers for Public Key

| Marker             | Meaning             |
| ------------------ | ------------------- |
| `BEGIN PUBLIC KEY` | Start of public key |
| `END PUBLIC KEY`   | End of public key   |

The public key contains:

* Public exponent (`e`)
* Modulus (`n`)

But **does not contain**:

* Private exponent
* Prime numbers

---

# Encrypt using Public Key and Decrypt using Private Key

> For encryption, the **Public Key** is used.
> For decryption, the **Private Key** is used.

---

# Encrypt Plaintext File

## Command

```bash
openssl pkeyutl -encrypt \
  -inkey public-key-4096.pub.pem \
  -pubin \
  -in plaintext.txt \
  -out encrypted.bin
```

---

## Command Explanation

| Option                           | Meaning                      |
| -------------------------------- | ---------------------------- |
| `pkeyutl`                        | Public key utility           |
| `-encrypt`                       | Perform encryption           |
| `-inkey public-key-4096.pub.pem` | Key file to use              |
| `-pubin`                         | Input key is a public key    |
| `-in plaintext.txt`              | Input plaintext file         |
| `-out encrypted.bin`             | Output encrypted binary file |

---

## Important Notes

### 1. `-inkey` Must Point to Public Key

This is extremely important.

Correct:

```bash
-inkey public-key-4096.pub.pem
```

Wrong:

```bash
-inkey private-key-4096.pem
```

---

### 2. Encrypted File Size Becomes Large

Example:

| Original File   | Size     |
| --------------- | -------- |
| `plaintext.txt` | 57 Bytes |

| Encrypted File  | Size      |
| --------------- | --------- |
| `encrypted.bin` | 512 Bytes |

Why?

Because RSA encryption output size equals the RSA modulus size.

For a 4096-bit key:

4096\text{ bits} \div 8 = 512\text{ bytes}

---

## Downside of Asymmetric Encryption

RSA encryption is:

* Computationally expensive
* Slow
* Produces large encrypted output

Therefore RSA is usually **NOT** used to encrypt large files directly.

---

# Decrypt Ciphertext File

## Command

```bash
openssl pkeyutl -decrypt \
  -inkey private-key-4096.pem \
  -in encrypted.bin \
  -out decrypted.txt
```

---

## Command Explanation

| Option                        | Meaning               |
| ----------------------------- | --------------------- |
| `pkeyutl`                     | Public key utility    |
| `-decrypt`                    | Perform decryption    |
| `-inkey private-key-4096.pem` | Private key file      |
| `-in encrypted.bin`           | Encrypted input       |
| `-out decrypted.txt`          | Decrypted output file |

---

# Show RSA Internal Parameters

## Command

```bash
openssl rsa -text -in private-key-512.pem -noout
```

---

## Command Explanation

| Option                    | Meaning                         |
| ------------------------- | ------------------------------- |
| `rsa`                     | RSA utility                     |
| `-text`                   | Display detailed RSA parameters |
| `-in private-key-512.pem` | Input key                       |
| `-noout`                  | Do not print PEM encoded key    |

---

# Understanding RSA Components

The output shows the internal mathematics behind RSA.

---

## RSA Core Formula

RSA is based on:

n = p \times q

Where:

* `p` = first prime number
* `q` = second prime number
* `n` = modulus

---

## Terms Explained

### 1. modulus

```text
modulus:
```

This is:

n = p \times q

The modulus is part of both public and private keys.

---

### 2. publicExponent

```text
publicExponent: 65537
```

Usually:

e = 65537

This value is commonly used because:

* Fast
* Secure
* Industry standard

---

### 3. privateExponent

```text
privateExponent:
```

This is:

d

Used for decryption.

---

### 4. prime1 and prime2

```text
prime1:
prime2:
```

These are the two secret primes:

p, q

These must remain secret.

If attacker gets them → RSA is broken.

---

### 5. exponent1 and exponent2

Optimization values used for faster decryption using CRT (Chinese Remainder Theorem).

---

### 6. coefficient

Used internally by CRT optimization.

Improves RSA performance significantly.

---

# How RSA Works (Simple Example)

## Step 1: Generate Two Prime Numbers

Example:

p = 11,\quad q = 13

---

## Step 2: Compute Modulus

n = 11 \times 13 = 143

---

## Step 3: Choose Public Exponent

Example:

e = 7

---

## Step 4: Compute Private Exponent

Find `d` such that:

(d \times e) \bmod \phi(n) = 1

Where:

\phi(n) = (p-1)(q-1) = 120

Result:

d = 103

---

## Step 5: Encrypt Message

Ciphertext formula:

c = m^e \bmod n

---

## Step 6: Decrypt Message

Decryption formula:

m = c^d \bmod n

---

# RSA Disadvantages

## 1. Too Many Keys in Large Networks

In large systems:

* Every user/server needs key management.
* Public/private key distribution becomes complex.

---

## 2. Encryption and Decryption Are Slow

RSA operations involve:

* Large integer arithmetic
* Modular exponentiation
* Prime number mathematics

This is CPU intensive.

---

## 3. Large Ciphertext Size

Encrypted output can become much larger than original plaintext.

---

# Modern Approach Used Instead (Hybrid Encryption)

Modern systems combine:

* RSA
* Symmetric encryption (AES)

---

## Typical Flow

### Step 1

Generate random AES key.

### Step 2

Encrypt actual data using AES.

AES is:

* Very fast
* Efficient for large files

---

### Step 3

Encrypt AES key using RSA public key.

---

## Why This Is Better

| RSA                   | AES                      |
| --------------------- | ------------------------ |
| Slow                  | Fast                     |
| Expensive             | Efficient                |
| Good for key exchange | Good for bulk encryption |

---

# Real World Usage

HTTPS/TLS typically works like this:

1. RSA/ECDSA used for authentication and secure key exchange
2. AES used for actual data encryption

This gives both:

* Strong security
* High performance
