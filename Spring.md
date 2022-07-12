# Spring

> [ Spring面试题（2020最新版）_ThinkWon的博客-CSDN博客_spring面试题](https://thinkwon.blog.csdn.net/article/details/104397516)

​	Spring可以帮助我们管理bean对象。

## IOC

​	控制反转，将对象的控制权交给Spring，由Spring来负责控制对象的生命周期和依赖关系。

## DI

​	依赖注入，程序在运行时依赖IOC容器来动态注入对象所需要的外部依赖。

### 接口注入

​	灵活性和易用性差，Spring4时开始被放弃。

### 构造器注入

​	通过容器触发一个类的构造器实现。

### Setter注入

​	容器调用类的无参构造器或无参static工厂方法实例化bean后，调用bean的setter方法实现。

## BeanFactory

​	Spring中最底层的接口。

​	可以作为Spring的容器，生产bean的工厂，负责生产和管理各个bean实例。

​	采用延迟加载的方式来注入bean，只有当使用到某个bean时，才对该bean进行加载实例化。

## ApplicationContext

​	可以作为Spring的容器，是BeanFactory的子接口。

​	除却BeanFactory所具有的基本功能，还提供了更加完整的框架功能。

​	容器启动时一次性创建所有的bean。

## Aware

## BeanDefinition

​	bean的定义信息。

## PostProcessor

​	增强器。

### BeanPostProcessor

### BeanFactoryProcessor

## 循环依赖

​	简单理解，类A中注入了类B，类B中注入了类A。

​	循环依赖的前置条件：

* Bean必须是单例。

## 三级缓存

1. `singletonObjects`：存储所有创建好的单例bean。
2. `earlySingletonObjects`：存储完成实例化 ，但是还未进行属性注入以及初始化的对象。
3. `singletonFactories`：提前暴露的一个单例工厂，二级缓存中存储的就是从这个工厂中获取的对象。

## Bean的生命周期

1. 得到bean的配置信息。
2. 通过利用反射的`createBeanInstance()`方法完成对bean的实例化。
3. 通过`populateBean()`方法调用set方法进行属性的赋值。
4. 设置beanName
5. 传入classLoader实例
6. 前置增强器对其增强
7. init-method
8. 后置增强器对其增强 
9. 销毁执行destory()方法
10. 根据destory-method执行相关方法

## AOP

​	面向切面，作为面向对象的补充。用于将那些与业务无关，但对多个对象产生影响的公共行为和逻辑，抽取并封装为一个可重用模块。

### 代理方式

#### JDK动态代理

#### CGLIB动态代理

### 连接点

​	程序运行过程所执行的方法。

### 切点

​	定义对哪些连接点进行拦截

### 切面

​	被抽取出来的公共模块

### 通知

​	要在连接点上执行的动作。

### 目标对象

### 织入

### 引入

## 事务

​	Spring提供了两种处理事务的方式。

### 编程式事务

​	在代码中手动的管理事物提交、回滚的操作，代码侵入性比较强。

```java
try {
    //TODO something
     transactionManager.commit(status);
} catch (Exception e) {
    transactionManager.rollback(status);
    throw new InvoiceApplyException("异常失败");
}
```

### 声明式事务

​	基于AOP面向切面，他将具体业务于事物处理部分解耦出来，代码侵入性很低，所以实际开发中，声明式事物用的比较多。声明式事物也有两种存在方式，一种是基于TX和AOP的XML配置文件的方式存在，一种就是基于`@Transactional`注解的方式。

```java
	@Transactional
    @GetMapping("/test")
    public String test() {
        //TODO
    }
```

### @Transcational

* 作用类：当把@Transcational注解放在类上时，表示所有该类的public方法都配置上相同的事物属性
* 作用方法：当配置了@Transcational，方法也配置了@Transcational,那么方法上的事物属性会覆盖掉类上声明的事物属性。
* 作用接口：不推荐这种使用方式，因为一旦标注在Interface上并且配置了Spring AOP使用CGLib动态代理，将会导致@Transcational失效。

#### 参数

* propagation：控制事务的传播行为。
  * Propagation.REQUIRE
  * Propagation.SUPPORTS
  * Propagation.MANDATORY
  * Propagation.REQUIRES_NEW
  * Propagation.NOT_SUPPORTED
  * Propagation.NEVER
  * Propagation.NESTED

* isolation：控制事务的隔离级别。
  * Isolation.DEFAULT
  * Isolation.READ_UNCOMMITTED
  * Isolation.READ_COMMITTED
  * Isolation.REPEATABLE_READ
  * Isolation.SERIALIZABLE

* timeout：超时自动回滚。

* readonly：指定事务是否是只读。

* rollback

* noRollback

#### 失效场景

1. 指定在非public的方法。
2. 事务传播行为配置错误。
3. 自行捕获异常并不抛出异常。
4. 抛出异常类型错误。
5. 数据库表的引擎不支持事务。