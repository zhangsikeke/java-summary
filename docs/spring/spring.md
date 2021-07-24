- spring生命周期

  - scope可以配置为singleton,prototype,request,session,application 默认为singleton
  - spring bean生命周期为4个  实例化(Instantiation) -> 属性赋值(Populate) ->初始化(Initialization) -> 销毁(Destruction) 
  - 实例化 -> 属性赋值 ->初始化 -> 销毁
- ioc
- aop
  - 基于动态代码技术实现，分JDK动态代理和cglib动态代理
  - 如果目标对象实现了接口，默认情况下会采用JDK的动态代理实现AOP。
  - 如果目标对象实现了接口，可以强制使用cglib实现AOP
  - 如果目标对象没有实现了接口，必须采用cglib库，Spring会自动在JDK动态代理和cglib之间转换
- spring循环依赖
  - 循环依赖分2种，1.构造器循环依赖，2.属性循环依赖，构造器循环依赖无法解决，jvm会报错。spring解决的是属性依赖
  - spring通过三级缓存的方式解决属性循环依赖
    1.   一级缓存**singletonObjects** ， 用于保存BeanName和创建bean实例之间的关系,beanName -> bean instance 
    2.  二级缓存**earlySingletonObjects** ， 保存提前曝光的单例bean对象 
    3.  三级缓存**singletonFactories** ， 保存beanName和创建bean实例之间的关系,与singletonObjects不同的地方在于，当一个单例bean被放到这里面后，bean在创建过程中，可以通过getBean方法获取到,目的是用来检测循环引用 。 在创建bean的时候，首先从缓存中获取单例的bean，这个缓存就是 singletonObjects  ，如果获取不到且bean正在创建中，就再从  earlySingletonObjects  中获取，如果还是获取不到且允许从singletonFactories中通过getObject拿到对象，就从  singletonFactories  中获取，如果获取到了就存入  并从  singletonFactories  中移除。 现在有A的field或者setter依赖B的实例对象，同时B的field或者setter依赖了A的实例，A首先开始创建，并将自己暴露到 singletonFactories 中，开始填充属性，此时发现自己依赖B的属性，尝试去get(B)，发现B还没有被创建，所以开始创建B，在进行属性填充时初始化A，就从singletonObjects中获取到了初始化但没有任何属性的A，B拿到A后完成了初始化阶段，将自己放到singletonObjects中,此时返回A，A拿到B的对象继续完成初始化，完成后将自己放到singletonObjects中，由A与B中所表示的A的属性地址是一样的，所以A的属性填充完后，B也获取了A的属性，这样就解决了循环的问题。
    4. ![](https://segmentfault.com/img/bVbIWhM)
- 事物

  - 基本要素（ACID）
    - 原子性
    - 一致性
    - 隔离性
    - 持久性
  - 事物并发问题
    - 脏度
    - 不可重复读
    - 幻读
  - 隔离级别
    - 读未提交
    - 不可重读读
    - 可重复读
    - 串行化



