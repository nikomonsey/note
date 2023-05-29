## jQuery



```
<script type="text/javascript" src="../script/jquery-1.7.2.js"></script>
<script type="text/javascript">

   //使用$()代替window.onload
   $(function(){
      //使用选择器获取按钮对象，随后绑定单击响应函数
      $("#btnId").click(function(){
         //弹出Hello
         alert('Hello');
      });
   });
```

$(function () {                                                  //表示页面加载完成之后，相当 window.onLoad = function () {}
});



<script type="text/javascript" src="../script/jquery-1.7.2.js"></script>

引入jQuery库 一定要

$就是一个函数 在jQuery中 把jQuery函数赋值给了$ ！！！

怎么为按钮添加点击响应函数的?

1、使用jQuery查询到标签对象

2、使用标签对象.dick( function(

));  ！！！不用 标签对象.dick=function(){}

### $函数功能

传入参数为[函数]时：在文档加载完成后执行这个函数

$(function()){

}          =         window.onload=function(){

}

传入参数为[HTML字符串]时：根据这个字符串创建元素节点对象

$("    <div>" +
    "        <span>div-span1</span>" +
    "        <span>div-span2</span>" +
    "    </div>").appendTo("body");

可直接输出在body上 等于

<body>

<div>
      <span>div-span1</span>
      <span>div-span2</span>
 </div>

</body>

传入参数为[选择器字符串]时：根据这个字符串查找元素节点对象

$("#id属性值");			id选择器，根据id查询标签对象            **查id要带# ！！！！**
$(“标签名");				  标签名选择器，根据指定的标签名查询标签对象
$(".class属性值");		类型选择器，可以根据class属性查询标签对象

传入参数为[DOM对象]时：将DOM对象包装为jQuery对象返回

jQuery对象的本质是什么?

jQuery对象是dom对象的数组+ jQuery提供的一系列功能函数。！！

jQuery对象和dom对象区别

不能使用对方的方法

dom对象转化为jQuery对象**（*重点)**
1、先有DOM对象

2、$( DOM对象）就可以转换成为jQuery对象

Query对象转为dom对象**(重点)**

1、先有jQuery对象

2、jQuery对象[下标]取出相应的DOM对象           和取数组是一样的

### 基本选择器（****重点)

```
//1.选择 id 为 one 的元素 "background-color","#bbffaa"
$("#btn1").click(function () {
	$("#one").css("background-color","#bbffaa")
})

//2.选择 class 为 mini 的所有元素
$("#btn2").click(function () {
	$(".mini").css("background-color","#bbffaa")
})

//3.选择 元素名是 div 的所有元素 
$("#btn3").click(function () {
	$("div").css("background-color","#bbffaa")
})
//4.选择所有的元素 
$("#btn4").click(function () {
   $("*").css("background-color","#bbffaa")
})

//5.选择所有的 span 元素和id为two的元素   
$("#btn5").click(function () {
   $("span,#two").css("background-color","#bbffaa")
})
```

#ID选择器:根据id查找标签对象

.class选择器:根据class查找标签对象

element选择器:根据标签名查找标签对象

*选择器:表示任意的，所有的元素

selector1, selector2组合选择器:合并选择器1，选择器2的结果并返回

p  .myClass	

表示标签名必须是p标签，而且class类型还要是myClass !!!

组合查询结果顺序和页面中顺序一致



css() 方法 可以设置和获取样式



### 层次选择器

```
$(document).ready(function(){
   //1.选择 body 内的所有 div 元素 
   $("#btn1").click(function(){
      $("body div").css("background", "#bbffaa");
   });

   //2.在 body 内, 选择div子元素  
   $("#btn2").click(function(){
      $("body>div").css("background", "#bbffaa");
   });

   //3.选择 id 为 one 的下一个 div 元素 
   $("#btn3").click(function(){
      $("#one+div").css("background", "#bbffaa");
   });

   //4.选择 id 为 two 的元素后面的所有 div 兄弟元素
   $("#btn4").click(function(){
      $("#two~div").css("background", "#bbffaa");
   });
});
```

$(document).ready(function(){} == 是其简写

$(function(){

})

ancestor descendant 				在给定的祖先元素下匹配所有的后代元素

parent > child       					   在给定的父元素下匹配所有的子元素

prev + next         						  匹配所有紧接在prev元素后的next元素

prev ~ siblings   						  匹配 prev元素之后的所有siblings元素

### 过滤选择器

#### 基本过滤选择器

```
$(document).ready(function(){
   //1.选择第一个 div 元素  
   $("#btn1").click(function(){
      $("div:first").css("background", "#bbffaa");
   });

   //2.选择最后一个 div 元素
   $("#btn2").click(function(){
      $("div:last").css("background", "#bbffaa");
   });

   //3.选择class不为 one 的所有 div 元素
   $("#btn3").click(function(){
      $("div:not(.one)").css("background", "#bbffaa");
   });

   //4.选择索引值为偶数的 div 元素
   $("#btn4").click(function(){
      $("div:even").css("background", "#bbffaa");
   });

   //5.选择索引值为奇数的 div 元素
   $("#btn5").click(function(){
      $("div:odd").css("background", "#bbffaa");
   });

   //6.选择索引值为大于 3 的 div 元素
   $("#btn6").click(function(){
      $("div:gt(3)").css("background", "#bbffaa");
   });

   //7.选择索引值为等于 3 的 div 元素
   $("#btn7").click(function(){
      $("div:eq(3)").css("background", "#bbffaa");
   });

   //8.选择索引值为小于 3 的 div 元素
   $("#btn8").click(function(){
      $("div:lt(3)").css("background", "#bbffaa");
   });

   //9.选择所有的标题元素
   $("#btn9").click(function(){
      $("div:header").css("background", "#bbffaa");
   });

   //10.选择当前正在执行动画的所有元素
   $("#btn10").click(function(){
      $("div:animated").css("background", "#bbffaa");
   });
```

:first        第一个
:last        最后一个
:not(selector)      如not(:checked)选择不选中的元素 checked是选中！！
:even        匹配所有索引值为偶数的元素，从0开始计数 表格第1 3 5行 索引值对应0 2 4 ！！！
:odd         匹配所有索引值为奇数的元素
:eq(index)     匹配一个给定索引值的元素
:gt(index)      匹配所有大于给定索引值的元素  
:lt(index)       匹配所有小于给定索引值的元素  
:header         匹配如h1, h2,h3之类的标题元素
:animated     匹配正在执行动画的元素

#### 内容过滤选择器



:contains(text)    匹配包含给定文本的元素 例div:contains("john")  查找div中包含john文本的元素

:empty                 匹配所有不包含子元素或者文本的空元素

:has(selector)      匹配含有选择器所匹配的元素的元素      例$(div:has(p).addClass("test"))  给所有包含p元素的div元素添加一个text类       返回的是div！！不是p

:parent                 匹配有子元素或者文本的元素  

### 属性过滤器

```
$(function() {
   //1.选取含有 属性title 的div元素
   $("#btn1").click(function() {
      $("div[title]").css("background", "#bbffaa");
   });
   //2.选取 属性title值等于'test'的div元素
   $("#btn2").click(function() {
      $("div[title='test']").css("background", "#bbffaa");
   });
   //3.选取 属性title值不等于'test'的div元素(*没有属性title的也将被选中)
   $("#btn3").click(function() {
      $("div[title!='test']").css("background", "#bbffaa");
   });
   //4.选取 属性title值 以'te'开始 的div元素
   $("#btn4").click(function() {
      $("div[title^='te']").css("background", "#bbffaa");
   });
   //5.选取 属性title值 以'est'结束 的div元素
   $("#btn5").click(function() {
      $("div[title$='est']").css("background", "#bbffaa");
   });
   //6.选取 属性title值 含有'es'的div元素
   $("#btn6").click(function() {
      $("div[title*='es']").css("background", "#bbffaa");
   });
   
   //7.首先选取有属性id的div元素，然后在结果中 选取属性title值 含有'es'的 div 元素
   $("#btn7").click(function() {
      $("div[id][title*='es']").css("background", "#bbffaa");
   });
   //8.选取 含有 title 属性值, 且title 属性值不等于 test 的 div 元素
   $("#btn8").click(function() {
      $("div[test][title!='test']").css("background", "#bbffaa");
   });
```

attribute 属性名

[attribute]      					匹配包含给定属性的元素     
[attribute=value]     	     匹配给定属性是某个特定值的元素
[attribute!=value]             匹配所有不含有指定的属性，或者属性不等于特定值的元素。
[attribute^=value]            匹配给定的属性是以某些值开始的元素
[attribute$=value]            匹配给定的属性是以某些值结尾的元素
[attribute*=value]            匹配给定的属性是包含某些值的元素
[attrSel1] [attrSel2] [attrSelN]   复合属性选择器，需要同时满足多个条件时使用。

含有 title 属性值, 且title 属性值不等于 test 的 div 元素

div[test] [title!='test']

### 表单过滤器



:input     		匹配所有input, textarea,select和 button元素
:text       	 	匹配所有的单行文本框
:password     匹配所有密码框
:radio        	 匹配所有单选按钮  radio为单选按钮
:checkbox      匹配所有复选按钮
:submit    	  匹配所有提交按钮
:image           匹配所有图形
:reset        	 匹配所有重置按钮
:button    	  匹配所有提交按钮
:file     			匹配所有文件域
:hidden    	  匹配所有不可见的元素或type为hidden的元素

表单对象的属性
:enabled      
:disabled     
:checked      匹配所有选中的被选中元素
:selected 	 匹配所有选中的option元素







## jQuery属性操作

```
$(function () {
        //     alert($("div").html());
        // $("div").html("<h1>我是div中的标题 1</h1>");


        // $("div").text("<h1>我是div中的标题 1</h1>");
        // })
        $("button").click(function () {
            alert( $("#username").val() )
        });

        });

    </script>
</head>
<body>
<div>我是div标签 <span>我是div中的span</span> </div>
<input type="text" name="username" id="username"/>
<button>操作输入框</button>
```



html()      它可以设置和获取起始标签和结束标签中的内容。跟dom属性innerHTML一样。
text()       它可以设置和获取起始标签和结束标签中的文本。跟dom 属性innerText一样。
val()         它可以设置和获取**表单**项的value属性值。跟dom 属性value一样。

val方法同时设需多个表单项的选中状态

```
$(function () {

	//批量操作单选
    $(":radio").val(["radio1"]);
	//批量操作筛选框的选中状态
    $(":checkbox").val(["checkbox1","checkbox2","checkbox3"]);
	/批量操作多选的下拉框选中状态
    $("#multiple").val(["mul1","mul2","mul3","mul4"]);
	//操作单选的下拉框选中状态
    $("#single").val(["sin2"])
    //批量操作
    $(":radio,:checkbox,#multiple,#single").val(["radio1","checkbox1","checkbox3","mul1","mul3","sin2"])

})

单选：
<input name="radio" type="radio" value="radio1" />radio1
<input name="radio" type="radio" value="radio2" />radio2
<br/>
多选：
<input name="checkbox" type="checkbox" value="checkbox1" />checkbox1
<input name="checkbox" type="checkbox" value="checkbox2" />checkbox2
<input name="checkbox" type="checkbox" value="checkbox3" />checkbox3
<br/>

下拉多选 ：
<select id="multiple" multiple="multiple" size="4">
    <option value="mul1">mul1</option>
    <option value="mul2">mul2</option>
    <option value="mul3">mul3</option>
    <option value="mul4">mul4</option>
</select>
<br/>

下拉单选 ：
<select id="single">
    <option value="sin1">sin1</option>
    <option value="sin2">sin2</option>
    <option value="sin3">sin3</option>
```

val(["xxx"])        		  xxx为标签属性value中的值  **！！！！！**

val("xxx")					xxx为标签属性value中的新值  改变了      *** ！！！！***



```
//attr
// alert( $(":checkbox:first").attr("name") ); // 获取
// $(":checkbox:first").attr("name","abc") ; // 设置

// $(":checkbox").prop("checked",false );// 官方觉得返回undefined是一个错误
// $(":checkbox:first").attr("abc","abcValue");

 <br/>
    多选：
    <input name="checkbox" type="checkbox" checked="checked" value="checkbox1" />checkbox1
    <input name="checkbox" type="checkbox" value="checkbox2" />checkbox2
```

attr()		  可以设置和获取属性的值 不推荐操作checked、readOnly、selected、disabled等等

可以操作非标准的属性，比如自定义属性abc等等

$(":checkbox:first").attr("abc","abcValue");

会在 第一个checkbox后面加上 abc=“abValue”

prop()		可以设置和获取属性的值 只推荐上面不推荐的

attr("checked")时 会返回undefined  用prop会返回true和false

## Dom增删改查

### 内部插入

```
$("<h1>标题</h1>").appendTo( $("div") );
$("<h1>标题</h1>").prependTo( $("div") );

<div>1234</div>
结果为<div>1234<h1>标题</h1></div>
```

appendTo()       		a.appendTo(b)		 把a插入到b子元素末尾，成为最后一个子元素
prependTo()			 a.prependTo(b)		把a插到b所有子元素前面，成为第一个子元素

### 外部插入

```
$("<h1>标题</h1>").insertAfter("div");

$("<h1>标题</h1>").insertBefore( $("div") );
```

insertAfter()			a.insertAfter(b)			得到 ba
insertBefore()		 a.insertBefore(b)		 得到ab

### 替换

replaceWith()		a.replaceWith(b)			用b替换掉a
replaceAll()			a.replaceAll(b)				用a替换掉所有b



### 删除

remove()		  a.remove();			 删除a标签
empty()			a.empty();				清空a标签里的内容

## CSS样式

```
div.whiteborder{
   border: 2px white solid;
}

div.redDiv{
   background-color: red;
}

div.blueBorder{
   border: 5px blue solid;
}
$('#btn01').click(function(){
			//addClass() - 向被选元素添加一个或多个类
			$divEle.addClass('redDiv blueBorder');
		});
		
		$('#btn02').click(function(){
			//removeClass() - 从被选元素删除一个或多个类 
			$divEle.removeClass(' blueBorder');
		});
	
		
		$('#btn03').click(function(){
			//toggleClass() - 对被选元素进行添加/删除类的切换操作 
			$divEle.toggleClass('redDiv');
		});
		
		
		$('#btn04').click(function(){
			//offset() - 返回第一个匹配元素相对于文档的位置。
			var pos=$divEle.offset();
			console.log(pos);
			$divEle.offset({
				top:100,
				left:50
			});
```

div.redDiv表示限制为div类型 span其他的不行

addclass()					  添加样式
removeClass()			  删除样式 不写东西就是全删
toggleClass()				有就删除，没有就添加样式。
offset()						  获取和设置元素的坐标。



## 动画

show()			 将隐藏的元素显示。

hide()			   将可见的元素隐藏。

toggle()			可见就隐藏，不可见就显示。

fadeln()						淡入（慢慢可见）
fadeOut()					淡出（慢慢消失)
fadeTo()					  在指定时长内慢慢的将透明度修改到指定的值。0透明，1完成可见，0.5半透明
fadeToggle()			   淡入/淡出切换

以上动画方法都可以添加参数。

1、第一个参数是动画执行的时长，以毫秒为单位

2、第二个参数是动画的回调函数			(动画完成后自动调用的函数)

## jQuery事件操作

$function();和

window.onload = function()的区别?

他们分别是在什么时候触发?

1、jQuery的页面加载完成之后是浏览器的内核解析完页面的标签创建好DOM对象之后就会马上执行。

2、原生js的页面加载完成之后，除了要等浏览器内核解析完标签创建好DOM对象，还要等标签显示时需要的内容加载完成。

他们触发的顺序?

1、jQuery页面加载完成之后先执行

2、原生js 的页面加载完成之后

他们执行的次数?

1、原生js的页面加韩完成之后,只会执行最后一次的赋值函楼。

2、jQuery的页面加载完成之后是全部把注册的function 函数，依次顺序全部执行。

## jQuery中其他的事件处理方法

```
$(function(){
         // $("h5").click(function () {
         //     alert("h5单击事件")
         // });

         // $("button").click(function () {
         //     $("h5").click();
         // });

         // $("h5").mouseover(function () {
         //     console.log("进来");
         // });

         // $("h5").mouseout(function () {
         //     console.log("出去");
         // });

         // $("h5").bind("click mouseover mouseout",function () {
         //     console.log("bind绑定事件");
         // });

         // $("h5").one("click mouseover mouseout",function () {
         //        console.log("one绑定事件");
         //      });
         //
         // $("h5").unbind("click");

         $("h5").live("click",function () {
            alert("h5单击事件 通过live绑定")
         });
         $('<h5 class="head">什么是jQuery?</h5>').appendTo($("#panel"));
      });
   
   </script>
</head>
<body>
   <div id="panel">
      <h5 class="head">什么是jQuery?</h5>
      <div class="content">
         jQuery是继Prototype之后又一个优秀的JavaScript库，它是一个由 John Resig 创建于2006年1月的开源项目。jQuery凭借简洁的语法和跨平台的兼容性，极大地简化了JavaScript开发人员遍历HTML文档、操作DOM、处理事件、执行动画和开发Ajax。它独特而又优雅的代码风格改变了JavaScript程序员的设计思路和编写程序的方式。
      </div>
      <button>按钮</button>
```

click()					它可以绑定单击事件，以及触发单击事件

mouseover()		它可以绑定单击事件，以及触发单击事件鼠标移入事件

mouseout()		 鼠标移出事件

bind()				   可以给元素一次性绑定一个或多个事件。

one()					使用上跟bind一样。但是one方法绑定的事件只会响应一次。

live()					 也是用来绑定事件。它可以用来绑定选择器匹配的所有元素的事件。哪怕这个元素是后面动态创出

unbind()				跟bind方法相反的操作，解除事件的绑定

## 事件的冒泡

什么是事件的冒泡?

事件的冒泡是指，父子元素同时监听同一个事件。当触发子元素的事件的时候，同一个事件也被传递到了父元素的事件里去响应。

例如

```
$("#content").click(function () {
					alert("div事件")
				});
				$("span").click(function () {
					alert("span事件")
				});
div id="content">
   外层div元素
   <span>内层span元素</span>
   外层div元素
</div>
```

这时 如果点击span标签 会跳出span事件和div事件两个提示

阻止 在子元素函数体内加入 return false；**！！！**



## javascript事件对象

事件对象，是封装有触发的事件信息的一个 javasaript对象。

如何获取呢 javascript事件对象呢?

在给元素绑定事件的时候，在事件的function( event ）参数列表中添加一个参数，这个参数名，我们习惯取名为event  这个event就是javascript传递参事件处理函褛数的事件对象。val



## serialize()方法

```
var data = $("#formID").serialize();
```

将表单内容序列化成一个字符串。	用于传递表单信息

调用ajax post请求，url为user/register	参数为$("#registerForm").serialize()！！！

```
$.post("user/register",$("#registerForm").serialize(),function (data) {

})
```