# SQL Tips

Conditionals can be used inside SUM() with group by aggregations

```sql
having sum(name="XYZ") > 0

sum(case when x="something" then 1 else 0 end) as col_name
```

### Use inverse

Instead of finding something using a complex query, use a subquery that does the opposite of the primary problem. Then use the outer query to find "not in subquery".



### Reduce duplicate rows cross-join

```sql
WITH cte AS (
  SELECT BASKET_ID, SUB_COMMODITY_DESC
  FROM transaction_df t 
  JOIN products_df p 
    ON t.PRODUCT_ID = p.PRODUCT_ID
)

SELECT 
  lt.SUB_COMMODITY_DESC AS p1, 
  rt.SUB_COMMODITY_DESC AS p2, 
  COUNT(*) AS cnt
FROM cte lt 
JOIN cte rt 
  ON lt.BASKET_ID = rt.BASKET_ID 
  AND lt.SUB_COMMODITY_DESC < rt.SUB_COMMODITY_DESC -- This line
  AND lt.SUB_COMMODITY_DESC != rt.SUB_COMMODITY_DESC 
GROUP BY p1, p2
ORDER BY cnt DESC
```

### Interview Questions

{% embed url="https://quip.com/2gwZArKuWk7W" %}
