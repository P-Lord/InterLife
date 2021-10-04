## 适合SpringBoot后端的Vue前端开发的简单教程

### 一、Vue-li

与Maven在后端的作用同理，用于快速生成一个Vue的脚手架，或者说是Vue前端文件管理框架。

#### 1、准备工作

##### ① 下载Node.js

http://nodejs.cn/download/

查看版本号：

```cmd
node -v
nmp -v
```



##### ② 下载镜像

```cmd
npm install cnpm -g
或在每次安装时加入：
npm install --registry=http://registry.npm.taobao.org
```



##### ③ 安装Vue-cli

```cmd
cnpm install vue-cli -g
```



#### 2、建立第一个Vue-cli项目

通过 cd 到项目建立的文件目录下,通过命令：

```cmd
vue init webpack myvue
```

根据提示填写项目名称

![image-20210921194702872](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210921194702872.png)

全部选no是为了体验安装库的过程。

到项目文件目录下安装依赖：

```cmd
npm install
```

根据提示修复错误

```cmd
npm audit fix
```

如此，项目便建立完毕，可通过以下命令在项目目录下运行

```cmd
npm run dev
```

相当于启动了类似Tomcat服务器(Node.js)

![image-20210921195546991](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210921195546991.png)

### 二、webpack

兼容工具，模块化处理，降维打击让所有浏览器都能使用。

##### 1、安装：

```cmd
cnpm install webpack -g
cnpm install webpack-cli -g
```



##### 2、js方法引用（模块化开发）

在一个文件(./xxx)中建立出口方法`export.方法名 = function(){方法体};`

在另一个文件中引用方法，只需要创建一个对象，用require("文件名")获取该方法`var hello = require("./xxx")`;即可用其文件名进行调用



##### 3、打包

在webpack.config.js中

```js
moudle.export = {
    entry:'./moudles/main.js',
    output:{
        filename:"./js/bundle.js"
    }
};
```

这是将mouldes的main文件打包到js/bundle文件中



##### 4、引入打包后的文件

```html
<script src="dist/js/bundle.js"></script>
```

几句话即可导入整个js文件，所以webpack就是个打包工具



### 三、Vue-Router

- 在router文件夹中建立index.js
- 导入VueRouter文件
- 导入要跳转的Vue页面模块
- 在routes中添加path、name、component



##### 1、在项目文件中安装

```cmd
cnpm install vue-router --save-dev
```

##### 2、导入Vue-router,配置组件跳转路径

① 在components中建立两个Vue页面(index和Content)

```vue
<template>
<h1>cnm</h1>
</template>

<script>
    export default {
        name: "index"
    }
</script>

<style scoped>

</style>
```

```vue
<template>
  <h1>ily</h1>
</template>

<script>
    export default {
        name: "Content"
    }
</script>

<style scoped>

</style>
```

Vue文件默认为项目中的组件

② 在src目录下建立文件夹router，在该文件夹下建立index.js文件

```js
import Vue from 'vue'
import VueRouter from 'vue-router'
import Content from '../components/Content'
import index from '../components/index'

//安装路由
Vue.use(VueRouter);

//配置导出路由
export default new VueRouter({
  routes:[{
    //路由路径
    path:'/content',
    name:'content',
    //组件跳转
    component: Content
  },
    {
      path:'/index',
      name:'index',
      //组件跳转
      component: index
    }
  ]
})
```

##### 3、Vue-Router的使用

① 在main.js中对应的Vue对象中添加路由

```js
import Vue from 'vue'
import App from './App'
import router from './router'//制动扫描router中的路由配置

Vue.config.productionTip = false

new Vue({
  el: '#app',
  //配置路由
  router,
  components: { App },
  template: '<App/>'
})
```

- main文件中两句话配置完成



##### ② 在App.vue中使用路由

```vue
<template>
  <div id="app">
    <router-link to="/index">index</router-link>
    <router-link to="/Content">content</router-link>
    <router-view></router-view>
  </div>
</template>
```

- router-link to="/跳转的页面名"：相当于超链接
- router-view：页面内容展示



### 四、Element-Ui

##### 1、安装

```cmd
npm i element-ui -S

一般还有
npm install vue-router --save-dev
npm install
cnpm intall sass-loader node-sass --save-dev
npm run dev
```

##### 2、使用流程

① 在 main.js 中写入以下内容

```js
import Vue from 'vue';
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';
import App from './App.vue';

Vue.use(ElementUI);

new Vue({
  el: '#app',
  render: h => h(App)
});
```

② 将对应组件代码复制到Vue文件即可





##### 3、嵌套路由

路由的最大特点就是组件化，只增加当前视图的内容，不跳转当前主页面。





















