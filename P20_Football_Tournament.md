## Tables and Schema

```
Table: Teams

+---------------+----------+
| Column Name   | Type     |
+---------------+----------+
| team_id       | int      |
| team_name     | varchar  |
+---------------+----------+
team_id is the primary key of this table.
Each row of this table represents a single football team.
Table: Matches

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| match_id      | int     |
| host_team     | int     |
| guest_team    | int     | 
| host_goals    | int     |
| guest_goals   | int     |
+---------------+---------+
match_id is the primary key of this table.
Each row is a record of a finished match between two different teams. 
Teams host_team and guest_team are represented by their IDs in the teams table (team_id) and they scored host_goals and guest_goals goals respectively.
 

You would like to compute the scores of all teams after all matches. Points are awarded as follows:
A team receives three points if they win a match (Score strictly more goals than the opponent team).
A team receives one point if they draw a match (Same number of goals as the opponent team).
A team receives no points if they lose a match (Score less goals than the opponent team).
```

## Question 1: 

Write an SQL query that selects the team_id, team_name and num_points of each team in the tournament after all described matches. Result table should be ordered by num_points (decreasing order). In case of a tie, order the records by team_id (increasing order).

```sql
WITH host AS (
    SELECT 
        t.team_id AS team_id,
        t.team_name AS team_name,
        CASE WHEN host_goals > guest_goals THEN 3
        WHEN host_goals = guest_goals THEN 1
        ELSE 0 END AS num_points
    FROM Teams t 
    LEFT JOIN Matches m
    ON m.host_team = t.team_id
), guest AS (
    SELECT 
        t.team_id AS team_id,
        t.team_name AS team_name,
        CASE WHEN guest_goals > host_goals THEN 3
        WHEN host_goals = guest_goals THEN 1
        ELSE 0 END AS num_points
    FROM Teams t
    LEFT JOIN Matches m
    ON m.guest_team = t.team_id
)


SELECT 
    team_id, team_name,
    SUM(num_points) AS num_points
FROM (
SELECT * FROM host
UNION ALL
SELECT * FROM guest
) TMP
GROUP BY team_id, team_name
ORDER BY num_points DESC, team_id ASC
```

