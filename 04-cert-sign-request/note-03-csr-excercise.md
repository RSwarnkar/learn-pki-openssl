
# CSR with internal Certificate Authorities:

fabrikam.com is company with a root CA. There is sales deplartment which wants to host sub-domain with HTTPS app. They want to sign a certificate with internal root CA. 


First create a private key at intermediate CA side: 


openssl genrsa -out private-key-sales.fabrikam.com-rsa-4096.pem 4096 
# OR 
openssl ecparam -genkey -name prime256v1 -out private-key-sales.fabrikam.com-ecc-prime256v1.pem -noout



Then generate the CSR request: using `openssl req` command

openssl req -key private-key-sales.fabrikam.com-rsa-4096.pem -new -out csr-rsa.pem -config sales.fabrikam.com.conf

openssl req -key private-key-sales.fabrikam.com-ecc-prime256v1.pem -new -out csr-ecc.pem -config sales.fabrikam.com.conf


Next, go to website https://certlogik.com/decoder and inspect csr-ecc.pem or csr-rsa.pem

or check using `openssl req`

openssl req -in csr-ecc.pem -text 
openssl req -in csr-rsa.pem -text 


Notice the output below. 

```
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
This is the exact stuff we need to send to Root CA for CSR signing.
The sales department can zip this with some secret to prevent any modification. 


## At Root CA Side:

Root CA will first check the CSR file. and the content of the CSR using `openssl req`. 

```
openssl req -in csr-ecc.pem -text 
openssl req -in csr-rsa.pem -text 
```

And then they will generate signed certificate using their private key and sned back the signed certificate 

