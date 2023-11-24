
## LinkedIn Power Creators (Part 2) Prompt

The LinkedIn Creator team is looking for power creators who use their personal profile as a company or influencer page. This means that if someone's Linkedin page has more followers than all the company they work for, we can safely assume that person is a Power Creator. Keep in mind that if a person works at multiple companies, we should take into account the company with the most followers.

Write a query to return the IDs of these LinkedIn power creators in ascending order.

Assumptions:

A person can work at multiple companies.
In the case of multiple companies, use the one with largest follower base.

#### SQL Solution

```sql
WITH profile_company AS (
  SELECT
    p.profile_id,
    p.followers,
    MAX(cp.followers) AS max_follower_company_page
  FROM personal_profiles p
  LEFT JOIN employee_company c
    ON p.profile_id = c.personal_profile_id
  LEFT JOIN company_pages cp
    ON c.company_id = cp.company_id
  GROUP BY 1,2
)

SELECT
  DISTINCT profile_id
FROM profile_company
WHERE followers > max_follower_company_page
ORDER BY profile_id ASC
```

#### Python Solution

```python
person_company = pd.merge(
  personal_profiles, employee_company, 
  left_on='profile_id',
  right_on='personal_profile_id'
  how='left'
)

person_company_follows = pd.merge(
  person_company,
  company_pages,
  on='company_id',
  how='left',
  suffixes=('_user', '_company')
)

tmp = person_company_follows.groupby(['profile_id', 'followers_user'])['followers_company'].max().reset_index()
results = tmp[tmp['followers_user'] > tmp['followers_company']]['profile_id'].drop_duplicates().sort_values()
```