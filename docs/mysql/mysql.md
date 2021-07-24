## mysql

MySQL 5.1之后，InnoDB 是 MySQL 默认支持的存储引擎，支持**事务、行级锁定和外键** 

#### MySQL 常见存储引擎的区别

- **MyISAM 存储引擎的特点**

  在 5.1 版本之前，MyISAM 是 MySQL 的默认存储引擎，MyISAM 并发性比较差，使用的场景比较少，主要特点是

  - 不支持`事务`操作，ACID 的特性也就不存在了，这一设计是为了性能和效率考虑的。

  - 不支持`外键`操作，如果强行增加外键，MySQL 不会报错，只不过外键不起作用。

  - MyISAM 默认的锁粒度是`表级锁`，所以并发性能比较差，加锁比较快，锁冲突比较少，不太容易发生死锁的情况。

  - MyISAM 会在磁盘上存储三个文件，文件名和表名相同，扩展名分别是 `.frm(存储表定义)`、`.MYD(MYData,存储数据)`、`MYI(MyIndex,存储索引)`。这里需要特别注意的是 MyISAM 只缓存`索引文件`，并不缓存数据文件。

  - MyISAM 支持的索引类型有 `全局索引(Full-Text)`、`B-Tree 索引`、`R-Tree 索引`

    Full-Text 索引：它的出现是为了解决针对文本的模糊查询效率较低的问题。

    B-Tree 索引：所有的索引节点都按照平衡树的数据结构来存储，所有的索引数据节点都在叶节点

    R-Tree索引：它的存储方式和 B-Tree 索引有一些区别，主要设计用于存储空间和多维数据的字段做索引,目前的 MySQL 版本仅支持 geometry 类型的字段作索引，相对于 BTREE，RTREE 的优势在于范围查找。

  - 数据库所在主机如果宕机，MyISAM 的数据文件容易损坏，而且难以恢复。

  - 增删改查性能方面：SELECT 性能较高，适用于查询较多的情况

- **InnoDB 存储引擎的特点**

  自从 MySQL 5.1 之后，**默认的存储引擎变成了 InnoDB 存储引擎**，相对于 MyISAM，InnoDB 存储引擎有了  较大的改变，它的主要特点是

  - **支持事务操作**，具有事务 ACID 隔离特性，默认的隔离级别是`可重复读(repetable-read)`、通过MVCC（并发版本控制）来实现的。能够解决`脏读`和`不可重复读`的问题。
  - InnoDB 支持外键操作。
  - InnoDB 默认的锁粒度**行级锁**，并发性能比较好，会发生死锁的情况。
  - 和 MyISAM 一样的是，InnoDB 存储引擎也有 `.frm文件存储表结构` 定义，但是不同的是，InnoDB 的表数据与索引数据是存储在一起的，都位于 B+ 数的叶子节点上，而 MyISAM 的表数据和索引数据是分开的。
  - InnoDB 有安全的日志文件，这个日志文件用于恢复因数据库崩溃或其他情况导致的数据丢失问题，保证数据的一致性。
  - InnoDB 和 MyISAM 支持的索引类型相同，但具体实现因为文件结构的不同有很大差异。
  - 增删改查性能方面，如果执行大量的增删改操作，推荐使用 InnoDB 存储引擎，它在删除操作时是对行删除，不会重建表

#### union/union all/join/left join/right join

- **union和union all**

  union和union all 都用相同字段联合查询，不同之处在于union会自动去重并按照默认顺序排序，而union all不排序也不去重


- **join/inner join**

  join默认inner join，只有表中的数据匹配时才能返回包含结果

  ```mysql
  SELECT * FROM t1 INNER JOIN t2 ON t1.p_name = t2.p_name; 
  ```

  t1表字段值与t2表字段值相同时，才能查询出t1表的数据

- **left join**

  与inner join相似，只是左连接查询是以左边表为基准的，右表查不出数据则显示NULL

  ```mysql
  SELECT t1.id,t2.id FROM t1 LEFT JOIN t2 ON t1.p_name = t2.p_name; 
  ```

- **right join**

  与inner join相似，只是右连接查询是以右边表为基准的，左表查不出数据则显示NULL

  ```mysql
  SELECT t1.id,t2.id FROM t1 RIGHT JOIN t2 ON t1.p_name = t2.p_name; 
  ```

  