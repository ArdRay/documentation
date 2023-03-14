---
id: la61hzoa44ubjkgybqfer3n
title: Postgres
desc: ''
updated: 1678808493508
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

### Create user
#### Grant all privileges
```
CREATE USER service_account WITH ENCRYPTED PASSWORD 'xxx';
grant all privileges on database intel_db to service_account;
```
#### Grant SELECT only
CREATE ROLE service_role;
GRANT CONNECT ON DATABASE intel_db TO service_role;
GRANT USAGE ON SCHEMA public TO service_role;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO service_role;

CREATE USER intel_db WITH ENCRYPTED PASSWORD 'xxx';
GRANT service_role TO intel_db;

### Administrator commands
- List roles: `\du`
- List databases: `\l`
- List creation date of dbs: `SELECT (pg_stat_file('base/'||oid ||'/PG_VERSION')).modification, datname FROM pg_database ORDER BY modification;`

### User privileges on specific database
```
WITH rol AS (
    SELECT oid,
            rolname::text AS role_name
        FROM pg_authid
    UNION
    SELECT 0::oid AS oid,
            'public'::text
),
schemas AS ( -- Schemas
    SELECT oid AS schema_oid,
            n.nspname::text AS schema_name,
            n.nspowner AS owner_oid,
            'schema'::text AS object_type,
            coalesce ( n.nspacl, acldefault ( 'n'::"char", n.nspowner ) ) AS acl
        FROM pg_catalog.pg_namespace n
        WHERE n.nspname !~ '^pg_'
            AND n.nspname <> 'information_schema'
),
classes AS ( -- Tables, views, etc.
    SELECT schemas.schema_oid,
            schemas.schema_name AS object_schema,
            c.oid,
            c.relname::text AS object_name,
            c.relowner AS owner_oid,
            CASE
                WHEN c.relkind = 'r' THEN 'table'
                WHEN c.relkind = 'v' THEN 'view'
                WHEN c.relkind = 'm' THEN 'materialized view'
                WHEN c.relkind = 'c' THEN 'type'
                WHEN c.relkind = 'i' THEN 'index'
                WHEN c.relkind = 'S' THEN 'sequence'
                WHEN c.relkind = 's' THEN 'special'
                WHEN c.relkind = 't' THEN 'TOAST table'
                WHEN c.relkind = 'f' THEN 'foreign table'
                WHEN c.relkind = 'p' THEN 'partitioned table'
                WHEN c.relkind = 'I' THEN 'partitioned index'
                ELSE c.relkind::text
                END AS object_type,
            CASE
                WHEN c.relkind = 'S' THEN coalesce ( c.relacl, acldefault ( 's'::"char", c.relowner ) )
                ELSE coalesce ( c.relacl, acldefault ( 'r'::"char", c.relowner ) )
                END AS acl
        FROM pg_class c
        JOIN schemas
            ON ( schemas.schema_oid = c.relnamespace )
        WHERE c.relkind IN ( 'r', 'v', 'm', 'S', 'f', 'p' )
),
cols AS ( -- Columns
    SELECT c.object_schema,
            null::integer AS oid,
            c.object_name || '.' || a.attname::text AS object_name,
            'column' AS object_type,
            c.owner_oid,
            coalesce ( a.attacl, acldefault ( 'c'::"char", c.owner_oid ) ) AS acl
        FROM pg_attribute a
        JOIN classes c
            ON ( a.attrelid = c.oid )
        WHERE a.attnum > 0
            AND NOT a.attisdropped
),
procs AS ( -- Procedures and functions
    SELECT schemas.schema_oid,
            schemas.schema_name AS object_schema,
            p.oid,
            p.proname::text AS object_name,
            p.proowner AS owner_oid,
            CASE p.prokind
                WHEN 'a' THEN 'aggregate'
                WHEN 'w' THEN 'window'
                WHEN 'p' THEN 'procedure'
                ELSE 'function'
                END AS object_type,
            pg_catalog.pg_get_function_arguments ( p.oid ) AS calling_arguments,
            coalesce ( p.proacl, acldefault ( 'f'::"char", p.proowner ) ) AS acl
        FROM pg_proc p
        JOIN schemas
            ON ( schemas.schema_oid = p.pronamespace )
),
udts AS ( -- User defined types
    SELECT schemas.schema_oid,
            schemas.schema_name AS object_schema,
            t.oid,
            t.typname::text AS object_name,
            t.typowner AS owner_oid,
            CASE t.typtype
                WHEN 'b' THEN 'base type'
                WHEN 'c' THEN 'composite type'
                WHEN 'd' THEN 'domain'
                WHEN 'e' THEN 'enum type'
                WHEN 't' THEN 'pseudo-type'
                WHEN 'r' THEN 'range type'
                WHEN 'm' THEN 'multirange'
                ELSE t.typtype::text
                END AS object_type,
            coalesce ( t.typacl, acldefault ( 'T'::"char", t.typowner ) ) AS acl
        FROM pg_type t
        JOIN schemas
            ON ( schemas.schema_oid = t.typnamespace )
        WHERE ( t.typrelid = 0
                OR ( SELECT c.relkind = 'c'
                        FROM pg_catalog.pg_class c
                        WHERE c.oid = t.typrelid ) )
            AND NOT EXISTS (
                SELECT 1
                    FROM pg_catalog.pg_type el
                    WHERE el.oid = t.typelem
                        AND el.typarray = t.oid )
),
fdws AS ( -- Foreign data wrappers
    SELECT null::oid AS schema_oid,
            null::text AS object_schema,
            p.oid,
            p.fdwname::text AS object_name,
            p.fdwowner AS owner_oid,
            'foreign data wrapper' AS object_type,
            coalesce ( p.fdwacl, acldefault ( 'F'::"char", p.fdwowner ) ) AS acl
        FROM pg_foreign_data_wrapper p
),
fsrvs AS ( -- Foreign servers
    SELECT null::oid AS schema_oid,
            null::text AS object_schema,
            p.oid,
            p.srvname::text AS object_name,
            p.srvowner AS owner_oid,
            'foreign server' AS object_type,
            coalesce ( p.srvacl, acldefault ( 'S'::"char", p.srvowner ) ) AS acl
        FROM pg_foreign_server p
),
all_objects AS (
    SELECT schema_name AS object_schema,
            object_type,
            schema_name AS object_name,
            null::text AS calling_arguments,
            owner_oid,
            acl
        FROM schemas
    UNION
    SELECT object_schema,
            object_type,
            object_name,
            null::text AS calling_arguments,
            owner_oid,
            acl
        FROM classes
    UNION
    SELECT object_schema,
            object_type,
            object_name,
            null::text AS calling_arguments,
            owner_oid,
            acl
        FROM cols
    UNION
    SELECT object_schema,
            object_type,
            object_name,
            calling_arguments,
            owner_oid,
            acl
        FROM procs
    UNION
    SELECT object_schema,
            object_type,
            object_name,
            null::text AS calling_arguments,
            owner_oid,
            acl
        FROM udts
    UNION
    SELECT object_schema,
            object_type,
            object_name,
            null::text AS calling_arguments,
            owner_oid,
            acl
        FROM fdws
    UNION
    SELECT object_schema,
            object_type,
            object_name,
            null::text AS calling_arguments,
            owner_oid,
            acl
        FROM fsrvs
),
acl_base AS (
    SELECT object_schema,
            object_type,
            object_name,
            calling_arguments,
            owner_oid,
            ( aclexplode ( acl ) ).grantor AS grantor_oid,
            ( aclexplode ( acl ) ).grantee AS grantee_oid,
            ( aclexplode ( acl ) ).privilege_type AS privilege_type,
            ( aclexplode ( acl ) ).is_grantable AS is_grantable
        FROM all_objects
)
SELECT acl_base.object_schema,
        acl_base.object_type,
        acl_base.object_name,
        acl_base.calling_arguments,
        owner.role_name AS object_owner,
        grantor.role_name AS grantor,
        grantee.role_name AS grantee,
        acl_base.privilege_type,
        acl_base.is_grantable
    FROM acl_base
    JOIN rol owner
        ON ( owner.oid = acl_base.owner_oid )
    JOIN rol grantor
        ON ( grantor.oid = acl_base.grantor_oid )
    JOIN rol grantee
        ON ( grantee.oid = acl_base.grantee_oid )
    WHERE acl_base.grantor_oid <> acl_base.grantee_oid ;
```

### Query - Join with the same table

```
SELECT DISTINCT x.domain, vm_domain_sslscan.id, vm_domain_sslscan.finding
FROM vm_domain_sslscan
JOIN (select * from vm_domain_sslscan WHERE id = 'cert_caIssuers' AND finding LIKE '%Encrypt%') AS x
ON x.domain = vm_domain_sslscan.domain
WHERE vm_domain_sslscan.id = 'DNS_CAArecord';
```
