#### 上下文工具

```java
package com.service;

import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;

@Component
public class ApplicationContextHolder implements ApplicationContextAware {
    private static ApplicationContext applicationContext;

    @Override
    public void setApplicationContext(ApplicationContext ctx) throws BeansException {
        applicationContext = ctx;
    }

    public static ApplicationContext getApplicationContext() {
        return applicationContext;
    }

    public static <T> T getBean(Class<T> clazz) {
        return applicationContext.getBean(clazz);
    }
    @SuppressWarnings("unchecked")
    public static <T> T getBean(String name) {
        return (T)applicationContext.getBean(name);
    }
}
```

#### 事务工具

```java
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.TransactionDefinition;
import org.springframework.transaction.TransactionStatus;
import org.springframework.transaction.support.DefaultTransactionDefinition;
import com.service.ApplicationContextHolder;

/**
 * 事务 https://blog.csdn.net/weixin_39625809/article/details/80707695
 */
public class TxUtil {
    /**
    *获取事务管理器
    */
    public static DataSourceTransactionManager getTxManager() {
        DataSourceTransactionManager transactionManager =
            (DataSourceTransactionManager)ApplicationContextHolder.getBean(DataSourceTransactionManager.class);
        return transactionManager;
    }

    /**
     * 开启事务
     * @return
     */
    public static TransactionStatus newTransaction() {
        DefaultTransactionDefinition def = new DefaultTransactionDefinition();
        // 事物隔离级别，需要在事务中运行
        def.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);
        // 设置事务失效时间，超时则事务回滚
        def.setTimeout(30);
        // 获得事务状态
        TransactionStatus status = getTxManager().getTransaction(def); 
        return status;
    }

    /**
     * 提交
     * @param status
     */
    public static void commit(TransactionStatus status) {
        if (status != null) {
            getTxManager().commit(status);
        }
    }

    /**
     * 回滚
     * @param status
     */
    public static void rollback(TransactionStatus status) {
        if (status != null) {
            getTxManager().rollback(status);
        }
    }
}
```

#### 直接使用

```java
// springboot 开启事务以及手动提交事务，可以在服务类上加上两个注解。
// https://www.cnblogs.com/smallfa/p/13683332.html
@Autowired
DataSourceTransactionManager dataSourceTransactionManager;
@Autowired
TransactionDefinition transactionDefinition;
// 手动开启事务
TransactionStatus transactionStatus = dataSourceTransactionManager.getTransaction(transactionDefinition);
// 手动提交事务
dataSourceTransactionManager.commit(transactionStatus);//提交
// 手动回滚事务
dataSourceTransactionManager.rollback(transactionStatus);//最好是放在catch 里面,防止程序异常而事务一直卡在哪里未提交
```

