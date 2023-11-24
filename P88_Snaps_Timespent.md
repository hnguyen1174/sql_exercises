
## Prompt

Assume you're given tables with information on Snapchat users, including their ages and time spent sending and opening snaps.

Write a query to obtain a breakdown of the time spent sending vs. opening snaps as a percentage of total time spent on these activities grouped by age group. Round the percentage to 2 decimal places in the output.

Notes:

Calculate the following percentages:
* time spent sending / (Time spent sending + Time spent opening)
* Time spent opening / (Time spent sending + Time spent opening)

To avoid integer division in percentages, multiply by 100.0 and not 100.


#### SQL Solution

```sql
WITH user_level AS (
  SELECT
    user_id,
    SUM(time_spent) AS total_timespent,
    SUM(CASE WHEN activity_type = 'send' THEN time_spent ELSE 0 END) AS send_timespent,
    SUM(CASE WHEN activity_type = 'open' THEN time_spent ELSE 0 END) AS open_timespent
  FROM activities
  WHERE activity_type IN ('send', 'open')
  GROUP BY 1
),

age_group_level AS (
  SELECT
    age_bucket,
    SUM(total_timespent) AS total_timespent,
    SUM(send_timespent) AS send_timespent,
    SUM(open_timespent) AS open_timespent
  FROM age_breakdown a
  LEFT JOIN user_level u
    ON a.user_id = u.user_id
  GROUP BY 1
)

SELECT
  age_bucket,
  ROUND(send_timespent * 100 / total_timespent, 2) AS send_perc,
  ROUND(open_timespent * 100 / total_timespent, 2) AS open_perc
FROM age_group_level
ORDER BY age_bucket
;
```

#### Python Solution

```python
send_open_act = activities[activities['activity_type'].isin(['send', 'open'])]
send_open_act['send_timespent'] = np.where(send_open_act['activity_type'] == 'send', time_spent, 0)
send_open_act['open_timespent'] = np.where(send_open_act['activity_type'] == 'open', time_spent, 0)

send_open_age_group = pd.merge(
    age_breakdown,
    send_open_act,
    how='left',
    on='user_id'
)

send_open_age_group.groupby('age_breakdown')[['send_timespent', 'open_timespent', 'time_spent']].sum().reset_index()
send_open_age_group['send_perc'] = send_open_age_group['send_timespent'] * 100 / send_open_age_group['time_spent'] * 100
send_open_age_group['send_perc'] = send_open_age_group['send_perc'].round(2)
send_open_age_group['open_perc'] = send_open_age_group['open_timespent'] * 100 / send_open_age_group['time_spent'] * 100
send_open_age_group['open_perc'] = send_open_age_group['open_perc'].round(2)

results = send_open_age_group[['age_breakdown', 'send_perc' , 'open_perc']].sort_values('age_breakdown')
```
