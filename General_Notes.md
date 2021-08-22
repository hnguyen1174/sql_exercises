1. `ORDER BY` is executed after `GROUP BY`, so we can order by after group by calculation


  ```sql
  SELECT c.Name, COUNT(v.id) AS numVotes
  FROM Vote v
  LEFT JOIN Candidate c
  ON v.CandidateId = c.id
  GROUP BY c.Name
  ORDER BY numVotes DESC
  ```
  
2. Use `coalesce` in the  `WHERE` clause to keep NULL value in the final result


  ```sql
  select e.name, b.bonus
  from Employee e
  left join Bonus b
  on e.empId = b.empId
  where coalesce(b.bonus, 0) < 1000
  ```
3. `WHERE` is executed before column creation, so we cannot create an alias and filter by `WHERE` in the same statement.
