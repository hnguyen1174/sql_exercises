## 1. SQL Schema

```sql
Create table If Not Exists Matches (player_id int, match_day date, result ENUM('Win', 'Draw', 'Lose'));
Truncate table Matches;
insert into Matches (player_id, match_day, result) values ('1', '2022-01-17', 'Win');
insert into Matches (player_id, match_day, result) values ('1', '2022-01-18', 'Win');
insert into Matches (player_id, match_day, result) values ('1', '2022-01-25', 'Win');
insert into Matches (player_id, match_day, result) values ('1', '2022-01-31', 'Draw');
insert into Matches (player_id, match_day, result) values ('1', '2022-02-08', 'Win');
insert into Matches (player_id, match_day, result) values ('2', '2022-02-06', 'Lose');
insert into Matches (player_id, match_day, result) values ('2', '2022-02-08', 'Lose');
insert into Matches (player_id, match_day, result) values ('3', '2022-03-30', 'Win');
```

```
The winning streak of a player is the number of consecutive wins uninterrupted by draws or losses.

Write a solution to count the longest winning streak for each player.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Matches table:
+-----------+------------+--------+
| player_id | match_day  | result |
+-----------+------------+--------+
| 1         | 2022-01-17 | Win    |
| 1         | 2022-01-18 | Win    |
| 1         | 2022-01-25 | Win    |
| 1         | 2022-01-31 | Draw   |
| 1         | 2022-02-08 | Win    |
| 2         | 2022-02-06 | Lose   |
| 2         | 2022-02-08 | Lose   |
| 3         | 2022-03-30 | Win    |
+-----------+------------+--------+
Output: 
+-----------+----------------+
| player_id | longest_streak |
+-----------+----------------+
| 1         | 3              |
| 2         | 0              |
| 3         | 1              |
+-----------+----------------+
Explanation: 
Player 1:
From 2022-01-17 to 2022-01-25, player 1 won 3 consecutive matches.
On 2022-01-31, player 1 had a draw.
On 2022-02-08, player 1 won a match.
The longest winning streak was 3 matches.

Player 2:
From 2022-02-06 to 2022-02-08, player 2 lost 2 consecutive matches.
The longest winning streak was 0 matches.

Player 3:
On 2022-03-30, player 3 won a match.
The longest winning streak was 1 match.
```

## 2. SQL Answer

```sql
-- Essentially we need to do 2 different rankings -
-- rnk - for each player, each type of results gets its own continuous ranking
-- p_rnk - for each player, rank everything regardless of the result
-- Then it's easy to see that for each player, 
-- having the same diff (p_rnk - rnk) means they get the same result.

SELECT player_id, IFNULL(MAX(cnt), 0) AS longest_streak
FROM Matches m
LEFT JOIN (
  SELECT player_id, p_rnk - rnk AS diff, COUNT(p_rnk - rnk) AS cnt
  FROM (
    SELECT *,   
      CAST(RANK () OVER (PARTITION BY player_id, result ORDER BY match_day) AS SIGNED) AS rnk,
      CAST(RANK () OVER (PARTITION BY player_id ORDER BY match_day) AS SIGNED) AS p_rnk
    FROM Matches
  ) t
  WHERE result = 'Win'
  GROUP BY player_id, diff
) t2
  USING (player_id)
GROUP BY player_id
;
```