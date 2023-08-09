---
id: yrdi1kzj8vw59ovbrg2e2yd
title: gpg
desc: ''
updated: 1691593419991
created: 1691565601485
---

## Encryption - File
gpg --homedir /var/lib/gnupg/ --output healthz_secret.txt.gpg --encrypt --armor --recipient gpg_automation healthz_secret.txt

## Decryption - File
gpg --homedir /var/lib/gnupg/ --output healthz_secret_.txt --decrypt healthz_secret.txt.gpg