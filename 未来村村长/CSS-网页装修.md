# 前端基础【CSS-网页装修】

`内容和表现分离 样式丰富 利于SEO`



## 一、导入方式



#### 基础语法Style

```css
选择器{
    声明1;
    声明2;
}
```

*每一个声明用分号结尾

*选择器选中相应的模块



**优先级 （就近原则）：和与应用的模块的顺序距离有关，一般情况：行内>内部>外部**

#### 1、内部样式

在html中：

```css
<style>
    h1{
        color:red;
    }
</style>
```



#### 2、外部（独立css文件）样式—链接式

可以在html文件中引进css文件，这样在css文件中就不用<style>标签

```html
<link rel="stylesheet" href="文件路径加文件名">
```



####  3、**行内样式：**

在标签元素中编写一个style属性，编写样式即可，有多个用分号结尾

```html
<h1 style="color:blue">我是标题</h1>
```



## 二、选择器

`定位到相应的模块`

### (一)基本选择器

**==优先级：id>类>标签==**

#### 1、标签选择器

`应用范围：标签相同，则全覆盖应用`

```css
h1{
}
```



#### 2、类（class）选择器

`应用范围：可以归类，跨标签使用`

```
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .pl{
        color:red;
        }
        .zky{
        color:blue;
        }
    </style>
</head>
<body>
<h1 class="pl">大屁股</h1>
<h2 class="pl">大屁股</h2>
<h1 class="zky">大屁股</h1>
</body>
</html>
```



#### 3、id选择器

`应用范围：指定id应用，唯一性`

```css
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        #id名称{
        color:pink;
        }
    </style>
</head>
<body>
<h1 class="pl" id="id名称">大屁股</h1>
</body>
</html>
```



### （二）层次选择器

#### 1、后代选择器

标签名 标签名{ }

`应用范围：在标签后的所有元素`

```css
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body ul{
            background:pink;
        }
    </style>
</head>
<body>
<p>p1</p>
<p>p2</p>
<p>p3</p>
<ul>
    <li>
        <p>p3</p>
    </li>
    <li>
        <p>p4</p>
    </li>
    <li>
        <p>p5</p>
    </li>
</ul>
</body>
</html>
```



#### 2、子代选择器

标签名>标签名{ }

`应用范围：在标签后一代`

```
<style>
    body>p{
        background:pink;
    }
</style>
```



#### 3、弟弟选择器

选择该标签 + 标签名{}

`应用范围：相邻且向下的一个标签`

```css
<style>
    .active + p{
        background:pink;
    }
</style>
```



#### 4、通用选择器

`应用范围：相邻向下的所有标签`

```css
.active~p{
    background:pink;
}
```
### （三）结构伪类选择器

ul li:first-child   ==-==ul下li同等级的第一个元素

ul li:last-child   ==-==ul下li同等级的第一个元素最后一个子元素

 p:nth-child(1)  ==-==p同等级第一个元素

p:nth-of-type(1)   ==-==p同等级该类型为p的元素

```css
    <style>
        ul li:first-child{
            background:pink;
        }

        p:nth-child(1){
            background:pink;
        }
    </style>
```



##### 伪类选择器：

鼠标移动到该位置才会显示出设定样式

```css
/*鼠标悬停*/
a:hover{
    color:yellow;
    font-size:100px;
}

/*鼠标按住*/
a:active{
    color:green;
}

/*点击过后*/
a:visited{
    color:blue;
}
```



### （四）属性选择器



#### 1、基本语法：

**标签名[属性名 运算符 "相应内容"]{语句块}**



#### 2、运算符：

>=   全等于
>
>*= 包含...
>
>^= 以...开头
>
>$= 以...结尾



#### 3、代码例子：


```css
a[id="1"]{
    background:blue;
}
a[class="c"]{
    background:green;
}
a[class*="c"]{
    background:blue;
}
a[class^="c"]{
    background:blue;
}
a[href$="doc"]{
    background:yellow;
}
```



## 三、样式

span标签：重点要突出的字，用span套起来

### 1、字体样式

```css
<style>
body{
font-family:楷体,Arial Black;
font-size:25px;
font-weight:bold;
color:#999adad9;
text-indent:50px;
}
</style>
```

| [font](https://www.runoob.com/cssref/pr-font-font.html)      | 在一个声明中设置所有的字体属性       |
| ------------------------------------------------------------ | ------------------------------------ |
| [font-family](https://www.runoob.com/cssref/pr-font-font-family.html) | 指定文本的字体系列                   |
| [font-size](https://www.runoob.com/cssref/pr-font-font-size.html) | 指定文本的字体大小                   |
| [font-style](https://www.runoob.com/cssref/pr-font-font-style.html) | 指定文本的字体样式                   |
| [font-variant](https://www.runoob.com/cssref/pr-font-font-variant.html) | 以小型大写字体或者正常字体显示文本。 |
| [font-weight](https://www.runoob.com/cssref/pr-font-weight.html) | 指定字体的粗细。                     |



### 2、文本样式

```css
<style>
body{
font-family:楷体,Arial Black;
font-size:25px;
font-weight:bold;
text-align:middle;
color:#999adad9;
text-shadow:#4654 10px -10px 2px;/*阴影颜色 水平偏移 垂直偏移 阴影半径*/
}
p{
color:rgb(0,255,100,0.2);/*0.2为透明度*/
text-align:left;/*排版方式center\left\right\justify*/
text-indent:6em;/*首行缩进*/
}
h1 {text-decoration:overline;}
h2 {text-decoration:line-through;}
h3 {text-decoration:underline;}
</style>
```



| 属性                                                         | 描述                     |
| :----------------------------------------------------------- | :----------------------- |
| [color](https://www.runoob.com/cssref/pr-text-color.html)    | 设置文本颜色             |
| [direction](https://www.runoob.com/cssref/pr-text-direction.html) | 设置文本方向。           |
| [letter-spacing](https://www.runoob.com/cssref/pr-text-letter-spacing.html) | 设置字符间距             |
| [line-height](https://www.runoob.com/cssref/pr-dim-line-height.html) | 设置行高                 |
| [text-align](https://www.runoob.com/cssref/pr-text-text-align.html) | 对齐元素中的文本         |
| [text-decoration](https://www.runoob.com/cssref/pr-text-text-decoration.html) | 向文本添加修饰           |
| [text-indent](https://www.runoob.com/cssref/pr-text-text-indent.html) | 缩进元素中文本的首行     |
| [text-shadow](https://www.runoob.com/cssref/css3-pr-text-shadow.html) | 设置文本阴影             |
| [text-transform](https://www.runoob.com/cssref/pr-text-text-transform.html) | 控制元素中的字母         |
| [unicode-bidi](https://www.runoob.com/cssref/pr-text-unicode-bidi.html) | 设置或返回文本是否被重写 |
| [vertical-align](https://www.runoob.com/cssref/pr-pos-vertical-align.html) | 设置元素的垂直对齐       |
| [white-space](https://www.runoob.com/cssref/pr-text-white-space.html) | 设置元素中空白的处理方式 |
| [word-spacing](https://www.runoob.com/cssref/pr-text-word-spacing.html) | 设置字间距               |



### 3、超链接样式

```css
a:link {background-color:#B2FF99;
		text-decoration:none;/*删除下划线*/
		}    /* 未访问链接 */
a:visited {background-color:#FFFF85;} /* 已访问链接 */
a:hover {background-color:#FF704D;}   /* 鼠标移动到链接上 */
a:active {background-color:#FF704D;}  /* 鼠标点击时 */
```



### 4、背景样式

linear-gradient(19deg,#21D4FD 0%,#B721FF 100%);渐变图片创建

```css
div{
    width:500px;
    height:300px;
    border:1px solid red;
    background-image:linear-gradient(19deg,#21D4FD 0%,#B721FF 100%);
}
```

| Property                                                     | 描述                                         |
| :----------------------------------------------------------- | :------------------------------------------- |
| [background](https://www.runoob.com/cssref/css3-pr-background.html) | 简写属性，作用是将背景属性设置在一个声明中。 |
| [background-attachment](https://www.runoob.com/cssref/pr-background-attachment.html) | 背景图像是否固定或者随着页面的其余部分滚动。 |
| [background-color](https://www.runoob.com/cssref/pr-background-color.html) | 设置元素的背景颜色。                         |
| [background-image](https://www.runoob.com/cssref/pr-background-image.html) | 把图像设置为背景。                           |
| [background-position](https://www.runoob.com/cssref/pr-background-position.html) | 设置背景图像的起始位置。                     |
| [background-repeat](https://www.runoob.com/cssref/pr-background-repeat.html) | 设置背景图像是否及如何重复。                 |



### 5、列表样式

```css
.title{
    font-size:15px;
    color:yellow;
}
ul{
background-color:blue
}

ul li{
   height:30px;
   list-style:none;
}

#li{
    width:300px;
}
```



| 属性                                                         | 描述                                               |
| :----------------------------------------------------------- | :------------------------------------------------- |
| [list-style](https://www.runoob.com/cssref/pr-list-style.html) | 简写属性。用于把所有用于列表的属性设置于一个声明中 |
| [list-style-image](https://www.runoob.com/cssref/pr-list-style-image.html) | 将图像设置为列表项标志。                           |
| [list-style-position](https://www.runoob.com/cssref/pr-list-style-position.html) | 设置列表中列表项标志的位置。                       |
| [list-style-type](https://www.runoob.com/cssref/pr-list-style-type.html) | 设置列表项标志的类型。                             |



### 6、盒子模型（重点）

#### 6.1盒子模型理解

![CSS box-model](https://www.runoob.com/images/box-model.gif)

所有HTML元素可以看作盒子，在CSS中，"box model"这一术语是用来设计和布局时使用。

CSS盒模型本质上是一个盒子，封装周围的HTML元素，它包括：边距，边框，填充，和实际内容。

盒模型允许我们在其它元素和周围元素边框之间的空间放置元素。



- **Margin(外边距)** - 清除边框外的区域，外边距是透明的。
- **Border(边框)** - 围绕在内边距和内容外的边框。
- **Padding(内边距)** - 清除内容周围的区域，内边距是透明的。
- **Content(内容)** - 盒子的内容，显示文本和图像。



#### 6.2border边框的样式：粗细、样式、颜色

```css
<style>
/*body总有一个默认的外边框margin*/
    body{
        margin:0;
    }

    form{
        background:yellow;
    }

    h4{
        background:green;
        font-size:16px;
        line-height:20px;
    }

    #box{
        width:300px;
        border:1px solid black;
    }
    div:nth-of-type(1) input{
        border:3px solid black;
    }
    div:nth-of-type(2) input{
        border:3px dashed green;
    }

</style>
```



#### 6.3圆角边框

```css
border-radius:50px 50px 20px;
```

#### 6.4盒子阴影

```css
box-shadow:10px 10px 100px blue;
margin:100px auto;/*auto居中*/
```



### 7、浮动

##### 7.1

块级元素：独占一行

```css
h
p
div
li
```

行内元素：不独占一行

```css
span
a
img
strong
```



##### 7.2 display

block：块元素

inline：行内元素

inline-block：行内块元素



```css
   <style>
    div{
        width:1000px;
        height:120px;
        border:1px solid blue;
        display:inline-block;
        }

    span{
        width:100px;
        height:100px;
        border:1px solid red;
        display:inline-block;
        }

    #pp{
        width:100px;
        height:100px;
        border:1px solid red;
        display:inline-block;
        float:right;
        }
    </style>
```



