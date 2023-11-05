## 1. Table Schema

```sql
Create table If Not Exists Calls (from_id int, to_id int, duration int);
Truncate table Calls;
insert into Calls (from_id, to_id, duration) values ('1', '2', '59');
insert into Calls (from_id, to_id, duration) values ('2', '1', '11');
insert into Calls (from_id, to_id, duration) values ('1', '3', '20');
insert into Calls (from_id, to_id, duration) values ('3', '4', '100');
insert into Calls (from_id, to_id, duration) values ('3', '4', '200');
insert into Calls (from_id, to_id, duration) values ('3', '4', '200');
insert into Calls (from_id, to_id, duration) values ('4', '3', '499');
```

```
Write a solution to report the number of calls and the total call duration between each pair of distinct persons (person1, person2) where person1 < person2.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Calls table:
+---------+-------+----------+
| from_id | to_id | duration |
+---------+-------+----------+
| 1       | 2     | 59       |
| 2       | 1     | 11       |
| 1       | 3     | 20       |
| 3       | 4     | 100      |
| 3       | 4     | 200      |
| 3       | 4     | 200      |
| 4       | 3     | 499      |
+---------+-------+----------+
Output: 
+---------+---------+------------+----------------+
| person1 | person2 | call_count | total_duration |
+---------+---------+------------+----------------+
| 1       | 2       | 2          | 70             |
| 1       | 3       | 1          | 20             |
| 3       | 4       | 4          | 999            |
+---------+---------+------------+----------------+
Explanation: 
Users 1 and 2 had 2 calls and the total duration is 70 (59 + 11).
Users 1 and 3 had 1 call and the total duration is 20.
Users 3 and 4 had 4 calls and the total duration is 999 (100 + 200 + 200 + 499).
```

## 2. SQL Answer

```sql
WITH all_calls AS (
  SELECT * FROM Calls
  UNION ALL
  SELECT to_id AS from_id, from_id AS to_id, duration FROM Calls
)

SELECT
  from_id AS person1,
  to_id AS person2,
  COUNT(*) AS call_count,
  SUM(duration) AS total_duration
FROM all_calls
WHERE from_id < to_id
GROUP BY 1,2
;
```

## 3. Pandas Schema

```python
import pandas as pd
import numpy as np

data = [[1, 2, 59], [2, 1, 11], [1, 3, 20], [3, 4, 100], [3, 4, 200], [3, 4, 200], [4, 3, 499]]
calls = pd.DataFrame(data, columns=['from_id', 'to_id', 'duration']).astype({'from_id':'Int64', 'to_id':'Int64', 'duration':'Int64'})
```

## 4. Pandas Results

```python
calls_2 = calls.rename(columns={'from_id': 'to_id', 'to_id': 'from_id'})
all_calls = pd.concat([calls, calls_2])
all_calls_agg = all_calls.groupby(['from_id', 'to_id'])['duration'].\
    agg(['count', 'sum']).reset_index()
results = all_calls_agg[all_calls_agg.from_id < all_calls_agg.to_id].rename(
    columns={
        'from_id': 'person1',
        'to_id': 'person2',
        'count': 'call_count',
        'sum': 'total_duration'
    }
)
print(results)
```