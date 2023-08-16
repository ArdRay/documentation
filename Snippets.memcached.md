---
id: 83adbj0pat4ssqszxx6u28c
title: memcached
desc: ''
updated: 1692207897240
created: 1692168988869
---

# Interact
telnet 127.0.0.1 11211
get umbrella_top_1m:www.hcaptcha.com
get umbrella_top_1m:dev-01
get umbrella_top_1m:download.test.io
get umbrella_top_1m:redis2b.data
get ssh_enrichment:bJUXf3j2lVanZ

flush_all
stats

# TLS config options
-Z -o ssl_chain_cert=/etc/pki/tls/certs/hostname.crt,ssl_key=/etc/pki/tls/private/hostname.key
