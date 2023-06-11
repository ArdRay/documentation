---
id: 3k9jl7fvq96yq7dvswur1u1
title: disk_linux
desc: ''
updated: 1686502510901
created: 1686415259604
---

# lsblk -io KNAME,TYPE,SIZE,MODEL
```
KNAME     TYPE   SIZE MODEL
sda       disk   7.3T ST8000NM0055-1RM112
sda1      part   7.3T
sda9      part     8M
sdb       disk   7.3T ST8000NM0055-1RM112
sdb1      part   7.3T
sdb9      part     8M
sdc       disk   7.3T ST8000NM0055-1RM112
sdc1      part   7.3T
sdc9      part     8M
sdd       disk   1.8T WDC_WDS200T1R0A-68A4W0
sde       disk 447.1G MTFDDAK480TDS 
sdf       disk   3.6T Samsung_SSD_870_EVO_4TB
sdg       disk   931G PERC_H330_Adp
sdg1      part  1007K -> proxmox boot
sdg2      part   512M -> proxmox
sdg3      part   320G --> thinLVM attached to Promox (RAID1) 
sdg4      part 610.5G --> app storage (RAID1)
nvme0n1   disk 465.8G Samsung SSD 970 EVO Plus 500GB -> ZFS caching
nvme0n1p1 part   160G -> ZFS ARC
nvme0n1p2 part    40G -> ZFS ZIL
nvme0n1p3 part 265.8G -> unused
```

# Disks to migrate
ZFS pool:
1. ST8000NM0055-1RM112 - ID="ata-ST8000NM0055-1RM112_ZA1JX7LG"
2. ST8000NM0055-1RM112 - ID="ata-ST8000NM0055-1RM112_ZA1JPFP2"
3. ST8000NM0055-1RM112 - ID="ata-ST8000NM0055-1RM112_ZA1JX71V"
ZFS added pools:
1. nvme-Samsung_SSD_970_EVO_Plus_500GB_S4EVNMFN757322P-part1
2. nvme-Samsung_SSD_970_EVO_Plus_500GB_S4EVNMFN757322P-part2 

VM application (RAID 1):
1. sdg4 (SSD - RAID1) - UUID="2e633bf4-d66f-4626-b98d-b1185f80f598" wwn-0x6b07b250f0f2c0002957a2061ebb7ed2-part4

Download Cache (prod-02):
1. MTFDDAK480TDS (SSD) - UUID="f43a8d01-9ab9-4b8d-87c6-4c247808ebe3" - ata-MTFDDAK480TDS_21283094DEAE

Rest:
1. WDC_WDS200T1R0A-68A4W0 (SSD) - UUID="f3322fe2-1baf-470e-af2e-a26aa23c23ff" - ata-WDC_WDS200T1R0A-68A4W0_20339A801081 
2. Samsung_SSD_870_EVO_4TB (SSD) - UUID="16543e92-d9ef-4614-a849-c056394b5a99" ata-Samsung_SSD_870_EVO_4TB_S6BCNX0T602608R
3. nvme0n1p3 (NVME)  - ID="nvme-Samsung_SSD_970_EVO_Plus_500GB_S4EVNMFN757322P-part3"