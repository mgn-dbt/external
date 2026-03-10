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
```
