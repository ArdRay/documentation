---
id: ps1of3oarlgpwg0tdfp6r60
title: Ansible
desc: ''
updated: 1691524156277
created: 1677510189090
---

## Vault operations

ansible-vault encrypt_string --vault-password-file .vault 'xx' --name 'xx_password'
ansible localhost -m debug -a 'var=xx_password' --vault-password-file .vault

## Copy file and restart service afterwards

```
tasks/main.yml:
- name: Copy ossec.conf to remote /var/ossec/etc/ossec.conf
  copy:
    src: files/ossec.conf
    dest: /var/ossec/etc/ossec.conf
    owner: ossec
    group: ossec
    mode: 0740
  notify:
    - restart_service

handlers/main.yml:
- name: restart_service
  systemd:
    name: service
    state: restarted
```

## Copy directory

```
- name: Copy files in /opt/files
  copy:
    src: files/
    dest: /opt/files
    directory_mode: yes
    owner: root
    group: root
    mode: 0740
```

