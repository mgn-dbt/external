# 🥪 The Jaffle Shop 🦘

Seeds to initialize the source tables for the Jaffle Shop tutorial

This project is an attempt to load Jaffle data into PostgreSQL and BigQuery.


### scripts PostgreSql :

### role :
```sql
CREATE ROLE jaffle WITH
LOGIN
NOSUPERUSER
INHERIT
NOCREATEDB
NOCREATEROLE
NOREPLICATION
NOBYPASSRLS
ENCRYPTED PASSWORD xxxxxxxxxxxxxxxxxxxxx;
```

### base :
```sql
CREATE DATABASE jaffle_shop
WITH
OWNER = jaffle
ENCODING = 'UTF8'
LOCALE_PROVIDER = 'libc'
CONNECTION LIMIT = -1
IS_TEMPLATE = False;
```

### schemas :
```sql
CREATE SCHEMA IF NOT EXISTS seeds_raw  AUTHORIZATION jaffle;
GRANT ALL ON SCHEMA seeds_raw TO jaffle;

CREATE SCHEMA IF NOT EXISTS dbt_raw_stripe  AUTHORIZATION jaffle;
GRANT ALL ON SCHEMA dbt_raw_stripe TO jaffle;

CREATE SCHEMA IF NOT EXISTS dbt_raw_jaffle_shop  AUTHORIZATION jaffle;
GRANT ALL ON SCHEMA dbt_raw_jaffle_shop TO jaffle;

CREATE SCHEMA IF NOT EXISTS dbt_raw_autre  AUTHORIZATION jaffle;
GRANT ALL ON SCHEMA dbt_raw_autre TO jaffle;

CREATE SCHEMA IF NOT EXISTS dbt_<user>  AUTHORIZATION jaffle;
GRANT ALL ON SCHEMA dbt_<user> TO jaffle;
```

### pb certificat pgadmin 4
& "<path_to>\postgresql\18.3\pgAdmin 4\python\python.exe" -m pip install pip_system_certs

