## Table and Schema

```sql
CREATE TABLE Student (
  student_id INT, 
  student_name VARCHAR(20)
);

INSERT INTO Student
VALUES 
(1, 'Daniel'),
(2, 'Jade'),
(3, 'Stella'),
(4, 'Jonathan'),
(5, 'Will');

CREATE TABLE Exam (
  exam_id INT, 
  student_id INT,
  score INT
);

INSERT INTO Exam
VALUES 
(10, 1, 70),
(10, 2, 80),
(10, 3, 90),
(20, 1, 80),
(30, 1, 70),
(30, 3, 80),
(30, 4, 90),
(40, 1, 60),
(40, 2, 70),
(40, 4, 80);
```

```
Student table:
+-------------+---------------+
| student_id  | student_name  |
+-------------+---------------+
| 1           | Daniel        |
| 2           | Jade          |
| 3           | Stella        |
| 4           | Jonathan      |
| 5           | Will          |
+-------------+---------------+

Exam table:
+------------+--------------+-----------+
| exam_id    | student_id   | score     |
+------------+--------------+-----------+
| 10         |     1        |    70     |
| 10         |     2        |    80     |
| 10         |     3        |    90     |
| 20         |     1        |    80     |
| 30         |     1        |    70     |
| 30         |     3        |    80     |
| 30         |     4        |    90     |
| 40         |     1        |    60     |
| 40         |     2        |    70     |
| 40         |     4        |    80     |
+------------+--------------+-----------+
```

A "quite" student is the one who took at least one exam and didn't score neither the high score nor the low score.

Write an SQL query to report the students (student_id, student_name) being "quiet" in ALL exams.

Don't return the student who has never taken any exam. Return the result table ordered by student_id.

The query result format is in the following example.

## SQL Answer

```sql
SELECT DISTINCT e.student_id, s.student_name
FROM Exam e
LEFT JOIN Student s
ON e.student_id = s.student_id
WHERE e.student_id NOT IN (
SELECT DISTINCT student_id FROM (
SELECT 
  student_id,
  exam_id,
  score,
  RANK() OVER (PARTITION BY exam_id ORDER by SCORE DESC) as rank_desc,
  RANK() OVER (PARTITION BY exam_id ORDER by SCORE ASC) as rank_asc
FROM Exam
) tmp WHERE rank_desc = 1 OR rank_asc = 1) 
ORDER BY e.student_id
```
