## Tables and Schema

```
Table: Movies

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| movie_id      | int     |
| title         | varchar |
+---------------+---------+
movie_id is the primary key for this table.
title is the name of the movie.
Table: Users

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user_id       | int     |
| name          | varchar |
+---------------+---------+
user_id is the primary key for this table.
Table: Movie_Rating

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| movie_id      | int     |
| user_id       | int     |
| rating        | int     |
| created_at    | date    |
+---------------+---------+
(movie_id, user_id) is the primary key for this table.
This table contains the rating of a movie by a user in their review.
created_at is the user's review date. 
```

```
Movies table:
+-------------+--------------+
| movie_id    |  title       |
+-------------+--------------+
| 1           | Avengers     |
| 2           | Frozen 2     |
| 3           | Joker        |
+-------------+--------------+

Users table:
+-------------+--------------+
| user_id     |  name        |
+-------------+--------------+
| 1           | Daniel       |
| 2           | Monica       |
| 3           | Maria        |
| 4           | James        |
+-------------+--------------+

Movie_Rating table:
+-------------+--------------+--------------+-------------+
| movie_id    | user_id      | rating       | created_at  |
+-------------+--------------+--------------+-------------+
| 1           | 1            | 3            | 2020-01-12  |
| 1           | 2            | 4            | 2020-02-11  |
| 1           | 3            | 2            | 2020-02-12  |
| 1           | 4            | 1            | 2020-01-01  |
| 2           | 1            | 5            | 2020-02-17  | 
| 2           | 2            | 2            | 2020-02-01  | 
| 2           | 3            | 2            | 2020-03-01  |
| 3           | 1            | 3            | 2020-02-22  | 
| 3           | 2            | 4            | 2020-02-25  | 
+-------------+--------------+--------------+-------------+

Result table:
+--------------+
| results      |
+--------------+
| Daniel       |
| Frozen 2     |
+--------------+

Daniel and Monica have rated 3 movies ("Avengers", "Frozen 2" and "Joker") but Daniel is smaller lexicographically.
Frozen 2 and Joker have a rating average of 3.5 in February but Frozen 2 is smaller lexicographically.
```

## Question 1: SQL

Write the following SQL query:

Find the name of the user who has rated the greatest number of movies.
In case of a tie, return lexicographically smaller user name.

Find the movie name with the highest average rating in February 2020.
In case of a tie, return lexicographically smaller movie name.

The query is returned in 2 rows, the query result format is in the following example:

**ANSWER 1**

```sql

WITH CTE1 AS (
SELECT name AS results
FROM (
SELECT 
    m.user_id AS user_id, 
    u.name AS name,
    COUNT(DISTINCT m.movie_id) AS count_movies
FROM Movie_Rating m
LEFT JOIN Users u
ON m.user_id = u.user_id
GROUP BY m.user_id
) TMP
ORDER BY count_movies DESC, name ASC
LIMIT 1
), 
CTE2 AS (
SELECT 
    title AS results
FROM (
SELECT 
    m.movie_id AS movie_id,
    o.title AS title,
    AVG(rating) AS average_rating
FROM Movie_Rating m
LEFT JOIN Movies o
ON m.movie_id = o.movie_id
WHERE YEAR(m.created_at) = 2020
AND MONTH(m.created_at) = 2
GROUP BY m.movie_id
) TMP
ORDER BY average_rating DESC, title ASC
LIMIT 1
)

SELECT results FROM CTE1
UNION 
SELECT results FROM CTE2
```

**ANSWER 2**

```sql
(SELECT
  u.name AS results
FROM movie_rating m
JOIN users u USING (user_id)
GROUP BY user_id
ORDER BY COUNT(m.user_id) DESC, u.name LIMIT 1
)

UNION

(SELECT 
  m.title AS results 
FROM movie_rating r
LEFT JOIN movies m USING (movie_id)
WHERE r.created_at LIKE '2020-02%'
GROUP BY r.movie_id
GROUP BY r.movie_id
ORDER BY AVG(rating) DESC, title LIMIT 1
)
```
