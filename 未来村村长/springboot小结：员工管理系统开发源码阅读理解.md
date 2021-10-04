## 适合springboot初学者！通过解析SpringBoot项目源码总结知识点，快速学习+复习

该项目源码来自B站编程不良人，项目为员工管理系统。数据库十分简单，只有登录用户表和员工表。本篇目的在于一步步分析项目及其源码，快速带领大家走进springboot项目的完成，从上至下学习知识点，可达到事半功倍的效果。

<font color=1124zky242pl424342zddsfdsfsdffsfwrwz>项目源码提取在文末！！！</font>

本项目解读涉及复习到的的技术栈包含：html、thymeleaf、springboot、mybatis、mysql、spring、javaWeb(session)

### 前言

前言所涉及知识点较为基础全面，基础好的小伙伴可直接跳过或者略读。

#### （1）常用注解说明

spring的一大特点便是面向注解开放，将建立的类注册为bean交给spring容器管理其的使用调度，所以在复习springboot知识点前，先复习各个注解对应的应用。

##### 1、成为可被扫描装配的组件

- `@Component` ：通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用`@Component` 注解标注。
- `@Repository` : 对应持久层即 Dao 层，主要用于数据库相关操作。
- `@Service` : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- `@Controller` : 对应 Spring MVC 控制层，主要用户接受用户请求并调用 Service 层返回数据给前端页面。

解析：我们一般使用 `@Autowired` 注解让 Spring 容器帮我们自动装配 bean。要想把类标识成可用于 `@Autowired` 注解自动装配的 bean 的类,可以采用以上注解实现。



##### 2、赋值

-  `@value`(常用)

使用 `@Value("${property}")` 读取比较简单的配置信息：

```go
@Value("${wuhan2020}")
String wuhan2020;
```

-  `@ConfigurationProperties`(常用)

通过`@ConfigurationProperties`读取配置信息并与 bean 绑定。

```go
@Component
@ConfigurationProperties(prefix = "library")

static class Book {
    String name;
    String description;
    }

```

你可以像使用普通的 Spring bean 一样，将其注入到类中使用。

- `PropertySource`（不常用）

`@PropertySource`读取指定 properties 文件

```go
@Component
@PropertySource("classpath:website.properties")

class WebSite {
    @Value("${url}")
    private String url;
}
```



##### 3、控制器

`@RestController`

`@RestController`注解是`@Controller和`@`ResponseBody`的合集,表示这是个控制器 bean,并且是将函数的返回值直接填入HTTP响应体中,是REST风格的控制器。

现在都是前后端分离，说实话我已经很久没有用过`@Controller`。如果你的项目太老了的话，就当我没说。

单独使用 `@Controller` 不加 `@ResponseBody`的话一般使用在要返回一个**视图**的情况，这种情况属于比较传统的 Spring MVC 的应用，对应于前后端不分离的情况。`@Controller` +`@ResponseBody` 返回 JSON 或 XML 形式数据

即

`@Controller`返回一个页面

`@RestController`返回json或xml形式数据

`@Controller `+`@ResponseBody`返回JSON 或 XML 形式数据



##### 4、主类

 `@SpringBootApplication`

我们可以把 `@SpringBootApplication`看作是 `@Configuration`、`@EnableAutoConfiguration`、`@ComponentScan` 注解的集合。

根据 SpringBoot 官网，这三个注解的作用分别是：

- `@EnableAutoConfiguration`：启用 SpringBoot 的自动配置机制
- `@ComponentScan`：扫描被`@Component` (`@Service`,`@Controller`)注解的 bean，注解默认会扫描该类所在的包下所有的类。
- `@Configuration`：允许在 Spring 上下文中注册额外的 bean 或导入其他配置类



##### 5、自动导入对象到类中

`@Autowired`

自动导入对象到类中，被注入进的类同样要被 Spring 容器管理比如：Service 类注入到 Controller 类中。

我们一般使用 `@Autowired` 注解让 Spring 容器帮我们自动装配 bean。要想把类标识成可用于 `@Autowired` 注解自动装配的 bean 的类,可以采用以下注解实现：

- `@Component` ：通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用`@Component` 注解标注。
- `@Repository` : 对应持久层即 Dao 层，主要用于数据库相关操作。
- `@Service` : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- `@Controller` : 对应 Spring MVC 控制层，主要用户接受用户请求并调用 Service 层返回数据给前端页面。



##### 6、Mybatis相关

`@Mapper`：为dao接口添加注释

`@MapperScan`：为xxxApplication启动类指定Mapper映射文件的位置，若Mapper存储在src/main/resources/mapper/xxxMapper.xml则不需要加该注解



#### （2）html常见标签复习

- ```html
  基本
  
  <html>…</html>      定义 HTML 文档
  
  <head>…</head>   文档的信息
  
  <meta>                    HTML 文档的元信息
  
  <title>…</title>        文档的标题
  
  <link>                      文档与外部资源的关系
  
  <style>…</style>    文档的样式信息
  
  <body>…</body>   可见的页面内容
  
  <!--…-->                 注释
  
  文本
  <h1>...</h1>               标题字大小（h1~h6）
  
  <b>...</b>                   粗体字
  
  <strong>...</strong>   粗体字(强调) 
  
  <i>...</i>                      斜体字 
  
  <em>...</em>              斜体字(强调)
  
  <center>…</center>   居中文本
  
  <ul>…</ul>                 无序列表 
  
  <ol>…</ol>                 有序列表
  
  <li>…</li>                    列表项目
  
  <a href=”…”>…</a>    超链接
  
  <font>                         定义文本字体尺寸、颜色、大小
  
  <sub>                         下标
  
  <sup>                         上标
  
  <br>                           换行
  
  <p>                            段落
  
   
  
  图形
  <img src=’”…”>   定义图像
  
  <hr>                   水平线
  
  <del>                  加删除线
  
   
  
  表格
  <table>…</table>   定义表格
  
  <th>…</th>            定义表格中的表头单元格
  
  <tr>…</tr>             定义表格中的行
  
  <td>…</td>           定义表格中的单元
  
   
  
  其它
  <form>…</form>    定义供用户输入的 HTML 表单
  
  <frame>                 定义框架集的窗口或框架
  ```

#### （3）返回数据与Session

##### 返回数据常用方法

要实现Controller返回数据给页面，Spring MVC 提供了以下几种途径（只列举常见的）：

方式一：通过org.springframework.web.servlet.ModelAndView实现

```java
	@RequestMapping(value="/view/{userId}/use/ModelAndView", method=RequestMethod.GET)
	private ModelAndView getUserInfo(@PathVariable("userId") Integer userId){
		User user = userService.getUserById(userId);
		return new ModelAndView("userinfo", "user", user);
	}
```

ModelAndView：将视图和数据封装成ModelAndView对象，作为方法的返回值，数据最终会存到HttpServletRequest对象中

方式二：通过org.springframework.ui.Model实现

```java
	@RequestMapping(value="/view/{userId}/use/Model", method=RequestMethod.GET)
	private String getUserInfo(@PathVariable("userId") Integer userId, Model model){
		User user = userService.getUserById(userId);
		model.addAttribute("user", user);
		return "userinfo";
	}
```

Model对象：通过给方法添加引用Model对象入参，直接通过addAttribute往Model对象添加属性值。

方式三：直接将数据存到HttpSession，让页面可以获取

```java
	@RequestMapping(value="/view/{userId}/use/HttpSession", method=RequestMethod.GET)
	private String getUserInfo(@PathVariable("userId") Integer userId, HttpSession session){
		User user = userService.getUserById(userId);
		session.setAttribute("user", user);
		return "userinfo";
	}
```

Session对象：通过给方法添加引用Session对象入参，直接通过setAttribute往Session对象添加属性值。



##### Session简单介绍

###### 1、概念

Cookie 可以让服务端程序跟踪每个客户端的访问，但是每次客户端的访问都必须传回这些 Cookie，如果 Cookie 很多，这无形地增加了客户端与服务端的数据传输量，而 Session 的出现正是为了解决这个问题。

同一个客户端每次和服务端交互时，不需要每次都传回所有的 Cookie 值，而是只要传回一个 ID，这个 ID 是客户端第一次访问服务器的时候生成的，而且每个客户端是唯一的。这样每个客户端就有了一个唯一的 ID，客户端只要传回这个 ID 就行了，这个 ID 通常是 NANE 为 JSESIONID 的一个 Cookie。

当访问服务器否个网页的时候，会在**服务器端**的内存里开辟一块内存，这块内存就叫做session，而这个内存是跟浏览器关联在一起的。这个浏览器指的是浏览器窗口，或者是浏览器的子窗口，意思就是，只允许当前这个session对应的浏览器访问，就算是在同一个机器上新启的浏览器也是无法访问的。而另外一个浏览器也需要记录session的话，就会再启一个属于自己的session。

Session可实现共享数据，会话之间的数据不能共享

服务器会给每一个用户（浏览器）创建一个Session，一个Session独占一个浏览器,一个Session对应一个ID,所以需要创建一个Cookie对象，并设置其有效时间。这样当用户关闭浏览器，重新打开浏览器访问该页面时，服务器也能找到之前为用户创建的Session对象。

###### 2、Session方法介绍

```java
//新建Session
//用户第1次访问的时候，自动创建会话
//获取Session的创建时间
session.getCreationTime();
//获取Session
HttpSession session = req.getSession();
//给Session储存信息
session.setAttribute("name","pl");//将值与名称关联后存储到当前的HttpSession的对象中
//获取Session的ID
String id = session.getId();
//从HttpSession对象获取Session的信息
String name =(String) session.getAttribute("name");
System.out.println(name);
//返回客户端上一次发送Session请求的时间
session.getLastAccessedTime();
//删除HttpSession对象对应的信息
session.removeAttribute("name");
//强制注销session
session.invalidate();
```

#### (4)thymeleaf语法简介

<table><thead><tr><th>关键字</th><th>功能介绍</th><th>案例</th></tr></thead><tbody><tr><td>th:id</td><td>替换id</td><td><code>&lt;input th:id="'xxx' + ${collect.id}"/&gt;</code></td></tr><tr><td>th:text</td><td>文本替换</td><td><code>&lt;p th:text="${collect.description}"&gt;description&lt;/p&gt;</code></td></tr><tr><td>th:utext</td><td>支持html的文本替换</td><td><code>&lt;p th:utext="${htmlcontent}"&gt;conten&lt;/p&gt;</code></td></tr><tr><td>th:object</td><td>替换对象</td><td><code>&lt;div th:object="${session.user}"&gt;</code></td></tr><tr><td>th:value</td><td>属性赋值</td><td><code>&lt;input th:value="${user.name}" /&gt;</code></td></tr><tr><td>th:with</td><td>变量赋值运算</td><td><code>&lt;div th:with="isEven=${prodStat.count}%2==0"&gt;&lt;/div&gt;</code></td></tr><tr><td>th:style</td><td>设置样式</td><td><code>th:style="'display:' + @{(${sitrue} ? 'none' : 'inline-block')} + ''"</code></td></tr><tr><td>th:onclick</td><td>点击事件</td><td><code>th:onclick="'getCollect()'"</code></td></tr><tr><td>th:each</td><td>属性赋值</td><td><code>tr th:each="user,userStat:${users}"&gt;</code></td></tr><tr><td>th:if</td><td>判断条件</td><td><code>&lt;a th:if="${userId == collect.userId}" &gt;</code></td></tr><tr><td>th:unless</td><td>和th:if判断相反</td><td><code>&lt;a th:href="@{/login}" th:unless=${session.user != null}&gt;Login&lt;/a&gt;</code></td></tr><tr><td>th:href</td><td>链接地址</td><td><code>&lt;a th:href="@{/login}" th:unless=${session.user != null}&gt;Login&lt;/a&gt; /&gt;</code></td></tr><tr><td>th:switch</td><td>多路选择 配合th:case 使用</td><td><code>&lt;div th:switch="${user.role}"&gt;</code></td></tr><tr><td>th:case</td><td>th:switch的一个分支</td><td><code>&lt;p th:case="'admin'"&gt;User is an administrator&lt;/p&gt;</code></td></tr><tr><td>th:fragment</td><td>布局标签，定义一个代码片段，方便其它地方引用</td><td><code>&lt;div th:fragment="alert"&gt;</code></td></tr><tr><td>th:include</td><td>布局标签，替换内容到引入的文件</td><td><code>&lt;head th:include="layout :: htmlhead" th:with="title='xx'"&gt;&lt;/head&gt; /&gt;</code></td></tr><tr><td>th:replace</td><td>布局标签，替换整个标签到引入的文件</td><td><code>&lt;div th:replace="fragments/header :: title"&gt;&lt;/div&gt;</code></td></tr><tr><td>th:selected</td><td>selected选择框 选中</td><td><code>th:selected="(${xxx.id} == ${configObj.dd})"</code></td></tr><tr><td>th:src</td><td>图片类地址引入</td><td><code>&lt;img class="img-responsive" alt="App Logo" th:src="@{/img/logo.png}" /&gt;</code></td></tr><tr><td>th:inline</td><td>定义js脚本可以使用变量</td><td><code>&lt;script type="text/javascript" th:inline="javascript"&gt;</code></td></tr><tr><td>th:action</td><td>表单提交的地址</td><td><code>&lt;form action="subscribe.html" th:action="@{/subscribe}"&gt;</code></td></tr><tr><td>th:remove</td><td>删除某个属性</td><td><code>&lt;tr th:remove="all"&gt; 1.all:删除包含标签和所有的孩子。2.body:不包含标记删除,但删除其所有的孩子。3.tag:包含标记的删除,但不删除它的孩子。4.all-but-first:删除所有包含标签的孩子,除了第一个。5.none:什么也不做。这个值是有用的动态评估。</code></td></tr><tr><td>th:attr</td><td>设置标签属性，多个属性可以用逗号分隔</td><td>比如 <code>th:attr="src=@{/image/aa.jpg},title=#{logo}"</code>，此标签不太优雅，一般用的比较少。</td></tr></tbody></table>

@{}的使用：用于获取链接地址。

${}的使用：和常规的使用差不多，用于**字符串的替换**或**对象获取**或**属性绑定**等，具体作用根据标签而定

@{}和${}的结合使用：在a标签的href中直接直接写对应值会导致**解析失败**

- ```html
  @{/admin/xxx/{id}(input(id=$(type))}
  ```





### 一、库表组成分析

一共两个数据库，分别是用户和员工，其中的字段如下，就不必多说啦。

```sql
CREATE TABLE `user` (
  `id` INT(11) UNSIGNED NOT NULL AUTO_INCREMENT,
  `username` VARCHAR(40) DEFAULT NULL COMMENT '用户名',
  `realname` VARCHAR(60) DEFAULT NULL COMMENT '真实姓名',
  `password` VARCHAR(40) DEFAULT NULL COMMENT '密码',
  `gender` TINYINT(1) UNSIGNED DEFAULT NULL COMMENT '性别',
  PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `employee` (
  `id` INT(11) UNSIGNED NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(60) DEFAULT NULL COMMENT '员工姓名',
  `salary` DOUBLE(10,2) DEFAULT NULL COMMENT '员工工资',
  `birthday` DATETIME DEFAULT NULL COMMENT '员工生日',
  `photo` VARCHAR(200) DEFAULT NULL COMMENT '头像路径',
  PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8mb4;
```



### 二、整合Mybatis（Dao+Pojo）

##### 1、添加相关依赖

整合Mybatis要添加mysql-connecter-java和mybatis-spring-boot-starter的依赖

```xml
		<!--mysql-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.38</version>
        </dependency>
        <!--myabtis-springboot-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.4</version>
        </dependency>
```

##### 2、建立表对应的属性类（pojo）

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Employee {
    private Integer id;
    private String name;
    private Double salary;
    private Date birthday;
    private String photo;//头像路径 
}

===========================================
    
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private Integer id;
    private String username;
    private String realname;
    private String password;
    private Boolean gender;
}
```

这里使用了偷懒插件lombok

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.10</version>
</dependency>
```

使用@Data是对应建立getter、setter、tostring方法，剩下两个分别是无参构造器（@NoArgsConstructor）和有参构造器（@AllArgsConstructor）

##### 3、建立sql操作层（DAO）

一个数据库表对应一个操作

```java
public interface EmployeeDao {

    //员工列表
    List<Employee> lists();

    //保存员工信息
    void save(Employee employee);

    //根据id查询一个
    Employee findById(Integer id);

    //更新员工信息
    void update(Employee employee);

    //删除员工信息
    void delete(Integer id);
}
```

```java
public interface UserDao {

    //根据用户查询用户
    User findByUserName(String username);

    //保存用户信息
    void save(User user);

}
```

<font color=#zkywqedsa>注释解释</font>：

@Repository和@Controller、@Service、@Component的作用差不多，都是把对象交给spring管理。@Repository用在持久层的接口上，这个注解是将接口的一个实现类交给spring管理。

为什么有时候我们不用@Repository来注解接口,我们照样可以注入到这个接口的实现类呢?
1、spring配置文件中配置了MapperScannerConfigurer这个bean，它会扫描持久层接口创建实现类并交给spring管理。

2、接口上使用了@Mapper注解或者springboot中主类上使用了@MapperScan注解，和MapperScannerConfigurer作用一样。

注：所以Dao层可以不使用@Repository注解，虽然idea会报警告，提示找不到这个bean，直接忽略即可。

##### 4、编写数据库的mapper映射文件

文件放置在resource目录下，这样springboot会自动扫描配置映射文件

```xml
<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.baizhi.dao.EmployeeDao">

    <!--lists-->
    <select id="lists" resultType="Employee">
        select id,name,salary,birthday,photo from `employee`
    </select>
    
    <!--save-->
    <insert id="save" parameterType="Employee" useGeneratedKeys="true" keyProperty="id">
        insert into `employee` values (#{id},#{name},#{salary},#{birthday},#{photo})
    </insert>


    <!--findById-->
    <select id="findById" parameterType="Integer" resultType="Employee">
        select id,name,salary,birthday,photo from `employee`
        where id = #{id}
    </select>

    <!--update-->
    <update id="update" parameterType="Employee" >
        update `employee` set name=#{name},salary=#{salary},birthday=#{birthday},photo=#{photo}
        where id = #{id}
    </update>

    <!--delete-->
    <delete id="delete" parameterType="Integer">
        delete from `employee` where id = #{id}
    </delete>
    

</mapper>
```

```xml
<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.baizhi.dao.UserDao">

    <!--findByUserName-->
    <select id="findByUserName" parameterType="String" resultType="User">
        select id,username,realname,password,gender from `user`
        where username = #{username}
    </select>

    <!--save-->
    <insert id="save" parameterType="User" useGeneratedKeys="true" keyProperty="id">
        insert into `user` values(#{id},#{username},#{realname},#{password},#{gender})
     </insert>

</mapper>
```

##### 5、在启动类增加@MapperScan注解

1、@Mapper注解：
作用：在接口类上添加了@Mapper，在编译之后会生成相应的接口实现类
添加位置：接口类上面
如果想要每个接口都要变成实现类，那么需要在每个接口类上加上@Mapper注解，比较麻烦，解决这个问题用@MapperScan

2、@MapperScan
作用：指定要变成实现类的接口(即上述所创建的Dao)所在的包，然后包下面的所有接口在编译之后都会生成相应的实现类
添加位置：是在Springboot启动类上面添加，

```java
@SpringBootApplication
@MapperScan("com.plord.demo.dao")
public class EmsThymeleafApplication {
    public static void main(String[] args) {
        SpringApplication.run(EmsThymeleafApplication.class, args);
    }
}
```



### 三、业务层（Service）

在整合Mybatis时，我们书写了sql语句的调用接口和对应的Mapper文件。

但在实际开发中，我们不能在controller控制层去直接调用Dao接口，应在service层中去实现Dao接口对应的操作。因为在具体工程中，service承担大部分功能，还包含除开CURD的操作，所以整合完Mybatis，可以先将对应的数据库操作方法在service中实例化。

###### interface:

```java
public interface EmployeeService {

    //员工列表方法
    List<Employee> lists();

    //保存员工信息
    void save(Employee employee);

    //根据id查询一个
    Employee findById(Integer id);

    //更新员工信息
    void update(Employee employee);

    //删除员工信息
    void delete(Integer id);
}
```

```java
public interface UserService {

    //注册用户
    void register(User user);

    //用户登录
    User login(String username, String password);
}
```

###### Impl:

impl实现其service接口，并置于Dao实现类，以便后期实现各种功能。此处impl包含功能具体实现将在后面对应项目实现模块逐步进行实现讲解。

```java
@Service
@Transactional
public class EmployeeServiceImpl  implements  EmployeeService{

    private EmployeeDao employeeDao;

    @Autowired
    public EmployeeServiceImpl(EmployeeDao employeeDao) {
        this.employeeDao = employeeDao;
    }

}
```

```java
@Service
@Transactional
public class UserServiceImpl  implements UserService{

    private UserDao userDao;

    @Autowired
    public UserServiceImpl(UserDao userDao) {
        this.userDao = userDao;
    }

}
```

注解记忆强化：

`@Autowired`

自动导入对象到类中，被注入进的类同样要被 Spring 容器管理比如此处：Dao实现类注入到service类中。

`@Service` :

对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。

`@Transactional` 

是声明式事务管理编程中使用的注解

添加位置:

1）接口实现类或接口实现方法上，而不是接口类中。
2）访问权限：public 的方法才起作用。@Transactional 注解应该只被应用到 public 方法上，这是由 Spring AOP 的本质决定的。
系统设计：将标签放置在需要进行事务管理的方法上，而不是放在所有接口实现类上：只读的接口就不需要事务管理，由于配置了@Transactional就需要AOP拦截及事务的处理，可能影响系统性能，<font color=#zky886>所以此处不应该对service进行事务管理，要具体到特定的方法不然影响性能</font>。



### 四、用户注册

用户注册需要从前端提交表单申请到后台进行处理，然后将数据提交至数据库中。所以流程为：

- <font color=#zky886>前端</font>页面提交注册表单到相应的映射给对应映射的<font color=#zky886>controller</font>方法
- <font color=#zky886>controller</font>层对应方法接收表单提交的信息，执行调用<font color=#zky886>service</font>方法
- <font color=#zky886>service</font>的方法被调用，连带调用<font color=#zky886>Dao</font>层的数据库相关方法，实现与数据库的联动操作

##### 1、前端页面核心代码

从登录页面跳转：

```html
<a href="/register">还没有账号,立即注册</a>
```

注册页面（简化保留关键html代码）：

```html
<!--表单--> 
<form th:action="@{/user/register}" method="post">
<table cellpadding="0" cellspacing="0" border="0"
class="form_table"> 
    
<!--用户名--> 
<input type="text" class="inputgri" name="username" />

<!--真实姓名--> 
<input type="text" class="inputgri" name="realname" />    

<!--密码-->  
<input type="password" class="inputgri" name="password" />    
  
<!--性别选项--> 
男
<input type="radio" class="inputgri" name="gender" value="1" checked="checked"/>
女
<input type="radio" class="inputgri" name="gender" value="0"/>
    
    
<!--提交按钮--> 
<input type="submit" class="button" value="立即注册 &raquo;" />

</table>    
</form>
```

![image-20210919173118948](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210919173118948.png)

对应的表单如上，三个输入栏，一个双选栏

- action属性的作用

  form标签中有一个action属性，该属性的作用是：
  提交表单后，将表单数据发送到指定位置“@{/user/register}”，即传到对应controller中映射的(user/register)方法中去。



##### 2、controller层

```java
@Controller
@RequestMapping("/user")
public class UserController {

    private static final Logger log = LoggerFactory.getLogger(UserController.class);
    private UserService userService;

    @Autowired
    public UserController(UserService userService) {
        this.userService = userService;
    }

    /**
     * 用户注册
     */
    @RequestMapping("/register")
    public String register(User user){
        log.debug("用户名: {},真实姓名: {},密码: {},性别: {},",user.getUsername(),user.getRealname(),user.getPassword(),user.getGender());

        try {
            //注册用户
            userService.register(user);
        } catch (RuntimeException e) {
            e.printStackTrace();
            return "redirect:/register"; //注册失败回到注册
        }
        return  "redirect:/login";  //注册成功跳转到登录
    }
}
```

- private static final Logger log = LoggerFactory.getLogger(UserController.class);这是一个日志类，用来记录传入信息和执行情况
- 接收到从前端传来的user,调用service层的register(注册方法)
- `redirect:/xxx`代表重定向，即跳转到指定xxx页面



##### 3、service层

```java
public class UserServiceImpl  implements UserService{

    private UserDao userDao;
    
    @Autowired
    public UserServiceImpl(UserDao userDao) {
        this.userDao = userDao;
    }
    
    @Override
    public void register(User user) {
        //1.根据用户名查询数据库中是否存在改用户
        User userDB = userDao.findByUserName(user.getUsername());
        //2.判断用户是否存在
        if(!ObjectUtils.isEmpty(userDB)) throw new RuntimeException("当前用户名已被注册!");
        //3.注册用户&明文的密码加密  特点: 相同字符串多次使用md5就行加密 加密结果始终是一致
        String newPassword = DigestUtils.md5DigestAsHex(user.getPassword().getBytes(StandardCharsets.UTF_8));
        user.setPassword(newPassword);
        userDao.save(user);
    }
}
```

- 当调用register时，应当传入一个新的user(比如来自前端表单提交的user)
- 先判断用户是否存在，调用userDao，根据输入的用户名进行查询，若不存在则继续
- 对密码进行加密，加密后存入user中
- 调用Dao的save方法将user存入数据库



### 五、用户登录

用户登录与注册原理相似，简单说。

##### 1、前端页面核心代码

```html
<!--表单--> 
<form th:action="@{/user/login}" method="post">
<table cellpadding="0" cellspacing="0" border="0" class="form_table">
    
<!--用户名--> 
<p>
用户名:  
<input type="text" class="inputgri" name="username" />
</p>
							
<!--密码--> 
<p>
密码:
<input type="password" class="inputgri" name="password" />
</p>
    
<!--提交按钮--> 
<p>
<input type="submit" class="button" value="点我登录 &raquo;" />
&nbsp;&nbsp;
</p>
    
<!--注册超链接--> 
<p>
<a href="/register">还没有账号,立即注册</a>
</p>
    
</table>    
</form>
```

![image-20210919205124756](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210919205124756.png)

如上图所示，两个输入栏和一个登录按钮（表单提交按钮）

- th:action="@{/user/login}"：即传入controller中对应映射（/user/login）的方法



##### 2、Controller层

```java
@Controller
@RequestMapping("user")
public class UserController {

    private static final Logger log = LoggerFactory.getLogger(UserController.class);

    private UserService userService;

    @Autowired
    public UserController(UserService userService) {
        this.userService = userService;
    }

    /**
     * 用户登录
     */
    @RequestMapping("login")
    public String login(String username,String password,HttpSession session){
        log.debug("本次登录用户名: {}",username);
        log.debug("本地登录密码: {}",password);
        try {
            //1.调用业务层进行登录
            User user = userService.login(username,password);
            //2.保存用户信息
            session.setAttribute("user",user);
        } catch (Exception e) {
            e.printStackTrace();
            return "redirect:/login";//登录失败回到登录界面
        }
        return "redirect:/employee/lists";//登录成功之后,跳转到查询所有员工信息控制器路径
    }

}
```

- 调用业务层进行登录
- 调用session通过setAttribute保存用户登录信息
- 重定向跳转相应的页面



##### 3、Service层

```java
@Service
@Transactional
public class UserServiceImpl  implements UserService{

    private UserDao userDao;

    @Autowired
    public UserServiceImpl(UserDao userDao) {
        this.userDao = userDao;
    }

    @Override//xiaochen  123 ====>     xiaochen   xxed
    public User login(String username, String password) {
        //1.根据用户名查询用户
        User user = userDao.findByUserName(username);
        if(ObjectUtils.isEmpty(user)) throw new RuntimeException("用户名不正确!");
        //2.比较密码
        String passwordSecret = DigestUtils.md5DigestAsHex(password.getBytes(StandardCharsets.UTF_8));
        if(!user.getPassword().equals(passwordSecret)) throw new RuntimeException("密码输入错误!");
        return user;
    }

}
```

- 根据controller传入的用户类的用户名查询用户
- 对密码进行解码，比较对应用户名的密码



### 六、员工展示

要获取员工列表信息要经过如下逻辑步骤：

- 链接到展示前端页面，向controller层获取请求
- controller层调用service层获取员工列表(List)
- service层调用Dao方法获取列表至controller层
- controller层将List传至前端，前端通过th:each进行遍历

##### 1、Controller层

```java
@Controller
@RequestMapping("employee")
public class EmployeeController {

    private static final Logger log = LoggerFactory.getLogger(EmployeeController.class);
    private EmployeeService employeeService;

    @Value("${photo.file.dir}")
    private String realpath;

    @Autowired
    public EmployeeController(EmployeeService employeeService) {
        this.employeeService = employeeService;
    }

    /**
     * 员工列表
     **/
    @RequestMapping("lists")
    public String lists(Model model) {
        log.debug("查询所有员工信息");
        List<Employee> employeeList = employeeService.lists();
        model.addAttribute("employeeList", employeeList);
        return "emplist";
    }
}
```

- 调用service.lists方法获取员工列表
- 调用Model向前端对应页面传入员工列表



##### 2、Service层

```java
@Service
@Transactional
public class EmployeeServiceImpl  implements  EmployeeService{

    private EmployeeDao employeeDao;

    @Autowired
    public EmployeeServiceImpl(EmployeeDao employeeDao) {
        this.employeeDao = employeeDao;
    }

    @Override
    public List<Employee> lists() {
        return employeeDao.lists();
    }
}
```

- 收到controller的调用后，调用Dao的lists方法获取相应的值



##### 3、前端页面核心代码

```html
<!--用户名显示--> 
欢迎
<span th:if="${session.user!=null}" th:text="${session.user.username}"></span>
<span th:if="${session.user==null}" >游客</span>!

<table class="table">
<tr class="table_header">
    
<!--列表属性名-->
<td>
								编号
</td>
<td>
								姓名
</td>
<td>
								头像
</td>
<td>
								工资
</td>
<td>
								生日
</td>
<td>
								操作
</td>
</tr>
    
    
<!--逐步行显示员工信息-->
<tr th:each="employee,state:${employeeList}"  th:class="${state.odd?'row1':'row2'}">
<td>
<span th:text="${employee.id}"></span>
</td>
<td>
<span th:text="${employee.name}"></span>
</td>
<td>
<img th:src="@{/}+${employee.photo}" width="60">
</td>
<td>
<span th:text="${employee.salary}"></span>
</td>
<td>
<span th:text="${#dates.format(employee.birthday,'yyyy/MM/dd')}"></span>
</td>
    
<!--更新+删除操作（后续功能实现进行解释）-->  
<td>
<a href="javascript:;" th:onclick="'deleteEmployee('+${employee.id}+')'">删除</a>
<a th:href="@{/employee/detail(id=${employee.id})}">更新</a>
</td>
</tr>
```

- 用户名显示：th:if 为thymeleaf的判断语句，若不满足"${xxx}"的判断，则不执行该标签语句
- Thymeleaf通过`${}`来获取model中的变量
- 逐行显示员工信息：`th:each="employee,state:${employeeList}"  `代表从模板上下文获取employeeList变量，用employee作为装载个体



### 七、员工添加

##### 1、前端页面核心代码

```html
<form th:action="@{/employee/save}" method="post" enctype="multipart/form-data">
<table cellpadding="0" cellspacing="0" border="0" class="form_table">
    
							<tr>
								<td valign="middle" align="right">
									姓名:
								</td>
								<td valign="middle" align="left">
									<input type="text" class="inputgri" name="name" />
								</td>
							</tr>
    
							<tr>
								<td valign="middle" align="right">
									头像:
								</td>
								<td valign="middle" align="left">
									<input type="file" width="" name="img" />
								</td>
							</tr>
    
							<tr>
								<td valign="middle" align="right">
									工资:
								</td>
								<td valign="middle" align="left">
									<input type="text" class="inputgri" name="salary" />
								</td>
							</tr>
    
							<tr>
								<td valign="middle" align="right">
									生日:
								</td>
								<td valign="middle" align="left">
									<input type="text" class="inputgri" name="birthday" />
								</td>
							</tr>
    
						</table>
						<p>
							<input type="submit" class="button" value="确认添加" />
							<input type="submit" class="button" value="返回列表" />
						</p>
					</form>
```

- 同员工注册，为表单提交
- 提交相关数据到Controller层"@{/employee/save}"对应的方法



##### 2、Controller层

```java
@Controller
@RequestMapping("employee")
public class EmployeeController {

    private static final Logger log = LoggerFactory.getLogger(EmployeeController.class);
    private EmployeeService employeeService;

    @Value("${photo.file.dir}")
    private String realpath;

    @Autowired
    public EmployeeController(EmployeeService employeeService) {
        this.employeeService = employeeService;
    }


    //上传头像方法
    private String uploadPhoto(MultipartFile img, String originalFilename) throws IOException {
        String fileNamePrefix = new SimpleDateFormat("yyyyMMddHHmmssSSS").format(new Date());
        String fileNameSuffix = originalFilename.substring(originalFilename.lastIndexOf("."));
        String newFileName = fileNamePrefix + fileNameSuffix;
        img.transferTo(new File(realpath, newFileName));
        return newFileName;
    }


    /**
     * 保存员工信息
     * 文件上传: 1.表单提交方式必须是post  2.表单enctype属性必须为 multipart/form-data
     * @return
     */
    @RequestMapping("save")
    public String save(Employee employee, MultipartFile img) throws IOException {
        log.debug("姓名:{}, 薪资:{}, 生日:{} ", employee.getName(), employee.getSalary(), employee.getBirthday());
        String originalFilename = img.getOriginalFilename();
        log.debug("头像名称: {}", originalFilename);
        log.debug("头像大小: {}", img.getSize());
        log.debug("上传的路径: {}", realpath);
        //1.处理头像的上传&修改文件名称
        String newFileName = uploadPhoto(img, originalFilename);
        //2.保存员工信息
        employee.setPhoto(newFileName);//保存头像名字
        employeeService.save(employee);
        return "redirect:/employee/lists";//保存成功跳转到列表页面
    }

}
```

- 收到前端页面提交的表单，调用service层save方法
- sevice层调用dao中save方法进行保存



##### 3、Service层

```java
@Service
@Transactional
public class EmployeeServiceImpl  implements  EmployeeService{

    private EmployeeDao employeeDao;

    @Autowired
    public EmployeeServiceImpl(EmployeeDao employeeDao) {
        this.employeeDao = employeeDao;
    }

    @Override
    public void save(Employee employee) {
        employeeDao.save(employee);
    }
}
```



### 结语

后续的员工更新与删除无非就是表单提交到controller,controller调用service的方法，然后service类中的dao方法被调用达成数据库的连接，执行mysql数据库的更新操作。这也是CRUD的基本操作，所以到这里就结束啦。想要源码的朋友就自行提取（源码来自编程不良人）

链接：https://pan.baidu.com/s/1aCOl6ZtX09kDv5sfsXEKKA 
提取码：KKKK