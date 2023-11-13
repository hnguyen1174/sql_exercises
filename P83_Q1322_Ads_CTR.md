## 1. SQL Schema

```sql
Create table If Not Exists Ads (ad_id int, user_id int, action ENUM('Clicked', 'Viewed', 'Ignored'));
Truncate table Ads;
insert into Ads (ad_id, user_id, action) values ('1', '1', 'Clicked');
insert into Ads (ad_id, user_id, action) values ('2', '2', 'Clicked');
insert into Ads (ad_id, user_id, action) values ('3', '3', 'Viewed');
insert into Ads (ad_id, user_id, action) values ('5', '5', 'Ignored');
insert into Ads (ad_id, user_id, action) values ('1', '7', 'Ignored');
insert into Ads (ad_id, user_id, action) values ('2', '7', 'Viewed');
insert into Ads (ad_id, user_id, action) values ('3', '5', 'Clicked');
insert into Ads (ad_id, user_id, action) values ('1', '4', 'Viewed');
insert into Ads (ad_id, user_id, action) values ('2', '11', 'Viewed');
insert into Ads (ad_id, user_id, action) values ('1', '2', 'Clicked');
```

```
A company is running Ads and wants to calculate the performance of each Ad.

Performance of the Ad is measured using Click-Through Rate (CTR) where:


Write a solution to find the ctr of each Ad. Round ctr to two decimal points.

Return the result table ordered by ctr in descending order and by ad_id in ascending order in case of a tie.

The result format is in the following example.

 

Example 1:

Input: 
Ads table:
+-------+---------+---------+
| ad_id | user_id | action  |
+-------+---------+---------+
| 1     | 1       | Clicked |
| 2     | 2       | Clicked |
| 3     | 3       | Viewed  |
| 5     | 5       | Ignored |
| 1     | 7       | Ignored |
| 2     | 7       | Viewed  |
| 3     | 5       | Clicked |
| 1     | 4       | Viewed  |
| 2     | 11      | Viewed  |
| 1     | 2       | Clicked |
+-------+---------+---------+
Output: 
+-------+-------+
| ad_id | ctr   |
+-------+-------+
| 1     | 66.67 |
| 3     | 50.00 |
| 2     | 33.33 |
| 5     | 0.00  |
+-------+-------+
Explanation: 
for ad_id = 1, ctr = (2/(2+1)) * 100 = 66.67
for ad_id = 2, ctr = (1/(1+2)) * 100 = 33.33
for ad_id = 3, ctr = (1/(1+1)) * 100 = 50.00
for ad_id = 5, ctr = 0.00, Note that ad_id = 5 has no clicks or views.
Note that we do not care about Ignored Ads.
```

## 2. SQL Answer

```
WITH num_click_view AS (
  SELECT
    ad_id,
    SUM(CASE WHEN action = 'Clicked' THEN 1 ELSE 0 END) AS num_click,
    SUM(CASE WHEN action = 'Viewed' THEN 1 ELSE 0 END) AS num_view
  FROM Ads
  GROUP BY ad_id
)

SELECT
  ad_id,
  CASE
    WHEN num_click + num_view = 0 THEN 0
    ELSE ROUND(num_click * 100.0 / (num_click + num_view), 2)
  END AS ctr
FROM num_click_view
ORDER BY ctr DESC, ad_id ASC
;
```