## Prompts and Tables

```sql
CREATE TABLE Logs (
  id INT,
  num VARCHAR(5));
  
INSERT INTO Logs VALUES
(1, 1),
(2, 1),
(3, 1),
(4, 2),
(5, 1),
(6, 2),
(7, 2);
```

Write an SQL query to find all numbers that appear at least three times consecutively.

Return the result table in any order.

The query result format is in the following example:

```sql
Logs table:
+----+-----+
| Id | Num |
+----+-----+
| 1  | 1   |
| 2  | 1   |
| 3  | 1   |
| 4  | 2   |
| 5  | 1   |
| 6  | 2   |
| 7  | 2   |
+----+-----+

Result table:
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
1 is the only number that appears consecutively for at least three times.
```

### Answwer

```sql
-- Step 1: Create 2 lags by ID (using windows function)
-- Step 2: Filter by ID (Num = Lag1 = Lag2)

SELECT DISTINCT Num AS ConsecutiveNums FROM (
  SELECT Id, Num,
  LAG(Num, 1) OVER (ORDER BY Id) AS Lag1,
  LAG(Num, 2) OVER (ORDER BY Id) AS Lag2
  FROM Logs
) T
WHERE Num = Lag1
AND Lag1 = Lag2;
```

```sql
Result table:
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
```

