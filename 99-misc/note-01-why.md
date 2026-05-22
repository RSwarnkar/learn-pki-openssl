# WHY does all Operating syatem have key store for certificates?

Because operating systems need a way to establish **trust** between computers, websites, software, and users.

At the core, certificates solve this problem:

> “How can my computer know that the thing on the other side is really who it claims to be?”

The OS trust store is the answer.

---

# The Fundamental Problem

Imagine you visit:

```text id="wk06f8"
https://bank.com
```

Your computer connects to *some* server on the internet.

But:

* Internet traffic can be intercepted
* DNS can be spoofed
* Attackers can impersonate servers
* Anyone can generate a fake certificate locally

So how does your laptop know:

> “This is REALLY Bank.com and not an attacker?”

It needs a list of trusted authorities.

That list is the certificate trust store.

---

# Why the OS Specifically Maintains It

The operating system sits underneath:

* Browsers
* VPN clients
* Email clients
* Update systems
* Enterprise authentication
* Wi-Fi authentication
* APIs
* Containers
* Cloud agents

All of these need secure communication.

Instead of every application inventing its own trust system, the OS provides a centralized trust mechanism.

---

# Real-World Analogy

Think of passports.

Your country trusts passports issued by certain governments.

Why?
Because there is a trusted authority behind them.

Similarly:

* A certificate says “I am google.com”
* A Certificate Authority (CA) signs it
* Your OS says:

  > “I trust this CA, therefore I trust this certificate”

Without the trusted CA list, certificates are meaningless text files.

---

# Why Self-Signed Certificates Are Not Trusted

Anyone can create this in 5 seconds:

```bash id="w2e8n9"
openssl req -x509 -newkey rsa:2048
```

Now you have a certificate claiming anything.

So the important thing is NOT the certificate itself.

The important thing is:

> WHO signed it.

The OS trust store contains the trusted signers.

---

# What Happens During HTTPS

When you open a website:

1. Server sends certificate
2. Browser/OS checks signature chain
3. Chain ends at a trusted root CA
4. OS says:

   > “I trust DigiCert / Let's Encrypt / GlobalSign”
5. Connection becomes trusted

If the root CA is not trusted:

* Browser shows warning
* TLS may fail
* APIs reject connection

---

# Why This Is Critical Today

Without OS certificate stores:

* HTTPS would not scale globally
* Every app would need manual trust config
* Software updates would be dangerous
* VPNs would be insecure
* Enterprise authentication would break
* Secure APIs would become painful

The internet at modern scale depends heavily on this trust infrastructure.

---

# Why Operating Systems Ship With Hundreds of Certificates

Ubuntu, Windows, macOS, Android all come with many trusted root CAs preinstalled.

Because users need websites to “just work.”

If no root certs existed:

* Every HTTPS site would show warnings
* Users would manually install trust for every website

Impossible at internet scale.

---

# Deeper Security Insight

The trust store is effectively:

> “The operating system’s list of entities allowed to prove identities cryptographically.”

That is enormous power.

If a trusted CA is compromised:

* Fake certificates can be issued
* HTTPS interception becomes possible
* Massive security incidents can occur

This has happened before.

Example:

* DigiNotar certificate authority breach

---

# Why Enterprises Add Their Own Certificates

Companies often install internal root CAs to:

* Inspect HTTPS traffic
* Issue internal certificates
* Secure internal apps
* Enable smart cards
* Authenticate devices

That’s why corporate laptops often trust additional certificates.

---

# Simple One-Line Summary

Operating systems maintain certificate trust stores because computers need a secure, scalable way to decide:

> “Which digital identities on the internet should I trust?”


# How and where to fine the list of certificates stored on Widnows, Linux, Mac ?

# Windows

Windows stores certificates in a centralized **Certificate Store**.

## GUI Method

Open Run:

```powershell id="tlnb8j"
certmgr.msc
```

This opens:

* Personal certificates
* Trusted Root CAs
* Intermediate CAs
* Enterprise certs

For machine-wide certificates:

```powershell id="e5skca"
certlm.msc
```

---

## PowerShell

### Current User Root Certificates

```powershell id="8av9j4"
Get-ChildItem Cert:\CurrentUser\Root
```

### Machine Root Certificates

```powershell id="q0vqgn"
Get-ChildItem Cert:\LocalMachine\Root
```

### Personal Certificates

```powershell id="6a7fie"
Get-ChildItem Cert:\CurrentUser\My
```

### List with Expiry Dates

```powershell id="0ce9qt"
Get-ChildItem Cert:\LocalMachine\Root | 
Select Subject, Issuer, NotAfter
```

---

## Physical Storage Location

Windows stores them internally in registry/system stores.

Some locations:

```text id="k6a4g5"
HKEY_CURRENT_USER\Software\Microsoft\SystemCertificates
HKEY_LOCAL_MACHINE\Software\Microsoft\SystemCertificates
```

But normally you use:

* MMC
* PowerShell
* Crypto APIs

instead of directly browsing registry.

---

# Linux (Ubuntu/Debian)

Linux usually stores certificates as files.

## Trusted Root Certificates

```bash id="17u4v3"
/etc/ssl/certs
```

List:

```bash id="7f8g5x"
ls -l /etc/ssl/certs
```

---

## Combined CA Bundle

```bash id="r8nxce"
/etc/ssl/certs/ca-certificates.crt
```

View:

```bash id="9m4smj"
less /etc/ssl/certs/ca-certificates.crt
```

Count certs:

```bash id="0x7gyy"
grep -c "BEGIN CERTIFICATE" /etc/ssl/certs/ca-certificates.crt
```

---

## Custom Added Certificates

```bash id="mo8tzl"
/usr/local/share/ca-certificates
```

---

## RHEL/CentOS/Fedora

Locations may be:

```bash id="y5g5mq"
/etc/pki/tls/certs
/etc/pki/ca-trust
```

---

## Inspect Certificate

```bash id="s1ty2p"
openssl x509 -in cert.pem -text -noout
```

---

# macOS

macOS uses the **Keychain** system.

---

## GUI Method

Open:

* Applications
* Utilities
* Keychain Access

You’ll see:

* Login keychain
* System keychain
* System Roots

---

## Terminal Commands

### List Certificates

```bash id="x6d71j"
security find-certificate -a
```

---

### List System Root Certificates

```bash id="zt2hm0"
security find-certificate -a /System/Library/Keychains/SystemRootCertificates.keychain
```

---

### Export Certificate Details

```bash id="f6j3tl"
security find-certificate -p
```

---

# Android (Bonus)

System certs:

```bash id="f6lm9f"
/system/etc/security/cacerts
```

Usually requires root access.

---

# Java Trust Store (Works on All OS)

Java often uses its own keystore.

Default:

```text id="k0x5f5"
$JAVA_HOME/lib/security/cacerts
```

List:

```bash id="f20sbh"
keytool -list -cacerts
```

---

# Quick Comparison

| OS      | Main Store Type               | Typical Location               |
| ------- | ----------------------------- | ------------------------------ |
| Windows | Centralized certificate store | MMC / Registry                 |
| Linux   | File-based PEM store          | `/etc/ssl/certs`               |
| macOS   | Keychain                      | Keychain Access                |
| Android | System CA store               | `/system/etc/security/cacerts` |
| Java    | Java keystore                 | `cacerts`                      |

---

# Important Thing to Understand

Many applications do NOT necessarily use the OS trust store.

Examples:

* Firefox may use its own store
* Java uses `cacerts`
* Python may use `certifi`
* Docker may use separate cert paths

So:

> “Certificate exists in OS but app still fails”

…is extremely common in enterprise environments.
