# redis安装

### 1.下载

 Github下载地址：https://github.com/MicrosoftArchive/redis/releases 

 百度网盘下载地址 https://pan.baidu.com/s/1z1_OdNVbtgyEjiktqgB83g 密码：kdfq 

### 2.安装(windows)

解压压缩包到某个目录，例如D:\Redis-x64-3.2.100，进入解压目录 cmd启动  redis-server redis.windows.conf 

### 3.安装到window服务

redis-server --service-install redis.windows.conf 

运行输入services.msc找到redis启动

### 4.redis常用指令

- 卸载服务：redis-server --service-uninstall
- 开启服务：redis-server --service-start
- 停止服务：redis-server --service-stop 

### 5.redis进阶

- 数据类型

  - string，最大支持的值是**512M**
  - hash
  - list
  - set
  - sorted set 排序的set，集合中最大的成员数为2的32次方(4294967295, 每个集合可存储40多亿个成员)

- 线程模型

  redis采用单线程避免上下文切换，纯内存操作, 非阻塞IO多路复用机制

- 持久化机制（2种）

  -  RDB 持久化方式就是将**数据保存到 RDB文件**中，而 AOF 持久化则是将执行的 set,sadd,lpush 三个**命令保存到 AOF 文件**中

  - rdp，默认持久化方式，产生 dump.rdb 二进制快照文件，实行主从复制，只有一个dump.rdb文件
  - aof，追加的方式保存文件， 随着Redis不断的进行，AOF 的文件会越来越大， 当AOF文件的大小超过所设定的阈值时，Redis就会 重写AOF文件，并启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集 

- 雪崩，穿透，预热，更新，降级

  - **缓存穿透**，是指在查询数据过程中缓存中没有，数据库中也没有，每次都绕过缓存去数据库查询， 最常见的解决办法是采用**布隆过滤器** 
  -  **缓存击穿** ， 指一个key非常热点，大并发集中对这个key进行访问，当这个key在失效的瞬间，仍然持续的大并发访问就穿破缓存，转而直接请求数据库。
    解决方案;在访问key之前，采用SETNX（set if not exists）来设置另一个短期key来锁住当前key的访问，访问结束再删除该短期key 
  - **缓存雪崩**， 原有缓存失效，新缓存未到期间 导致原本应该去缓存区查询的数据都到数据库去查询 CPU和内存造成巨大压力，严重的会造成数据库宕机。从而形成一系列连锁反应，造成整个系统崩溃 （ 例如：我们设置缓存时采用了相同的过期时间，在同一时刻出现大面积的缓存过期 ）
  - **缓存预热**， 存预热就是系统上线后，将相关的缓存数据直接加载到缓存系统 
  - **缓存更新**，1. 定时去清理过期的缓存 ,2.  有用户请求过来时，再判断这个请求所用到的缓存是否过期，过期的话就去底层系统得到新数据并更新缓存 
  - **缓存降级**， 当访问量剧增、服务出现问题（如响应时间慢或不响应）或非核心服务影响到核心流程的性能时，仍然需要保证服务还是可用的，即使是有损服务。系统可以根据一些关键数据进行自动降级，也可以配置开关实现人工降级 



