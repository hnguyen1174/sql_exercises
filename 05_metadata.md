### 1. Listing Tables in a Schema

```sql
select table_name
from information_schema.tables
where table_schema = 'SMEAGOL'
```

### 2. Listing a Table’s Columns

```sql
select column_name, data_type, ordinal_position
from information_schema.columns
where table_schema = 'SMEAGOL'
and table_name   = 'EMP'
```

### 3. Listing Indexed Columns for a Table

You want list indexes, their columns, and the column position (if available) in the index for a given table.

```sql
show index from emp
```

### 4. Listing Constraints on a Table

Constraints are such a critical part of relational databases that it should go without saying why you need to know what constraints are on your tables. Listing the con‐ straints on tables is useful for a variety of reasons: you may want to find tables miss‐ ing a primary key, you may want to find which columns should be foreign keys but are not (i.e., child tables have data different from the parent tables and you want to know how that happened), or you may want to know about check constraints (Are columns nullable? Do they have to satisfy a specific condition? etc.).

```sql
select a.table_name,
       a.constraint_name,
       b.column_name,
       a.constraint_type
from information_schema.table_constraints a,
     information_schema.key_column_usage b
where a.table_name = 'EMP' 
      and a.table_schema = 'SMEAGOL'
      and a.table_name = b.table_name
      and a.table_schema = b.table_schema
      and a.constraint_name = b.constraint_name
```

### 5. Listing Foreign Keys Without Corresponding Indexes

You can use the SHOW INDEX command to retrieve index information such as index name, columns in the index, and ordinal position of the columns in the index. Additionally, you can query INFORMATION_SCHEMA.KEY_COLUMN_USAGE to list the foreign keys for a given table. In MySQL 5, foreign keys are said to be indexed automatically, but can in fact be dropped. To determine whether a foreign key column’s index has been dropped, you can execute SHOW INDEX for a particu‐ lar table and compare the output with that of INFORMATION_SCHEMA.KEY_ COLUMN_USAGE.COLUMN_NAME for the same table. If the COLUMN_NAME is listed in KEY_COLUMN_USAGE but is not returned by SHOW INDEX, you know that column is not indexed.

### 6. Using SQL to Generate SQL

(Not important)

### 7. Describing the Data Dictionary Views in an Oracle Database

(Not applicable)

