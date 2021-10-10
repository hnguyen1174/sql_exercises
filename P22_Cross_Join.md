## Tables and Schema

```sql
CREATE TABLE Experiments (
  experiment_id INT,
  platform VARCHAR(10),
  experiment_name VARCHAR(20)
);

INSERT INTO Experiments
VALUES 
(4, 'IOS', 'Programming'),
(13, 'IOS', 'Sports'),
(14, 'Android', 'Reading'),
(8, 'Web', 'Reading'),
(12, 'Web', 'Reading'),
(18, 'Web', 'Programming');
```

```
--------------
SELECT * FROM Experiments
--------------

+---------------+----------+-----------------+
| experiment_id | platform | experiment_name |
+---------------+----------+-----------------+
|             4 | IOS      | Programming     |
|            13 | IOS      | Sports          |
|            14 | Android  | Reading         |
|             8 | Web      | Reading         |
|            12 | Web      | Reading         |
|            18 | Web      | Programming     |
+---------------+----------+-----------------+
6 rows in set (0.00 sec)
```

## Answer:

```sql
SELECT 
  e1.platform, 
  e1.experiment_name,
  COUNT(DISTINCT experiment_id) as num_experiments
FROM 
(
SELECT * FROM 
  (SELECT DISTINCT platform FROM Experiments) t1,
  (SELECT DISTINCT experiment_name FROM Experiments) t2
) e1
LEFT JOIN Experiments e2
ON e1.platform = e2.platform
AND e1.experiment_name = e2.experiment_name
GROUP BY e1.platform, e1.experiment_name
```

```
+----------+-----------------+-----------------+
| platform | experiment_name | num_experiments |
+----------+-----------------+-----------------+
| Android  | Programming     |               0 |
| Android  | Reading         |               1 |
| Android  | Sports          |               0 |
| IOS      | Programming     |               1 |
| IOS      | Reading         |               0 |
| IOS      | Sports          |               1 |
| Web      | Programming     |               1 |
| Web      | Reading         |               2 |
| Web      | Sports          |               0 |
+----------+-----------------+-----------------+
9 rows in set (0.01 sec)
```
