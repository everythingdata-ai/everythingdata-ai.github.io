---
layout: post
title: Snowflake ELT project with dbt/Airflow
categories: [Data Engineering, Snowflake]
---

Today I will be exploring a new data stack that I have never used before.
In Data Engineering, what matters is bringing value, no matter which technology you use.
However you still need some experience if you want to land a job.
That's why it's time to expand my knowledge beyond GCP, and see what's it's like at other providers as well, like Azure or Snowflake.

#### Overview

This is an introduction to Snowflake project, so the datawarehouse will be at Snowflake.
I will use dbt for transformation and Airflow for orchestration.
I will be using the TPC-H dataset, available for free at Snowflake.

#### Snowflake trial account

Snowflake offers a 30-day free trial account with USD400 of credit, so let's go ahead and create an account [here](https://signup.snowflake.com).

![image](https://github.com/user-attachments/assets/31e4164e-f5b2-451c-8747-e9d12406ed08)

The first thing we will do is set-up our Snowflake environment, by creating a new Worksheet :

<img width="211" alt="image" src="https://github.com/user-attachments/assets/b1d42f9f-f56f-403c-88d5-b4a3a97c977b" />

In your worksheet, create a role/warehouse/database using the super user (accountadmin).
The created role will be assigned to our Snowflake user, and will be granted access to the database/warehouse :

```sql
USE ROLE accountadmin;

CREATE WAREHOUSE dbt_wh WITH warehouse_size = 'x-small';
CREATE DATABASE dbt_db;
CREATE ROLE dbt_role;

GRANT usage ON WAREHOUSE dbt_wh TO ROLE dbt_role;

GRANT ROLE dbt_role TO USER mouradgh;

GRANT all ON DATABASE dbt_db TO ROLE dbt_role;
```
You can check that dbt_role has been granted access to dbt_wh using this command :

```sql
SHOW GRANTS ON WAREHOUSE dbt_wh;
```

<img width="489" alt="image" src="https://github.com/user-attachments/assets/295f53c9-414a-41c0-b4f7-aad646682209" />

We can now switch to the newly created role :

```sql
USE ROLE dbt_role;
```

And create a schema :

```sql
CREATE SCHEMA dbt_db.dbt_schema;
```

If you refresh you Databases objects, you should be able to see your schema :

<img width="378" alt="image" src="https://github.com/user-attachments/assets/cfd10775-e258-465c-b050-aafe6babcecb" />



#### Install dbt

Create a new project in your favorite IDE then create a Python virtual environment :

```bash
python3 -m venv env
source env/bin/activate
```

Install dbt-core as well as the dbt§snowflake adapter :

```bash
pip install dbt-core dbt-snowflake
```

Now let's initialize our dbt project :

```bash
dbt init
```

When prompted, choose a name for your project, a connector, and then your Snowflake account (you should have received it in an e-mail).
You then put your username and password, and finally the role/dw/db/aschema we just created in Snowflake :

<img width="722" alt="image" src="https://github.com/user-attachments/assets/c6ffbf2b-9a56-4598-9108-aa4251573b74" />

Once your dbt project ready, edit your dbt_project.yml file by replacing the example model with these 2 models : 

```yaml
models:
  data_pipeline:
    # Config indicated by + and applies to all files under models/example/
    staging:
      +materialized: view
      snowflake_warehouse: dbt_wh
    marts:
      +materialized: table
      snowflake_warehouse: dbt_wh
```

In the models folder you can delete example and create 2 new folders : marts and staging.

Now let's install a useful package called `dbt-utils`, by creating a packages.yml file :

```yaml
packages:
  - package: dbt-labs/dbt_utils
    version: 1.3.0
```

And then in your terminal run `dbt deps`

#### Closing notes

To avoid encurring costs, you can drop everything you created in this tutorial using these commands :

```sql
USE ROLE accountadmin;
DROP WAREHOUSE IF EXISTS dbt_wh;
DROP DATABASE IF EXISTS dbt_db;
DROP ROLE IF EXISTS dbt_role;
```



