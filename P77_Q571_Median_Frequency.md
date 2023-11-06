## 1. SQL Schema

```sql
Create table If Not Exists Numbers (num int, frequency int);
Truncate table Numbers;
insert into Numbers (num, frequency) values ('0', '7');
insert into Numbers (num, frequency) values ('1', '1');
insert into Numbers (num, frequency) values ('2', '3');
insert into Numbers (num, frequency) values ('3', '1');
```

```
The median is the value separating the higher half from the lower half of a data sample.

Write a solution to report the median of all the numbers in the database after decompressing the Numbers table. Round the median to one decimal point.

The result format is in the following example.

 

Example 1:

Input: 
Numbers table:
+-----+-----------+
| num | frequency |
+-----+-----------+
| 0   | 7         |
| 1   | 1         |
| 2   | 3         |
| 3   | 1         |
+-----+-----------+
Output: 
+--------+
| median |
+--------+
| 0.0    |
+--------+
Explanation: 
If we decompress the Numbers table, we will get [0, 0, 0, 0, 0, 0, 0, 1, 2, 2, 2, 3], so the median is (0 + 0) / 2 = 0.
```

## 2. SQL Answer

```sql
WITH t AS (
  SELECT 
    *, 
    SUM(frequency) OVER (ORDER BY num) AS freq, 
    (SUM(frequency) over())/2 AS median_num
  FROM Numbers
)


SELECT 
  AVG(num) as median
FROM t
WHERE median_num BETWEEN (freq-frequency) AND freq
;
```
