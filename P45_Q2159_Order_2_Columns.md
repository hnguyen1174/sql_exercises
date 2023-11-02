## 1. Table Schema


```sql
Create table If Not Exists Data (first_col int, second_col int);
Truncate table Data;
insert into Data (first_col, second_col) values ('4', '2');
insert into Data (first_col, second_col) values ('2', '3');
insert into Data (first_col, second_col) values ('3', '1');
insert into Data (first_col, second_col) values ('1', '4');
```

```
Write a solution to independently:

order first_col in ascending order.
order second_col in descending order.
The result format is in the following example.

 

Example 1:

Input: 
Data table:
+-----------+------------+
| first_col | second_col |
+-----------+------------+
| 4         | 2          |
| 2         | 3          |
| 3         | 1          |
| 1         | 4          |
+-----------+------------+
Output: 
+-----------+------------+
| first_col | second_col |
+-----------+------------+
| 1         | 4          |
| 2         | 3          |
| 3         | 2          |
| 4         | 1          |
+-----------+------------+
```

## 2. SQL Answer 

```
WITH col_ranks AS (
  SELECT
    D.first_col,
    D.second_col,
    ROW_NUMBER() OVER (ORDER BY D.first_col) AS fc_rnk,
    ROW_NUMBER() OVER (ORDER BY D.second_col DESC) AS sc_rnk
  FROM
    Data D
)
SELECT
  CR1.first_col,
  CR2.second_col
FROM
  col_ranks CR1
  INNER JOIN col_ranks CR2 ON CR1.fc_rnk = CR2.sc_rnk
ORDER BY
  CR1.fc_rnk
```
;