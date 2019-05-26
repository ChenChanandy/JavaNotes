- [MySQL简介](#mysql简介)
  - [三大范式](#三大范式)
  - [事务](#事务)
  - [事务隔离级别](#事务隔离级别)
  - [存储引擎](#存储引擎)
- [SQL语法](#sql语法)
- [索引](#索引)
- [数据库优化](#数据库优化)
- [MySQL主从简介](#mysql主从简介)
- [MyCat简介](#mycat简介)

## MySQL简介

MySQL 是最流行的关系型数据库管理系统，在 WEB 应用方面 MySQL 是最好的 RDBMS(Relational Database Management System：关系数据库管理系统)应用软件之一。

**常用工具**

- Navicat for MySQL

- PowerDesigner数据库设计



### 三大范式

- 第一范式 (1NF)
  - 属性不可分
- 第二范式 (2NF)
  - 每个非主属性完全函数依赖于键码
- 第三范式 (3NF)
  - 非主属性不传递函数依赖于键码



### 事务

事务指的是满足 ACID 特性的一组操作，可以通过 Commit 提交一个事务，也可以使用 Rollback 进行回滚。

#### ACID

- **原子性（Atomicity）**

  事务被视为不可分割的最小单元，事务的所有操作要么全部提交成功，要么全部失败回滚。

  回滚可以用回滚日志来实现，回滚日志记录着事务所执行的修改操作，在回滚时反向执行这些修改操作即可。

- **一致性（Consistency）**

  数据库在事务执行前后都保持一致性状态。在一致性状态下，所有事务对一个数据的读取结果都是相同的。

- **隔离性（Isolation）**

  一个事务所做的修改在最终提交以前，对其它事务是不可见的。

- **持久性（Durability）**

  一旦事务提交，则其所做的修改将会永远保存到数据库中。即使系统发生崩溃，事务执行的结果也不能丢失。

  使用重做日志来保证持久性。



### 事务隔离级别

**未提交读（READ UNCOMMITTED）**

事务中的修改，即使没有提交，对其它事务也是可见的

**提交读（READ COMMITTED）**

一个事务只能读取已经提交的事务所做的修改（一个事务所做的修改在提交之前对其它事务是不可见的）。

MySQL默认隔离级别

**可重复读（REPEATABLE READ）**

保证在同一个事务中多次读取同样数据的结果是一样的。

Oracle默认隔离级别

**可串行化（SERIALIZABLE）**

强制事务串行执行



### 存储引擎

**InnoDB**

是 MySQL **默认**的事务型存储引擎，只有在需要它不支持的特性时，才考虑使用其它存储引擎。

支持事务和外键；数据多版本读取；锁定机制的改进。

**MyISAM**

主要用于优化查询,不支持事务和外键。

使用该数据库引擎会生成三个文件：.frm 表结构信息；.MYD 数据文件；.MYI 表的索引信息。



## SQL语法

```mysql
/* 数据库创建与使用：*/
CREATE DATABASE test;
USE test;

/* 创建表 */
CREATE TABLE mytable (
  # int 类型，不为空，自增
  id INT NOT NULL AUTO_INCREMENT,
  # int 类型，不可为空，默认值为 1，不为空
  col1 INT NOT NULL DEFAULT 1,
  # 变长字符串类型，最长为 45 个字符，可以为空
  col2 VARCHAR(45) NULL,
  # 日期类型，可为空
  col3 DATE NULL,
  # 设置主键为 id
  PRIMARY KEY (`id`)
);

/* 修改表 */
# 添加列
ALTER TABLE mytable ADD col CHAR(20);
# 删除列
ALTER TABLE mytable DROP COLUMN col;
# 删除表
DROP TABLE mytable;

/* 插入数据 */
# 普通插入
INSERT INTO mytable(col1, col2) VALUES(val1, val2);
# 插入检索出来的数据
INSERT INTO mytable1(col1, col2) SELECT col1, col2 FROM mytable2;
#将一个表的内容插入到一个新表
CREATE TABLE newtable AS SELECT * FROM mytable;

/* 更新数据 */
UPDATE mytable SET col = val WHERE id = 1;

/* 删除数据 */
DELETE FROM mytable WHERE id = 1;

/* 查询数据 */
# DISTINCT，相同值只会出现一次。它作用于所有列，也就是说所有列的值都相同才算相同。
SELECT DISTINCT col1, col2 FROM mytable;
#LIMIT，限制返回的行数。可以有两个参数，第一个参数为起始行，从 0 开始；第二个参数为返回的总行数。
SELECT * FROM mytable LIMIT 5; -- 返回前5行
SELECT * FROM mytable LIMIT 0, 5; -- 返回前5行
SELECT * FROM mytable LIMIT 2, 3; -- 返回第3~5行

/* 排序 */
# ASC：升序（默认）；DESC：降序
SELECT * FROM mytable ORDER BY col1 DESC, col2 ASC;

/* 函数 */
# AVG() 会忽略 NULL 行。使用 DISTINCT 可以汇总不同的值。
SELECT AVG(DISTINCT col1) AS avg_col FROM mytable;
	-- AVG()	返回某列的平均值
	-- COUNT()	返回某列的行数
	-- MAX()	返回某列的最大值
	-- MIN()	返回某列的最小值
	-- SUM()	返回某列值之和

/* 分组 */ -- 把具有相同的数据值的行放在同一组中
SELECT col, COUNT(*) AS num FROM mytable GROUP BY col;

/* 子查询 */
SELECT * FROM mytable1 WHERE col1 IN (SELECT col2 FROM mytable2);

/* 连接查询(join) */
# 内连接(inner join),默认就是内连接,可省略inner,on表示连接条件
SELECT A.value, B.value FROM tablea AS A INNER JOIN tableb AS B ON A.key = B.key;
# 外连接(outer join),左外连接left join,右外连接right join
SELECT Customers.cust_id, Orders.order_num FROM Customers LEFT OUTER JOIN Orders ON Customers.cust_id = Orders.cust_id;
# 自然连接(natural join) -- 自然连接是把同名列通过等值测试连接起来的，同名列可以有多个。
SELECT A.value, B.value FROM tablea AS A NATURAL JOIN tableb AS B;
```



## 索引

### 普通索引

这是最基本的索引，它没有任何限制。

如果是CHAR，VARCHAR类型，length可以小于字段实际长度；如果是BLOB和TEXT类型，必须指定 length。

```mysql
-- 创建索引
-- 方式一：
CREATE INDEX indexName ON mytable(username(length));
-- 方式二：修改表结构(添加索引)
ALTER table tableName ADD INDEX indexName(columnName);
-- 方式三：创建表的时候直接指定
CREATE TABLE mytable(   
	ID INT NOT NULL,    
	username VARCHAR(16) NOT NULL,   
	INDEX [indexName] (username(length))   
);  

-- 删除索引
DROP INDEX [indexName] ON mytable;
```



### 唯一索引

它与前面的普通索引类似，不同的就是：索引列的值必须唯一，但允许有空值。如果是组合索引，则列值的组合必须唯一。

```mysql
-- 创建索引
-- 方式一：
CREATE UNIQUE INDEX indexName ON mytable(username(length));
-- 方式二：修改表结构
ALTER table mytable ADD UNIQUE [indexName] (username(length));
-- 方式三：创建表的时候直接指定
CREATE TABLE mytable(  
	ID INT NOT NULL,   
 	username VARCHAR(16) NOT NULL,  
 	UNIQUE [indexName] (username(length))   
); 
```



### 使用ALTER命令添加和删除索引

有四种方式来添加数据表的索引：

````mysql
-- 该语句添加一个主键，这意味着索引值必须是唯一的，且不能为NULL。
ALTER TABLE tbl_name ADD PRIMARY KEY (column_list);
-- 这条语句创建索引的值必须是唯一的（除了NULL外，NULL可能会出现多次）
ALTER TABLE tbl_name ADD UNIQUE index_name (column_list);
-- 添加普通索引，索引值可出现多次
ALTER TABLE tbl_name ADD INDEX index_name (column_list);
-- 该语句指定了索引为 FULLTEXT ，用于全文索引
ALTER TABLE tbl_name ADD FULLTEXT index_name (column_list):
````

### 显示索引信息

```mysql
--  \G 格式化输出信息
SHOW INDEX FROM table_name; \G
```



## 数据库优化

- 查询时尽量避免全盘扫描，考虑在where及order by列上建索引
- 尽量避免在where子句中
  - 对字段进行null判断
    - 否则将引擎放弃使用索引而进行全表扫描
  - 使用!=或<>操作符
    - 同上
  - 使用or来连接条件
    - 同上
  - 对字段进行表达式操作
    - 同上
  - 对字段进行函数操作
    - 同上
  - “=”左边进行函数、算术运算或其他表达式运算
- in和not in也要慎用
  - 会导致全表扫描
  - 对于连续的数值，能用between就不要用in
- 会导致全表扫描的查询
  - select id from t where name like %abc%
- 不要写一些没有意义的查询
- 索引不是越多越好
- 尽量使用数字型字段
- 尽可能的使用varchar代替char
  - 可以节省存储空间，提高查询效率
- 尽量不用使用 *



## MySQL主从简介

MySQL 默认支持主(master)从(slave)功能。配置完主从备份后效果：在主数据库中操作时，从同步进行变化。

**主从本质**：主数据的操作写入到日志中，从数据库从日志中读取，进行操作

**主从原理：**

- 默认 MySQL 没有开启日志功能
- 每个数据库需要有一个 server_id，主 server_id 值小于从server_id
- 每个 mysql 都有一个 uuid，由于虚拟机直接进行克隆，需要修改uuid 的值
- 必须要在主数据库中有一个用户具有被从数据库操作的权限



## MyCat 简介

利用MySQL主从备份功能实现**读写分离：**

- 增加、删除、修改都操作主数据库；查询操作从数据库。
- 优点:提升程序执行性能

**MyCat解释**: 数据库中间件软件。

**MyCat具备分库/分表功能**

- 默认 MyCat 分库(建议使用)
- 可以配置让MyCat进行分表,业务比较复杂,配置起来也比较复杂。

MyCat 中默认 tableRule 要求至少 3 个 database。

**必知的几个概念：**

- 逻辑库：一个包含了所有数据库的逻辑上的数据库
- 逻辑表：一个包含了所有表的逻辑上的表
- 数据主机：数据库软件安装到哪个服务器上
- 数据节点：数据库软件中的 database
- 分片规则:：默认每个表中数据都一样的
- 读主机：哪个数据库做为读操作
- 写主机：哪个数据做为写操作