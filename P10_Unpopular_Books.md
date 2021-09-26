## Schema and Tables

```sql
CREATE TABLE Books (
  book_id INT,
  name VARCHAR(20),
  available_from DATE
);
  
INSERT INTO Books VALUES
(1, "Kalila And Demna", "2010-01-01"),
(2, "28 Letters", "2012-05-12"),
(3, "The Hobbit", "2019-06-10"),
(4, "13 Reasons Why", "2019-06-01"),
(5, "The Hunger Games", "2008-09-21");

CREATE TABLE Orders (
  order_id INT,
  book_id INT,
  quantity INT,
  dispatch_date DATE
);

INSERT INTO Orders VALUES
(1, 1, 2, "2018-07-26"),
(2, 1, 1, "2018-11-05"),
(3, 3, 8, "2019-06-11"),
(4, 4, 6, "2019-06-05"),
(5, 4, 5, "2019-06-20"),
(6, 5, 9, "2009-02-02"),
(7, 5, 8, "2010-04-13");
```

## Answer

```sql
WITH eligible_books AS (
  SELECT *
  FROM Books
  WHERE DATE_SUB("2019-06-23", INTERVAL 1 MONTH) > available_from
),

SELECT 
  e.book_id, 
  e.name
FROM (
  SELECT * 
  FROM Orders
  WHERE DATE_SUB("2019-06-23", INTERVAL 1 YEAR) < dispatch_date
  ) o
RIGHT JOIN eligible_books e
ON o.book_id = e.book_id
GROUP BY e.book_id, e.name
HAVING SUM(COALESCE(o.quantity, 0)) < 10
```
