## SSM学习之Mybatis：封装的JDBC

官方中文文档：https://mybatis.org/mybatis-3/zh/index.html

![MyBatis logo](https://mybatis.org/images/mybatis-logo.png)

### 一、初始Mybatis



#### 1、什么是Mybatis？

##### ① 相关概念

- MyBatis 是一款优秀的**持久层**框架，它支持自定义 SQL、存储过程以及高级映射。`简单易学，容易上手`

- MyBatis **免除了几乎所有的 JDBC 代码**以及设置参数和获取结果集的工作。`sql与代码分离，提高可维护性`

- MyBatis 可以**通过简单的 XML 或注解来配置和映射**原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。`提供xml标签，支持编写动态sql`



##### ② 持久层

持久层，可以理解成数据保存在数据库或者硬盘一类可以保存很长时间的设备里面，不像放在内存中那样断电就消失了，也就是把数据存在持久化设备上 。



#### 2、Mybatis安装

##### 通过Maven仓库

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>
```



### 二、快速搭建第一个Mybatis程序

**框架建立流程**：搭建环境` 》》》`导入Mybatis`》》》`编写代码`》》》`测试

####  1、准备工作

##### ① 建立一个数据库

```sql
CREATE DATABASE `mybatis`;
USE `mybatis`;
CREATE TABLE `user`(
`id` INT(20) NOT NULL PRIMARY KEY,
`name` VARCHAR(20) DEFAULT NULL,
`pwd` VARCHAR(20) DEFAULT NULL
)ENGINE=INNODB DEFAULT CHARSET=utf8;

INSERT INTO `user`(`id`,`name`,`pwd`)VALUES
(1,'蝙蝠侠','101'),
(2,'钢铁侠','102'),
(3,'键盘侠','103')
```

##### ② 新建一个普通的maven项目

即不勾选任何选项的maven项目，然后删除src文件作为一个父亲项目

##### ③ 导入架包，配置环境

```c
<!-- 导入依赖 -->
    <dependencies>
<!--mysql驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.46</version>
        </dependency>
<!--mybatis-->
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.6</version>
        </dependency>
        <!--junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
        </dependency>
    </dependencies>
            
<!-- 配置打包功能，将非resources的文件也能被搜索保存 -->            
<build>
        <resources>

            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>

            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>

        </resources>
</build>
```



##### ④ 创建新的一个子模块

好处：不用重复导入架包

- 编写mybatis的核心配置文件：在resources中建立下xml文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--configuration核心配置文件-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?userSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

- 编写mybatis工具栏

```java
package utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

public class MybatisUtils {
    private static SqlSessionFactory sqlSessionFactory;
    
    static {
        try {
            //获取sqlSessionFactory的对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }//静态代码块，初始即加载
    
    

    //获取SqlSession实例：SqlSession包含了面向数据库指向SQL命令所需要的所有方法
    public static SqlSession getSqlSession(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        return sqlSession;
    }
}
```

##### ⑤ 编写代码

- 实体类

```java
//实体类
public class User {
    private int id;
    private String name;
    private String pwd;

    public User(){
        
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setId(int id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return super.toString();
    }
}
```

- 接口Interface

```java
public interface UserDao {
    List<User> getUserlist();
}
```

- 接口实现类:由继承类转换为一个Mapper文件

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace绑定一个对应的Mapper接口-->
<mapper namespace="dao.UserDao">
<!--查询语句-->
    <select id="getUserList" resultType="pojo.User">
        select * from mybatis.user
    </select>
</mapper>
```

### 三、通过Mybatis实现增删改查【CURD】

现在UserMapper接口中编写方法，然后到UserMapper.xml增加该方法的映射，然后到测试用例文件中编写代码

#### 1、Select

##### ① 在UserMapper接口中写对应方法

```java
 User getUserById(int id);
```

##### ② 在UserMapper.xml中写对应映射和sql语句

```xml
<mapper namespace="dao.UserDao">
<!--查询语句-->
    <select id="getUserList" resultType="pojo.User">
        select * from mybatis.user
    </select>
</mapper>
```

- id:对应namespace中的方法的名
- resultType：Sql语句执行的返回值
- parameterType:参数类型

##### ③ 在测试类中调用工具类使用相应的方法

```java
  //增删改需要提交事务
    @Test
    public void test() {
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        mapper.getUserById(4);
        sqlSession.close();
    }
```

#### 2、增删改

##### ① 在UserMapper接口中写对应方法

```java
  //插入用户
    int addUser(User user);

    //修改用户
    int update(User user);

    //删除用户
    int deleteUser(int id);
```

##### ② 在UserMapper.xml中写对应映射和sql语句

```xml
   <insert id="addUser" parameterType="pojo.User">
        insert into mybatis.user(id,name,pwd) values (#{id},#{name},#{pwd});
    </insert>

    <update id="update" parameterType="pojo.User">
        update mybatis.user set  name = #{name},pwd = #{pwd} where id=#{id};
    </update>

    <delete id="deleteUser" parameterType="int">
        delete from mybatis.user where id = #{id};
    </delete>
```

##### ③ 在测试类中调用工具类使用相应的方法

```java
  //增删改需要提交事务
    @Test
    public void test1() {
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        mapper.addUser(new User(4,"蜘蛛侠","104"));
        sqlSession.commit();
        sqlSession.close();
    }
```

#### 3、<font color=#plzky999>Map的使用</font>

`当数据库中的表、字段或者参数过多时，我们应当考虑使用Map`

```java
int addUser(Map<String,object>map);
```

```xml
<insert id="addUser" parameterType="map">
    insert into mybatis.user(id,pwd) values (#{userid},#{password});
</insert>
```

```java
@Test
public void addUser2(){
    Sqlsession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    Map<String,object> map = new HashMap<String,object>();
    map.put("userid",5);
    map.put("password","123");
    mapper.addUser2(map);
    sqlSession.close();
}
```





### 四、mybatis-config.xml配置解析

#### 1、环境配置(environments)

Mybatis可以配置适应多种环境，但是每个SqlSessionFactory实例只能选择一种环境。学会使用配置多套运行环境，Mybatis默认的事务管理器就是JDBC，连接池：POOLED。

#### 2、属性优化(properties)

可以通过配置properties属性来引用配置文件

##### ① 引入外部文件

```xml
<properties resource="db.properties"></properties>
```

##### ② 在properties中配置连接属性

```xml
    <properties resource="db.properties">
        <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf8"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
    </properties>
```



#### 3、别名优化(typeHandlers)

类型别名是为java类设置一个短的名字，可以减少类完全限定名的冗余

##### ① 指定类取别名

`实体类较少时，且可以自定义`

```xml
    <typeAliases>
        <typeAlias type="pojo.User" alias="User"/>
    </typeAliases>
```

##### ② 指定包取别名：引用时只需要写该包下，小写的类名(大写也能识别，只是写小写比较规范)

`实体类较多时，不能取类名`

```xml
    <typeAliases>
        <package name="pojo"/>
    </typeAliases>
```

##### ③ 若改包名仍要改类名，可以通过在实体类上增加注解

```java
@Alias("user")
public class User{}
```

#### 4、设置

```xml
<settings>
  <setting name="cacheEnabled" value="true"/>
  <setting name="lazyLoadingEnabled" value="true"/>
  <setting name="multipleResultSetsEnabled" value="true"/>
  <setting name="useColumnLabel" value="true"/>
  <setting name="useGeneratedKeys" value="false"/>
  <setting name="autoMappingBehavior" value="PARTIAL"/>
  <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
  <setting name="defaultExecutorType" value="SIMPLE"/>
  <setting name="defaultStatementTimeout" value="25"/>
  <setting name="defaultFetchSize" value="100"/>
  <setting name="safeRowBoundsEnabled" value="false"/>
  <setting name="mapUnderscoreToCamelCase" value="false"/>
  <setting name="localCacheScope" value="SESSION"/>
  <setting name="jdbcTypeForNull" value="OTHER"/>
  <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
</settings>
```

| 常用设置                 | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| cacheEnabled             | 全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。     |
| lazyLoadingEnabled       | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置 `fetchType` 属性来覆盖该项的开关状态。 |
| logImpl                  | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。        |
| mapUnderscoreToCamelCase | 是否开启驼峰命名自动映射，即从经典数据库列名 A_COLUMN 映射到经典 Java 属性名 aColumn |



#### 5、映射器说明(mappers)

① 使用资源路径【首选】

```xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
```

##### ② 使用接口映射器实现类的完全限定类名

`xml与方法接口必须写在java下同一包中，并且同名`

```xml
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
```

##### ③ 将包内的映射器接口实现全部注册为映射器

```xml
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```

### 五、生命周期与作用域

#### 1、生命周期与作用域

一旦创建了 SqlSessionFactory，就不再需要它了。 因此 SqlSessionFactoryBuilder 实例的最佳作用域是方法作用域（也就是局部方法变量）。

SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例。因此 SqlSessionFactory 的最佳作用域是应用作用域。 

每个线程都应该有它自己的 SqlSession 实例。SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域。如果你现在正在使用一种 Web 框架，考虑将 SqlSession 放在一个和 HTTP 请求相似的作用域中。 换句话说，每次收到 HTTP 请求，就可以打开一个 SqlSession，返回一个响应后，就关闭它。 这个关闭操作很重要，为了确保每次都能执行关闭操作，你应该把这个关闭操作放到 finally 块中。

映射器是一些绑定映射语句的接口。映射器接口的实例是从 SqlSession 中获得的。虽然从技术层面上来讲，任何映射器实例的最大作用域与请求它们的 SqlSession 相同。但方法作用域才是映射器实例的最合适的作用域。映射器实例应该在调用它们的方法中被获取，使用完毕之后即可丢弃。在这个作用域上管理太多像 SqlSession 的资源会让你忙不过来。 因此，最好将映射器放在方法作用域内。

#### 2、Mabatis执行流程

![image-20210611104641883](C:\Users\官二的磊子\Desktop\未来村村长\image-20210611104641883.png)



### 六、ResultMap

#### 1、解决属性名与字段名不一致问题

```java
public class User {
    private int id;
    private String name;
    private String password;
}
```

```xml
    <select id="getUserById" parameterType="int" resultType="User">
        select id,name,pwd from mybatis.user where id = #{id}
    </select>
```

以上便产生了属性名字段和sql字段不一致情况（password和pwd）

##### 解决办法 ①

```xml
    <select id="getUserById" parameterType="int" resultType="User">
        select id,name,pwd as password from mybatis.user where id = #{id}
    </select>
```

##### 解决办法 ②

通过< resultMap >标签,为返回的结果建立一个映射，属性名对应数据库表的列，即建立结果集映射：只需要对名称不同的属性进行映射。

- 建立resultMap标签：增加id，type（为对应sql的接口类）
- 在该标签下，增加column（sql对应的属性名），property（接口类对应的属性名）

```xml
<mapper namespace="dao.UserMapper">
    
    <resultMap id="UserMap" type="User">
        <result column="pwd" property="password"/>
    </resultMap>

    <select id="getUserById" parameterType="int" resultMap="UserMap">
        select * from mybatis.user where id = #{id}
    </select>

</mapper>
```

<!--column：数据库中的字段，property：实体类中的属性-->xml中出现中文注释可能导致编译失败

### 七、日志

#### 1、日志工厂

当数据库操作出现错误时，可以使用日志工厂进行排错。

logImpl：指定 MyBatis 所用日志的具体实现，未指定时将自动查找。

**STDOUT_LOGGING**：自动标准日志

```xml
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
```

![image-20210613115414935](C:\Users\官二的磊子\Desktop\未来村村长\image-20210613115414935.png)

#### 2、LOG4J

可以控制日志输送的目的地，输出格式，定义日志级别：通过配置文件进行配置不需要修改代码

##### ① 导入Log4j架包

```xml
    <dependencies>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.12</version>
        </dependency>
    </dependencies>
```

##### ② 建立logj.properties文件

```properties
# priority  :debug<info<warn<error
#you cannot specify every priority with different file for log4j 
log4j.rootLogger=debug,stdout,info,debug,warn,error 
 
#console
log4j.appender.stdout=org.apache.log4j.ConsoleAppender 
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout 
log4j.appender.stdout.layout.ConversionPattern= [%d{yyyy-MM-dd HH:mm:ss a}]:%p %l%m%n
#info log
log4j.logger.info=info
log4j.appender.info=org.apache.log4j.DailyRollingFileAppender 
log4j.appender.info.DatePattern='_'yyyy-MM-dd'.log'
log4j.appender.info.File=./src/com/hp/log/info.log
log4j.appender.info.Append=true
log4j.appender.info.Threshold=INFO
log4j.appender.info.layout=org.apache.log4j.PatternLayout 
log4j.appender.info.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n
#debug log
log4j.logger.debug=debug
log4j.appender.debug=org.apache.log4j.DailyRollingFileAppender 
log4j.appender.debug.DatePattern='_'yyyy-MM-dd'.log'
log4j.appender.debug.File=./src/com/hp/log/debug.log
log4j.appender.debug.Append=true
log4j.appender.debug.Threshold=DEBUG
log4j.appender.debug.layout=org.apache.log4j.PatternLayout 
log4j.appender.debug.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n
#warn log
log4j.logger.warn=warn
log4j.appender.warn=org.apache.log4j.DailyRollingFileAppender 
log4j.appender.warn.DatePattern='_'yyyy-MM-dd'.log'
log4j.appender.warn.File=./src/com/hp/log/warn.log
log4j.appender.warn.Append=true
log4j.appender.warn.Threshold=WARN
log4j.appender.warn.layout=org.apache.log4j.PatternLayout 
log4j.appender.warn.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n
#error
log4j.logger.error=error
log4j.appender.error = org.apache.log4j.DailyRollingFileAppender
log4j.appender.error.DatePattern='_'yyyy-MM-dd'.log'
log4j.appender.error.File = ./src/com/hp/log/error.log 
log4j.appender.error.Append = true
log4j.appender.error.Threshold = ERROR 
log4j.appender.error.layout = org.apache.log4j.PatternLayout
log4j.appender.error.layout.ConversionPattern = %d{yyyy-MM-dd HH:mm:ss a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n
```

##### ③  在config文件中设置

```xml
    <settings>
        <setting name="logImpl" value="LOG4J"/>
    </settings>
```

最后输出这样的结果：

![image-20210613122546928](C:\Users\官二的磊子\Desktop\未来村村长\image-20210613122546928.png)

##### 简单应用

```java
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;
import pojo.User;
import utils.MybatisUtils;
import org.apache.log4j.Logger;

public class UserMapperTest {
    static Logger logger = Logger.getLogger(UserMapperTest.class);
    @Test
    public void test2(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        System.out.println(mapper.getUserById(4));
        logger.info("test2测试成功");
        sqlSession.close();
    }
}
```

##### 日志级别

```java
logger,info("");
logger.debug("");
logger.error("");
```



### 八、<font color=#plzky999>分页</font>

`数据量太大时，分页展示减少数据处理量`

#### 1、使用Limit分页

```mysql
SELECT * FROM user LIMIT 2,-1;[start,size]
SELECT * FROM user LIMIT 3;[0,n]
```

#### 2、使用Mybatis实现分页(核心SQL)

##### ① 接口

```java
  //分页
    List<User> getUserByLimit(Map<String,Integer> map);
```

##### ② Mapper.xml

```xml
    <select id="getUserByLimit" parameterType="map" resultMap="UserMap">
        select * from mybatis.user limit #{startIndex},#{pageSize}
    </select>
```

##### ③ 测试

```java
    @Test
    public void getUserByLimit(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        HashMap<String,Integer> map = new HashMap<>();
        map.put("startIndex",0);
        map.put("pageSize",2);
        List<User> userList = mapper.getUserByLimit(map);
        for(User user:userList){
            System.out.println(user);
        }
    }
```

### 九、<font color=#999plzky>使用注解开发(在Mybatis不常用以后常用)</font>

`开发中常用面向接口编程，原因是解耦`

#### 1、使用说明

对于像 BlogMapper 这样的映射器类来说，还有另一种方法来完成语句映射。 它们映射的语句可以不用 XML 来配置，而可以使用 Java 注解来配置。比如，上面的 XML 示例可以被替换成如下的配置：

```java
package org.mybatis.example;
public interface BlogMapper {
  @Select("SELECT * FROM blog WHERE id = #{id}")
  Blog selectBlog(int id);
}
```

使用注解来映射简单语句会使代码显得更加简洁，但对于稍微复杂一点的语句，Java 注解不仅力不从心，还会让你本就复杂的 SQL 语句更加混乱不堪。 因此，如果你需要做一些很复杂的操作，最好用 XML 来映射语句。

##### ① 接口

```java
import org.apache.ibatis.annotations.Select;
import pojo.User;
import java.util.List;

public interface UserMapper {
    @Select("select * from user")
    List<User> getUsers();
}
```

##### ② 绑定接口

```xml
    <mappers>
        <mapper class="dao.UserMapper"/>
    </mappers>
```

##### ③ 测试

```java
public class UserMapperTest {
    @Test
    public void test(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> userList = mapper.getUsers();
        for(User user:userList){
            System.out.println(user);
        }
        sqlSession.close();
    }
}
```

#### 2、注解书写增删改查



##### ① 设置为自动提交事务

```java
//获取SqlSession实例：SqlSession包含了面向数据库指向SQL命令所需要的所有方法
public static SqlSession getSqlSession(){
    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    return sqlSession;
}
```



##### ② 增删改查语句书写

```java
    @Select("select * from user")
    List<User> getUsers();

    @Select("select * from user where id = #{id}")
    User getUserByID(@Param("id")int id);

    @Insert("insert into user(id,name,pwd) values (#{id},#{name},#{password})")
    int addUser(User user);

	@update("update user set name=#{name},pwd=#{passwoed} where id=#{id}")
    int updateUser(User user);

	@delete("delete from user where id = #{uid}")
	int deleteUser(@param("uid") int id);
```

@Param:

- 基本类型的参数或者String类型时需要加入，引用类型不需要加
- 如果只有一个基本类型，可以忽略
- 在SQL中引用的就是我们这里的@Parm()中设定的属性名



#### 3、Lombok（偷懒插件）

##### ① 先下载lombok插件

<img src="C:\Users\官二的磊子\Desktop\未来村村长\image-20210614120053831.png" alt="image-20210614120053831" style="zoom:50%;" />

##### ② 然后在项目中导入架包

```xml
<dependencies>
	<dependency>
		<groupId>org.projectlombok</groupId>
		<artifactId>lombok</artifactId>
		<version>1.18.20</version>
		<scope>provided</scope>
	</dependency>
</dependencies>
```

##### ③ 使用注释，免去编写getter、setter、toString、无参构造器、有参

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@NoArgsConstructor
@AllArgsConstructor
@Data
public class User {
    private int id;
    private String name;
    private String password;
}
```



### 十、动态SQL

`根据条件判断，进行不同的Sql语句拼接，即在sql里加入逻辑语句`

##### 1、if

通过 “title” 和 “author” 两个参数进行可选搜索:

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```

##### 2、choose(when,otherwise)

MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。

策略变为：传入了 “title” 就按 “title” 查找，传入了 “author” 就按 “author” 查找的情形。若两者都没有传入，就返回标记为 featured 的 BLOG

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

##### 3、trim(where,set)

where元素只会在子元素返回任何内容的情况下才插入 “WHERE” 子句。而且，若子句的开头为 “AND” 或 “OR”，where 元素也会将它们去除。

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```

如果where元素与你期望的不太一样，你也可以通过自定义trim元素来定制where元素的功能。比如，和where元素等价的自定义trim元素为：

```xml
<trim prefix="WHERE" prefixOverrides="AND|OR ">
  ...
</trim>
```

用于动态更新语句的类似解决方案叫做set。set 元素可以用于动态包含需要更新的列，忽略其它不更新的列。比如：

```xml
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```

巧用Map书写java语句

##### 4、foreach

动态SQL的另一个常见使用场景是对集合进行遍历（尤其是在构建IN条件语句的时候）。比如：

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```

foreach元素的功能非常强大，它允许你指定一个集合，声明可以在元素体内使用的集合项（item）和索引（index）变量。它也允许你指定开头与结尾的字符串以及集合项迭代之间的分隔符。这个元素也不会错误地添加多余的分隔符，看它多智能！

##### 5、sql复用片段

```xml
<sql id="if1">
    <if test = "title != null">title = #{title}</if>
</sql>

<select id="" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <include refid="if1"></include>
    </where>
</select>
```



### 十一、缓存

`第一次查询走数据库，第二次查询走缓存,先二级缓存，再一级缓存`

`一级缓存一个SqlSession独有一个，当Sqlsession关闭时，一级缓存内容存放在二级缓存，二级缓存在Mapper中`

#### 1、二级缓存启用方法

##### ① 开启全局缓存

在config.xml中的settings中设置

```xml
<settings>
    <setting nmae="cacheEnabled" value="true"/>
<settings>
```

##### ② 在Mapper.xml中开启二级缓存

```xml
<cache/>
```



#### 2、官方相关介绍:来自官方文档

MyBatis 内置了一个强大的事务性查询缓存机制，它可以非常方便地配置和定制。 为了使它更加强大而且易于配置，我们对 MyBatis 3 中的缓存实现进行了许多改进。

默认情况下，只启用了本地的会话缓存，它仅仅对一个会话中的数据进行缓存。 要启用全局的二级缓存，只需要在你的 SQL 映射文件中添加一行：

```xml
<cache/>
```

基本上就是这样。这个简单语句的效果如下:

- 映射语句文件中的所有 select 语句的结果将会被缓存。
- 映射语句文件中的所有 insert、update 和 delete 语句会刷新缓存。
- 缓存会使用最近最少使用算法（LRU, Least Recently Used）算法来清除不需要的缓存。
- 缓存不会定时进行刷新（也就是说，没有刷新间隔）。
- 缓存会保存列表或对象（无论查询方法返回哪种）的 1024 个引用。
- 缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调用者修改，而不干扰其他调用者或线程所做的潜在修改。

**提示** 缓存只作用于 cache 标签所在的映射文件中的语句。如果你混合使用 Java API 和 XML 映射文件，在共用接口中的语句将不会被默认缓存。你需要使用 @CacheNamespaceRef 注解指定缓存作用域。

这些属性可以通过 cache 元素的属性来修改。比如：

```xml
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
```

这个更高级的配置创建了一个 FIFO 缓存，每隔 60 秒刷新，最多可以存储结果对象或列表的 512 个引用，而且返回的对象被认为是只读的，因此对它们进行修改可能会在不同线程中的调用者产生冲突。

可用的清除策略有：

- `LRU` – 最近最少使用：移除最长时间不被使用的对象。
- `FIFO` – 先进先出：按对象进入缓存的顺序来移除它们。
- `SOFT` – 软引用：基于垃圾回收器状态和软引用规则移除对象。
- `WEAK` – 弱引用：更积极地基于垃圾收集器状态和弱引用规则移除对象。

默认的清除策略是 LRU。

flushInterval（刷新间隔）属性可以被设置为任意的正整数，设置的值应该是一个以毫秒为单位的合理时间量。 默认情况是不设置，也就是没有刷新间隔，缓存仅仅会在调用语句时刷新。

size（引用数目）属性可以被设置为任意正整数，要注意欲缓存对象的大小和运行环境中可用的内存资源。默认值是 1024。

readOnly（只读）属性可以被设置为 true 或 false。只读的缓存会给所有调用者返回缓存对象的相同实例。 因此这些对象不能被修改。这就提供了可观的性能提升。而可读写的缓存会（通过序列化）返回缓存对象的拷贝。 速度上会慢一些，但是更安全，因此默认值是 false。

**提示** 二级缓存是事务性的。这意味着，当 SqlSession 完成并提交时，或是完成并回滚，但没有执行 flushCache=true 的 insert/delete/update 语句时，缓存会获得更新。

#### 使用自定义缓存

除了上述自定义缓存的方式，你也可以通过实现你自己的缓存，或为其他第三方缓存方案创建适配器，来完全覆盖缓存行为。

```xml
<cache type="com.domain.something.MyCustomCache"/>
```

这个示例展示了如何使用一个自定义的缓存实现。type 属性指定的类必须实现 org.apache.ibatis.cache.Cache 接口，且提供一个接受 String 参数作为 id 的构造器。 这个接口是 MyBatis 框架中许多复杂的接口之一，但是行为却非常简单。

```java
public interface Cache {
  String getId();
  int getSize();
  void putObject(Object key, Object value);
  Object getObject(Object key);
  boolean hasKey(Object key);
  Object removeObject(Object key);
  void clear();
}
```

为了对你的缓存进行配置，只需要简单地在你的缓存实现中添加公有的 JavaBean 属性，然后通过 cache 元素传递属性值，例如，下面的例子将在你的缓存实现上调用一个名为 `setCacheFile(String file)` 的方法：

```
<cache type="com.domain.something.MyCustomCache">
  <property name="cacheFile" value="/tmp/my-custom-cache.tmp"/>
</cache>
```

你可以使用所有简单类型作为 JavaBean 属性的类型，MyBatis 会进行转换。 你也可以使用占位符（如 `${cache.file}`），以便替换成在[配置文件属性](https://mybatis.org/mybatis-3/zh/configuration.html#properties)中定义的值。

从版本 3.4.2 开始，MyBatis 已经支持在所有属性设置完毕之后，调用一个初始化方法。 如果想要使用这个特性，请在你的自定义缓存类里实现 `org.apache.ibatis.builder.InitializingObject` 接口。

```
public interface InitializingObject {
  void initialize() throws Exception;
}
```

**提示** 上一节中对缓存的配置（如清除策略、可读或可读写等），不能应用于自定义缓存。

请注意，缓存的配置和缓存实例会被绑定到 SQL 映射文件的命名空间中。 因此，同一命名空间中的所有语句和缓存将通过命名空间绑定在一起。 每条语句可以自定义与缓存交互的方式，或将它们完全排除于缓存之外，这可以通过在每条语句上使用两个简单属性来达成。 默认情况下，语句会这样来配置：

```
<select ... flushCache="false" useCache="true"/>
<insert ... flushCache="true"/>
<update ... flushCache="true"/>
<delete ... flushCache="true"/>
```

鉴于这是默认行为，显然你永远不应该以这样的方式显式配置一条语句。但如果你想改变默认的行为，只需要设置 flushCache 和 useCache 属性。比如，某些情况下你可能希望特定 select 语句的结果排除于缓存之外，或希望一条 select 语句清空缓存。类似地，你可能希望某些 update 语句执行时不要刷新缓存。

#### cache-ref

回想一下上一节的内容，对某一命名空间的语句，只会使用该命名空间的缓存进行缓存或刷新。 但你可能会想要在多个命名空间中共享相同的缓存配置和实例。要实现这种需求，你可以使用 cache-ref 元素来引用另一个缓存。

```
<cache-ref namespace="com.someone.application.data.SomeMapper"/>
```



### 十二、基本项目文件汇总

#### 导包和配置pom.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
<!--  父工程  -->
    <groupId>com.plord</groupId>
    <artifactId>MybatisStudy</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>mybatis-01</module>
        <module>mybatis-02</module>
        <module>mybatis-03</module>
        <module>mybatis-04</module>
        <module>mybatis-05</module>
        <module>mybatis-04</module>
    </modules>
    <!-- 导入依赖 -->
    <dependencies>
<!--mysql驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.23</version>
        </dependency>
<!--mybatis-->
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.6</version>
        </dependency>
        <!--junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>


    <build>
            
        <resources>

            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>

            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>

        </resources>

        <!--指定jdk版本-->
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!--加入这个插件估计就不会报以上错误了-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.22.1</version>
                <configuration>
                    <skipTests>true</skipTests>
                </configuration>
            </plugin>
        </plugins>

    </build>

</project>
```







#### 1、配置项目文件(resources)

##### ① mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <properties resource="db.properties">
        <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf8"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
    </properties>

    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>

    <typeAliases>
        <package name="pojo"/>
    </typeAliases>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    
    <mappers>
        
    </mappers>

</configuration>
```

##### ② db.properties

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf8
username=root
password=123456
```

#### 2、书写工具类（java-utils）

```java
package utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

public class MybatisUtils {
    private static SqlSessionFactory sqlSessionFactory;

    static {
        try {
            //获取sqlSessionFactory的对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }//静态代码块，初始即加载

    //获取SqlSession实例：SqlSession包含了面向数据库指向SQL命令所需要的所有方法
    public static SqlSession getSqlSession(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        return sqlSession;
    }
}
```

#### 3、书写书体类以及其对应接口和配置文件

##### ① 书写实体类（java-pojo）：即对应sql的表

```java
public class Student {
    //所有属性名
    private int id;
    private String name;
    //外码写法
    private Teacher teacher;   
}
```

##### ② 书写实体类对应的Mapper接口（java-dao）

```java
public interface StudentMapper {
}
```

##### 配置文件（resources-dao）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="dao.StudentMapper">

</mapper>
```

