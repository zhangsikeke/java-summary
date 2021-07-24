### jvm调优工具

 **jconsole-jdk自帶，jProfile-商业软件，jvisualvm-jdk自带** 

### 调优概念

 对JVM内存的系统级的调优主要的目的是减少GC的频率和Full GC的次数

- 通过jvisualvm等工具观看GC时间已近频率实例数量等，来确定是否需要优化
- 合理的设置Xms，Xmx，Xss等系统参数

- 尽量减少Full GC的频率和GC时间
- 尽量让对象在新生代被GC
- 生成堆dump， 分析dump文件(比如jvisualvm工具)
  - 如果各项参数设置合理，系统没有超时日志出现，GC频率不高，GC耗时不高，那么没有必要进行GC优化，如果GC时间超过1-3秒，或者频繁GC，则必须优化。
  - 注：如果满足下面的指标，则一般不需要进行GC：
    - Minor GC执行时间不到50ms；
    - Minor GC执行不频繁，约10秒一次；
    - Full GC执行时间不到1s；
    - Full GC执行频率不算频繁，不低于10分钟1次；

