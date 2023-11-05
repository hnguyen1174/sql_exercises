## 1. SQL Schema

```sql
Create table If Not Exists Sales (sale_date date, fruit ENUM('apples', 'oranges'), sold_num int);
Truncate table Sales;
insert into Sales (sale_date, fruit, sold_num) values ('2020-05-01', 'apples', '10');
insert into Sales (sale_date, fruit, sold_num) values ('2020-05-01', 'oranges', '8');
insert into Sales (sale_date, fruit, sold_num) values ('2020-05-02', 'apples', '15');
insert into Sales (sale_date, fruit, sold_num) values ('2020-05-02', 'oranges', '15');
insert into Sales (sale_date, fruit, sold_num) values ('2020-05-03', 'apples', '20');
insert into Sales (sale_date, fruit, sold_num) values ('2020-05-03', 'oranges', '0');
insert into Sales (sale_date, fruit, sold_num) values ('2020-05-04', 'apples', '15');
insert into Sales (sale_date, fruit, sold_num) values ('2020-05-04', 'oranges', '16');
```

```
Write a solution to report the difference between the number of apples and oranges sold each day.

Return the result table ordered by sale_date.

The result format is in the following example.

 

Example 1:

Input: 
Sales table:
+------------+------------+-------------+
| sale_date  | fruit      | sold_num    |
+------------+------------+-------------+
| 2020-05-01 | apples     | 10          |
| 2020-05-01 | oranges    | 8           |
| 2020-05-02 | apples     | 15          |
| 2020-05-02 | oranges    | 15          |
| 2020-05-03 | apples     | 20          |
| 2020-05-03 | oranges    | 0           |
| 2020-05-04 | apples     | 15          |
| 2020-05-04 | oranges    | 16          |
+------------+------------+-------------+
Output: 
+------------+--------------+
| sale_date  | diff         |
+------------+--------------+
| 2020-05-01 | 2            |
| 2020-05-02 | 0            |
| 2020-05-03 | 20           |
| 2020-05-04 | -1           |
+------------+--------------+
Explanation: 
Day 2020-05-01, 10 apples and 8 oranges were sold (Difference  10 - 8 = 2).
Day 2020-05-02, 15 apples and 15 oranges were sold (Difference 15 - 15 = 0).
Day 2020-05-03, 20 apples and 0 oranges were sold (Difference 20 - 0 = 20).
Day 2020-05-04, 15 apples and 16 oranges were sold (Difference 15 - 16 = -1).
```

## 2. SQL Answer

```sql
SELECT 
  sale_date,
  SUM(CASE WHEN fruit = 'apples' THEN sold_num ELSE 0 END) - SUM(CASE WHEN fruit = 'oranges' THEN sold_num ELSE 0 END) AS diff
FROM Sales
GROUP BY 1
ORDER BY sale_date ASC
;
```

## 3. Pandas Schema 

```python
import pandas as pd
import numpy as np

data = [['2020-05-01', 'apples', 10], ['2020-05-01', 'oranges', 8], ['2020-05-02', 'apples', 15], ['2020-05-02', 'oranges', 15], ['2020-05-03', 'apples', 20], ['2020-05-03', 'oranges', 0], ['2020-05-04', 'apples', 15], ['2020-05-04', 'oranges', 16]]
sales = pd.DataFrame(data, columns=['sale_date', 'fruit', 'sold_num']).astype({'sale_date':'datetime64[ns]', 'fruit':'object', 'sold_num':'Int64'})
```

```python
df_pivot = pd.pivot_table(sales, values='sold_num', columns='fruit', index='sale_date').reset_index()
df_agg = df_pivot.groupby('sale_date').sum().reset_index()
df_agg['diff'] = df_agg['apples'] - df_agg['oranges']
print(df_agg.sort_values('sale_date'))
```