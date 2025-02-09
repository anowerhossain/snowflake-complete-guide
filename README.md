# snowflake-complete-guide ❄️
A complete guide to mastering Snowflake – covering core concepts, data ingestion, transformations, performance tuning, security.

## Snowflake core features ❄️

❄ Set up a database and schema

```sql
CREATE DATABASE anower_snowflake_db;
USE DATABASE anower_snowflake_db;

CREATE SCHEMA anower_schema;
USE SCHEMA anower_schema;
```
In Snowflake, data is organized in a hierarchy `ACCOUNT → DATABASE → SCHEMA → TABLE → ROW`

❄ Set up a database and schema

```sql
CREATE OR REPLACE TABLE customers (
    id INT AUTOINCREMENT PRIMARY KEY,
    name STRING,
    email STRING,
    country STRING,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

```sql
INSERT INTO customers (name, email, country)
VALUES 
    ('Alice Johnson', 'alice@example.com', 'USA'),
    ('Bob Smith', 'bob@example.com', 'Canada'),
    ('Charlie Brown', 'charlie@example.com', 'UK'),
    ('David Lee', 'david@example.com', 'Australia'),
    ('Eva Green', 'eva@example.com', 'Germany');
```

- For JSON data use VARIANT data type in snowflake.

```sql
CREATE OR REPLACE TABLE json_data (
    id INT,
    data VARIANT
);
```

```sql
INSERT INTO json_data (id, data) 
VALUES (1, PARSE_JSON('{"name": "Alice", "age": 30}'));
```

- Query JSON Data

```sql
SELECT data:name::STRING AS customer_name FROM json_data;
```

❄️ Loading Data into Snowflake

- Create a stage

```sql
CREATE OR REPLACE STAGE my_stage;
```


❄️ Querying Data

- Simple Query

```sql
SELECT * FROM customers WHERE country = 'USA' LIMIT 10;
```

- Using Aggregations

```
SELECT country, COUNT(*) AS total_customers 
FROM customers 
GROUP BY country;
```

❄️ Time Travel & Cloning (Restore Deleted Data)

```sql
DROP TABLE customers;
```
The table is now dropped but can still be recovered.

```sql
UNDROP TABLE customers;
```

✅ This will restore the customers table with all data.
