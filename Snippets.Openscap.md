---
id: azt9n0sq3jdku5wd7pdncsj
title: OpenSCAP
desc: ''
updated: 1677508348639
created: 1677484523617
---

# Install OpenSCAP

- `dnf install openscap-scanner scap-security-guide`

```
Installing:
 openscap-scanner                x86_64             1.3.6-4.el8.rocky.0.2               appstream              75 k
 scap-security-guide             noarch             0.1.63-4.el8.rocky.0.1              appstream              14 M
 ```

- `ls /usr/share/xml/scap/ssg/content/`

```
# ls /usr/share/xml/scap/ssg/content/ | grep rl
ssg-rl8-ds-1.2.xml
ssg-rl8-ds.xml
ssg-rl8-xccdf.xml
```

- `oscap info /usr/share/xml/scap/ssg/content/ssg-rhel8-ds.xml`

```
oscap xccdf eval \
--profile xccdf_org.ssgproject.content_profile_ospp \
--results ssg-rl8-ds.xml \
--report ssg-rl8-ds.html \
--fetch-remote-resources \
/usr/share/xml/scap/ssg/content/ssg-rl8-ds.xml
```

## Vulnerability data

- `wget https://www.redhat.com/security/data/metrics/com.redhat.rhsa-all.xccdf.xml`
- `wget https://www.redhat.com/security/data/oval/com.redhat.rhsa-all.xml`
- `wget https://download.rockylinux.org/pub/oval/org.rockylinux.rlsa-8.xml`

- `oscap xccdf eval --results results.xml --report report.html com.redhat.rhsa-all.xccdf.xml`
```
ps aux | grep oscap
root     1455740 99.5  4.0 827400 652088 pts/0   R+   09:27   3:18 oscap xccdf eval --results results.xml --report report.html com.redhat.rhsa-all.xccdf.xml
```
- `oscap oval eval --results results.xml --report report.html org.rockylinux.rlsa-8.xml`
```
ps aux | grep oscap
root     1456283  111  9.0 2255192 1463540 pts/0 Rl+  09:39   0:31 oscap oval eval --results results.xml --report report.html org.rockylinux.rlsa-8.xml
```
- `oscap oval validate org.rockylinux.rlsa-8.xml`

- `<oval-def:reference ref_id="cpe:/o:rocky:rocky:8" source="CPE"/>`
- `sed -i 's/cpe:\/a:rocky:linux:8/cpe:\/a:rocky:rocky:8/g' mod.rlsa-8.xml`


