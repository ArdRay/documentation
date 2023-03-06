---
id: la61hzoa44ubjkgybqfer3n
title: Postgres
desc: ''
updated: 1678114680301
created: 1677411909872
---

### ALL DB backup - Alpine Linux

`docker exec -i cbc824d185e7 /usr/local/bin/pg_dumpall -U postgres > postgres-backup.sql`

### Multiple primary keys

```
CREATE TABLE IF NOT EXISTS vm_domain_shcheck(
            date DATE,
            domain TEXT,
            category TEXT,
            id TEXT,
            severity TEXT,
            finding TEXT,
            PRIMARY KEY (date, domain, category, id));
```
```
sql_query = f"""INSERT INTO vm_domain_sslscan (date, domain, category, id, severity, finding)\
                            VALUES ('{date_entered}',\
                                '{json_data['scanResult'][0]['targetHost']}',\
                                '{scanResult}',\
                                '{i['id']}',\
                                '{i['severity']}',\
                                '{i['finding'].replace("'", '"')}')\
                            ON CONFLICT(date,domain,category,id) DO NOTHING;"""
```