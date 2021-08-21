1. `ORDER BY` is executed after `GROUP BY`, so we can order by after group by calculation


```sql
SELECT c.Name, COUNT(v.id) AS numVotes
FROM Vote v
LEFT JOIN Candidate c
ON v.CandidateId = c.id
GROUP BY c.Name
ORDER BY numVotes DESC
```
