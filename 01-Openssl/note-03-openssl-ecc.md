# OpenSSL and Elliptic Curve Cryptography (ECC)

## What is ECC?

**Elliptic Curve Cryptography (ECC)** is a modern public-key cryptography method based on mathematical operations over elliptic curves.

Compared to RSA:

| Algorithm | Typical Key Size | Security Level      |
| --------- | ---------------- | ------------------- |
| RSA       | 2048-bit         | Standard            |
| ECC       | 256-bit          | Similar to RSA 3072 |
| ECC       | 384-bit          | Very High           |

ECC advantages:

* Smaller key sizes
* Faster computations
* Lower CPU and memory usage
* Commonly used in:

  * TLS/HTTPS certificates
  * SSH keys
  * JWT signing
  * Mobile and IoT devices
  * Blockchain systems (e.g. Bitcoin uses `secp256k1`)

---

# OpenSSL EC Parameters

OpenSSL stores predefined elliptic curves called **EC parameters**.

These curves define:

* Mathematical equation
* Generator point
* Prime field
* Security characteristics

Common curves:

| Curve        | Alias         | Notes              |
| ------------ | ------------- | ------------------ |
| `prime256v1` | NIST P-256    | Most commonly used |
| `secp384r1`  | NIST P-384    | Higher security    |
| `secp521r1`  | NIST P-521    | Very high security |
| `secp256k1`  | Bitcoin curve | Used in blockchain |

---

# List Available EC Curves

## Command

```bash
openssl ecparam -list_curves
```

## Example Output

```text
secp112r1 : SECG/WTLS curve over a 112 bit prime field
secp112r2 : SECG curve over a 112 bit prime field
secp128r1 : SECG curve over a 128 bit prime field
secp128r2 : SECG curve over a 128 bit prime field
secp160k1 : SECG curve over a 160 bit prime field
secp160r1 : SECG curve over a 160 bit prime field
secp160r2 : SECG/WTLS curve over a 160 bit prime field
secp192k1 : SECG curve over a 192 bit prime field
secp224k1 : SECG curve over a 224 bit prime field
secp224r1 : NIST/SECG curve over a 224 bit prime field
secp256k1 : SECG curve over a 256 bit prime field
secp384r1 : NIST/SECG curve over a 384 bit prime field
secp521r1 : NIST/SECG curve over a 521 bit prime field
prime192v1: NIST/X9.62/SECG curve over a 192 bit prime field
prime256v1: X9.62/SECG curve over a 256 bit prime field
...
```

---

# Generate ECC Private Key

## Commands

```bash
openssl ecparam -genkey -name prime256v1 -out private-key-ecc-prime256v1.pem
```

```bash
openssl ecparam -genkey -name prime256v1 -out private-key-ecc-prime256v1.pem -noout
```

---

## Parameter Explanation

| Parameter          | Meaning                                    |
| ------------------ | ------------------------------------------ |
| `ecparam`          | OpenSSL utility for EC parameters and keys |
| `-genkey`          | Generate EC private key                    |
| `-name prime256v1` | Use the `prime256v1` curve                 |
| `-out file.pem`    | Output file name                           |
| `-noout`           | Do not print EC parameters to output       |

---

## About `prime256v1`

`prime256v1` is:

* NIST P-256 curve
* 256-bit ECC curve
* Most widely supported ECC curve
* Commonly used in HTTPS certificates

Equivalent names:

| Name         | Alias        |
| ------------ | ------------ |
| `prime256v1` | OpenSSL name |
| `secp256r1`  | SECG name    |
| `P-256`      | NIST name    |

---

# ECC Private Key PEM Format

Generated file:

```text
private-key-ecc-prime256v1.pem
```

Typical contents:

```pem
-----BEGIN EC PRIVATE KEY-----
MHcCAQEEIDuPlwieyNmqI+6ecpe66Pz6H2cYJ0gtvFBP9Xa5T25UoAoGCCqGSM49
AwEHoUQDQgAEE8yyfjMVnge0Ff7QmTAmKijwQC+HF3MHkonFaPdMmkI5qi1lHQtz
PT/esges1ai77B8qA/7+TBdroxXSI1+DJA==
-----END EC PRIVATE KEY-----
```

---

## PEM Demarkers

| Marker                 | Meaning                 |
| ---------------------- | ----------------------- |
| `BEGIN EC PRIVATE KEY` | Start of EC private key |
| `END EC PRIVATE KEY`   | End of EC private key   |

The content between markers is:

* Base64 encoded
* ASN.1 DER formatted binary data

---

# Generate ECC Public Key

## Command

```bash
openssl ec -in private-key-ecc-prime256v1.pem -pubout -out public-key-ecc-prime256v1.pub.pem
```

---

## Parameter Explanation

| Parameter       | Meaning                |
| --------------- | ---------------------- |
| `ec`            | OpenSSL EC key utility |
| `-in file.pem`  | Input private key      |
| `-pubout`       | Extract public key     |
| `-out file.pem` | Output public key file |

---

# ECC Public Key PEM Format

Generated file:

```text
public-key-ecc-prime256v1.pub.pem
```

Typical contents:

```pem
-----BEGIN PUBLIC KEY-----
MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEE8yyfjMVnge0Ff7QmTAmKijwQC+H
F3MHkonFaPdMmkI5qi1lHQtzPT/esges1ai77B8qA/7+TBdroxXSI1+DJA==
-----END PUBLIC KEY-----
```

---

## PEM Demarkers

| Marker             | Meaning             |
| ------------------ | ------------------- |
| `BEGIN PUBLIC KEY` | Start of public key |
| `END PUBLIC KEY`   | End of public key   |

> Note: Public keys use generic `PUBLIC KEY` markers, not `EC PUBLIC KEY`.

---

# View ECC Key in Binary/Text Format

## Command

```bash
openssl ec -in private-key-ecc-prime256v1.pem -text -noout
```

---

## Example Output

```text
read EC key
Private-Key: (256 bit)
priv:
    3b:8f:97:08:9e:c8:d9:aa:23:ee:9e:72:97:ba:e8:
    fc:fa:1f:67:18:27:48:2d:bc:50:4f:f5:76:b9:4f:
    6e:54
pub:
    04:13:cc:b2:7e:33:15:9e:07:b4:15:fe:d0:99:30:
    26:2a:28:f0:40:2f:87:17:73:07:92:89:c5:68:f7:
    4c:9a:42:39:aa:2d:65:1d:0b:73:3d:3f:de:b2:07:
    ac:d5:a8:bb:ec:1f:2a:03:fe:fe:4c:17:6b:a3:5c:
    d2:27:5f:83:24
ASN1 OID: prime256v1
NIST CURVE: P-256
```

---

# Parameter Explanation

| Parameter      | Meaning                       |
| -------------- | ----------------------------- |
| `-in file.pem` | Input EC private key          |
| `-text`        | Display decoded key contents  |
| `-noout`       | Do not output PEM encoded key |

---

# Output Explanation

## `Private-Key: (256 bit)`

Indicates:

* ECC private key size
* Based on selected curve size

---

## `priv:`

The actual private key value in hexadecimal format.

Example:

```text
3b:8f:97:08:...
```

This value must remain secret.

---

## `pub:`

The derived public key in hexadecimal format.

Example:

```text
04:13:cc:b2:...
```

### Why does it start with `04`?

`04` indicates:

* Uncompressed EC point format

Public key contains:

* X coordinate
* Y coordinate

Both are stored after the `04`.

---

## `ASN1 OID: prime256v1`

ASN.1 Object Identifier representing the curve.

---

## `NIST CURVE: P-256`

Human-friendly NIST name of the curve.

Equivalent to:

```text
prime256v1
secp256r1
P-256
```

---

# Useful Additional Commands

## Extract Only Public Key Text

```bash
openssl ec -pubin -in public-key-ecc-prime256v1.pub.pem -text -noout
```

---

## Convert PEM to DER

### Private Key

```bash
openssl ec -in private-key-ecc-prime256v1.pem -outform DER -out private-key.der
```

### Public Key

```bash
openssl ec -pubin -in public-key-ecc-prime256v1.pub.pem -outform DER -out public-key.der
```

---

# ECC vs RSA Quick Comparison

| Feature          | ECC         | RSA               |
| ---------------- | ----------- | ----------------- |
| Key Size         | Smaller     | Larger            |
| Speed            | Faster      | Slower            |
| CPU Usage        | Lower       | Higher            |
| Security per Bit | Higher      | Lower             |
| Common Today     | Very Common | Still Widely Used |

---

# Commonly Used ECC Curves

| Curve        | Security         | Usage                   |
| ------------ | ---------------- | ----------------------- |
| `prime256v1` | Strong           | Default choice          |
| `secp384r1`  | Very Strong      | Enterprise / Compliance |
| `secp521r1`  | Extremely Strong | Specialized systems     |
| `secp256k1`  | Strong           | Cryptocurrency          |
