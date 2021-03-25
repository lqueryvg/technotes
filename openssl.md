# OpenSSL

```
name=fred
openssl genrsa -out ${name}.priv 2048      # PEM
openssl rsa -in ${name}.priv -pubout > ${name}.pub
openssl x509 -in certificate.crt -text -noout     # show cert details
```

Checking a private key and cert match via the modulus:

```
openssl rsa -in priv.pem -noout -modulus | openssl md5 -sha1
openssl x509 -in prod.cer -noout -modulus | openssl md5 -sha1
```
