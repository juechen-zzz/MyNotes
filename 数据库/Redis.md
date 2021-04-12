[TOC]

# Redis笔记

## 一、Nosql

### 1.1 概述

1. 单机MySQL的年代
	<img src="../images/image-20210117091528169.png" alt="image-20210117091528169" style="zoom:50%;" />

	* 问题一：数据量如果太大，一个机器放不下
	* 问题二：数据的索引（300万），一个机器内存也放不下
	* 问题三：访问量（读写混合），一个服务器承受不了

2. Memcached（缓存） + MySQL + 垂直拆分（读写分离）

	* 问题：网站80%的情况都在读，每次都要去查询数据库就十分的麻烦！所以希望能减轻数据的压力，可以使用缓存来保证效率！
	* <img src="../images/image-20210117093639309.png" alt="image-20210117093639309" style="zoom:50%;" />
	* 发展过程：优化数据结构和索引-->文件缓存（IO）-->Memcached

3. 分库分表 + 水平拆分 + MySQL集群
	本质：数据库读写  
	MyISAM：表锁，十分影响效率，高并发下会出现严重的锁问题
	转战INNODB：行锁

	<img src="../images/image-20210117094422334.png" alt="image-20210117094422334" style="zoom: 67%;" />

4. 如今
	MySQL等关系型数据库不够用，数据量很多，变化很快

	MySQL有的使用它来存储一些比较大的文件，博客、图片！数据库表很大，效率就低了

	研究一种数据库来专门处理这种数据

### 1.2 什么是NoSQL

Not only SQL （不仅仅是SQL），泛指非关系型数据库，Redis是发展最快的，所以必须学

* 很多的数据类型（用户的个人信息、社交网络、地理位置），这些数据类型的存储不需要一个固定的格式
* 不需要多余的操作就可以横向扩展（类似Map<String, Object>）

### 1.3 特点

* 方便拓展（数据之间没有关系，很好拓展）

* 大数据量高性能（Redis一秒读8万次，读取11万，NoSQL的缓存记录级，是一种细粒度的缓存，性能比较高）

* 数据类型是多样型的！（不需要事先设计数据库，随取随用）

* 传统RDBMS和NoSQL

	```
	传统RDBMS
	- 结构化组织
	- SQL
	- 数据和关系都存在单独的表中
	- 数据定义语言
	- 严格的一致性
	- 基础的事务
	
	NoSQL
	- 不仅仅是数据
	- 没有固定的查询语言
	- 键值对存储，列存储，文档存储，图形数据库
	- 最终一致性
	- CAP定理 和 BASE（异地多活）
	- 高性能，高可用，高可扩
	```

<img src="../images/image-20210117115201258.png" alt="image-20210117115201258" style="zoom:50%;" />

### 1.4 NoSQL四大类型

<img src="../images/image-20210117124042040.png" alt="image-20210117124042040" style="zoom:50%;" />

* **KV键值对**
	* 新浪：**Redis**
	* 美团：Redis + Tair
	* 阿里、百度：Redis + memecache
* **文档型数据库（bson格式 和json一样）**
	* **MongoDB**
		* 一种基于分布式文件存储的数据库，C++编写，主要用来处理大量的文档
		* 介于关系型数据库和非关系型数据库中间的产品
		* 是非关系型数据库中功能最丰富，最像关系型数据库的
	* ConthDB
* **列存储数据库**
	* **HBase**
	* 分布式文件系统
* **图关系数据库**
	* 并非存图形，放的是关系，比如：朋友圈社交网络，广告推荐！ 
	* **Neo4j** , InfoGrid



## 二、Redis

### 2.1 概述

 Remote Dictionary Server（远程字典服务），可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API

* 内存存储、持久化，内存中断电即失去
* 效率高，可用于高速缓存
* 发布订阅系统
* 地图信息分析
* 计时器、计数器（浏览器）
* 特性：
	* **单线程**：Redis基于内存操作，CPU不是性能瓶颈，瓶颈是根据机器的内存和网络带宽，既然可以使用单线程，就用单线程 
	* 多样的数据类型
	* 持久化
	* 集群
	* 事务
* <img src="../images/image-20210118104425940.png" alt="image-20210118104425940" style="zoom:50%;" />

### 2.2 基本指令

```java
// 安装
brew install redis
// 启动
//方式一：使用brew帮助我们启动软件
brew services start redis
//方式二
redis-server /usr/local/etc/redis.conf
// 连接
redis-cli -h 127.0.0.1 -p 6379
// 测试
cd /usr/local/bin
redis-benchmark -h localhost -p 6379 -c 100 -n 100000
// 查看当前库的信息
info replication
```

![image-20210117151537271](../images/image-20210117151537271.png)

<img src="../images/image-20210117152318972.png" alt="image-20210117152318972" style="zoom:50%;" />

* Redis默认有16个数据库，默认使用第0个，使用select切换数据库

	```bash
	# 切换数据库
	127.0.0.1:6379> select 3
	OK
	127.0.0.1:6379[3]> dbsize
	(integer) 0
	# 设置值
	127.0.0.1:6379[3]> set name komorebi
	OK
	127.0.0.1:6379[3]> dbsize
	(integer) 1
	127.0.0.1:6379[3]> get name
	"komorebi"
	```

* 查看所有的key

	```bash
	127.0.0.1:6379[3]> keys *
	1) "name"
	```

* 清空当前库

	```bash
	127.0.0.1:6379[3]> flushdb		# 清空全部，flushall
	OK
	```

* 其他操作

	```bash
	127.0.0.1:6379> keys *
	1) "age"
	2) "name"
	# 查看数据类型
	127.0.0.1:6379> type name
	string
	# 判定是否存在
	127.0.0.1:6379> exists name
	(integer) 1
	# 移除
	127.0.0.1:6379> move name 1
	(integer) 1
	# 设置过期时间，现在是10秒，ttl查看剩余时间
	127.0.0.1:6379> expire name 10
	(integer) 1
	127.0.0.1:6379> ttl name
	(integer) 5
	```

	

## 三、五大数据类型

### 3.1 String

* 使用场景：除了字符串还可以是数字
	* 计数器
	* 统计多单位的数量
	* 粉丝数
	* 对象缓存存储

|   操作   |               指令                |                           含义                            |
| :------: | :-------------------------------: | :-------------------------------------------------------: |
|   set    |         set name komorebi         |                        设置字符串                         |
|   get    |         get name komorebi         |                         取字符串                          |
|  append  |        append name "hello"        |                        字符串拼接                         |
|  strlen  |            strlen name            |                        字符串长度                         |
|   incr   |            incur views            |                           自增                            |
|   decr   |            decr views             |                           自减                            |
|  incrby  |          incrby views 10          |                     自增（指定步长）                      |
|  decrby  |          decrby views 10          |                     自减（指定步长）                      |
| getrange |         getrange name 0 3         |               substring (闭区间，-1指最后)                |
| setrange |        setrange name 1 xx         |        修改字符串(指定开始的索引和替换后的字符串)         |
|  setex   |       setex key 30 "hello"        |              set with expire (设置过期时间)               |
|  setnx   |        setnx name komorebi        |             set if  not exist (不存在再设置)              |
|   mset   |      mset k1 v1 k2 v2 k3 v3       |                        批量设置值                         |
|   mget   |           mget k1 k2 k3           |                        批量获取值                         |
|  msetnx  |                                   |             批量设置，原子性操作（类似事务）              |
| set对象  | set user:1 {name:komorebi, age:3} | mset user:1:name komorebi user:1:age 3<br />user:{id}:key |
|  getset  |        getset db komorebi         |                        先get再set                         |

### 3.2 List

* 所有的list命令都使用l开头

|   操作    |                 指令                  |                       含义                        |
| :-------: | :-----------------------------------: | :-----------------------------------------------: |
|   lpush   |           lpush mylist one            |                向队列左侧中放入值                 |
|  lrange   |          lrange mylist 0 -1           |                  取值（闭区间）                   |
|   rpush   |           rpush mylist four           |                向队列右侧中放入值                 |
|   lpop    |              lpop mylist              |                  从队列左侧移除                   |
|   rpop    |              rpop mylist              |                  从队列右侧移除                   |
|  lindex   |            lindex mylist 0            |                通过下标获取一个值                 |
|   llen    |              llen mylist              |                   得到队列长度                    |
|   lrem    |          lrem mylist 2 three          |            移除指定的值(移除2个three)             |
|   ltrim   |           ltrim mylist 1 2            |                  截取（闭区间）                   |
| rpoplpush |     rpoplpush mylist myotherlist      | 移除mylist最后一个元素，并添加到myotherlist的左侧 |
|   lset    |          lset mylist 0 item           |           更新list（必须首先存在list）            |
|  linsert  | linsert mylist before "world" "other" |           在world前插入other，可改after           |

### 3.3 Set

* set中的值不能重复，无序不重复集合

|    操作     |            指令             |              含义              |
| :---------: | :-------------------------: | :----------------------------: |
|    sadd     |     sadd myset "hello"      |         向set中添加值          |
|  smembers   |       smembers myset        |      查看set中的所有元素       |
|  sismember  |    sismember myset hello    | 查看hello是否为set中的一个元素 |
|    scard    |         scard myset         |       查看set元素的个数        |
| srandmember |      srandmember myset      |    从set中随机取出一个元素     |
|    srem     |      srem myset hello       |         删除指定的元素         |
|    spop     |         spop myset          |          随机删除元素          |
|    smove    | smove myset myset2 komorebi |         移动指定的元素         |
|    sdiff    |     sdiff myset1 myset2     |     差集（1中有，2中没有）     |
|   sinter    |    sinter myset1 myset2     |              交集              |
|   sunion    |    sunion myset1 myset2     |              并集              |

### 3.4 Hash

* Map集合，key-Map，这时候这个value是一个Map集合

|  操作   |                  指令                   |               含义               |
| :-----: | :-------------------------------------: | :------------------------------: |
|  hset   |       hset myhash field1 komorebi       |      设置hash，myhash是key       |
|  hget   |           hget myhash field1            |  取myhash中的value里面的field1   |
|  hmset  | hmset myhash field1 hello field2 hello2 |          设置多个field           |
|  hmget  |       hmget myhash field1 field2        |      取myhash中的多个field       |
| hgetall |             hgetall myhash              |     取出myhash中的所有field      |
|  hdel   |           hdel myhash field1            |       删除myhash中的field1       |
|  hlen   |               hlen myhash               |         查看myhash的长度         |
| hexists |          hexists myhash field1          |    查看myhash中是否存在field1    |
|  hkeys  |              hkeys myhash               |         只获取所有field          |
|  hvals  |              hvals myhash               |         只获取所有value          |
| hincrby |         hincrby myhash field3 5         |           指定步长增长           |
| hsetnx  |         hsetnx myhash field4 5          | set if  not exist (不存在再设置) |

### 3.5 Zset

* 有序集合，在set的基础上增加了一个值

|       操作       |                      指令                      |             含义             |
| :--------------: | :--------------------------------------------: | :--------------------------: |
|       zadd       |               zadd myzset 1 one                |      往set中添加一个值       |
|      zrange      |               zrange myzset 0 -1               |       查看里面的所有值       |
|    zrevrange     |              zrevrange myzset 0 1              |         最大的两个值         |
|  zrangebyscore   |  zrangebyscore myzset -inf +inf (withscores)   |             排序             |
| zrevrangebyscore | zrevrangebyscore myzset +inf -inf (withscores) |         排序（逆序）         |
|       zrem       |                zrem myzset one                 |         移除一个元素         |
|      zcard       |                  zcard myzset                  |         获取元素个数         |
|      zcount      |               zcount myzset 1 2                | 获取指定区间内的元素(闭区间) |



## 四、三种特殊数据类型

### 4.1 geospatial

* 地理位置，有效经度-180 -- +180，有效维度-85 -- +85
* 底层用zset实现，zset的指令也可以用 

|       操作        |                             指令                             |                      含义                      |
| :---------------: | :----------------------------------------------------------: | :--------------------------------------------: |
|      geoadd       |           geoadd china:city 31.23 121.47 shanghai            |       添加地理位置（key-(维度精度名称)）       |
|      geopos       |                  geopos china:city beijing                   |       从key中返回所有给定位置元素的位置        |
|      geodist      |          geodist china:city beijing shanghai （km）          |           返回两个给定位置之间的距离           |
|     georadius     | georadius china:city 116.40 39.90 1100 km （withdist / withcoord） |   以给定的经纬度为中心，找出某一半径内的元素   |
| georadiusbymember |         georadiusbymember china:city beijing 1100 km         | georadiusbymember 的中心点由给定的位置元素决定 |
|      geohash      |                  geohash china:city beijing                  |        返回一个或多个元素的geohash表示         |

### 4.2 hyperloglog

* 基数（不重复的元素）
* hyperloglog 基数统计的算法（一个人访问一个网站多次，也还是算一个人）
* 优点：占用的内存固定，2^64不同的基数的集合，只需要12KB的内存

|  操作   |              指令               |   含义   |
| :-----: | :-----------------------------: | :------: |
|  pfadd  | pfadd mykey a b c d e f g h i j | 添加元素 |
| pfcount |          pfcount mykey          |   计数   |
| pfmerge |   pfmerge mykey3 mykey mykey2   |   融合   |

### 4.3 bitmaps

* 位存储，用来统计用户信息（活跃与否、登录状态、打卡）

|   操作   |      指令       |       含义        |
| :------: | :-------------: | :---------------: |
|  setbit  | setbit sign 3 1 | 添加（索引+状态） |
|  getbit  |  getbit sign 0  |       查看        |
| bitcount |  bitcount sign  |       统计        |



## 五、事务

### 5.1 基础指令

* Redis单条命令是**保持原子性**的，但是事务**不保证原子性**

* 事务本质：一组命令的集合（一次性、顺序性、排他性）

* 步骤：

	```bash
	127.0.0.1:6379> multi
	OK
	127.0.0.1:6379> set k1 v1
	QUEUED
	127.0.0.1:6379> set k2 v2
	QUEUED
	127.0.0.1:6379> set k3 v3
	QUEUED
	127.0.0.1:6379> exec
	1) OK
	2) OK
	3) OK
	```

	* 开启事务（**multi**）
	* 命令入队（）
	* 执行事务（**exec**）

* 放弃事务：**discard**

* 编译型异常（**代码有问题，命令有错**），事务中的所有命令都不执行

* 运行时异常（**1 / 0 这种**），事务队列中存在语法问题，那么执行命令的时候，其他命令是可以正常执行的

### 5.2 实现乐观锁

乐观锁：认为什么时候都不会出问题，所以不会上锁。更新数据的时候判断一下是否有人改动过数据

悲观锁：无论做什么都会加锁

* 乐观锁步骤：

	* 获取version

	* 更新的时候比较version

	* ```bash
		# 正常执行
		127.0.0.1:6379> set money 100
		OK
		127.0.0.1:6379> set out 0
		OK
		127.0.0.1:6379> watch money
		OK
		127.0.0.1:6379> multi
		OK
		127.0.0.1:6379> decrby money 20
		QUEUED
		127.0.0.1:6379> incrby out 20
		QUEUED
		127.0.0.1:6379> exec
		1) (integer) 80
		2) (integer) 20
		```

	* ```bash
		# 多线程修改值，使用watch可以当做Redis的乐观锁
		127.0.0.1:6379> watch money
		OK
		127.0.0.1:6379> multi
		OK
		127.0.0.1:6379> decrby money 10
		QUEUED
		127.0.0.1:6379> incrby out 10
		QUEUED
		127.0.0.1:6379> exec
		(nil)
		# unwatch
		```

	* <img src="../images/image-20210119142450683.png" alt="image-20210119142450683" style="zoom:50%;" />



## 六、Jedis

要使用Java来操作Redis

* Jedis是官方推荐的java连接开发工具，使用Java操作Redis中间件

步骤：

1. 导入依赖

	```xml
	<!--Jedis-->
	<dependency>
	    <groupId>redis.clients</groupId>
	    <artifactId>jedis</artifactId>
	    <version>3.2.0</version>
	</dependency>
	<!--fastjson-->
	<dependency>
	    <groupId>com.alibaba</groupId>
	    <artifactId>fastjson</artifactId>
	    <version>1.2.75</version>
	</dependency>
	```

2. 测试:

	* 连接数据库

		```java
		public class TestPing {
		    public static void main(String[] args) {
		        // 1 new一个Jedis对象
		        Jedis jedis = new Jedis("127.0.0.1", 6379);
		
		        // Jedis的所有命令就是常规指令
		        System.out.println(jedis.ping());
		    }
		}
		```

3. ```java
	public class TestKey {
	    public static void main(String[] args) {
	        Jedis jedis = new Jedis("127.0.0.1", 6379);
	
	        System.out.println("清空数据："+jedis.flushDB());
	        System.out.println("判断某个键是否存在："+jedis.exists("username"));
	        System.out.println("新增<'username','kuangshen'>的键值对："+jedis.set("username", "kuangshen"));
	        System.out.println("新增<'password','password'>的键值对："+jedis.set("password", "password"));
	        System.out.print("系统中所有的键如下：");
	        Set<String> keys = jedis.keys("*");
	        System.out.println(keys);
	        System.out.println("删除键password:"+jedis.del("password"));
	        System.out.println("判断键password是否存在："+jedis.exists("password"));
	        System.out.println("查看键username所存储的值的类型："+jedis.type("username"));
	        System.out.println("随机返回key空间的一个："+jedis.randomKey());
	        System.out.println("重命名key："+jedis.rename("username","name"));
	        System.out.println("取出改后的name："+jedis.get("name"));
	        System.out.println("按索引查询："+jedis.select(0));
	        System.out.println("删除当前选择数据库中的所有key："+jedis.flushDB());
	        System.out.println("返回当前数据库中key的数目："+jedis.dbSize());
	        System.out.println("删除所有数据库中的所有key："+jedis.flushAll());
	    }
	}
	```



## 七、SpringBoot整合

* 在SpringBoot2.x后，jedis被替换为了lettuce
* jedis：采用直连，多个线程操作，是不安全的，如果想要避免不安全，使用jedis pool连接池
* lettuce：采用netty，实例可以在多个线程中共享，不存在线程不安全的情况，可以减少线程数据，更像NIO模式

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(RedisOperations.class)
@EnableConfigurationProperties(RedisProperties.class)
@Import({ LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class })
public class RedisAutoConfiguration {
	// 默认的RedisTemplate，没有过多的设置，对象都是需要序列化
    // 两个泛型都是object，后面需要强制转换<String, Object>
	@Bean
	@ConditionalOnMissingBean(name = "redisTemplate")
	@ConditionalOnSingleCandidate(RedisConnectionFactory.class)
	public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
		RedisTemplate<Object, Object> template = new RedisTemplate<>();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}

	@Bean
	@ConditionalOnMissingBean   // 由于String是最常使用的类型，所以单独提出来一个bean
	@ConditionalOnSingleCandidate(RedisConnectionFactory.class)
	public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
		StringRedisTemplate template = new StringRedisTemplate();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}

}
```

### 7.1 测试

1. 导入依赖

2. 配置连接

3. 测试

	```java
	@SpringBootTest
	class Redis02SpringbootApplicationTests {
	    @Autowired
	    private RedisTemplate redisTemplate;
	
	    @Test
	    void contextLoads() {
	        // redisTemplate
	        // opsForValue  操作字符串，类似String
	        // opsForList   操作List，类似List...
	        // 常用的方法可以直接通过template操作，比如事务
	
	        // 获取连接
	//        RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
	//        connection.flushDb();
	
	        redisTemplate.opsForValue().set("mykey", "komorebi");
	        System.out.println(redisTemplate.opsForValue().get("mykey"));
	    }
	
	    @Test
	    public void test() throws JsonProcessingException {
	        // 真实开发用json传送对象，注意必须序列化(或者实体类实现序列化接口)
	        //public class User implements Serializable
	        User user = new User("komorebi", 18);
	        String jsonUser = new ObjectMapper().writeValueAsString(user);
	        redisTemplate.opsForValue().set("user", jsonUser);
	        System.out.println(redisTemplate.opsForValue().get("user"));
	    }
	}
	```

4. RedisTemplate模板

	```java
	@Configuration
	public class RedisConfig {
	
	  @Bean
	  @SuppressWarnings("all")
	  public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
	      RedisTemplate<String, Object> template = new RedisTemplate<String, Object>();
	      template.setConnectionFactory(factory);
	
	      // Json序列化配置
	      Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
	      ObjectMapper om = new ObjectMapper();
	      om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
	      om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
	      jackson2JsonRedisSerializer.setObjectMapper(om);
	      // String序列化
	      StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
	
	      // key采用String的序列化方式
	      template.setKeySerializer(stringRedisSerializer);
	      // hash的key也采用String的序列化方式
	      template.setHashKeySerializer(stringRedisSerializer);
	      // value序列化方式采用jackson
	      template.setValueSerializer(jackson2JsonRedisSerializer);
	      // hash的value序列化方式采用jackson
	      template.setHashValueSerializer(jackson2JsonRedisSerializer);
	      template.afterPropertiesSet();
	
	      return template;
	  }
	}
	```

	

## 八、Redis.conf详解

### 8.1 单位

* unit单位对大小写不敏感

![image-20210120090625792](../images/image-20210120090625792.png)

### 8.2 包含（INCLUDES）

* 可以把多个配置文件配置过来

![image-20210120090712460](../images/image-20210120090712460.png)

### 8.3 网络（NETWORK）

```bash
bind 127.0.0.1		# 绑定的IP
protected-mode yes  # 保护
port 6379			# 端口设置
```

### 8.4 通用（GENERAL）

```bash
daemonize yes						# 以守护进程的方式运行
pidfile /var/run/redis_6379.pid		# 如果以后台的方式运行，就需要指定一个pid文件

# 日志
# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
loglevel notice
logfile ""  				# 日志的输出文件位置名
databases 16				# 数据库的数量
always-show-logo yes		# 是否显示logo

```

### 8.5 快照（SNAPSHOTTING）

持久化，在规定的时间内执行了多少次操作，则会持久化到文件（.rdb .aof）

Redis是内存数据库，如果没有持久化，那么数据断电即失

```bash
#   In the example below the behavior will be to save:
#   after 900 sec (15 min) if at least 1 key changed
#   after 300 sec (5 min) if at least 10 keys changed
#   after 60 sec if at least 10000 keys changed
save 900 1
save 300 10
save 60 10000
# 之后学习持久化，会自定义
stop-writes-on-bgsave-error yes		# 持久化如果出错，是否还需要继续工作
rdbcompression yes					# 是否压缩rdb文件，需要消耗一些CPU资源
rdbchecksum yes						# 保存rdb文件的时候，进行错误的检查校验
dir /usr/local/var/db/redis/		# rdb文件保存的目录
```

### 8.6 复制（REPLICATION）

### 8.7 安全（SECURITY）

```bash
config get requirepass
config set requirepass "XXXXXX"
auth XXXXXX 			# 登录
```

* 设置密码

![image-20210120092950018](../images/image-20210120092950018.png)

### 8.8 限制（CLIENTS / MEMORY MANAGEMENT）

```bash
maxclients 10000				# 设置能连接上Redis的最大客户端的数量
maxmemory <bytes>				# Redis配置最大的内存容量
maxmemory-policy noeviction		# 内存达到上限之后的处理方式
```

### 8.9 APPEND ONLY MODE（AOF）

```bash
appendonly no 					# 默认不开始，默认使用rdb方式持久化，在大部分情况下，rdb完全够用
appendfilename "appendonly.aof"	# 持久化文件的名字
appendfsync everysec			# 每秒执行一次同步，可能会丢失这1s的数据
```



## 九、Redis持久化

Redis是内存数据库，如果不将内存中的数据保存到磁盘，那么一旦服务器进程退出，服务器中的数据库状态也会消失，所以Redis提供了持久化功能

### 9.1 RDB（Redis DataBase）

<img src="../images/image-20210120095641281.png" alt="image-20210120095641281" style="zoom:50%;" />

* 在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是snapshot快照，它恢复时是将快照文件直接读到内存里
* Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写到一个临时文件中，待持久化过程快结束了，再用这个临时文件替换上次持久化好的文件。
* **优点**：
	* 整个过程中，主进程是不进行任何IO操作的，确保了高性能。
	* 如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那rdb方式要比aof方式更加的高效。
* **缺点**：
	* 需要一定的时间间隔进行操作
	* fork进程的时候，会占用一定的内存空间
	* rdb最后一次持久化后的数据可能丢失
* 触发机制（生成dump.rdb）：
	* 1 save的规则满足的情况下，会自动触发rdb规则
	* 2 执行flushall命令
	* 3 退出Redis，也会产生rdb文件

### 9.2 AOF（Append Only File）

将我们的所有命令都记录下来，恢复的时候就把这个文件全部再执行一遍

<img src="../images/image-20210120103540938.png" alt="image-20210120103540938" style="zoom:50%;" />

* 以日志的形式记录每个**写**操作，将Redis执行过的所有指令记录下来，只许追加文件，但不可以改写文件

* Redis启动之初会读取该文件重新构建数据，换言之，Redis重启就会根据日志文件的内容从前到后执行一次来恢复

* 如果保存的aof文件有错误，那此时Redis是启动不起来的，需要修复

	```bash
	redis-check-aof --fix appendonly.aof
	```

* **优点**：

	* 每一次修改都同步，文件的完整性会更好
	* 每秒同步一次，可能会丢失一秒的数据
	* 从不同步，效率最高

* **缺点**：

	* 相对于数据文件来说，aof远远大于rdb，修复的速度也比rdb慢
	* aof运行效率也比rdb慢

![image-20210120105754594](../images/image-20210120105754594.png)



## 十、Redis订阅发布

<img src="../images/image-20210120140920897.png" alt="image-20210120140920897" style="zoom:50%;" />

<img src="../images/image-20210120141233273.png" alt="image-20210120141233273" style="zoom:50%;" />



## 十一、Redis主从复制

### 11.1 概念

* 主从复制：指将一台Redis服务器的数据，复制到其他的Redis服务器。前者称为主节点（master / leader），后者称为从节点（slave / follower）；**数据的复制是单向的，只能从主节点到从节点**。Master以写为主，Slave以读为主。
* 默认情况下，**每台Redis服务器都是主节点**，且一个主节点可以有多个从节点，但一个从节点只能有一个主节点
* **作用**：
	* 数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式
	* 故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复，实际上是一种服务的冗余
	* 负载均衡：在主从复制的基础上，配合读写分离，可以由主节点提供写服务，从节点提供读服务，分担服务器负载；尤其是在写少读多的场景下，通过多个从节点分担读负载，可以大大提高Redis服务器的并发量
	* 高可用（集群）基石：除了上述作用以外，主从复制还是哨兵和集群能够实施的基础
* **主机断开连接**：从机依旧连接到主机，但是没有写操作，如果主机恢复了，还可以继续写
* **从机断开连接**：如果不是用配置文件配置的，那从机就会变回主机，但只要变回从机，立刻还可以获取数据

### 11.2 实现流程

1.第一步，从服务器保存主服务器的配置信息，保存之后待从服务器内部的定时器执行时，就会触发复制的流程。

2.第二步，从服务器首先会与主服务器建立一个socket套字节连接，用作主从通信使用。后面主服务器发送数据给从服务器也是通过该套字节进行。

3.第三步，socket套字节连接成功之后，接着发送鉴权ping命令，正常的情况下，主服务器会发送对应的响应。ping命令的作用是为了，保证socket套字节是否可以用，同时也是为了验证主服务器是否接受操作命令。

4.第四步，接着就是鉴权验证，判断从节点配置的主节点连接密码是否正确。

5.第五步，鉴权成功之后，就可以开始复制数据了。主服务器此时会进行全量复制，将主服务的数据全部发给从服务器，从服务器保存主服务器发送的数据。

6.接下来就是持续复制操作。主服务器会进行异步复制，一边将写的数据写入自身，同时会将新的写命令发送给从服务器。

### 11.3 实现策略

**1. 全量复制。**全量复制用在主从复制刚建立时或者从切主服务器时，从服务器没有主服务器的数据，主服务器会将自身的数据通过rdb文件方式发送给从服务器，从服务器会清空自身数据，接着将主服务器发送的数据加载到自身中。

**2. 部分复制。**部分复制用在一些异常情况下，例如主从延迟、从服务宕机之后重新启动接收主服务器发送的部分数据。

部分复制的实现主要依赖于复制缓存区、主服务的runid、主从服务器各自的复制偏移量(offset)。

复制缓存区：主服务在接收写命令时，会将命令写入缓存区，以便从服务器在异常情况下，减少数据的丢失。当从服务器正常连接之后，主服务器会将缓存区内的数据发送给从服务器。这里的缓存区是一个长队列。

主服务器runid：主服务器会在每次服务启动之后，会生成一个唯一的ID，作为自身标识。从服务器会将该标识保存起来，发送部分复制命令时，会使用该runid。

```
psync runid offset
```

主从复制各自偏移量：主从服务在建立复制之后，都会有自身的偏移量。从节点会每秒钟发送自身复制的偏移量给从节点，主节点在发送写命令之后，从节点也会增加自身的复制偏移量。主节点在每次进行了写命令之后，也会增加自身的偏移量。这里的偏移量是通过命令的字节长度累加计算。

**3. 异步复制。**异步复制是针对主从建立复制关系之后，主从服务器持续保持复制关系。



## 十二、哨兵模式

* 主从切换技术的方法是：当主机宕机后，需要手动把一台服务器切换为主服务器，需要人工干预，不是一种推荐的方式。更多时候，我们优先考虑哨兵模式，Redis从2.8开始提供Sentinel架构来解决问题
* 哨兵模式是一种特殊的模式，首先Redis提供了哨兵的命令，哨兵是一个独立的进程，会自动运行。
* 原理：哨兵通过发送命令，等待Redis服务器响应，从而监控运行的多个Redis实例
	<img src="../images/image-20210120184831062.png" alt="image-20210120184831062" style="zoom:50%;" />
* 作用：
	* 通过发送命令，让Redis服务器返回运行状态，包括主服务器和从服务器
	* 当哨兵监测到master宕机，会自动将slave切换成master，然后通过**发布订阅模式**通知其他的服务器，修改配置文件，让它们切换主机
	* <img src="../images/image-20210120185128123.png" alt="image-20210120185128123" style="zoom:50%;" />
	* <img src="../images/image-20210120185252556.png" alt="image-20210120185252556" style="zoom:50%;" />
* 优点：
	* 哨兵集群，基于主从复制模式，所有的主从配置模型优点都有
	* 主从可以切换，故障可以转移，系统的可用性更好
	* 哨兵模式就是主从模式的升级，手动到自动
* 缺点：
	* Redis不好在线扩容，集群容量一旦到达上限，在线扩容就十分麻烦
	* 实现哨兵模式的配置很复杂



## 十三、Redis缓存穿透和雪崩

### 13.1 缓存穿透

概念：用户想要查询一个数据，发现Redis内存数据库中没有，也就是缓存没有，于是向持久层数据库查询，发现也没有，于是本次查询失效。当用户很多的时候，缓存都没有命中（秒杀！），于是都去请求了持久层数据库，这会给持久层数据库造成很大的压力，这就相当于出现了缓存穿透

解决方案：

* <img src="../images/image-20210120193249534.png" alt="image-20210120193249534" style="zoom:50%;" />
* <img src="../images/image-20210120193315206.png" alt="image-20210120193315206" style="zoom:50%;" />
	* 问题
		* 如果空值能够被缓存起来，那就意味着缓存需要更多的空间存储更多的键，因为这当中可能会有很多的空值的键
		* 即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口的不一致，只对于需要保持一致性的业务会有影响



### 13.2 缓存击穿

* 和缓存穿透的区别，缓存击穿，是指一个Key非常热点，在不停的扛着大并发，大并发集中对这一个点进行访问，当这个Key在失效的瞬间，持续的大并发就击破缓存，直接请求数据库，就像在一个屏障上凿开了一个洞
* 当某个Key在过期的瞬间，有大量的请求并发访问，这类数据一般是热点数据，由于缓存过期，会同时访问数据库来查询最新数据，并且回写缓存，会导致数据库瞬间压力过大
* 解决方案：
	* <img src="../images/image-20210120194051043.png" alt="image-20210120194051043" style="zoom:50%;" />



### 13.3 缓存雪崩

概念：缓存雪崩，指在某一个时间段，缓存集中过期失效

产生雪崩的原因之一：比如在写文本的时候，马上双十二零点，很快就会迎来一波抢购，这波商品时间比较集中的放入了缓存，假设缓存一个小时，那么到凌晨一点的时候，这批商品的缓存就过期了。而对这批商品的访问查询，都落到了数据库上，对于数据库而言，就会产生周期性的压力波峰。于是所有的请求都会达到存储层，存储层的调用量会暴增，造成存储层也挂掉的现象

<img src="../images/image-20210120194728056.png" alt="image-20210120194728056" style="zoom:50%;" />