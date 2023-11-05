## 1. SQL Schema

```sql
Create table If Not Exists LogInfo (account_id int, ip_address int, login datetime, logout datetime);
Truncate table LogInfo;
insert into LogInfo (account_id, ip_address, login, logout) values ('1', '1', '2021-02-01 09:00:00', '2021-02-01 09:30:00');
insert into LogInfo (account_id, ip_address, login, logout) values ('1', '2', '2021-02-01 08:00:00', '2021-02-01 11:30:00');
insert into LogInfo (account_id, ip_address, login, logout) values ('2', '6', '2021-02-01 20:30:00', '2021-02-01 22:00:00');
insert into LogInfo (account_id, ip_address, login, logout) values ('2', '7', '2021-02-02 20:30:00', '2021-02-02 22:00:00');
insert into LogInfo (account_id, ip_address, login, logout) values ('3', '9', '2021-02-01 16:00:00', '2021-02-01 16:59:59');
insert into LogInfo (account_id, ip_address, login, logout) values ('3', '13', '2021-02-01 17:00:00', '2021-02-01 17:59:59');
insert into LogInfo (account_id, ip_address, login, logout) values ('4', '10', '2021-02-01 16:00:00', '2021-02-01 17:00:00');
insert into LogInfo (account_id, ip_address, login, logout) values ('4', '11', '2021-02-01 17:00:00', '2021-02-01 17:59:59');
```

```
Write a solution to find the account_id of the accounts that should be banned from Leetflex. An account should be banned if it was logged in at some moment from two different IP addresses.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
LogInfo table:
+------------+------------+---------------------+---------------------+
| account_id | ip_address | login               | logout              |
+------------+------------+---------------------+---------------------+
| 1          | 1          | 2021-02-01 09:00:00 | 2021-02-01 09:30:00 |
| 1          | 2          | 2021-02-01 08:00:00 | 2021-02-01 11:30:00 |
| 2          | 6          | 2021-02-01 20:30:00 | 2021-02-01 22:00:00 |
| 2          | 7          | 2021-02-02 20:30:00 | 2021-02-02 22:00:00 |
| 3          | 9          | 2021-02-01 16:00:00 | 2021-02-01 16:59:59 |
| 3          | 13         | 2021-02-01 17:00:00 | 2021-02-01 17:59:59 |
| 4          | 10         | 2021-02-01 16:00:00 | 2021-02-01 17:00:00 |
| 4          | 11         | 2021-02-01 17:00:00 | 2021-02-01 17:59:59 |
+------------+------------+---------------------+---------------------+
Output: 
+------------+
| account_id |
+------------+
| 1          |
| 4          |
+------------+
Explanation: 
Account ID 1 --> The account was active from "2021-02-01 09:00:00" to "2021-02-01 09:30:00" with two different IP addresses (1 and 2). It should be banned.
Account ID 2 --> The account was active from two different addresses (6, 7) but in two different times.
Account ID 3 --> The account was active from two different addresses (9, 13) on the same day but they do not intersect at any moment.
Account ID 4 --> The account was active from "2021-02-01 17:00:00" to "2021-02-01 17:00:00" with two different IP addresses (10 and 11). It should be banned.
```

## 2. SQL Answer

```sql
SELECT
  DISTINCT l1.account_id
FROM
  LogInfo l1
JOIN
  LogInfo l2
WHERE l1.account_id = l2.account_id 
  AND l1.ip_address != l2.ip_address 
  AND l1.login <= l2.logout 
  AND l2.login <= l1.logout
;
```

## 3. Pandas Schema

```python
import pandas as pd
import numpy as np

data = [[1, 1, '2021-02-01 09:00:00', '2021-02-01 09:30:00'], [1, 2, '2021-02-01 08:00:00', '2021-02-01 11:30:00'], [2, 6, '2021-02-01 20:30:00', '2021-02-01 22:00:00'], [2, 7, '2021-02-02 20:30:00', '2021-02-02 22:00:00'], [3, 9, '2021-02-01 16:00:00', '2021-02-01 16:59:59'], [3, 13, '2021-02-01 17:00:00', '2021-02-01 17:59:59'], [4, 10, '2021-02-01 16:00:00', '2021-02-01 17:00:00'], [4, 11, '2021-02-01 17:00:00', '2021-02-01 17:59:59']]
log_info = pd.DataFrame(data, columns=['account_id', 'ip_address', 'login', 'logout']).astype({'account_id':'Int64', 'ip_address':'Int64', 'login':'datetime64[ns]', 'logout':'datetime64[ns]'})
```

## 4. Pandas Answer

```python
results = pd.merge(
    log_info,
    log_info,
    on='account_id',
    how='inner',
    suffixes=('_1', '_2')
)
results = results[
    (results['ip_address_1'] != results['ip_address_2']) &
    (results['login_1'] <= results['logout_2']) & 
    (results['login_2'] <= results['logout_1'])
]

print(results['account_id'].unique())
```