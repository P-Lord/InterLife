# 新手（后端）不看很亏系列！知道Vue语法还是没搞懂前端，从完整流程和案例出发，包含Element-ui的讲解，让你自己也能写前端页面。

之前写了一篇《SpringBoot项目快速上手，你忘记的知识点都在这！》，是关于一个前后端分离项目搭建的基础知识讲解，写的还行，就是火不了。但是我写文章是为了自己学习的记录，并将自己的学习的困惑表达出来，提供一个解决方案给和我一样的新手，所以希望能获得大家的认可。您的关注是对我最大的支持！，让我知道我也可以🙆‍。

B站视频，像狂神(六小时)或者黑马(四小时)的速成教程只简单说明了Vue的语法，单独将语法提炼出来说明，其实不利于对Vue的学习，没有一个完整体系说明。知识点零零散散，可能当时懂了什么意思，但是没有完整地走一遍前端搭建的流程（完整的页面），很难自己动手写自己原创的前端页面。身为一个学习时间不多的后端学生党，如果去专门学习前端的知识有点有气无力，就是弹幕常见的“被迫学前端”

所以呢，写下这篇文章，通过我自己写的一个前端项目，帮助大家快速补充前端知识，了解完整页面的搭建，学会使用element-ui去搭建自己的页面~

学习这篇文章需要：学习过html、Vue语法、是一个后端learning。

### 一、后端的前端必知知识补充

#### 1、如何使用Vue-cli搭建一个前端项目

如果是第一次搭建环境（若环境搭配完毕，直接vue ui）：

- 首先需要下载Node.js
- 下载淘宝镜像(因为有的安装需要通过镜像)
- 安装Vue-cli（Vue脚手架），推荐3.0，可以使用项目搭建可视化工具
- 在idea中安装Vue插件
- 通过命令：vue ui打开可视化工具
- 通过可视化工具搭建项目：选中label、router、vuex
- 在可视化工具中安装：element-ui、axios
- 从idea中打开即可

具体可以看《SpringBoot项目快速上手，你忘记的知识点都在这！》有详细介绍。



#### 2、Vue核心知识讲解

##### 【1】常见语法

详细可以看这篇：《VUE基础知识讲解，适合Springboot后端人员的简单教程》包含了Vue的基本语法

##### 【2】axios原理以及使用

Axios 是一个基于 promise 的 HTTP 库，可以用在浏览器和 node.js 中。

其中文帮助文档：http://www.axios-js.com/zh-cn/docs/#%E5%8D%8F%E8%AE%AE

###### ① axios语法

get方法（获取数据）：

```vue
<script>
let id = this.$route.query.id
let _this = this
axios.get("http://localhost:8081/powerhuman/find/"+id).then(function (response) {
console.log(id)
_this.form = response.data
})
}
</script>
```

put方法（修改表单）：

```vue
<script>
let _this = this
axios.put("http://localhost:8081/powerhuman/update",this.form).then(function (response) {})
</script>
```

post方法（提交表单）:

```vue
<script>
let _this = this
axios.post("http://localhost:8081/powerhuman/insert",this.form).then(function (response) {})
</script>
```

delete方法（删除数据）：

```vue
<script>
axios.delete('http://localhost:8081/powerhuman/delete/'+row.id).then(function (response) {
})
</script>
```

可以看到anxios使用的核心代码是：

```cmd
var that = this;
axios.请求方法("请求的地址").then(function(response){
that.数据 = response.data;
}
```

###### ② anxios解决跨域

Axios是不允许跨域访问的，别说跨域，跨端口都不行。一般我们可以修改后台，支持跨域。

后端连接前端，加一个解决跨域问题的配置类即可

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

##### 【3】Vue常用方法理解

methods ：定义方法，methods 将被混入到 Vue 实例中。可以直接通过 VM 实例访问这些方法，或者在指令表达式中使用。方法中的 this 自动绑定为 Vue 实例；

computed ：computed主要作用是对原始数据，进行二次处理，例如：类型转换，排序，拼接；

created ：生命周期钩子函数，实例创建完成后执行的代码。（html加载完成之前执行）

mounted：生命周期钩子函数，DOM加载完成后调该钩子函数，ajax请求在此阶段调用，该钩子在服务器端渲染期间不被调用。如果你希望等到整个视图都渲染完毕，可以用 vm.$nextTick 替换掉 mounted。（html加载完成后执行）

watch：watch是去监听一个值的变化，然后执行相对应的函数。

```vue
var vm = new Vue({
	el:"#app",
	data:{
	},
	created:{
	}.
	methods:{
	},
	computed:{
	},
	mounted() {
	},
	watch(){
	}
})
```



### 二、Element-ui的使用

不知道大家还记不记得java中的GUI组件，其实可视化组件的搭建都一样的，java中的GUI和element-ui是一个道理。

https://element.eleme.cn/#/zh-CN/component/installation

#### 1、容器

用于布局的容器组件，方便快速搭建页面的基本结构：

`<el-container>`：外层容器。当子元素中包含 `<el-header>` 或 `<el-footer>` 时，全部子元素会垂直上下排列，否则会水平左右排列。

`<el-header>`：顶栏容器。

`<el-aside>`：侧边栏容器。

`<el-main>`：主要区域容器。

`<el-footer>`：底栏容器。

```vue
<el-container>
  <el-aside width="200px">Aside</el-aside>
  <el-container direction:"vertical">
    <el-header>Header</el-header>
    <el-main>Main</el-main>
    <el-footer>Footer</el-footer>
  </el-container>
</el-container>
```

容器之间可以如上嵌套使用，相当于建立小区房中，划分用地要建立几栋楼。

| 参数      | 说明             | 类型   | 可选值                | 默认值                                                     |
| :-------- | :--------------- | :----- | :-------------------- | :--------------------------------------------------------- |
| direction | 子元素的排列方向 | string | horizontal / vertical | 子元素中有 `el-header` 或 `el-footer` 时为水平，否则为垂直 |



#### 2、布局

还记得GUI中的Layout布局吗，因为网页大小是任意变化的，所以element-ui采用流式布局实现。如果说容器是划分用地，那布局这是具体一块用地中要建立几个房间。

“通过基础的 24 分栏，迅速简便地创建布局。”在element-ui中布局组件将页面分为多行，每行最多24列。我们用网站中的混合布局代码来进行讲解。

```vue
<el-row :gutter="20">
  <el-col :span="16"><div class="grid-content bg-purple"></div></el-col>
  <el-col :span="8"><div class="grid-content bg-purple"></div></el-col>
</el-row>
<el-row :gutter="20">
  <el-col :span="8"><div class="grid-content bg-purple"></div></el-col>
  <el-col :span="8"><div class="grid-content bg-purple"></div></el-col>
  <el-col :span="4"><div class="grid-content bg-purple"></div></el-col>
  <el-col :span="4"><div class="grid-content bg-purple"></div></el-col>
</el-row>
<el-row :gutter="20">
  <el-col :span="4"><div class="grid-content bg-purple"></div></el-col>
  <el-col :span="16"><div class="grid-content bg-purple"></div></el-col>
  <el-col :span="4"><div class="grid-content bg-purple"></div></el-col>
</el-row>

<style>
  .el-row {
    margin-bottom: 20px;
    &:last-child {
      margin-bottom: 0;
    }
  }
  .el-col {
    border-radius: 4px;
  }
  .bg-purple-dark {
    background: #99a9bf;
  }
  .bg-purple {
    background: #d3dce6;
  }
  .bg-purple-light {
    background: #e5e9f2;
  }
  .grid-content {
    border-radius: 4px;
    min-height: 36px;
  }
  .row-bg {
    padding: 10px 0;
    background-color: #f9fafc;
  }
</style>
```

提取出代码：

```vue
<el-row :gutter="20">
  <el-col :span="16"><div class="grid-content bg-purple"></div></el-col>
  <el-col :span="8"><div class="grid-content bg-purple"></div></el-col>
</el-row>
```

- 建立一行`el-row`

- 一行中有两列`el-col`

- 一列占用16栏`:span="16"`，一列占用8栏

- 然后每列间隔为20`:gutter="20"`

当我们一行只有一列时，我们改变其位置需要用到`:offset`属性，表示从第一栏开始

```vue
<el-row :gutter="20">
<el-col :span="6" :offset="6"><div class="grid-content bg-purple"></div></el-col>
</el-row>
```

| 属性名   | 移动方式     | 类型 | 可选值| 默认值 |
| ------ | ------------------ | ------ | ---- | ---- |
| offset | 栅格左侧的间隔格数 | number | —    | 0    |
| push   | 栅格向右移动格数   | number | —    | 0    |

由上图可知，我们也可以使用`:push`属性来完成向右移动，但是offset是通过间隔，push是通过悬浮



#### 3、页面搭建组件组合



#### 













3、





### 三、具体案例搭建《LZ Diary》







