## Mybatis中文帮助文档整理：Mybatis一篇教你学会

文档地址：https://mybatis.org/mybatis-3/zh/index.html

## 入门

### 一、安装：只需要在pom.xml中配置即可

```xml
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>x.x.x</version>
</dependency>
```

### 二、构建SqlSessionFactory

每个基于MyBatis的应用都是基于一个实例SqlSessionFactory为核心的，获得它需要：

- 可以通过SqlSessionFactory获得，获得它需要：
  - 从XML配置文件或者Configuration实例来构建出

##### ① XML配置文件

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
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```

##### ② 构建SqlSessionFactory

```java
String resource = "org/mybatis/example/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```

##### ③ SQL语句XML映射

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.example.BlogMapper">
  <select id="selectBlog" resultType="Blog">
    select * from Blog where id = #{id}
  </select>
</mapper>
```

##### ④ 从SqlSessionFactory中获取SqlSession

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  BlogMapper mapper = session.getMapper(BlogMapper.class);
  Blog blog = mapper.selectBlog(101);
}
```

## XML配置

### 一、属性(properties)

##### ① 属性可以在外部配置和动态替换

```xml
<properties resource="org/mybatis/example/config.properties">
  <property name="username" value="dev_user"/>
  <property name="password" value="F2Fa3!33TYyg"/>
</properties>

<dataSource type="POOLED">
  <property name="driver" value="${driver}"/>
  <property name="url" value="${url}"/>
  <property name="username" value="${username}"/>
  <property name="password" value="${password}"/>
</dataSource>
```

这个例子中的 username 和 password 将会由 properties 元素中设置的相应值来替换。 driver 和 url 属性将会由config.properties 文件中对应的值来替换。这样就为配置提供了诸多灵活选择。

② Mybatis将按照下面的顺序来加载：

- 读取properties元素体内指定的属性
- 然后根据resource属性或根据url路径读取文件，并覆盖之前读取的同名属性
- 最后将读取作为方法参数传递的属性，并覆盖之前读取过的同名属性



二、类型别名

























