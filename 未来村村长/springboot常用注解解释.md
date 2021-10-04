## springboot常用注解解释

#### 1、成为可被扫描装配的组件

- `@Component` ：通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用`@Component` 注解标注。
- `@Repository` : 对应持久层即 Dao 层，主要用于数据库相关操作。
- `@Service` : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- `@Controller` : 对应 Spring MVC 控制层，主要用户接受用户请求并调用 Service 层返回数据给前端页面。

解析：我们一般使用 `@Autowired` 注解让 Spring 容器帮我们自动装配 bean。要想把类标识成可用于 `@Autowired` 注解自动装配的 bean 的类,可以采用以下注解实现：



#### 2、赋值

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





#### 3、控制器

`@RestController`

`@RestController`注解是`@Controller和`@`ResponseBody`的合集,表示这是个控制器 bean,并且是将函数的返回值直接填入HTTP响应体中,是REST风格的控制器。

现在都是前后端分离，说实话我已经很久没有用过`@Controller`。如果你的项目太老了的话，就当我没说。

单独使用 `@Controller` 不加 `@ResponseBody`的话一般使用在要返回一个**视图**的情况，这种情况属于比较传统的 Spring MVC 的应用，对应于前后端不分离的情况。`@Controller` +`@ResponseBody` 返回 JSON 或 XML 形式数据

即

@Controller返回一个页面

@RestController返回json或xml形式数据

@Controller +@ResponseBody返回JSON 或 XML 形式数据



#### 4、主类

 `@SpringBootApplication`

我们可以把 `@SpringBootApplication`看作是 `@Configuration`、`@EnableAutoConfiguration`、`@ComponentScan` 注解的集合。

根据 SpringBoot 官网，这三个注解的作用分别是：

- `@EnableAutoConfiguration`：启用 SpringBoot 的自动配置机制
- `@ComponentScan`：扫描被`@Component` (`@Service`,`@Controller`)注解的 bean，注解默认会扫描该类所在的包下所有的类。
- `@Configuration`：允许在 Spring 上下文中注册额外的 bean 或导入其他配置类



#### 5、自动导入对象到类中

`@Autowired`

自动导入对象到类中，被注入进的类同样要被 Spring 容器管理比如：Service 类注入到 Controller 类中。

我们一般使用 `@Autowired` 注解让 Spring 容器帮我们自动装配 bean。要想把类标识成可用于 `@Autowired` 注解自动装配的 bean 的类,可以采用以下注解实现：

- `@Component` ：通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用`@Component` 注解标注。
- `@Repository` : 对应持久层即 Dao 层，主要用于数据库相关操作。
- `@Service` : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- `@Controller` : 对应 Spring MVC 控制层，主要用户接受用户请求并调用 Service 层返回数据给前端页面。



#### 6、Mybatis相关

`@Mapper`：为dao接口添加注释

`@MapperScan`：为xxxApplication启动类指定Mapper映射文件的位置，若Mapper存储在src/main/resources/mapper/xxxMapper.xml则不需要加该注解