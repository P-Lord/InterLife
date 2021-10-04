## javaWeb其三：大战javaWeb中的CS，Cookie与Session详解



### 复习项目的建立流程

先复习一下javaWeb项目的建立的流程与准备工作。

小插曲—当idea中的Maven窗口消失时：点击菜单栏Help->Find Action(Ctrl+Shift+A),输入Maven projects，然后点击：![image-20210522184216362](C:\Users\官二的磊子\AppData\Roaming\Typora\typora-user-images\image-20210522184216362.png)

① 建立一个maven-webapp项目：记得勾选Creat from archetype

<img src="C:\Users\官二的磊子\AppData\Roaming\Typora\typora-user-images\image-20210522183430657.png" alt="image-20210522183430657" style="zoom:67%;" />

② 在main文件夹建立java和resources文件夹，并标记

![image-20210522185104496](C:\Users\官二的磊子\AppData\Roaming\Typora\typora-user-images\image-20210522185104496.png)



③ 在pom.xml文件中配置依赖dependencies

```xml
  <dependencies>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
    </dependency>
  </dependencies>
```

④ 修改web.xml为最新的

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
</web-app>
```

⑤ 配置tomcat，修改Deployment

<img src="C:\Users\官二的磊子\AppData\Roaming\Typora\typora-user-images\image-20210522184548072.png" alt="image-20210522184548072" style="zoom:67%;" />



### 一、会话与会话技术：Cookie与Session概念



#### 1、cookie

Cookie是会话技术的一种，主要用于将会话过程产生的数据保存到**<font color=#s548ad7>客户端</font>**（浏览器），从而使客户端每次和服务端之间可以更好的进行交互。

Cookie实际上是一小段的文本信息。客户端请求服务器，如果服务器需要记录该用户状态，就使用response向客户端浏览器颁发一个Cookie

客户端浏览器会把Cookie保存起来。当浏览器再请求该网站时，浏览器把请求的网址连同该Cookie一同提交给服务器。服务器检查该Cookie。

<img src="https://img-blog.csdnimg.cn/20200415185849232.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NjY2ODU3,size_16,color_FFFFFF,t_70" alt="资源分配图" style="zoom:67%;" />









#### 2、Session

 Cookie 可以让服务端程序跟踪每个客户端的访问，但是每次客户端的访问都必须传回这些 Cookie，如果 Cookie 很多，这无形地增加了客户端与服务端的数据传输量，而 Session 的出现正是为了解决这个问题。

同一个客户端每次和服务端交互时，不需要每次都传回所有的 Cookie 值，而是只要传回一个 ID，这个 ID 是客户端第一次访问服务器的时候生成的，而且每个客户端是唯一的。这样每个客户端就有了一个唯一的 ID，客户端只要传回这个 ID 就行了，这个 ID 通常是 NANE 为 JSESIONID 的一个 Cookie。

当访问服务器否个网页的时候，会在**<font color=#s548ad7>服务器端</font>**的内存里开辟一块内存，这块内存就叫做session，而这个内存是跟浏览器关联在一起的。这个浏览器指的是浏览器窗口，或者是浏览器的子窗口，意思就是，只允许当前这个session对应的浏览器访问，就算是在同一个机器上新启的浏览器也是无法访问的。而另外一个浏览器也需要记录session的话，就会再启一个属于自己的session。



#### 3、Cookie与Session

##### 1、理解

假设用户访问网页的这个过程比喻为用户访问超市

- <font color=#56565982s59sx6>Cookie就是一张会员卡</font>，每次消费以后，由店员在会员卡上填写消费记录或者积分等信息。

- 而Session是基于Cookie的，意思是当用户消费访问超市时，不需要在卡上写东西了，只需要<font color=#56565982s59sx6>将会员卡的ID</font>告诉店员，店员通过该ID在自己的电脑上去记录相关的信息



##### 2、Session和Cookie的区别？

1、数据存储位置：cookie数据存放在客户的浏览器上，session数据放在服务器上。

2、安全性：cookie不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗，考虑到安全应当使用session。

3、服务器性能：session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能，考虑到减轻服务器性能方面，应当使用cookie。

4、数据大小：单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie。

5、信息重要程度：可以考虑将登陆信息等重要信息存放为session，其他信息如果需要保留，可以放在cookie中。

6、应用场景：

- 登录网站，今输入用户名密码登录了，第二天再打开很多情况下就直接打开了。这个时候用到的一个机制就是cookie。
- session一个场景是购物车，添加了商品之后客户端处可以知道添加了哪些商品，而服务器端如何判别呢，所以也需要存储一些信息就用到了session。



### 二、Cookie使用详解

`cookie使用相对较少`

cookie一般会保存在本地的用户目录下

#### 1、Cookie方法介绍

```java
//cookie的建立
Cookie cookie = new Cookie("访问时间",""+System.currentTimeMillis());//设立name与value
//获取Cookie的名字
cookie.getName();
//获取Cookie的值
cookie.getValue();
//设置Cookie的有效期
cookie.setMaxAge(24*60*60);//有效期为1天
//添加Cookie：客户端去响应Cookie
resp.addCookie(cookie);
//获取服务器的Cookie
Cookie[] cookies = req.getCookies();
```



#### 2、关于记录访问时间的servlet

```java
import javax.servlet.ServletException;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.Date;

public class Cookiedemo1 extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //解决乱码
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");
        PrintWriter writer = resp.getWriter();
        //获取Cookie
        Cookie[] cookies = req.getCookies();
        //判断有没有会员卡
        if(cookies != null){
            writer.println("你上一次访问的时间是：");
            for(int i = 0;i<cookies.length;i++){
                Cookie cookie = cookies[i];
                //获取cookie的名字
                if(cookie.getName().equals("访问时间")){
                    //获取cookie的值
                    long lastLoginTime = Long.parseLong(cookie.getValue());
                    Date date = new Date(lastLoginTime);
                    writer.write(date.toString());
                }
            }
        }else {
            writer.println("第一次访问");
        }
        //给客户端响应一个Cookie：办会员卡
        Cookie cookie = new Cookie("访问时间",""+System.currentTimeMillis());
        cookie.setMaxAge(24*60*60);
        resp.addCookie(cookie);
    }
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req, resp);
    }
}
```

##### 

#### 3、删除Cookie

① 将有效期设置为0

```java
cookie.setMaxAge(24*60*60);
```

② 不设置有效期：关闭浏览器自动失效



### 三、Session<font color=#sazawe4s6s762z>（重点）</font>使用详解

`可实现共享数据，会话之间的数据不能共享`

服务器会给每一个用户（浏览器）创建一个Session，一个Session独占一个浏览器,一个Session对应一个ID,所以需要创建一个Cookie对象，并设置其有效时间。这样当用户关闭浏览器，重新打开浏览器访问该页面时，服务器也能找到之前为用户创建的Session对象。

#### 1、Session方法介绍

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



#### 2、创建Session

```java
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

public class Sessiondemo extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        //解决乱码问题
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");
        //设置浏览器响应字符格式
        resp.setContentType("text/html;charset=utf-8");
        //获取Session
        HttpSession session = req.getSession();
        //给Session储存信息
        session.setAttribute("name","pl");
        //获取Session的ID
        String id = session.getId();
        //判断Session是不到新创建的
        if (!session.isNew()){
            resp.getWriter().write("session创建成功，ID="+id);
        }else {
            resp.getWriter().write("session已经存在，ID="+id);
        }

    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}

```

在另外一个页面（servlet）获取Session

```java
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

public class Sessiondemo2 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //解决乱码问题
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");
        //设置浏览器响应字符格式
        resp.setContentType("text/html;charset=utf-8");
        //获取Session
        HttpSession session = req.getSession();
        //获取Session的信息
        String name =(String) session.getAttribute("name");
        System.out.println(name);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```



#### 3、注销Session

① 使用invalidata方法

```java
session.invalidate();
```

② 手动注销Session

在web.xml中设置Session默认的注销时间

```xml
<!--设置自动注销时间为60分钟-->   
<session-config>
   <session-timeout>60</session-timeout>
</session-config>
```



### 四、Cookie和Session的区别

原文链接：https://blog.csdn.net/xiaokang123456kao/article/details/77098191

##### 1 .存取方式的不同

Cookie中只能保管ASCII字符串，假如需求存取Unicode字符或者二进制数据，需求先进行编码。Cookie中也不能直接存取Java对象。若要存储略微复杂的信息，运用Cookie是比拟艰难的。
而Session中能够存取任何类型的数据，包括而不限于String、Integer、List、Map等。Session中也能够直接保管Java Bean乃至任何Java类，对象等，运用起来十分便当。能够把Session看做是一个Java容器类。

##### 2 .隐私策略的不同

Cookie存储在客户端阅读器中，对客户端是可见的，客户端的一些程序可能会窥探、复制以至修正Cookie中的内容。而Session存储在服务器上，对客户端是透明的，不存在敏感信息泄露的风险。
假如选用Cookie，比较好的方法是，敏感的信息如账号密码等尽量不要写到Cookie中。最好是像Google、Baidu那样将Cookie信息加密，提交到服务器后再进行解密，保证Cookie中的信息只要本人能读得懂。而假如选择Session就省事多了，反正是放在服务器上，Session里任何隐私都能够有效的保护。

##### 3.有效期上的不同

使用过Google的人都晓得，假如登录过Google，则Google的登录信息长期有效。用户不用每次访问都重新登录，Google会持久地记载该用户的登录信息。要到达这种效果，运用Cookie会是比较好的选择。只需要设置Cookie的过期时间属性为一个很大很大的数字。

由于Session依赖于名为JSESSIONID的Cookie，而Cookie JSESSIONID的过期时间默许为–1，只需关闭了阅读器该Session就会失效，因而Session不能完成信息永世有效的效果。运用URL地址重写也不能完成。而且假如设置Session的超时时间过长，服务器累计的Session就会越多，越容易招致内存溢出。

##### 4.服务器压力的不同

Session是保管在服务器端的，每个用户都会产生一个Session。假如并发访问的用户十分多，会产生十分多的Session，耗费大量的内存。因而像Google、Baidu、Sina这样并发访问量极高的网站，是不太可能运用Session来追踪客户会话的。

而Cookie保管在客户端，不占用服务器资源。假如并发阅读的用户十分多，Cookie是很好的选择。关于Google、Baidu、Sina来说，Cookie或许是唯一的选择。

##### 5 .浏览器支持的不同

Cookie是需要客户端浏览器支持的。假如客户端禁用了Cookie，或者不支持Cookie，则会话跟踪会失效。关于WAP上的应用，常规的Cookie就派不上用场了。

假如客户端浏览器不支持Cookie，需要运用Session以及URL地址重写。需要注意的是一切的用到Session程序的URL都要进行URL地址重写，否则Session会话跟踪还会失效。关于WAP应用来说，Session+URL地址重写或许是它唯一的选择。

假如客户端支持Cookie，则Cookie既能够设为本浏览器窗口以及子窗口内有效（把过期时间设为–1），也能够设为一切阅读器窗口内有效（把过期时间设为某个大于0的整数）。但Session只能在本阅读器窗口以及其子窗口内有效。假如两个浏览器窗口互不相干，它们将运用两个不同的Session。（IE8下不同窗口Session相干）

##### 6.跨域支持上的不同

Cookie可以通过如下方式实现跨域名访问，例如将domain属性设置为“.xxx.com”，则“a.xxx.com”和“b.xxx.com”均能够访问该Cookie。事实上只要是以”.xxx.com”结尾的域名均可访问。跨域名Cookie如今被普遍用在网络中，例如Google、Baidu、Sina等。而Session则不会支持跨域名访问。Session仅在他所在的域名内有效。

仅运用Cookie或者仅运用Session可能完成不了理想的效果。这时应该尝试一下同时运用Cookie与Session。Cookie与Session的搭配运用在实践项目中会完成很多意想不到的效果。


















