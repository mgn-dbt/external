# 🥪 The Jaffle Shop 🦘

Seeds to initialize the source tables for the Jaffle Shop tutorial

Do not create dependencies between this project or these seeds anywhere else<br>
Otherwise, compilation may crash due to transaction handling<br>
https://docs.getdbt.com/reference/resource-configs/pre-hook-post-hook#transaction-behavior<br>

These seeds are only used to prepare the source tables for the Jaffle tutorial (hence the separate project)

This project is an attempt to load Jaffle data into BigQuery, PostgreSQL or DuckDB.

# Profiles.yml
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
```

BigQuery profile must be named "default" for dbt cloud.

offline directory is in .gitignore to keep duckdb database out of version

Command to load data :<br>
dbt seed --full-refresh --profile pg          (for PostgreSql)<br>
dbt seed --full-refresh --profile default     (for BigQuery)<br>
dbt seed --full-refresh --profile duck_tuto   (for Duckdb)

