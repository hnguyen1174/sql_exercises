## Tables and Prompts:

The Employee table holds all employees including their managers. Every employee has an Id, and there is also a column for the manager Id.

Given the Employee table, write a SQL query that finds out employees who earn more than their managers. For the above table, Joe is the only employee who earns more than his manager.

```
+----------+
| Employee |
+----------+
| Joe      |
+----------+
```

```sql
CREATE TABLE Employee (
  Id INT,
  Name VARCHAR(5),
  Salary INT,
  ManagerId INT
);
  
INSERT INTO Employee VALUES 
(1, 'Joe', 70000, 3),
(2, 'Henry', 80000, 4),
(3, 'Sam', 60000, NULL),
(4, 'Max', 90000, NULL);
```

## Answers

```sql
SELECT e1.Name AS Employee
FROM Employee e1
LEFT JOIN Employee e2
ON e1.ManagerId = e2.Id
WHERE e1.Salary > e2.Salary
```

