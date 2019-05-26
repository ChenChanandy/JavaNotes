- [MyBatis简介](#mybatis简介)
- [数据库连接池](#数据库连接池)
- [Log4J](#Log4J)

## MyBatis简介

Mybatis开源免费框架，原名叫 iBatis，2010在google code，2013年迁移到 github。

**作用：** 数据访问层框架，底层是对 JDBC 的封装。

**优点：**使用 mybatis 时不需要编写实现类，只需要写需要执行的sql 命令。



## 数据库连接池

在内存中开辟一块空间，存放多个数据库连接对象。JDBC Tomcat Pool，直接由 tomcat 产生数据库连接池。

- active 状态:当前连接对象被应用程序使用中
- Idle 空闲状态:等待应用程序使用

使用数据库连接池的**目的**

- 在高频率访问数据库时，使用数据库连接池可以降低服务器系统压力，提升程序运行效率。小型项目不适用数据库连接池。

**实现 JDBC tomcat Pool 的步骤**

- 在 web 项目的 META-INF 中存放 context.xml，在 context.xml 编写数据库连接池相关属性

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <Context>
      <Resource
      driverClassName="com.mysql.jdbc.Driver"
      url="jdbc:mysql://localhost:3306/ssm"
      username="root"
      password="smallming"
      maxActive="50"
      maxIdle="20"
      name="test"
      auth="Container"
      maxWait="10000"
      type="javax.sql.DataSource"
      />
  </Context>
  ```

- 把项目发布到 tomcat 中，数据库连接池产生了。



## Log4J

由 apache 推出的开源免费日志处理的类库。

为什么需要**日志**：

- 在项目中编写 System.out.println()输出到控制台，当项目发布到 tomcat 后，没有控制台(在命令行界面能看见)，不容易观察一些输出结果。
- log4j 作用：不仅能把内容输出到控制台，还能把内容输出到文件中，便于观察结果。

**使用**

```properties
log4j.rootCategory=DEBUG, CONSOLE ,LOGFILE

log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=%C %d{YYYY-MM-dd hh:mm:ss} %m %n
log4j.appender.LOGFILE=org.apache.log4j.FileAppender
log4j.appender.LOGFILE.File=E:/my.log
log4j.appender.LOGFILE.Append=true
log4j.appender.LOGFILE.layout=org.apache.log4j.PatternLayout
log4j.appender.LOGFILE.layout.ConversionPattern=%C %m %L %n
```

**Log4j 输出级别**

- fatal(致命错误) > error (错误) > warn (警告) > info(普通信息) > debug(调试信息)

- 在 log4j.properties 的第一行中控制输出级别

**pattern中常用几个表达式：**

- %C 包名+类名

- %d{YYYY-MM-dd HH:mm:ss} 时间

- %L 行号

- %m 信息

- %n 换行