- [Redis简介](#redis简介)
- [数据类型](#数据类型)
- [持久化策略](#持久化策略)
- [集群](#集群)
- [使用](#使用)
- [安装](#安装)



## Redis简介

Redis是一个基于key-value形式进行存储的内存型数据库。效率高，理论值：每秒10K数据读取。

**Reids是一个NoSql数据库**

- 字面意思：不使用SQL命令操作数据库软件
- NoSQL：英文全称Not Only SQL ，表示在应用程序开发时，不是必须使用关系型数据库，可以使用 NoSQl 替代关系型数据库的**部分**功能
- 目前 NoSQL 不能完全替代关系型数据库，使用关系型数据库结合NoSQl数据库进行完成项目
- 当数据比较复杂时不适用于 NoSQL 数据库，关系型数据库依然做为数据存储的主要软件

NoSQL 数据库当作缓存工具来使用：

- 把某些**使用频率较高**的内容不仅仅存储到关系型数据库中还存储到 NoSQL 数据中
- 还需要考虑到: NoSQL 和关系型数据库数据同步的问题

**Windows端连接工具：RedisDesktopManager**



## 数据类型

[命令手册](http://doc.redisfans.com/)

**String**

String数据结构是简单的key-value类型，value其实不仅可以是String，也可以是数字。 常规key-value缓存应用；常规计数：微博数，粉丝数等。

**Hash**

hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象，后续操作的时候，你可以直接仅仅修改这个对象中的某个字段的值。 比如我们可以 hash 数据结构来存储用户信息，商品信息等等。

**List**

list 就是链表，Redis list 的应用场景非常多，也是Redis最重要的数据结构之一，比如微博的关注列表，粉丝列表，消息列表等功能都可以用Redis的 list 结构来实现。

**Set**

set 对外提供的功能与list类似是一个列表的功能，特殊之处在于 set 是可以自动排重的。

**Sorted Set**

和set相比，sorted set增加了一个权重参数score，使得集合中的元素能够按score进行有序排列。



## 持久化策略

**RDB**

默认的持久化策略，每隔一定时间后把内存中数据持久化到dump.rdb文件中。

缺点：

​	数据过于集中，可能导致最后的数据没有持久化到 dump.rdb 中

​	解决办法：使用命令 SAVE 或BGSAVE 手动持久化

**AOF**

监听 Redis 的日志文件，监听如果发现执行了修改、删除,、新增命令，立即根据这条命令把数据持久化。

缺点：效率降低



## 集群

**集群的概念**

多个业务单元协同工作组成的整体称为集群，每个业务单元都是相同的。

当集群中业务单元中超过或等于 1/2 个 down 掉时整个集群不可用，建议使用奇数个,整体 down 机率小

**一主一备模式**

给每个业务单元创建一个备份业务单元，原来的业务单元(master)后产生的叫做(slave)

**集群和伪集群**

集群: 每个业务单元都安装到单独的服务器上

伪集群: 所有业务单元都安装到同一个服务器上，通过端口区分不同的业务单元



## 使用

**Jedis**是Redis 客户端工具 jar

**Jedis接口**

```java
public interface JedisDao {
	/**
	 * 判断key是否存在
	 * @param key
	 * @return
	 */
	Boolean exists(String key);
	
	/**
	 * 删除
	 * @param key
	 * @return
	 */
	Long del(String key);
	
	/**
	 * 设置值
	 * @param key
	 * @param value
	 * @return
	 */
	String set(String key, String value);
	
	/**
	 * 获取值
	 * @param key
	 * @return
	 */
	String get(String key);
	
	/**
	 * 设置key的过期时间
	 * @param key
	 * @param seconds
	 * @return
	 */
	Long expire(String key,int seconds);
}
```

**Jedis接口实现类**

```java
@Repository
public class JedisDaoImpl implements JedisDao{
	@Resource
	private JedisCluster jedisClient;
	
	@Override
	public Boolean exists(String key) {
		return jedisClient.exists(key);
	}

	@Override
	public Long del(String key) {
		return jedisClient.del(key);
	}

	@Override
	public String set(String key, String value) {
		return jedisClient.set(key, value);
	}

	@Override
	public String get(String key) {
		return jedisClient.get(key);
	}

	@Override
	public Long expire(String key, int seconds) {
		return jedisClient.expire(key,seconds);
	}
}
```

**applicationContext-jedis.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">
	<!-- 注解扫描 -->
	<context:component-scan base-package="com.ego.redis.dao.impl"></context:component-scan>
	<!-- 配置jedis连接池 -->
	<bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">
		<!-- 最大连接数 -->
		<property name="maxTotal" value="30" />
		<!-- 最大空闲连接数 -->
		<property name="maxIdle" value="10" />
		<!-- 每次释放连接的最大数目 -->
		<property name="numTestsPerEvictionRun" value="1024" />
		<!-- 释放连接的扫描间隔（毫秒） -->
		<property name="timeBetweenEvictionRunsMillis" value="30000" />
		<!-- 连接最小空闲时间 -->
		<property name="minEvictableIdleTimeMillis" value="1800000" />
		<!-- 连接空闲多久后释放, 当空闲时间>该值 且 空闲连接>最大空闲连接数 时直接释放 -->
		<property name="softMinEvictableIdleTimeMillis" value="10000" />
		<!-- 获取连接时的最大等待毫秒数,小于零:阻塞不确定的时间,默认-1 -->
		<property name="maxWaitMillis" value="1500" />
		<!-- 在获取连接的时候检查有效性, 默认false -->
		<property name="testOnBorrow" value="true" />
		<!-- 在空闲时检查有效性, 默认false -->
		<property name="testWhileIdle" value="true" />
		<!-- 连接耗尽时是否阻塞, false报异常,ture阻塞直到超时, 默认true -->
		<property name="blockWhenExhausted" value="false" />
	</bean>
			
	<!-- jedisCluster -->
	<bean id="jedisClients" class="redis.clients.jedis.JedisCluster">
		<constructor-arg name="poolConfig" ref="jedisPoolConfig"/>
		<constructor-arg name="nodes">
			<set>
				<bean class="redis.clients.jedis.HostAndPort">
					<constructor-arg name="host" value="192.168.182.129"/>
					<constructor-arg name="port" value="7001"/>
				</bean>
				<bean class="redis.clients.jedis.HostAndPort">
					<constructor-arg name="host" value="192.168.182.129"/>
					<constructor-arg name="port" value="7002"/>
				</bean>
				<bean class="redis.clients.jedis.HostAndPort">
					<constructor-arg name="host" value="192.168.182.129"/>
					<constructor-arg name="port" value="7003"/>
				</bean>
				<bean class="redis.clients.jedis.HostAndPort">
					<constructor-arg name="host" value="192.168.182.129"/>
					<constructor-arg name="port" value="7004"/>
				</bean>
				<bean class="redis.clients.jedis.HostAndPort">
					<constructor-arg name="host" value="192.168.182.129"/>
					<constructor-arg name="port" value="7005"/>
				</bean>
				<bean class="redis.clients.jedis.HostAndPort">
					<constructor-arg name="host" value="192.168.182.129"/>
					<constructor-arg name="port" value="7006"/>
				</bean>
			</set>
		</constructor-arg>
	</bean>
</beans>
```

**在其他类中使用**

````java
@Service
public class TbContentServiceImpl implements TbContentService{
	@Reference
	private TbContentDubboService tbContentDubboServiceImpl;
	@Resource
	private JedisDao jedisDaoImpl;
	@Value("${redis.bigpic.key}")
	private String key;
	
	@Override
	public String showBigPic() {
		//先判断redis中是否存在,存在就从redis里取
		if(jedisDaoImpl.exists(key)) {
			String value = jedisDaoImpl.get(key);
			if(value!=null&&!value.equals("")) {
				return value;
			}
		}
		
		//如果redis不存在就从mysql中取，并放入redis中
		//取最近前6个大广告
		List<TbContent> list = tbContentDubboServiceImpl.selByConut(6, true);
		List<Map<String,Object>> listResult = new ArrayList<>();
		for (TbContent content : list) {
			Map<String,Object> map = new HashMap<>();
			map.put("srcB", content.getPic2());
			map.put("height", 240);
			map.put("alt", "图片加载失败");
			map.put("width", 670);
			map.put("src", content.getPic());
			map.put("widthB", 550);
			map.put("href", content.getUrl());
			map.put("heightB", 240);
			
			listResult.add(map);
		}
		//把数据转换成json格式
		String listJson = JsonUtils.objectToJson(listResult);
		//把数据放入到redis中
		jedisDaoImpl.set(key, listJson);	
        
		return listJson;
	}
}
````



## 安装

[Redis安装](manual/redis安装.md)

