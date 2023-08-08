---
id: 5iiry3ggzif0adrtcwdtoqk
title: linux_commands
desc: ''
updated: 1690979763860
created: 1687593839866
---

# Certbot
certbot certonly --dns-cloudflare --dns-cloudflare-credentials /opt/certbot/cloudflare.ini -d example.cloud -m xx@xx.com --agree-tos --no-eff-email

# HAproxy compatible certs
cat /etc/letsencrypt/live/example.cloud/privkey.pem \
    /etc/letsencrypt/live/example.cloud/fullchain.pem > \
    /opt/compose/files/haproxy/certs/example.cloud

