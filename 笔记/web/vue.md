# Vue

Vue是一套前端框架，免除原生JavaScript中的DOM操作，简化书写。

基于MVVM(Model-View-ViewModel)思想，实现数据的双向绑定，将编程的关注点放在数据上。

![image-20230517142529690](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230517142529690.png)

即在前面 body中编写视图，在script中编写数据模型， 不管是数据模型还是视图发生变化，都会相互影响

在使用前，一定要用script标签 把vue.js引入！！！！1

<script src="js/vue.js"></script>

例子：

```html
<script src="js/vue.js"></script>
<script>
    new new Vue({
        el:"#app",
        data:{
            message:"Hello Vue !"
        }
    })
</script>

<body>
<div id="app">
    <input type="text" v-model="message">
    {{message}}
</div>
</body>
```

相当于在script中定义一个Vue	el作用于id为app的视图中 数据data中定义key：message value："Hello Vue !

<input type="text" v-model="message">为设置文本输入框，通过v-model="message"引入key，在文本框中修改，则会通过vue框架直接修改data中message的值

而{{message}}	表示读取vue中key为message中的值	所以在文本中修改值，也会影响{{message}}中的值





## Vue指令

**指令：**HTML 标签上带有 v- 前缀的特殊属性

| **指令**  | **作用**                                            |
| --------- | --------------------------------------------------- |
| v-bind    | 为HTML标签绑定属性值，如设置  href , css样式等      |
| v-model   | 在表单元素上创建双向数据绑定(即可读 也可写)         |
| v-on      | 为HTML标签绑定事件                                  |
| v-if      | 条件性的渲染某元素，判定为true时渲染,否则不渲染     |
| v-else    | 条件性的渲染某元素，判定为true时渲染,否则不渲染     |
| v-else-if | 条件性的渲染某元素，判定为true时渲染,否则不渲染     |
| v-show    | 根据条件展示某元素，区别在于切换的是display属性的值 |
| v-for     | 列表渲染，遍历容器的元素或者对象的属性              |





### v-bind和v-model

v-bind:  为HTML标签绑定属性值，如设置  href , css样式等。当vue对象中的数据模型发生变化时，标签的属性值会随之发生变化。	即是单向的读取vue中的数据模型，若数据模型发送变化，自身也发生变化

在实例中 v-bind:href="url"和 :href="url"一致， 即v-bind可以忽略 但是:不能忽略

**注意：html属性前面有:表示采用的vue的属性绑定！**

```
v-bind:href="url"
```



v-model： 在表单元素上创建双向数据绑定。什么是双向？

-  vue对象的data属性中的数据变化，视图展示会一起变化
-  视图数据发生变化，vue对象的data属性中的数据也会随着变化。

data属性中数据变化，我们知道可以通过赋值来改变，但是视图数据为什么会发生变化呢？**只有表单项标签！所以双向绑定一定是使用在表单项标签上的**。！！！

例子：

```html
<body>
    <div id="app">
        <a :href="url">{{url}}</a>
        <input type="text" v-model="url"/>
    </div>
</body>
<script>
    //定义Vue对象
    new Vue({
        el: "#app", //vue接管区域
        data:{
           url:"http://www.baidu.com"
        }
    })
</script>
```





### v-on

v-on: 用来给html标签绑定事件的。**需要注意的是如下2点**：

- v-on语法给标签的事件绑定的函数，必须是vue对象种声明的函数

- v-on语法绑定事件时，事件名相比较js中的事件名，没有on

  绑定事件时 例如单击事件 可通过v-on:click="handle()" 来绑定也可通过@click="handle()"绑定！！



例子：

```html
<body>
    <div id="app">

        <input type="button" value="点我一下" v-on:click="handle()">

        <input type="button" value="点我一下" @click="handle()">

    </div>
</body>
<script>
    //定义Vue对象
    new Vue({
        el: "#app", //vue接管区域
        data:{
           
        },
        methods: {
            handle:function(){
                alert("被点击了！");
            }
        }
    })
</script>
```





### v-if和v-show

v-if  v-else-if v-else 相当于if else-if else
v-show相当于 while	只有满足了才输出 没有分流

例子：

```html
<body>
    <div id="app">
        
        年龄<input type="text" v-model="age">经判定,为:
        <span v-if="age <= 35">年轻人(35及以下)</span>
        <span v-else-if="35< age && age <60">中年人(35-60)</span>
        <span v-else="60 <= age">老年人(60及以上)</span>

        <br><br>

        年龄<input type="text" v-model="age">经判定,为:
        <span v-show="age<=35">年轻人(35及以下)</span>
        <span v-show="5< age && age <60">中年人(35-60)</span>
        <span v-show="60 <= age">老年人(60及以上)</span>

    </div>
</body>
<script>
    //定义Vue对象
    new Vue({
        el: "#app", //vue接管区域
        data:{
           age: 20
        },
        methods: {
            
        }
    })
</script>
```



### v-for

用于遍历数据	语法格式为:	相当于foreach addr为每次遍历中遍历的数据	addrs为要遍历的数据

```html
<标签 v-for="addr in addrs">
    {{变量名}}
</标签>
```

也可通过	(addr,index)	addr仍然是每次遍历的数据 而index为遍历次数，从0开始

```html
<标签 v-for="(addr, index) in addrs">
        {{index+1}}:{{addr}}
</标签>
```

例子：

```html
<body>
    <div id="app">
        <div v-for="(addr, index) in addrs">
            {{index+1}}:
            {{addr}}
        </div>

    </div>
</body>
<script>
    //定义Vue对象
    new Vue({
        el: "#app", //vue接管区域
        data:{
           addrs:["北京", "上海", "西安", "成都", "深圳"]
        },
        methods: {
            
        }
    })
</script>
```





## 生命周期

vue的生命周期：指的是vue对象从创建到销毁的过程。vue的生命周期包含8个阶段：每触发一个生命周期事件，会自动执行一个生命周期方法，这些生命周期方法也被称为钩子方法。其完整的生命周期如下图所示：

| 状态          | 阶段周期 |
| ------------- | -------- |
| beforeCreate  | 创建前   |
| created       | 创建后   |
| beforeMount   | 挂载前   |
| mounted       | 挂载完成 |
| beforeUpdate  | 更新前   |
| updated       | 更新后   |
| beforeDestroy | 销毁前   |
| destroyed     | 销毁后   |

vue中可编写上方的钩子方法，当触发一个生命周期时间，则会执行一个钩子方法

![image-20230517153044770](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230517153044770.png)



重点关注mounted：
挂载完成，Vue初始化成功，HTML页面渲染成功。**以后我们一般用于页面初始化自动的ajax请求后台数据**

即当初始化之后会自动指定方法，不需要调用

例子：	mounted()方法与methods平级！！！

```html
<body>
    <div id="app">

    </div>
</body>
<script>
    //定义Vue对象
    new Vue({
        el: "#app", //vue接管区域
        data:{
           
        },
        methods: {
            
        },
        mounted () {
            alert("vue挂载完成,发送请求到服务端")
        }
    })
</script>
```

