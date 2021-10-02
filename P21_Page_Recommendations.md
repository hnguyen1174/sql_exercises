## Tables and Schema

```
Table: Friendship

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user1_id      | int     |
| user2_id      | int     |
+---------------+---------+
(user1_id, user2_id) is the primary key for this table.
Each row of this table indicates that there is a friendship relation between user1_id and user2_id.
 

Table: Likes

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| user_id     | int     |
| page_id     | int     |
+-------------+---------+
(user_id, page_id) is the primary key for this table.
Each row of this table indicates that user_id likes page_id.
```

## Question 1: 

Write an SQL query to recommend pages to the user with user_id = 1 using the pages that your friends liked. It should not recommend pages you already liked.

Return result table in any order without duplicates.

```sql
# Write your MySQL query statement below

-- Step 1: Identify who user 1 is friend with
-- Step 2: Identify which page the friends like
-- Step 3: Distinct

SELECT DISTINCT page_id AS recommended_page
FROM Likes
WHERE user_id IN (
    SELECT user2_id AS friends FROM Friendship WHERE user1_id = 1
    UNION
    SELECT user1_id AS friends FROM Friendship WHERE user2_id = 1
)
AND page_id NOT IN (
    SELECT page_id FROM Likes WHERE user_id = 1
)
ORDER BY recommended_page
```
