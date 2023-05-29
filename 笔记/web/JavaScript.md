# JavaScript

## JavaScript与html代码结合方式

### 一

```
<head>
    <script type="text/javascript">
        alert("hello JavaScript!");/*警告框*/
    </script>
</head>
```

只需要在head 标签中，或者在body标签中，使用script标签来书写JavaScript 代码

### 二

```
<script type="text/javascript" src="1.js">
</script>
```

src属性专门用来引入js文件路径（可以是相对路径，也可以是绝对路径)

script标签可以定义js代码，也可以引入js文件，但只能二选一 不能同时用

要用得再写一个script标签

## 变量

JavaScript 的变量类型:
			数值类型:           number
			字符串类型:		string
			对象类型:			object
			布尔类型:			boolean
			函数类型:			function

javascript特殊值

undefined								 未定义，所有js变量未赋于初始值时，默认值都是undefined

NAN											非数字，非数值

定义变量格式

var 变量名;

var 变量名=值; （**JavaScript中定义变量没有类型 通过值来确定类型**）

### 要遵循下面的规则：

组成字符可以是任何字母、数字、下划线(_）或美元符号($)
数字不能开头
建议使用驼峰命名

```
//js为弱类型语言，即变量没有类型 通过值来确定类型
//var x =1;		赋值int类型
//x="张三"	这里也可以直接赋值String类型！！！
    
//特点：作用域大，为全局变量	
//可以重复被定义
    {	//定义在{}代码块中	若在java中则为局部变量，但是js中为全局变量
        var x = 1;
        var x = "ljxxx"
    }
    alert(x);	//输出结果为 "ljxxx"
```



typeof()是JavaScript语言提供的一个函数。

它可以取变量的数据类型返回

```
var a = 12;
var b = "abc";
alert( a * b ); // 输出值为NaN  表示非数字，非数值。
```

取回的类型为NAN就是有数字也有数值



### 注意事项

ECMAScript 6新增了Ilet关键字来定义变量。它的用法类似于var，但是所声明的变量，只在let关键字所在的代码块内有效，且不允许重复声明。

```
//let   定义局部变量，且不能被重复定义
{
    let a = 1;	//局部变量
    a="abc";	//还是若类型，即可以为同一对象赋不同类型的值
    let a = A;	//报错，不能被重复定义
    alert(a);	//输出	因为在代码块中，输出局部变量
}
alert(a);	//不输出，没有找到全局变量a
```

ECMAScript 6新增了const关键字，用来声明一个只读的常量。一旦声明，常量的值就不能改变。

```
const b = 1.5;	//定义常量
b=1.6;		//若执行，执行到这一步会报错无法执行(网站控制台)
alert(b);	//无法输出，在上一行报错
```





### 关系比较运算

```
var a = "12";
var b = 12;

alert( a == b ); // true
alert( a === b ); // false
```

等于         	==  表面比较，会进行类型转换，比较具体的值

全等于    	=== 比较数值和类型，不会进行类型转换



### 逻辑运算

且运算:       &&
或运算:       ll
取反运算:    l

在JavaScript语言中，所有的变量，都可以做为一个 boolean类型的变量去使用。

0 ，null，undefined，""(空串 **是空 不是空格**）都认为是false;

其余都为真

```
var a = "abc";
var b = true;
var d = false;
var c = null;

// alert( a && b );//true
// alert( b && a );//true
// alert( a && d ); // false
// alert( a && c ); // null
```

&& 且运算有两种情况：
      第一种：当表达式全为真的时候。返回最后一个表达式的***值***。
      第二种：当表达式中，有一个为假的时候。返回第一个为假的表达式的***值***

**（返回的是值）**

```
var a = "abc";
var b = true;
var d = false;
var c = null;

// alert( d || c ); // null
// alert( c|| d ); //false
// alert( a || c ); //abc
// alert( b || c ); //true
```

|| 或运算
      第一种情况：当表达式全为假时，返回最后一个表达式的值
      第二种情况：只要有一个表达式为真。就会把回第一个为真的表达式的值

**（返回的是值）**



## 数据类型

分为原始数据和引用数据

### 原始数据

number:数字（整数、小数、NaN(Not a Number)，即非数字非数值)
string:字符串，单双引皆可
boolean:布尔。true，falsenull:对象为空
undefined:当声明的变量未初始化时，该变量的默认值是undefined

```
//原始数据类型
    var b=123;
    var c = "ljxxx";
    alert(typeof (b*c));    //输出number！！！！整数*String 不是数字也不是数值，所以输出number
    alert(typeof 3);    //输出number
    alert(typeof 3.14); //输出number

    alert(typeof "A");  //输出String
    alert(typeof 'Hello');  //输出String

    alert(typeof true);     //输出boolean
    alert(typeof false);    //输出boolean

    alert(typeof null);     //比较特殊 输出Object(早期js的一个错误，被沿用至今)

    var a ;
    alert(typeof a);        //输出undefined
```



### 类型转换

- 字符串类型转为数字:
  - 将字符串字面值转为数字。如果字面值不是数字，则转为NaN。

- 其他类型转为boolean:
  - Number: 	0和NaN 转为false，其他均 转为true。
  - String:    ""空字符串为false(不是空格字符" ")，其他均 转为true。
  - Null和undefined :均转为false.

```
// 类型转换 - 其他类型转为数字
    // alert(parseInt("12")); 
    // alert(parseInt("12A45")); 
    // alert(parseInt("A45"));


    // 类型转换 - 其他类型转为boolean
    // if(0){	//若0转换为true则执行下方语句
    //     alert("0 转换为false");//不执行
    // }
    // if(NaN){
    //     alert("NaN 转换为false");//不执行
    // }
    // if(1){
    //     alert("除0和NaN其他数字都转为 true");//执行
    // }

    // if(""){
    //     alert("空字符串为 false, 其他都是true");//不执行
    // }
    
 	// if("ljxxx"){
    //     alert("\"ljxxx\"字符串转换为true");//执行
    // }
        
    // if(null){
    //     alert("null 转化为false");//不执行
    // }
    // if(undefined){
    //     alert("undefined 转化为false");//不执行
    // }
```







## 函数

两种定义方式

### 第一种定义格式

```js
function 函数名(参数1,参数2..){
    要执行的代码
}

//定义函数
function add(a,b){
    return a+b;
}

//调用函数，接收返回值
var result = add(10,20);
alert(result);
```

因为js为弱数据类型的语言，所以有如下几点需要注意：

- 参数不需要写声明类型，js定义参数写的是var let 不管声明什么类型都没有意义
- 返回值也不需要声明类型，直接在函数内部return就行



### 第二种定义格式

```js
var functionName = function (参数1,参数2..){   
	//要执行的代码
}

//定义函数
var add = function(a,b){
    return a+b;
}

//调用函数，接收返回值
var result = add(10,10);
alert(result);
```

**注** js不允许函数重载





### 隐藏参数

```
function fun(){
		alert(arguments.length);
		alert(arguments[0]);
		alert(arguments[1]);
		alert("无参函数fun()");
}
fun(1,"ab")
```

函数的arguments隐形参数（只在function函数内)

就是在 function函数中不需要定义，但却可以直接用来获取所有参数的变量。我们管它叫隐形参数。

和数组用法一样

**！！！**用于不知道具体几个参数时

```
// 需求：要求 编写 一个函数。用于计算所有参数相加的和并返回
function sum(num1,num2) {
    var result = 0;
    for (var i = 0; i < arguments.length; i++) {
        if (typeof(arguments[i]) == "number") {//用于确定相加的为数字而不是字符
            result += arguments[i];//result=arguments[i]+result
        }
    }
    return result;
}

alert( sum(1,2,3,4,"abc",5,6,7,8,9) );
```



## JavaScript对象

js中的对象可分为三类：

基本对象(Arrays，JSON，String)
BOM对象，主要是和浏览器相关的几个对象
DOM对象，JavaScript中将html的每一个标签都封装成一个对象

### 基本对象

#### 数组(arrays)对象

***语法格式***

方式1：

~~~js
var 变量名 = new Array(元素列表); 

var arr = new Array(1,2,3,4); //1,2,3,4 是存储在数组中的数据（元素）
~~~

方式2：

~~~js
var 变量名 = [ 元素列表 ]; 

var arr = [1,2,3,4]; //1,2,3,4 是存储在数组中的数据（元素）
~~~

而通过数组对象 arr[索引] 可以取出数组中的值	若取出没有赋值索引位置的值，则会输出undefined

```js
var arr = new Array(1,2,3,4);
    var arr =[1,2,3,4];
    alert(arr[0]);
    alert(arr[3]);
```

**特点**

与java中不一样的是，JavaScript中数组相当于java中的集合，数组的长度是可以变化的。而且JavaScript是弱数据类型的语言，所以数组中可以存储任意数据类型的值。

即js中的数组是可以扩容的，即使一开始定义的是空数组也可直接扩容，而扩容只有在赋值的时候才会

```js
var arr = new Array(1,2,3,"123");
arr[10]=10;
alert(arr[0]);	//输出1
alert(arr[3]);	//输出123
alert(arr[5]);	//输出undefined
alert(arr[10]);	//输出10
```

**属性与方法**

属性：

| 属性   | 描述                         |
| :----- | :--------------------------- |
| length | 设置或返回数组中元素的数量。 |

方法：

| 方法方法  | 描述                                             |
| :-------- | :----------------------------------------------- |
| forEach() | 遍历数组中的每个有值得元素，并调用一次传入的函数 |
| push()    | 将新元素添加到数组的末尾，并返回新的长度         |
| splice()  | 从数组中删除元素                                 |

- length属性：

  length属性可以用来获取数组的长度，所以我们可以借助这个属性，来遍历数组中的元素，添加如下代码：

```js
var arr = new Array(1,2,3,4);
arr[10]=10;
alert(arr.length);	//输出11
for(let i =0;i<arr.length;i++){
    console.log(arr[i]);	//依次输出
}
```

- forEach()函数
  用于遍历，传入参数e，该参数就为每次循环遍历的值

```js
var arr = new Array(1,2,3,4);
arr[10] = 10;
//forEach 和for循环相比，不会输出undefined的值，即不会输出没赋值索引位的值
arr.forEach(function(e){
    console.log(e);	//输出1，2，3，4，10 不输出undefined
})

//在ES6中，引入箭头函数的写法
arr.forEach(e=>{
    console.log(e);	//结果与上面相同
})
```

- push()函数
  在数组末尾加上元素，并返回新的长度值

```js
var arr = new Array(1,2,3,4);
arr[10] = 10;
var length = arr.push(5,6,7);
alert(length);	//输出14
console.log(arr);	//输出1，2，3，4，10，5，6，7
```

- splice()函数
  删除元素	第一个参数为要删除元素索引值，第二个参数为要删除几个元素

```js
var arr = new Array(1,2,3,4);
arr[10] = 10;
arr.splice(2,2);	//删除索引值为2，删除两个元素，即删除3，4
console.log(arr);	//输出1，2，10
```

#### String对象

**语法格式**

```js
var 变量名 = new String("xxx");

var str = new String("ljxxx");

或者
var 变量名 = "ljxxx";

var str = "ljxxx";
```

**属性与方法**

属性：

| 属性   | 描述           |
| ------ | -------------- |
| length | 字符串的长度。 |

方法：

| 方法        | 描述                                     |
| ----------- | ---------------------------------------- |
| charAt()    | 返回在指定位置的字符。                   |
| indexOf()   | 检索字符串。                             |
| trim()      | 去除字符串两边的空格                     |
| substring() | 提取字符串中两个指定的索引号之间的字符。 |

- charAt()方法	传入索引值，返回该索引值位置的字符
- indexOf()方法    传入字符，返回该字符在字符串中的索引值，若在字符串中不存在，则返回-1

```js
var str = "ljxxx";
alert(str.indexOf("j"));	//返回1
alert(str.indexOf("z"));	//返回-1，因为在字符串中找不到
```

- substring()方法	传入两个参数[x,y)从x位置开始截取，到y位置，不包含y



#### 自定义对象

js中自定义对象 类似于java中的新建一个类！！

~~~js
var 对象名 = {
    属性名1: 属性值1, 
    属性名2: 属性值2,
    属性名3: 属性值3,
    函数名称: function(形参列表){}
};

var user = {
    name : "ljxxx",
    age : 12,
    add:function(){
    console.log("ljxxx和12");
    }
}
console.log(user.name);	//输出ljxxx
user.add();	//输出ljxxx和12

~~~



#### Object对象

和自定义对象类似，只是没有类名

```js
对象的定义：
var 变量名=new Object();		//对象实例(空对象)
变量名.函数名=值; 				   //定义一个属性
变量名.函数名=function(){}	//定义一个函数

对象的访问：
变量名.属性/属性名();

var obj = new Object();
obj.name = "华仔";
obj.age = 18;
obj.fun = function () {
    alert("姓名：" + this.name + " , 年龄：" + this.age);
}
// alert( obj.age );
obj.fun();
```



#### json对象

JSON对象：**J**ava**S**cript **O**bject **N**otation，JavaScript对象标记法。是通过JavaScript标记法书写的文本。其格式如下：

~~~js
{
    "key":value,
    "key":value,
    "key":value
}

实例：
{
    "name":"Tom"，
    "age":18, 
    "addr":["北京","上海","西安"],
    "user":{"name":"ljxxx"}
}

json数据中
value的数据类型为:
数字（整数或浮点数)
字符串(在双引号中)
逻辑值(true或 false)
数组(在方括号中)
对象（在花括号中)null		对象中的属性也要用双引号括起来！！！ 否则语法错误

~~~

其中，**key必须使用引号并且是双引号标记，value可以是任意数据类型。**

一定要双引号！！！	定义对象中的属性也要用双引号括起来

json数据通常用于作为前后台交互的数据载体，即前后端传递数据时使用！！

##### 实例(重！！)：

```js
var jsonstr ='{"name": "Tom","age": 18,"addr": ["北京", "上海", "西安"],"user": {"name": "ljxxx","age": 12}}';	
//这里不能分行写，因为前端没有+拼接的语法！！！	直接一行写
{
	"name":"Tom"，
    "age":18, 
    "addr":["北京","上海","西安"]
}错误写法
alert(jsonstr.name);	//输出undefined
```

这时输出jsonstr.name输出undefined，因为这时jsonstr为字符串，而不是json对象，需要转换

##### 字符串-->JSON	

JSON.parse(字符串对象)

```js
var jsonstr = '{"name":"Tom", "age":18, "addr":["北京","上海","西安"]}';
var obj = JSON.parse(jsonstr);
alert(obj.name);
```



##### JSON-->字符串

JSON.stringify(json对象)

```js
var jsonstr = '{"name":"Tom", "age":18, "addr":["北京","上海","西安"]}';
var obj = JSON.parse(jsonstr);
alert(obj.name);	//输出Tom
var str =JSON.stringify(obj);
 alert(str.name);	//输出undefined
```



#### BOM对象

BOM的全称是Browser Object Model,翻译过来是浏览器对象模型。也就是JavaScript将浏览器的各个组成部分封装成了对象，便于我们操作，例如：我们想要将浏览器的地址改为`http://www.baidu.com`,我们就可以通过BOM中提供的location对象的href属性来完成，代码如下：`location.href='http://www.baidu.com'`

BOM中提供了如下5个对象：

| 对象名称  | 描述           |
| :-------- | :------------- |
| Window    | 浏览器窗口对象 |
| Navigator | 浏览器对象     |
| Screen    | 屏幕对象       |
| History   | 历史记录对象   |
| Location  | d地址栏对象    |

重点学习Window和Location

##### Window对象

对于window对象，我们可以直接使用

~~~
window.alert('hello');
~~~

其可以省略window.  所以可以简写成

~~~
alert('hello')
~~~

window对象提供了获取其他BOM对象的属性：

| 属性      | 描述                  |
| --------- | --------------------- |
| history   | 用于获取history对象   |
| location  | 用于获取location对象  |
| Navigator | 用于获取Navigator对象 |
| Screen    | 用于获取Screen对象    |

**常用方法**

| 函数          | 描述                                                       |
| ------------- | ---------------------------------------------------------- |
| alert()       | 显示带有一段消息和一个确认按钮的警告框。                   |
| comfirm()     | 显示带有一段消息以及确认按钮和取消按钮的对话框。           |
| setInterval() | 按照指定的周期（以毫秒计）来调用函数或计算表达式。多次调用 |
| setTimeout()  | 在指定的毫秒数后调用函数或计算表达式。只调用一次           |

- **confirm()**方法

~~~js
var flag = confirm("您确认删除该记录吗?");
alert(flag);
~~~

当用户点击确定时，返回true，点击取消，返回false

- setInterval()方法	第一个参数为要执行的方法，第二个参数为间隔时间

```js
var i =0;
setInterval(function(){
	i++;
	console.log("执行了"+i+"次");
},2000);	//每隔2秒执行一次
```

- setTimeout()方法	参数和setInterval一致，只执行一次

```js
setTimeout(function(){
	    alert("JS");
    },3000);	//隔3秒执行一次，只执行一次
```



##### Location对象

location是指代浏览器的地址栏对象，对于这个对象，我们常用的是href属性，用于获取或者设置浏览器的地址信息

```js
alert(location.href);	//输出当前网页url值
location.href = "https://www.baidu.com";	//跳转到百度
```



#### DOM对象

Document Object Model 文档对象模型。也就是 JavaScript 将 HTML 文档的各个组成部分封装为对象。	即前端中 html文件的 各种标签组成<div>等

封装的对象分为

- Document：整个文档对象
- Element：元素对象
- Attribute：属性对象
- Text：文本对象
- Comment：注释对象

如下图，左边是 HTML 文档内容，右边是 DOM 树

![1668796698067](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\1668796698067.png) 



**获取DOM对象**

HTML中的Element对象可以通过Document对象获取，而Document对象是通过window对象获取的。document对象提供的用于获取Element元素对象的api如下表所示：

| 函数                              | 描述                                     |
| --------------------------------- | ---------------------------------------- |
| document.getElementById()         | 根据id属性值获取，返回单个Element对象    |
| document.getElementsByTagName()   | 根据标签名称获取，返回Element对象数组    |
| document.getElementsByName()      | 根据name属性值获取，返回Element对象数组  |
| document.getElementsByClassName() | 根据class属性值获取，返回Element对象数组 |

```js
<body>
    <img id="h1" src="img/off.gif">  <br><br>

    <div class="cls">传智教育</div>   <br>
    <div class="cls">黑马程序员</div>  <br>

    <input type="checkbox" name="hobby"> 电影
    <input type="checkbox" name="hobby"> 旅游
    <input type="checkbox" name="hobby"> 游戏
</body>

<script>
    var img = document.getElementById('h1');
    alert(img);	//输出[object HTMLImageElement]

    var divs = document.getElementsByTagName('div');
    for (let i = 0; i < divs.length; i++) {
        alert(divs[i]);	//输出[object HTMLDivElement]
    }

    var ins = document.getElementsByName('hobby');
    for (let i = 0; i < ins.length; i++) {
        alert(ins[i]);	//输出[object HTMLInputElement]
    }

    var divs = document.getElementsByClassName('cls');
    for (let i = 0; i < divs.length; i++) {
        alert(divs[i]);	//输出[object HTMLDivElement]
    }

</script>
```

通过document.get 获取的是Element元素对象，相当于div img等，而这些标签，也被封装成html对象

通过这些html对象，可以直接操作其属性

```js
var img = document.getElementById('h1');
img.src = "img/on.gif";	//通过id获取img对象，通过img对象的src属性，修改img显示的图片
```

##### 案例：

innerHTML属性 用于控制文本信息
input的checked属性，用于控制复选框的被选中状态

```js
<body>
    <img id="h1" src="img/off.gif">  <br><br>

    <div class="cls">传智教育</div>   <br>
    <div class="cls">黑马程序员</div>  <br>

    <input type="checkbox" name="hobby"> 电影
    <input type="checkbox" name="hobby"> 旅游
    <input type="checkbox" name="hobby"> 游戏
</body>

<script>
    //1. 点亮灯泡 
    var img = document.getElementById('h1');
    img.src = "img/on.gif";

    //2. 将所有div标签的内容后面加上红色的 "very good"文本
    var divs = document.getElementsByTagName('cls');
    for (let index = 0; index < divs.length; index++) {
        const div = divs[index];
        //通过修改div的innerHTML属性 可以修改文本值，而innerHTML属性可以识别<>括号
		//即可以在加入一个标签front，用于修改文本颜色
        div.innerHTML +="<front color = 'red'>very good</front>"
    }

    //3. 使所有的复选框呈现选中状态
    var input = document.getElementsByName('hobby');
    for (let index = 0; index < input.length; index++) {
        const element = input[index];
        element.checked = true;	//通过input的checked属性，控制复选框被选中状态
        
    }

</script>
```









## JS中的事件

### 常用的事件:

onload 加载完成事件:				页面或图像加载完成之后，常用于做页面js代码初始化操作。
onclick单击事件:					 	常用于按钮的点击响应操作。
onblur 失去焦点事件:				常用用于输入框失去焦点后验证其输入内容是否合法。
onfocus										获得焦点事件
onchange内容发生改变事件:	常用于下拉列表和输入框内容发生改变后操作。
onsubmit表单提交事件:			常用于表单提交前，验证所有表单项是否合法。
onkeydown								某个键盘的键被按下
onmouseover							鼠标被移到某元素之上
onmouseout							  鼠标从某元素移开



### 事件注册

​	什么是事件的注册（绑定）?
​	其实就是告诉浏览器，当事件响应后要执行哪些操作代码，叫事件注册或事件绑定。



#### 静态注册

通过html标签的事件属性直接赋予事件响应后的代码

~~~html
<input type="button" id="btn1" value="事件绑定1" onclick="on()">

<script>
    function on(){
        alert("按钮1被点击了...");
    }
</script>
~~~



#### 动态注册

​	是指先通过js代码得到标签的dom对象，然后再通过dom 对象.事件名=function(){}
​		这种形式赋于事件响应后的代码，叫动态注册。

​	动态注册基本步骤:

​		1、获取标签对象

​		2、标签对象.事件名= fucntion(){}

**dom=document**

通过element对象的属性来操作标签的属性。此时我们再次添加一个按钮，代码如下：

~~~html
<input type="button" id="btn2" value="事件绑定2">

<script>
document.getElementById('btn2').onclick = function(){
    alert("按钮2被点击了...");
}
</script>
~~~





### onload

静态

```
<script type="text/javascript">
    // onload事件的方法
    function onloadFun() {
        alert('静态注册onload事件，所有代码');
    }
    </script>
    <body onload="onloadFun();">
```

动态

```
<script>
window.onload = function () {
    alert("动态注册的onload事件");
}
```

**！！**onload事件动态注册。是固定写法

### onclick

静态

```
<script type="text/javascript">
function onclickFun() {
            alert("静态注册onclick事件");
        }
        </script>
<body>
    <!--静态注册onClick事件-->
    <button onclick="onclickFun();">按钮1</button>
    <button id="btn01">按钮2</button>
</body>
```

动态

```
window.onload = function () {
    
    var btnObj = document.getElementById("btn01");
    btnObj.onclick = function () {
        alert("动态注册的onclick事件");
    }
}

<body>
    <button id="btn01">按钮2</button>
</body>
```





### onblur

静态

```
function onblurFun() {
     
    console.log("静态注册失去焦点事件");
}
<body>
		用户名:<input type="text" onblur="onblurFun();"><br/>
</body>
```

console是控制台对象，是由JavaScript语言提供，专门用来向**浏览器的控制器**打印输出， 用于测试使用

log() 是打印的方法



动态

```
window.onload = function () {
    //1 获取标签对象
   var passwordObj = document.getElementById("password");
   // alert(passwordObj);
    //2 通过标签对象.事件名 = function(){};
    passwordObj.onblur = function () {
        console.log("动态注册失去焦点事件");
    }
}
<body>
	密码:<input id="password" type="text" ><br/>
</body>
```



### onchange

静态

```
function onchangeFun() {
            alert("女神已经改变了");
        }

<select onchange="onchangeFun();">
    <option>--女神--</option>
    <option>芳芳</option>
    <option>佳佳</option>
    <option>娘娘</option>
</select>
```



动态

```
window.onload = function () {
    var selObj = document.getElementById("sel01");
    selObj.onchange = function () {
        alert("男神已经改变了");
    }
}

请选择你心中的男神：
    <select id="sel01">
        <option>--男神--</option>
        <option>国哥</option>
        <option>华仔</option>
        <option>富城</option>
    </select>
```



### onsubmit

静态

```
 function onsubmitFun(){
            alert("静态注册表单提交事件----发现不合法");

            return false;
        }

<form action="http://localhost:8080" method="get" onsubmit="return onsubmitFun();">
    <input type="submit" value="静态注册"/>
</form>
```

return false可以阻止表单提交**！！！**

onsubmit函数返回了一个flase值 所有要用return来返回**！！！**

要验证所有表单项是否合法，如果，有一个不合法就阻止表单提交！！



动态

```
window.onload = function () {
    var formObj = document.getElementById("form01");
    formObj.onsubmit = function () {
        // 要验证所有表单项是否合法，如果，有一个不合法就阻止表单提交
        alert("动态注册表单提交事件----发现不合法");

        return false;
    }
}

<form action="http://localhost:8080" id="form01">
        <input type="submit" value="动态注册"/>
    </form>
```

### 注 

所有动态注册流程都一样
window.onload只能用于动态注册时，若静态注册，即在html标签上加方法无法绑定！！！！

window.onload 表示网页加载之后立刻执行的操作
为什么动态注册中要window.onload	因为js中的函数方法要等html文档渲染完成之后才可以只用，若没渲染完，dom树不完整，会报错	所以最好加上window.onload = function(){}

```js
window.onload = function () {
		var a= document.getElementById("xxx");
		a.事件名=function(){
		alert("xxxx");
		}
}
```



### dom

![image-20220914133559586](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20220914133559586.png)

Document对象的理解:

第一点:	Document 它管理了所有的HTML文档内容。

第二点:	document它是一种树结构的文档。有层级关系。

第三点:	它让我们把所有的标签都对象化。

第四点:	我们可以通过document访间所有的标签对象。

第三点解释

```
对象化
<body>
	<div id="div1">div01</div>
</body>
```

模拟对象化，相当于:class Dom{

private String id;					// id属性

private String tagName;		//表示标签名

private Dom parentNode;	//父亲

private List<Dom> children;	//孩子结点

private String innerHTML;	//起始标签和结束标签中间的内容

}



#### getElementById

**重**

返回对拥有指定id的第一个对象的引用。

所以id不能重复！！

```
<script type="text/javascript" >
        /*
        * 需求：当用户点击了较验按钮，要获取输出框中的内容。然后验证其是否合法。<br/>
        * 验证的规则是：必须由字母，数字。下划线组成。并且长度是5到12位。
        * */
        function onclickFun() { 
            var usernameObj = document.getElementById("username");
            // [object HTMLInputElement] 它就是dom对象
            var usernameText = usernameObj.value;
            // 
            var patt = /^\w{5,12}$/;
            /*
            *  
            *  匹配就返回true。不匹配就返回false.
            * */
            if (patt.test(usernameText)) {
                alert("用户名合法！");
            } else {
                alert("用户名不合法！");
            }
        }
    </script>
</head>
<body>
    用户名：<input type="text" id="username" value="wzg"/>
    <button onclick="onclickFun()">较验</button>
</body>
```

当我们要操作一个标签的时候，一定要先获取这个标签对象。

test()方法用于测试某个字符串，是不是匹配我的规则 ！！！！

**！！**通过文本框中属性value就可获得文本框内容 包含后面修改了之后的内容**！！**



innerHTML表示标签开头和结束之间的内容  并且可读可写   **！！！**

```
var usernameSpanObj = document.getElementById("usernameSpan");
// innerHTML 表示起始标签和结束标签中的内容
// innerHTML 这个属性可读，可写
usernameSpanObj.innerHTML = "国哥真可爱！";

if (patt.test(usernameText)) {
			usernameSpanObj.innerHTML = "用户名合法！";
} else {
			usernameSpanObj.innerHTML = "用户名不合法！";
    }
<body>
    用户名：<input type="text" id="username" value="wzg"/>
    
    <span id="usernameSpan" style="color:red;">
    
    </span>
    
    <button onclick="onclickFun()">较验</button>
```











如何 验证 字符串，符合某个规则 ，需要使用正则表达式技术

##### 正则表达式技术

语法:

var patt=new RegExp (内容 , modifiers) ;

或者更简单的方式:

var patt=/内容/modifiers;

```
// 表示要求字符串中，是否包含字母e
// var patt = new RegExp("e");
// var patt = /e/; // 也是正则表达式对象

// 表示要求字符串中，是否包含字母a或b或c
// var patt = /[abc]/;

// 表示要求字符串，是否包含小写字母
// var patt = /[a-z]/;

// 表示要求字符串，是否包含任意大写字母
// var patt = /[A-Z]/;

// 表示要求字符串，是否包含任意数字
// var patt = /[0-9]/;

// 表示要求字符串，是否包含字母，数字，下划线
// var patt = /\w/;

// 表示要求 字符串中是否包含至少一个a
// var patt = /a+/;

// 表示要求 字符串中是否 *包含* 零个 或 多个a
// var patt = /a*/;

// 表示要求 字符串是否包含一个或零个a    123aaa也满足 是包含！
// var patt = /a?/;

// 表示要求 字符串是否包含连续三个a
// var patt = /a{3}/;

// 表示要求 字符串是否包 至少3个连续的a，最多5个连续的a
// var patt = /a{3,5}/;

// 表示要求 字符串是否包 至少3个连续的a，
// var patt = /a{3,}/;

// 表示要求 字符串必须以a结尾
// var patt = /a$/;

// 表示要求 字符串必须以a打头
// var patt = /^a/;

// 要求字符串中是否*包含* 至少3个连续的a
// var patt = /a{3,5}/;

// 要求字符串，从头到尾都必须完全匹配
// var patt = /^a{3,5}$/;

var patt = /^\w{5,12}$/;

var str = "wzg168[[[";

alert( patt.test(str) );
```

表示要求字符串，是否包含字母，数字，下划线

/\w/                    **！！**

/\W/		与		/\w/       相反

// 要求字符串，从头到尾都必须完全匹配

/^a{3,5}$/ 用^$框起来！！

var patt = /^\w{5,12}$/;

可用 patt.test(一个参数)进行验证是否满足 满足返回ture 不满足返回false；



#### getElementsByName

通过name来查找 可返回多个 不像id只能唯一

```
<script type="text/javascript">
    // 全选
    function checkAll() {
        var hobbies = document.getElementsByName("hobby");
        for (var i = 0; i < hobbies.length; i++){
            hobbies[i].checked = true;
        }
    }
    
    //全不选
    function checkNo() {

        var hobbies = document.getElementsByName("hobby");
        
        for (var i = 0; i < hobbies.length; i++){
            hobbies[i].checked = false;
        }
    }
    
    // 反选
    function checkReverse() {
        var hobbies = document.getElementsByName("hobby");

        for (var i = 0; i < hobbies.length; i++) {
            hobbies[i].checked = !hobbies[i].checked;

            // if (hobbies[i].checked) {
            //     hobbies[i].checked = false;
            // }else {
            //     hobbies[i].checked = true;
            // }
        }

    }

</script>

<body>
    兴趣爱好：
    <input type="checkbox" name="hobby" value="cpp" checked="checked">C++
    <input type="checkbox" name="hobby" value="java">Java
    <input type="checkbox" name="hobby" value="js">JavaScript
    <br/>
    <button onclick="checkAll()">全选</button>
    <button onclick="checkNo()">全不选</button>
    <button onclick="checkReverse()">反选</button>
</body>
```

checked表示复选框的选中状态。如果选中是true，不选中是false

checked 这个属性可读，可写



#### getElementsByTagName

通过标签名 如input查找



```
// 全选
function checkAll() {
    alert( document.getElementById("btn01") );
    
    var inputs = document.getElementsByTagName("input");

    for (var i = 0; i < inputs.length; i++){
        inputs[i].checked = true;
    }
}

<body>

    <!--as -->
    兴趣爱好：
    <input type="checkbox" value="cpp" checked="checked">C++
    <input type="checkbox" value="java">Java
    <input type="checkbox" value="js">JavaScript
    <br/>
    <button id="btn01" onclick="checkAll()">全选</button>

</body>
```

 document.getElementsByTagName("input");

​    		是按照指定标签名来进行查询并返回集合

  		  这个集合的操作跟数组一样

 		   集合中都是dom对象

​    	   集合中元素顺序 是他们在html页面中从上到下的顺序。



#### 注

document对象的三个查询方法，如果有id属性，优先使用getElementById方法来进行查询如果没有id属性，则优先使用getElementsByName方法来进行查询，如果id属性和name属性都没有最后再按标签名查getElementsByTagName

以上三个方法，一定要在页面加载完成之后执行。才能查询到标签对象。



### 节点常用属性和方法

节点就是标签对象

方法:

通过具体的元素节点调用

getElementsByTagName()

方法：获取当前节点的指定标签名孩子节点

appendChild( oChildNode )

方法：可以添加一个子节点，oChildNode是要添加的孩子节点

属性:

childNodes
属性，获取当前节点的所有子节点

firstChild
属性，获取当前节点的第一个子节点

lastChild
属性，获取当前节点的最后一个子节点

parentNode
属性，获取当前节点的父节点

nextSibling
属性，获取当前节点的下一个节点

previousSibling
属性，获取当前节点的上一个节点

className（不用class因为冲突 dom对象.class）
用于获取或设置标签的class属性值

innerHTML
属性，表示获取/设置起始标签和结束标签中的内容

innerText
属性，表示获取/设置起始标签和结束标签中的文本



注意 空格字符也算节点！！！！！

如 获得第一个子节点

《ul》《li》



这样是可以的 

但《ul》

《li》

有换行不行 因为空格字符也算节点！！！！！