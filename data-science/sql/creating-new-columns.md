# Creating new columns

### Using Alias with Case and Sum

```sql
SELECT profession AS department,
    SUM(CASE WHEN MONTH(birthday) = 1 THEN 1 ELSE 0 END) AS month_1,
    SUM(CASE WHEN MONTH(birthday) = 2 THEN 1 ELSE 0 END) AS month_2,
    SUM(CASE WHEN MONTH(birthday) = 3 THEN 1 ELSE 0 END) AS month_3
FROM employee_list
GROUP BY profession;
```

