## 1. Table Schema

```sql
Create table If Not Exists Accounts (account_id int, max_income int);
Create table If Not Exists Transactions (transaction_id int, account_id int, type ENUM('creditor', 'debtor'), amount int, day datetime);
Truncate table Accounts;
insert into Accounts (account_id, max_income) values ('3', '21000');
insert into Accounts (account_id, max_income) values ('4', '10400');
Truncate table Transactions;
insert into Transactions (transaction_id, account_id, type, amount, day) values ('2', '3', 'Creditor', '107100', '2021-06-02 11:38:14');
insert into Transactions (transaction_id, account_id, type, amount, day) values ('4', '4', 'Creditor', '10400', '2021-06-20 12:39:18');
insert into Transactions (transaction_id, account_id, type, amount, day) values ('11', '4', 'Debtor', '58800', '2021-07-23 12:41:55');
insert into Transactions (transaction_id, account_id, type, amount, day) values ('1', '4', 'Creditor', '49300', '2021-05-03 16:11:04');
insert into Transactions (transaction_id, account_id, type, amount, day) values ('15', '3', 'Debtor', '75500', '2021-05-23 14:40:20');
insert into Transactions (transaction_id, account_id, type, amount, day) values ('10', '3', 'Creditor', '102100', '2021-06-15 10:37:16');
insert into Transactions (transaction_id, account_id, type, amount, day) values ('14', '4', 'Creditor', '56300', '2021-07-21 12:12:25');
insert into Transactions (transaction_id, account_id, type, amount, day) values ('19', '4', 'Debtor', '101100', '2021-05-09 15:21:49');
insert into Transactions (transaction_id, account_id, type, amount, day) values ('8', '3', 'Creditor', '64900', '2021-07-26 15:09:56');
insert into Transactions (transaction_id, account_id, type, amount, day) values ('7', '3', 'Creditor', '90900', '2021-06-14 11:23:07');
```

```
A bank account is suspicious if the total income exceeds the max_income for this account for two or more consecutive months. The total income of an account in some month is the sum of all its deposits in that month (i.e., transactions of the type 'Creditor').

Write a solution to report the IDs of all suspicious bank accounts.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Accounts table:
+------------+------------+
| account_id | max_income |
+------------+------------+
| 3          | 21000      |
| 4          | 10400      |
+------------+------------+
Transactions table:
+----------------+------------+----------+--------+---------------------+
| transaction_id | account_id | type     | amount | day                 |
+----------------+------------+----------+--------+---------------------+
| 2              | 3          | Creditor | 107100 | 2021-06-02 11:38:14 |
| 4              | 4          | Creditor | 10400  | 2021-06-20 12:39:18 |
| 11             | 4          | Debtor   | 58800  | 2021-07-23 12:41:55 |
| 1              | 4          | Creditor | 49300  | 2021-05-03 16:11:04 |
| 15             | 3          | Debtor   | 75500  | 2021-05-23 14:40:20 |
| 10             | 3          | Creditor | 102100 | 2021-06-15 10:37:16 |
| 14             | 4          | Creditor | 56300  | 2021-07-21 12:12:25 |
| 19             | 4          | Debtor   | 101100 | 2021-05-09 15:21:49 |
| 8              | 3          | Creditor | 64900  | 2021-07-26 15:09:56 |
| 7              | 3          | Creditor | 90900  | 2021-06-14 11:23:07 |
+----------------+------------+----------+--------+---------------------+
Output: 
+------------+
| account_id |
+------------+
| 3          |
+------------+
Explanation: 
For account 3:
- In 6-2021, the user had an income of 107100 + 102100 + 90900 = 300100.
- In 7-2021, the user had an income of 64900.
We can see that the income exceeded the max income of 21000 for two consecutive months, so we include 3 in the result table.

For account 4:
- In 5-2021, the user had an income of 49300.
- In 6-2021, the user had an income of 10400.
- In 7-2021, the user had an income of 56300.
We can see that the income exceeded the max income in May and July, but not in June. Since the account did not exceed the max income for two consecutive months, we do not include it in the result table.
```

## 2. SQL Answer

```sql
WITH activity_summary AS (
  SELECT
    T.account_id,
    DATE_FORMAT(T.day, '%Y-%m-01') AS date,
    (SUM(T.amount) > MAX(A.max_income)) AS is_suspicious
  FROM Transactions T
  JOIN Accounts A 
    ON A.account_id = T.account_id
  WHERE
    T.type = 'Creditor'
  GROUP BY
    1,2
)


SELECT DISTINCT
  AS1.account_id
FROM activity_summary AS1
JOIN activity_summary AS2
  ON AS1.account_id = AS2.account_id
  AND DATE_ADD(AS1.date, INTERVAL 1 MONTH) = AS2.date
  AND AS1.is_suspicious = 1 
  AND AS2.is_suspicious = 1
ORDER BY AS1.account_id
;
```