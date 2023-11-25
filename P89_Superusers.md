
## 1. Prompt

A Microsoft Azure Supercloud customer is defined as a company that purchases at least one product from each product category.

Write a query that effectively identifies the company ID of such Supercloud customers.

#### SQL Solution

```sql
WITH max_product_cat AS (
  SELECT
    COUNT(DISTINCT product_category) AS max_product_cat
  FROM products
),

product_cat_per_customer AS (
  SELECT
    c.customer_id,
    COUNT(DISTINCT p.product_category) AS product_cat
  FROM customer_contracts c
  LEFT JOIN products p
    ON c.product_id = p.product_id
  GROUP BY 1
)

SELECT
  customer_id
FROM product_cat_per_customer
WHERE product_cat = (SELECT max_product_cat FROM max_product_cat)
ORDER BY 1
;
```

#### Python Solution

```python
max_product_cat = products['product_category'].nunique()
customer_product = pd.merge(
    customer_contracts,
    products,
    on='product_id',
    how='left'
)
product_cat_per_customer = customer_product.groupby('customer_id')['product_category'].nunique().reset_index().rename(columns={'product_category':'num_product_category'})
results = product_cat_per_customer[product_cat_per_customer['num_product_category'] == max_product_cat]['customer_id']
```

