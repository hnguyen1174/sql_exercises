## 1. SQL Schema

```sql
Create table If Not Exists Person (id int, name varchar(15), phone_number varchar(11));
Create table If Not Exists Country (name varchar(15), country_code varchar(3));
Create table If Not Exists Calls (caller_id int, callee_id int, duration int);
Truncate table Person;
insert into Person (id, name, phone_number) values ('3', 'Jonathan', '051-1234567');
insert into Person (id, name, phone_number) values ('12', 'Elvis', '051-7654321');
insert into Person (id, name, phone_number) values ('1', 'Moncef', '212-1234567');
insert into Person (id, name, phone_number) values ('2', 'Maroua', '212-6523651');
insert into Person (id, name, phone_number) values ('7', 'Meir', '972-1234567');
insert into Person (id, name, phone_number) values ('9', 'Rachel', '972-0011100');
Truncate table Country;
insert into Country (name, country_code) values ('Peru', '051');
insert into Country (name, country_code) values ('Israel', '972');
insert into Country (name, country_code) values ('Morocco', '212');
insert into Country (name, country_code) values ('Germany', '049');
insert into Country (name, country_code) values ('Ethiopia', '251');
Truncate table Calls;
insert into Calls (caller_id, callee_id, duration) values ('1', '9', '33');
insert into Calls (caller_id, callee_id, duration) values ('2', '9', '4');
insert into Calls (caller_id, callee_id, duration) values ('1', '2', '59');
insert into Calls (caller_id, callee_id, duration) values ('3', '12', '102');
insert into Calls (caller_id, callee_id, duration) values ('3', '12', '330');
insert into Calls (caller_id, callee_id, duration) values ('12', '3', '5');
insert into Calls (caller_id, callee_id, duration) values ('7', '9', '13');
insert into Calls (caller_id, callee_id, duration) values ('7', '1', '3');
insert into Calls (caller_id, callee_id, duration) values ('9', '7', '1');
insert into Calls (caller_id, callee_id, duration) values ('1', '7', '7');
```

```
A telecommunications company wants to invest in new countries. The company intends to invest in the countries where the average call duration of the calls in this country is strictly greater than the global average call duration.

Write a solution to find the countries where this company can invest.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Person table:
+----+----------+--------------+
| id | name     | phone_number |
+----+----------+--------------+
| 3  | Jonathan | 051-1234567  |
| 12 | Elvis    | 051-7654321  |
| 1  | Moncef   | 212-1234567  |
| 2  | Maroua   | 212-6523651  |
| 7  | Meir     | 972-1234567  |
| 9  | Rachel   | 972-0011100  |
+----+----------+--------------+
Country table:
+----------+--------------+
| name     | country_code |
+----------+--------------+
| Peru     | 051          |
| Israel   | 972          |
| Morocco  | 212          |
| Germany  | 049          |
| Ethiopia | 251          |
+----------+--------------+
Calls table:
+-----------+-----------+----------+
| caller_id | callee_id | duration |
+-----------+-----------+----------+
| 1         | 9         | 33       |
| 2         | 9         | 4        |
| 1         | 2         | 59       |
| 3         | 12        | 102      |
| 3         | 12        | 330      |
| 12        | 3         | 5        |
| 7         | 9         | 13       |
| 7         | 1         | 3        |
| 9         | 7         | 1        |
| 1         | 7         | 7        |
+-----------+-----------+----------+
Output: 
+----------+
| country  |
+----------+
| Peru     |
+----------+
Explanation: 
The average call duration for Peru is (102 + 102 + 330 + 330 + 5 + 5) / 6 = 145.666667
The average call duration for Israel is (33 + 4 + 13 + 13 + 3 + 1 + 1 + 7) / 8 = 9.37500
The average call duration for Morocco is (33 + 4 + 59 + 59 + 3 + 7) / 6 = 27.5000 
Global call duration average = (2 * (33 + 4 + 59 + 102 + 330 + 5 + 13 + 3 + 1 + 7)) / 20 = 55.70000
Since Peru is the only country where the average call duration is greater than the global average, it is the only recommended country.
```

## 2. SQL Answer

```sql
WITH all_calls AS (
  SELECT * FROM Calls
  UNION
  SELECT callee_id AS caller_id, caller_id AS callee_id, duration FROM Calls
),

country_calls AS (
  SELECT
    c.*,
    co.name AS country
  FROM all_calls c
  LEFT JOIN Person p
    ON c.caller_id = p.id
  LEFT JOIN Country co
    ON SUBSTRING_INDEX(p.phone_number, '-', 1) = co.country_code
),

averages AS (
  SELECT
    *,
    AVG(duration) OVER (PARTITION BY country) AS country_average,
    AVG(duration) OVER () AS global_average
  FROM country_calls
)

SELECT
  DISTINCT country
FROM averages
WHERE country_average > global_average
;
```

## 3. Pandas Schema

```python
import pandas as pd
import numpy as np

data = [[3, 'Jonathan', '051-1234567'], [12, 'Elvis', '051-7654321'], [1, 'Moncef', '212-1234567'], [2, 'Maroua', '212-6523651'], [7, 'Meir', '972-1234567'], [9, 'Rachel', '972-0011100']]
person = pd.DataFrame(data, columns=['id', 'name', 'phone_number']).astype({'id':'Int64', 'name':'object', 'phone_number':'object'})
data = [['Peru', '051'], ['Israel', '972'], ['Morocco', '212'], ['Germany', '049'], ['Ethiopia', '251']]
country = pd.DataFrame(data, columns=['name', 'country_code']).astype({'name':'object', 'country_code':'object'})
data = [[1, 9, 33], [2, 9, 4], [1, 2, 59], [3, 12, 102], [3, 12, 330], [12, 3, 5], [7, 9, 13], [7, 1, 3], [9, 7, 1], [1, 7, 7]]
calls = pd.DataFrame(data, columns=['caller_id', 'callee_id', 'duration']).astype({'caller_id':'Int64', 'callee_id':'Int64', 'duration':'Int64'})
```

## 4. Pandas Answer

```python
all_calls = pd.concat(
    [calls,
    calls.rename(columns={'caller_id': 'callee_id', 'callee_id': 'caller_id'})]
)
all_calls_with_numbers = pd.merge(
    all_calls,
    person,
    left_on='caller_id', right_on='id',
    how='left'
)
all_calls_with_numbers[['country_code', 'to_drop']] = all_calls_with_numbers['phone_number'].str.split('-', expand=True)
all_calls_ft = all_calls_with_numbers[['caller_id', 'duration', 'country_code']]
all_calls_with_countries = pd.merge(
    all_calls_ft,
    country,
    on='country_code',
    how='left'
).reset_index().rename(columns={'name':'country'})
country_means = all_calls_with_countries.groupby('country')['duration'].mean()
global_means = all_calls_with_countries['duration'].mean()
print(country_means[country_means > global_means].index)
```