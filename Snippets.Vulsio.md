---
id: xcez9d5fc8h9pexvxwk2izh
title: Vulsio
desc: ''
updated: 1677680679442
created: 1677664186642
---

# Vuls.io

## Installation
mkdir vuls_delete_later
cd vuls_delete_later/
git clone https://github.com/vulsio/vulsctl.git
cd vulnsctl/docker && ./update-all.sh

## Setup key material
echo 'key' > /root/.ssh/privkey
```
cat config.toml
[servers]
[servers.hostos]
host        = "172.22.129.1"
port        = "22"
user        = "privkey"
keyPath     = "/root/.ssh/privkey"
```

## Execute
./docker/scan.sh
./docker/report.sh
./docker/tui.sh
