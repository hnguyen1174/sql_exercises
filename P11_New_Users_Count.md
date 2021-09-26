## Schema and Tables

Write an SQL query that reports for every date within at most 90 days from today, the number of users that logged in for the first time on that date. Assume today is 2019-06-30.

```sql
CREATE TABLE Traffic (
  user_id INT,
  activity VARCHAR(10),
  activity_date DATE
);
  
INSERT INTO Traffic VALUES
(1, 'login', '2019-05-01'),
(1, 'homepage', '2019-05-01'),
(1, 'logout', '2019-05-01'),
(2, 'login', '2019-06-21'),
(2, 'logout', '2019-06-21'),
(3, 'login', '2019-01-01'),
(3, 'jobs', '2019-01-01'),
(3, 'logout', '2019-01-01'),
(4, 'login', '2019-06-21'),
(4, 'groups', '2019-06-21'),
(4, 'logout', '2019-06-21'),
(5, 'login', '2019-03-01'),
(5, 'logout', '2019-03-01'),
(5, 'login', '2019-06-21'),
(5, 'logout', '2019-06-21');
```

## Answer

```sql
SELECT 
  login_date, 
  COUNT(DISTINCT user_id) AS user_count 
FROM (
  SELECT user_id, min(activity_date) AS login_date
  FROM Traffic
  WHERE activity = 'login'
  GROUP BY user_id
  HAVING login_date BETWEEN DATE_SUB("2019-06-30", INTERVAL 90 DAY) AND DATE('2019-06-30')
) TMP
GROUP BY login_date
```
