# 🥪 The Jaffle Shop 🦘

Seeds to initialize the source tables for the Jaffle Shop tutorials

There are 2 batches of tables used in DBT tutorials.<br>
3 tables tuto : raw_customers, raw_orders and raw_payments<br>
6 tables tuto : customers, items, orders, products, stores and supplies

Do not create dependencies between this project or the seeds in it in your project.

These seeds are only used to prepare the source tables for the Jaffle tutorials (hence the separate project)<br>
Cf https://github.com/mgn-dbt/tutorial

This project is for loading Jaffle data quickly into BigQuery, PostgreSQL or DuckDB.

dbt seed --full-refresh --profile default     (for BigQuery with dbt fusion or dbt cloud)<br>
dbt seed --full-refresh --profile pg          (for PostgreSql with dbt core)<br>
dbt seed --full-refresh --profile duck_tuto   (for Duckdb with dbt core)


# My DBT Profiles

($env:USERPROFILE\.dbt\profiles.yml)
```yaml
default:
  target: dev
  outputs:
    dev:
      type: bigquery
      threads: 4
      project: dbt-jaffle-shop-xxxxxx
      dataset: dbt_<user>
      method: service-account
      keyfile: C:\Users\<user>\.dbt\dbt-jaffle-shop-xxxxxx-yyyyyyyyyyyy.json
      location: US
pg:
  target: dev
  outputs:
    dev:
      dbname: jaffle_shop
      host: localhost
      password: jaffle
      port: 5432
      schema: dbt_<user>
      search_path: dbt_<user>,public
      threads: 1
      type: postgres
      user: jaffle
      sslmode: verify-ca
      sslrootcert: C:\Users\<user>\SCOOP\persist\ssl\CA\certs\ca.cert.pem
duck_tuto:
  target: dev
  outputs:
    dev:
      type: duckdb
      path: 'C:\Users\<user>\SCOOP\_dev_\dbt\jaffle_shop\offline\duck_tuto.duckdb'
      threads: 4
      schema: dbt_<user>
      settings:
        log_query_path: 'C:\Users\<user>\SCOOP\_dev_\dbt\jaffle_shop\offline\duck_tuto_query.log'
```

BigQuery profile must be named "default" for dbt cloud.

Duckdb log_query_path is optional. Use it only to debug queries.

NB : offline directory is in .gitignore file to keep duckdb files out of version.


# PostgreSQL


Start instance :
```
pg_ctl.exe start
```

Connect as DBA :
```
chcp 1252
psql.exe -U postgres
```

### create role, database and schemas :
```sql
set password_encryption = 'scram-sha-256';

CREATE ROLE jaffle WITH
LOGIN
NOSUPERUSER
INHERIT
NOCREATEDB
NOCREATEROLE
NOREPLICATION
NOBYPASSRLS
ENCRYPTED PASSWORD 'xxxxxxxxxxxx';

CREATE ROLE lecteur WITH
LOGIN
NOSUPERUSER
INHERIT
NOCREATEDB
NOCREATEROLE
NOREPLICATION
NOBYPASSRLS
ENCRYPTED PASSWORD 'xxxxxxxxxxxx';

CREATE DATABASE jaffle_shop
WITH
OWNER = jaffle
ENCODING = 'UTF8'
LOCALE_PROVIDER = 'libc'
CONNECTION LIMIT = -1
IS_TEMPLATE = False;

CREATE SCHEMA IF NOT EXISTS seeds_raw  AUTHORIZATION jaffle;
GRANT ALL ON SCHEMA seeds_raw TO jaffle;

CREATE SCHEMA IF NOT EXISTS dbt_raw_autre  AUTHORIZATION jaffle;
GRANT ALL ON SCHEMA dbt_raw_autre TO jaffle;

CREATE SCHEMA IF NOT EXISTS dbt_<user>  AUTHORIZATION jaffle;
GRANT ALL ON SCHEMA dbt_<user> TO jaffle;
```

Before exiting psql, secure the postgres (DBA) account
```
ALTER USER postgres PASSWORD 'xxxxxxxxxxxx';

select * from pg_catalog.pg_authid where rolcanlogin = true;
```

\q to exit psql


PostgreSQL connection should be established under SSL/TLS for security.<br>
SSL/TLS certificate generation and configuration is not documented here.<br>
Cf https://learn.microsoft.com/en-us/azure/application-gateway/self-signed-certificates

### postgresql.conf and pg_hba.conf

Add this at the end of postgresql.conf
```
include_if_exists = 'instance.conf'
```

Add this in instance.conf in the same directory as postgresql.conf :
```
listen_addresses = '*'
password_encryption = scram-sha-256
ssl=on
ssl_min_protocol_version = 'TLSv1.2'
ssl_ca_file = 'C:\\Users\\<user>\\SCOOP\\persist\\ssl\\CA\\certs\\ca.cert.pem'
ssl_cert_file = 'C:\\Users\\<user>\\SCOOP\\persist\\ssl\\CA\\certs\\server.cert.pem'
ssl_key_file = 'C:\\Users\\<user>\\SCOOP\\persist\\ssl\\CA\\private\\server.key.pem'
```

Backup original pg_hba.conf.<br>
Overwrite pg_hba.conf with :

```
# TYPE      DATABASE        USER            ADDRESS                 METHOD
hostssl     all             all             0.0.0.0/0               scram-sha-256
hostssl     all             all             ::/0                    scram-sha-256
hostnossl   all             all             0.0.0.0/0               reject
hostnossl   all             all             ::/0                    reject
```


### postgresql.conf and pg_hba.conf without SSL/TLS (passwords travel in clear text. not secure)

Add this in instance.conf in the same directory as postgresql.conf :
```
listen_addresses = '*'
password_encryption = scram-sha-256
```

Backup original pg_hba.conf.<br>
Overwrite pg_hba.conf with :

```
# TYPE      DATABASE        USER            ADDRESS                 METHOD
host     all             all             0.0.0.0/0               scram-sha-256
host     all             all             ::/0                    scram-sha-256
```


### pgadmin 4

Restart instance
```
pg_ctl.exe restart
```

Solve certificate error
```powershell
& "<path_to>\postgresql\18.3\pgAdmin 4\python\python.exe" -m pip install pip_system_certs
```

Test SSL/TLS connection with pgadmin 4

![pgadmin 4 params](pgadmin4params.png)


# Duckdb

I have given up using sqltools duckdb plugin.<br>
I use duckdb ui instead.

duckdb.exe -ui

```sql
attach 'c:\Users\<user>\SCOOP\_dev_\dbt\jaffle_shop\offline\duck_tuto.duckdb' as duck_tuto;
use duck_tuto;

SELECT * FROM duckdb_settings();

detach duck_tuto;
```

Don't forget to detach from database before exiting.

.exit (to quit)


# SCOOP

I use SCOOP under Windows and Powershell with no admin rights.<br>

Excerpt from "scoop list" :
```
openssl-light    3.6.1        main
nodejs           25.9.0       main
postgresql       18.3         main
duckdb           1.5.1        main
pwsh             7.6.0        main
python           3.12.7       <auto-generated>
vscode           1.114.0      extras
```

