---
id: lolqgxg8cvz3sifgdcow7od
title: faasd
desc: ''
updated: 1692207359906
created: 1690453986648
---

# Build
1. faas-cli new --lang python3-http lib-v1
2. faas-cli build -f ./lib-v1.yml --shrinkwrap

1. faas-cli new --lang dockerfile lib-v2

faas-cli deploy --image=my_image --name=my_fn --handler=/path/to/fn/
                  --gateway=http://remote-site.com:8080 --lang=python
                  --env=MYVAR=myval

harbor.domain.ch/security/images/faas-redis-exists:latest

1. faas-cli secret create sentinel_pwd --from-file=sentinel_pwd.txt
2. faas-cli new --lang python3-http redis-exists
3. faas-cli build -f ./redis-exists.yml --shrinkwrap
4. docker build --no-cache --network=host .
5. docker tag a12486e79ba4 harbor.domain.ch/security/images/faas-redis-exists:latest
6. docker push harbor.domain.ch/security/images/faas-redis-exists:latest
7. faas-cli deploy -f ./redis-exists.yml

## build, tag and push all together
1. docker build --no-cache --network=host -t harbor.domain.ch/security/images/faas-redis-check:latest .
2. docker push harbor.domain.ch/security/images/faas-redis-check:latest
3. faas-cli deploy -f ./redis-check.yml

# Authentication
1. cat /var/lib/faasd/secrets/basic-auth-password | faas-cli login -u admin --password-stdin
2. docker login harbor.domain.ch

# Secrets
faas-cli secret create redis-pwd --from-file=/data/faasd_workshop/secrets/redis_pwd.txt
faas-cli secret create redis-crt --from-file=/data/faasd_workshop/secrets/redis.crt
faas-cli secret create redis-key --from-file=/data/faasd_workshop/secrets/redis.key
faas-cli secret create redis-ca --from-file=/data/faasd_workshop/secrets/ca.crt

# Download templates
1. faas-cli template store pull python3-http

# Build without docker build

1. faas-cli new --lang python3-http healthz
2. faas-cli build -f ./healthz.yml --shrinkwrap
3. docker build --no-cache --network=host -t harbor.domain.ch/security/images/faas/healthz:latest .
4. docker push harbor.domain.ch/security/images/faas/healthz:latest
5. faas-cli deploy -f ./healthz.yml

# Response

def return_response(HTTPstatusCode, HTTPbody):
    return(
        {
            "statusCode": HTTPstatusCode,
            "body": HTTPbody,
            "headers": {
                "Content-Type": "application/json"
            }
        }
    )
