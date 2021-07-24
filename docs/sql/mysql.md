s参考：https://blog.csdn.net/zjt980452483/article/details/82945663

https://www.cnblogs.com/reaptomorrow-flydream/p/8145610.html

#### 四种join查询的区别

1. JOIN: 如果表中有至少一个匹配，则返回行；
2. INNER JOIN：在表中存在至少一个匹配时，返回行。
3. LEFT JOIN: 即使右表中没有匹配，也从左表返回所有的行 ；
4. RIGHT JOIN: 即使左表中没有匹配，也从右表返回所有的行；
5. FULL JOIN: 只要其中一个表中存在匹配，就返回行；

#### 注意事项

- 尽量少用交叉查询cross join，先生成笛卡尔积再匹配where条件，性能很差，（例如：select a.name,b.age from a,b where a.id = b.id）
- 子查询会产生零时表
-  嵌套在 SELECT 语句的 FROM 子句中的子查询必须制定别名（例如：SELECT * FROM (子查询) AS 表的别名 ）

#### inner join多表联查

```sql
<select id="listOrders" resultType="com.alibaba.fastjson.JSONObject">
        select c.paramName, l.labName, e.equipmentName, o.startTime, o.endTime
        from corelist c
        inner join orderinfo o on c.coreId = o.coreId and c.userAccount = #{userAccount}
        inner join equipment e on e.eId = o.eId
        inner join lab l on l.labId = e.labId
        where o.state = 1;
</select>
-- on和where的选择条件的区别：
-- on后面的是连接条件， 代表两个表建立关系所遵循的规则
-- where后面的可以看作是筛选条件，是对最终结果集进行过滤所遵循的规则
```



#### JOIN用于按照ON条件联接两个表，主要有四种：

boy表
| hid  | bname  |
| ---- | ------ |
| A    | 屌丝   |
| B    | 杨过   |
| C    | 陈冠希 |

girl表

| hid  | gname  |
| ---- | ------ |
| B    | 小龙女 |
| C    | 张柏芝 |
| D    | 宅女   |

1. inner join：两表交集，必须同时满足ON的条件才显示

   ```sql
   SELECT boy.hid,boy.bname,girl.gname FROM boy INNER JOIN girl ON girl.hid = boy.hid;
   --以下三种写法相同
   --1. select * from a join b on a.id = b.id-
   --2. select * from a inner join b on a.id = b.id
   --3. select * from a, b where a.id = b.id
   --结果
   --hid--+--bname--+--gname
   --B        杨过     小龙女 
   --C       陈冠希    张柏芝
   ```

2. left outer join：以左表为ON条件基准，右表不匹配ON条件的NULL代替

   ```sql
   SELECT boy.hid,boy.bname,girl.gname FROM boy LEFT JOIN girl ON girl.hid = boy.hid;
   --hid--+--bname--+--gname
   --B       杨过     小龙女    
   --C       陈冠希    张柏芝
   --A       屌丝      NULL
   ```

3. right outer join：以右表为ON条件基准，右表不匹配ON条件的NULL代替

   ```sql
   SELECT boy.hid,boy.bname,girl.gname FROM boy RIGHT JOIN girl ON girl.hid = boy.hid;
   --hid--+--bname--+--gname
   --B       杨过      小龙女    
   --C       陈冠希    张柏芝
   --NULL    NULL       宅女
   ```

4. full outer join（msyql没有full join）： 全部展示，左右两表不匹配ON条件的NULL代替

   ```sql
   --mysql union 实现full join
   select * from boy left join girl on boy.hid = girl.hid union  select * from boy right join girl on boy.hid = girl.hid ;
SELECT boy.hid,boy.bname,girl.gname FROM boy FULL JOIN girl ON girl.hid = boy.hid;
   --hid--+--bname--+--gname
   --A       屌丝      NULL
   --B       杨过     小龙女    
   --C       陈冠希    张柏芝
   --D       NULL     宅女
   ```
   

#### UNION/UNION ALL

1. UNION运算符，将两个或更多查询的结果集组合为单个结果集，该结果集包含联合查询中的所有查询的全部行。UNION的结果集列名与UNION运算符中第一个Select语句的结果集的列名相同。另一个Select语句的结果集列名将被忽略。

   ```sql
   SELECT id,SUM(num) as num FROM ((SELECT id,num FROM a) UNION ALL(SELECT id,num FROM b)) as tb GROUP BY id;
   ```

2. UNION和UNION ALL，区别在于UNION从结果集中删除重复的行。UNION ALL 包含所有行并且将不删除重复的行。 
