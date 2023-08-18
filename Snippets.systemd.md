---
id: xofoutgogqcfbkeyrympizu
title: systemd
desc: ''
updated: 1692268236787
created: 1692261896406
---

## Remove service
```
systemctl stop [servicename]
systemctl disable [servicename]
rm /etc/systemd/system/[servicename]
rm /usr/lib/systemd/system/[servicename] 
systemctl daemon-reload
systemctl reset-failed
```


