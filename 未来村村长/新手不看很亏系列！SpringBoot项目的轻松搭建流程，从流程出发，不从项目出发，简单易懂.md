## 新手不看很亏系列，还搞不懂项目搭建？SpringBoot项目的轻松搭建流程，从流程出发，不从项目出发，简单易懂

刚学完SpringBoot的朋友们都想要快速上手一个小项目，这里呢提供了一个看完就能自己写小项目的文章。会根据新手对于一个项目的搭建遇到的问题进行讲解，<font color=#sad5487qwedasdz>通过解决问题的方式去说明项目的搭建，而不是从项目本身下手，希望能够为大家解决入门的小问题。</font>

首先文章遇到的技术栈主要是SpringBoot和Vue，会使用到Mybatis-plus，element-ui，echarts，后面提到的技术不需要懂也能看懂文章，这是一个前后端分离项目的搭建讲解，前端只需要懂一点点Vue语法，后续都会有详细介绍。

参考的视频是来自B站Up主“**楠哥教你学java**”，大家也可以去B站看他的视频，笔者也在学习阶段，懒得自己重新写一个项目了，后续再分享自己写的项目源码。

项目的构建流程就是，前端的搭建、后端的搭建、前后端的数据传递，补充echarts的使用(感觉挺好用的，就补充以下，不需要可以不看)。



### 一、前端的搭建

前端的搭建用的是Vue-cli脚手架，然后代码复制的element-ui的组件，后续用的图表来自echarts，网站如下

https://element.eleme.cn/#/zh-CN/component/installation

https://echarts.apache.org/zh/index.html

新手一般遇到的问题有如下：



#### 1、Vue项目框架怎么搭建，需要哪些插件，怎么配置并使用？

##### 【1】准备工作【已经下载的就不用下载了】

###### ① 下载Node.js

http://nodejs.cn/download/

查看版本号：

```cmd
node -v
nmp -v
```



###### ② 下载镜像(下载淘宝镜像)

```cmd
npm install cnpm -g
```



###### ③ 安装Vue-cli

```cmd
cnpm install vue-cli -g
```



###### ④ 安装vue-cli3.0脚手架(这样才能用可视化工具)

```cmd
npm install -g @vue/cli
```



###### ⑤ 在idea中安装Vue插件（settings中的plugins）



##### 【2】利用Vue可视化工具搭建项目框架

###### ① Vue可视化

```cmd
vue ui
```



###### ② 创建新项目

根据需求安装，比较傻瓜式

【Vue2】label、router、vuex选中即可



###### ③ 安装插件

element-ui、axios、Echarts





##### 【3】最终main.js配置

```js
import Vue from 'vue'
import './plugins/axios'
import App from './App.vue'
import router from './router'
import store from './store'
import './plugins/element.js'
import echarts from 'echarts'


Vue.prototype.$echarts = echarts
Vue.config.productionTip = false

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')
```



#### 2、前端页面怎么写，或者怎么复制使用，搭建的流程是什么？



##### 【1】文件目录解读

怎么复制使用，我们可以先来分析目录文件的组成。

![image-20210924190813674](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210924190813674.png)

整个项目，我们只用看src里面的文件

- assets：用来放静态资源文件，一些图片啥的
- components：用来写一些展示的组件
- pligins：是插件文件，不用管
- router：这是路由回执，index.js为配置文件，相当于给生成超链接组件，可以通过标签使用，点击便生成对应组件的视图。
- views：在这里写主要页面的整体子页面视图，如登录，数据展示等独立页面，基本上每一个views中的文件都要到router中注册一个路由。
- App.vue：首页，父页面
- main.js：前端整体配置文件

###### ① 解读复习router

index.js

```js
import Vue from 'vue'
import VueRouter from 'vue-router'
import Home from '../views/Home.vue'

Vue.use(VueRouter)

const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/about',
    name: 'About',
    component: () => import(/* webpackChunkName: "about" */ '../views/About.vue')
  }
]

const router = new VueRouter({
  mode: 'history',
  base: process.env.BASE_URL,
  routes
})

export default router
```

- 配置固定，增加新的子页面时,需要import导入和增加属性

- ```vue
  import Home from '../views/Home.vue'
  ```

- ```cmd
   {
      path: '/',
      name: 'Home',
      component: Home
    },
  ```



使用标签

```vue
<!--提供一个展示容器-->
<router-view></router-view>
<!--提供一个展示内容切换超链接-->
<router-link to="/bar">柱状图</router-link>
```

- router-view：相当于提供了一个展示子页面的容器
- router-link：使用该标签，点击后容器会跳转到对应页面



##### 【2】Echarts组件解读

###### ① 创建一个图表类页面

在views文件下创建一个Echarts.xue页面,先不用写代码，先到routers中进行路由注册

```js
import Vue from 'vue'
import VueRouter from 'vue-router'
import Echarts from "../views/Echarts";


Vue.use(VueRouter)

const routes = [
  {
    path: '/Echarts',
    name: 'Echarts',
    component: Echarts
  }
]
```

然后到App.Vue父页面中，增加路由跳转标签

```vue
<template>

  <div id="app">
    <tr/><h3>选项栏</h3><tr/>
    <div id="select">
<!--提供一个展示内容切换超链接-->
<router-link to="/Echarts">图表展示</router-link>
    </div>
      
  <div style="border:0px solid #35a2ff;width: 800px;height: 600px;margin-left: 366px;">
<!--提供一个展示容器-->
    <router-view/>
  </div>
  </div>
</template>

<script>
export default {
  name: 'app'
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

注意：新手常犯，template是用来写视图层代码，该标签下只能有一个根目录，即` <div id="app">`，所有代码都要在这个根目录下填写，不然就会报错



###### ② Echarts组件使用说明

官网使用教程： https://echarts.apache.org/handbook/zh/get-started/

到Echarts官网去copy一个组件的代码。

- a.在script的export default中增加一个属性mounted(){}、并导入'echarts'包（在属性之外导入）

- b.在template中增加一个div展示容器

  - ```vue
    <template>
            <!--容器-->
        <div id="main" :style="{width:'800px',height:'600px'}"></div>
    </template>
    
    <script>
        //<!--导入的包-->
        import * as echarts from 'echarts';
        export default {
            name: "Echarts",
        mounted(){
            //<!--把网站的代码直接复制到这里-->
        }
     	}
    
    
    </script>
    
    <style scoped>
    
    </style>
    ```

- 复制代码，将`import * as echarts from 'echarts';`放置在外面(如果没导入)

关键代码如下，只用关注这些即可

```vue
<script>
import * as echarts from 'echarts';
	export default {
		name: "Echarts",
		mounted(){//
            var chartDom = document.getElementById('main');//这是创建的图表对象，用于视图层的引用传递
            option = {
            title: {//这是图表标题
                text: '饼图纹理',
                textStyle: {
                    color: '#235894'
                }
            },
            series: [
                {
				data: [//这是你要修改或者传输的数据
                        { value: 1048, name: 'Search Engine' },
                        { value: 735, name: 'Direct' },
                        { value: 580, name: 'Email' },
                        { value: 484, name: 'Union Ads' },
                        { value: 300, name: 'Video Ads' }
                    ]
	}
}
</script>   
```

- mounted():在模板渲染成html后调用，通常是初始化页面完成后，再对html的dom节点进行一些需要的操作。
  其实两者比较好理解，通常created使用的次数多，而mounted通常是在一些插件的使用或者组件的使用中进行操作，比如插件chart.js的使用: var ctx = document.getElementById(ID);通常会有这一步，而如果你写入组件中，你会发现在created中无法对chart进行一些初始化配置，一定要等这个html渲染完后才可以进行，那么mounted就是不二之选。下面看一个例子（用组件）。



##### 【3】element-ui组件解读

通过对Echarts的解读我们可以知道，Echarts组件的使用是通过在mounted()属性中建立渲染视图，然后在template中绑定对应的属性进行展示，而element-ui更加符合Vue的生态，提供组件是整体的Vue代码。

element-ui组件：https://element.eleme.cn/#/zh-CN/component/table

同样的：

###### ① 在Views下创建一个视图，我们选择解读CURD操作绑定的表格

###### ② 绑定视图后，去routers下的index.js中绑定路由

###### ③ 整个，复制粘贴代码

表格的标签简单解读下

```vue
<template>
    <el-table
            :data="tableData"
            style="width: 100%"
            :default-sort = "{prop: 'date', order: 'descending'}"
    >
        <el-table-column
                prop="id"
                label="id"
                width="180">
        </el-table-column>
        <el-table-column
                prop="name"
                label="姓名"
                width="180">
        </el-table-column>
        <el-table-column
                prop="power"
                label="能力值"
                sortable
                width="180">
        </el-table-column>


        <el-table-column label="操作">
            <template slot-scope="scope">
                <el-button
                        size="mini"
                        @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                <el-button
                        size="mini"
                        type="danger"
                        @click="handleDelete(scope.$index, scope.row)">删除</el-button>
            </template>
        </el-table-column>

    </el-table>
</template>

<script>
    export default {
        data() {
            return {
                tableData: [{
                    id: '1',
                    name: '王小虎',
                    power: 240
                }, {
                    id: '1',
                    name: '王小虎',
                    power: 240
                }, {
                    id: '1',
                    name: '王小虎',
                    power: 240
                }, {
                    id: '1',
                    name: '王小虎',
                    power: 240
                }]
            }
        },
        methods: {
            formatter(row, column) {
                return row.address;
            }
        }
    }
</script>
```

算了不解读了，直接看就行，复制过来都看不懂的请自己去补一下html和vue语法。

##### 【搭建流程小结】

从上面我们知道搭建一个子页面基本步骤为

- 在Views中建立一个vue文件
- 在routers中建立路由
- 在父页面中增加router-link和router-view标签
- 开始写Vue页面

其余页面我们还要一个增加数据的页面，和修改数据的页面，为了提高大家的举一反三能力，就不作解释了。前端页面建立好，不需要急着修改，如何与后端进行交互，等我们建立后端之后再来解读。



### 二、后端的搭建

相信能看到这篇文章的同学，已经对如何创建SpringBoot项目已经很熟悉了。但是其实大多数人都只是用过Mabatis，没有用过Mybatis-Plus。其实Mybatis-Plus的作用就是实现一个自动化配置，它会帮你自动生成mapper配置文件、Dao、service。后端搭建的流程主要是：连接数据库-编写各个层级（Entity、DAO、Service、Controller）-各个层级的具体实现。

#### 1、数据库搭建

```sql
USE `database`;

CREATE TABLE IF NOT EXISTS `powerhuman`( 
    `id` INT(4) NOT null, 
    `name` VARCHAR(30) NOT NULL DEFAULT '匿名' COMMENT '姓名', 
    `power` INT(10) NOT NULL DEFAULT 0 COMMENT '能力值', 
    PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8; 

INSERT INTO powerhuman VALUES (1,'路飞',999);
INSERT INTO powerhuman VALUES (2,'索隆',888);
INSERT INTO powerhuman VALUES (3,'山治',666);
INSERT INTO powerhuman VALUES (4,'乌索普',10000);

--自增：auto_increment
```



#### 2、编写各个层级(Mybatis-Plus)

用了这个你会发现，什么Mybatis逆向工程都是lj。

##### 【1】依赖导入

在创建springboot工程的时候，选中lombok、springweb、sql driver。然后再导入以下依赖：

```xml
     <!--基于springboot的mybatis-plus启动器-->  
	<dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.4.2</version>
        </dependency>

     <!--mybatis-plus的代码生成依赖--> 
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.3.2</version>
        </dependency>

     <!--代码生成模板-->  
        <dependency>
            <groupId>org.apache.velocity</groupId>
            <artifactId>velocity</artifactId>
            <version>1.7</version>
        </dependency>

```

##### 【2】代码自动生成（mybatis-plus）

① 在java-com.xxx文件夹根目录下建立一个代码生成类

```java
package com.plord.power;

import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;

public class Generate {
    public static void main(String[] args) {
        //创建generator对象
        AutoGenerator autoGenerator = new AutoGenerator();
        //数据源
        DataSourceConfig dataSourceConfig = new DataSourceConfig();
        dataSourceConfig.setDbType(DbType.MYSQL);
        dataSourceConfig.setDriverName("com.mysql.cj.jdbc.Driver");
        dataSourceConfig.setUsername("root");
        dataSourceConfig.setPassword("123456");
        dataSourceConfig.setUrl("jdbc:mysql://localhost:3306/database");//指定连接数据库
        autoGenerator.setDataSource(dataSourceConfig);
        //全局配置
        GlobalConfig globalConfig = new GlobalConfig();
        globalConfig.setOutputDir(System.getProperty("user.dir")+"/src/main/java");
        globalConfig.setAuthor("admin");
        globalConfig.setOpen(false);//是否打开文件
        globalConfig.setServiceName("%sService");//设置Service的命名
        autoGenerator.setGlobalConfig(globalConfig);
        //包信息
        PackageConfig packageConfig = new PackageConfig();
        packageConfig.setParent("com.plord");//将类放到包下，以下是指定包名
        packageConfig.setEntity("entity");
        packageConfig.setMapper("mapper");
        packageConfig.setService("service");
        packageConfig.setServiceImpl("service.impl");
        packageConfig.setController("controller");
        autoGenerator.setPackageInfo(packageConfig);
        //策略配置
        StrategyConfig strategyConfig = new StrategyConfig();
        strategyConfig.setInclude("powerhuman");//连接的表名，可多表同时执行，用逗号隔开即可
        strategyConfig.setNaming(NamingStrategy.underline_to_camel);//下划线转驼峰
        strategyConfig.setColumnNaming(NamingStrategy.underline_to_camel);
        strategyConfig.setEntityLombokModel(true);//是否使用lombok插件生成entity文件
        autoGenerator.setStrategy(strategyConfig);
        //运行
        autoGenerator.execute();
    }
}
```

② 运行一下，应有尽有

![image-20210927132355910](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210927132355910.png)



##### 【3】配置文件

刚才是配置连接数据库生成文件，但是springboot项目的运行，还没有配置连接,即配置数据源。所以要在配置文件中配置，这里的配置文件格式用的是yml格式。

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/database
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
    
mybatis-plus:
  configuration:
    #打印sql语句
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    #指定扫描的mapper文件夹
  mapper-locations: classpath:com/plord/mapper/xml/*.xml
  
  
server:
  port: 8081
```

除此之外，因为自动生成的mapper文件在java目录下，所以还得手动加上MapperScan

```java
@MapperScan("com.xxx.mapper")
```



##### 【4】测试

自动生成完毕，配置也完成。我们只需要编辑一下Controller，返回表单所有值即可，然后到浏览器测试一下即可。

```java
@RestController
@RequestMapping("//powerhuman")
public class PowerhumanController {

    @Autowired
    private PowerhumanService service;

    @RequestMapping("/list")
    public List<Powerhuman> test(){
        return service.list();
    }
}
```

这里再说明一下RestController与Controller的区别

- RestController：@RestController注解相当于@ResponseBody ＋ @Controller合在一起的作用,如果只是使用@RestController注解Controller，则Controller中的方法无法返回jsp页面，或者html，配置的视图解析器 InternalResourceViewResolver不起作用，返回的内容就是Return 里的内容。
- Controller：使用@Controller 注解，在对应的方法上，视图解析器可以解析return 的jsp,html页面，并且跳转到相应页面，若返回json等内容到页面，则需要加@ResponseBody注解。

![image-20210927185417207](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210927185417207.png)

测试成功！



#### 3、各个层级的具体实现

各个层级具体实现，其实在一些基础的CRUD操作中，Mabatis-Plus自动生成类中的方法已经够用，我们只需要写Controller即可。具体实现，我们将在第三部分展开，因为我们是依照前端需求去写我们的后端代码。现在前端需要对数据进行增删改查，并能用表单和图表的方式展现。





### 三、前后端数据传递

##### 【1】搭建前端和后台的联系

我们只需要解决以下两个问题即可实现前端与后端互连。

###### ① 前端如何连接后台？

我们需要在接收的Vue页面script中，调用axios去get后台数据放入response对象中

```js
        created() {
            axios.get('http://localhost:8081/powerhuman/list').then(function (response) {
                console.log(response)
            })
        },
```

- console.log(response)：调用console即控制台，记录传入的response。

这个时候我们运行前端，发现控制台的数据显示错误：

![image-20210927191338944](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210927191338944.png)

这是为什么呢，因为刚才的代码，只在前端去接收'http://localhost:8081/powerhuman/list'的数据，但是后端并没有发送数据，交流不一致。所以就有第二个问题：



###### ② 后端如何连接前端

加一个解决跨域问题的配置类即可

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class CrosConfiguration implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOriginPatterns("*")
                .allowedMethods("GET", "HEAD", "POST", "PUT", "DELETE", "OPTIONS")
                .allowCredentials(true)
                .maxAge(3600)
                .allowedHeaders("*");
    }

}
```

- CorsRegistry：的全称为Cross-Origin Resource Sharing Registry ，直译过来就是跨域资源共享登记。是spring中解决跨域的一种方式

- 需要在配置类中引入@Configuration注解,SpringBoot才能识别到该配置文件,这个就涉及到boot的自动装配原理了,

  然后实现 `WebMvcConfigurer`接口中的 `addCorsMappings` 方法

- allowed后面跟的就是允许的相关事务

![image-20210927192234878](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210927192234878.png)

这样前后端的就实现了数据交流。



##### 【2】CRUD前端可视化实现

###### ① 将数据转换为表格

```Vue
<template>
    <el-table
            :data="tableData"
            style="width: 100%">

        <el-table-column
                prop="id"
                label="id"
                width="180">
        </el-table-column>
        <el-table-column
                prop="name"
                label="姓名"
                width="180">
        </el-table-column>
        <el-table-column
                prop="power"
                label="能力值"
                sortable
                width="180">
        </el-table-column>


        <el-table-column label="操作">
            <template slot-scope="scope">
                <el-button
                        size="mini"
                        @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                <el-button
                        size="mini"
                        type="danger"
                        @click="handleDelete(scope.$index, scope.row)">删除</el-button>
            </template>
        </el-table-column>

    </el-table>
</template>

<script>
    export default {
        created() {
            let _this = this
            axios.get('http://localhost:8081/powerhuman/list').then(function (response) {
                _this.tableData = response.data
            })
        },

        data() {
            return {
                tableData: null
            }
        },
        methods: {
            formatter(row, column) {
                return row.address;
            }
        }
    }
</script>
```

我们直接将tableData的数据清空，将tableData的数据换为response接收的data数据。这里做了一个this命名处理，因为在(response){}中this表示response这个对象，所以我们要用let使_this表示全局的this，这样才不会发生调用冲突。

- 要记住template的el-table-column中，prop要对应response返回接收的属性名



###### ② 删除操作

**前端代码分析**：

```Vue
        <el-table-column label="操作">
            <template slot-scope="scope">
                <el-button
                        size="mini"
                        @click="handleEdit(scope.row)">编辑</el-button>
                <el-button
                        size="mini"
                        type="danger"
                        @click="handleDelete(scope.row)">删除</el-button>
            </template>
        </el-table-column>
```

@click="handleDelete(scope.row)"这里对应绑定的是方法handleDelete,这个方法将返回这个区域的这一行的数据（scope.row），所以我们要在script中的method属性添加该方法：

```vue
       methods: {
            handleDelete(row){
                axios.delete('http://localhost:8081/powerhuman/delete/'+row.id).then(function (response) {
                    if(response.data){
                        alert("删除成功")
                    }
                })
            }
        }
```

同样的，前端数据若想传至后端，也需要使用axios去执行delete的Mapping操作，这里直接传入row.id即可，然后通过返回的值判断删除是否成功，成功则弹窗告知用户。要设置警告弹窗，可以到element-ui去找对应的组件进行配置。例如：

```vue
methods: {
            handleDelete(row){
                this.$confirm('此操作将永久删除'+row.name, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {

                    axios.delete('http://localhost:8081/powerhuman/delete/'+row.id).then(function (response) {
                        if(response.data){
                            alert("删除成功")
                        }
                    })
                    location.reload()//自动刷新
                    this.$message({
                        type: 'success',
                        message: '删除成功!'
                    });
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            }
```



**后端代码分析**：

```java
    @DeleteMapping("/delete/{id}")
    public boolean deleteById(@PathVariable("id") Integer id){
        return service.removeById(id);
    }
```

- 在Controller中书写代码，因为是删除方法，所以注解使用的是DeleteMapping（与前端方法对应），这是满足Resful风格的写法
- 通过{id}去接收值，然后通过@pathVariable去绑定传入的值
- 最后调用service的removeById进行删除执行



###### ③ 更新操作

**前端代码分析**：

首先我们需要从编辑按钮跳转的编辑页面，这里补充Vue页面跳转方式：

1.声明式页内跳转

```vue
<router-link to='/a'>页面a</router-link>
<!--携带参数的形式-->
<router-link to='/a?userid=666&username=itachi'>页面a</router-link>
```

2.声明式页外跳转（新开页面）方式

```vue
<router-link to='/a' target='_blank'>页面a</router-link>
<!--带参数形式-->
<router-link to='/a?userid=666&username=itachi' target='_blank'>页面a</router-link>
```


注：router-link在页面渲染的时候是按照a标签的标准来的，即将router-link渲染成a标签

3.编程式页内跳转

```vue
this.$router.push({ path: '/a' });
//带参数形式1
this.$router.push({ path: '/a', query: {'userid': 666, 'username': 'itachi'}});
//带参数形式2
this.$router.push({ path: '/a?userid=666&username=itachi'});
```

4.编程式页外跳转（新开页面）方式

```vue
window.open('#/a', '_blank');
//带参数方式
window.open('#/a?userid=' + userid + '&username' + username, '_blank');//这里一定要注意路径
```

知识补充完毕后，我们来写这个跳转代码，通过handleEdit方法，同样提示弹窗，通过确定跳转。

```vue
            handleEdit(row){
                this.$confirm('此操作将进入'+row.name+'的修改页面', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.$router.push('/edit')
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消修改'
                    });
                });
            }
```

需要一个表单页面，进行信息的接收和修改信息的传递。还记得一个前端页面建立步骤吗？就是在views中建立vue文件，然后到router中注册路径，因为这是修改表单，通过按钮直接跳转，所以不需要去使用router-link和router-view了。根据element-ui的表单组件，我们修改代码得如下

```vue
<template>
<el-form ref="form" :model="form" label-width="80px">

    <el-form-item label="id" prop="id">
        <el-input v-model="form.id" readonly></el-input>
    </el-form-item>
    <el-form-item label="name" prop="name">
        <el-input v-model="form.name"></el-input>
    </el-form-item>
    <el-form-item label="power" prop="power">
        <el-input v-model="form.power"></el-input>
    </el-form-item>

    <el-form-item>
        <el-button type="primary" @click="onSubmit">修改</el-button>
        <el-button @click="noSubmit">取消</el-button>
    </el-form-item>
</el-form>
</template>

<script>
    export default {
        //获取信息填入
        created() {
            let id = this.$route.query.id
            let _this = this
          axios.get("http://localhost:8081/powerhuman/find/"+id).then(function (response) {
              console.log(id)
              _this.form = response.data
          })
        },

        data() {
            return {
                form: {
                    id: '',
                    name: '',
                    power: ''
                }
            }
        },
        //表单提交处理
        methods: {
            onSubmit() {

            },
            noSubmit(){
                this.$router.push('/table')
            }
        }
    }
</script>
```

- let id = this.$route.query.id：这是获取传入页面的id的值，也就是获取编程式页内跳转参数
- v-model：实现双向绑定，我们在created()中通过访问"find/1"去获取后台数据，通过axios去获取数据参数，直接填入
- 在id的表单中还设置了readonly，不能改变其id

表单提交处理我们单独来看

```vue
        methods: {
            onSubmit() {
                    let _this = this
                    axios.put("http://localhost:8081/powerhuman/update",this.form).then(function (response) {
                        if(response.data){
                            _this.$alert("修改成功")
                            _this.$router.push('/table')
                        }
                        else {
                            _this.$alert("修改失败")
                            _this.$router.push('/table')
                        }
                    })

            },
            noSubmit(){
                this.$router.push('/table')
            }
        }
```

- 我们通过axios，传入this.form，成功则跳转，失败也跳转，点击取消也跳转
- axios.put("http://localhost:8081/powerhuman/update",this.form)：这便是提交表达的形式
- _this：因为在(response){}的this代表其本身，所以我们要将 _this重命名实现跳转



**后台代码分析**：

```java
    @PutMapping("update")
    public boolean update(@RequestBody Powerhuman powerhuman){
        return service.updateById(powerhuman);
    }
```

- @PutMapping：接收表达使用put提交形式
- @RequestBody：它的作用其实是将json格式的数据转为java对象，因为前端传到后台的表单是json，我们要将其转换为实体类对象



###### ④ 增加操作

增加操作和修改操作很相似，相比看了前面的案例，大家已经能举一反三弄出来，这里我就不作过多的解释了，然后增加操作推荐使用post方法进行数据交流。直接看代码：

**前端**

```vue
<template>
    <el-form ref="form" :model="form" label-width="80px">

        <el-form-item label="id" prop="id">
            <el-input v-model="form.id"></el-input>
        </el-form-item>
        <el-form-item label="name" prop="name">
            <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="power" prop="power">
            <el-input v-model="form.power"></el-input>
        </el-form-item>

        <el-form-item>
            <el-button type="primary" @click="onSubmit">增加</el-button>
            <el-button @click="noSubmit">取消</el-button>
        </el-form-item>
    </el-form>
</template>

<script>
    export default {
        data() {
            return {
                form: {
                    id: '',
                    name: '',
                    power: ''
                }
            }
        },
        methods: {
            onSubmit() {
                let _this = this
                axios.post("http://localhost:8081/powerhuman/insert",this.form).then(function (response) {
                    if(response.data){
                        _this.$alert("添加成功")
                        _this.$router.push('/table')
                    }
                    else {
                        _this.$alert("添加失败，id可能存在")
                        location.reload()
                    }
                })

            },
            noSubmit(){
                location.reload()
            }
        }
    }
</script>
```



**后端**

```java
    @PostMapping("/insert")
    public boolean add(@RequestBody Powerhuman powerhuman){
        return service.save(powerhuman);
    }
```

综上所述，我们的CURD操作讲解就结束了，图表展示只是一个echarts的附加项，大家看完这里就可以去搞事请啦！搞懂知识点就自己从零搭建一个自己原创项目，搭建完你的收获即将满满！

这里补充一下数据校验的知识：原文链接：https://blog.csdn.net/qq_41402200/article/details/86016313

```vue
 <el-form ref="rulesForm" :rules="Rules" :model="rulesForm" label-width="200px">
          <el-form-item label="用户名称:" prop="userName">
            <el-input v-model="rulesForm.userName" style="width:300px" maxlength="50"/>
          </el-form-item>
</el-form>
```

```vue
Rules: {
        userName: [
          {
            required: true,
            message: "请输入用户名称",
            trigger: "blur"
          }
        ]
}
```



##### 【3】图表展示

这里涉及的核心理念是：先找到前端页面的数据封装形式，因为前端和后端交互传递的是json数据，所以我们要在后端传递对应的数据格式到达前端，这里一般是建立一个类（类似entity），去进行return传递。

###### ① 建立传递的数据实体类

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class EchartsPieValue {
    private Integer value;
    private String name;

}
```



###### ② Service层封装传递的数据

```java
public class PowerhumanServiceImpl extends ServiceImpl<PowerhumanMapper, Powerhuman> implements PowerhumanService {

    @Override
    public List<EchartsPieValue> pieList() {
        List<EchartsPieValue> pielist = new ArrayList<>();
        List<Powerhuman> powerhumanList = getBaseMapper().selectList(null);
        for(Powerhuman powerhuman:powerhumanList){
            EchartsPieValue value = new EchartsPieValue();
            value.setName(powerhuman.getName());
            value.setValue(powerhuman.getPower());
            pielist.add(value);
        }
        return pielist;
    }
}
```



###### ③ Controller层传递数据

```java
    @GetMapping("/pievalue")
    public List<EchartsPieValue> pieValueList(){
        return service.pieList();
    }
```







###### ④ 前端代码

```vue
<template>
    <div id="main" :style="{width:'800px',height:'600px'}"></div>
</template>

<script>
    import * as echarts from 'echarts';
    export default {
        name: "Echarts",
    mounted(){
        let _this = this
        axios.get('http://localhost:8081/powerhuman/pievalue').then(function (response) {
        var app = {};
        var chartDom = document.getElementById('main');
        var myChart = echarts.init(chartDom);
        var option;

        const piePatternSrc =null;
        const bgPatternSrc = null;
        const piePatternImg = new Image();
        piePatternImg.src = piePatternSrc;
        const bgPatternImg = new Image();
        bgPatternImg.src = bgPatternSrc;
        option = {
            backgroundColor: {
                image: bgPatternImg,
                repeat: 'repeat'
            },
            title: {
                text: '饼图纹理',
                textStyle: {
                    color: '#235894'
                }
            },
            tooltip: {},
            series: [
                {
                    name: 'pie',
                    type: 'pie',
                    selectedMode: 'single',
                    selectedOffset: 30,
                    clockwise: true,
                    label: {
                        fontSize: 18,
                        color: '#235894'
                    },
                    labelLine: {
                        lineStyle: {
                            color: '#235894'
                        }
                    },
                    data:response.data,
                    itemStyle: {
                        opacity: 0.7,
                        color: {
                            image: piePatternImg,
                            repeat: 'repeat'
                        },
                        borderWidth: 3,
                        borderColor: '#235894'
                    }
                }
            ]
        };
        option && myChart.setOption(option);
        })
        }
    }
</script>


<style scoped>

</style>
```

注意：

- 前后端数据传递，前端axios，后端允许跨域，前端通过url获取后端传入的json数据（或向后端传递）
- json数据一致：后端向前端传递的数据，要满足前端接收的类型
- 交流方法一致：前后端传递和接收的url方法要一致，展示（Request），删除（Delete），增加（post），修改（put），接收（get）



到此结束，原创不容易，望各位大佬一键三连！！！:kissing_heart:













