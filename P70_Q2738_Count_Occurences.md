## 1. SQL Schema

```sql
Create table If Not Exists Files (file_name varchar(100), content text );
Truncate table Files;
insert into Files (file_name, content) values ('draft1.txt', 'The stock exchange predicts a bull market which would make many investors happy.');
insert into Files (file_name, content) values ('draft2.txt', 'The stock exchange predicts a bull market which would make many investors happy, but analysts warn of possibility of too much optimism and that in fact we are awaiting a bear market.');
insert into Files (file_name, content) values ('final.txt', 'The stock exchange predicts a bull market which would make many investors happy, but analysts warn of possibility of too much optimism and that in fact we are awaiting a bear market. As always predicting the future market is an uncertain game and all investors should follow their instincts and best practices.');
```

```
Write a solution to find the number of files that have at least one occurrence of the words 'bull' and 'bear' as a standalone word, respectively, disregarding any instances where it appears without space on either side (e.g. 'bullet', 'bears', 'bull.', or 'bear' at the beginning or end of a sentence will not be considered) 

Return the word 'bull' and 'bear' along with the corresponding number of occurrences in any order.

The result format is in the following example.

 

Example 1:

Input: 
Files table:
+------------+----------------------------------------------------------------------------------+
| file_name  | contenet                                                                         | 
+------------+----------------------------------------------------------------------------------+
| draft1.txt | The stock exchange predicts a bull market which would make many investors happy. | 
| draft2.txt | The stock exchange predicts a bull market which would make many investors happy, |
|            | but analysts warn of possibility of too much optimism and that in fact we are    |
|            | awaiting a bear market.                                                          | 
| draft3.txt | The stock exchange predicts a bull market which would make many investors happy, |
|            | but analysts warn of possibility of too much optimism and that in fact we are    |
|            | awaiting a bear market. As always predicting the future market is an uncertain   |
|            | game and all investors should follow their instincts and best practices.         | 
+------------+----------------------------------------------------------------------------------+
Output: 
+------+-------+
| word | count |  
+------+-------+
| bull | 3     | 
| bear | 2     | 
+------+-------+
Explanation: 
- The word "bull" appears 1 time in "draft1.txt", 1 time in "draft2.txt", and 1 time in "draft3.txt". Therefore, the total number of occurrences for the word "bull" is 3.
- The word "bear" appears 1 time in "draft2.txt", and 1 time in "draft3.txt". Therefore, the total number of occurrences for the word "bear" is 2.
```

## 2. SQL Answer

```sql
SELECT 
  'bull' AS word,
  SUM(CASE WHEN content LIKE '% bull %' THEN 1 ELSE 0 END) AS count
FROM Files
UNION
SELECT 
  'bear' AS word,
  SUM(CASE WHEN content LIKE '% bear %' THEN 1 ELSE 0 END) AS count
FROM Files
;
```

## 3. Pandas Schema

```python
import pandas as pd
import numpy as np

data = [['draft1.txt', 'The stock exchange predicts a bull market which would make many investors happy.'], ['draft2.txt', 'The stock exchange predicts a bull market which would make many investors happy, but analysts warn of possibility of too much optimism and that in fact we are awaiting a bear market.'], ['final.txt', 'The stock exchange predicts a bull market which would make many investors happy, but analysts warn of possibility of too much optimism and that in fact we are awaiting a bear market. As always predicting the future market is an uncertain game and all investors should follow their instincts and best practices.']]
files = pd.DataFrame(data, columns=['file_name', 'content']).astype({'file_name':'object', 'content':'object'})
```

## 4. Pandas Answer

```python
files['bull'] = files['content'].str.contains(' bull ')
files['bear'] = files['content'].str.contains(' bear ')
results = files[['bull', 'bear']].sum().reset_index().rename(
    columns={'index':'word', 0:'count'}
)
print(results)
```