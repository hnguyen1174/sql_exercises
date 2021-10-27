```sql
CREATE TABLE student (
  name VARCHAR(10), 
  continent VARCHAR(10)
);

INSERT INTO student
VALUES 
('Jack', 'America'),
('Pascal', 'Europe'),
('Xi', 'Asia'),
('Jane', 'America');
```

Pivot the continent column in this table so that each name is sorted alphabetically and displayed underneath its corresponding continent. The output headers should be America, Asia and Europe respectively. It is guaranteed that the student number from America is no less than either Asia or Europe.
 
For the sample input, the output is:
 
 ```
| name   | continent |
|--------|-----------|
| Jack   | America   |
| Pascal | Europe    |
| Xi     | Asia      |
| Jane   | America   |

| America | Asia | Europe |
|---------|------|--------|
| Jack    | Xi   | Pascal |
| Jane    |      |        |
 ```

## Answer

```sql
-- Step 1: Assign an ID from 1 to n to students from the same continent
-- Step 2: Create 3 tables for America, Europe and Asia
-- Step 3: Join 3 tables by ID

WITH TMP AS (
  SELECT *,
  ROW_NUMBER() OVER (PARTITION BY continent ORDER BY name) AS id
  FROM student
)

SELECT America, Asia, Europe
FROM (
  SELECT id, name AS America
  FROM TMP 
  WHERE continent = 'America'
) t1
LEFT JOIN (
  SELECT id, name AS Asia
  FROM TMP 
  WHERE continent = 'Asia'
) t2
ON t1.id = t2.id
LEFT JOIN (
  SELECT id, name AS Europe
  FROM TMP 
  WHERE continent = 'Europe'
) t3
ON t1.id = t3.id
```

