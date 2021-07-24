# mybatis

#### 介绍

- mybatis前生是ibatis，mybatis是团队从apache转战google后的名字
-  Mybatis是一个半ORM（对象关系映射）框架，它内部封装了JDBC，开发时只需要关注SQL语句本身，不需要花费精力去处理加载驱动、创建连接、创建statement等繁杂的过程。程序员直接编写原生态sql，可以严格控制sql执行性能，灵活度高
-  MyBatis 可以使用 **XML** 或**注解**来配置和映射原生信息，将 POJO映射成数据库中的记录，避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集 
- 通过xml 文件或注解的方式将要执行的各种 statement 配置起来，并通过java对象和 statement中sql的动态参数进行映射生成最终执行的sql语句，最后由mybatis框架执行sql并将结果映射为java对象并返回。（从执行sql到返回result的过程） 
- javaBean => 编写sql => 预编译 => 参数设置 => 执行sql => 结果封装
-  MyBatis 的应用是以SqlSessionFactory的实例为核心， SqlSessionFactory可以通过 SqlSessionFactoryBuilder  获得， SqlSessionFactoryBuilder可以从 XML 配置文件或一个预先配置的 Configuration 实例来构建出 SqlSessionFactory 实例 
- mybatis详细执行流程https://blog.csdn.net/ma15732625261/article/details/81123349
- SqlSession代表一次和数据库的会话，用完必须关闭
- SqlSession和connection一样是非线程安全的，每次使用应该获取新的对象
- mapper接口没有实现类，但mybatis会动态生成代理对象，接口将和xml进项绑定
- mybatis的全局配置文件包含连接池信息，事务管理，系统运行等信息。mapper.xml保存和sql映射信息

#### 优点

1. 基于SQL语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL写在XML里，解除sql与程序代码的耦合，便于统一管理；提供XML标签，支持编写动态SQL语句，并可重用

2. 与JDBC相比，减少了50%以上的代码量，消除了JDBC大量冗余的代码，不需要手动开关连接

3. 很好的与各种数据库兼容（因为MyBatis使用JDBC来连接数据库，所以只要JDBC支持的数据库MyBatis都支持）

4. 能够与Spring很好的集成

5. 提供映射标签，支持对象与数据库的ORM字段关系映射；提供对象关系映射标签，支持对象关系组件维护

#### 缺点

1. sql语句编写工作量大，对开发人员sql工地有一定要求
2. sql依赖数据库，切换数据库困难

#### 适用场景

1. mybatis专注于sql本身，是一个足够灵活的DAO层解决方案
2. 对性能要求很高，或者需求变化较多的项目，如互联网项目

#### jdbc交互工具

-  JDBC -> DbUtils(QueryRunner) -> JDBCTemplate 


#### MyBatis和Hibernate区别 

- hibernate：全自动ORM(Object Relation Mapping)框架，sql由框架自动生成和java代码高度耦合,旨在消除sql，即使不懂sql也能熟练开发， 数据库无关性好 ，但定制用HQL，加重了学习成本
- mybatis：半自动ORM框架，代码和sql分离，高度解耦合，方便高度定制sql和优化sql

#### 执行流程

1. 根据配置文件（全局、sql映射）初始化configuration对象

2. 创建一个defaultSqlSession对象

​        包含Configuration及Executor（根据全局配置文件中defaultExecutorType创建对应的Executor）

   3. DefaultSqlSession.getMapper拿到Mapper接口对应的MapperProxy

4. MapperProxy里有DefaultSqlSession

5. 执行增删改查

​        1. 调用DefaultSqlSession增删改查

​        2. 创建StatementHandler

​         （同时创建ParameterHandler,ResultSetHandler）

​        3. 调用StatementHandler预编译参数以及设置参数值

​          使用ParameterHandler给sql设置参数

​        4. 调用StatementHandler增删改查

​        5. ResultSetHandler封装结果

​		 ![img](http://zhangsike.gitee.io/javasummary/img/mybatis-process.jpg) 

#### 常见问题

- **#{}和${}区别**

  1. #{}是预编译处理，${}是字符串替换，Mabatis在处理#{}时会将sql中的#替换为? 号，调用 PreparedStatement的set方法来赋值 ，在处理${}时，就是把${}替换成变量的值。

  2. **使用#{}可以有效的防止SQL注入**，提高系统安全性。

- **当实体类中的属性名和表中的字段名不一样 ，怎么办 ？**

  1.  通过在查询的sql语句中定义字段名的别名，让字段名的别名和实体类的属性名一致 
  2.  通过<resultMap>来映射字段名和实体类属性名的一一对应的关系 

-  **Mybatis是如何进行分页的？分页插件的原理是什么？**

  1. Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页。可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。

  2. 分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数

- **Xml映射文件中，除了常见的select|insert|updae|delete标签之外，还有哪些标签**

  - 还有很多其他的标签，<resultMap>、<parameterMap>、<sql>、<include>、<selectKey>，加上动态sql的9个标签，trim|where|set|foreach|if|choose|when|otherwise|bind等，其中<sql>为sql片段标签，通过<include>标签引入sql片段，<selectKey>为不支持自增的主键生成策略标签 

- **最佳实践中，通常一个Xml映射文件，都会写一个Dao接口与之对应，请问，这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？**

  - Dao接口，就是人们常说的Mapper接口，接口的全限名，就是映射文件中的namespace的值，接口的方法名，就是映射文件中MappedStatement的id值，接口方法内的参数，就是传递给sql的参数。Mapper接口是没有实现类的，当调用接口方法时，接口全限名+方法名拼接字符串作为key值，可唯一定位一个MappedStatement，举例：com.mybatis3.mappers.StudentDao.findStudentById，可以唯一找到namespace为com.mybatis3.mappers.StudentDao下面id = findStudentById的MappedStatement。在Mybatis中，每一个<select>、<insert>、<update>、<delete>标签，都会被解析为一个**MappedStatement**对象。

  - Dao接口里的方法，是**不能重载**的，因为是**权限名（ namespace ）+方法名的保存和寻找策略**。

  - Dao接口的工作原理是JDK动态代理，Mybatis运行时会使用JDK动态代理为Dao接口生成代理proxy对象，代理对象proxy会拦截接口方法，转而执行MappedStatement所代表的sql，然后将sql执行结果返回