## Tables and Schema:

```sql
CREATE TABLE Tasks (
  task_id INT,
  subtasks_count INT
);

INSERT INTO Tasks VALUES 
(1, 3),
(2, 2),
(3, 4);

CREATE TABLE Executed (
  task_id INT,
  subtask_id INT
);

INSERT INTO Executed VALUES
(1, 2),
(3, 1),
(3, 2),
(3, 3),
(3, 4);
```

Write an SQL query to report the IDs of the missing subtasks for each task_id.

Return the result table in any order.

The query result format is in the following example.

```
Tasks table:
+---------+----------------+
| task_id | subtasks_count |
+---------+----------------+
| 1       | 3              |
| 2       | 2              |
| 3       | 4              |
+---------+----------------+
Executed table:
+---------+------------+
| task_id | subtask_id |
+---------+------------+
| 1       | 2          |
| 3       | 1          |
| 3       | 2          |
| 3       | 3          |
| 3       | 4          |
+---------+------------+
Output: 
+---------+------------+
| task_id | subtask_id |
+---------+------------+
| 1       | 1          |
| 1       | 3          |
| 2       | 1          |
| 2       | 2          |
+---------+------------+
```

## Answer

```sql
WITH RECURSIVE CTE AS (
  SELECT 
    task_id,
    subtasks_count,
    1 AS id
  FROM Tasks
  UNION
  SELECT 
    task_id,
    subtasks_count,
    id + 1
  FROM CTE WHERE id < subtasks_count
)

SELECT 
  task_id, 
  id AS subtask_id 
FROM CTE
WHERE (task_id, id) NOT IN (
  SELECT task_id, subtask_id 
  FROM 
  Executed
)
```
