## jvm
---

- [JVM类型](# JVM类型)
- [JVM内存模型](# JVM内存模型)
- [堆](# 堆)
- [程序计数器](# 程序计数器)
- [虚拟机栈](# 虚拟机栈)
- [本地方法栈](# 本地方法栈)
- [执行引擎](# 执行引擎)
- [jvm常用参数](# JVM常用参数)
- [jvm线程模型](# jvm线程模型)

### JVM类型

主流商用的主要是Hotspot VM，主流的有Oracle JDK和OpenJDK，其区别在于OpenJDK是OracleJDK的开源实现，没有版权问题，linux服务器上一般用openJDK，Oracle JDK1.8及以后的版本在2019年1月后需要授权

- Hotspot, JRockit(oracle),oracle将这两种vm进行了整合，统一对外称oracle JDK
- J9 (iBM)，IBM产品专用
-  Azul  ,Zing (Azul)  
- 其他各种jvm，比如android  Dalvik VM ， Alibaba Dragonwell8 JDK 等等

### JVM内存模型

<img src="https://images.gitee.com/uploads/images/2020/0424/140526_8971600a_2047979.png" style="zoom:75%;" />

### 堆

-  堆GC：
  -  垃圾回收算法
    - 复制算法，没有碎片问题，但可用内容缩小到原来一半
    - 标记清除，标记和清除过程效率不高，产生大量碎片
  - 标记整理，解决了复制算法的缺陷
  - 分代收集算法，是目前大部分虚拟机采用的算法
    - 引用计数法，优点：可以很快执行，缺点：无法检测出循环引用
    - 不可达分析法，从GC root起搜索,效率没有引用计数快
  -  新生代垃圾回收动作，**Minor GC** ，主要使用**复制算法**
  -  老生代垃圾回收动作，**Major GC/Full GC**，使用**标记清除**为主，因为要对整个堆整理，所以比较慢，尽量较少Full GC
  -  老生代内存不足会发生Full GC，如果回收后还是不足，则抛出 java.lang.OutOfMemoryError: Java heap space
  -  **不管是Minor GC还是FullGC，总会stop the world**
-  JVM管理的最大的一块内存区域，存放着对象的实例，是线程共享区。
- JAVA堆的分类： 

  - JVM内存划分为堆内存和非堆内存，堆内存分为年轻代（Young Generation）、老年代（Old Generation），默认比例为**2:1**，非堆内存就一个永久代（Permanent Generation）。
  
  - 年轻代又分为Eden和Survivor区。Survivor区由FromSpace和ToSpace组成。Eden区占大容量，Survivor两个区占小容量，默认比例是**8:1:1**。
 -  堆内存用途：存放的是对象，垃圾收集器就是收集这些对象，然后根据GC算法回收。
 -  非堆内存用途：永久代，也称为方法区，存储程序运行时长期存活的对象，比如类的元数据、方法、常量、属性等。
 -  在JDK1.8版本废弃了永久代，替代的是元空间（MetaSpace），元空间与永久代上类似，都是方法区的实现，他们最大区别是：元空间并不在JVM中，而是使用本地内存。元空间有注意有两个参数：
- MetaspaceSize ：初始化元空间大小，控制发生GC阈值
   - MaxMetaspaceSize ： 限制元空间大小上限，防止异常占用过多物理内存
   
  - <u>新生成的对象首先放到年轻代Eden区，当Eden空间满了，触发**Minor GC**，存活下来的对象移动到Survivor0区，Survivor0区满后触发执行**Minor GC**，Survivor0区存活对象移动到Suvivor1区，这样保证了一段时间内总有一个survivor区为空。经过多次Minor GC仍然存活的对象移动到老年代。</u>
  - <u>老年代存储长期存活的对象，占满时会触发**Major GC=Full GC**，GC期间会停止所有线程等待GC完成，所以对响应要求高的应用尽量减少发生Major GC，避免响应超时。Minor GC ： 清理年轻代 Major GC ： 清理老年代Full GC ： 清理整个堆空间，包括年轻代和永久代,所有GC都会停止应用所有线程</u>。

### 程序计数器

- 程序计数器（Program Counter Register）是[JVM](https://www.bdm361.com/portal/article/index/id/82.html)中一块较小的内存区域，保存着当前线程执行的虚拟机字节码指令的内存地址（可以看作当前线程所执行的字节码的行号指示器）。
- 如果线程执行的是java方法，这个计数器记录的是正在执行的虚拟机字节码指令的地址（可以理解为上图所示的行号），如果正在执行的是native方法，这个计数器的值为undefined。
- JVM的多线程是通过线程轮流切换并分配CPU执行时间片的方式来实现的，任何一个时刻，一个CPU都只会执行一条线程中的指令。为了保证线程切换后能恢复到正确的执行位置，每条线程都需要有一个独立的程序计数器，各线程间的程序计数器独立存储，互不影响。
- 此区域是唯一一个在java虚拟机规范中没有规定任何OutOfMemoryError情况的区域，因为程序计数器是由虚拟机内部维护的，不需要开发者进行操作。

### 虚拟机栈

- 虚拟机栈（Java Virtual Machine Stacks）是线程隔离的，每创建一个线程时就会对应创建一个Java栈，即每个线程都有自己独立的虚拟机栈。这个栈中又会对应包含多个栈帧，**每调用一个方法时就会往栈中创建并压入一个栈帧**，栈帧存储**局部变量表、操作栈、动态链接、方法出口**等信息，每一个方法从调用到最终返回结果的过程，就对应一个栈帧从入栈到出栈的过程。


- 虚拟机栈是一个后入先出的数据结构，线程运行过程中，**只有处于栈顶的栈帧才是有效的，称为当前栈帧**，与这个栈帧相关联的方法称为当前方法，当前活动帧栈始终是虚拟机栈的栈顶元素。


- 局部变量表存放了编译期可知的各种基本数据类型和对象引用类型。通常我们所说的“**栈内存**”指的就是局部变量表这一部分。
- 局部变量表所需的内存空间在编译期间完成分配，当进入一个方法时，这个方法需要在帧分配多少内存是固定的，运行期间不会改变局部变量表的大小。
- [64位](https://www.baidu.com/s?wd=64位&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)的long和double类型的数据会占用2个局部变量空间，其余的数据类型只占用1个

### 本地方法栈

- 和虚拟机栈类似，均具有线程隔离特点都能抛出stackOverFlowError和outOfMemoryError异常，不同的是本地方法栈执行的是native方法

### 执行引擎

- jvm的核心部分负责执行字节码或则native方法

### JVM常用参数

| -Xms                   | 堆内存初始大小，单位m、g                                  |
| ---------------------- | --------------------------------------------------------- |
| -Xmx（MaxHeapSize）    | 堆内存最大允许大小，一般不要大于物理内存的80%             |
| -XX:PermSize           | 非堆内存初始大小，一般应用设置初始化200m，最大1024m就够了 |
| -XX:MaxPermSize        | 非堆内存最大允许大小                                      |
| -XX:NewSize（-Xns）    | 年轻代内存初始大小                                        |
| -XX:MaxNewSize（-Xmn） | 年轻代内存最大允许大小，也可以缩写                        |
| -XX:SurvivorRatio=8    | 年轻代中Eden区与Survivor区的容量比例值，默认为8，即8:1    |
| -Xss                   | 线程堆栈内存大小，最小128k，jdk1.5以后默认为1M之前是256KB |

一个调参示例

```shell
java -Xmx1024m -Xms512m -Xss512k -jar 3d-cloud-app-server-0.0.1-SNAPSHOT.jar --http.port=7000 --server.port=7001

```

### jvm线程模型

- 对于OS来说java只是其一个应用
- hotspot模型 1:1模型，线程上锁等均交给os管理

### java中的引用

- 强引用, 普通定义的引用都是强引用
- 软引用(SoftReference)，有用但非必须，会在内存不足时被回收掉
- 弱引用(WeakReference)，非必须，比软引用还弱，只能生存在下一次垃圾回收之前
- 虚引用(PhantomReference)，完全不会对对象的生存时间构成影响，作用是在被引用的对象被GC后得到一个通知，通常用语检测对象是否还活着