## 1. SQL Schema

```sql
Create table If Not Exists Buses (bus_id int, arrival_time int);
Create table If Not Exists Passengers (passenger_id int, arrival_time int);
Truncate table Buses;
insert into Buses (bus_id, arrival_time) values ('1', '2');
insert into Buses (bus_id, arrival_time) values ('2', '4');
insert into Buses (bus_id, arrival_time) values ('3', '7');
Truncate table Passengers;
insert into Passengers (passenger_id, arrival_time) values ('11', '1');
insert into Passengers (passenger_id, arrival_time) values ('12', '5');
insert into Passengers (passenger_id, arrival_time) values ('13', '6');
insert into Passengers (passenger_id, arrival_time) values ('14', '7');
```

```
Buses and passengers arrive at the LeetCode station. If a bus arrives at the station at time tbus and a passenger arrived at time tpassenger where tpassenger <= tbus and the passenger did not catch any bus, the passenger will use that bus.

Write a solution to report the number of users that used each bus.

Return the result table ordered by bus_id in ascending order.

The result format is in the following example.

 

Example 1:

Input: 
Buses table:
+--------+--------------+
| bus_id | arrival_time |
+--------+--------------+
| 1      | 2            |
| 2      | 4            |
| 3      | 7            |
+--------+--------------+
Passengers table:
+--------------+--------------+
| passenger_id | arrival_time |
+--------------+--------------+
| 11           | 1            |
| 12           | 5            |
| 13           | 6            |
| 14           | 7            |
+--------------+--------------+
Output: 
+--------+----------------+
| bus_id | passengers_cnt |
+--------+----------------+
| 1      | 1              |
| 2      | 0              |
| 3      | 3              |
+--------+----------------+
Explanation: 
- Passenger 11 arrives at time 1.
- Bus 1 arrives at time 2 and collects passenger 11.

- Bus 2 arrives at time 4 and does not collect any passengers.

- Passenger 12 arrives at time 5.
- Passenger 13 arrives at time 6.
- Passenger 14 arrives at time 7.
- Bus 3 arrives at time 7 and collects passengers 12, 13, and 14.
```

## 2. SQL Answer

```sql
WITH t AS (
  SELECT  
    passenger_id, 
    MIN(b.arrival_time) AS arrival_time
  FROM Passengers p
  JOIN Buses b
    ON p.arrival_time <= b.arrival_time
  GROUP BY passenger_id
)

SELECT 
  bus_id, 
  COUNT(t.arrival_time) AS passengers_cnt
FROM Buses b
LEFT JOIN t
  ON b.arrival_time = t.arrival_time
GROUP BY bus_id
ORDER BY bus_id
;
```