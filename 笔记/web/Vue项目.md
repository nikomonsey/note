# Vue项目

和之前的项目相比非常不一样，开始区分各个模块，且前后端分离

根据	C:\Users\ljxxx\Desktop\student\java\笔记\框架\vue项目 下看怎么下载vue脚手架和创建vue工程等



## vue组件文件格式

Vue的组件文件以.vue结尾，每个组件由三个部分组成: <template> , <script>,<style> 。

- template: 模板部分，主要是HTML代码，用来展示页面主体结构的

- script: js代码区域，主要是通过js代码来控制模板的数据来源和行为的

- style: css样式部分，主要通过css样式控制模板的页面效果得

  ![image-20230522084103504](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522084103504.png)







## vue结构目录

平时我们开发的代码就放在src下

![image-20230517203645684](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230517203645684.png)

通过VsCode启动vue项目

![image-20230517203740673](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230517203740673.png)



可通过修改vue.config.js来修改vue相关配置，例如端口号等

```vue
module.exports = defineConfig({
  transpileDependencies: true,
  devServer:{
    port:7000		//修改端口号为7000
  }
```



## vue项目开发流程

这时打开public\index.html 发现什么都没有，但是启动vue项目，发现有挺多东西
原因：

对于vue项目，index.html文件默认是引入了入口函数main.js文件，而main.js文件中

```js
import Vue from 'vue'
import App from './App.vue'
import router from './router'

Vue.config.productionTip = false

new Vue({
  router,
  render: h => h(App)
}).$mount('#app')
```

- import: 导入指定文件，并且重新起名。例如上述代码`import App from './App.vue'`导入当前目录下得App.vue并且起名为App
- new Vue(): 创建vue对象
- $mount('#app');将vue对象创建的dom对象挂在到id=app的这个标签区域中，作用和之前学习的vue对象的le属性一致。
- router:  路由，
- render: 主要使用视图的渲染的。

```
而这里的：
new Vue({
  router,	若冒号前后相同，则可以直接简写为router
  render: h => h(App)	//render是一个方法，相当于从App.Vue中读取视图生成虚拟dom元素挂载到#app中		=>为lambda语法	只是->变为=>
}).$mount('#app')	//相当于el:"#app"，只是调用$mount()方法挂载在app上
和之前学的：	一样
new Vue({
    el:"#app",
    mounted () {
    	router:router,
        render: h => h(App)
    }
})
```



说明index首页是通过vue创建了dom对象挂载在了id=app的区域中
而具体的生成，则是通过render方法，读取App.Vue

App.Vue	
这里的数据使用data(){return{xxx}}	而不是data:{xxx}
因为不适用return包裹的数据会在项目的全局可见，所有组件都会使用同一个数据，导致数据污染

```vue
<template>
  <div id="app">
  </div>
</template>
<script>
export default {
  data(){
    return{
      message:"Hello vue22"
    }
  },  
}
</script>
<style>
</style>
```



## Vue组件库Element

Element:是饿了么团队研发的，一套为开发者、设计师和产品经理准备的基于Vue 2.0 的桌面端组件库。

组件:组成网页的部件，例如超链接、按钮、图片、表格、表单、分页条等等。

官网: https://element.eleme.cn/#/zh-CNListener

Element 提供了很多组件（组成网页的部件）供我们使用。例如 超链接、按钮、图片、表格等等。如下图所示就是我们开发的页面和ElementUI提供的效果对比：可以发现ElementUI提供的各式各样好看的按钮

![1669357961971](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\1669357961971.png)

我们只需要学习如何从官网上拷贝组件到我们的页面中，并进行修改



### 快速入门

要先安装ElementUI组件库，在当前项目中开启cmd	输入

```
npm install element-ui@2.15.3 
```

![image-20230522084243563](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522084243563.png)

在main.js入口js文件中引入ElementUI的组件库

```js
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);
```

在src\views目录下创建vue组件文件，后缀为.vue	该目录用于存放自建的vue文件

![image-20230522084514421](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522084514421.png)

建立ElementView.vue文件,在官网上找要使用的组件代码，加入到template标签中

```vue
<template>
    <el-row>
        <el-button>默认按钮</el-button>
        <el-button type="primary">主要按钮</el-button>
        <el-button type="success">成功按钮</el-button>
        <el-button type="info">信息按钮</el-button>
        <el-button type="warning">警告按钮</el-button>
        <el-button type="danger">危险按钮</el-button>
    </el-row>
    
</template>

<script>
export default {
}
</script>

<style>
</style>

```

在根组件App.vue文件中	引入ElementView文件

```vue
<template>
  <div id="app">
    <element-view></element-view>	//引入文件
  </div>
</template>

<script>
import ElementView from './views/Element/ElementView.vue'	//script中会自动引入文件
export default {
  components: { ElementView },			//自动引入
  
}
</script>
```

启动项目

![image-20230522085411514](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522085411514.png)





### element组件

#### table表格

在官网中找到table表格代码	复制到自建vue中，注意 template中 若存在button和table两个组件，则需要在外面加上div标签，把两个组件包起来，否则会报错
复制时，若代码有script和style部分 也要一起复制

![image-20230522090734836](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522090734836.png)



```vue
<template>
    <div>
        <!-- table 表格 -->
		//:data 从数据模型中取出tableData数据
        <el-table :data="tableData" style="width: 100%">
		//prop 绑定数据模型中的什么数据	label为显示名称
        <el-table-column prop="date" label="日期" width="180"></el-table-column>
        <el-table-column prop="name" label="姓名" width="180"></el-table-column>
        <el-table-column prop="address" label="地址"></el-table-column>
        </el-table>
    </div>
</template>

<script>
    export default {
        data() {
            return {
              tableData: [{
                date: '2016-05-02',
                name: '王小虎',
                address: '上海市普陀区金沙江路 1518 弄'
              }, {
                date: '2016-05-04',
                name: '王小虎',
                address: '上海市普陀区金沙江路 1517 弄'
              }, {
                date: '2016-05-01',
                name: '王小虎',
                address: '上海市普陀区金沙江路 1519 弄'
              }, {
                date: '2016-05-03',
                name: '王小虎',
                address: '上海市普陀区金沙江路 1516 弄'
              }]
           }
        }
	}
</script>
```

显示效果：

![image-20230522090650808](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522090650808.png)

#### pagination分页

```vue
<!-- 分页条  pagination-->
<el-pagination 
	background 
	:page-size="20" 
	:pager-count="11" 
	layout="sizes, prev, pager, next, jumper, total" 
	:total="1000">
</el-pagination>
```

##### 属性

分页组件中有几个重要的属性

- background: 添加背景颜色

- layout: 分页工具条的布局，其具体值包含`sizes`, `prev`, `pager`, `next`, `jumper`, `->`, `total`, `slot` 这些值

- page-size：每页显示条目个数，支持 .sync 修饰符

- pager-count：总页数，total 和 page-count 设置任意一个就可以达到显示页码的功能；

- total: 数据的总数量

  其中layout属性中
  sizes显示每页显示数目	prev显示向前翻页箭头 pager显示页数1，2，3，4.....
  jumper显示 前往x页的框，用于跳转	total显示共多少页
  且有顺序，先写什么，就先显示什么

  ![image-20230522092657533](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522092657533.png)



##### 事件

- size-change ： pageSize 改变时会触发 	返回参数为每页条数
- current-change ：currentPage 改变时会触发    返回参数为当前页

@size-change="handleSizeChange"     
@current-change="handleCurrentChange"

每当每页记录数发生变化时 ，提示

```vue
<el-pagination 
    background 
    @size-change="handleSizeChange"
    @current-change="handleCurrentChange" 
    layout="sizes,prev, pager, next,jumper,total" 
    :total="1000">
</el-pagination>

<script>
    export default {
        methods: {
            handleSizeChange:function(val){		//给予参数val，用于返回数据每页条数
                alert("每页记录数发生变化"+val);
            },
            handleCurrentChange:function(val){	//返回数据当前页页码
                alert("页码发生变化"+val);
            }
        }    
    }
</script>
```



#### Dialog对话框

跳出一个对话框，用于告知用户信息，对话框中的内容可以完全由我们决定，可以放表格或表单等等

主要通过dialogTableVisible属性来控制是否显示对话框，默认为false，当点击之后设置为true

```vue
<el-button type="text" @click="dialogTableVisible = true">打开嵌套表单的 Dialog</el-button>
<el-dialog title="收货地址" :visible.sync="dialogTableVisible">
    <el-table :data="gridData">
         <el-table-column prop="date" label="日期" width="180"></el-table-column>
         <el-table-column prop="name" label="姓名" width="180"></el-table-column>
         <el-table-column prop="address" label="地址"></el-table-column>
    </el-table>
</el-dialog>

数据模型：
gridData: [{
          date: '2016-05-02',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }, {
          date: '2016-05-04',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }, {
          date: '2016-05-01',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }, {
          date: '2016-05-03',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }],
        dialogTableVisible: false,
```

#### Form表单

制作一个，dialog对话框，点击对话框，跳出表单

```vue
<!-- 对话框dialog 弹出form表单 -->
    <el-button type="text" @click="dialogFormVisible = true">打开嵌套表单的Dialog</el-button>
    <el-dialog title="form表单" :visible.sync="dialogFormVisible">
        <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="活动名称">
                <el-input v-model="form.name"></el-input>
            </el-form-item>
            <el-form-item label="活动区域">
                <el-select v-model="form.region" placeholder="请选择活动区域">
                    <el-option label="区域一" value="shanghai"></el-option>
                    <el-option label="区域二" value="beijing"></el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="活动时间">
                <el-col :span="11">
                    <el-date-picker type="date" placeholder="选择日期" v-model="form.date1" style="width: 100%;"></el-date-picker>
                </el-col>
                <el-col class="line" :span="2">-</el-col>
                <el-col :span="11">
                    <el-time-picker placeholder="选择时间" v-model="form.date2" style="width: 100%;"></el-time-picker>
                </el-col>
            </el-form-item>
            <el-form-item>
                <el-button type="primary" @click="onSubmit">提交</el-button>
                <el-button>取消</el-button>
            </el-form-item>
    	</el-form>
	</el-dialog>


<script>
export default {
	methods: {
        onSubmit:function(){
            alert(JSON.stringify(this.form));
        }
    },
	data() {
        return {
        form: {
          name: '',
          region: '',
          date1: '',
          date2: ''
        },
	dialogFormVisible: false,
}
</script>
```

这时要新建属性dialogFormVisible	用于管理表单显示，要与之前的表格dialogTableVisible区分

点击提交按钮，调用onSubmit方法，使用JSON.stringify()方法，将JSON数据转换为String类型显示
this.form	用于获取当前vue的form数据模型
显示如下：



![img](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\4`CAJH`1OEC16YMFK}AL[P6.png)



### 案例

要求实现如下效果

![image-20230522103200413](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522103200413.png)

上面是标题，左侧是导航栏，右侧是数据展示区域
数据来源地址：http://yapi.smart-xwork.cn/mock/169327/emp/list

#### 案例分析：

主要步骤为

1. 创建页面，完成页面的整体布局规划
2. 然后分别针对3个部分进行各自组件的具体实现
3. 针对于右侧核心内容展示区域，需要使用异步加载数据，以表格渲染数据



#### 整体布局

在ElementUI的官网中有专门的布局组件

![image-20230522104405853](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522104405853.png)

在EmpView.vue文件中引入布局组件

```vue
<div>
    <el-container>
        <el-header>Header</el-header>	//标题
        <el-container>
            <el-aside width="200px">Aside</el-aside>	//左侧导航栏
            <el-main>Main</el-main>		//数据展示区域
        </el-container>
    </el-container>
</div>
```

修改边框和标题字体颜色

```vue
<el-container style="height: 700px; border: 1px solid #eee">
     <el-header style="font-size: 40px;background-color: rgb(238, 241, 246)">tlias 智能学习辅助系统</el-header>
```



#### 左侧菜单栏

在官网 布局组件案例下有代码，复制之后将内容修改

```vue
<el-aside width="200px"> 
	<el-menu :default-openeds="['1', '3']">
		<el-submenu index="1">
			<template slot="title"><i class="el-icon-message"></i>系统信息管理				</template>
		<el-menu-item index="1-1">部门管理</el-menu-item>
		<el-menu-item index="1-2">员工管理</el-menu-item>
		</el-submenu>
	</el-menu>
</el-aside>
```



#### 右侧数据

插入表格组件	设置操作 按钮组件	并设置数据模型tableData

```vue
<!-- 右侧核心数据部分 -->
<el-main>
	<el-table :data="tableData">
		<el-table-column prop="name" label="姓名" width="180"></el-table-column>
		<el-table-column prop="image" label="图像" width="180"></el-table-column>
		<el-table-column prop="gender" label="性别" width="140"></el-table-column>
		<el-table-column prop="job" label="职位" width="140"></el-table-column>
		<el-table-column prop="entrydate" label="入职日期" width="180"></el-table-column>
		<el-table-column prop="updatetime" label="最后操作时间" width="230"></el-table-column>
		<el-table-column label="操作" >
			<el-button type="primary" size="mini">编辑</el-button>
			<el-button type="danger" size="mini">删除</el-button>
		</el-table-column>
	</el-table>
</el-main>

<script>
export default {
    data() {
        return {
            tableData: []
        }
    },
}
</script>
```

在表格下方添加分页条

```vue
<!--  分页显示-->
<el-pagination 
      background 
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange" 
      layout="total, sizes, prev, pager, next, jumper" 
      :total="1000">
</el-pagination>
```

在表格上面添加表单，用于查询数据

其中入职日期，需要在官网中找到日期范围选择器使用

```vue
<!-- 上方查询表单 -->
<el-form ref="form" :model="form" label-width="80px" :inline="true">
	<el-form-item label="姓名" label-width="50px">
		<el-input v-model="form.name" placeholder="姓名"></el-input>
	</el-form-item>
	<el-form-item label="性别">
		<el-select v-model="form.gender" placeholder="请选择性别">
			<el-option label="男" value="1"></el-option>
			<el-option label="女" value="2"></el-option>
		</el-select>
	</el-form-item>
	<el-form-item label="入职日期">
		<!-- 使用日期范围选择器 -->
		<el-date-picker v-model="form.entrydate" type="daterange" range-separator="至" start-placeholder="开始日期" end-placeholder="结束日期">
		</el-date-picker>
	</el-form-item>
	<el-form-item>
		<el-button type="primary" @click="onSubmit">查询</el-button>
	</el-form-item>
</el-form>
```



#### 数据显示

数据显示需要通过ajax发起异步请求，局部刷新区域

vue中使用ajax，要先安装ajax
		终端中安装: npm install axios
		需要使用axios时，导入axios:  import axios from 'axios'

在mounted方法中绑定，当vue创建成功后就调用ajax方法
获取到的数据与tableData绑定

```
mounted() {
        axios.get("http://yapi.smart-xwork.cn/mock/169327/emp/list").then((result) => {
            this.tableData = result.data.data;
        });
    }
```



而显示性别时，只显示1或2，需要用到插槽，相当于自定义显示
可通过scope.row	获取到表单的数据，再通过三元运算判断

```vue
<el-table-column prop="gender" label="性别" width="140">
	<template slot-scope="scope">
		{{scope.row.gender==1 ? "男":"女"}}
	</template>
</el-table-column>
```

显示图片也是，只显示了地址	通过img标签，设置其src属性值，并设置长宽等



```vue
<el-table-column prop="img" label="图像" width="180">
	<template slot-scope="scope">
		<img :src="scope.row.image" width="100px" height="70px">
	</template>
</el-table-column>
```



## vue路由

- 介绍：Vue Router 是 Vue的官方路由。

- 组成:

  - VueRouter:路由器类，根据路由请求在路由视图中动态渲染选中的组件
  - <router-link>:请求链接组件，浏览器会解析成<a>
  - <router-view>:动态视图组件，用来渲染展示与路由路径对应的组件

  VueRouter相当于路由表    <router-link>发起路由请求，在路由表中找，若找到<router-view>渲染路由路径对应的组件

  路由请求最后会被解析为超链接

  

  在上面的案例中，我们要实现点击左侧部门管理和员工管理，可以进行跳转

  准备DeptView文件，放在EmpView同一目录下 相当于部门管理

  网页url ：http://localhost:7000/#/

  前端路由：URL中的hash(#号之后的内容）与组件之间的对应关系

### vue入门

安装router插件

```
npm install vue-router@3.5.1
```

在vue项目的src/router/index.js文件，是用于管理路由信息的
其中routes就为路由表	

```js
import Vue from 'vue'
import VueRouter from 'vue-router'
import HomeView from '../views/HomeView.vue'

Vue.use(VueRouter)

const routes = [
  {
    path: '/',		//path为网页url访问的路径
    name: 'home',	//name为别名
    component: HomeView	//component为访问该路径，加载哪个vue组件	
						//通过上方import将HomeView引入
  },
  {
    path: '/about',
    name: 'about',
    component: () => import('../views/AboutView.vue')//第二种引入方式 直接在用的地方引入	
  }
]
```

变为

```js
const routes = [
  {
    path: '/emp',	//访问/emp 即访问员工管理
    name: 'emp',	
    component: () => import('../views/tlias/EmpView.vue')
  },
  {
    path: '/dept',	//访问/dept 即访问部门管理
    name: 'dept',
    component: () => import('../views/tlias/DeptView.vue')
  },
  {
    path: '/',	//项目刚启动，自动访问/	
    redirect: "/dept"	//重定向 使一开始访问部门管理
  }
]
```

在EmpView和DeptView中加上路由请求 <router-link to ="">	to即请求的路径

```
<template slot="title"><i class="el-icon-message"></i>系统信息管理</template>
<el-menu-item index="1-1">
	<router-link to="/dept">部门管理</router-link>
</el-menu-item>
<el-menu-item index="1-2">
	<router-link to="/emp">员工管理</router-link>
</el-menu-item>
```

在根Vue App.vue中加上<router-view>标签，表示在这里展示路由请求的vue组件

```vue
<div id="app">
    <router-view></router-view>
  </div>
```



## 打包部署

### 打包

通过vsCode	打包文件 生成dist目录，存放了需要发布的前端资源

![image-20230522135848196](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522135848196.png)



### 部署前端项目

部署在nginx上

nginx: Nginx是一款轻量级的Web服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器。其特点是占有内存少，并发能力强，在各大型互联网公司都有非常广泛的使用。

nginx的目录结构：

![image-20230522140023906](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522140023906.png)

我们要将项目放在html中，即将dist目录中的文件复制到html中

nginx默认占用端口80，而window中通常80端口被占用	
通过修改conf/nginx.conf	server {	listen       80;	可进行修改端口

修改之后 访问http://localhost:端口号	即可访问项目	部署在本地



