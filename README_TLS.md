#### Creation of a TLS certificate with openssl

Installation of the EZD RP frontend requires a TLS certificate. Follow the example below if a self-signed certificate is preferred.

Example:

1. CA creation  

```
mkdir ~/certs
cd ~/certs

CERT_C="PL"
CERT_ST="Mazovian"
CERT_L="Warsaw"
CERT_O="Linux Polska Sp. z o.o."
CERT_OU="Tech"
CERT_CN="Linux Polska CA"

openssl genpkey -algorithm ed25519 -out ca.key

openssl req -x509 -new -nodes -sha512 -days 3650 \
-subj "/C=${CERT_C}/ST=${CERT_ST}/L=${CERT_L}/O=${CERT_O}/OU=${CERT_OU}/CN=${CERT_CN}" \
-key ca.key \
-out ca.crt
```

2. Generate TLS certificate

```
CERT_C="PL"
CERT_ST="Mazovian"
CERT_L="Warsaw"
CERT_O="Linux Polska Sp. z o.o."
CERT_OU="Tech"
CERT_CN=*.apps.test1.lab.linuxpolska.pl
CERT_NAME=$(echo "${CERT_CN}" | sed 's/\*\.//g')

cd ~/certs

openssl genpkey -algorithm ed25519 -out ${CERT_NAME}.key

openssl req -sha512 -new \
-subj "/C=${CERT_C}/ST=${CERT_ST}/L=${CERT_L}/O=${CERT_O}/OU=${CERT_OU}/CN=${CERT_CN}" \
-key ${CERT_NAME}.key \
-out ${CERT_NAME}.csr

cat > ${CERT_NAME}.v3.ext << EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = critical,digitalSignature
extendedKeyUsage = serverAuth
subjectAltName = @alt_names
[alt_names]
DNS.1=${CERT_CN}
IP.1=10.10.48.225
IP.2=10.10.48.226
EOF

openssl x509 -req -sha512 -days 3650 \
-extfile ${CERT_NAME}.v3.ext \
-CA ca.crt -CAkey ca.key -CAcreateserial \
-in ${CERT_NAME}.csr \
-out ${CERT_NAME}.crt
```
