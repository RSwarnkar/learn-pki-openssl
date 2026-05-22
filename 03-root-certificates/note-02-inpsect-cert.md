# OpenSSL Certificate Conversion and Inspection

## Convert `.pem` Certificate to DER Format

Generate a self-signed ECC certificate from an existing private key:

```bash
openssl req -x509 -sha256 -days 365 \
  -key private-key-ecc-prime256v1.pem \
  -out ecc-cert.pem
```

Convert the certificate from PEM format to DER format:

```bash
openssl x509 -outform der \
  -in ecc-cert.pem \
  -out ecc-cert.der
```

### View Certificate in Windows GUI

1. Locate the generated `ecc-cert.der` file.
2. Double-click the file in Windows.
3. The Windows Certificate Viewer (Certificate Wizard) will open.
4. You can inspect:

   * Subject
   * Issuer
   * Validity dates
   * Public key information
   * Signature algorithm
   * Thumbprints
   * Extensions

---

# Inspect Certificates Using OpenSSL

## Export a Certificate from Browser

Example workflow:

1. Open `github.com` in your browser.
2. Click the padlock icon near the address bar.
3. View the certificate.
4. Export/save the certificate as a `.der` file (for example: `github.der`).

You can also double-click the `.der` file on Windows to inspect it visually.

Or you can use some websites like :
- [certlogik](https://certlogik.com/decoder)
- [Godaddy SSLtool](https://ssltools.godaddy.com/views/certDecoder)

---

## Display DER Certificate Details in OpenSSL

Use the following command to inspect the certificate contents:

```bash
openssl x509 -inform der \
  -in github.der \
  -text -noout
```

### Explanation of Parameters

| Parameter        | Meaning                                      |
| ---------------- | -------------------------------------------- |
| `-inform der`    | Input certificate format is DER              |
| `-in github.der` | Input certificate file                       |
| `-text`          | Display human-readable certificate details   |
| `-noout`         | Do not output the encoded certificate itself |

---

# Common Certificate Fields Explained

When viewing a certificate, you will commonly see these fields:

| Field                          | Description                                            |
| ------------------------------ | ------------------------------------------------------ |
| Version                        | X.509 certificate version                              |
| Serial Number                  | Unique identifier assigned by CA                       |
| Signature Algorithm            | Algorithm used to sign the certificate                 |
| Issuer                         | Certificate Authority (CA) that issued the certificate |
| Valid From / To                | Certificate validity period                            |
| Subject                        | Entity the certificate belongs to                      |
| Public Key Info                | Public key and algorithm details                       |
| Extensions                     | Additional metadata and capabilities                   |
| Key Usage                      | Allowed usages like signing or encryption              |
| Subject Alternative Name (SAN) | Additional DNS names/domains                           |
| Basic Constraints              | Whether the certificate is a CA                        |
| Thumbprint / Fingerprint       | Hash identifier of certificate                         |

---

# Useful OpenSSL Commands

## Display PEM Certificate Details

```bash
openssl x509 -in ecc-cert.pem -text -noout
```

## Display Only Subject

```bash
openssl x509 -in ecc-cert.pem -subject -noout
```

## Display Only Issuer

```bash
openssl x509 -in ecc-cert.pem -issuer -noout
```

## Display Certificate Expiry

```bash
openssl x509 -in ecc-cert.pem -dates -noout
```

## Display Certificate Fingerprint

```bash
openssl x509 -in ecc-cert.pem -fingerprint -noout
```

---

# PEM vs DER

| Format | Description                     | Encoding |
| ------ | ------------------------------- | -------- |
| PEM    | Base64 encoded text certificate | Text     |
| DER    | Binary encoded certificate      | Binary   |

### PEM Example

```text
-----BEGIN CERTIFICATE-----
MIIB...
-----END CERTIFICATE-----
```

### DER Example

DER files are binary and cannot be read directly in a text editor.

---

# Typical File Extensions

| Extension       | Common Usage                          |
| --------------- | ------------------------------------- |
| `.pem`          | PEM encoded certificate/key           |
| `.crt`          | Certificate                           |
| `.cer`          | Certificate                           |
| `.der`          | DER encoded certificate               |
| `.key`          | Private key                           |
| `.csr`          | Certificate Signing Request           |
| `.pfx` / `.p12` | PKCS#12 archive containing cert + key |
| `.p7b`          | PKCS#7 certificate chain bundle       |

---

# Notes

* PEM and DER usually contain the same certificate data in different encodings.
* Windows GUI tools commonly work well with `.der`, `.cer`, and `.pfx`.
* Linux/OpenSSL workflows usually prefer `.pem`.
* Browsers and operating systems maintain trusted root certificate stores internally.


