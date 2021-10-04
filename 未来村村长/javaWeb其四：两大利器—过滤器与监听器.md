## javaWeb其四：三大利器之二过滤器与监听器



### 一、过滤器：Filter

#### 1、过滤器概念

<img src="C:\Users\官二的磊子\AppData\Roaming\Typora\typora-user-images\image-20210526165401747.png" alt="image-20210526165401747" style="zoom:67%;" />

**概念**：对浏览器的请求或服务器的响应进行拦截，预处理后再发送传达

**基本功能**：对Servlet容器调用Servlet的过程进行拦截，从而在Servlet进行响应处理前后实现一些特殊功能。

**拦截流程**：浏览器请求访问服务器中的目标资源》过滤器进行拦截，进行预处理》将请求发送给目标资源》服务器响应，将响应结果发送给过滤器》过滤器处理发送至客户端

**一般使用**：处理乱码问题，登录验证等



#### 2、过滤器的编写

##### ① 编写步骤

常规：建立Maven-webapp项目>更新web.xml>在pom.xml中导入Servlet包>建立java和resources包>编写Filter实现类(导入的Filter要来自javax.Servlet)>在web.xml中配置Filter映射



##### ② 过滤乱码样例

1. 建立一个类实现javax.servlet.Filter接口，重写doFilter方法:该方法体必须含有 :

> filterChain.doFilter(servletRequest,servletResponse);

​	该方法的作用是让请求传递到下一个过滤器或servlet处理，无该方法则请求或者响应无法继续传递

```java
import javax.servlet.*;
import java.io.IOException;

public class Filter01 implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        servletResponse.setCharacterEncoding("utf-8");
        servletRequest.setCharacterEncoding("utf-8");
        servletResponse.setContentType("text/html;charset=UTF-8");
        filterChain.doFilter(servletRequest,servletResponse);//让请求传递到下一个过滤器或servlet处理

    }
}
```

2. 在Web.xml中配置其映射，决定过滤的资源：配置与servlet类型，名称替换

```xml
    <filter>
        <filter-name>Filter1</filter-name>
        <filter-class>Filter.Filter01</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>Filter1</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

映射配置：

/*：使用该通配符可拦截用户的所有请求或响应

/指定路径：只拦截指定的请求或者响应



#### 3、 Filter链

在一个web应用中，可以编写多个Filter,这些Filter组合起来称之为Filter链。web服务器根据在web.xml中注册顺序决定调用先后顺序。在spring过滤器中，有很多过滤器是不用自己写的，已经给提供好了的，比如：字符编码的过滤器。



### 二、监听器

推荐文章链接：https://blog.csdn.net/lkforce/article/details/81781286

#### 1、监听器概念



**监听器理解**：对事件进行监听，并对该事件做出处理和响应。类似GUI中的监听器。



**Servlet规范中的8种监听器**：

![image-20210526194858501](C:\Users\官二的磊子\Desktop\未来村村长\image-20210526194858501.png)

**8种监听器分为三类**：

（1）监听Context、Request、Session对象的创建和销毁，需要在web.xml中配置

> ServletContextListener
>
> ServletRequestListener
>
> HttpSessionListener

（2）监听Context、Request、Session对象属性的变化，需要在web.xml中配置

> ServletContextAttributeListener
>
> ServletRequestAttributeListener
>
> HttpSessionAttributeListener

（3）监听Session内部的对象，不需要再web.xml中配置

> HttpSessionActivationListener
>
> HttpSessionBindingListener



#### 2、监听器的创建案例

1. 创建一个类实现ServletRequestAttributeListener接口

   ```java
   import javax.servlet.http.HttpSessionEvent;
   import javax.servlet.http.HttpSessionListener;
    
   public class MyHttpSessionListener implements HttpSessionListener{
    
            /**
             * 新增session
             */
            @Override
            public void sessionCreated(HttpSessionEvent arg0) {
                     
                      System.out.println("MyHttpSessionListener Created,sessionId:"+arg0.getSession().getId());
                     
                      Integer count=null;//session数量
                      Object sessionCount=arg0.getSession().getServletContext().getAttribute("sessionCount");
                      if(sessionCount==null){
                               count=0;
                      }else{
                               count=Integer.valueOf(sessionCount.toString());
                      }
                      count++;
                      System.out.println("当前session数："+count.toString());
                      arg0.getSession().getServletContext().setAttribute("sessionCount", count);
                      arg0.getSession().setAttribute("testName", "this is a name");
            }
    
            /**
             * 销毁session
             */
            @Override
            public void sessionDestroyed(HttpSessionEvent arg0) {
                      System.out.println("MyHttpSessionListener Destory,sessionId:"+arg0.getSession().getId());
                     
                      Integer count=null;//session数量
                      Object sessionCount=arg0.getSession().getServletContext().getAttribute("sessionCount");
                      if(sessionCount==null){
                               count=0;
                      }else{
                               count=Integer.valueOf(sessionCount.toString());
                      }
                      count--;
                      System.out.println("当前session数："+count.toString());
                      arg0.getSession().getServletContext().setAttribute("sessionCount", count);
                     
            }
   ```

2. 在web.xml种配置监听器

   ```xml
   <listener>
    
       <listener-class>com.web.system.listener.MyHttpSessionListener</listener-class>
    
   </listener>
   ```