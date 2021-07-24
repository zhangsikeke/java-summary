# 分布式锁

在单机场景下可以使用内置锁来实现进程同步，但在分布式场景下需要同步的进程可能位于不同节点上，就需要用到分布式锁， 可以保证在分布式部署的应用集群中，同一个方法在同一操作只能被一台机器上的一个线程执行。

场景，假设库存剩余服务部署在三个节点上，只剩余1个，三个用户都查询到剩余1个，都下单了，结果另外两个人肯定都拿不到货。发生了超卖， 在这种场景中，我们就需要一种方法解决这个问题，**这就是分布式锁要解决的问题**。 

![](https://pic1.zhimg.com/80/v2-a3bd57a68d71f4a70aee9fadac12178c_720w.jpg) 

#### **分布式锁的实现与特性**

- #### **分布式锁的实现**

  - 基于数据库实现分布式锁
  - 基于缓存实现分布式锁
    - Memcached：利用 Memcached 的 add 命令。此命令是原子性操作，只有在 key 不存在的情况下，才能 add 成功，也就意味着线程得到了锁 
    - Redis：和 Memcached 的方式类似，利用 Redis 的 setnx 命令。此命令同样是原子性操作，只有在 key 不存在的情况下，才能 set 成功
  - 基于zookeeper实现分布式锁：利用 Zookeeper 的顺序临时节点，来实现分布式锁和等待队列。Zookeeper 设计的初衷，就是为了实现分布式锁服务的 
  - Chubby：Google 公司实现的粗粒度分布式锁服务，底层利用了 Paxos 一致性算法 

- #### **分布式锁的特性**

  -  在分布式系统环境下，一个方法在同一时间只能被一个机器的一个线程执行 
  -  高可用的获取锁与释放锁 
  -  高性能的获取锁与释放锁 
  -  具备可重入特性 
  -  具备非阻塞锁特性，即没有获取到锁将直接返回获取锁失败 

#### 解决方案

[redisson github](https://github.com/redisson/redisson/wiki/目录)，[demo](https://gitee.com/zhangsike/springboot-learn/tree/master/springboot-redisson)

学习参考： https://zhuanlan.zhihu.com/p/143641599 

