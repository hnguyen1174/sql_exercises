```sql
CREATE TABLE Trips (
  Id INT,
  Client_Id INT,
  Driver_Id INT,
  City_Id INT,
  Status VARCHAR(20),
  Request_at DATE
);
  
INSERT INTO Trips VALUES 
(1, 1, 10, 1, 'completed', '2013-10-01'),
(2, 2, 11, 1, 'cancelled_by_driver', '2013-10-01'),
(3, 3, 12, 6, 'completed', '2013-10-01'),
(4, 4, 13, 6, 'cancelled_by_client', '2013-10-01'),
(5, 1, 10, 1, 'completed', '2013-10-02'),
(6, 2, 11, 6, 'completed', '2013-10-02'),
(7, 3, 12, 6, 'completed', '2013-10-02'),
(8, 2, 12, 12, 'completed', '2013-10-03'),
(9, 3, 10, 12, 'completed', '2013-10-03'),
(10, 4, 13, 12, 'cancelled_by_driver', '2013-10-03');



CREATE TABLE Users (
  Users_Id INT,
  Banned VARCHAR(5),
  Role VARCHAR(10)
);

INSERT INTO Users VALUES 
(1, 'No', 'client'),
(2, 'Yes', 'client'),
(3, 'No', 'client'),
(4, 'No', 'client'),
(10, 'No', 'driver'),
(11, 'No', 'driver'),
(12, 'No', 'driver'),
(13, 'No', 'driver');

```

```sql
SELECT Request_at AS Day,
ROUND(cancelledTrips / totalTrips, 2) AS 'Cancellation Rate'

FROM (
  SELECT Request_at, COUNT(*) AS totalTrips,
SUM(CASE WHEN Status = 'completed' THEN 0 ELSE 1 END) AS cancelledTrips
FROM Trips 
WHERE Client_Id NOT IN (SELECT Users_Id FROM Users WHERE Banned = 'Yes')
AND Driver_Id NOT IN (SELECT Users_Id FROM Users WHERE Banned = 'Yes')
GROUP BY Request_at
) T
WHERE Request_at BETWEEN '2013-10-01' AND '2013-10-03'
```

