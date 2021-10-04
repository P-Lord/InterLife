## 怒肝一个月！！！从整合SSM开始到项目的搭建：带你全面学习SpringBoot

### 一、分解回顾ssm的使用

#### 1、回顾mybatis的使用

![MyBatis logo](https://img-blog.csdnimg.cn/img_convert/689bcaab3313a792be1b4a9c66c808ad.png)

往期文章：https://blog.csdn.net/apple_51976307/article/details/117907965

##### 1.1 一个基础的Mybatis项目的建立需要如下步骤

![image-20210714163112047](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210714163112047.png)

###### ① 导入架包，配置环境

此处是在pom.xml中通过maven仓库导入mybatis架包，然后进行非resources读取过滤的配置。

###### ② 在java文件夹中建立pojo

建立与所连接数据库的表对应的实体类，一个表对应一个实体类（建立列属性以及列属性对应的get和set方法，还有无参构造和实参构造），作为mapper.xml的映射类型。

###### ③ 在java文件夹中建立mapper接口和映射文件

建立mapper接口是建立查询方法，通过mybatis框架的封装，不需要自己书写sql语句，直接在映射文件中书写对应方法的映射语句。

###### ④ 配置mybatis架构文件

该结构文件需要在环境（environment）中配置（dataSource），再dataSource中注入database的内容，还需要再mappers中添加书写的mapper映射文件。

##### 1.2 mybatis的使用流程

要调用CURD方法，首先得在mapper接口中书写对应的方法和在mapper映射文件中书写对应的sql语句。

调用的流程为：

① SqlSessionBuilder通过流导入mybatis的架构文件去创建SqlSessionFactory。

② 每次调用CURD方法时，通过调用SqlSessionFactory去创建一个SqlSession。

③ 通过SqlSession针对不同的Mapper接口去创建对应的UserMapper对象，通过其对象去调用CURD方法。

#### 2、回顾spring的使用

![image-20210714221523498](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210714221523498.png)

往期文章：https://blog.csdn.net/apple_51976307/article/details/118058195

##### 2.1 IOC详解

###### 2.1.1 概念理解

`IOC即控制反转，将开发中设计的类交给容器去控制，在一个类需要调用另外一个类时，只需要调用另外一个类在容器中的名字即可获得其实例对象`

![image-20210714214424621](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210714214424621.png)

###### 2.1.2 一个基础的Spring容器使用需要如下步骤

![image-20210714215349877](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210714215349877.png)

**① 准备好设计的类**

SpringIOC起到一个仓库调度员的作用，将类传入仓库中，在仓库中完成类属性的赋值，然后调用时将仓库中的类通过context容器拿出去。

**② 在resources中建立bean仓库配置文件**

这个“仓库”由Spring的配置文件完成封装，即在配置文件中去存放映射对应的类，在该文件中进行对类属性值的注入。

**③ 通过调用context容器获取该类**

当想要创建一个类的实例时，无需new一个对象，只需要针对对应的仓库（bean配置文件）通过获取一个ApplicationContext的对象，该对象通过getBean方法和其对应在仓库中的id去获取所需要的实例对象。

###### 2.1.3 Spring容器的使用流程

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-2YjxRgAk-1624102967072)(C:\Users\官二的磊子\Desktop\未来村村长\image-20210615161607934.png)]](https://img-blog.csdnimg.cn/20210619194457284.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FwcGxlXzUxOTc2MzA3,size_16,color_FFFFFF,t_70)

① java类通过bean配置文件去创建对象存放在Spring容器中

② 通过ApplicationContext去进行对象获取的调度

##### 2.2 AOP略解

`Spring提供面向切面编程，简单来说就是分离业务逻辑与系统服务。比如分离业务处理和事务，比如分离业务处理和日志`

#### 3、回顾spring-mvc的使用

![image-20210714221500054](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210714221500054.png)

往期文章：https://blog.csdn.net/apple_51976307/article/details/118278365

##### 3.1 一个基础的spring-mvc框架建立需要如下步骤

![image-20210715115336227](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210715115336227.png)

###### ① 搭建好Web环境

SpringMVC是基于Servlet的轻量级的用于开发Web项目的MVC框架，所以搭建该框架首先需要给项目添加Web环境。

###### ② 在Web.xml注册DispatcherServlet（前端控制器）

普通的javaWeb是在Web.xml中建立Servlet映射，在Spring中只需要给前端控制器建立映射即可，之后的请求与接收由前端控制器来调度。

###### ③ 在resource下新建springmvc架构文件

在底层springmvc架构文件封装了处理器映射器、处理器适配器、视图解析器。在实际开发中处理映射器（HandlerMapping）和处理适配器（HandlerAdapter）自动完成配置。只需要配置视图解析器。

###### ④ 编写操作业务Controller用于返回ModelAndView

创建Controller，即向Model添加属性和值，返回视图，交给视图解析器拼接，然后前端控制器进行渲染展示。

##### 3.2 一个基础的SpringMVC架构的运行经历如下流程

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-lmXEYUBV-1624792134446)(C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210626154444291.png)]](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210626154444291.png)

如图所示，我们只需要完成前端控制器、视图解析器、控制器的编写即可，其它皆由SpringMVC框架封装完成。



### 二、自上而下分析ssm的搭建

#### 1、从需求入手



#### 2、从框架运行流程入手



#### 3、从文件内容入手