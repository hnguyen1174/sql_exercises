## Table Schema

```sql
Create table If Not Exists Customers (customer_id int, customer_name varchar(20), email varchar(30));
Create table If Not Exists Contacts (user_id int, contact_name varchar(20), contact_email varchar(30));
Create table If Not Exists Invoices (invoice_id int, price int, user_id int);
Truncate table Customers;
insert into Customers (customer_id, customer_name, email) values ('1', 'Alice', 'alice@leetcode.com');
insert into Customers (customer_id, customer_name, email) values ('2', 'Bob', 'bob@leetcode.com');
insert into Customers (customer_id, customer_name, email) values ('13', 'John', 'john@leetcode.com');
insert into Customers (customer_id, customer_name, email) values ('6', 'Alex', 'alex@leetcode.com');
Truncate table Contacts;
insert into Contacts (user_id, contact_name, contact_email) values ('1', 'Bob', 'bob@leetcode.com');
insert into Contacts (user_id, contact_name, contact_email) values ('1', 'John', 'john@leetcode.com');
insert into Contacts (user_id, contact_name, contact_email) values ('1', 'Jal', 'jal@leetcode.com');
insert into Contacts (user_id, contact_name, contact_email) values ('2', 'Omar', 'omar@leetcode.com');
insert into Contacts (user_id, contact_name, contact_email) values ('2', 'Meir', 'meir@leetcode.com');
insert into Contacts (user_id, contact_name, contact_email) values ('6', 'Alice', 'alice@leetcode.com');
Truncate table Invoices;
insert into Invoices (invoice_id, price, user_id) values ('77', '100', '1');
insert into Invoices (invoice_id, price, user_id) values ('88', '200', '1');
insert into Invoices (invoice_id, price, user_id) values ('99', '300', '2');
insert into Invoices (invoice_id, price, user_id) values ('66', '400', '2');
insert into Invoices (invoice_id, price, user_id) values ('55', '500', '13');
insert into Invoices (invoice_id, price, user_id) values ('44', '60', '6');
```

```
Write a solution to find the following for each invoice_id:

customer_name: The name of the customer the invoice is related to.
price: The price of the invoice.
contacts_cnt: The number of contacts related to the customer.
trusted_contacts_cnt: The number of contacts related to the customer and at the same time they are customers to the shop. (i.e their email exists in the Customers table.)
Return the result table ordered by invoice_id.

The result format is in the following example.

 

Example 1:

Input: 
Customers table:
+-------------+---------------+--------------------+
| customer_id | customer_name | email              |
+-------------+---------------+--------------------+
| 1           | Alice         | alice@leetcode.com |
| 2           | Bob           | bob@leetcode.com   |
| 13          | John          | john@leetcode.com  |
| 6           | Alex          | alex@leetcode.com  |
+-------------+---------------+--------------------+
Contacts table:
+-------------+--------------+--------------------+
| user_id     | contact_name | contact_email      |
+-------------+--------------+--------------------+
| 1           | Bob          | bob@leetcode.com   |
| 1           | John         | john@leetcode.com  |
| 1           | Jal          | jal@leetcode.com   |
| 2           | Omar         | omar@leetcode.com  |
| 2           | Meir         | meir@leetcode.com  |
| 6           | Alice        | alice@leetcode.com |
+-------------+--------------+--------------------+
Invoices table:
+------------+-------+---------+
| invoice_id | price | user_id |
+------------+-------+---------+
| 77         | 100   | 1       |
| 88         | 200   | 1       |
| 99         | 300   | 2       |
| 66         | 400   | 2       |
| 55         | 500   | 13      |
| 44         | 60    | 6       |
+------------+-------+---------+
Output: 
+------------+---------------+-------+--------------+----------------------+
| invoice_id | customer_name | price | contacts_cnt | trusted_contacts_cnt |
+------------+---------------+-------+--------------+----------------------+
| 44         | Alex          | 60    | 1            | 1                    |
| 55         | John          | 500   | 0            | 0                    |
| 66         | Bob           | 400   | 2            | 0                    |
| 77         | Alice         | 100   | 3            | 2                    |
| 88         | Alice         | 200   | 3            | 2                    |
| 99         | Bob           | 300   | 2            | 0                    |
+------------+---------------+-------+--------------+----------------------+
Explanation: 
Alice has three contacts, two of them are trusted contacts (Bob and John).
Bob has two contacts, none of them is a trusted contact.
Alex has one contact and it is a trusted contact (Alice).
John doesn't have any contacts.
```

## 2. SQL Answer

```sql
WITH contacts_expanded AS (
  SELECT
    c.customer_id,
    COUNT(ct.user_id) AS contacts_cnt,
    SUM( 
      CASE WHEN ct.contact_email IN (SELECT email FROM Customers)
      THEN 1
      ELSE 0 END
    ) AS trusted_contacts_cnt
  FROM Customers c
  LEFT JOIN Contacts ct
    ON c.customer_id = ct.user_id
  GROUP BY 1
)

SELECT
  i.invoice_id,
  c.customer_name,
  i.price,
  COALESCE(ce.contacts_cnt, 0) AS contacts_cnt,
  COALESCE(ce.trusted_contacts_cnt, 0) AS trusted_contacts_cnt
FROM Invoices i
LEFT JOIN Customers c
  ON i.user_id = c.customer_id

LEFT JOIN contacts_expanded ce
  ON c.customer_id = ce.customer_id
ORDER BY i.invoice_id ASC
;
```