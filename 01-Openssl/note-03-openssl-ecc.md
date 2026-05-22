# OpenSSL and Elliptic Curver Cryptography 


## Get list of EC : 
```
openssl ecparam -list_curves
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
  prime192v2: X9.62 curve over a 192 bit prime field
  prime192v3: X9.62 curve over a 192 bit prime field
  prime239v1: X9.62 curve over a 239 bit prime field
  prime239v2: X9.62 curve over a 239 bit prime field
  prime239v3: X9.62 curve over a 239 bit prime field
  prime256v1: X9.62/SECG curve over a 256 bit prime field
  sect113r1 : SECG curve over a 113 bit binary field
  sect113r2 : SECG curve over a 113 bit binary field
  sect131r1 : SECG/WTLS curve over a 131 bit binary field
...

```


Ask chat gpt to:
- add explanation of ecc
- openssl ecc params 

## Generate Private Key 

openssl ecparam -genkey -name prime256v1 -out private-key-ecc-prime256v1.pem
openssl ecparam -genkey -name prime256v1 -out private-key-ecc-prime256v1.pem -noout

Ask chat gpt to:
- add explanation of each params 
- file demarkers  : BEGIN EC PRIVATE KEY, END EC PRIVATE KEY
 

## View binary format 

openssl ec -in private-key-ecc-prime256v1.pem -text -noout 
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

Ask chat gpt to:
- add explanation of each params 