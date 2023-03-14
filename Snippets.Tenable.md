---
id: qkren1d4zsyoci72hilawd9
title: Tenable
desc: ''
updated: 1678456012987
created: 1678453496150
---

## Install Tenable Nessus

1. ```
curl --request GET \
--url 'https://www.tenable.com/downloads/api/v2/pages/nessus/files/Nessus-10.5.0-es8.x86_64.rpm' \
--output 'Nessus-10.5.0-es8.x86_64.rpm'
```
2. `rpm -i Nessus-10.5.0-es8.x86_64.rpm`
3. `mount --bind /data/nessus /opt/nessus`
4. `/etc/fstab`: `/data/nessus          	/opt/nessus		none    defaults,bind 0 0`