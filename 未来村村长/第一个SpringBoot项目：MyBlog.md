## 第一个SpringBoot项目：狂神教程

### 一、静态资源的放置

1、resources下的public、resources、static三个文件夹下的静态资源可通过文件名直接访问

- 访问优先级：resources>static（架构默认文件夹）>public

2、若在资源文件下自定义了spring.mvc.static-path-pattern路径，则上面三个文件夹皆失效

3、在templates中的所有页面只能通过Controller跳转，若无模板引擎，默认解析的是.jsp文件



### 二、DIY首页和图标

![image-20210912205819755](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210912205819755.png)

##### 1、DIY首页

`WebMvcAutoConfiguration.java包含了所有自动配置的信息，助于理解架构`

```java
	@Bean
		@ConditionalOnMissingBean(name = DispatcherServlet.FLASH_MAP_MANAGER_BEAN_NAME)
		public FlashMapManager flashMapManager() {
			return super.flashMapManager();
		}

		private Resource getWelcomePage() {
			for (String location : this.resourceProperties.getStaticLocations()) {
				Resource indexHtml = getIndexHtml(location);
				if (indexHtml != null) {
					return indexHtml;
				}
			}
			ServletContext servletContext = getServletContext();
			if (servletContext != null) {
				return getIndexHtml(new ServletContextResource(servletContext, SERVLET_LOCATION));
			}
			return null;
		}

		private Resource getIndexHtml(String location) {
			return getIndexHtml(this.resourceLoader.getResource(location));
		}

		private Resource getIndexHtml(Resource location) {
			try {
				Resource resource = location.createRelative("index.html");
				if (resource.exists() && (resource.getURL() != null)) {
					return resource;
				}
			}
			catch (Exception ex) {
			}
			return null;
		}
```

通过WebMvcAutoConfiguration.java源码可知，首页默认为index.html文件。可在resources下的静态资源文件夹中建立同名文件，即可在首页面看到该文件内容。

##### 2、DIY图标

。可在resources下的静态资源文件夹中建立favican.ico文件，即可在标签栏看到该内容

![image-20210912210002109](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210912210002109.png)

### 三、Thymeleaf模板引擎的使用

```xml
        <dependency>
            <groupId>org.thymeleaf</groupId>
            <artifactId>thymeleaf-spring5</artifactId>
        </dependency>

        <dependency>
            <groupId>org.thymeleaf.extras</groupId>
            <artifactId>thymeleaf-extras-java8time</artifactId>
        </dependency>
```

因为templates目录下的所有页面只能通过conteoller跳转，所以需要跳转的页面放置在该目录下。由于Controller默认解析.jsp文件，所以需要导入模板引擎依赖（导入即可使用）。

##### 1、th标签的使用

所有的html的标签元素都能被thymeleaf接管

```html
<h1 th:text="${name}"></h1>
```

```java
    @RequestMapping("index")
    public String index(Model model){
        model.addAttribute("name","zky");
        return "index";
    }
```

如此便可通过Model传值

##### 2、thymeleaf语法小结？

```html
<h1>123<span th:text="${name}"></span>456</h1>
    
<h1 th:each="name:${names}">[[${name}]]</h1>
```

```java
    @RequestMapping("index")
    public String index(Model model){
        model.addAttribute("name","zky");
        model.addAttribute("names", Arrays.asList("pl","zky"));
        return "index";
    }
```



### 四、扩展SpringMVC方法

需要添加`@confiiguration`注释和实现`WebMvcConfigurer`接口

例如：修改视图控制器跳转

```java
import org.springframework.context.annotation.Configuration;
        import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
        import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class ExtraMvcConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/ppp").setViewName("index");
    }
}
```



### 五、bootstrap模板的Thymeleaf拓展使用

```html
<!--①增加引擎-->
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">

<!--②本地链接标签添加th:，并改为"@{}"形式-->
<link th:href="@{/css/bootstrap.min.css}" rel="stylesheet">
<link th:href="@{/css/signin.css}" rel="stylesheet">
<img class="mb-4" th:src="@{/img/bootstrap-solid.svg}" alt="" width="72" height="72">
```

所有页面的静态资源都需要thymeleaf接管

使用@符号，相当于从项目的根目录开始访问

### 六、中英文页面切换

1、先将项目编码改为utf-8

![image-20210918155429668](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210918155429668.png)

2、增添多语言配置文件

![image-20210918162035172](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210918162035172.png)

![image-20210918162114304](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210918162114304.png)



![image-20210918162124295](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210918162124295.png)

3、配置文件thymeleaf式导入html文件

```html
			<img class="mb-4" th:src="@{/img/bootstrap-solid.svg}" alt="" width="72" height="72">
			<h1 class="h3 mb-3 font-weight-normal" th:text="#{login.tip}">Please sign in</h1>
			<input type="text" class="form-control" th:placeholder="#{login.username}" required="" autofocus="">
			<input type="password" class="form-control" th:placeholder="#{login.password}" required="">
			<div class="checkbox mb-3">
				<label>
          <input type="checkbox" value="remember-me"> [[#{login.rember}]]
        </label>
```

4、为按钮配置链接传入值

```html
<a class="btn btn-sm" th:href="@{/index.html(lang='zh_CN')}">中文</a>
<a class="btn btn-sm" th:href="@{/index.html(lang='zh_US')}">English</a>
```

5、配置LocaleResolver组件，并在配置类通过@Bean添加到Spring容器中

```java
public class MylocaleResolver implements LocaleResolver {

    //解析请求
    @Override
    public Locale resolveLocale(HttpServletRequest httpServletRequest) {

        String language = httpServletRequest.getParameter("lang");
        Locale locale = Locale.getDefault();//如果没有则使用默认
        if(!StringUtils.isEmpty(language)){
           String[] split = language.split("_");
           locale = new Locale(split[0],split[1]);
        }
        return locale;
    }

    @Override
    public void setLocale(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Locale locale) {

    }
}
```

```java
@Configuration
public class ExtraMvcConfig implements WebMvcConfigurer {

        @Override
        public void addViewControllers(ViewControllerRegistry registry) {
                registry.addViewController("/").setViewName("index");
                registry.addViewController("/index.html").setViewName("index");
        }

        @Bean
        public LocaleResolver localeResolver(){
                return new MylocaleResolver();
        }
}
```

### 七、登录功能实现

1、关键html

```html
<!--密码验证失败提醒-->
<p style="color:#ffa594" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></p>


<!--登录提交表单-->
<input type="text" name="username" class="form-control" th:placeholder="#{login.username}" required="" autofocus="">
<input type="password" name="password" class="form-control" th:placeholder="#{login.password}" required="">
```

2、登录控制层

```java
@Controller
public class LoginController {

    @RequestMapping("/user/login")
    public String login(@RequestParam("username") String username,
                        @RequestParam("password")String password,
                        Model model){
        if("admin".equals(username) && "123456".equals(password)){
            return "redirect:/main";
        }else {
            model.addAttribute("msg","用户或密码错误");
        }

        return "index";
    }
}
```

3、设置跳转信息重定向

```java
registry.addViewController("/main").setViewName("dashboard");
//addViewController添加重命名
//setViewName对应的html后缀文件
```



### 八、登录拦截器

1、在配置类中添加拦截

```java
@Override
public void addInterceptors(InterceptorRegistry registry) {
                registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**").excludePathPatterns("/css/**","/","/index","/index.html","/img/**","/user/login","/js/**","/img/**");
        }
```

2、自写拦截器

```java
public class LoginHandlerInterceptor implements HandlerInterceptor {
    
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        Object loginUser = request.getSession().getAttribute("loginUser");

        if(loginUser==null){
            request.setAttribute("msg","无权限，请先登录");
            request.getRequestDispatcher("/index").forward(request,response);
            return false;
        }
            //true为放行，false为不放行
        return true;

    }
}
```

九、











