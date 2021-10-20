## Tables and Schema:

```sql
CREATE TABLE Accounts (
  id INT,
  name VARCHAR(20)
);

INSERT INTO Accounts
VALUES 
(1, 'Winston'),
(7, 'Jonathan');

CREATE TABLE Logins (
  id INT,
  login_date DATE
);

INSERT INTO Logins
VALUES 
(7, "2020-05-30"),
(1, "2020-05-30"),
(7, "2020-05-31"),
(7, "2020-06-01"),
(7, "2020-06-02"),
(7, "2020-06-02"),
(7, "2020-06-03"),
(1, "2020-06-07"),
(7, "2020-06-10");
```

```
Accounts table:
+----+----------+
| id | name     |
+----+----------+
| 1  | Winston  |
| 7  | Jonathan |
+----+----------+

Logins table:
+----+------------+
| id | login_date |
+----+------------+
| 7  | 2020-05-30 |
| 1  | 2020-05-30 |
| 7  | 2020-05-31 |
| 7  | 2020-06-01 |
| 7  | 2020-06-02 |
| 7  | 2020-06-02 |
| 7  | 2020-06-03 |
| 1  | 2020-06-07 |
| 7  | 2020-06-10 |
+----+------------+
```

Write an SQL query to find the id and the name of active users.

Active users are those who logged in to their accounts for 5 or more consecutive days.

Return the result table ordered by the id.

The query result format is in the following example:

```
+----+----------+
| id | name     |
+----+----------+
| 7  | Jonathan |
+----+----------+
```

## Approach 1: Self-Join

```sql
SELECT 
  DISTINCT l1.id,
  (SELECT name FROM Accounts WHERE id = l1.id) AS name
-- Self join
FROM Logins l1
JOIN Logins l2 
-- On id as key, datediff login date between 1 and 4 to address 5 consecutive days
ON l1.id = l2.id 
AND DATEDIFF(l2.login_date, l1.login_date) BETWEEN 1 AND 4
GROUP BY l1.id, l1.login_date
HAVING COUNT(DISTINCT l2.login_date) = 4
ORDER BY l1.id
```

```sql
SELECT *
FROM Accounts
WHERE id IN
    (SELECT 
      DISTINCT t1.id 
     FROM Logins t1 
     JOIN Logins t2 
     ON t1.id = t2.id AND DATEDIFF(t1.login_date, t2.login_date) BETWEEN 1 AND 4
     GROUP BY t1.id, t1.login_date
     HAVING COUNT(DISTINCT(t2.login_date)) = 4)
ORDER BY id
```



