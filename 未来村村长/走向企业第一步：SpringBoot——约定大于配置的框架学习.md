## 搭建企业级项目第一步：SpringBoot——约定大于配置的自动装配框架学习



### 一、搭建第一个SpringBoot程序

#### 1、编写与运行

##### ① 从IDEA中下载springboot框架

##### ② 在java文件包中建立dao、pojo、service、controller文件包：必须与项目启动类同级或下一级目录编写代码

##### ③ 在controller中编写程序,采用注释的方式，由springboot框架完成自动装配

```java
//自动装配
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String hello(){
        //调用业务，接收前端参数
        return "hello,world";
    }
}
```

#### 2、自动装配原理小讲

@SpringBootApplication——@SpringBootApplication——@EnableAutoConfiguration——@Import——AutoConfigurationImportSelector的selectImports()方法通过SpringFactoriesLoader.loadFactoryNames()扫描所有具有META-INF/spring.factories的jar包。spring-boot-autoconfigure-x.x.x.x.jar里就有一个这样的spring.factories文件。

```java
Spring Boot启动的时候会通过@EnableAutoConfiguration注解找到META-INF/spring.factories配置文件中的所有自动配置类，并对其进行加载，而这些自动配置类都是以AutoConfiguration结尾来命名的，它实际上就是一个JavaConfig形式的Spring容器配置类，它能通过以Properties结尾命名的类中取得在全局配置文件中配置的属性如：server.port，而XxxxProperties类是通过@ConfigurationProperties注解与全局配置文件中对应的属性进行绑定的。
```



### 二、配置文件

#### 1、yaml配置文件基本语法

```yaml
#语法1
#属性： 值
name: zhangyu


#语法2
#换行空格表示属性下属
student:
 name: zhangyu
 age: 19

#语法3
#同语法2作用
student: {age: 19,name: zhangyu}

#语法4
#一个属性多个值(数组)
books:
 - math
 - EN
 - CN
 
#语法5
#同语法4作用
books: [math,EN,CN]
```

#### 2、yaml特殊语法

```yaml
${random.uuid}
#随机生成数
${person.hello:hello}_旺财
#判断赋值是否存在，不存在使用默认值
```

#### 3、多个环境的配置文件的建立

springboot的主核心配置文件只能有一个，命名为application

多个配置环境时，命名需要application-xxx，也可以用yaml的---分隔符区别环境的不同，即一个yaml文件可实现多个环境文件配置(xxx就是该配置文件的名字，调用时在主核心配置文件中用xxx调用)

要使用其它环境配置文件时，要在主核心配置文件中进行激活：spring.profiles.active=xxx



### 三、属性赋值方法

#### 1、原始

通过new一个对象进行赋值

#### 2、常规

① 通过@Value方式实现赋值

也可通过@Value("${xxx}")的方式从核心配置文件中获取xxx对应的值

② 通过@Autowired实现自动装配

#### 3、通过yaml配置文件进行赋值（核心常用）

① 通过

```java
@Component
@ConfigurationProperties(prefix = "personvalue")
public class person {
```

将类与配置文件进行绑定

`ConfigurationProperties`会告诉Springboot将本类中的所有属性和配置文件中的相关配置进行绑定

```yaml
personvalue:
 name: zhangyuge
 age: 18
 happy: true
 birth: 20020903
 maps: {k1: v2,K2: v3}
 lists: [dingzui,aishengqi]
 dog:
  name: zhangyu
  age: 4
```

② 通过@Autowired实现自动装配

提示：通过yaml配置文件进行赋值

- 支持松散绑定，即命名格式-或者驼峰可混用识别
- 支持JSR303数据校验
  - ① 使用注解@Validated

#### 4、通过properties配置文件进行赋值（不常用）

(properties文件容易乱码，也可配置但不常用，建议使用yaml文件形式)

```go
@Component
@PropertySource("classpath:website.properties")
class WebSite {
```



### 四、SpringBoot Web开发

#### 1、SpringBoot整合jsp文件

##### ① 在src文件夹下建立webapp目录

- ![image-20210902102619443](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210902102619443.png)

② 打开有上角的项目结构（文件夹下三蓝块），给该文件夹添加Web资源

- ![image-20210902102757284](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210902102757284.png)



##### ③ 增加springboot内嵌tomcat对jsp的解析依赖，在build中增加jps的编译路径

```xml
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
        </dependency>
```

```xml
        <resources>
         <resource>
            <!--源文件夹-->
            <directory>src/main/webapp</directory>
             <!--制定编译的路径：target的path-->
            <targetPath>META-INF/resources</targetPath>
             <!--制定源文件夹中的哪些资源需要编译-->
            <includes>
                <include>*.*</include>
            </includes>
         </resource>
        </resources>
```

##### ④ 在配置文件中配置视图解析器

```yaml
spring:
  mvc:
    view:
      prefix: /
      suffix: .jsp
```

##### ⑤ 在webapp中编写jsp文件

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<h1>${message}</h1>
</body>
</html>
```

##### ⑥ 在controller中编写控制器

```java
@RestController
public class akacontroller {
    @RequestMapping("/jsp")
    public ModelAndView jsp(){
        ModelAndView mv = new ModelAndView();
        mv.addObject("message","hello,springBoot");
        mv.setViewName("say");
        return mv;
    }
}
```

#### 2、Springboot整合Mybatis

##### ① Mybatis的逆向工程来生成实体bean、映射文件、dao接口

- 1.环境配置pom.xml

- ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
      <parent>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-parent</artifactId>
          <version>2.5.4</version>
          <relativePath/> <!-- lookup parent from repository -->
      </parent>
      <groupId>com.plord</groupId>
      <artifactId>springboot-mybatis-2</artifactId>
      <version>0.0.1-SNAPSHOT</version>
      <name>springboot-mybatis-2</name>
      <description>Demo project for Spring Boot</description>
  
  
      <properties>
          <java.version>1.8</java.version>
      </properties>
  
  
  <dependencies>
      <dependency>
          <groupId>org.apache.tomcat.embed</groupId>
          <artifactId>tomcat-embed-jasper</artifactId>
      </dependency>
  
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
  
      <dependency>
          <groupId>org.mybatis.spring.boot</groupId>
          <artifactId>mybatis-spring-boot-starter</artifactId>
          <version>2.1.2</version>
      </dependency>
  
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
          <version>8.0.23</version>
          <scope>runtime</scope>
      </dependency>
  
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-test</artifactId>
          <scope>test</scope>
          <exclusions>
              <exclusion>
                  <groupId>org.junit.vintage</groupId>
                  <artifactId>junit-vintage-engine</artifactId>
              </exclusion>
          </exclusions>
      </dependency>
  </dependencies>
  
  <build>
      <resources>
          <resource>
              <directory>src/main/java</directory>
              <includes>
                  <include>**/*.xml</include>
              </includes>
          </resource>
  
          <resource>
              <!--源文件位置-->
              <directory>src/main/webapp</directory>
              <!--指定编译到META-INF/resources-->
              <targetPath>META-INF/resources</targetPath>
              <!--指定源文件夹中的哪些资源要进行编译-->
              <includes>
                  <include>*.*</include>
              </includes>
          </resource>
  
          <resource>
              <directory>src/main/resources</directory>
              <includes>
                  <include>**/*.yml</include>
                  <include>**/*.properties</include>
                  <include>**/*.xml</include>
              </includes>
          </resource>
  
      </resources>
  
  
      <plugins>
          <plugin>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-maven-plugin</artifactId>
          </plugin>
  
          <!--mybatis逆向工程插件-->
          <plugin>
              <groupId>org.mybatis.generator</groupId>
              <artifactId>mybatis-generator-maven-plugin</artifactId>
              <version>1.3.6</version>
              <configuration>
                  <configurationFile>GeneratorMapper.xml</configurationFile>
                  <verbose>true</verbose>
                  <overwrite>true</overwrite>
              </configuration>
          </plugin>
  
      </plugins>
  </build>
  
  </project>
  
  ```

- 2.创建数据库和表

- ```sql
  USE `springboot`;
  
  CREATE TABLE `t_student` (
  `id` INT(10) NOT NULL AUTO_INCREMENT COMMENT '学生id',
  `name` VARCHAR(100) NOT NULL COMMENT '姓名',
  `age` INT(11) NOT NULL COMMENT '年龄',
  PRIMARY KEY(id)
  ) ENGINE=INNODB DEFAULT CHARSET=utf8
  
  INSERT  INTO `t_student`(`id`,`name`,`age`)VALUES
  (1,'小明',1),
  (2,'小张',10),
  (3,'小徐',5);
  
  
  ```

- 3.在根目录下建立文件GeneratorMapper.xml

- ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE generatorConfiguration PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
          "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
  <generatorConfiguration>
  
      <!--指定mysql的jdbc驱动位置，指定到本机完整路径-->
      <classPathEntry location="C:\Users\官二的磊子\Desktop\文件留存\mysql-connector-java-8.0.23\mysql-connector-java-8.0.23.jar" />
  
      <!--配置table表信息内容体，targetRuntime指定采用MyBatis3版本-->
      <context id="tables" targetRuntime="MyBatis3">
  
          <!--抑制生成注释，由于生成的注释都是英文的，可以不让它生成-->
          <commentGenerator>
              <property name="suppressAllComments" value="true"/>
          </commentGenerator>
  
          <!--配置数据库连接信息-->
          <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                          connectionURL="jdbc:mysql://127.0.0.1:3306/springboot"
                          userId="root"
                          password="123456">
          </jdbcConnection>
  
          <!--生成model类，targetPackage指定model类的包名，targetProject指定生成的model放在ide的哪个工程下面-->
          <javaModelGenerator targetPackage="com.plord.springbootmybatis2.beans" targetProject="src\main\java">
              <property name="enableSubPackages" value="false"/>
              <property name="trimStrings" value="false" />
          </javaModelGenerator>
  
          <!--生成mybatis的mapper.xml文件targetPackage指定mapper.xml文件的包名，
          targetProject指定生成的mapper.xml放在ide的哪个工程下面-->
          <sqlMapGenerator targetPackage="com.plord.springbootmybatis2.mapper" targetProject="src\main\java">
              <property name="enableSubPackages" value="false"/>
          </sqlMapGenerator>
  
          <!--生成mybatis的mapper的接口类文件，targetPackage指定mapper接口类的包名，targetProject指定生成的mapper接口放在
          ide哪个工程下面-->
          <javaClientGenerator type="XMLMAPPER" targetPackage="com.plord.springbootmybatis2.mapper" targetProject="src\main\java">
              <property name="enableSubPackages" value="false"/>
          </javaClientGenerator>
  
          <!--数据表名及对应的java模型类名-->
          <table tableName="t_student" domainObjectName="Student"
                 enableCountByExample="false"
                 enableUpdateByExample="false"
                 enableDeleteByExample="false"
                 enableSelectByExample="false"
                 selectByExampleQueryId="false" >
              <property name="useActualColumnNames" value="false"/>
          </table>
  
      </context>
  </generatorConfiguration>
  ```

- 4.通过插件逆向生成beans（pojo）和Mapper映射文件

- ![image-20210906203338394](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210906203338394.png)

- 完成生成
- ![image-20210906203400794](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210906203400794.png)

##### ② 建立service服务层和controller控制层

- 服务层

- ```java
  package com.plord.springbootmybatis2.service;
  
  import com.plord.springbootmybatis2.beans.Student;
  
  public interface StudentService {
  
      Student queryStudentId(Integer id);
  
  }
  
  ```

- ```java
  package com.plord.springbootmybatis2.service;
  
  import com.plord.springbootmybatis2.beans.Student;
  import com.plord.springbootmybatis2.mapper.StudentMapper;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.stereotype.Service;
  
  
  @Service
  public class StudentServiceImpl implements StudentService {
  
      @Autowired
      private StudentMapper studentMapper;
  
      @Override
      public Student queryStudentId(Integer id) {
          return studentMapper.selectByPrimaryKey(id);
      }
  }
  ```

- 控制层

- ```java
  package controller;
  
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.beans.factory.annotation.Qualifier;
  import org.springframework.stereotype.Controller;
  import org.springframework.ui.Model;
  import org.springframework.web.bind.annotation.RequestMapping;
  import pojo.Books;
  import service.BookService;
  import service.BookServiceImpl;
  
  import java.awt.print.Book;
  import java.util.List;
  
  @Controller
  @RequestMapping("/book")
  public class BookController {
      //controller调service
      @Autowired
      @Qualifier("BookServiceImpl")
      private BookService bookService;
  
      //查询全部的书籍，并返回到书籍展示页面
      public String list(Model model){
          List<Books> list = bookService.queryAllBook();
          model.addAttribute("lsit",list);
          return "allBook";
      }
  }
  
  ```



###### 注意Mybatis映射文件的存放：



### 五、SpringBoot与事务

在方法上添加注解**@Transactional**

若该方法执行中出现错误，则会整体回滚，该方法错误生成前的代码不生效



### 六、RESTful风格

```java
@RestController
public class StuController {
    @Autowired
    private StudentService studentService;

    @RequestMapping("/student/{idnumber}")
    public Object student(@PathVariable("idnumber") Integer id){
        Student student = studentService.queryStudentId(id);
        return student;
    }
}
```

非RESTful风格，访问方式为/student?id=xxx&xxx=xxx&...

RESTful中将参数位置放置在设置的访问路径中，并用@PathVariable("")注释形参

注意：当出现同类型参数时，即请求路径相同时

- 通常在RESTful风格中方法的方式会按照增删改查的注释方式进行区分（即请求方式不同）








