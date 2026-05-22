
# Generate Self-signed Certificate 

Lets use eliptic curve private key to generate a self signed root cert


openssl genrsa -out private-key-rsa-2048.pem 2048 # OR 

openssl ecparam -genkey -name prime256v1 -out private-key-ecc-prime256v1.pem


openssl req -x509 -sha256 -days 365 -key private-key-ecc-prime256v1.pem -out ecc-cert.pem

$> openssl req -x509 -sha256 -days 365 -key private-key-ecc-prime256v1.pem -out ecc-cert.pem
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:IN
State or Province Name (full name) [Some-State]:Maharashtra
Locality Name (eg, city) []:Thane   
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Litware Inc
Organizational Unit Name (eg, section) []:IT
Common Name (e.g. server FQDN or YOUR name) []:litwareapi.com
Email Address []:admin@litwareapi.com


# View Certificate using Openssl :

```bash
$ openssl x509 -in ecc-cert.pem -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            29:9f:d2:62:c9:d1:d2:f3:f5:19:22:49:07:05:e4:0c:73:69:b7:9d
        Signature Algorithm: ecdsa-with-SHA256
        Issuer: C = IN, ST = Maharashtra, L = Thane, O = Litware Inc, OU = IT, CN = litwareapi.com, emailAddress = admin@litwareapi.com
        Validity
            Not Before: May 22 07:25:12 2026 GMT
            Not After : May 22 07:25:12 2027 GMT
        Subject: C = IN, ST = Maharashtra, L = Thane, O = Litware Inc, OU = IT, CN = litwareapi.com, emailAddress = admin@litwareapi.com
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
                Public-Key: (256 bit)
                pub:
                    04:bd:c3:f6:3c:d7:5b:ea:23:36:72:e5:8c:2d:ec:
                    ab:7b:ab:03:98:fc:4a:49:c2:e8:58:df:1c:29:65:
                    10:43:6f:06:7b:8b:1e:8a:5d:08:5d:b1:57:ac:fc:
                    10:1e:ca:30:37:0c:c9:4a:79:47:73:22:09:7b:d6:
                    f3:e7:2c:b3:d0
                ASN1 OID: prime256v1
                NIST CURVE: P-256
        X509v3 extensions:
            X509v3 Subject Key Identifier: 
                01:C3:22:D3:60:2D:34:31:3E:D3:E5:E7:04:AE:19:70:25:E2:6D:D8
            X509v3 Authority Key Identifier: 
                01:C3:22:D3:60:2D:34:31:3E:D3:E5:E7:04:AE:19:70:25:E2:6D:D8
            X509v3 Basic Constraints: critical
                CA:TRUE
    Signature Algorithm: ecdsa-with-SHA256
    Signature Value:
        30:46:02:21:00:a7:0d:2d:4a:45:10:86:3c:25:a8:49:94:2c:
        ce:dc:90:e4:45:ca:22:67:a0:36:e7:ca:66:52:cd:db:c8:e3:
        83:02:21:00:87:be:60:43:ec:dd:33:1b:6e:38:a5:e8:a3:e7:
        ba:17:e7:4e:3e:a4:d6:2d:55:b8:56:d1:ac:99:bd:f4:82:f5


```

Here you noticed, value of `X509v3 Subject Key Identifier` is same as `X509v3 Authority Key Identifier`. This means that this is self-signed certificate.
