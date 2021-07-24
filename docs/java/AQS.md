#### 简介

-  AQS的全称是AbstractQueuedSynchronizer(**抽象队列同步器)**，它的定位是为Java中几乎所有的锁和同步器提供一个基础框架 ,AQS是实现ReentrantLock的关键核心组件。

- AQS是一个抽象类，不可以被实例化，它的设计之初就是为了让子类通过继承来实现多样的功能的。它内部提供了一个FIFO的等待队列，用于多个线程等待一个事件（锁）。它有一个重要的状态标志——state，该属性是一个int值，表示对象的当前状态（如0表示lock，1表示unlock）。AQS提供了三个protected final的方法来改变state的值，分别是：getState、setState(int)、compareAndSetState(int, int)。根据修饰符，它们是不可以被子类重写的，但可以在子类中进行调用，这也就意味着子类可以根据自己的逻辑来决定如何使用state值

#### 关键点

- 状态变量int state，0,1表示没加锁和加锁
- AQS队列，等待获取锁的线程将被加入队列
- Condition队列
- 模板方法
- 需要子类实现的方法

