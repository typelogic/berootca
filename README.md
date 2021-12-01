# Root CA Setup
```
mkdir -p root-ca/{conf,private,public}
chmod 400 root-ca/private
cd root-ca/
vi conf/openssl.cnf
openssl req -nodes -config conf/openssl.cnf -days 1825 -x509 -newkey rsa:1024 -out public/root.pem -outform PEM
vi conf/openssl.cnf 
vi conf/openssl.cnf 
mkdir signed-keys
echo "01" > conf/serial
touch conf/index
```

# Create a CSR
```
cd root-ca
openssl req -nodes -newkey rsa:2048 -keyout request/server.key -out request/server.csr -subj "/C=PH/ST=Cebu/L=New Jersey/O=Decahomes Prime/OU=Test 2/CN=server.com"
```

Here, the following fields must match between a CSR and root CA:
- C=PH
- ST=Cebu
- O=Decahomes Prime

Otherwise, the root CA cannot sign.

# Sign a CSR
```
cd root-ca
openssl ca -batch -config conf/openssl.cnf -in ../request/server.csr 
```

# Revoke the certificate
```
cd root-ca
openssl ca -config conf/openssl.cnf -revoke signed-keys/01.pem
```

You should find that the only change has been to the conf/index file. Now we need to generate our CRL so users can tell it’s been revoked.

```
cd root-ca
openssl ca -config conf/openssl.cnf -gencrl -out ~/example-ca-crl.pem
vi ~/example-ca-crl.pem 
openssl crl -in ~/example-ca-crl.pem -noout -text
```

Revocation is based on the certificate's serial number!

### https://www.davidpashley.com/articles/becoming-a-x-509-certificate-authority/
