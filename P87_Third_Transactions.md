
## Prompt

Assume you are given the table below on Uber transactions made by users. Write a query to obtain the third transaction of every user. Output the user id, spend and transaction date.

#### SQL Solution

```sql
WITH transaction_order AS (
  SELECT
    *,
    ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY transaction_date ASC) AS rn
  FROM transactions
)

SELECT
  user_id,
  spend,
  transaction_date
FROM transaction_order 
WHERE rn = 3;
```

#### Python Solution

```python
import pandas as pd
import numpy as np

data = [
    [111, 100.50, '01/08/2022 12:00:00'], 
    [111, 55.00, '01/10/2022 12:00:00'], 
    [121, 36.00, '01/18/2022 12:00:00'],
    [145, 24.99, '01/26/2022 12:00:00'],
    [111, 89.60, '02/05/2022 12:00:00']
]
transactions = pd.DataFrame(data, columns=['user_id', 'spend', 'transaction_date']).astype({'user_id':'int64', 'spend':'float64', 'transaction_date':'datetime64[ns]'})

transactions['transaction_rank'] = transactions.groupby('user_id')['transaction_date'].rank()
print(transactions[transactions['transaction_rank'] == 3].drop('transaction_rank', axis=1))
```