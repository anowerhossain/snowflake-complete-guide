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
- The table is now dropped but can still be recovered.

```sql
UNDROP TABLE customers;
```

- Also works for the schema and database 

```sql
UNDROP SCHEMA my_schema;
UNDROP DATABASE my_database;
```

✅ This will restore the customers table with all data.

❌ Limitations of `UNDROP TABLE`

- Standard Snowflake accounts: 1-day retention.
- Enterprise accounts: Up to 90 days.
- If the retention period expires, the table cannot be recovered.
- If you create a new table with the same name after dropping the old one, the UNDROP TABLE command will fail.


- To use time travel we need to create the table with

```sql
CREATE OR REPLACE TABLE customers (
    id INT AUTOINCREMENT PRIMARY KEY,
    name STRING,
    email STRING,
    country STRING,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
)
DATA_RETENTION_TIME_IN_DAYS = 7;
```
- Use `BEFORE (STATEMENT => 'query_id')`

```sql
SELECT * FROM CUSTOMERS BEFORE (STATEMENT => '01ba4873-0000-daf4-0000-0008bfb26521');
```

This will return the `SELECT * FROM CUSTOMERS` before the `01ba4873-0000-daf4-0000-0008bfb26521` is executed. To get the `query_id` 

```sql
SELECT QUERY_ID, QUERY_TEXT
FROM TABLE(INFORMATION_SCHEMA.QUERY_HISTORY())
WHERE USER_NAME = 'ANOWERHOSSAIN' -- USER_NAME -- USE SELECT CURRENT_USER();
AND QUERY_TEXT LIKE '%INSERT%'
ORDER BY START_TIME DESC
LIMIT 50;
```
This query will return the specific `query_id` you are looking for.
