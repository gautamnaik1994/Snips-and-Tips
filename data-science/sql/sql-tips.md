# SQL Tips

Conditionals can be used inside SUM() with group by aggregations

```sql
having sum(name="XYZ") > 0

sum(case when x="something" then 1 else 0 end) as col_name
```

### Use inverse

Instead of finding something using a complex query, use a subquery that does the opposite of the primary problem. Then use the outer query to find "not in subquery".
