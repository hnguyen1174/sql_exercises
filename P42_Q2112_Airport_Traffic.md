## 1. Table Schema

```sql
Create table If Not Exists Flights (departure_airport int, arrival_airport int, flights_count int);
Truncate table Flights;
insert into Flights (departure_airport, arrival_airport, flights_count) values ('1', '2', '4');
insert into Flights (departure_airport, arrival_airport, flights_count) values ('2', '1', '5');
insert into Flights (departure_airport, arrival_airport, flights_count) values ('2', '4', '5');
```

```
Write a solution to report the ID of the airport with the most traffic. The airport with the most traffic is the airport that has the largest total number of flights that either departed from or arrived at the airport. If there is more than one airport with the most traffic, report them all.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Flights table:
+-------------------+-----------------+---------------+
| departure_airport | arrival_airport | flights_count |
+-------------------+-----------------+---------------+
| 1                 | 2               | 4             |
| 2                 | 1               | 5             |
| 2                 | 4               | 5             |
+-------------------+-----------------+---------------+
Output: 
+------------+
| airport_id |
+------------+
| 2          |
+------------+
Explanation: 
Airport 1 was engaged with 9 flights (4 departures, 5 arrivals).
Airport 2 was engaged with 14 flights (10 departures, 4 arrivals).
Airport 4 was engaged with 5 flights (5 arrivals).
The airport with the most traffic is airport 2.
Example 2:

Input: 
Flights table:
+-------------------+-----------------+---------------+
| departure_airport | arrival_airport | flights_count |
+-------------------+-----------------+---------------+
| 1                 | 2               | 4             |
| 2                 | 1               | 5             |
| 3                 | 4               | 5             |
| 4                 | 3               | 4             |
| 5                 | 6               | 7             |
+-------------------+-----------------+---------------+
Output: 
+------------+
| airport_id |
+------------+
| 1          |
| 2          |
| 3          |
| 4          |
+------------+
Explanation: 
Airport 1 was engaged with 9 flights (4 departures, 5 arrivals).
Airport 2 was engaged with 9 flights (5 departures, 4 arrivals).
Airport 3 was engaged with 9 flights (5 departures, 4 arrivals).
Airport 4 was engaged with 9 flights (4 departures, 5 arrivals).
Airport 5 was engaged with 7 flights (7 departures).
Airport 6 was engaged with 7 flights (7 arrivals).
The airports with the most traffic are airports 1, 2, 3, and 4.
```

## 2. SQL Answer

```sql
WITH cte AS (
  SELECT departure_airport AS airport1, arrival_airport AS airport2, flights_count FROM Flights
  UNION
  SELECT arrival_airport AS airport1, departure_airport AS airport2, flights_count FROM Flights
),

cte2 AS (
  SELECT
    airport1,
    RANK() OVER (ORDER BY SUM(flights_count) DESC) AS rn
  FROM cte
  GROUP BY 1
)

SELECT DISTINCT airport1 AS airport_id FROM cte2 WHERE rn = 1;
````