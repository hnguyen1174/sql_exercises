## 1. SQL Schema

```sql
Create Table If Not Exists Variables (name varchar(3), value int);
Create Table If Not Exists Expressions (left_operand varchar(3), operator ENUM('>', '<', '='), right_operand varchar(3));
Truncate table Variables;
insert into Variables (name, value) values ('x', '66');
insert into Variables (name, value) values ('y', '77');
Truncate table Expressions;
insert into Expressions (left_operand, operator, right_operand) values ('x', '>', 'y');
insert into Expressions (left_operand, operator, right_operand) values ('x', '<', 'y');
insert into Expressions (left_operand, operator, right_operand) values ('x', '=', 'y');
insert into Expressions (left_operand, operator, right_operand) values ('y', '>', 'x');
insert into Expressions (left_operand, operator, right_operand) values ('y', '<', 'x');
insert into Expressions (left_operand, operator, right_operand) values ('x', '=', 'x');
```

```
Evaluate the boolean expressions in Expressions table.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Variables table:
+------+-------+
| name | value |
+------+-------+
| x    | 66    |
| y    | 77    |
+------+-------+
Expressions table:
+--------------+----------+---------------+
| left_operand | operator | right_operand |
+--------------+----------+---------------+
| x            | >        | y             |
| x            | <        | y             |
| x            | =        | y             |
| y            | >        | x             |
| y            | <        | x             |
| x            | =        | x             |
+--------------+----------+---------------+
Output: 
+--------------+----------+---------------+-------+
| left_operand | operator | right_operand | value |
+--------------+----------+---------------+-------+
| x            | >        | y             | false |
| x            | <        | y             | true  |
| x            | =        | y             | false |
| y            | >        | x             | true  |
| y            | <        | x             | false |
| x            | =        | x             | true  |
+--------------+----------+---------------+-------+
Explanation: 
As shown, you need to find the value of each boolean expression in the table using the variables table.
```

## 2. SQL Answer

```sql
SELECT 
  e.left_operand, 
  e.operator, 
  e.right_operand,
  CASE
    WHEN e.operator = '<' AND v1.value < v2.value THEN 'true'
    WHEN e.operator = '=' AND v1.value = v2.value THEN 'true'
    WHEN e.operator = '>' AND v1.value > v2.value THEN 'true'
    ELSE 'false'
  END AS value
FROM Expressions e
JOIN Variables v1
  ON e.left_operand = v1.name
JOIN Variables v2
  ON e.right_operand = v2.name
;
```