# 作用:

数据交换:通过Ajax可以给服务器发送请求，并获取服务器响应的数据。

异步交互:可以在不重新加载整个页面的情况下，与服务器交换数据并更新部分网页的技术，如:搜索联想、用户名是否可用的校验等等。



# AJAX的特点

## AJAX的优点

1) 可以无需刷新页面而与服务器端进行通信。

2) 允许你根据用户事件来更新部分页面内容。

##  AJAX的缺点

1) 没有浏览历史，不能回退

2) 存在跨域问题(同源)

3) SEO 不友好



# Axios

Axios是对原生的AJAX进行封装，简化书写。
Axios官网是：`https://www.axios-http.cn`

## 入门

- 引入Axios文件

  ~~~html
  <script src="js/axios-0.18.0.js"></script>
  ~~~

- 使用Axios发送请求，并获取响应结果，官方提供的api很多，此处给出2种，如下

  - 发送 get 请求

    ~~~js
    axios({
        method:"get",
        url:"http://localhost:8080/ajax-demo1/aJAXDemo1?username=zhangsan"
    }).then(function (resp){
        alert(resp.data);
    })
    ~~~

  - 发送 post 请求

    ```js
    axios({
        method:"post",
        url:"http://localhost:8080/ajax-demo1/aJAXDemo1",
        data:"username=zhangsan"
    }).then(function (resp){
        alert(resp.data);
    });
    ```

  获取返回值也可通过then(result=>{

  ​		console.log(result.data)

  ​	})	=>为c语言中的lambda表达式中的写法 java中为->	但是ajax中为=>写法

  

  例子：从http://yapi.smart-xwork.cn/mock/169327/emp/list和http://yapi.smart-xwork.cn/mock/169327/emp/deleteById中拿取json数据
  post请求 参数在方法体中， 所以要用data:"id = 1"来带着参数

```html
 <body>  
    <input type="button" value="获取数据GET" onclick="get()">

    <input type="button" value="删除数据POST" onclick="post()">
</body>
<script>
    function get(){
            axios({
                method: "get",
                url:"http://yapi.smart-xwork.cn/mock/169327/emp/list"
            }).then(result=>{
                console.log(result.data);
            })

    }

    function post(){

        axios({
            method: "post",
            url:"http://yapi.smart-xwork.cn/mock/169327/emp/deleteById",
            data:"id=1"
        }).then(result=>{
            console.log(result.data);
        })

    }
</script>
```



## 请求方法的别名

Axios还针对不同的请求，提供了别名方式的api,具体如下：

| 方法                              | 描述           |
| --------------------------------- | -------------- |
| axios.get(url [, config])         | 发送get请求    |
| axios.delete(url [, config])      | 发送delete请求 |
| axios.post(url , data[, config])  | 发送post请求   |
| axios.put(url [, data[, config]]) | 发送put请求    |

即不需要单独写method:"get" data:"xxx"这么繁琐
axios.post()第一个参数为url，即要请求的地址，第二个参数为要传递的参数

```html
<script>
    function get(){
        axios.get("http://yapi.smart-xwork.cn/mock/169327/emp/list").then(result=>{
                console.log(result.data);
        })  
    }

    function post(){
        axios.post("http://yapi.smart-xwork.cn/mock/169327/emp/deleteById","id=1").then(result=>{
            console.log(result.data);
        }) 
    }
</script>
```





## 案例

通过ajax 访问网页“http://yapi.smart-xwork.cn/mock/169327/emp/list”	返回json数据，其中带有idnamegender等信息，输出遍历到网页中

![image-20230517161756715](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230517161756715.png)

scrpit中new Vue	建立数据模型,用于传递数据 emps: []  因为请求的数据为数组，所以用[]
通过对vue的生命周期的分析 mounted为挂载完成之后自动调用的方法
在mounted中发起异步请求，读取数据
使用this.emps = result.data.data; this和java用法一致，保存数据 data表示我们请求回来的数据，而data.data，因为我们只需要请求回来的数据中的data部分

```html
<script>
    new Vue({
       el: "#app",
       data: {
         emps:[]
       },
       mounted () {
          //发送异步请求,加载数据
          axios.get("http://yapi.smart-xwork.cn/mock/169327/emp/list").then(result => {
            this.emps = result.data.data;
          })
       }
    });
</script>
```

表格中的操作

```html
<tr align="center" v-for="(emp,index) in emps">
                <td>{{index + 1}}</td>
                <td>{{emp.name}}</td>
                <td>
                    <img :src="emp.image" width="70px" height="50px">
                </td>
                <td>
                    <span v-if="emp.gender == 1">男</span>
                    <span v-if="emp.gender == 2">女</span>
                </td>
                <td>{{emp.job}}</td>
                <td>{{emp.entrydate}}</td>
                <td>{{emp.updatetime}}</td>
            </tr>
```

