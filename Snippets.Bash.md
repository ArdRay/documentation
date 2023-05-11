---
id: 5job635dob28evr9wko67tk
title: Bash
desc: ''
updated: 1683819603800
created: 1683369846428
---

# Download files via SSH /w sudo requirement
#!/bin/bash
mkdir pdfs
for FILE in $(ssh 10.10.2.5 "sudo ls /data/sftp/lexmark/"); do
  ssh 10.10.2.5 "sudo cat /data/sftp/lexmark/$FILE" > pdfs/$FILE;  
  echo $FILE downloaded;
done

