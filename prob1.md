## Problem 1: Question with Highest Answer Rate

### Problem

Get the highest answer rate question from a table survey_log with these columns:

* `id`: user id
* `action`: action has these kind of values: "show", "answer", "skip"
* `question_id`: id of question
* `answer_id`: is not null when action column is "answer", while is null for "show" and "skip"
* `q_num`: the numeral order of the question in current session
* `timestamp`: timestamp of action

### Prompt

Write a sql query to identify the question which has the highest answer rate.

### Input and Output

```
Input:
+------+-----------+--------------+------------+-----------+------------+
| id   | action    | question_id  | answer_id  | q_num     | timestamp  |
+------+-----------+--------------+------------+-----------+------------+
| 5    | show      | 285          | null       | 1         | 123        |
| 5    | answer    | 285          | 124124     | 1         | 124        |
| 5    | show      | 369          | null       | 2         | 125        |
| 5    | skip      | 369          | null       | 2         | 126        |
+------+-----------+--------------+------------+-----------+------------+
Output:
+-------------+
| survey_log  |
+-------------+
|    285      |
+-------------+
```

### Answer

```sql
SELECT question_id AS survey_log
FROM survey_log
WHERE answer_id IS NOT NULL
GROUP BY question_id
ORDER BY COUNT(answer_id) DESC
LIMIT 1
```