# OpenSSL

```
name=fred
openssl genrsa -out ${name}.priv 2048      # PEM
openssl rsa -in ${name}.priv -pubout > ${name}.pub
```
