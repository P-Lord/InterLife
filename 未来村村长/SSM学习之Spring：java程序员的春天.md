## SSM学习之Spring：java程序员的春天

![Spring](C:\Users\官二的磊子\Desktop\未来村村长\图片\spring-logo-9146a4d3298760c2e7e49595184e1975.svg)

官网：https://spring.io

官方下载地址：http://repo.spring.io/release/org/springframework/spring

GitHub：https://github.com/spring-projects/spring-framework

maven：spring-webmvc&spring-jdbc

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.0.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.0.RELEASE</version>
</dependency>
```



### 一、Spring介绍

`Spring是一个轻量级实现控制反转（IOC）和面向切面编程（AOP）的框架`

#### 1、概念介绍

**Spring框架**：是一个[开放源代码](https://baike.baidu.com/item/开放源代码/114160)的[J2EE](https://baike.baidu.com/item/J2EE/110838)应用程序框架，由[Rod Johnson](https://baike.baidu.com/item/Rod Johnson/1423612)发起，是针对bean的生命周期进行管理的轻量级容器（lightweight container）。 Spring解决了开发者在J2EE开发中遇到的许多常见的问题，提供了功能强大IOC、[AOP](https://baike.baidu.com/item/AOP/1332219)及Web MVC等功能。

**IOC**：控制反转——Spring通过一种称作控制反转（[IoC](https://baike.baidu.com/item/IoC/4853)）的技术促进了低耦合。控制反转意味着在系统开发过程中，设计的类将交由容器去控制，而不是在类的内部去控制，类与类之间的关系将交由容器处理，一个类在需要调用另一个类时，只要调用另一个类在容器中注册的名字就可以得到这个类的实例，与传统的编程方式有了很大的不同，“不用你找，我来提供给你”，这就是控制反转的含义 。

**AOP**：面向切面——Spring提供了[面向切面编程](https://baike.baidu.com/item/面向切面编程)的丰富支持，允许通过分离应用的业务逻辑与系统级服务（例如审计（auditing）和[事务](https://baike.baidu.com/item/事务)（[transaction](https://baike.baidu.com/item/transaction)）管理）进行[内聚性](https://baike.baidu.com/item/内聚性)的开发。[应用对象](https://baike.baidu.com/item/应用对象)只实现它们应该做的——完成业务逻辑——仅此而已。它们并不负责（甚至是意识）其它的系统级关注点，例如日志或事务支持。

#### 2、Spring框架组成

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019102923475419.png)

1、核心容器：核心容器提供 Spring 框架的基本功能(Spring Core)。

2、Spring 上下文：Spring 上下文是一个[配置文件](https://baike.baidu.com/item/配置文件)，向 Spring框架提供上下文信息

3、Spring AOP：通过配置管理特性，Spring AOP 模块直接将面向切面的编程功能集成到了 Spring 框架中。

4、Spring DAO：JDBC[DAO](https://baike.baidu.com/item/DAO)抽象层提供了有意义的异常层次结构，可用该结构来管理[异常处理](https://baike.baidu.com/item/异常处理)和不同数据库供应商抛出的错误消息

5、Spring ORM：负责框架中[对象关系映射](https://baike.baidu.com/item/对象关系映射/311152)，提供相关ORM 接入框架的关系对象管理工具。

6、Spring Web 模块：Web 上下文模块建立在应用程序上下文模块之上，为基于 Web 的应用程序提供了上下文

7、Spring MVC 框架：[MVC](https://baike.baidu.com/item/MVC)框架是一个全功能的构建 Web应用程序的 MVC 实现。



### 二、IOC

`降低系统的耦合性，更加专注于业务的实现`

#### 1、思想解释

<font color=#plzky999>控制反转是一种通过第三方(XML或注释)去生产或获取特定对象的方式。在Spring中实现控制反转的是IOC容器，实现方法是依赖注入(DI)。</font>

![img](https://images0.cnblogs.com/blog/281227/201305/30130748-488045b61d354b019a088b9cb7fc2d73.png)

软件系统在没有引入IOC容器之前，如图1所示，对象A依赖于对象B，那么对象A在初始化或者运行到某一点的时候，自己必须主动去创建对象B或者使用已经创建的对象B。无论是创建还是使用对象B，控制权都在自己手上。

![img](https://images0.cnblogs.com/blog/281227/201305/30131727-a8268fe6370049028078e6b8a1cbc88f.png)

软件系统在引入IOC容器之后，这种情形就完全改变了，如图3所示，由于IOC容器的加入，对象A与对象B之间失去了直接联系，所以，当对象A运行到需要对象B的时候，IOC容器会主动创建一个对象B注入到对象A需要的地方。

![img](https://images0.cnblogs.com/blog/281227/201305/30132051-58562511cc6541fdb2636081e455d808.png)

通过前后的对比，我们不难看出来：对象A获得依赖对象B的过程,由主动行为变为了被动行为，控制权颠倒过来了，这就是“控制反转”这个名称的由来。

#### 2、HelloWorld的IOC实现

##### ① 写一个类

```java
package pojo;

public class HelloWorld {
    private String str;
    public String getStr() {
        return str;
    }
    public void setStr(String str) {
        this.str = str;
    }
    @Override
    public String toString() {
        return "Hello{ "
                +"str="
                + str +" }";
    }
}
```

##### ② 书写配置文件beans：new 这个类

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--使用Spring来创建对象，在Spring这些都称为Bean-->
    <bean id="hello" class="pojo.HelloWorld">
        <property name="str" value="World"/>
    </bean>
</beans>
```

bean等于HelloWorld的实例对象，相当于new了一个HelloWorld。其中

- id为变量名，class是需要new的类，ref是引用创建好的对象

property相当于给对象中的属性设置值

##### ③ 测试类：创建这个类

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import pojo.HelloWorld;

public class MyTest {

    public static void main(String[] args) {
        //获取spring的上下文对象
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //我们的对象现在都在spring中管理，我们要使用，直接去里面取出来
        HelloWorld hello = (HelloWorld) context.getBean("hello");
        System.out.println(hello.toString());
    }

}
```

> ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");

拿到Spring的容器，使用容器去获得（context.getbean("id")）这个类的实例对象。

#### 3、IOC创建对象方式解析

![image-20210615161607934](C:\Users\官二的磊子\Desktop\未来村村长\image-20210615161607934.png)

##### ① 创建一个User类

```java
package pojo;

public class User {

    private String name;
    public User() {
        System.out.println("User的无参构造");
    }

    public User(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public void show(){
        System.out.println("name =" + name);
    }
}
```

##### ② 配置xml文件：通过< bean >-< constructor-arg >给构造器赋值

- 下标赋值

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user" class="pojo.User">
        <constructor-arg index="0" value="pl"/>
    </bean>

</beans>
```

- 类型赋值（不建议使用）：基本类型可以直接用，引用类型必须写全

```xml
    <bean id="user" class="pojo.User">
        <constructor-arg type="java.lang.String" value="pl"/>
    </bean>
```

- name原始方式

```xml
 <bean id="user" class="pojo.User">
        <constructor-arg name="name" value="pl"/>
    </bean>
```

##### ③ 测试类

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import pojo.User;
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        User user = (User) context.getBean("user");
        user.show();
    }
}
```

### 三、Spring配置



#### 1、别名< alias >

```xml
<alias name="user" alias="U1"/>
```

如果添加了别名，也可以使用别名获取对象

```java
User user = (User) context.getBean("U1");
```

#### 2、Bean的配置

**id**：是bean的唯一标识符，相当于对象名

**class**：bean对象所对应的类（全限定名—包名+类型）

**name**：也是别名，可以取多个别名

```xml
<bean id="user" class="pojo.User" name="U1,U2,U3">

</bean>
```

```java
User user = (User) context.getBean("U1");
User user = (User) context.getBean("user");//等效
```

#### 3、import

`一般用于团队开发使用，可将多个配置文件导入合并为一个文件`

```xml
<import resource="bean1.xml"/>
<import resource="bean2.xml"/>
<import resource="bean3.xml"/>
```

导入后相同内容会自动合并



### 四、DI依赖注入

回顾构造器注入方式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
   
<!--通过构造器中下标-->
    <bean id="user" class="pojo.User">
        <constructor-arg index="0" value="pl"/>
    </bean>
    
<!--通过构造器中属性类型-->
     <bean id="user" class="pojo.User">
        <constructor-arg type="java.lang.String" value="pl"/>
    </bean>
<!--通过构造器中属性名-->
     <bean id="user" class="pojo.User">
        <constructor-arg name="name" value="pl"/>
    </bean>
    
</beans>
```



#### 1、环境搭建

**java类**

```java
package pojo;
public class Address {
    private String address;
    /*省略setter、getter、tostring*/
}
```

```java
package pojo;
import java.util.*;
public class Student {
    private String name;
    private Address adderss;
    private String[] books;
    private List<String> hobbies;
    private Map<String,String> card;
    private Set<String> games;
    private  String wife;
    private Properties info;
    /*省略setter、getter、tostring*/
}
```

**beans.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="Student" class="pojo.Student">
    </bean>
</beans>
```

**测试类**

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import pojo.Student;
public class Mytest {
    public static void main(String[] args) {
        //获取仓库
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //获取对象
        Student student = (Student)context.getBean("student");
    }
}
```

#### 2、<font color=plzky999>SETTER注入方式</font>

`注入即是给类的属性赋值，不同的类型赋值方式不同`

##### ① 普通值注入：

```xml
    <bean id="Student" class="pojo.Student">
        <property name="name" value="pl"/>
    </bean>
```

即在beans.xml文件中给bean增加property进行对类的属性赋值

##### ② bean(对象)注入：

```xml
    <bean id="address" class="pojo.Address"/>
    <bean id="Student" class="pojo.Student">
        <property name="adderss" ref="address"/>
    </bean>
```

即给值注入一个bean，即给对象赋值对象

##### ③ 数组注入：

```xml
    <bean id="Student" class="pojo.Student">
        <property name="books">
            <array>
                <value>《金瓶梅》</value>
                <value>《理想国》</value>
                <value>《长安乱》</value>
            </array>
        </property>
    </bean>
```

即给数组类型赋值

##### ③ List注入：

```xml
    <bean id="Student" class="pojo.Student">
        <property name="hobbies">
            <list>
                <value>打篮球</value>
                <value>rap</value>
            </list>
        </property>    
    </bean>
```

即给List类型属性赋值

##### ④ Map注入

```xml
    <bean id="Student" class="pojo.Student">
        <property name="card">
            <map>
                <entry key="学号" value="20191000000"/>
                <entry key="身份证" value="52231213215489723121X"/>
            </map>
        </property>
    </bean>
```

即给Map类型属性赋值

##### ⑤ Set注入

```xml
    <bean id="Student" class="pojo.Student">
        <property name="games">
            <set>
                <value>LOL</value>
                <value>DNF</value>
            </set>
        </property>
    </bean>
```

即给Set类型属性赋值

##### ⑥ 空值注入

```xml
    <bean id="Student" class="pojo.Student">    
		<property name="wife">
            <null/>
        </property>
    </bean>
```

##### ⑦ Properties注入

```xml
    <bean id="Student" class="pojo.Student">    
        <property name="info">
            <props>
                <prop key="学号">201910000</prop>
                <prop key="性别">男</prop>
            </props>
        </property>
    </bean>
```

#### 3、C命名和P命名注入

##### ① 在beans.xml文件中导入这两句

```xml
       xmlns:c="http://www.springframework.org/schema/c"
       xmlns:p="http://www.springframework.org/schema/p"
```

##### ② 具体示例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:c="http://www.springframework.org/schema/c"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    
<!--P命名空间注入，可以直接注入属性的值：property-->
    <bean id="user" class="pojo.User" p:name="pl" p:age="20"/>
<!--C命名空间注入，通过构造器注入：construct-orgs-->
    <bean id="user2" class="pojo.User" c:name="pl" c:age="20"/>
    
</beans>
```



### 五、bean的作用域（scope）

（1）singleton：(Spring默认)单例模式，Spring IoC容器中**只会存在一个共享的Bean实例**，无论有多少个Bean引用它，始终指向同一对象Singleton作用域是Spring中的缺省作用域，也可以显示的将Bean定义为singleton模式，配置为：

```xml
<bean id="teacher" class="dao.Teacher" p:id="3" p:name="赵" scope="singleton"></bean>
```

（2）prototype:原型模式，每次通过Spring容器获取prototype定义的bean时，容器都将**创建一个新的Bean实例**，每个Bean实例都有自己的属性和状态，而singleton全局只有一个对象。根据经验，对有状态的bean使用prototype作用域，而对无状态的bean使用singleton作用域。

```xml
 <bean id="teacher" class="dao.Teacher" p:id="3" p:name="赵老师" scope="prototype"></bean>
```

（3）request：在一次Http请求中，容器会返回该Bean的同一实例。而对不同的Http请求则会产生新的Bean，而且该bean仅在当前Http Request内有效。

（4）session：在一次Http Session中，容器会返回该Bean的同一实例。而对不同的Session请求则会创建新的实例，该bean实例仅在当前Session内有效。

（5）global Session：在一个全局的Http Session中，容器会返回该Bean的同一个实例，仅在使用portlet context时有效。


### 六、bean自动装配（autowire）

`自动装配是Spring满足bean依赖的方式，Spring会在上下文中自动寻找并给bean装配属性`

#### 1、通过autowire设置自动装配

##### ① Byname

byname：会自动在容器上下文中查找，和自己对象set方法后面的值对应的bean的id。

需要保证id的唯一，并且该id和对应的类中set方法的参数名要一致。

```xml
    <bean id="cat" class="Cat"/>
    <bean id="dog" class="Dog"/>

    <bean id="person" class="Person" autowire="byName">
        <property name="name" value="pl"/>
    </bean>
```

##### ② ByType

byname：会自动在容器上下文中查找，和自己对象属性类型相同的bean。

保证该class唯一，并且属性一致。

```xml
    <bean id="cat" class="Cat"/>
    <bean id="dog" class="Dog"/>

    <bean id="person" class="Person" autowire="byType">
        <property name="name" value="pl"/>
    </bean>
```

#### 2、使用注解自动装配

​	**@Autowired**

##### ① 导入约束

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	        https://www.springframework.org/schema/beans/spring-beans.xsd
	        http://www.springframework.org/schema/context
	        https://www.springframework.org/schema/context/spring-context.xsd">
    <!--开启注解-->
    <context:annotation-config/>
    
</beans>
```

##### ② 添加Autowired注解

```java
public class Person {
    @Autowired(required=false)
    private Cat cat;
    @Autowired
    @Qualifier(value="cat111")
    private Dog dog;
}
```

​	==@Autowired==

- 可以在属性上使用，也可以在set方法上使用 。 

- @Autowired(required=false)表示这个对象可以为空。

- 若自动装配的属性在IOC中存在，且符合byType要求。使用Autowired后，可以不编写set方法。

   ==@Qualifier==

- 在Autowired装配的环境比较复杂，可以增加Qualifier去组合使用，通过bean的id指定bean对象。

  **@Resource**

```java
public class Person {
    @Resource
    private Cat cat;
    @Resource
    private Dog dog;
}
```

   ==@Resource==

- 默认通过byName的方式实现，如果找不到名字，则通过byType实现。如果两个都找不到的情况下，就报错。

### 七、使用注解开发

#### 1、准备

- spring-aop架包

- 自动装配需要的约束

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	        https://www.springframework.org/schema/beans/spring-beans.xsd
	        http://www.springframework.org/schema/context
	        https://www.springframework.org/schema/context/spring-context.xsd">
    <!--开启注解-->
    <context:annotation-config/>
    <!--指定要扫描的包，即注解生效的包-->
    <context:component-scan base-package="pojo"/>
</beans>
```

#### 2、类注册注解

##### ① Component

等价于在配置文件中增加 < bean id="person" class="Person" />

```java
package pojo;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;
@Component
@Scope("singleton")
public class Person {
    @Value("pl")
    public String name;
}

```

增加了@Value("pl")后等价于

```xml
<bean id="person" class="Person">
     <properties name="name" value="pl"/>
</bean
```

==@Value==也可以放在set方法上进行注入

增加了@Scope("singleton")等价于开启单例模式

```xml
<bean id="person" class="Person" scope="singleton">
     <properties name="name" value="pl"/>
</bean
```

##### ② 衍生注解

- dao 【@Repository】
- service 【@Service】
- controller 【@Controller】

这三个注解的功能和Component相同，都是代表将某个类注册放到Spring容器中，进行bean的装配

#### 3、自动装配注解

- @Autowired：可组合@Qualifier("xxx")指定bean的id
- @Nullable：标记后该值可为null
- @Resource：先执行byName，再执行byType

#### 4、完全脱离配置文件

##### ① pojo

```java
package pojo;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class Person {
    @Value("pl")
    public String name;
}
```

##### ② 配置类

```java
package config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import pojo.Person;

@Configuration
public class personConfig {
    @Bean
    public Person myPerson(){
        return new Person();
    }
}
```

说明：

1. @Configuration本身配置了@Component注解，被注解的类会作为配置类，等价于之前的beans.xml文件
2. @bean相当于在配置类中注册一个bean
   - 被注解方法的名字相当于bean标签中的id属性
   - 被注解方法的返回值相当于bean标签中的class属性



##### ③ 测试类

```java
import config.personConfig;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import pojo.Person;
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(personConfig.class);
        Person person = (Person) context.getBean("myPerson");
        System.out.println(person.name);
    }
}
```

说明：若使用配置类去实现注解开发，则只能通过AnnotationConfigApplicationContext上下文获取同期，通过配置类的class对象加载

### 八、代理模式理解



#### 1、静态代理

##### ① 事务处理时的对象：

- 业务方(房东)
- 业务代理方(租方中介)
- 业务(租房)
- 业务请求方(客户)

##### ② 好处：

- 使角色分工更加明确
- 便于业务的扩展与管理

##### ③ 静态代理特点/缺点：

- 一个真实角色产生一个代理角色,代码量翻倍，例如日志功能的增加

#### 2、动态代理

`一个动态代理类可以代理多项业务`

##### ① 事务处理时的对象：

- 业务方(房东)
- 业务代理方(租方中介)
- 业务(租房)
- 业务请求方(客户)

##### ② 动态代理特点：

- 代理类是动态生成的
- 分为两大类：
  - 基于接口：JDK动态代理(InvocationHandler)
  - 基于类：cglib
  - 基于java字节码：javasist

##### ③ 案例

1. 业务

   ```java
   public interface Rent {
       public void rent();
   }
   ```

2. 业务方

   ```java
   public class Host implements Rent{
       public void rent() {
           System.out.println("房东要出租房子！");
       }
   }
   ```

3. 代理方(自动生成)

   ```java
   //我们会用这个类，自动生成代理类！
   public class ProxyInvocationHandler implements InvocationHandler {
       //被代理的接口
       private Rent rent;
   
       public void setRent(Rent rent) {
           this.rent = rent;
       }
   
       //生成得到代理类
       public Object getProxy(){
           return Proxy.newProxyInstance(this.getClass().getClassLoader(),
                   rent.getClass().getInterfaces(),this);
       }
   
       //处理代理实例，并返回结果
       public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
           //动态代理的本质，就是使用反射机制实现！
           Object result = method.invoke(rent, args);
           seeHose();
           fee();
           return result;
       }
   
       public void seeHose(){
           System.out.println("中介带着看房子！");
       }
   
       public void fee(){
           System.out.println("中介收取费用！");
       }
   }
   ```

4. 业务请求方

   ```java
   public class Client {
       public static void main(String[] args) {
           //真实角色
           Host host = new Host();
   
           //代理角色：现在没有
           ProxyInvocationHandler pih = new ProxyInvocationHandler();
   
           //通过调用程序处理角色来处理我们要调用的接口对象！
           pih.setRent(host);
           Rent proxy = (Rent) pih.getProxy(); //这里的proxy就是动态生成的，我们并没有写
           proxy.rent();
   
       }
   }
   ```



### 九、AOP实现

`AOP的底层就是动态代理`

#### 准备:导入AOP实现的依赖

```xml
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.6</version>
        </dependency>
```

#### 1、方式一：使用spring的API接口

##### ① 业务层

UserService

```java
package service;

public interface UserService {
    public void add();
    public void delete();
    public void update();
    public void select();
}
```

UserServiceImpl

```java
package service;

public class UserServiceImpl implements UserService {

    @Override
    public void add() {
        System.out.println("增加了一个用户");
    }

    @Override
    public void delete() {
        System.out.println("删除了一个用户");
    }

    @Override
    public void update() {
        System.out.println("更新了一个用户");
    }

    @Override
    public void select() {
        System.out.println("查询了一个用户");
    }
}
```

##### ② 日志层

BeforeLog

```java
package log;
import org.springframework.aop.MethodBeforeAdvice;
import java.lang.reflect.Method;
public class Log implements MethodBeforeAdvice {
    //method：要执行的目标对象的方法
    //orgs：参数
    //target：目标对象
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName() + "的" + method.getName() + "被执行了");
    }
}
```

AfterLog

```java
package log;
import org.springframework.aop.AfterReturningAdvice;
import java.lang.reflect.Method;
public class AfterLog implements AfterReturningAdvice {

    @Override
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
        System.out.println("执行了"+method.getName()+",返回结果为："+returnValue);
    }
}
```

##### ③  代理层（配置）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	        https://www.springframework.org/schema/beans/spring-beans.xsd
	        http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean id="userService" class="service.UserServiceImpl"/>
    <bean id="log" class="log.Log"/>
    <bean id="afterLog" class="log.AfterLog"/>

<!--配置AOP-->
    <aop:config>
<!--切入点-->
        <aop:pointcut id="pointcut" expression="execution(* service.UserServiceImpl.*(..))"/>
<!--执行环绕增强-->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
    </aop:config>

</beans>
```

##### ④ 业务请求层（测试）

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import service.UserService;
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationcontext.xml");
        UserService userService = (UserService) context.getBean("userService");
        userService.add();
    }
}
```

#### 2、方式二：自定义实现AOP

##### ① 自定义切入类

```java
package diy;

public class DiyPointCut {
    public void before(){
        System.out.println("方法执行前");
    }
    public void after(){
        System.out.println("方法执行后");
    }
}
```

##### ② 代理配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	        https://www.springframework.org/schema/beans/spring-beans.xsd
	        http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean id="userService" class="service.UserServiceImpl"/>
    <bean id="log" class="log.Log"/>
    <bean id="afterLog" class="log.AfterLog"/>
<!--方式二-->
    <bean id="diy" class="diy.DiyPointCut"/>
    <aop:config>
<!--自定义切面-->
        <aop:aspect ref="diy">
<!--切入点-->
            <aop:pointcut id="point" expression="execution(* service.*.*(..))"/>
<!--通知-->
            <aop:before method="before" pointcut-ref="point"/>
            <aop:after method="after" pointcut-ref="point"/>
        </aop:aspect>
    </aop:config>
</beans>
```

#### 3、方式三：通过注解

##### ① 自定义切入类

```java
package diy;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
@Aspect
public class DiyPointCut {
    @Before("execution(* service.UserServiceImpl.*(..))")
    public void before(){
        System.out.println("方法执行前");
    }
}
```

##### ② 开启注解配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	        https://www.springframework.org/schema/beans/spring-beans.xsd
	        http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean id="userService" class="service.UserServiceImpl"/>
    <bean id="log" class="log.Log"/>
    <bean id="afterLog" class="log.AfterLog"/>
<!--配置-->
    <bean id="diy" class="diy.DiyPointCut"/>
<!--开启注解支持-->
    <aop:aspectj-autoproxy/>


</beans>
```



### 十、整合Mybatis

#####  导入相关架包

- junit

```xml
		 <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
```

- mybatis

```xml
		<dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
```

- mysql数据库

```xml
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
```

- spring相关

```xml
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
```

- mybatis-spring

```xml
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.2</version>
        </dependency>
```

- spring-jdbc

```xml
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
```

#### 1、方式一：

##### ① 编写相关类

User

```java
package pojo;
import lombok.Data;

@Data
public class User {
    private int id;
    private String name;
    private String pwd;
}
```

UserMapper

```java
package Mapper;

import pojo.User;

import java.util.List;

public interface UserMapper {
    List<User> selectUser();
}
```

UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="Mapper.UserMapper">

    <select id="selectUser" resultType="pojo.User">
        select * from mybatis.user;
    </select>

</mapper>
```

##### UserMapperImpl

```java
package Mapper;
import org.mybatis.spring.SqlSessionTemplate;
import pojo.User;
import java.util.List;

public class UserMapperImpl implements UserMapper{
//    需要使用SqlSessionTemplate
    private SqlSessionTemplate sqlSession;

    public void setSqlSession(SqlSessionTemplate sqlSession){
        this.sqlSession = sqlSession;
    }

    @Override
    public List<User> selectUser() {
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        return mapper.selectUser();
    }
}
```





##### ② 编写配置文件

mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

</configuration>
```

spring-dao.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

<!--Datasource:使用spring的数据源替换mybatis的配置-->
    <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf8"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
    </bean>
    
<!--sqlSessionFactory对象-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="datasource"/>
<!--绑定mybatis-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:Mapper/*.xml"/>
    </bean>
    
<!--SqlSessionTemplate就是sqlSession-->
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg index="0" ref="sqlSessionFactory"/>
    </bean>

</beans>
```

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <import resource="spring-dao.xml"/>

    <!--注入实现类-->
    <bean id="userMapper" class="Mapper.UserMapperImpl">
        <property name="sqlSession" ref="sqlSession"/>
    </bean>

</beans>
```

##### ③ 测试

```java
import Mapper.UserMapper;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import pojo.User;
import java.io.IOException;

public class MyTest {
    @Test
    public void test() throws IOException {

        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserMapper userMapper = context.getBean("userMapper",UserMapper.class);
        for (User user:userMapper.selectUser()){
            System.out.println(user);
        }

    }
}
```



#### 2、方式二：简化版

##### ① Impl变化

```java
package mapper;
import org.mybatis.spring.support.SqlSessionDaoSupport;
import pojo.User;

import java.util.List;

public class UserMapperImpl  extends SqlSessionDaoSupport implements UserMapper{
    @Override
    public List<User> selectUser() {
        return getSqlSession().getMapper(UserMapper.class).selectUser();
    }
}
```

##### ② 配置文件变化

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <import resource="spring-dao.xml"/>

    <bean id="UserMapper" class="mapper.UserMapperImpl">
        <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
    </bean>

</beans>
```

### 十一、spring声明式事务

#### 1、事务概念

事务指逻辑上的一组操作，组成这组操作的各个单元，要不全部成功，要不全部不成功

#### 2、ACID原则

- 原子性：整个事务中的所有操作，要么全部完成，要么全部不完成，不可能停滞在中间某个环节
- 一致性：在事务开始之前和事务结束以后，数据库的完整性约束没有被破坏
- 隔离性：如果有两个事务，运行在相同的时间内，执行 相同的功能，事务的隔离性将确保每一事务在系统中认为只有该事务在使用系统
- 持久性：在事务完成以后，该事务所对数据库所作的更改便持久的保存在数据库之中，并不会被回滚

#### 3、Spring中的事务管理

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx.xsd">

    <import resource="spring-dao.xml"/>

    <bean id="userMapper" class="com.yang.mapper.UserMapperImpl2">
        <property name="sqlSessionTemplate" ref="sqlSession"/>
    </bean>
    <!--配置声明事务注入-->
    <!--要开启 Spring 的事务处理功能，在 Spring 的配置文件中创建一个 DataSourceTransactionManager 对象：-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
        <!--或者使用构造注入-->
        <!--<constructor-arg ref="dataSource" />-->
    </bean>

    <!--结合AOP实现事务的织入-->
    <!--配置事务通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <!--给哪些方法配置事务-->
        <!--配置事务的传播特性 propagation
                PROPAGATION_REQUIRED:如果当前没有事务，就新建一个事务，如果已存在一个事务中，加入到这个事务中，这是最常见的选择。
                PROPAGATION_SUPPORTS:支持当前事务，如果没有当前事务，就以非事务方法执行。
                PROPAGATION_MANDATORY:使用当前事务，如果没有当前事务，就抛出异常。
                PROPAGATION_REQUIRES_NEW:新建事务，如果当前存在事务，把当前事务挂起。
                PROPAGATION_NOT_SUPPORTED:以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
                PROPAGATION_NEVER:以非事务方式执行操作，如果当前事务存在则抛出异常。
                PROPAGATION_NESTED:	如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与PROPAGATION_REQUIRED 类似的操作
        -->
        <tx:attributes>
            <tx:method name="add" propagation="REQUIRED"/>
            <tx:method name="delete" propagation="REQUIRED"/>
            <tx:method name="update" propagation="REQUIRED"/>
            <tx:method name="select" read-only="true"/>
            <!--全部方法-->
            <tx:method name="*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>

    <!--配置事务切入-->
    <aop:config>
    	<!--该包下的所有方法-->
        <aop:pointcut id="txPointCut" expression="execution(* com.yang.mapper.*.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
    </aop:config>
</beans>
```





