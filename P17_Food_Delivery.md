## Tables and Schema

```
SQL Schema
Table: Delivery

+-----------------------------+---------+
| Column Name                 | Type    |
+-----------------------------+---------+
| delivery_id                 | int     |
| customer_id                 | int     |
| order_date                  | date    |
| customer_pref_delivery_date | date    |
+-----------------------------+---------+
delivery_id is the primary key of this table.
The table holds information about food delivery to customers that make orders at some date and specify a preferred delivery date (on the same order date or after it).
```

## Question 1:

If the preferred delivery date of the customer is the same as the order date then the order is called immediate otherwise it's called scheduled.

Write an SQL query to find the percentage of immediate orders in the table, rounded to 2 decimal places.

The query result format is in the following example:

```sql
SELECT
    ROUND(SUM(CASE WHEN order_date = customer_pref_delivery_date THEN 1 ELSE 0 END)*100/COUNT(delivery_id), 2) AS immediate_percentage
FROM Delivery
```

**Python**

```python
import pandas as pd
import numpy as np

delivery_id = [1, 2, 3, 4, 5, 6]
customer_id = [1, 5, 1, 3, 4, 2]
order_date = ['2019-08-01', '2019-08-02',
              '2019-08-11', '2019-08-24',
              '2019-08-21', '2019-08-11']

customer_pref_delivery_date = [
    '2019-08-02', 
    '2019-08-02',
    '2019-08-11',
    '2019-08-26',
    '2019-08-22',
    '2019-08-13'
]

df = pd.DataFrame({
    'delivery_id': delivery_id,
    'customer_id': customer_id,
    'order_date': order_date,
    'customer_pref_delivery_date': customer_pref_delivery_date
})
df['order_date'] = pd.to_datetime(df['order_date'])
df['customer_pref_delivery_date'] = pd.to_datetime(df['customer_pref_delivery_date'])
df_immediate = df[df['order_date'] == df['customer_pref_delivery_date']]
print(round(df_immediate.shape[0]*100/df.shape[0], 2))
```

## Question 2:

If the preferred delivery date of the customer is the same as the order date then the order is called immediate otherwise it's called scheduled.

The first order of a customer is the order with the earliest order date that customer made. It is guaranteed that a customer has exactly one first order.

Write an SQL query to find the percentage of immediate orders in the first orders of all customers, rounded to 2 decimal places.

The query result format is in the following example:

```sql
SELECT 
    ROUND(SUM(CASE WHEN order_date = customer_pref_delivery_date THEN 1 ELSE 0 END)*100/COUNT(delivery_id), 2) AS immediate_percentage
FROM (
SELECT 
    customer_id,
    RANK() OVER (PARTITION BY customer_id ORDER BY order_date) AS customer_order_rank,
    delivery_id,
    order_date,
    customer_pref_delivery_date
FROM Delivery
) TMP
WHERE customer_order_rank = 1
```

**Python**

```python
import pandas as pd
import numpy as np


delivery_id = [1, 2, 3, 4, 5, 6, 7]
customer_id = [1, 2, 1, 3, 3, 2, 4]
order_date = ['2019-08-01', '2019-08-02',
              '2019-08-11', '2019-08-24',
              '2019-08-21', '2019-08-11',
              '2019-08-09']

customer_pref_delivery_date = [
    '2019-08-02', 
    '2019-08-02',
    '2019-08-12',
    '2019-08-24',
    '2019-08-22',
    '2019-08-13',
    '2019-08-09'
]

df = pd.DataFrame({
    'delivery_id': delivery_id,
    'customer_id': customer_id,
    'order_date': order_date,
    'customer_pref_delivery_date': customer_pref_delivery_date
})
df['order_date'] = pd.to_datetime(df['order_date'])
df['customer_pref_delivery_date'] = pd.to_datetime(df['customer_pref_delivery_date'])

df_first = df[df['order_date'] == df.groupby('customer_id')['order_date'].transform(min)]
df_first_immediate = df_first[df_first['order_date'] == df_first['customer_pref_delivery_date']]
print(df_first_immediate.shape[0]*100/df_first.shape[0])
```
