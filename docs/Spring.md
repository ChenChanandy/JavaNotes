- [IOC](#IOC)
  - [IOC是什么](#IOC是什么)
  - [bean](#bean) 
  - [代码体现](#代码体现)
- [AOP](#AOP)
  - [常用概念](#常用概念)
  - [相关代码](#相关代码)
- [声明式事务](#声明式事务)
  - [声明式事务中属性解释](#声明式事务中属性解释)
- [applicationContext-spring.xml](#applicationcontext-spring.xml)

# Spring

Spring是J2EE应用程序框架，是轻量级的IOC和AOP的容器框架，主要针对JavaBean的生命周期进行管理的轻量级容器，可以单独使用，也可以和MyBatis、SpringMVC框架等组合使用。

## IOC

IOC控制反转,，又称为“依赖注入”

### IOC是什么

- IOC完成的事情原先由程序员主动通过new实例化对象事情,转交给 Spring 负责
- 控制反转：控制指-->控制类的对象；反转指-->转交给 Spring 负责
- IOC最大的作用：**解耦**，程序员不需要管理对象，解除了对象管理和程序员之间的耦合

**Spring IOC的初始化过程**

![img](imgs/16387903ee72c831)

新版本中ApplicationContext接口是BeanFactory子接口，BeanFactory的功能在ApplicationContext中都有。

### bean

在 Spring 中，那些组成应用程序的主体及由 Spring IOC 容器所管理的对象，被称之为 bean。简单地讲，bean 就是由 IOC 容器初始化、装配及管理的对象，除此之外，bean 就与应用程序中的其他对象没有什么区别了。而 bean 的定义以及 bean 相互间的依赖关系将通过配置元数据来描述。Spring中的bean默认都是单例的。

**bean的作用域**

![img](imgs/166fd45773d5dd2e)

### 代码体现

````xml
<!-- applicationContext.xml中配置JDBC -->
<!-- 加载属性文件 -->
<context:property-placeholder location="classpath:db.properties"/>
<!-- id 表示获取到对象标识，class：创建哪个类的对象-->
<bean id="dataSource"class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <!-- 给Bean的属性赋值(注入) -->
    <property name="driverClassName" value="${jdbc.driver}"></property>
    <property name="url" value="${jdbc.url}"></property>
    <property name="username" value="${jdbc.username}"></property>
    <property name="password" value="${jdbc.password}"></property>
</bean>
````



## AOP

面向切面编程

在程序原有纵向执行流程中,针对某一个或某一些方法添加通知，形成横切面过程就叫做面向切面编程。

AOP思想的实现一般都是基于 **代理模式** ，在JAVA中一般采用JDK动态代理模式，但是我们都知道，**JDK动态代理模式只能代理接口而不能代理类**。因此，Spring AOP 会这样子来进行切换，因为Spring AOP 同时支持 CGLIB、ASPECTJ、JDK动态代理。

- 如果目标对象的实现类实现了接口，Spring AOP 将会采用 JDK 动态代理来生成 AOP 代理类；
- 如果目标对象的实现类没有实现接口，Spring AOP 将会采用 CGLIB 来生成 AOP 代理类——不过这个选择过程对开发者完全透明、开发者也无需关心。

**AOP通常用来做：**

- 事务处理 ：执行方法前开启事务，执行完成后关闭事务，出现异常后回滚事务
- 权限判断 ：在执行方法前，判断是否具有权限
- 日志 ：在执行前进行日志处理

### 常用概念

- **原有功能**: 切点，pointcut

- **前置通知**: 在切点之前执行的功能.，before advice
- **后置通知**: 在切点之后执行的功能，after advice
- 如果切点执行过程中出现异常，会触发**异常通知**，throws advice
- 所有功能总称叫做**切面**
- **织入**: 把切面嵌入到原有功能的过程叫做织入

### 相关代码

````xml
<!-- 声明式事务 -->
<tx:advice id="txAdvice" transaction-manager="txManage">
    <tx:attributes>
        <tx:method name="ins*" rollback-for="java.lang.Exception"/>
        <tx:method name="del*" rollback-for="java.lang.Exception"/>
        <tx:method name="upd*" rollback-for="java.lang.Exception"/>
        <tx:method name="*" read-only="true"/>
    </tx:attributes>
</tx:advice>
<!-- 配置AOP -->
<aop:config>
    <aop:pointcut expression="execution(* com.ego.dubbo.service.impl.*.*(..))" id="mypoint"/>
    <aop:advisor advice-ref="txAdvice" pointcut-ref="mypoint"/>
</aop:config>
````



## 声明式事务

事务控制代码已经由 spring 写好，程序员只需要声明出哪些方法需要进行事务控制和如何进行事务控制。

- 声明式事务都是针对于 ServiceImpl 类下的方法
- 事务管理器基于通知(advice)的

### 声明式事务中属性解释

- **name=" "** 哪些方法需要有事务控制，支持*通配符
- **rollback-for=**"异常类型全限定路径"，当出现什么异常时需要进行回滚，手动抛异常一定要给该属性值
- **no-rollback-for=" "** 当出现什么异常时不滚回事务
- **readonly=”boolean”** 是否是只读事务
  - 如果为 true，告诉数据库此事务为只读事务，数据库优化，会对性能有一定提升，所以只要是查询的方法,建议使用此数据
  - 如果为 false(默认值)，事务需要提交的事务，建议新增、删除、修改
- **propagation** 控制事务传播行为；当一个具有事务控制的方法被另一个有事务控制的方法调用后,需要如何管理事务
  - REQUIRED (**默认值**): 如果当前有事务，就在事务中执行，如果当前没有事务，新建一个事务
  - SUPPORTS:如果当前有事务就在事务中执行，如果当前没有事务，就在非事务状态下执行
  - MANDATORY:必须在事务内部执行，如果当前有事务，就在事务中执行；如果没有事务，报错
  - REQUIRES_NEW:必须在事务中执行，如果当前没有事务，新建事务；如果当前有事务，把当前事务挂起
  - NOT_SUPPORTED:必须在非事务下执行,如果当前没有事务,正常执行；如果当前有事务，把当前事务挂起
  - NEVER:必须在非事务状态下执行，如果当前没有事务,正常执行； 如果当前有事务,报错
  - NESTED:必须在事务状态下执行，如果没有事务,新建事务；如果当前有事务,创建一个嵌套事务
- **isolation=" "** 事务隔离级别，在多线程或并发访问下如何保证访问到的数据具有完整性的
  - DEFAULT: 默认值，由底层数据库自动判断应该使用什么隔离界别
  - READ_UNCOMMITTED: 可以读取未提交数据，可能出现脏读、不重复读、幻读；**效率最高**
  - READ_COMMITTED:只能读取其他事务已提交数据，可以防止脏读,可能出现不可重复读和幻读
  - REPEATABLE_READ: 读取的数据被添加锁,防止其他事务修改此数据，可以防止不可重复读、脏读、可能出现幻读
  - SERIALIZABLE: 排队操作，对整个表添加锁。一个事务在操作数据时,另一个事务等待事务操作完成后才能操作这个表；最安全的，效率最低的

**脏读**

一个事务(A)读取到另一个事务(B)中未提交的数据，另一个事务中数据可能进行了改变，此时 A事务读取的数据可能和数据库中数据是不一致的，此时认为数据是脏数据,读取脏数据过程叫做脏读

**不可重复读**

- 主要针对的是某行数据(或行中某列)，针对的操作是修改操作，两次读取在同一个事务内
- 当事务 A 第一次读取事务后，事务 B 对事务 A 读取的数据进行修改，事务 A 中再次读取的数据和之前读取的数据不一致，过程不可重复读

**幻读**

- 主要针对的操作是新增或删除，两次事务的结果
- 事务 A 按照特定条件查询出结果，事务 B 新增了一条符合条件的数据。事务 A 中查询的数据和数据库中的数据不一致的，事务 A 好像出现了幻觉,这种情况称为幻读



## applicationContext-spring.xml

````xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx" 
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx.xsd" default-autowire="byName">
	<!-- 注解扫描 可以使用通配符* -->
	<context:component-scan base-package="com.ego.*.service.impl"></context:component-scan>
	<!-- 加载属性文件 可以使用通配符* -->
	<context:property-placeholder location="classpath*:*.properties"/>
	<!-- 数据源 -->
	<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="driverClassName" value="${jdbc.driver}"></property>
		<property name="url" value="${jdbc.url}"></property>
		<property name="username" value="${jdbc.username}"></property>
		<property name="password" value="${jdbc.password}"></property>
	</bean>
	<!-- SqlSessionFactory -->	
	<bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource"></property>
		<property name="typeAliasesPackage" value="com.ego.pojo"></property>
		<property name="configLocation" value="classpath:mybatis.xml"></property>
	</bean>
	<!-- 扫描器 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="com.ego.mapper"></property>
		<property name="sqlSessionFactoryBeanName" value="factory"></property>
	</bean>
	<!-- 事务管理器 -->
	<bean id="txManage" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	<!-- 声明式事务 -->
	<tx:advice id="txAdvice" transaction-manager="txManage">
		<tx:attributes>
			<tx:method name="ins*" rollback-for="java.lang.Exception"/>
			<tx:method name="del*" rollback-for="java.lang.Exception"/>
			<tx:method name="upd*" rollback-for="java.lang.Exception"/>
			<tx:method name="*" read-only="true"/>
		</tx:attributes>
	</tx:advice>
	<!-- 配置AOP -->
	<aop:config>
		<aop:pointcut expression="execution(* com.ego.dubbo.service.impl.*.*(..))" id="mypoint"/>
		<aop:advisor advice-ref="txAdvice" pointcut-ref="mypoint"/>
	</aop:config>
</beans>
````

