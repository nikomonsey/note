### CSS与html结合方法

***style标签专门用来定义css代码样式***

#### 一

```
<div style="border: 1px solid red;">div标签1</div>
<div  style="border: 1px solid red;">div标签2</div>
<span style="border: 1px solid red;">span标签1</span>
<span style="border: 1px solid red;">span标签2</span>
```



**！！重！！**<div> 元素 (或 HTML 文档分区元素) 是一个通用型的流内容容器，在不使用CSS的情况下，其对内容或布局没有任何影响。



这种方式的缺点?

1.如果标签多了。样式多了。代码量非常庞大。

2.可读性非常差。(无分行)

3.Css代码没什么复用性可方言。



#### 二

```
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <style  type="text/css">
        div{
            border: 1px solid red;
        }
        span{
            border: 1px solid red;
        }
    </style>
</head>
<body>


<div>div标签1</div>
<div>div标签2</div>
<span>span标签1</span>
<span>span标签2</span>
</body>
```

在head标签中使用style标签定义各种css

格式 xxx{

​				key:value value;

​				}

这种方式的缺点?

1.只能在同一页面内复用代码，不能在多个页面中复用css代码。

⒉维护起来不方便，实际的项目中会有成千上万的页面，要到每个页面中去修改。工作量太大了。

#### 三

```
<head>
    <meta charset="UTF-8">
    <title>Title</title>
<link rel="stylesheet" type="text/css " href="1.css"/>
</head>
```

把css样式写成一个单独的css文件，再通过link标签引入即可复用。

通过创建stylesheet文件 在1.css文件中写入内容



### CSS选择器



#### 标签名选择器

标签名选择器的格式是:

标签名{

​			属性:值;

​			}

标签名选择器，可以决定哪些标签被动的使用这个样式。



#### id选择器

```
<head>
   <meta charset="UTF-8">
   <title>ID选择器</title>
   <style type="text/css">
      #id001{
         color: blue;
         font-size: 30px;
         border: 1px yellow solid;
      }#id002{
         color: red;
         font-size: 20px;
         border: 5px blue dotted;
      }
   </style>
</head>
<body>    
   <div id="id001">div标签1</div>
   <div id="id002">div标签2</div>
</body>
```

id选择器的格式是:

​			#id属性值{

​								属性:值;

​								}

id选择器,可以让我们通过d属性选择性的去使用这个样式。

#### class选择器

同id选择器差不多

id选择器的格式是:

​			.class属性值{

​								属性:值;

​								}

#### 注注意 

class选择器可与id选择器等共用

组合选择器的格式是:

选择器1，选择器2，选择器n{

​													属性:值;

​													}

组合选择器可以让多个选择器共用同一个css样式代码。

### 常用样式

```
margin-left: auto;
margin-right: auto;
```

div框居中

```
text-align: center;
```

文本内容居中

```
a{
	text-decoration: none
}
```

超链接去下划线

```
table{
            border-collapse: collapse;
        }
```

把边框合并

```
td{
    border: 1px red solid;
}
```

可单独设置td