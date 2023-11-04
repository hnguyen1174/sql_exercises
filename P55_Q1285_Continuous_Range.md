## 1. Table Schema


```sql
Create table If Not Exists Logs (log_id int);
Truncate table Logs;
insert into Logs (log_id) values ('1');
insert into Logs (log_id) values ('2');
insert into Logs (log_id) values ('3');
insert into Logs (log_id) values ('7');
insert into Logs (log_id) values ('8');
insert into Logs (log_id) values ('10');
```


```
Write a solution to find the start and end number of continuous ranges in the table Logs.

Return the result table ordered by start_id.

The result format is in the following example.

 

Example 1:

Input: 
Logs table:
+------------+
| log_id     |
+------------+
| 1          |
| 2          |
| 3          |
| 7          |
| 8          |
| 10         |
+------------+
Output: 
+------------+--------------+
| start_id   | end_id       |
+------------+--------------+
| 1          | 3            |
| 7          | 8            |
| 10         | 10           |
+------------+--------------+
Explanation: 
The result table should contain all ranges in table Logs.
From 1 to 3 is contained in the table.
From 4 to 6 is missing in the table
From 7 to 8 is contained in the table.
Number 9 is missing from the table.
Number 10 is contained in the table.
```

## 2. SQL Answer

```sql
WITH row_tbl AS (
  SELECT 
    log_id, 
    ROW_NUMBER() OVER(ORDER BY log_id) as num
  FROM Logs
)

SELECT 
  min(log_id) AS start_id, 
  max(log_id) AS end_id
FROM row_tbl
GROUP BY log_id - num
;
```