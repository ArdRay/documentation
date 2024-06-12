---
id: idh4fbowosid8mr8xusmb2y
title: Openssl
desc: ''
updated: 1692268214831
created: 1679300155752
---

# Doc: https://www.feistyduck.com/library/openssl-cookbook/online/

## Contents of TLS certificate - PEM
`openssl x509 -in cert.pem -text`

## Contents of TLS certificate - DER
`openssl x509 -in cert.der -inform der -text`

## Connect to website
`openssl s_client -connect test.io:443 -tls1_3`

## Convert CRT to PEM
`openssl x509 -in mycert.crt -out mycert.pem -outform PEM`

## Convert PKCS#1 to PKCS#8
`openssl pkcs8 -topk8 -inform PEM -outform PEM -nocrypt -in pkcs1.key -out pkcs8.key`
