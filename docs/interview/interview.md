#### 基础知识

- ==和 equals 的区别？

  == 是比较是不是同一个对象，equals是比较值是否相等，object equals方法默认使用的是==比较

- string、stringBuilder、SrtingBuffer的区别？

  String是字符串类使用了final修饰不可以被更改不可以被继承，stringBuilder，SrtingBuffer是String的缓冲类可以灵活操作字符串的内容，前者是线程安全的后者线程安全，效率更高

- Object作为父类，它里面有哪些方法？

  equals，toString，hashcode等等

- HashMap和HashTable的区别？

  HashMap线程不安全，HashTable使用了**syncrinized**修饰是线程安全的，但一般不怎么用，一般用ConcurrentHashMap

- map集合的底层数据结构是什么？原理是什么？

  - 通过hash取模计算出需要插入的值在数组中的位置，如果该位置为空就直接插入，如果已经有值就以链表的方式追加到后面
  - JDK1.7数组(Entry)+链表
- JDK1.8数组(Node)+链表，当链表的长度大于8的时候就转换分为红黑树
  
- HashMap最大可以存储多少数据？

  理论上可以存放2^31 -1 个

- ArrayList 和 LinkedList 的区别是什么？

  ArrayList 是动态数组，LinkedList 是链表，前者是连续的数组结构，后者是双向链表

- ArrayList 和 Vector 的区别是什么？

  ArrayList 是非线程安全，Vector方法大部分都使用了synchronized修饰是线程安全的

- Array 和 ArrayList 有何区别？

  Array是数组，只能存放单一类型，必须声明大小声明后不可以更改，后者是动态数组，使用了泛型和预留机制，可以动态自动扩容，实际上也是使用Array实现的

- 说一下 HashMap 的实现原理？

  - 通过hash取模计算出需要插入的值在数组中的位置，如果该位置为空就直接插入，如果已经有值就以链表的方式追加到后面
  - JDK1.7数组(Entry)+链表
  - JDK1.8数组(Node)+链表，当链表的长度大于8的时候就转换分为红黑树

- 说一下 HashSet 的实现原理？

  - HasSet内部是使用HashMap实现的，本省只有很少的代码，key和value都是一样的

- 如何决定使用 HashMap 还是 TreeMap？

  - HashMap是无序的，LinkedHashMap是按插入顺序排列，TreeMap是按照字典顺序排列，内部使用了红黑树结构存储

- java.util.concurrent包下使用过哪些？

  ConcurrentHashMap

  - JDK1.7，使用了 Segment  分段锁机制来实现高并发，默认为16个segment
  - JDK1.8，使用了 数组+链表/红黑树+Synchronized + CAS  来实现高并发，只锁住链表头或红黑树根节点，如果计算的hash不冲突则不影响比并发

- HashMap的工作原理是什么？还有什么处理哈希冲突的方法？

  - 工作原理
    -  先检查当前所有键值对中是否存在key，key相等，值替换
    -  检查是否是key=null 
    -  调用键对象的hashCode()方法来计算hashcode，通过键对象的equals()方法比较 
    -  若有效个数/数组长度>=0.75（扩容因子） 
    -  需要扩容，2倍扩容 
    -  重新哈希，new数组 
  - 解决处理hash冲突
    - 容量是2的幂次，因为计算通过hash计算位置时时 hash % length,当容量为 2^n时，hash & (length - 1) == hash % length，它俩是等价不等效的。 
    - hash冲突时通过链表或红黑树保存

#### java多线程

- 线程安全的数组有哪些？

  Vector

- 线程安全的集合有哪些？

  Vector，HashTable

- hashMap为什么不是线程安全的？底层原理是什么？

- 请用代码实现同时开启一个线程打印1～52，开启另一个线程打印A～Z，最终交替打印1A2B3C...这样的数据

- Java里实现线程的方式有哪些？各自的优缺点是什么？

- 你如何理解同步、异步、阻塞、非阻塞？

- 你的程序中用到了哪些锁？是如何实现的?

#### java线程池

- Java里线程池常用的有哪些？

  需要频繁创建线程的场景

- 线程池在你的生产开发中有哪些应用场景？是如何使用的？

- 线程池的原理是什么？

  队列+维护特定数量的活动线程

#### jvm

- JVM的内存模型是什么？每个区的作用是什么？

  - 堆，方法区（公有）
  - 线程栈，本地方法栈（私有）
  - 程序计数器
  - 本地接口
  - 执行引擎

- 如何进行JVM调优？

  -  对JVM内存的系统级的调优主要的目的是减少GC的频率和Full GC的次数
  - 参考https://www.cnblogs.com/Darrenblog/p/10712125.html

  - Xms，Xmx，xss等参数合理化设置

- 如何对线程服务进行JVM排查和优化？

  - jvisualvm，jconsole等工具，查看minor GC时间，Full GC频率和时间，打印dump

- JVM GC有几种算法？

  - 判断对象是否已死
    - 引用计数法
    - 可达性分析法
  - 垃圾回收算法
    - 复制算法（Minor GC）
    - 标记清除（Full GC）
    - 标记整理

#### mysql

- MySQL设计表的三大范式是什么？
  1. 1NF字段不可被拆分
  2. 2NF一张表只说明一个事务
  3. 3NF非主键字段不能相互依赖 
  
- MySQL的`selete *` 和select全字段区别？

  前者会 Query Table Metadata For Columns ，相比而言比后者多查询了一次

- MySQL的`selete *`除了是全字段扫描外为什么慢？

  - 无用字段增加网络消耗
  - 增加查询分析器的分析成本
  - 无用大字段如varchar、blob、text等会增加io操作（长度超过 728 字节的时候，会先把超出的数据序列化到另外一个地方，因此读取这条记录会增加一次 io 操作。（MySQL InnoDB））

- MySQL如何做优化？

  - 禁止所有select * 查询
  - 尽量简化查询语句和查询到字段
  - 合理设置索引，不易过多
  - 尽量避免慢查询

- MySQL的常用SQL语句优化有哪些？

- MySQL索引的作用？和它的优点缺点是什么？

- MySQL有哪些索引（或者说索引类型有哪些）？

- MySQL的主键和索引有什么区别？

- MySQL创建索引的原则是什么？

- MySQL的索引在哪些情况下会失效？

- MySQL的索引原理是什么？

- MySQL的存储引擎有哪些？

- MySQL的最左原则是什么？

- MySQL的乐观锁和悲观锁？

- 如果在a、b、c三个字段上建立复合索引，那么有几个索引？`a、c`、`b、c`两个索引会生效吗？

- MySQL的聚簇索引和非聚簇索引是什么？

- MySQL的是传递性是什么？如何来保证？

- 如何判断SQL语句是否索引用上了？

- MySQL调优时用什么方法或命令进行排查？命令的回显结果集中哪些子弹是需要重点关注的？

- MySQL进行全表扫描是会触发表锁吗？

- MySQL如何做分表？

- 已知现在有一个一千万数据的日志记录表，每天会向里面新增5000条记录，如何优化？

- 如何做大数据量表的查询和拆分？如果拆分怎么拆？有哪些方式？

- 跨表查询有哪些方式？你最多做过几表联查？

- 事物的隔离级别有哪些？

- 你们每天的数据量大概有多少？

- 你们最大的一张表多大？

- mysql中如何查看执行计划？如何做sql优化？

#### 事务

- 数据库事务的四大特性是什么？
  - ACID，原子性，一致性，隔离性，持久性
- 分布式事物如何保证？
- springcloud的分布式事物如何保证？
- 可以用Redis来实现分布式事物吗？
- 什么是事物的传导性？有哪些问题？
- 事务的CAP理论是什么？
- 你们在生成中用到了哪种方式来保证事务一致性？

#### spring

- spring的IOC和AOP原理是什么？
- spring的启动加载流程是怎样的？
- springMVC的底层执行流程是怎样的？重要的类有哪些？
- springboot支持的日志有哪些？默认的是什么？怎么配置
- springboot的数据源有哪些？怎么配置？
- springcloud的常用开发框架有哪些？
- springcloud的熔断是怎么实现的？原理是什么？
- springcloud的eureka和zoopeeker的区别是什么？
- springcloud的网管的主要做了哪些？
- springcloud里bus的底层通信是用的什么？

#### redis

- Redis你们用的是单机还是集群？集群如何搭建？
- Redis是什么？都有哪些使用场景？
- Redis的基本数据类型都有哪些？
- Redis在开发中常用到哪些数据类型？
- Redis中hash的数据结构是什么？
- Redis集群数据不同步有可能是什么原因？
- 请实现用Redis做登录，错误次数为最多五次，时间为60秒
- Redis的哨兵机制原理是什么？
- Redis的持久化方法有哪些？
- Redis的缓存穿透是什么？怎么解决？
- Redis怎么实现分布式锁？
- Redis分布式锁有什么缺陷？
- Redis如何做内存优化？
- Redis淘汰策略有哪些？
- Redis常见的性能问题有哪些？该如何解决？
- Redis支持的 java 客户端都有哪些？
- Redis为什么是单线程的？
- Redis的管道是什么？你们什么时候用？怎么使用的？

#### 中间件

- ActiveMQ是什么？
- ActiveMQ的应用场景有哪些？
- ActiveMQ有哪些优缺点？
- ActiveMQ在应用中可以保证分布式事物吗？是怎么是实现的？有哪些优缺点？
- 常用的MQ的对比了解吗？
- kufaka原理是什么？为什么快？

#### 设计模式

- 常用的设计模式有哪些？
- 请写出一个单例模式（要求是线程安全的）
- 单例模式的双重检查机制如何实现？（DCL）

#### 其他

- Nginx可以做哪些事情？
- Nginx有几种负载均衡？
- Nginx的轮询里一致性哈希算法的底层原理是什么？
- Nginx如何避免缓存雪崩
- 服务器的CUP超负荷了，如何排查是哪个项目的？
- 如何根据CPU的负载情况定位代码和线程所在？
- Java程序里如果有无限for循环的代码导致CPU负载超高，如何排查？
- 在爬虫里如何实现用户登录？
- 你是如何让你的爬虫项目避过一些网站的防爬机制的？
- solr的原理是什么?是怎么保证排序的？
- solr在你的项目中起到了什么作用？
- MyBatis里`${}`和`#{}`的区别是什么？发生SQL注入的原理是什么？
- MyBatis里可以返回哪些对象？
- MyBatis在开发中什么情况下回用到`${}`符号？
- 常用的linux命令，并阐述作用
- 当你请求了一个url地址后，它的后面都发生了什么？
- maven的生命周期有哪些？
- 前端的标签选择器有哪些？
- 常见的页面状态有哪些？

