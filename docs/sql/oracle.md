左联查

```sql
SELECT p.FirstName,p.LastName,a.City,a.State FROM Person p LEFT JOIN Address a ON p.PersonId = a.PersonId
```

分组,查询分组后每个组的数据个数

```sql
SELECT CLASSNAME,COUNT(CLASSNAME) FROM CLASSINFO GROUP BY CLASSNAME
```

