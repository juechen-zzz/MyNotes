# Mybatis

## 1 简介

* MyBatis 是一款优秀的**持久层**框架：
	* 持久层：完成持久化工作的代码块
	* 持久化就是将程序的数据在持久状态和瞬时状态转化的过程
	* 内存：**断电即失**
	* 数据库(JDBC)：io文件持久化
	* 生活：冷藏
* 它支持自定义 SQL、存储过程以及高级映射。
* MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
* MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。
* 优点
	* 简单易学
	* 灵活
	* sql和代码的分离，提高了可维护性
	* 提供映射标签，支持对象与数据库的orm字段关系映射
	* 提供对象关系映射标签，支持对象关系组建维护
	* 提供xml标签，支持编写动态sql

## 2 环境配置

<img src="../images/image-20201113082857386.png" alt="image-20201113082857386" style="zoom: 50%;" />

### 2.1 搭建环境

* 1 构造数据库

	```sql
	CREATE TABLE `user`(
		`id` INT(20) NOT NULL,
		`name` VARCHAR(30) DEFAULT NULL,
		`pwd` VARCHAR(30) DEFAULT NULL,
		PRIMARY KEY(`id`)
	)ENGINE=INNODB DEFAULT CHARACTER SET=utf8;
	
	INSERT INTO `user`(`id`, `name`, `pwd`) VALUES
	(1, 'komorebi', '123456'),
	(2, 'zhang_three', '123456'),
	(3, 'li_four', '123456')
	```

* 2 新建项目

	* 新建一个普通Maven项目
	* 删除src目录
	* 导入依赖（**Mysql Mybatis Junit**）

* 3 创建一个模块，空的Maven模块即可

* 4 编写Mybatis的核心配置文件(在子模块resources目录下)

	```xml
	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE configuration
	        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
	    <environments default="development">
	        <environment id="development">
	            <transactionManager type="JDBC"/>
	            <dataSource type="POOLED">
	                <property name="driver" value="com.mysql.jdbc.Driver"/>
	                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
	                <property name="username" value="root"/>
	                <property name="password" value="123456"/>
	            </dataSource>
	        </environment>
	    </environments>
	    <mappers>
	        <mapper resource="com/komorebi/dao/UserMapper.xml"/>
	    </mappers>
	</configuration>
	```

* 5 编写MybatisUtils工具类

	```java
	// sqlSessionFactory --> sqlSession
	public class MybatisUtils {
	    private static SqlSessionFactory sqlSessionFactory;
	    static {
	        try {
	            // 获取sqlSessionFactory对象
	            String resource = "mybatis-config.xml";
	            InputStream inputStream = Resources.getResourceAsStream(resource);
	            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	        } catch (IOException e) {
	            e.printStackTrace();
	        }
	    }
	
	    // 既然有了SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例
	    // SqlSession 提供了在数据库执行 SQL 命令所需的所有方法
	    public static SqlSession getSqlSession(){
	        return sqlSessionFactory.openSession();
	    }
	}
	```

### 2.2 编写代码

* 实体类

	```java
	package com.komorebi.pojo;
	
	// 实体类
	public class User {
	    private int id;
	    private String name;
	    private String pwd;
	
	    public User() {}
	
	    public User(int id, String name, String pwd) {
	        this.id = id;
	        this.name = name;
	        this.pwd = pwd;
	    }
	
	    public int getId() {return id;}
	    public void setId(int id) {this.id = id;}
	    public String getName() {return name;}
	    public void setName(String name) {this.name = name;}
	    public String getPwd() {return pwd;}
	    public void setPwd(String pwd) {this.pwd = pwd;}
	
	    @Override
	    public String toString() {
	        return "User{" +
	                "id=" + id +
	                ", name='" + name + '\'' +
	                ", pwd='" + pwd + '\'' +
	                '}';
	    }
	}
	
	```

* Dao接口

	```java
	public interface UserDao {
	    List<User> getUserList();
	}
	```

* 接口实现类（由原来的Impl转换为一个Mapper配置文件）

	```xml
	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	<!--命名空间 绑定一个对应的Mapper接口-->
	<mapper namespace="com.komorebi.dao.UserDao">
	    <!--查询语句-->
	    <select id="getUserList" resultType="com.komorebi.pojo.User">
	        secect * from mybatis.user
	    </select>
	</mapper>
	```

### 2.3 测试

* org.apache.ibatis.binding.BindingException: Type interface com.komorebi.dao.UserDao is not known to the MapperRegistry.

	* **解决方案**：每一个Mapper.xml都需要在Mybatis核心配置文件中注册

* Caused by: java.io.IOException: Could not find resource com/komorebi/dao/UserMapper.xml

	* **解决方案**：pom.xml中加入build中配置resources,来防止我们资源导出失败的问题，可能只需要加java目录

* ```Java
	public class UserDaoTest {
	    @Test
	    public void test(){
	        // 1 获得SqlSession对象
	        SqlSession sqlSession = MybatisUtils.getSqlSession();
	
	        // 2.1 执行
	        UserDao userDao = sqlSession.getMapper(UserDao.class);
	        List<User> userList = userDao.getUserList();
	
	        for (User user : userList) {
	            System.out.println(user);
	        }
	
	        // 3 关闭SqlSession
	        sqlSession.close();
	    }
	}
	```



## 3 CRUD

### 3.1 namespace

* namespace中的包名要和mapper接口的包名一致

* id：对应的namespace中的方法名
* resultType：SQL语句执行的返回值
* parameterType：参数类型

### 3.2 CRUD操作

* 只需要修改**接口**处和接口实现的**xml**
* **增删改需要提交事务**

```java
public interface UserMapper {
    // 查询全部用户
    List<User> getUserList();

    // 根据ID查询用户
    User getUserById(int id);

    // insert一个用户
    int addUser(User user);

    // 修改用户
    int updateUser(User user);

    // 删除一个用户
    int deleteUser(int id);
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--命名空间 绑定一个对应的Mapper接口-->
<mapper namespace="com.komorebi.dao.UserMapper">
    <!--查询语句-->
    <select id="getUserList" resultType="com.komorebi.pojo.User">
        select * from mybatis.user;
    </select>

    <!--按指定ID查询语句-->
    <select id="getUserById" parameterType="int" resultType="com.komorebi.pojo.User">
        select * from mybatis.user where id = #{id};
    </select>

    <!--插入一个用户，对象中的属性，可以直接取出-->
    <insert id="addUser" parameterType="com.komorebi.pojo.User">
        insert into mybatis.user(id, name, pwd) values (#{id}, #{name}, #{pwd});
    </insert>

    <!--修改一个用户-->
    <update id="updateUser" parameterType="com.komorebi.pojo.User">
        update mybatis.user set name = #{name}, pwd = #{pwd}  where id = #{id};
    </update>

    <!--删除一个用户-->
    <delete id="deleteUser" parameterType="int">
        delete from mybatis.user where id = #{id};
    </delete>
</mapper>
```

### 3.3 万能map

* 假设实体类或者数据库中**参数太多**，考虑使用Map

```java 
// 用map作为输入
int addUser2(Map<String, Object> map);
```

```xml
<!--万能Map-->
<insert id="addUser2" parameterType="map">
    insert into mybatis.user(id, name, pwd) values (#{userID}, #{userName}, #{userPassword})
</insert>
```

```java
@Test
public void testAddUser2(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    Map<String, Object> map = new HashMap<String, Object>();
    map.put("userID", 4);
    map.put("userName", "wang_five");
    map.put("userPassword", "123123");

    mapper.addUser2(map);

    sqlSession.commit();
    sqlSession.close();
}
```

* **Map传递参数**，直接在sql中取出key即可   parameterType="map"
* **对象传递参数**，直接在sql中取出对象的属性即可    parameterType="com.komorebi.pojo.User"
* 只有一个**基本类型参数**的情况下，可以直接在sql中取到‘
* 多个参数情况下，可以用Map，也可以用**注解**

### 3.4 模糊查询

* Java代码执行的时候，传递通配符%%
* 在SQL拼接中使用通配符

```Java
// 模糊查询
List<User> getUserLike(String value);
```

```xml
<!--模糊查询-->
<select id="getUserLike" resultType="com.komorebi.pojo.User">
    select * from mybatis.user where name like #{value};
</select>
```

```java
@Test
public void testGetUserLike(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    List<User> userList = mapper.getUserLike("%li%");	// 传递通配符%%
    for (User user : userList) {
        System.out.println(user);
    }
    sqlSession.close();
}
```



## 4 配置解析

### 4.1 核心配置文件

<img src="../images/image-20201114102204102.png" alt="image-20201114102204102" style="zoom:50%;" />

* mybatis-config.xml

### 4.2 环境配置（environments）

* MyBatis 可以配置成适应多种环境
* **尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**
* 默认的事务管理器：**JDBC**，连接池：**POOLED**

### 4.3 属性（properties）

* 可以通过properties属性引用配置文件

* 这些属性可以在外部进行配置，并可以进行动态替换。你既可以在典型的 Java 属性文件中配置这些属性，也可以在 properties 元素的子元素中设置

* 1 编写一个配置文件db.properties

	```properties
	driver=com.mysql.jdbc.Driver
	url=jdbc:mysql://localhost:3306/mybatis?useSSL=true&useUnicode=true&characterEncoding=UTF-8
	username=root
	password=123456
	```

* 2 可以直接引入外部配置文件，如果有字段缺失，可以在这里补；两处重复字段，优先外部配置文件

	```xml
	<properties resource="db.properties"/>
	```

### 4.4 类型别名（typeAliases）

* 类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在降低冗余的全限定类名书写

* 可以固定指定；也可以扫描实体类的包，默认别名为这个类的类名，首字母小写

* 第一种可以DIY别名，第二种不行，如要改，需要在实体类上加注解

	```xml
	<!--给实体类起别名-->
	<typeAliases>
	    <typeAlias type="com.komorebi.pojo.User" alias="User"/>
	    <package name="com.komorebi.pojo"/>
	</typeAliases>
	```

	![image-20201114110911996](../images/image-20201114110911996.png)

### 4.5 设置（settings）

![image-20201114111624506](../images/image-20201114111624506.png)

### 4.6 其他

<img src="../images/image-20201114111933539.png" alt="image-20201114111933539" style="zoom:50%;" />

### 4.7 Mapper

MapperRegistry：注册绑定我们的Mapper文件

方式一：

```xml
<mappers>
	<mapper resource="com/komorebi/dao/UserMapper.xml"/>
</mappers>
```

方式二：使用class文件绑定注册（不推荐）

```xml
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
    <mapper class="org.mybatis.builder.AuthorMapper"/>
    <mapper class="org.mybatis.builder.BlogMapper"/>
    <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
```

* 注意：接口和Mapper配置文件**必须同名**，必须在用一个包下

方式三：使用扫描包进行注入绑定（不推荐）

```xml
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
    <package name="org.mybatis.builder"/>
</mappers>
```

* 注意：接口和Mapper配置文件**必须同名**，必须在用一个包下

### 4.8 生命周期和作用域

<img src="../images/image-20201114143004071.png" alt="image-20201114143004071" style="zoom:50%;" />

* 错误的使用会导致非常严重的**并发问题**
*  **SqlSessionFactoryBuilder**
	* 一旦创建了 SqlSessionFactory，就不再需要它了
	* 局部变量
* **SqlSessionFactory**
	* 可以想象为数据库连接池
	* SqlSessionFactory 一旦被创建就应该在应用的运行期间**一直存在**，**没有任何理由丢弃它**或重新创建另一个实例
	* **最佳作用域**：应用作用域
	* 最简单的就是使用单例模式或者静态单例模式
* **SqlSession**
	* 连接到连接池的一个请求
	* **最佳作用域**：方法作用域
	* 用完需要关闭，否则资源被占用

<img src="../images/image-20201114143554104.png" alt="image-20201114143554104" style="zoom:50%;" />

## 5 ResultMap

* 问题：实体类内的属性名和数据库字段名不一致<img src="../images/image-20201114155423409.png" alt="image-20201114155423409"  />

	* ```sql
		select  * from mybatis.user where id = #{id}
		// 类型处理器
		select id, name, pwd from mybatis.user where id = #{id}
		```

* 解决方案：

	* 1 起别名

		```sql
		select id, name, pwd as password from mybatis.user where id = #{id}
		```

	* 2 ResultMap

		* 结果集映射

			```xml
			<!--按指定ID查询语句-->
			<resultMap id="UserMap" type="User">
			    <!--column指数据库中的字段，property指实体类中的属性-->
			    <result column="id" property="id" />
			    <result column="name" property="name" />
			    <result column="pwd" property="password" />
			</resultMap>
			<select id="getUserById" parameterType="int" resultMap="UserMap">
			    select * from mybatis.user where id = #{id};
			</select>
			```

		* `resultMap` 元素是 MyBatis 中最重要最强大的元素

		* ResultMap 的**设计思想**：对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了。

