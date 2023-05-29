# web代码开头部分

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body  onclick="alert(132)">
hello
<button onclick="alert(123)">按钮</button>
hard<br/>h<hr/>ard
</body>
</html>
```

!DOCTYPE html 表示约束

html					标签表示开始

html lang="zh_CN" 表示中文 

html lang="en" 表示英文

包含三部分 title标签 css样式 js代码

bgcolor背景颜色 

onclick  表示单击(点击)事件 

alert()是警告框函数 可以接受任意参数 参数就是警告框内的内容

br 换行 hr水平线

![image-20220908213427234](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20220908213427234.png)

---



# 标签<>

标签不能嵌套 对 <div><spa sd an> 123</spa sd an></div>

​                        错 <div><spa sd an>123</div></spa sd an>

<br/> /为结束 这样就是单标签

注释不能嵌套

<font>字体标签 coloer字体颜色 face字体宋体or... size字体大小

---



## 特殊字符

```
<body>
   
   <!-- 特殊字符 -->

我是&lt;br&gt;标签
   
</body>
```

常用特殊字符

<     代表&lt；

 <代表 &gt；

空格代表 &nbsp；(因为多个空格 如n    b只会加载其中一个 所以得用特殊字符 n&nbsp；&nbsp；b)

---



## 标题标签

```
<body>
	
	<!-- 标题标签 -->
	<h1 align="left">标题1</h1>
	<h2 align="center">标题2</h2>
	<h3 align="right">标题3</h3>
	<h4>标题4</h4>
	<h5>标题5</h5>
	<h6>标题6</h6>
```

h1~h6都是标题标签

h1最大 h6最小

align属性是对齐属性

left 左对齐 center剧中 right 右对齐

---



## 超链接

```
<body>

   <!-- 超链接 -->
   <a href="http://www.baidu.com">百度</a>
   <a href="http://www.baidu.com" target="_self">百度_self</a>
   <a href="http://www.baidu.com" target="_blank">百度_blank</a>

</body>
```

a标签是超链接

href属性设置连接地址

target属性设置怎么跳转 _self 表示当前页面打开 _blank表示打开新页面

---



## 列表标签

```
<ul type="none">
    <li>赵四</li>
    <li>刘能</li>
    <li>小沈阳</li>
    <li>宋小宝</li>

</ul>
<ol>
    <li>赵四</li>
    <li>刘能</li>
    <li>小沈阳</li>
    <li>宋小宝</li>
</ol>
```

ul是无序列表

type属性可以修改列表项前面的符号类型

li是列表项

ol是有序列表 就是前面有数字

![image-20220908222158455](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20220908222158455.png)

---



## img标签

```
<img src="../imgs/1.jpg" width="100" height="120" border="1" alt="123"/>
<img src="../imgs/2.jpg" width="100" height="120"/>
<img src="../imgs/3.jpg" width="100" height="120"/>
<img src="../imgs/4.jpg" width="100" height="120"/>
<img src="../imgs/5.jpg" width="100" height="120"/>
<img src="../imgs/6.jpg" width="100" height="120"/>
```

**重！！**img标签是图片标签,用来显示图片
    src属性可以设置图片的路径
在JavaSE中路径也分为相对路径和络对路径.

相对路径:  从工程名开始算

绝对路径:  盘符:/目录/文件名

在<u>web</u>中路径分为相对路径和绝对路径两种

相对路径:

​     .    	 表示当前文件所在的目录
​     ..    	表示当前文件所在的上一级目录
文件名	表示当前文件所在目录的文件,相当于./文件名   ./可以省略
绝对路径:
​      正确格式是:    http : //ip:port/工程名/资源路径
​      错误格式是:    盘符:/目录/文件名

属性border 设置边框大小 1为像素

属性alt 当指定路径找不到图片时没用来替代显示的文本内容

---

## 表格标签

```
<table border="1" width="300" height="300" align="center" cellspacing="0">
   <tr>
      <th>1.1</th>
      <th>1.2</th>
      <th>1.3</th>
   </tr>
   <tr>
      <td>2.1</td>
      <td>2.2</td>
      <td>2.3</td>
   </tr>
   <tr>
      <td>3.1</td>
      <td>3.2</td>
      <td>3.3</td>
   </tr>
</table>
   
```

table 标签是表格标签

​			cellspacing设置单元格间距

tr 是行标签

th 是表头标签 等于  <td align="center"><b>可直接居中加粗

td 是单元格标签  align 设置单元格文本对齐方式

b 是加粗标签

```
<table  width="500" height="500" cellspacing="0" border="1">
   <tr>
      <td colspan="2">1.1</td>
      <td>1.3</td>
      <td>1.4</td>
      <td>1.5</td>
   </tr><tr>
      <td rowspan="2">2.1</td>
      <td>2.2</td>
      <td>2.3</td>
      <td>2.4</td>
      <td>2.5</td>
   </tr><tr>
      <td>3.2</td>
      <td>3.3</td>
      <td>3.4</td>
      <td>3.5</td>
   </tr><tr>
      <td>4.1</td>
      <td>4.2</td>
      <td>4.3</td>
      <td colspan="2" rowspan="2">4.4</td>
   </tr><tr>
      <td>5.1</td>
      <td>5.2</td>
      <td>5.3</td>
   </tr>
</table>
```

colspan属性表示跨行  

rowspan属性表示跨列

![image-20220910164333824](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20220910164333824.png)



## iframe标签

```
<iframe src="3.标题标签.html" width="500" height="400" name="abc"></iframe>
<br/>
<br/>
<ul>
    <li><a href="0-标签语法.html" target="abc">0-标签语法.html</a></li>
    <li><a href="1.font标签.html" target="abc">1.font标签.html</a></li>
    <li><a href="2.特殊字符.html" target="abc">2.特殊字符.html</a></li>
</ul>
```

iframe标签可在页面上开辟一块区域显示一个单独的页面

在iframe中使用name属性定义一个名称 用a标签的target属性设置name的名称可把a中链接在iframe区域中显示出来

![image-20220910165753630](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20220910165753630.png)

---

## 表单标签

```
<form action="http://www.baidu.com" method="get">
    <input type="hidden" name="action" value="login"/>
    <h1>用户注册</h1>
    <table>
        <tr>
            <td>用户名称:</td>
            <td><input type="text" value="默认值" name="username"></td>
        </tr>
        <tr>
            <td>用户密码:</td>
            <td><input type="password" value="123" name="password"></td>
        </tr>
        <tr>
            <td>确认密码:</td>
            <td><input type="password" value="123"></td>
        </tr>
        <tr>
            <td>性别:</td>
            <td><input type="radio" name="sex" checked="checked" value="boy"/>男
                <input type="radio" name="sex" value="girl"/>女</td>
        </tr>
        <tr>
            <td>兴趣爱好:</td>
            <td><input type="checkbox" name="hobby" value="java">Java
                <input type="checkbox" name="hobby" value="python">python
                <input type="checkbox" name="hobby" value="c++">C++</td>
        </tr>
        <tr>
            <td>国籍:</td>
            <td><select name="country">
                <option>--请选择国籍--</option>
                <option selected="selected">中国</option>
                <option>美国</option>
                <option>日本</option>
                <option>德国</option>
            </select></td>
        </tr>
        <tr>
            <td>自我评价:
            <td><textarea name="desc" rows="10" cols="20"/>我是默认值</textarea></td>
        </tr>
        <tr>
            <td><input type="reset"/></td>
            <td><input type="submit"/></td>
        </tr>
    </table>
```

form标签就是表单            **!   !   !**通常使用表单时用表格更整齐

​			input type=text 是文件输入框 value设置默认显示内容

​			input type=password是密码输入框 value设置默认显示内容			

​			input type=radio是单选框 name属性可以对其进行分组 			

​			input type=checkbox 是复选框 勾选框 check="check"表示默认选中

​			select 标签是下拉列表框

​			option 标签是下拉列表框中的选项 select=select表示默认选中

​			textarea表示多行文本输入框(起始标签和结束标签中的内容激素默认值)   rows属性设置可以显示几行的高度   cols属性设置每行可以显示几个字符宽度

​			input type=reset 是重置按钮 value属性修改按钮上的文本

​			input type=submit 是提交按钮

​			input type=file是文件上传域

​			input type=hidden是隐藏域 当我们要发送某些信息，而这些信息，不需要用户参与，就可以使用隐藏域(提交的时候同时发送给服务器)

### 表单提交

form标签是表单标签

​		action属性设置提交的服务器地址

​		method属性设置提交的方式GET(默认值)或POST

​				GET诗求的特点是:

​				1.浏览器地址栏中的地址是:action属性[+?+请求参数]

​					请求参数的格式是:name=value&name=value

​				2.不安全

​				3.它有数据长度的限制

​				https://www.baidu.com/?action=login&username=123&password=123456&sex=boy&hobby=java&hobby=python&country=%E4%B8%AD%E5%9B%BD&desc=aaa

​				POST请求的特点是:

​				1.浏览器地址栏中只有action属性值

​				2.相对于GET请求要安全

​				3.理论上没有数据长度的限制

​				https://www.baidu.com/

表单提交的时候,数据没有发送给服务器的三种情况:

1、表单项没有name属性值

2、单选、复选（下拉列表中的option标签）都需要添加value属性，以便发送给服务器

3、表单项不在提交的form标签中



## 其他标签

```
<div>div标签1</div>
<div>div标签2</div>
<span>span标签1</span>
<span>span标签2</span>
<p>p段落标签1</p>
<p>p段落标签2</p>1
```

div标签		默认独占一行

span标签	它的长度是封装数据的长度

p段落标签	默认会在段落的上方或下方各空出一行来（如果已有就不再空)

![image-20220911155156645](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20220911155156645.png)