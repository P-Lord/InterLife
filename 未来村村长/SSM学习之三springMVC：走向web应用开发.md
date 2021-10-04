## SSM学习之三springMVC：走向web应用开发

<font color=#999plzky>Model-模型</font>:提供供展示的数据和行为，即数据Dao与服务行为Service,提供了数据模型与数据操作。

<font color=#99sdofm6+qw+8asd89a8d9z8x9dad89wd89qdqd>View-视图</font>：模式的展示，用户界面。

<font color=#plzky999>Controller-控制器-Servlet</font>：控制三个环节：接收用户请求，交给模型处理，返回到视图。

<img src="C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210626104824613.png" alt="image-20210626104824613" style="zoom:67%;" />

### 一、回顾Servlet

##### ① 搭建web环境

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>SpringMvc</artifactId>
        <groupId>org.example</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>SpringMvc-01-servlet</artifactId>
    <dependencies>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>jsp-api</artifactId>
        <version>2.2</version>
    </dependency>
    </dependencies>
</project>

    <build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
    </build>

```



![image-20210626111418683](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210626111418683.png)



##### ② 写一个Servlet类

```java
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1、获取前端参数
        //2、调用业务层
        //3、视图转发或者重定向
    }
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doPost(req, resp);
    }
}
```

##### ③ 配置servlet

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    
    <servlet>
        <servlet-name>hello</servlet-name>
        <servlet-class>HelloServlet</servlet-class>
    </servlet>
    
    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
    
    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
    
    
</web-app>
```

##### ④ 配置tomcat,导入项目架包

![image-20210626111702326](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210626111702326.png)

### 二、SpringMvc入门

#### 1、简介

![image-20210626112039305](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210626112039305.png)

很多人用的基于Servlet的轻量级的功能强大的可以整合Spring和Mybatis的用于开发Web项目的MVC框架。

#### 2、第一个SpringMVC程序

##### ① 新建普通项目，添加Web支持

##### ② 在Web.xml中注册DispatcherServlet

```xml
 <!--1.注册DispatcherServlet-->
   <servlet>
       <servlet-name>springmvc</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--关联一个springmvc的配置文件:【servlet-name】-servlet.xml-->
       <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springmvc-servlet.xml</param-value>
       </init-param>
       <!--启动级别-1-->
       <load-on-startup>1</load-on-startup>
   </servlet>

   <!--/ 匹配所有的请求；（不包括.jsp）-->
   <!--/* 匹配所有的请求；（包括.jsp）-->
   <servlet-mapping>
       <servlet-name>springmvc</servlet-name>
       <url-pattern>/</url-pattern>
   </servlet-mapping>
```

##### ③ 在resources下新建springmvc-servlet.xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--处理器映射器-->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>

    <!--处理器适配器-->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>

    <!--视图解析器:DispatcherServlet给他的ModelAndView-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>
```

##### ④ 编写操作业务Controller用于返回ModelAndView：实现接口/添加注解

```java
package controller;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
//注意：这里我们先导入Controller接口
public class HelloController implements Controller {
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        //ModelAndView 模型和视图
        ModelAndView mv = new ModelAndView();
        //封装对象，放在ModelAndView中。Model
        mv.addObject("msg","HelloSpringMVC!");
        //封装要跳转的视图，放在ModelAndView中
        mv.setViewName("hello"); //: /WEB-INF/jsp/hello.jsp
        return mv;
    }
}
```

##### ⑤ 在SpringIOC中注册Controller的bean

```xml
    <!--Handler-->
    <bean id="/hello" class="controller.HelloController"/>
```

##### ⑥ 写要跳转的页面

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

${msg}

</body>
</html>
```

##### ⑦ 启动Tomcat进行测试

在Artifacts导入架包，再启动tomcat

<img src="C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210626120401114.png" alt="image-20210626120401114" style="zoom:67%;" />

<img src="C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210626144539921.png" alt="image-20210626144539921" style="zoom:67%;" />

#### 3、Servlet执行原理

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201119092856360.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L051bWJlcl9vbmVFbmdpbmVlcg==,size_16,color_FFFFFF,t_70#pic_center)

1. 发起请求到前端控制器(DispatcherServlet)
2. 前端控制器请求HandlerMapping查找 Handler，可以根据xml配置、注解进行查找
3. 处理器映射器HandlerMapping向前端控制器返回Handler
4. 前端控制器调用处理器适配器去执行Handler
5. 处理器适配器去执行Handler
6. Handler执行完成给适配器返回ModelAndView
7. 处理器适配器向前端控制器返回ModelAndView，ModelAndView是springmvc框架的一个底层对象，包括 Model和view
8. 前端控制器请求视图解析器去进行视图解析，根据逻辑视图名解析成真正的视图(jsp)
9. 视图解析器向前端控制器返回View
10. 前端控制器进行视图渲染，视图渲染将模型数据(在ModelAndView对象中)填充到request域
11. 前端控制器向用户响应结果

![image-20210626154444291](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210626154444291.png)

主要的：书写Controller类【👉注册SpringIOC👉配置Web.xml】

我们做的：

- Controller层调用业务层
- 设置View视图返回的名字



#### 4、Servlet深入理解

==web.xml==

##### ① 前端控制器

```xml
<!--1.注册DispatcherServlet-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--关联一个springmvc的配置文件:【servlet-name】-servlet.xml-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <!--启动级别-1-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!--/ 匹配所有的请求；（不包括.jsp）-->
    <!--/* 匹配所有的请求；（包括.jsp）-->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
```

在init-param标签中关联配置文件

```xml
<init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
```



==springmvc-servlet.xml==

##### ② 处理映射器（HandlerMapping）和处理适配器（HandlerAdapter）

`真实开发自动配置`

```xml
    <!--处理器映射器-->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
    <!--处理器适配器-->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
```

<hr></hr>

`以上都不用我们做,以下是我们要写的`

<hr></hr>

==Controller.class==

##### ③ Handler(Controller)

```java
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

//注意：这里我们先导入Controller接口
public class HelloController implements Controller {
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        //1、创建ModelAndView
        ModelAndView mv = new ModelAndView();
        //2、业务代码
        mv.addObject("xxx","yyy");
        //3、视图跳转
        mv.setViewName("xxx");
        //4、返回mv
        return mv;
    }
}
```



==springmvc-servlet.xml==

##### ④ 视图解析器（ViewResolver）

`拼接视图的名字`

```xml
 <!--视图解析器:DispatcherServlet给他的ModelAndView-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>
```

##### ⑤ 注册Handler

```xml
  <!--Handler-->
    <bean id="/hello" class="controller.HelloController"/>
```



### 三、使用注解开发SpringMVC

##### 准备：view

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
${msg}
</body>
</html>
```



##### ① 配置web.xml文件

<img src="C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210626163407392.png" alt="image-20210626163407392" style="zoom:67%;" />

<img src="C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210626163621914.png" alt="image-20210626163621914" style="zoom:50%;" />



```xml
<!--1.注册DispatcherServlet-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--关联一个springmvc的配置文件:【servlet-name】-servlet.xml-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <!--启动级别-1-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!--/ 匹配所有的请求；（不包括.jsp）-->
    <!--/* 匹配所有的请求；（包括.jsp）-->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
```

##### ② 配置springmvc-servlet.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">
    
    <context:component-scan base-package="controller"/>
    <mvc:default-servlet-handler />
    <mvc:annotation-driven />

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp" />
    </bean>

</beans>
```

注解版

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
    <context:component-scan base-package="controller"/>
    <!-- 让Spring MVC不处理静态资源 -->
    <mvc:default-servlet-handler />
    <!--
    支持mvc注解驱动
        在spring中一般采用@RequestMapping注解来完成映射关系
        要想使@RequestMapping注解生效
        必须向上下文中注册DefaultAnnotationHandlerMapping
        和一个AnnotationMethodHandlerAdapter实例
        这两个实例分别在类级别和方法级别处理。
        而annotation-driven配置帮助我们自动完成上述两个实例的注入。
     -->
    <mvc:annotation-driven />

    <!-- 视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!-- 前缀 -->
        <property name="prefix" value="/WEB-INF/jsp/" />
        <!-- 后缀 -->
        <property name="suffix" value=".jsp" />
    </bean>

</beans>
```

##### ③ 创建Controller

```java
package controller;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HelloController {

    //处理url请求：真实访问地址
    @RequestMapping("/hello")
    public String hello(Model model){
        //封装数据：向Model中添加属性和值，在jsp页面取出并渲染
        model.addAttribute("msg","Hello,SpringMvcAnnotation!");
        //返回视图：视图解析器拼接，返回给前端控制器
        return "hello";
    }
}

```

javaWeb阶段是写多个servlet，此处只用在该类中增加方法

```java
//对比
package controller;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
@Controller
@RequestMapping("/hello")
public class HelloController {
    //url请求://localhost:8080/hello/h1
    @RequestMapping("/h1")
    public String hello(Model model){
        //封装数据
        model.addAttribute("msg","Hello,SpringMvcAnnotation!");
        //返回视图
        return "hello";
    }  
}
```

##### ④ 配置Tomcat并运行

### 四、注解配置Controller和RequestMapping

`controller:处理请求返回模型与视图`

如果直接继承Controller的话，一个控制器只有一个方法，需要定义多个控制器，所以使用注解开发，使用注解开发就不需要每次都在SpringIOC注册Controller的bean

```java
@Controller
public class HelloController {

    //处理url请求：真实访问地址
    @RequestMapping("/hello")
    public String hello(Model model){
        //封装数据：向Model中添加属性和值，在jsp页面取出并渲染
        model.addAttribute("msg","Hello,SpringMvcAnnotation!");
        //返回视图：视图解析器拼接，返回给前端控制器
        return "hello";
    }
}
```

##### ==使用@Controller==

- 在springIOC中开启：代表这个类被Spring接管

- 如果该类返回值为String，并有具体页面可以跳转，则会被视图解析器解析

- return的值是对应需要跳转的页面的名字

- ```xml
  <context:component-scan base-package="controller"/>
  ```

##### ==使用@RequestMapping("/xxx")==

- 用于映射url到控制器类或一个特定的处理程序方法

- /xxx:代表请求访问的真实路径，如果在类上注解，则访问方法的路径在类路径之后

RequestMapping源码

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Mapping
public @interface RequestMapping {
    String name() default "";
    @AliasFor("path")
    String[] value() default {};
    @AliasFor("value")
    String[] path() default {};
    RequestMethod[] method() default {};
    String[] params() default {};
    String[] headers() default {};
    String[] consumes() default {};
    String[] produces() default {};
}
```

**请求方式限定**

DELETE方式

```java
@RequestMapping(name="/h1",method=RequestMethod.DELETE)
```

```java
@DELETEMapping("/h1")//与上同理
```

Get方式

```xml
@RequestMapping(path="/h1",method=RequestMethod.GET)
```

```java
@GetMapping("/h1")//与上同理
```

同理还有

```java
@PostMapping
@PutMapping
@PatchMapping
```



### 五、RestFul风格

原代码

```java
package controller;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HelloController {
    @RequestMapping("/h1")
    public String hello(int a,int b,Model model){
        int res = a + b;
        //封装数据
        model.addAttribute("msg","结果为 :"+res);
        //返回视图
        return "jsp/hello";
    }

}
```

> 访问方式：http://localhost:8080/h1?a=1&b=1



==增加注解@PathVariable==

```java
package controller;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HelloController {
    @RequestMapping("/h1/{a}/{b}")
    public String hello(@PathVariable int a,@PathVariable int b, Model model){
        int res = a + b;
        //封装数据
        model.addAttribute("msg","结果为 :"+res);
        //返回视图
        return "jsp/hello";
    }
}
```

> 访问方式:http://localhost:8080/h1/1/24

/1/24：表示给a和b赋值，注意注解位置是在每一个参数前面加

如果在该方式基础上限定提交方式的不同，则可以通过不同的请求方式实现不同的效果，例如可以实现请求地址相同但功能不同。

==小黄鸭测试法：跟小黄鸭梳理自己的问题==

### 六、重定向与转发

不用视图解析器:则需要加上前缀后后缀

@ResponseBody加了这个注解则不会走视图解析器

##### /或者forward:/   表示转发

```java
@Controller
public class HelloController {
    @RequestMapping("/h1")
    public String hello(Model model){
        model.addAttribute("msg","hello");
        return "forward:/WEB-INF/jsp/hello";
    }
}
```

##### forward:/  表示重定向

```java
@Controller
public class HelloController {

    @RequestMapping("/h1")
    public String hello(Model model){
        model.addAttribute("msg","hello");
        //返回视图
        return "redirect:/index.jsp";
    }
}
```

在有视图解析器的情况下，可以直接像如上方式那样实现重定向，但不能像如上实现转发，会发生拼接异常



### 七、请求与处理

#### 1、接收参数

##### ① 书写一个pojo类（导入lombok架包）

```java
package pojo;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private int id;
    private String name;
    private int age;
}
```

##### ② 实现请求的处理与响应

1.@RequestParam("username")：使用该注解，表示该参数必须从前端获取，获取形式必须为：localhost:8080/user/test?username=xxx;

```java
@Controller
@RequestMapping("/user")
public class UserController {

    @GetMapping("/test")
    public String hello(@RequestParam("username") String name, Model model){
        //1、接收前端参数：localhost:8080/user/test?username=xxx;
        System.out.println("接收到前端参数："+name);
        //2、将返回结果传递前端
        model.addAttribute("msg",name);
        //3、视图跳转
        return "jsp/hello";
    }
}
```



2.也可以使用之前的RestFul风格方式接收

```java
@Controller
@RequestMapping("/user")
public class UserController {

    @GetMapping("/test/{name}")
    public String hello(@PathVariable String name, Model model){
        //1、接收前端参数：localhost:8080/user/test/xxx;
        System.out.println("接收到前端参数："+name);
        //2、将返回结果传递前端
        model.addAttribute("msg",name);
        //3、视图跳转
        return "jsp/hello";
    }
}
```



3.若针对对象User，则直接传递一个User的对象

- 传递的为一个对象，则会匹配User对象中的字段名：使用字段名字需要保存一致

- > 访问方式：http://localhost:8080/user/test?name=pl&id=1&age=3

```java
@Controller
@RequestMapping("/user")
public class UserController {

    @GetMapping("/test")
    public String hello(User user, Model model){
        //1、接收前端参数
        System.out.println("接收到前端参数："+user);
        //2、将返回结果传递前端
        model.addAttribute("msg",user.toString());
        //3、视图跳转
        return "jsp/hello";
    }
}
```

#### 2、返回参数

①通过ModelAndView：可以进行设置返回逻辑视图，显示控制展示层的跳转

②通过Model：简化了操作，适合新手

③通过ModelMap：继承了LinkedHashMap，提升了灵活性



#### 3、过滤器解决乱码

方法一:

`不用手写，直接在Web.xml中配置`

```xml
<filter>
   <filter-name>encoding</filter-name>
   <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
   <init-param>
       <param-name>encoding</param-name>
       <param-value>utf-8</param-value>
   </init-param>
</filter>
<filter-mapping>
   <filter-name>encoding</filter-name>
   <url-pattern>/*</url-pattern>
</filter-mapping>
```

方法二：

修改Tomcat/conf/server.xml文件的编码

```xml
<Connector URIEncoding="utf-8" port="8080" protocol="HTTP/1.1"
          connectionTimeout="20000"
          redirectPort="8443" />
```

方法三：

自定义过滤器

```java
import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletRequestWrapper;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.util.Map;

/**
* 解决get和post请求 全部乱码的过滤器
*/
public class GenericEncodingFilter implements Filter {

   @Override
   public void destroy() {
  }

   @Override
   public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
       //处理response的字符编码
       HttpServletResponse myResponse=(HttpServletResponse) response;
       myResponse.setContentType("text/html;charset=UTF-8");

       // 转型为与协议相关对象
       HttpServletRequest httpServletRequest = (HttpServletRequest) request;
       // 对request包装增强
       HttpServletRequest myrequest = new MyRequest(httpServletRequest);
       chain.doFilter(myrequest, response);
  }

   @Override
   public void init(FilterConfig filterConfig) throws ServletException {
  }

}
//自定义request对象，HttpServletRequest的包装类
class MyRequest extends HttpServletRequestWrapper {

   private HttpServletRequest request;
   //是否编码的标记
   private boolean hasEncode;
   //定义一个可以传入HttpServletRequest对象的构造函数，以便对其进行装饰
   public MyRequest(HttpServletRequest request) {
       super(request);// super必须写
       this.request = request;
  }
   // 对需要增强方法 进行覆盖
   @Override
   public Map getParameterMap() {
       // 先获得请求方式
       String method = request.getMethod();
       if (method.equalsIgnoreCase("post")) {
           // post请求
           try {
               // 处理post乱码
               request.setCharacterEncoding("utf-8");
               return request.getParameterMap();
          } catch (UnsupportedEncodingException e) {
               e.printStackTrace();
          }
      } else if (method.equalsIgnoreCase("get")) {
           // get请求
           Map<String, String[]> parameterMap = request.getParameterMap();
           if (!hasEncode) { // 确保get手动编码逻辑只运行一次
               for (String parameterName : parameterMap.keySet()) {
                   String[] values = parameterMap.get(parameterName);
                   if (values != null) {
                       for (int i = 0; i < values.length; i++) {
                           try {
                               // 处理get乱码
                               values[i] = new String(values[i]
                                      .getBytes("ISO-8859-1"), "utf-8");
                          } catch (UnsupportedEncodingException e) {
                               e.printStackTrace();
                          }
                      }
                  }
              }
               hasEncode = true;
          }
           return parameterMap;
      }
       return super.getParameterMap();
  }
   //取一个值
   @Override
   public String getParameter(String name) {
       Map<String, String[]> parameterMap = getParameterMap();
       String[] values = parameterMap.get(name);
       if (values == null) {
           return null;
      }
       return values[0]; // 取回参数的第一个值
  }
   //取所有值
   @Override
   public String[] getParameterValues(String name) {
       Map<String, String[]> parameterMap = getParameterMap();
       String[] values = parameterMap.get(name);
       return values;
  }
}
```

记得在web.xml配置

```xml
<filter>name calss
<filter-mapping> name url-parrtern
```

















