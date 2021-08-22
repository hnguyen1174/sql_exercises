```sql
CREATE TABLE Activity (
  player_id int,
  device_id int,
  event_date date,
  games_played int
);

INSERT INTO Activity
VALUES 
(1, 2, '2016-03-01', 5),
(1, 2, '2016-03-02', 6),
(2, 3, '2017-06-25', 1),
(3, 1, '2016-03-01', 0),
(3, 4, '2016-07-03', 5);

SELECT * FROM Activity;
```

We define the install date of a player to be the first login day of that player.

We also define day 1 retention of some date X to be the number of players whose install date is X and they logged back in on the day right after X, divided by the number of players whose install date is X, rounded to 2 decimal places.

Write an SQL query that reports for each install date, the number of players that installed the game on that day and the day 1 retention.

The query result format is in the following example:

```
Activity table:
+-----------+-----------+------------+--------------+
| player_id | device_id | event_date | games_played |
+-----------+-----------+------------+--------------+
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-03-02 | 6            |
| 2         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-01 | 0            |
| 3         | 4         | 2016-07-03 | 5            |
+-----------+-----------+------------+--------------+

Result table:
+------------+----------+----------------+
| install_dt | installs | Day1_retention |
+------------+----------+----------------+
| 2016-03-01 | 2        | 0.50           |
| 2017-06-25 | 1        | 0.00           |
+------------+----------+----------------+
Player 1 and 3 installed the game on 2016-03-01 but only player 1 logged back in on 2016-03-02 so the day 1 retention of 2016-03-01 is 1 / 2 = 0.50
Player 2 installed the game on 2017-06-25 but didn't log back in on 2017-06-26 so the day 1 retention of 2017-06-25 is 0 / 1 = 0.00
```

```sql
-- Step 1: Identify installation dates using windows function
-- Step 2: Lead the event_date, and then create a column called 'retented' if datediff of lead_event_date and event_date is 1
-- Step 3: Analytics


SELECT event_date AS install_dt,
SUM(installation) AS installs,
ROUND(SUM(retention) / SUM(installation), 2) AS Day1_retention

FROM 
(
SELECT player_id, event_date, 
CASE WHEN (RANK() OVER (PARTITION BY player_id ORDER BY event_date) = 1)
THEN 1 ELSE 0 END AS installation,
CASE WHEN (DATEDIFF((LEAD(event_date, 1) OVER (PARTITION BY player_id ORDER BY event_date)), event_date) = 1) THEN 1 ELSE 0 END AS retention
FROM Activity
) T
WHERE installation = 1
GROUP BY event_date
ORDER BY install_dt
```

