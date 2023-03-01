---
id: 10knux48zcrif38k4rhr6qh
title: LVM
desc: ''
updated: 1677664858634
created: 1677664812902
---

## Extend disk

```
# vgs
VG  #PV #LV #SN Attr   VSize   VFree
vd1   1   3   0 wz--n- <19.00g <10.00g
# lvextend -L +1G -r /dev/mapper/vd1-root
```

