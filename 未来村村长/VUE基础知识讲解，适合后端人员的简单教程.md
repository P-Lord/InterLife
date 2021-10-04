## VUE基础知识讲解，适合后端人员的简单教程

后端不得不系列：不得不学一点VUE了,本教程结合了狂神和黑马程序员，所以知识较为完善，值得收藏:heart_eyes::heart_eyes::heart_eyes:

==关于前端==

html-css-javaScript：前端三要素

前端组成：逻辑判断（遍历与循环）、事件监听、视图呈现（html、css）、通信功能（ajax、Axios）

==关于框架==

vue：渐进式javaScript的框架——框架=规则

Element UI：一套为开发者、设计师和产品经理准备的基于 Vue 3.0 的桌面端组件库



### 一、第一个VUE程序

##### 1、在idea安装Vue插件

![image-20210920190348442](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210920190348442.png)

##### 2、导入Vue.js

```html
<script src=“https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js”></script>
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
```

##### 3、hello，Vue！

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>


</head>
<body>

<div id="app">
    {{message}}
</div>

<!--引入Vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script type="text/javascript">
    var vm = new Vue({
        el:"#app",
        data:{
            message:"hello,vue!"
        }
    })
</script>

</body>
</html>
```

- `el: '#xxx'`：绑定元素的ID(id=xxx)，该标签下的{{xxx}}都可以取到该值，但外部的无法取到
- `data:{message:'Hello Vue!'}`：数据对象中有一个名为message的属性，并设置了初始值Hello，Vue！
- data所办含数据类型：字符串（message:"xxx"），对象（school{name:"xxx",locatioan:"xxx"}）,数组（campous:["xxx","xxx1","xxx2"]）
- 对应的取法：字符（{{message}}）,对象（{{school.name}}）,数组（{{campous[0]}}）



一个Vue前端项目基础单位：

- 一个标签的id

  - ```html
    <div id="app">
        {{message}}
    </div>
    ```

- 一个绑定这个id的Vue对象

  - ```html
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
    <script type="text/javascript">
        var vm = new Vue({
            el:"#app",
            data:{
                message:"hello,vue!"
            }
        })
    </script>
    ```



### 二、基础语法指令

指令官方API教程地址：https://v3.cn.vuejs.org/api/directives.html

##### ① v-bind

```html
<!DOCTYPE html>
<html lang="en" xmlns:v-bind="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

</head>
<body>

<!--view层，模板-->
<div id="app">
    <span v-bind:title="message">
    鼠标悬停几秒钟查看此处动态绑定的提示信息！
  </span>
</div>

<!--1.导入Vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script type="text/javascript">
    var vm = new Vue({
        el:"#app",
        /*Model：数据*/
        data:{
            message: '页面加载于 ' + new Date().toLocaleString()
        }
    });
</script>
</body>
</html>
```

- 在html中，span标签是使用来组合文档中的行内元素，以便使用样式来对它们进行格式化。span标签本身并没有什么格式表现（比如：换行等），需要对它应用样式才会有视觉上的变化。

  span标签通常使用来将文本的一部分或者文档的一部分独立出来，从而对独立出来的内容设置单独的样式。

-  v-bind:v-bind等被称为指令。指令带有前缀v以表示它们是Vue提供的特殊特性。该指令此处的意思是将这个元素节点的title特性和Vue实例的message属性保持一致。相当于之前的{{}}。



##### ② v-if、v-else

条件判断语句。

```html
<!DOCTYPE html>
<html lang="en" xmlns:v-bind="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<div id="app">
    <h1 v-if="ok">Yes</h1>
    <h1 v-else>No</h1>
</div>

<div id="app1">
    <h1 v-if="type==='A'">A</h1>
    <h1 v-else-if="type==='B'">B</h1>
    <h1 v-else-if="type==='D'">D</h1>
    <h1 v-else>C</h1>

</div>



<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script type="text/javascript">
    var vm = new Vue({
        el:"#app",
        data:{
            ok:true
        }
    });

    var vm = new Vue({
        el:"#app1",
        data:{
            type: 'E'
        }
    });

</script>
</body>
</html>
```

- `<h1 v-if="ok">Yes</h1>`一个v-if="xxx"代表，xxx===ture。因为Vue的语法中三个等号才表示相等判断
- v-if="判断语句"、v-else-if="判断语句"、v-else="判断语句"
- 所判断的值，来自Vue所传入的值，实现动态dom渲染

##### ③ v-for

###### 1、完全遍历

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--view层，模板-->
<div id="app">
    <li v-for="item in items">
        {{item.message}}
    </li>

</div>

<!--1.导入Vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script type="text/javascript">
    var vm = new Vue({
        el:"#app",
        /*Model：数据*/
        data:{
            items:[
                {message:'狂神说Java'},
                {message:'狂神说前端'},
                {message:'狂神说运维'}
            ]
        }
    });
</script>
</body>
</html>
```

- []里面放数组，{}里面放对象（键值对对象）
- 完全遍历v-for语法：
  - v-for="单体名 in 遍历对象"
  - {{单体名.属性名}}即Vue对象中对应的传入值属性名



###### 2、索引生成

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--view层，模板-->
<div id="app">
    <li v-for="(item,index) in items">
        {{index}}-{{item.message}}
    </li>

</div>

<!--1.导入Vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script type="text/javascript">
    var vm = new Vue({
        el:"#app",
        /*Model：数据*/
        data:{
            items:[
                {message:'狂神说Java'},
                {message:'狂神说前端'},
                {message:'狂神说运维'}
            ]
        }
    });
</script>
</body>
</html>
```

- 索引生成遍历v-for语法：
  - v-for="（单体名，index） in 遍历对象"

  - {{index}}，{{单体名.属性名}}



##### ④ v-on（@）

监听事件的方法

```html
<!DOCTYPE html>
<html lang="en" xmlns:v-on="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<div id="app">
    <button v-on:click="sayHi">点我</button>
</div>


<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script type="text/javascript">

    var vm = new Vue({
        el:"#app",
        data:{
            message:'Hello World'
        },
        methods:{
            sayHi:function(event){
                alert(this.message);
            }
        }
    });
</script>
</body>
</html>
```

- 在标签中添加`v-on:事件名="连接Vue的对应method名"`，`v-on:`可以替换为@

- 在Vue对象中methods属性中添加method

  - ```html
    methods:{
                属性名:function(event){
                    事件;
                }
    ```

- alert为弹出弹窗



##### ⑤ v-model

【双向绑定】：可以用`v-model`指令在表单、及元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。`v-model`会忽略所有表单元素的`value`、`checked`、`selected`特性的初始值而总是将`Vue`实例的数据作为数据来源。你应该通过`JavaScript`在组件的`data`选项中声明初始值。

当数据发生变化时，视图也发生变化。就像线上做选择题，选完之后答案会显示在题目旁边。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div id="app">

    下拉框:
    <select v-model="pan">
        <option value="" disabled>---请选择---</option>
        <option>A</option>
        <option>B</option>
        <option>C</option>
        <option>D</option>
    </select>
    <span>value:{{pan}}</span>



</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script type="text/javascript">
    var vm = new Vue({
        el:"#app",
        data:{
            pan:""
        }
    });
</script>
</body>
</html>
```

注意：`v-model`会忽略所有表单元素的`value`、`checked`、`selected`特性的初始值而总是将`Vue`实例的数据作为数据来源。你应该通过`JavaScript`在组件的`data`选项中声明初始值。

- `v-model:"属性名"`
-  `{{属性名}}`
- 因为v-model绑定了该下拉框，所以所输入的值会传给v-model后面绑定的属性，该属性的值会传回Vue对象对应属性名。在其它如单行文本、多行文本、单选按钮、多选按钮皆可使用v-modl绑定，达成双向值的传递。

##### ⑥ v-text

```html
<div id="app">
    <button v-text:"message">点我</button>
</div>
==============
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script type="text/javascript">

    var vm = new Vue({
        el:"#app",
        data:{
            message:'Hello World'
        }
    });
</script>
```

- v-text：起到的是标签内的内容替换为对应js中的属性的值，特点是全部替换
- 和v-text作用相似的就是{{属性名}},如此别不会导致全替换



##### ⑦ v-html

```html
<div id="app">
    <button v-text:"message">点我</button>
</div>
==============
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script type="text/javascript">

    var vm = new Vue({
        el:"#app",
        data:{
            message:'Hello World'
        }
    });
</script>
```

- 对于普通文本来说，和v-text作用相似标签内的内容替换为对应js中的属性的值，也是全部替换
- 当属性的值为html语法格式时，会自动增整个html语句



##### ⑧ v-show

```html
<div id="app">
    <button v-show="true">点我</button>
    <button v-show="isSHow">点我</button>
    <button v-show="age>=18">点我</button>
</div>
==============
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script type="text/javascript">

    var vm = new Vue({
        el:"#app",
        data:{
            isShow:false
            age:16
        }
    });
</script>
```

- 当v-show="判断语句"，当判断语句结果为true时，才会显示
- 若使用v-if来完成，需要使用dom，性能消耗比较大



### 三、Vue组件[Vue.component]

组件是可复用的`Vue`实例， 就是一组可以重复使用的模板。例如，你可能会有页头、侧边栏、内容区等组件，每个组件又包含了其它的像导航链接、博文之类的组件。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200616171528707.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Bhbl9oMTk5NQ==,size_16,color_FFFFFF,t_70)

##### 1、第一个Vue组件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<div id="app">
    <xxx></xxx>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script type="text/javascript">
    //先注册组件
    Vue.component("xxx",{

        template:'<li>hello</li>'

    });
    //再实例化Vue
    var vm = new Vue({
        el:"#app",
    });
</script>

</body>
</html>
```

- 语法：Vue.component("组件名",{template:组件组成})；

- 相当于提前定制好一个前端页面的组件，可以增强代码的复用性，使前端页面更加简洁
- `Vue.component()`：注册组件
- `template`：组件的模板

##### 2、组件传参

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<div id="app">
    <xxx v-bind:property="message"></xxx>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script type="text/javascript">
    //先注册组件
    Vue.component("xxx",{
        props:["property"],
        template:'<li>{{property}}</li>'

    });
    //再实例化Vue
    var vm = new Vue({
        el:"#app",
        data:{
            message: "hello",
        }
    });
</script>

</body>
</html>
```

- 语法：Vue.component("组件名",{props:["内置属性"],template:组件组成})；
- 对应使用组件时，需要用v-bind绑定内置属性，v-bind:内置属性名=Vue对象的数据中的属性名
- 组件中的内置属性名与组件中取值{{}}的属性名要一致



### 四、Axios异步通信[mounted属性]

`Vue.js`是一个视图层框架并且作者(尤雨溪) 严格准守SoC(关注度分离原则)所以`Vue.js`并不包含AJAX的通信功能， 为了解决通信问题， 作者单独开发了一个名为`vue-resource`的插件， 不过在进入2.0版本以后停止了对该插件的维护并推荐了`Axios`框架。

`Axios`：易用、简洁且高效的http库(内部就是ajax)

##### 1、第一个Axios程序

创建一个json数据

```json
{
  "name": "狂神说Java",
  "url": "https://blog.kuangstudy.com",
  "page": 1,
  "isNonProfit": true,
  "address": {
    "street": "含光门",
    "city": "陕西西安",
    "country": "中国"
  },
  "links": [
    {
      "name": "bilibili",
      "url": "https://space.bilibili.com/95256449"
    },
    {
      "name": "狂神说Java",
      "url": "https://blog.kuangstudy.com"
    },
    {
      "name": "百度",
      "url": "https://www.baidu.com/"
    }
  ]
}
```

编写Axios程序

```html
<!DOCTYPE html>
<html lang="en" xmlns:v-binf="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>


<div id="vue">
    <div>地名：{{info.name}}</div>
    <div>地址：{{info.address.country}}--{{info.address.city}}--{{info.address.street}}</div>
    <div>链接：<a v-binf:href="info.url" target="_blank">{{info.url}}</a> </div>
</div>

<!--引入js文件-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script type="text/javascript">
    var vm = new Vue({
        el:"#vue",
        data(){
            return{
                info:{
                    name:null,
                    address:{
                        country:null,
                        city:null,
                        street:null
                    },
                    url:null
                }
            }
        },
        mounted(){//钩子函数
            axios
                .get('data.json')
                .then(response=>(this.info=response.data));
        }
    });
</script>
</body>
</html>
```

- `<script src="https://unpkg.com/axios/dist/axios.min.js"></script>`:导入axios在线依赖
- 通过mounted属性去获取axio，通过get方法获取json数据，通过then去绑定要渲染的Vue的data中info的属性值（链式函数）
- 使用axios框架的get方法请求AJAX并自动将数据封装进了Vue实例的数据对象中
- 我们在data中的数据结构必须和`Ajax`响应回来的数据格式匹配！

语法补充：

```html
==get请求==
axios.get(地址).then(function(response){},function(err){});

==post请求==
axios.post(地址,参数对象).then(function(response){},function(err){});
```

### 五、计算属性[methods属性]

##### 1、计算属性（Vue特色）

计算属性的重点突出在`属性`两个字上(属性是名词)，首先它是个`属性`其次这个属性有`计算`的能力(计算是动词)，这里的`计算`就是个函数：简单点说，它就是一个能够将计算结果缓存起来的属性(将行为转化成了静态的属性)，仅此而已；可以想象为缓存!

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--view层，模板-->
<div id="app">
    <p>currentTime1:{{currentTime1()}}</p>
    <p>currentTime2:{{currentTime2}}</p>
</div>

<!--1.导入Vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script type="text/javascript">
    var vm = new Vue({
        el:"#app",
        data:{
            message:"pan"
        },
        methods:{
            currentTime1:function(){
                return Date.now();//返回一个时间戳
            }
        },
        computed:{
            currentTime2:function(){//计算属性：methods，computed方法名不能重名，重名之后，只会调用methods的方法
                this.message;
                return Date.now();//返回一个时间戳
            }
        }
    });
</script>
</body>
</html>
```

注意：methods和computed里的东西不能重名
说明：

- methods：定义方法， 调用方法使用currentTime1()， 需要带括号

- computed：定义计算属性， 调用属性使用currentTime2， 不需要带括号：this.message是为了能够让currentTime2观察到数据变化而变化

- 如果在方法中的值发生了变化，则缓存就会刷新。

结论：
  调用方法时，每次都需要讲行计算，既然有计算过程则必定产生系统开销，那如果这个结果是不经常变化的呢?此时就可以考虑将这个结果缓存起来，采用计算属性可以很方便的做到这点，计算属性的主要特性就是为了将不经常变化的计算结果进行缓存，以节约我们的系统开销；











