​		



### 如何部署web工程到tomcat

1.把项目复制到tomcat包下的webapps文件夹下 可直接在网页上输入

 http://ip:port/工程名/目录下/文件名 访问



2.

tomcat只支持utf-8格式 

找到Tomcat下的conf目录\catalina\localhost\下,创建如下的配置文件:

<Context docBase="E:\IdeaProjects\JavaWeb\out\artifacts\web03_war_exploded" path="/web03"/>



Context表示一个了工程上下文    path表示工程的访问路径: /abc             docBase表示你的工程目录在哪里


### root工程

当我们在浏览器地址栏中输入访问地址如下:

http://ip:port/	                  没有工程名的时候，默认访问的是ROOT工程。

当我们在浏览器地址栏中输入的访问地址如下:
http://ip:port/工程名/		没有资源名，默认访问index.html页面



在使用tomcat中版本和jdk要相符合



## servlet



Servlet 是运行在服务器上的一个java小程序，它可以接收客户端发送过来的请求，并响应数据给客户端。



手动实现 Servlet程序

1、编写一个类去实现servlet 接口

2、实现service方法，处理请求，并响应数据

3、到web.xml中去配置servlet程序的访问地址

```

在web.xml中
<servlet>
    <servlet-name>Helloservlet</servlet-name>
    <servlet-class>com.atguigu.servlet.Helloservlet</servlet-class>
</servlet>

<servlet-mapping>
    <servlet-name>Helloservlet</servlet-name>
    <url-pattern>/hello</url-pattern>
</servlet-mapping>
```

servlet-name 				起别名

servlet-class 				 servlet程序全类名

servlet-mapping		  配置servlet访问的地址

url-pattern					配置访问地址

/									 斜杠在服务器解析的时候，表示地址为:http:/ /ip: port/工程路径

hello 							表示http:/ /ip: port/工程路径/hello可以访问servlet程序



Servlet生命周期

1、执行Servlet构造器方法

2、执行init初始化方法

1，2 是在第一次访问的时候创建servlet程序会调用。

3、执行service方法

每次访问都会调用。

4、执行destroy销毁方法

在web工程停止的时候调用。

## ServletConfig类

### 作用

1、可以获取Servlet程序的别名servlet-name的值

2、获取初始化参数init-param

3、获取ServletContext对象！！！！

```
System.out.println("Helloservlet别名为:" + servletConfig.getServletName());

System.out.println("初始化参数username值为:" + servletConfig.getInitParameter("username"));

System.out.println("初始化参数url值为:" + servletConfig.getInitParameter("url"));

System.out.println(servletConfig.getServletContext());


<init-param>
            <param-name>username</param-name>
            <param-value>root</param-value>
        </init-param>
        <init-param>
            <param-name>url</param-name>
            <param-value>www.baidu.com</param-value>
        </init-param>
```

可在web.xml中设置参数！！！

如果要重写init()方法，要加上 super.init(config);		调用父类的init方法 把父类中 config 保存下来



## ServletContext类

ServletContext是一个**全局**的储存信息的空间，服务器开始就存在，服务器关闭才释放。

1、ServletContext是一个接口，它表示 Servlet上下文对象

2、一个web工程，只有一个 ServletContext对象实例。

3、ServletContext对象是一个域对象。

什么是域对象?

域对象，是可以像Map一样存取数据的对象，叫域对象。这里的域指的是存取数据的操作范围。

​						存数据					取数据						删除数据
Map				put()						get()							remove()
域对象		setAttribute()		getAttribute()			removeAttribute();

### 作用

1、获取 web.xml中配置的上下文参数context-param

属于整个web工程 所以web.xml    不写在<servlet></servlet>中    **！！！！！**

2、获取当前的工程路径，格式:/工程路径

3、获取工程部署后在服务器硬盘上的绝对路径

4、像 Map一样存取数据



```
ServletContext servletContext = getServletConfig().getServletContext();

System.out.println("参数username为:"+ servletContext.getInitParameter("username"));
        System.out.println("参数url为:" + servletContext.getInitParameter("url"));

        System.out.println("当前工程路径为:" + servletContext.getContextPath());

        System.out.println("工程部署路径"+ servletContext.getRealPath("/"));
        
```

第3 绝对路径用/

/ 			斜杠被服务器解析地址为: http: //ip : port/工程名/

解析出来为C:\Users\ljxxx\IdeaProjects\java web\out\artifacts\06_servlet_war_exploded\

也可在web下建其他文件夹		 /文件夹名	获取其绝对路径



servletContext,getResourceAsStream("/路径");

获取文件，像流一样传输，创建的对象是一个流对象**！！！！！**

ServletContext. getResourceAsStream(String path)：默认从WebAPP根目录下取资源





## 请求的HTTP协议



请求又分为GET请求，和POST请求两种



### GET请求

GET/06_servlet/a.html HTTP/1.1

Accopt: application/x-ms-application，image/ jpeg，application'xaml/+xml，image/g if， image/pioeg， applicationl'x-ms-.bup，*/*

Accept-Language: zh-CN

Uscr-Agont:Mozilla/4.0 (compatiblo;MSIE 8.0; Windows NT 6.1;Winb4; xb4;Irident/4.0; .NET CLR 2.0.50727，8L.2; .NE &R3.5.30729; .NET CLR 3.0.30729 ; Media Center PC 6.0)

UA-CPU:AMD64

Accept-Encoding: gzip, deflate

Host: localhost:8080

Connection: Kcep-Alive




1、请求行
(1)请求的方式					GET

2请求的资源路径[+?+请求参数]

3)请求的协议的版本号			HTTP/1.1

2、请求头

key : value		组成不同的键值对，表示不同的含义。

Accept:告诉服务器,客户端可以接收的数将类型

Accept-Language:告诉版务器客户追可以接收的语言类型

zh_CN					中文中国
cn_us					 英文美国

User-Agent:就是浏览器的信息

Accept-Encoding:告诉服务器，客户端可以接收的数撂缟码（压缩）格式Host:表示请求的服务器ip和端口号

Connection:告诉服务器请求连接如何处理

​					Kecp-Alivc	  告诉服务努回传数据不要马上关闭，保持一小段时间的连接

​					Closed			马上关闭


### POST请求

1、请求行
(1)请求的方式					GET

2请求的资源路径[+?+请求参数]

3)请求的协议的版本号			HTTP/1.1

2、请求头

1)key : value			不同的请求头，有不同的含义

空行

3、请求体				发送给服务器的数据



### 哪些是GET请求，哪些是POST请求

GET请求有哪些:

1、form标签method=get

2、a标签

3、link标签引入css

4、Script标签引入js文件

5、img标签引入图片

6、iframe引入 html页面

7、在浏览器地址栏中输入地址后敲回车

pOST请求有哪些:

form标签method=post



### HTTP响应

1、响应行

(1)响应的协议和版本号

(2)响应状态码

(3)响应状态描述符

2、响应头

(1) key : value

不同的响应头，有其不同含义

空行

3、响应体---->>>就是回传给客户端的数据



状态码	200		表示请求成功

​				302		表示请求重定向

​				404		表示请求服务器已收到，但请求地址错误

​				500		表示服务i去已经收到请求，但是服务器内部错误(代码错误)



### MIME类型

格式

大类型/小类型			.html		text/html	等等

## HttpServletRequest类

### 作用

每次只要有请求进入Tomcat服务器，Tomcat服务器就会把请求过来的HTTP协议信息解析好封装到Request对象中。然后传递到service方法(doGet和doPost)中给我们使用。我们可以通过HttpServletRequest对象，获取到所有请求的信息。

### 常见方法

i. getRequestURI()							获取请求的资源路径

ii. getRequestURL()						  获取请求的统一资源定位符（绝对路径〉

iii.getRemoteHost()						 获取客户端的ip地址

iv. getHeader()								 获取请求头

v. getParameter()							获取请求的参数

vi. getParameterValues()			   获取请求的参数(多个值的时候使用)

vii.getMethod()							    获取请求的方式GET或POST

viii.setAttribute(key , value);		 设置域数据

ix. getAttribute(key );					 获取域数据

x. getRequestDispatcher()			获取请求转发对象

getScheme()									获取当前链接使用的协议

getSererName()							  获取服务器的ip

```
req.setCharacterEncoding("UTF-8");
//        i. getRequestURI()                     获取请求的资源路径

            System.out.println("资源路径为"+ req.getRequestURI());

//        ii. getRequestURL()                   获取请求的统一资源定位符（绝对路径〉

          System.out.println("绝对路径为"+ req.getRequestURL());

//        iii.getRemoteHost()                  获取客户端的ip地址

            System.out.println("客户端ip地址为"+req.getRemoteHost());
//        iv. getHeader()                        获取请求头

            System.out.println("请求头User-Agent为"+req.getHeader("User-Agent"));

//        vii.getMethod()                        获取请求的方式GET或POST

        System.out.println("请求方式"+req.getMethod());

//        v. getParameter()                      获取请求的参数

        String username = req.getParameter("username");
        String password = req.getParameter("password");

        System.out.println("用户名为："+ username);
        System.out.println("密码为："+ password);



//        vi. getParameterValues()          获取请求的参数(多个值的时候使用)

        String[] hobby = req.getParameterValues("hobby");
        System.out.println("兴趣爱好："+ Arrays.asList(hobby));
```

post传参时 中文会乱码

使用req.setCharacterEncoding("UTF-8"); 设置字符集			且要在获取参数之前使用（是所有参数之前）



## 请求转发

**服务器中有两个servlet程序 请求从servlet1到servlet2的过程叫请求转发**

```
servlet1

String username = req.getParameter("username");
System.out.println("servlet1查看材料:"   +  username);
System.out.println("servlet1处理中");
//设置一个参数给req
req.setAttribute("key1","servlet1的章");
//获取目的url
RequestDispatcher requestDispatcher = req.getRequestDispatcher("/servlet2");
//向前传送
requestDispatcher.forward(req,resp);

servlet2
String username = req.getParameter("username");

        System.out.println("servlet2查看材料"+username);
        //确定值
        Object key1 = req.getAttribute("key1");

        System.out.println("数据有的章为:"+  key1);

        System.out.println("servlet2处理中");
```



RequestDispatcher requestDispatcher = req.getRequestDispatcher("/servlet2");

通过函数getRequestDispatcher	把servlet2转换为url存储在requestDispatcher 中



setAttribute(”属性名“，”属性值“)

把指定的属性设置为指定的值。如果不存在具有指定名称的属性，该方法将创建一个新属性。

getAttribute("属性名")读取



RequestDispatcher.forward(xxx，xxx)方法 	RequestDispatcher 为跳转页面的Url  带着xxx，xxx参数过去

请求转发的特点:

1、浏览器地址栏没有变化:

2、他们是一次请求

:3、他们共享Request域中的数据:

4、可以转发到WEB-INF自录下:

5、不能可以访问工程以外的资源	如百度



## 请求重定向

**请求重定向，是指客户端给服务器发请求，然后服务器告诉客户端说。我给你一些地址。你去新地址访问。**叫**请求重定向（因为之前的地址可能已经被废弃)。**

和请求转发区别在于 请求重定向为服务器再次发给客户端 然后客户端再转发到另外的程序

```
response1

System.out.println("经过了response1");
//设置响应码
resp.setStatus(302);
//设置响应头
resp.setHeader("Location","http://localhost:8080/07_servlet/response2");

resp.sendRedirect("http://localhost:8080/07_servlet/response2");

response2
resp.setCharacterEncoding("UTF-8");
        resp.setHeader("Content-Type","text/html;charset=UTF-8");
       resp.getWriter().write("response2回应处理");
```

resp.sendRedirect()等同于

resp.setStatus();
resp.setHeader();



请求重定向的特点:

1、浏览器地址栏会发生变化

2、两次请求
'3、不共享Request域中数据

4、不能访问WEB-INF下的资源

5、可以访问工程外的资源





## base标签

用于head标签中

***所有相对路径在工作时候都会参照当前浏览器地址栏中的地址来进行跳转。***

base标签作用可以把浏览器地址(相对路径)固定为一个值 <base href=""></base>



## web中 / 斜杠的不同意义

在web中/斜杠是一种绝对路径。

/斜杠如果被浏览器解析，得到的地址是:http://ip:port/

<a href="/">斜杠</ a>




/斜杠如果被服务器解析，得到的地址是: http://ip:port/工程路径

1、<ur1-pattern>/ servlet1</url-pattern>

2、servletcontext.getRealPath(“1");

3、request.getRequestDispatcher(“ /");

特殊情况:response.sendRediect("/");	把斜杠发送给浏览器解析	得到http://ip:port/



## HttpServletResponse类

HttpServletResponse类的作用

HttpServletResponse类和HttpServletRequest类一样。每次请求进来，Tomcat服务器都会创建一个Response对象传递给 servlet程序去使用。HttpServletRequest表示请求过来的信息，HttpServletResponse表示所有响应的信息

我们如果需要设置返回给客户端的信息，都可以通过HttpServletResponse对象来进行设置。



resp.setContentType()写的是响应头

resp.setHeader()写的是消息头，包含了响应头，请求头等，相当于再写个头

### 两个输出流

字节流

getOutputStream();			常用于下载(传递二进制数据)
字符流

getWriter();						  常用于回传字符串（常用)

两个流同时只能使用一个。使用了字节流，就不能再使用字符流，反之亦然。



### 中文乱码问题

#### 方法一

//设置服务器字符集为UTF-8

resp.setCharacterEncoding( "UTF-8");

//通过响应头，设置浏览器也使用UTF-8字符集

resp.setHeader( name: "Content-Type",value: "text/html; charset=UTF-8");



#### 方法二

resp.setContentType("text/html;charset=UTF-8");

等同于方法一 会自动配置服务器和浏览器的字符集

此方法要在获取流对象前部署才有效



## QueryRunner

DBUtils包所提供的QueryRunner类，是针对数据库链接池的使用

QueryRunner中一共有6种方法：

•execute（执行SQL语句）

•batch（批量处理语句）

•insert（执行INSERT语句）

•insertBatch（批量处理INSERT语句）

•query（SQL中 SELECT 语句）		返回值是一个Object对象,该Object对象保存着从数据库获取的数据,

•update（SQL中 INSERT, UPDATE, 或 DELETE 语句）(最为常用）返回值为影响的行数



## 书城第二阶段

### jdbcUnits.java

```

private static DruidDataSource dataSource;
static {
    try {
        Properties properties=new Properties();
        //读取jdbc.properties属性配置文件
        InputStream resourceAsStream = JdbcUnits.class.getClassLoader().getResourceAsStream("jdbc.properties");
        //从流中加载数据
        properties.load(resourceAsStream);
        //创建数据库连接池
        dataSource= (DruidDataSource) DruidDataSourceFactory.createDataSource(properties);

        System.out.println(dataSource.getConnection());
    } catch (Exception e) {
        e.printStackTrace();
    }
    
    public static Connection getConnection(){
        //如果返回null说明获取连接失败
        Connection connection=null;
        try {
            connection=dataSource.getConnection();
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return null;
    }
    
    public static void close(Connection connection){
        if (connection!=null){
            try {
                connection.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
```

### BaseDao.java

```
public abstract class BaseDao {
    //使用dbutils操作数据库
    private QueryRunner queryRunner=new QueryRunner();
    //updata()方法 执行insert updat delete语句

    public int updata(String sql,Object ... args){
        Connection connection= JdbcUnits.getConnection();
        try {
            return queryRunner.update(connection,sql,args);
        }catch (SQLException e){
            e.printStackTrace();
        }
        return -1;
    }
/*
*查询返回单个javaBean的sqL语句
* @param type   返回的对象类型
* @param sql    执行的sqL语句
* @param args   sql对应的参数值
* @param <T>    返回的类型的泛型
@return*/
    public <T> T queryForOne(Class<T> type,String sql,Object ... args){
        Connection connection=JdbcUnits.getConnection();
        try {
            return queryRunner.query(connection,sql,new BeanHandler<T>(type),args);
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUnits.close(connection);
        }
        return null;
    }

    /*
*查询返回多个javaBean的sqL语句
* @param type   返回的对象类型
* @param sql    执行的sqL语句
* @param args   sql对应的参数值
* @param <T>    返回的类型的泛型
@return*/
    public <T> List<T> queryList(Class<T> type, String sql, Object ... args){
        Connection connection=JdbcUnits.getConnection();
        try {
            return queryRunner.query(connection,sql,new BeanListHandler<T>(type),args);
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUnits.close(connection);
        }
        return null;
    }
    /*查询返回一行一列javaBean的sqL语句
     * @param type   返回的对象类型
     * @param sql    执行的sqL语句
     * @param args   sql对应的参数值
     @return*/
    public Object queryList(String sql, Object ... args){
        Connection connection=JdbcUnits.getConnection();
        try {
            return queryRunner.query(connection,sql,new ScalarHandler(),args);
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            JdbcUnits.close(connection);
        }
        return null;
    }
```

Object ... args		可变长参数 可以传无数个参数 username，password	！！！

**其中 参数 Class<T> type 要写 例如 User.class**







## jsp

jsp的全换是java server pagesoJava 的服务器页面。

jsp的主要作用是代替servlet程序回传 html页面的数据。

存放在web目录下

### 本质

jsp 页面本质上是一个 servlet程序。

当我们第一次访问jsp页面的时候。Tomcat服务器会帮我们把jsp页面翻译成为一个java源文件。并且对它进行编译成为.class字节码程序。

### jsp头部的page指令

```
<%@ page contentType="text/html;charset=UTF-8"
         import="com.atguigu.*"
         pageEncoding="UTF-8"
         autoFlush="true"
         buffer="8kb"
         errorPage="/error500.jsp"

         language="java" %>
```

jsp的 page指令可以修改jsp页面中一些重要的属性，或者行为。

i.language属性				表示 jsp翻译后是什么语言文件。暂时只支持java。

i. contentType属性		 表示jsp返回的数据类型是什么。也是源码中response.setContentType()参数值

iii.pageEncoding属性	 表示当前jsp页面文件本身的字符集。

iv. import属性				 跟java源代码中一样。用于导包，导类。

-======两个属性是给out输出流使用-====================

out为jsp的java文件中用于输出到浏览器的函数

v. autoFlush属性			设置当out输出流缓冲区满了之后,是否自动刷新冲级区。默认值是true 

vi. buffer属性				  设置out缓冲区的大小。默认是8kb

======两个属性是给out输出流使用-====================

vii.errorPage属性	设置当jsp页面运行时出错，自动跳转去的错误页面路径。

一般为斜杠打头，映射到web目录下

viii.isErrorPage 		属性设置当前jsp页面是否是错误信息页面。默认是false。如果是true可以获取异常信息。

ix. session				 属性设置访问当前jsp页面，是否会创建HttpSession对象。默认是true 。

x.extends				  属性设置jsp翻译出来的java类默认继承谁。



### 常用脚本

脚本不可穿插使用

例如<%	<%=%>	%>

#### 声明脚本(极少用)

格式<%!		%>		作用：可以给jsp翻译出来的java类定义属性和方法，静态代码块，内部类等等

```
<body>

<%--1.声明类的属性--%>
<%!
    private Integer id;
    private String name;
    private static Map<String,Object> map;
%>

<%--2.声明static代码块--%>
<%!
    static {
        map=new HashMap<String,Object>();
        map.put("key1","value1");
        map.put("key2","value2");
        map.put("key3","value3");
    }
%>
<%--3.声明类方法--%>
<%!
    public int abc(){
        return 12;
    }
%>
<%--4.声明内部类--%>
<%!
    public static class A{
        private Integer id=12;
        private String abc="abc";
    }
%>
</body>
```



#### 表达式脚本(常用)

格式	<%=表达式%>	作用：在jsp页面上输出数据。

表达式脚本的特点:

1、所有的表达式脚本都会被翻译到_jspService()方法中

2、表达式脚本都会被翻译成为out.print()输出到页面上

3、由于表达式脚本翻译的内容都在 _jspService()方法中,所以_jspServicel)方法中的对象都可以直接使用。例如resp，req等等

4、表达式脚本中的表达式不能以分号结束。

输出语句为 out.print(表达式);加了分号 语句提前结束



#### 代码脚本

格式 <%	java语句	%>	作用：可以在jsp页面中，编写我们需要的功能（写的是java语句）

例如 if语句	for循环	等等

写方法等	要用声明脚本

代码脚本的特点是:

1、代码脚本翻译之后都_jspService()在方法中_
2、代码脚本由于翻译到_jspService()方法中，所以在_jspService()方法中的现有对象都可以直接使用。

3、还可以由多个代码脚本块组合完成一个完整的java语句。

例如<%for(int j=0;j<=10;j++){%>

<%

system.out.println(j)

}

%>

4、代码脚本还可以和表达式脚本一起组合使用，在jsp页面上输出数据



### jsp中三种注释



#### html注释

<!--这是htmL注释-->

html注释会被翻译到 java源代码中。在_jspService方法里，以out.writer输出到客户端。

#### java注释

<%

//单行java注释

/*多行java注释*/

%>

java注释会被翻译到java源代码中。

#### jsp注释

<%--这是jsp注释--%>		jsp注释快捷键 ctrl+shift+\

jsp注释可以注掉，jsp页面中所有代码。



### jsp中的内置对象

jsp中的内置对象，是指Tomcat在翻译jsp页面成为servlet源代码后，内部提供的九大对象，叫内置对象。

public void _jspService(final javax.servlt.htrp.ittpServletRequest request,final javax.servlet.httpServrletResponse response) throws java.io.IOException,javax.servlet.ServletException {



request									请求对象
response	 							响应对象
pageContext						   jsp的上下文对象
session								    会话对象
application							 ServletContext对象
config									 ServletConfig对象
out										  jsp输出流对象
page										指向当前jsp的对象
exception								异常对象(要在jsp头部指令中isErrorPage 开启，获取异常信息)

### jsp中四大域对象

域对象是可以像Map一样存取数据的对象。四个域对象功能一样。不同的是它们对数据的存取范围。

四个域对象分别是:

pageContext(PageContextlmpl类)	当前jsp页面范围内有效

request(HttpservletRequest类)		一次请求内有效

session(Httpsession类)						一个会话范围内有效〈打开浏览器访问服务器，直到关闭浏览器)

application(ServletContext类)			整个web工程范围内都有效〈只要web工程不停止，数据都在) appliocation在工程开始的时候创建，只有工程结束才销毁(重新部署)

```
scope.jsp
<body>
<h1>这是scope.jsp页面</h1>
<%
    pageContext.setAttribute("key","pageContext");
    request.setAttribute("key","request");
    session.setAttribute("key","session");
    application.setAttribute("key","application");
%>
pageContext域中值：<%=pageContext.getAttribute("key")%><br>
request域中值：<%=request.getAttribute("key")%><br>
session域中值：<%=session.getAttribute("key")%><br>
application域中值：<%=application.getAttribute("key")%><br>

<%
    request.getRequestDispatcher("/scope2.jsp").forward(request,response);
%>
</body>

scope2.jsp
<body>
<h1>这是scope2.jsp页面</h1>

pageContext域中值：<%=pageContext.getAttribute("key")%><br>
request域中值：<%=request.getAttribute("key")%><br>
session域中值：<%=session.getAttribute("key")%><br>
application域中值：<%=application.getAttribute("key")%><br>


</body>
```





### out输出和response输出

```
<%
    out.write("out输出1 <br/>");
    out.write("out输出2 <br/>");
    
    response.getWriter().write("response输出1 <br/>");
    response.getWriter().write("response输出2 <br/>");


%>
```

会发现response输出语句在out之上

因为服务器中有 out缓冲区，response缓冲区

当jsp页面中所有代码执行完成后会做以下两个操作:

1、执行out.flush()操作，会把out缓冲区中的数据追加写入到response缓冲区末尾

2、会执行response的刷新操作。把全部数据写给客户端

数据其实是一起加到浏览器中，而不是一个一个加

可用

```
<%
    out.write("out输出1 <br/>");
    out.flush();
    out.write("out输出2 <br/>");

    response.getWriter().write("response输出1 <br/>");
    response.getWriter().write("response输出2 <br/>");


%>
```

这样就是out输出1在最上面 out输出2在最下面

由于jsp翻译之后，底层源代码都是使用out 来进行输出，所以一般情况下。我们在jsp页面中统一使用out来进行输出。避免打乱页面输出内容的顺序。

out中输出有两种

out.write()	输出字符串没有问题，输出整形等会乱码

原因：write中输出整形的方法有 String ***=(char)c 强转，而整形强转为字符串类型会转换为对应的ASCII中的字符，导致乱码，而字符串类型强转为整形会转换为ASCII中对应的值

out.print()	输出任意数据都没有问题（会转换成字符串形式调用write方法输出)

#### 结论 

使用out.print输出

### 常用标签

#### 静态包含

格式：<%@ include file="	"%>在file中写入单独的jsp文件，其中写入脚页信息，修改也只需要修改单独的jsp文件即可

场景  网页通常分为三块 头部信息 主体信息 脚页信息 ，当脚页信息中一处要修改，可用静态包含一次性修改

```
main.jsp
<body>
头部信息<br>
主体信息<br>
<%@ include file="/include/footer.jsp"%>
</body>

footer.jsp
<body>
脚页信息<br>
修改信息
</body>
```

静态包含的特点:

1，静态包含不会翻译被包含的jsp页面。(就是不会生成footer.java文件)

2，静态包含其实是把被包含的jsp页面的代码拷贝到包含的位置执行输出。



#### 动态包含 

格式：<jsp:include page="	"></jsp:include>>page中写入要传入的jsp文件位置

```
main,jsp
<body>
头部信息<br>
主体信息<br>
<jsp:include page="/include/footer.jsp">
 	<jsp:param name="username" value="ljxxx"/>
    <jsp:param name="password" value="123456"/>
</jsp:include>
</body>

footer.jsp
<body>
脚页信息<br>
修改信息
<%=request.getParameter("password")%>
</body>
```

动态包含的特点:

1，动态包含会把包含的jsp页面也翻译成为java代码。

2，动态包含底层代码使用如下代码去调用被包含的jsp页面执行输出。

JspRuntimeLibrary.include( request，response，"/include/footer.jsp", out，false);

main.jsp把自己的request,response, out对象都传递给了footer.jsp页面去使用

3.动态包含还可以传递参数

<jsp:param name="username" value="ljxxx"/>

<%=request.getParameter("password")%>

#### 请求转发

格式：<jsp:forward page="	"></jsp:forward>>

功能就是请求转发

```
<%
    pageContext.setAttribute("key","pageContext");
    request.setAttribute("key","request");
    session.setAttribute("key","session");
    application.setAttribute("key","application");
%>
pageContext域中值：<%=pageContext.getAttribute("key")%><br>
request域中值：<%=request.getAttribute("key")%><br>
session域中值：<%=session.getAttribute("key")%><br>
application域中值：<%=application.getAttribute("key")%><br>

<%--<%
    request.getRequestDispatcher("/scope2.jsp").forward(request,response);
%>--%>
<jsp:forward page="scope2.jsp"></jsp:forward>
</body>
```

request.getRequestDispatcher("/scope2.jsp").forward(request,response);和
<jsp:forward page="scope2.jsp"></jsp:forward>>功能一样

作业1

在jsp页面中输出九九乘法

```
<%for (int i=1;i<=9;i++){%>
    <tr>
        <%for (int j=1;j<=i;j++){%>
        <td><%=j + "x" + i + "=" + (i*j) %>
        </td>
    <%}%>
    </tr>
        <%}%>
```



## Listener监听器

1、Listener 监听器它是JavaWeb的三大组件之一。JavaWeb的三大组件分别是:Servlet程序、Filter过滤器、Listener监听器。

2、Listener它是JavaEE的规范,就是接口

3、监听器的作用是，监听某种事物的变化。然后通过回调函数，反馈给客户（程序）去做一些相应的处理。



### ServletContextListener监听器

ServletContextlistener它可以监听servletContext对象的创建和销毁。

ServletContext对象在web工程启动的时候创建，在web工程停止的时候销毁。**！！！！！**

监听到创建和销毁之后都会分别调用servlefContextListener监听器的方法反馈。

两个方法分别是

public interface ServletContextListener extends EventListener {

​	//在ServletContext对象创建之后调用，做初始化

​    void contextInitialized(ServletContextEvent var1);

​	//在ServletContext对象销毁之后调用

​	void contextDestroyed(ServletContextEvent var1);

}



如何使用ServletContextListener 监听器监听servletContext对象。使用步骤如下:
1、编写一个类去实现ServletContextListener

2、实现其两个回调方法

```
public class ServletContextListenerImpl implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        System.out.println("ServletContext对象被创建");
    }

    @Override
    public void contextDestroyed(ServletContextEvent servletContextEvent) {
        System.out.println("ServletContext对象被销毁");
    }
}
```

3、到web.xml 中去配置监听器

```
<listener>
    <listener-class>com.atguigu.listener.ServletContextListenerImpl</listener-class>
</listener>
```







## EL表达式

EL表达式的全称是:Expression Language 。是表达式语言。

EL表达式的什么作用:EL表达式主要是代替jsp页面中的表达式脚本在jsp页面中进行数据的输出。

因为EL表达式在输出数据的时候，要比jsp的表达式脚本要简洁很多。

```
<%
    request.setAttribute("key","123");
%>
jsp表达式脚本输出key的值：<%=request.getAttribute("key")%><br/>
EL表达式脚本输出key的值：${key}
```

EL表达式格式：${key}	输出的是域中的key值

且若输出的是空值 例如

jsp表达式脚本输出key的值：<%=request.getAttribute("key1")%><br/>
EL表达式脚本输出key的值：${key1}

jsp表达式脚本会输出null字符串，而EL表达式脚本会输出空串，就是空，对于用户而言空串更友好



### 输出顺序

EL表达式主要是在jsp页面中输出数据。主要是输出域对象中的数据。

当四个域中都有相同的Ikey的数据的时候，EL表达式会按照四个域的从小到大的顺序去进行搜索，找到就输出。

顺序为pageContext，request，session，application



### 输出复杂的bean对象

需求 输出person类中的普通属性，数组属性，list集合属性，map集合属性

```
Person.java
private String name;
    private String[] phone;
    private List<String> cities;
    private Map<String,Object> map;

b.jsp
<%
    Person person = new Person();
    person.setName("ljxxx");
    person.setPhone(new String[]{"123","1234","12345"});
    ArrayList<String> cities = new ArrayList<>();
    cities.add("北京");
    cities.add("上海");
    cities.add("广州");
    person.setCities(cities);
    HashMap<String, Object> map = new HashMap<>();
    map.put("key1","values1");
    map.put("key2","values2");
    map.put("key3","values3");
    map.put("key4","values4");
    person.setMap(map);
    pageContext.setAttribute("person",person);
%>

        输出Person${person}<br/>
        输出Person的name属性${person.name}<br/>
        输出Person的phone数组属性值${person.phone[0]}<br/>
        输出Person的cities集合中的元素值${person.cities}<br/>
        输出Person的cities集合中个别元素值${person.cities[1]}<br/>
        输出Person中map集合${person.map}<br/>
        输出Person中map集合某个key的值${person.map.key1}<br/>
</body>
```

输出数组值要写[]其中写入具体值 ，否则会返回其地址

输出list集合可不用写[]，若要输出具体的值则写

输出map中某个key的值直接用 map.(key的名称)



### 运算

会返回运算你的结果

#### 关系运算

返回true 或 false

== 或 eq		!= 或 ne		< 或 lt		》或 gt		<= 或 le		》= 或 ge	

#### 逻辑运算

返回true 或 false

&&或and		||或or		!或not

#### empty运算

${empty 要判断的对象}

empty运算可以判断一个数据是否为空，如果为空，则输出true,不为空输出false 

以下几种情况为空:

1、值为null 值的时候，为空

2、值为空串的时候，为空

3、值是object类型数组,长度为零的时候

4、list集合，元素个数为零

5、map集合，元素个数为零

```
<%
    request.setAttribute("emptynull",null);

    request.setAttribute("emptystr","");

    request.setAttribute("emptyobj",new Object[]{});

    ArrayList<String> list = new ArrayList<>();
    request.setAttribute("emptylis",list);

    HashMap map = new HashMap<>();
    request.setAttribute("emptymap",map);

%>
${empty emptynull}<br/>
${empty emptystr}<br/>
${empty emptyobj}<br/>
${empty emptylis}<br/>
${empty emptymap}<br/>
```



#### 三元表达式

表达式1?表达式2:表达式3

如果表达式1的值为真，返回表达式2的值，如果表达式1的值为假，返回表达式3的值。



#### "."点运算和[]中括号运算

.点运算，可以输出Bean对象中某个属性的值。

[]中括号运算，可以输出有序集合中某个元素的值。

并且中括号运算,还可以输出map集合中 key里含有特殊字符的 key的值。

例如map中的数据为

("a.a.a","aaavalues")("b+b+b","bbbvalues")("c-c-c","cccvalues")

这时输出要用 map["a.a.a"]输出，要用单引号或双引号框起来



### EL中的隐含对象

| 变量             | 类型                 | 作用                                                 |
| ---------------- | -------------------- | ---------------------------------------------------- |
| pageContext      | PageContextimpl      | 它可以获取jsp中的九大内置对象                        |
| pageScope        | Map<String,Object>   | 它可以获取pageContext域中的数据                      |
| requestScope     | Map<String,Object>   | 它可以获取Request域中的数据                          |
| sessionScope     | Map<String,Object>   | 它可以获取session域中的数据                          |
| applicationScope | Map<string,Object>   | 它可以获取ServletContext 域中的数据                  |
| param            | Map<String,String>   | 它可以获取请求参数的值                               |
| paramValues      | Map<String,String[]> | 它也可以获取请求参数的值,获取多个值的时候使用。      |
| header           | Map<String,String>   | 它可以获取请求头的信息                               |
| headerValues     | Map<String,String[]> | 它可以获取请求头的信息,它可以获取多个值的情况        |
| cookie           | Map<String,Cookie>   | 它可以获取当前请求的Cookie信息                       |
| initparam        | Map<string,string>   | 它可以获取在web.xml中配置的<context-param>上下文参数 |

##### pageContext

使用

1.协议

2.服务器ip 

3.服务器端口

4.获取工程路径

5.获取请求方却

6.获取客户端 ip 地址

7.获取会话的id编号

request.getScheme()它可以获取请求的协议

request.getServerName()获取请求的服务器ip或域名

request.getServerPort()获取请求的服务器端口号

getContextPath()获取当前工程路径

request.getMethod()获取请求的方式(GET或POST)

request.getRemoteHost()获取客户端的ip地址

session.getId()获取会话的唯一标识

```
1.协议${pageContext.request.scheme}<br/>

2.服务器ip${pageContext.request.serverName}<br/>

3.服务器端口${pageContext.request.serverPort}<br/>

4.获取工程路径${pageContext.request.servletPath}<br/>

5.获取请求方却${pageContext.request.method}<br/>

6.获取客户端ip地址${pageContext.request.remoteHost}<br/>

7.获取会话的id编号${pageContext.session.id}<br/>
```

技巧

```
<%
    pageContext.setAttribute("req",request);
%>
1.协议${req.scheme}<br/>
```



##### 输出域

```
<%
    pageContext.setAttribute("key1", "pageContext1");
    pageContext.setAttribute( "key2", "pageContext2" );
    request.setAttribute( "key2", "request" );
    session.setAttribute( "key2" , "session" );
    application.setAttribute( "key2", "application" );
%>
${pageScope.key2}
${requestScope.key2}
${sessionScope.key2}
${applicationScope.key2}
```

​	

##### param和paramValues  

```
输出参数username的值${param.username}<br>
输出参数password的值${param.password}<br>
输出参数hobby的值${paramValues.hobby[0]}<br>
```

http://localhost:8080/09_EL_JSTL/other_el_obj.jsp?username=ljxxx&password=123456&hobby=java&hobby=cpp

多个参数	例如hobby中，再用paramvalues

##### header 和 headerValues 

```
输出请求头信息${header}
```

输出请求头信息{sec-fetch-mode=navigate, sec-fetch-site=none, accept-language=zh-CN,zh;q=0.9, cookie=JSESSIONID=4355991039C24E3B00ECF2DA2FF6D73E; Idea-7a6f03f8=be249191-9103-4e4a-8c96-6fddcb94a6a1, sec-fetch-user=?1, accept=text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9, sec-ch-ua="Chromium";v="106", "Google Chrome";v="106", "Not;A=Brand";v="99", sec-ch-ua-mobile=?0, sec-ch-ua-platform="Windows", host=localhost:8080, upgrade-insecure-requests=1, connection=keep-alive, cache-control=max-age=0, accept-encoding=gzip, deflate, br, user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.0.0 Safari/537.36, sec-fetch-dest=document}

可用

```
<hr>
输出请求头信息${header}
<hr>
输出请求头user-agent信息${header["User-Agent"]}
```

输出单个请求头信息		注意header	[]之间没有点“.”	且User-Agent中有特殊符号，要用[]

```
<hr>
输出请求头user-agent信息${headerValues["User-Agent"][0]}
```

输出多个请求头信息

《hr》标签 创建一条水平线 用于分割！！！！

##### cookie 

```
获取cookie的名称${cookie.JSESSIONID.name}<br>
获取cookie的值${cookie.JSESSIONID.value}<br>
```



##### initparam

```
web.xml
<context-param>
    <param-name>username</param-name>
    <param-value>root</param-value>
</context-param>
<context-param>
    <param-name>url</param-name>
    <param-value>localhost://test</param-value>
</context-param>

jsp页面
输出<Context-param>uesername的值${initParam.username}<br>
输出<Context-param>url的值${initParam.url}<br>

```



## JSTL标签库

使用步骤

1、先导入 jstl 标签库的 jar 包。

2、第二步，使用 taglib 指令引入标签库。

```
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```



### set标签

```
保存前：${requestScope.abc}<br/>
<c:set scope="request" var="abc" value="abcvalue"/>
保存后：${requestScope.abc}<br/>
```

作用：set标签可用往域里保存数据

域对象.setAttribute( key, value);

scope属性设置保存到哪个域

page表示Pagecontext域（默认值)

request表示Request域

session表示Session域

application表示ServLetContext域

var属性设置key是多少

vaLue属性设置值



### if标签

```
<c:if test="${12==12}">
    <h1>12等于12！！</h1>
</c:if>
```

作用：用于做if判断

test属性表示判断的条件（用el表达式）！！！

正确输出<h1>12等于12！！</h1>	错误不输出



### choose when otherwise标签

和switch...case...default相似

不同在于不用break，不会像switch一样一个成立会执行往下所有的操作，而是就执行满足的操作

```
<c:choose>
    <c:when test="${requestScope.heigh> 190}">
    <h2>大于190</h2>
    </c:when>

    <c:when test="${requestScope.heigh> 180}">
    <h2>大于180</h2>
    </c:when>

    <c:when test="${requestScope.heigh> 170}">
    <h2>大于170</h2>
    </c:when>
    <c:otherwise>
        <h2>小于170</h2>
    </c:otherwise>
</c:choose>
```

choose标签开始选择判断

when标签表示每一种判断情况

otherwis标签表示剩下的情况

test属性表示当前这种判断情况的值

#### 注意

在choose when otherwise标签中

1、标签里不能使用htmL注释，要使用jsp注释

2，when标签的父标签一定要是choose标签



### forEach标签

作用：遍历输出使用



遍历1到10，输出

```
遍历1到10，输出
<c:forEach begin="1" end="10" var="i">
    ${i}<br>
</c:forEach>
```

begin属性设置开始的索引

end属性设置结束的索引

var属性表示循环的变量(也是当前正在遍历到的数据)

for (int i = 1; i< 10; i++)



遍历object数组

```
遍历object数组
<%
    request.setAttribute("arr",new String[]{"110","120","119"});
%>
<c:forEach items="${requestScope.arr}" var="item">
    ${  item    }
</c:forEach>
```

for (object item: arr)

items表示遍历的数据源(遍历的集合)

var表示当前遍历到的数据



遍历map数组

```
遍历map数组
<%
    HashMap<String, Object> map = new HashMap<>();
    map.put("key1","vaule1");
    map.put("key2","vaule2");
    map.put("key3","vaule3");
    request.setAttribute("map",map);
%>
<c:forEach items="${requestScope.map}" var="entry">
    ${entry.value}<br>
</c:forEach>
```

可用entry.value或entry.key	调用key和value值



遍历List集合---List中存放Student类，有属性:编号，用户名，密码，年龄，电话信息

```
遍历List集合
<%
    List<Student> list = new ArrayList<Student>();
    for (int i=1;i<=10;i++){
        list.add(new Student(i,"username+i","password+i",18+i,"phone+i"));
    }
    request.setAttribute("stus",list);
%>
<table>
    <tr>
        <th>编号</th>
        <th>姓名</th>
        <th>密码</th>
        <th>年龄</th>
        <th>电话</th>
        <th>操作</th>
    </tr>
<c:forEach begin="2" end="10" step="2" varStatus="status" items="${requestScope.stus}" var="stu">
    <tr>
        <td>${stu.id}</td>
        <td>${stu.username}</td>
        <td>${stu.password}</td>
        <td>${stu.age}</td>
        <td>${stu.phone}</td>
        <td>${status.Count}</td>

    </tr>
</c:forEach>
</table>
```

<c:forEach begin="2" end="10" step="2" items="${requestScope.stus}" var="stu">

step表示步长值	即i++;	若为2	则表示i+=2

varStatus继承了LoopTagStatus类

包含方法如下

public Object getCurrent();	 表示获取当前遍历到的数据
public int getIndex();				表示获取遍历的索引
public int getCount();				表示遍历了几个	第一个或第二个或第三个
public boolean isFirst() ;public boolean isLast(); 			表示当前遍历的数据是否是第一条，或最后一条

public Integer getBegin(); 	
public Integer getEnd();
public lnteger getStep() ;			获取begin.end.step属性值





## 文件上传和下载

### 上传

#### 介绍

1、要有一个form标签，method=post请求（get有长度限制 url中有限制）

2、form标签的encType属性值必须为multipart/form-data值3、在form标签中使用input type=file添加上传的文件

multipart/form-data表示提交的数据,以多段(每一个表单项一个数据段)的形式进行拼接，然后以二进制流的形式发送给服务器

4、编写服务器代码接收，处理上传的数据。

```
<form action="http://localhost:8080/09_EL_JSTL/uploadServlet" method="post" enctype="multipart/form-data">
    用户名 <input type="text"><br/>
    头像 <input type="file"><br/>
    <input type="submit" value="上传">
</form>
```



#### 文件上传时发送的HTTP协议内容

POST /09_EL_STL/uploadServlet HTTP/1.1

Host: 192.168.31.74:8080

Connection: keep-aliveContent-Length: 4647

Cache-Control: max-age=8

origin: http: / /192.168.31.74:8080

Upgrade-Insecure-Requests: 1

User-Agent: Mozilla/5.0 (Windows NT 6.1;MOM64) ApplellebKit/537.36(KHTML，like Gecko)

Content-Type: multipart/form-data; boundary=----MebKitFormBoundaryc12joVsAFxzitHaw

Accept: text/html,application/ xhtm1+xml,application/xml;q=0.9,image/webp,*/*;q=0.8

Referer: http:/ /192.168.31.74:8080/09_EL_STL/upload.jsp

Accept-Encoding: gzip, deflate

Accept-Language: zh-CN,zh;q=0.8,en-US;q=0.6,en;q=8.4

Cookie: JSESSIONID=588D36868550E65F52155c005F86CFDC



数据

------lebKitFormBoundaryc12joVsAFxzitHal

Content-Disposition: form-data; name="username"

ljxxx

------webKitFormBoundaryc12joVsAFxzitHal

Content-Disposition: form-data; name="photo"; filename="d.jpg"

content-Type: imageljpeg

------webKitFormBoundarvc12ioVsAFxzitHal--



Content-Type 表示提交的数据类型

multipart/form-data表示提交的数据,以多段(每一个表单项一个数据段)的形式进行拼接，然后以二进制流的形式发送给服务器
boundary表示每段数据的分隔符

—--WebKitFormBoundarycl2joVsAFxzitHaW是由浏览努每次都随机生成。它就是每段数据的分界符。

------webKitFormBoundarvc12ioVsAFxzitHal--多了两个减号表示数据结束的标识



#### commons-fileupload-1.2.1包

##### 常用类

ServletFileUpload类，用于解析上传的数据。

Fileltem类,表示每一个表单项。

boolean ServletFileUpload.isMultipartContent(HttpServletRequest request);（用ServletFileUpload类）

判断当前上传的数据格式是否是多段的格式。（多段数据才上传）



public List<Fileltem> parseRequest(HttpServletRequest request)（用ServletFileUpload类）

解析上传的数据

得到	Fileltem	对象



boolean FileItem.isFormField()

判断当前这个表单项，是否是普通的表单项。还是上传的文件类型。

true表示普通类型的表单项	false表示上传的文件



String FileItem.getFieldName()

获取表单项的name属性值



String FileItem.getString()

获取当前表单项的值。



String FileItem.getName( );

获取上传的文件名



void FileItem.write( file );

将上传的文件写到参数file所指向抽硬盘位置。



#### 使用！！！！

```
UploadServlet.java
//1.判断上传的文件是否多段数据（多段才上传）
if (ServletFileUpload.isMultipartContent(req)) {
    //创建FileItemFactory工具实现类
    FileItemFactory fileItemFactory=new DiskFileItemFactory();
    //创建用于解析上传数据的工具类servletupload类
    ServletFileUpload servletFileUpload=new ServletFileUpload(fileItemFactory);
    //解析上传的数据，得到没一个表达项FileItem
    try {
        List<FileItem> list=servletFileUpload.parseRequest(req);
        System.out.println("1");
        //循环判断，每一个表单项，是普通类型，还是上传的文件类型
        for(FileItem fileItem :list){
            System.out.println("2");
            if (fileItem.isFormField()){
                //普通表单项
                System.out.println("表单属性name的属性值：" + fileItem.getFieldName());
                //参数utf-8解决中文乱码
                System.out.println("表单属性value的属性值" + fileItem.getString("UTF-8"));
            }else{
                //上传文件
                System.out.println("表单属性name的属性值：" + fileItem.getFieldName());
                System.out.println("上传的文件名：" + fileItem.getName());
                fileItem.write(new File("C:\\Users\\ljxxx\\Desktop\\test\\" + fileItem.getName()));
            }

        }
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

fileItem.write()中要new一个flie对象，地址中test后要加\\\	否则就是给文件名前面加test



### 下载

#### 过程

服务器(Tomcat)

1、获取要下载的文件名

2、读取要下载的文件内容

3、把下载的文件内容回传给客户端

4、在回传前，通过响应头告诉客户端返回的数据类型

5、还要告诉客户端收到的;数据是用于下载使用(还是;使用响应头)



#### 使用！！！！！

```
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        1、获取要下载的文件名
            String downloadFileName="2.jpg";

//        2、读取要下载的文件内容(通过Servlet对象可以读取)
        ServletContext servletContext=getServletContext();
        //获取要下载的文件类型
        String mimeType = servletContext.getMimeType("/file/" + downloadFileName);
        System.out.println("下载的文件类型：" + mimeType);

//        3、在回传前，通过响应头告诉客户端返回的数据类型
        resp.setContentType(mimeType);

//        4、还要告诉客户端收到的数据是用于下载使用(还是使用响应头)
		//content-Disposition响应头，表示收到的数据怎么处理
        //attachment表示附件，表示下载使用
        //filename=表示指定下载的文件名
        resp.setHeader("Content-Disposition","attachment; filename=" + downloadFileName);

//        5、把下载的文件内容回传给客户端
        InputStream resourceAsStream = servletContext.getResourceAsStream("/file/" + downloadFileName);
        // 获取响应的输出流
        OutputStream outputStream = resp.getOutputStream();
        //        3、把下载的文件内容回传给客户端
        // 读取输入流中全部的数据，复制给输出流，输出给客户端
        IOUtils.copy(resourceAsStream, outputStream);
    }
```

servletContext.getMimeType(String file)	获取文件类型

resp.setContentType() 方法用于设置发送到客户端的响应的内容类型

resp.setHeader()		设置响应头

content-Disposition响应头，表示收到的数据怎么处理

attachment表示附件，表示下载使用

filename=表示指定下载的文件名



#### 保存文件名包含中文的情况

##### 谷歌IE浏览器

```
// url编码是把汉字转换成为%xx%xx的格式
resp.setHeader("Content-Disposition","attachment; filename=" + URLEncoder.encode("中国,jpg","UTF-8"));
```



##### 火狐浏览器

要用到Base64编码

```
Base64编码操作

String context="这是需要base64编码的内容";
//创建base64编码器
BASE64Encoder base64Encoder=new BASE64Encoder();
//执行base64编码操作
String encode = base64Encoder.encode(context.getBytes("UTF-8"));
System.out.println(encode);
//创建base64解码器
BASE64Decoder base64Decoder=new BASE64Decoder();
//执行base64解码操作
byte[] bytes = base64Decoder.decodeBuffer(encode);
String str = new String(bytes, "UTF-8");
System.out.println(str);
```



使用中的固定格式为

resp.setHeader(Content-Disposition: attachnent; filename==?charset?B? xxxxx?=);

=? 										   表示编码内容的开始

charset 								 表示字符集

B 										   表示 BASE64 编码

xxxx 									 表示文件名 BASE64 编码后的内容

?= 										 表示编码内容的结束

```
resp.setHeader("Content-Disposition", "attachment; filename==?UTF-8?B?" + new BASE64Encoder().encode("中国.jpg".getBytes("UTF-8")) + "?=");
```



最后加个判断

```
if (resp.getHeader("User-Agent").contains("Firefox")){
    //如果是火狐浏览器，使用base64编码
    resp.setHeader("Content-Disposition","attachment; filename==?UTF-8?" + new BASE64Encoder().encode("中国.jpg".getBytes("UTF-8")));

}else   
    //如果不是火狐，使用url编码
    resp.setHeader("Content-Disposition","attachment; filename=" + URLEncoder.encode("中国,jpg","UTF-8"));
```





## 书城第三阶段

#### 重复代码静态包含（重！！！！！！）

第一步，先把重复的代码静态包含

```
<div id="bottom">
      <span>
         尚硅谷书城.Copyright &copy;2015
      </span>
</div>
<base href="http://localhost:8080/book/">
<link type="text/css" rel="stylesheet" href="static/css/style.css" >

<script type="text/javascript" src="static/script/jquery-1.7.2.js"></script>
```

等等



因为静态包含地址中base href="http://localhost:8080/book/“	localhost为自身主机ip，不是要访问的服务器的ip，所以要对地址进行动态处理

```
<%
    String basePath=request.getScheme()
            +"://"
            + request.getServerName()
            +":"
            + request.getServerPort()
            + request.getContextPath()
            +"/";
%>

<base href="<%=basePath%>">
```

以下常用方法

request.getScheme() 返回当前链接使用的协议！！！！

request.getServerName()	获取服务器的ip！！！！！

request.getServerPort()		获取服务器的端口号

request.getContextPath()，获取当前工程名	例如/book，/08_jsp	等等

request.getServletPath(),	获取当前页面的具体位置 例如/book/index.jsp

request.getRequestURL()	获取完整地址http://localhost:8080/book/index.jsp



#### 注册或登录失败的错误回显(有趣！！！！！)



```
LoginServlet,java
if( (userService.login(new User(null,username,password,null) ) ) ==null ){
    //把错误信息和回显的表单信息保存到request域中
    req.setAttribute("msg","用户名或密码错误");
    System.out.println("登录失败");
    req.getRequestDispatcher("/pages/user/login.jsp").forward(req,resp);
    
login,jsp

<span class="errorMsg"><%=request.getAttribute("msg")==null?"请输入用户名和密码":request.getAttribute("msg")%></span>
```

把要回显的数据放在reques域中

在jsp errorMsg中进行判断是否为空，空就表示是第一次到见面，还未注册登录，所以显示原来的”请输入用户名和密码“



注册界面操作相同，要分两种，一种用户名不可用，一种验证码错误，

可以回显用户名，邮箱信息，对用户友好

<a href="/">	中的/是在浏览器中解析的！！！！！



#### 代码优化

##### 合并serlvet程序

通常一个模块只用一个servlet程序，所以要把loginservlet和registservlet合并

在login.jsp和regist.jsp的form标签下加入隐藏表单

```
<input type="hidden" name="action" value="login">
<input type="hidden" name="action" value="regist">
```

并新建UserServlet.java 通过getParameter方法获取参数进行判断

```
String action=req.getParameter("action");
if ("login".equals(action)){
    System.out.println("执行登录操作");
}else if ("regist".equals(action)){
    System.out.println("执行注册操作");
}
```

再或者新建两个方法login和regist，在dopost方法中调用！！！



##### 反射优化else if语句

当action值为login时 调用login方法，当action值为regist时 调用regist

所以可用反射，直接用action调用其值所代表的方法

```
public void login(){
    System.out.println("login方法被调用");
}
public void regist(){
    System.out.println("regist方法被调用");
}
public void updateuser(){
    System.out.println("updateuser方法被调用");
}

public static void main(String[] args) {
    String action="login";
    try {
        //获取action业务鉴别字符串，获取相应的业务方法反射现象
        Method method = UserServletTest.class.getDeclaredMethod(action);
        //调用目标业务方法
        method.invoke(new UserServletTest());

    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

java.lang.Class.getDeclaredMethod(name,parameterTypes)	方法用法(java.lang表示类名)

注：方法返回一个Method对象，它反映此Class对象所表示的类或接口的指定已声明方法。

name表示要获取的方法名称	**！！！！**

parameterTypes表示参数是**Class对象**识别方法的形参类型，可写可不写，有顺序，可写多个

即代表要写的参数的类，若要传入的参数为req就写 Httpservletrequest.class



java.lang.Class为获取class对象	

方法

1.Class.forName("类的全名称”）

2.实例化对象.getClass()

3.类名.class获取

getClass()		返回运行的java类	

Person p=new Person()

sout(p.getClass())	输出结果为	Person



所以	UserServletTest.class	也可以写成this.getclass()	this相当于就是当前类的实例化

但静态方法	mian	等等 不能用this指针！！

因为静态方法在一开始创建，没有任何对象，而this调用或引用的是当前对象！！！！



Method method = class.getDeclaredMethod(String name, Class<?>… parameterTypes)

method.invoke(Object obj,Object...args)	就是调用其代表的方法**！！！！！！**

obj是对象	一般情况就是当前类 new Test()

args表示调用	这个方法所要用的参数



##### 抽取baseServlet程序

因为很多servlet程序都有以下操作

1、获取action参数值
2、通过反射获取action对应的业务方法
3、通过反射调用业务方法

所以把所有servlet程序中的上诉操作抽取出来 整为baseservlet

```
baseServlet.java
public abstract class BaseServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        String action=req.getParameter("action");
        try {
            Method method = this.getClass().getDeclaredMethod(action,HttpServletRequest.class,HttpServletResponse.class);
            method.invoke(this,req,resp);
        } catch (Exception e) {
            e.printStackTrace();
        }
//        if ("login".equals(action)){
//            login(req,resp);
//        }else if ("regist".equals(action)){
//            regist(req,resp);
//
//        }
    }
}
```

在baseServlet中继承HttpServlet类，子类不需要继承HttpServlet，只需要继承父类baseServlet

在UserServlet中不需要再写dopost方法

因为继承父类时，会先在子类中找要用的方法，若没有，再进入到父类中找，若再没有就报错



##### 数据封装和抽取 BeanUtils,WebUtils类使用

外部类，要导包

不管是注册还是登录操作都要

```
String username = req.getParameter("username");
String password = req.getParameter("password");
String email = req.getParameter("email");
String code = req.getParameter("code");
```

获取参数，还要调用setxxx方法，对参数进行赋值

这时可用beanutils类	它可以一次性的把所有请求的参数注入到JavaBean中。

```
String username = req.getParameter("username");
String password = req.getParameter("password");
String email = req.getParameter("email");
String code = req.getParameter("code");
 User user=new User();
try {
    System.out.println("注入之前："+ user);
    BeanUtils.populate(user,req.getParameterMap());
    System.out.println("注入之后："+ user);
} catch (Exception e) {
    e.printStackTrace();
}
```

注入之后打印出来的user为

注入之后：User{id=null, username='wzg123', password='123456', email='wzg@qq.com'}

BeanUtils.populate(Object bean, Map properties)方法

这个方法会遍历map<key, value>中的key，如果bean中有这个属性，就把这个key对应的value值赋给bean的属性，调用的是set方法，即setUsername，setPassword等等。

若set方法对应不上，就跳过进行下一个赋值



然后可把方法封装成WebUtils类

```
WebUtils.java
public static <T> T  copyParametertoBean(T bean, Map value){

    try {
        System.out.println("注入之前："+ bean);
        BeanUtils.populate(bean,value);
        System.out.println("注入之后："+ bean);
    } catch (Exception e) {
        e.printStackTrace();
    }
	return bean;
}

UserServlet.java

User user=WebUtils.copyParametertoBean(new User(),req.getParameterMap());
```

public static <T> T copyParametertoBean(T bean, Map value)要设置为static静态，保证其在项目一开始能被创建，工具类都要设置为static静态

参数设置为Map，因为在实际开发中，有service等等层，是没有继承HttpServlet的，所以没有req参数，在servlet.java中req.getParameterMap()方法	获取map

使用泛型，使UserServlet.java中不需要强转为User类型对象



## 书城第四阶段

##### 使用EL表达式修改表单回显

<%=request.getAttribute("msg")==null?"请输入用户名和密码":request.getAttribute("msg")%>

被下面EL表达式替代

${empty requestScope.msg?"请输入用户名和密码":requestScope.msg}



<%=request.getAttribute("msg")==null?"":request.getAttribute("msg")%>

被下面EL表达式代替，因为EL表达式特点，若值为空，就会输出空串，而本身为空就要输出空串，所以不需要写，但jsp表达式值为空，会输出null**！！！！**

${requestScope.msg}



## 书城第五阶段

### MVC概念

MVc全称: Model模型、view视图、Controller控制器。

MVC最早出现在lavaEE三层中的 web层，它可以有效的指导web层的代码如何有效分离，单独工作。

view视图﹔只负责数据和界面的显示，不接受任何与显示数据无关的代码，便于程序员和美工的分工合作———JSP/HTML。

Controller控制器﹔只负责接收请求,调用业务层的代码处理请求,然后派发页面,是一个“调度者”的角色——Servlet。转到某个页面。或者是重定向到某个页面。

Model模型:将与业务逻辑相关的数据封装为具体的JavaBean类，其中不掺杂任何与数据处理相关的代码—―JavaBeanfdomain/entity。

Mvc是一种思想

Mvc的理念是将软件代码拆分成为组件，单独开发，组合使用（目的还是为了降低耦合度)。

![image-20221103125503532](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221103125503532.png)





### 图书模块

#### 1.编写图书模块的数据库表



#### 2.编写图书模块的JavaBean

String img_path="xxxx" img_path要有一个默认值,使网页一开始就有图片

```
private String imgPath="static/img/default.jpg";

public Book(Integer id, String name, String author, BigDecimal price, Integer sales, Integer stock, String imgPath) {
    this.id = id;
    this.name = name;
    this.author = author;
    this.price = price;
    this.sales = sales;
    this.stock = stock;
    if (imgPath!=null&&"".equals(imgPath)){
        this.imgPath = imgPath;
    }
```

if (imgPath!=null&&"".equals(imgPath)){
        this.imgPath = imgPath;
    }

若imgPath值为空，也就是我们没给它赋值，就用初始值imgPath="static/img/default.jpg"



#### 3.编写图书模块的Dao和测试 Dao

```
public class BookDaoImpl extends BaseDao implements BookDao {
    @Override
    public int aadBook(Book book) {
        String sql="insert into t_book(`name`,`price`,`author`,`sales`,`stock`,`img_path`) values(?,?,?,?,?,?)";
        return updata(sql,book.getName(),book.getPrice(),book.getAuthor(),book.getSales(),book.getStock(),book.getImg_path());
    }

    @Override
    public int deleteBook(Integer id) {
        String sql="delete from t_book where id = ?";
        return updata(sql,id);
    }

    @Override
    public int updateBook(Book book) {
        String sql="update t_book set `name`=?,`price`=?,`author`=?,`sales`=?,`stock`=?,`img_path`=? where id =?";
        return updata(sql,book.getName(),book.getPrice(),book.getAuthor(),book.getSales(),book.getStock(),book.getImg_path(),book.getId());
    }

    @Override
    public Book queryBookById(Integer id) {
        String sql="select `id`,`name`,`price`,`author`,`sales`,`stock`,`img_path` from t_book where id=?";
        return queryForOne(Book.class,sql,id);
    }

    @Override
    public List<Book> queryBooks() {
        String sql="select `id`,`name`,`price`,`author`,`sales`,`stock`,`img_path` from t_book";
        return queryList(Book.class,sql);
    }
}
```

#### 4.编写图书模块的Service和测试 Service

#### 5.编写图书模块的Web 层和页面联调测试

manager.jsp中一开始没有数据，所以要先调用query方法查询数据，所以不能直接访问manager.jsp要先访问BookServlet程序，通过BookServlet程序查询数据，保存到Request域中，请求转发给manager.jsp

在web中配置时，地址要写/manager/bookServlet（用于权限管理

前台是给用户使用的，一般不需要权限检查，都可以直接访问（/client/bookServlet）

后台是给管理员使用，要权限检查（/manager/bookServlet）

```
<servlet-mapping>
    <servlet-name>BookServlet</servlet-name>
    <url-pattern>/manager/bookServlet</url-pattern>
</servlet-mapping>
```

在manager.menu中改成

```
<a href="manager/bookServlet?action=list">图书管理</a>
```

action=list表示要请求的方法



#### 6.实现增加图书功能

```
book_edit.jsp
<div id="main">
   <form action="manager/bookServlet" method="get">
      <input type="hidden" name="action" value="add"/>
      <table>
         <tr>
            <td>名称</td>
            <td>价格</td>
            <td>作者</td>
            <td>销量</td>
            <td>库存</td>
            <td colspan="2">操作</td>
```

图书增加页面要加隐藏域，用于给action参数赋值为add，方便区分调用什么方法

```
protected void add(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //获取请求的参数，封装成为book对象
    Book book=new Book();

    WebUtils.copyParametertoBean(book,req.getParameterMap());
    //调用BookService.addBook()保存图书
    bookService.addBook(book);
    //跳到图书列表页面
    req.getRequestDispatcher("/manager/bookService?action=list").forward(req,resp);
}
```

要调用webUtils类中的copyParametertoBean方法

copyParametertoBean(bean,map)，

这个方法会遍历map<key, value>中的key，如果bean中有这个属性，就把这个key对应的value值赋给bean的属性，调用的是set方法，即setUsername，setPassword等等。

##### 问题

表单重复提交:

当用户提交完请求，浏览器会记录下最后一次请求的全部信息。当用户按下功能键F5，就会发起浏览器记录的最后一次请求。

而最后一次请求就为添加，所以每次刷新都会添加一条数据

##### 解决

所以要用重定向，而不是请求转发，因为重定向是让浏览器再去一个新的地址，这时最后一次请求就为请求那个新地址，而不是添加操作





#### 7.实现删除图书功能

```
$(function (){
			$("a.deleteClass").click(function () {
			//function中有个this对象，表示正在响应的dom对象
			/**
			 * confirm是确认提示框
			 * 参数是它提示的内容
			 * 点确认返回true，点取消返回false
			 * return false表示阻止元素的行为，等于不提交
			 * return true
			 */
			return confirm("确定要删除" + $(this).parent().parent().find("td:first").text() + "?");
			})
		})



<td><a  class="deleteClass" href="manager/bookServletaction=delete&id=${book.id}">删除</a></td>
```

为a标签绑上单击事件$(function (){	$("a.deleteClass").click(function () {}

做一个确认框，用于用户确认是否删除





    protected void delete(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    String id = req.getParameter("id");
    int i = Integer.parseInt(id);
    bookService.deleteBook(i);
    resp.sendRedirect(req.getContextPath()  + "/manager/bookServlet?action=list");
    }


int i = Integer.parseInt(id);把String类型转换为int类型





#### 8.实现修改图书功能

修改图书分为两个部分，第一部分为bookservlet中getBook方法，获取要修改的图书信息，请求转发到book_edit修改页面中，使其回显出来。第二部分为bookservlet中update方法，先获取修改之后的数据，update方法保存，重定向回book_manager.jsp，使其刷新出来修改后的数据

##### 问题

book_edit页面既要做添加操作，又要做修改操作

```
<input type="hidden" name="action" value="add"/>
```

所以value中的值不能固定

##### 解决一

可以发请求发起时，附带上当前要操作的值。并注入到隐藏域中。

book_eidts改为

```
<input type="hidden" name="action" value="${param.method}"/>
```

在book_manager中改成

```
<td><a href="manager/bookServlet?action=getBook&id=${book.id}&method=update">修改</a></td>

<td><a href="pages/manager/book_edit.jsp?method=add">添加图书</a></td>
```

多加一个参数method，用于区分调用方法



##### 解决二

修改时会带参数id=？？，添加时没有id

所以可以判断id值是否为空来决定传什么值

```
${empty param.id ? "add":"update"}
```

隐藏域变为

```
<form action="manager/bookServlet" method="get">
   <input type="hidden" name="action" value="${empty param.id ? "add":"update"}"/>
```



##### 解决三

判断 requestScore.book是否为空，空就表示添加操作，不为空就表示修改操作

因为修改操作要经过serlvet，带回数据

隐藏域变为

```
<input type="hidden" name="action" value="${empty requestScope.book ? "add":"update"}"/>
```



第一部分

```
protected void getBook(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    String id = req.getParameter("id");
    int i = Integer.parseInt(id);
    Book book = bookService.queryBookById(i);
    System.out.println(book);
    req.setAttribute("book",book);
    req.getRequestDispatcher("/pages/manager/book_edit.jsp").forward(req,resp);


}
```

第二部分

```
protected void update(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Book book=WebUtils.copyParametertoBean(new Book(),req.getParameterMap());
    bookService.updateBook(book);
    resp.sendRedirect(req.getContextPath()+ "/manager/bookServlet?action=list");

}

<form action="manager/bookServlet" method="post">
				<input type="hidden" name="action" value="${empty param.id ? "add":"update"}"/>
				<input type="hidden" name="id" value="${requestScope.book.id}"/>
```

因为修改需要参数，所以隐藏域加个id的参数



#### 9.分页数据

由夯贡的视函分析出分贡的对象模型Page类

pageNo						   当前页码

pageTotal					   总页码

pagelotalCount			 总记录数

pageSize					    每页显示数量

items							  当前页数据

pageNo当前页码是由客户端进行传递pageSize每页显示数量由两种因素决定。
一:客户端进行传递

二:由页面布局决定

pageTotalCount				总记录数可以由sql语句求得。
										sql语句是:select count(*) from表名。

pageTotal						 总页码可以由总记录数/每页数量得到。

​							注:总记录数%每页数量>0,则总页码+1（如9%4余1，说明有一条记录剩下了）

items							 当前页数据，也是可以由sql语句求得。

​					sql语句是: select * from表名limit begin ,pageSize;begin

可以由公式求得: (pageNo-1)	x	pageSize;

begin为索引值，从0开始 0代表1

limit子句用于限制查询结果返回的数量。

【select * from tablename limit i,j;】

*:i:查询结果的索引值（默认从0开始）（从第几个开始查询）
*:j:查询结果返回数量;

![image-20221107202620741](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221107202620741.png)



##### page基础设置

```
page.java
public class Page<T> {

    public static final Integer PAGE_SIZE =4;

    //当前页码
    private Integer pageNo;
    //总页码
    private Integer pageTotal;
    //当前页显示数量
    private Integer pageSize = PAGE_SIZE;
    //总记录数
    private Integer pageTotalCount;
    //当前页数据
    private List<T> items;
```

在webutils.java中

```
public static int parseInt(String strInt,int defaultValue) {
    try {
        return Integer.parseInt(strInt);
    } catch (Exception e) {
    //            e.printStackTrace();
    }
    return defaultValue;
}
```

用于将字符串转换成为int类型的数据，若为空，则返回默认值defaultValue，

可用于设置一开始的pageNo当前页码为1

bookServlet

```
protected void page(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //获取请求的参数pageNo和pageSize
    int pageNo = WebUtils.parseInt(req.getParameter("pageNo"), 1);
    int pageSize = WebUtils.parseInt(req.getParameter("pageSize"), Page.PAGE_SIZE);

    //调用BookService.page(pageNo,pageSize):page对象
    Page<Book> page = bookService.page(pageNo,pageSize);


    //保存page对象到request域中
    req.setAttribute("page",page);

    //请求转发到pages/manager/book_manager.jsp页面
    req.getRequestDispatcher("/pages/manager/book_manager.jsp").forward(req,resp);
}
```

bookServiceImpl

```
@Override
public Page<Book> page(int pageNo, int pageSize) {
    Page<Book> page=new Page<Book>();
    //设置当前页码
    page.setPageNo(pageNo);
    //设置每页显示的数量
    page.setPageSize(pageSize);
    //求总记录数
    Integer pageTotalCount=bookDao.queryForPageTotalCount();
    //设置总记录数
    page.setPageTotalCount(pageTotalCount);
    //求总页码
    Integer pageTotal =pageTotalCount / pageSize ;
    if (pageTotalCount%pageSize>0){
        pageTotal=pageTotalCount+1;
    }
    //设置总页码
    page.setPageTotal(pageTotal);
    //求当前页数据的开始索引
    int begin=(page.getPageNo()-1)  *   pageSize;
    //求当前页数据
    List<Book> items=bookDao.queryForPageItems(begin,pageSize);
    //设置当前页数据
    page.setItems(items);
    return page;
}
```

bookDaoImpl

```
@Override
public Integer queryForPageTotalCount() {
    String sql="select count(*) from t_book";
    Number count = (Number) queryForSingleValue(sql);
    //转换为Integer类型
    return count.intValue();
}

@Override
public List<Book> queryForPageItems(int begin, int pageSize) {
    String sql="select  `id`,`name`,`price`,`author`,`sales`,`stock`,`img_path` from t_book limit ?,?";
    return queryList(Book.class,sql,begin,pageSize);
}
```

select count(*) from t_book查询的结果为Number类型，所以要转换为Number类

Number count = (Number) queryForSingleValue(sql);

Number 是一个抽象类，也是一个超类（即父类）。Number 类属于 java.lang 包，所有的包装类（如 Double、Float、Byte、Short、Integer 以及 Long）都是抽象类 Number 的子类。



##### 上下页和跳转页

在book_manager.jsp中

```
<div id="page_nav">
      <c:if test="${requestScope.page.pageNo >  1}">
         <a href="manager/bookServlet?action=page&pageNo=1">首页</a>
         <a href="manager/bookServlet?action=page&pageNo=${requestScope.page.pageNo-1}">上一页</a>
         <a href="manager/bookServlet?action=page&pageNo=${requestScope.page.pageNo-1}">${requestScope.page.pageNo-1}</a>
      </c:if>

      【${requestScope.page.pageNo}】

      <c:if test="${requestScope.page.pageNo <  requestScope.page.pageTotal}">
         <a href="manager/bookServlet?action=page&pageNo=${requestScope.page.pageNo+1}">${requestScope.page.pageNo+1}</a>
         <a href="manager/bookServlet?action=page&pageNo=${requestScope.page.pageNo+1}">下一页</a>
         <a href="manager/bookServlet?action=page&pageNo=${requestScope.page.pageTotal}">末页</a>
      </c:if>

      共${requestScope.page.pageTotal}页，${requestScope.page.pageTotalCount}条记录
      到第<input value="${requestScope.page.pageNo}" name="pn" id="pn_input"/>页
      <input id="searchPageNo" type="button" value="确定">
   </div>

   <script type="text/javascript">
      $(function () {
         //跳到指定页码
         $("#searchPageNo").click(function () {

         var pageNo = $("#pn_input").val();
         var pageTotal=${requestScope.page.pageTotal};
         if (pageNo => 1&&pageNo<=pageTotal){

         }else {
            alert("请输入正确的页码");
            return false;
         }
         // javascript语言中提供了一个location地址栏对象
         // 它有子个属性叫href.它可以获取浏览器地址栏中的地址
         // href属性可读,可写(写就是跳转到哪个页面)

            location.href = "${pageScope.basePath}manager/bookServlet?action=page&pageNo=" + pageNo;
         })
      })

   </script>
</div>
```

<c:if test="${requestScope.page.pageNo >  1}">当处于第一页时，不显示点击首页和上一页的按钮

$("#searchPageNo").click(function () {	为跳转页码的按钮绑定单击事件，做个判断，若超过总页码数，则弹框alert("请输入正确的页码"); return false阻止其跳转行为



javascript语言中提供了一个location地址栏对象

它有子个属性叫href.它可以获取浏览器地址栏中的地址

href属性可读,可写(写就是跳转到哪个页面)

用location.href设置跳转页面	

${pageScope.basePath}为在head.jsp中设置

```
<%
    String basePath=request.getScheme()
            +"://"
            + request.getServerName()
            +":"
            + request.getServerPort()
            + request.getContextPath()
            +"/";
    pageContext.setAttribute("basePath",basePath);
%>
<%=basePath%>
```

把http://localhost:8080/book保存到pagecontext域中



##### 数据边界有效检查

在page.java中

```
public void setPageNo(Integer pageNo) {
    //数据边界有效检查
    if(pageNo<1){
        pageNo=1;
    }
    if (pageNo>pageTotal){
        pageNo=pageTotal;
    }

    this.pageNo = pageNo;
}
```

设置pageNo值时，先进行判断是否小于1或是否超出总页码

并在bookserviceimpl中设置

```
//设置总页码
page.setPageTotal(pageTotal);
//设置当前页码
page.setPageNo(pageNo);
```

要先设置总页码，再设置当前页码，因为设置当前页码需要有总页码！！！

因为有人可以直接在网页url中设置pageNo的值，所以需要防止这种行为



##### 显示页码1，2，3，4，5

要显示五个页码

###### 若总页码数小于5，页码范围为1-总页码



###### 若总页码大于5，为 10

三种情况

第一种

为前三个1，2，3	页码范围为1-5

【1】，2，3，4，5

1，【2】，3，4，5

1，2，【3】，4，5

第二种

为最后三个 8，9，10	页码范围为	总页码-4	到	总页码

6，7，【8】，9，10

6，7，8，【9】，10

6，7，8，9，【10】

第三种

为中间	4，5，6，7	页码范围为	当前页码-2到当前页码+2

2，3，【4】，5，6

3，4，【5】，6，7

4，5，【6】，7，8

5，6，【7】，8，9

```
<c:if test="${requestScope.page.pageNo  >  1}">
   <a href="manager/bookServlet?action=page&pageNo=1">首页</a>
   <a href="manager/bookServlet?action=page&pageNo=${requestScope.page.pageNo-1}">上一页</a>
</c:if>

<%--页码输出开始--%>

<c:choose>
   <%--若总页码数小于5，页码范围为1-总页码--%>
   <c:when test="${requestScope.page.pageTotal <=5}">
      <c:set var="begin" value="1"/>
      <c:set var="end" value="${requestScope.page.pageTotal}"/>

   </c:when>
   <%--若总页码大于5--%>
   <c:when test="${requestScope.page.pageTotal > 5}">
      <c:choose>
         <%--页码为前三个1，2，3--%>
         <c:when test="${requestScope.page.pageNo<=3}">
            <c:set var="begin" value="1"/>
            <c:set var="end" value="5"/>

         </c:when>
         <%--页码为后三个8，9，10--%>
         <c:when test="${requestScope.page.pageNo> requestScope.page.pageTotal-3}">
            <c:set var="begin" value="${requestScope.page.pageTotal-4}"/>
            <c:set var="end" value="${requestScope.page.pageTotal}"/>

         </c:when>
         <c:otherwise>
            <c:set var="begin" value="${requestScope.page.pageNo-2}"/>
            <c:set var="end" value="${requestScope.page.pageNo+2}"/>

         </c:otherwise>
      </c:choose>
   </c:when>
</c:choose>

<c:forEach begin="${begin}" end="${end}" var="i">
   <c:if test="${i==requestScope.page.pageNo}">
      【${i}】
   </c:if>
   <c:if test="${i!=requestScope.page.pageNo}">
      <a href="manager/bookServlet?action=page&pageNo=${i}">${i}</a>
   </c:if>
</c:forEach>

<%--页码输出结束--%>


<c:if test="${requestScope.page.pageNo <  requestScope.page.pageTotal}">
   <a href="manager/bookServlet?action=page&pageNo=${requestScope.page.pageNo+1}">下一页</a>
   <a href="manager/bookServlet?action=page&pageNo=${requestScope.page.pageTotal}">末页</a>
</c:if>
```

记录下每次的begin和end值，在最后再用forEach，这样forEach只需要用一次





且因为使用分页，所以要在所有action=list中改成action=page，不管是重定向还是请求转发



##### 处理修改删除添加之后的影响

当修改删除添加之后，页面会调回第一页，所以需要修改

修改

在book_manager.jsp中	在添加图书按钮时，把当前页码传过去

```
<td><a href="pages/manager/book_edit.jsp?method=add&pageNo=${requestScope.page.pageNo}">添加图书</a></td>
```

在book_edit.jsp	再加个隐藏域pageNo，让其传到BookServlet中

```
<input type="hidden" name="pageNo" value="${param.pageNo}">
<input type="hidden" name="action" value="${empty param.id ? "add":"update"}"/>
```

在BookServlet.add中加入参数i，记录pageNo，让其加一，使添加操作会不会再往前一页，都能跳回到最后一页，若添加操作没往前一页，会超过边界导致回到最后一页，而往前了一页刚好

```
int i =WebUtils.parseInt(req.getParameter("pageNo"),0);
i=i+1;

resp.sendRedirect(req.getContextPath()  + "/manager/bookServlet?action=page&pageNo="+i);
```



删除，修改同理





### 前台分页

我们通常直接访问index.jsp，不会像访问图书管理一样，先访问servlet程序，这样网页url太长了，所以我们在pages下建立一个client文件，再建立一个index.jsp文件（一模一样），然后在一开始的index.jsp中跳转到我们要用的servlet，在请求转发到client下的index.jsp

ClientBookServlet		web中的配置为 /client/bookservlet

```
public class ClientBookServlet extends BaseServlet {


    BookService bookService=new BookServiceImpl();

    protected void page(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("经过了ClientBookServlet方法");
        //获取请求的参数pageNo和pageSize
        int pageNo = WebUtils.parseInt(req.getParameter("pageNo"), 1);
        int pageSize = WebUtils.parseInt(req.getParameter("pageSize"), Page.PAGE_SIZE);

        //调用BookService.page(pageNo,pageSize):page对象
        Page<Book> page = bookService.page(pageNo,pageSize);

        //保存page对象到request域中
        req.setAttribute("page",page);

        //请求转发到pages/manager/book_manager.jsp页面
        req.getRequestDispatcher("/pages/client/index.jsp").forward(req,resp);
    }
```

原本的index.jsp中为

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<jsp:forward page="/client/bookServlet?action=page"></jsp:forward>
```

<jsp: forward page="">为请求转发

index.jsp为

```
       <c:forEach items="${requestScope.page.items}" var="books">
      <div class="b_list">
         <div class="img_div">
            <img class="book_img" alt="" src="${books.img_path}" />
         </div>
         <div class="book_info">
            <div class="book_name">
               <span class="sp1">书名:</span>
               <span class="sp2">${books.name}</span>
            </div>
            <div class="book_author">
               <span class="sp1">作者:</span>
               <span class="sp2">${books.author}</span>
            </div>
            <div class="book_price">
               <span class="sp1">价格:</span>
               <span class="sp2">${books.price}</span>
            </div>
            <div class="book_sales">
               <span class="sp1">销量:</span>
               <span class="sp2">${books.sales}</span>
            </div>
            <div class="book_amount">
               <span class="sp1">库存:</span>
               <span class="sp2">${books.stock}</span>
            </div>
            <div class="book_add">
               <button>加入购物车</button>
            </div>
         </div>
      </div>
      </c:forEach>
   </div>

   <%--分页条的开始--%>
   <div id="page_nav">
      <c:if test="${requestScope.page.pageNo >  1}">
         <a href="client/bookServlet?action=page&pageNo=1">首页</a>
         <a href="client/bookServlet?action=page&pageNo=${requestScope.page.pageNo-1}">上一页</a>
      </c:if>

      <%--页码输出开始--%>

      <c:choose>
         <%--若总页码数小于5，页码范围为1-总页码--%>
         <c:when test="${requestScope.page.pageTotal <=5}">
            <c:set var="begin" value="1"/>
            <c:set var="end" value="${requestScope.page.pageTotal}"/>

         </c:when>
         <%--若总页码大于5--%>
         <c:when test="${requestScope.page.pageTotal > 5}">
            <c:choose>
               <%--页码为前三个1，2，3--%>
               <c:when test="${requestScope.page.pageNo<=3}">
                  <c:set var="begin" value="1"/>
                  <c:set var="end" value="5"/>

               </c:when>
               <%--页码为后三个8，9，10--%>
               <c:when test="${requestScope.page.pageNo> requestScope.page.pageTotal-3}">
                  <c:set var="begin" value="${requestScope.page.pageTotal-4}"/>
                  <c:set var="end" value="${requestScope.page.pageTotal}"/>

               </c:when>
               <c:otherwise>
                  <c:set var="begin" value="${requestScope.page.pageNo-2}"/>
                  <c:set var="end" value="${requestScope.page.pageNo+2}"/>

               </c:otherwise>
            </c:choose>
         </c:when>
      </c:choose>

      <c:forEach begin="${begin}" end="${end}" var="i">
         <c:if test="${i==requestScope.page.pageNo}">
            【${i}】
         </c:if>
         <c:if test="${i!=requestScope.page.pageNo}">
            <a href="client/bookServlet?action=page&pageNo=${i}">${i}</a>
         </c:if>
      </c:forEach>

      <%--页码输出结束--%>


      <c:if test="${requestScope.page.pageNo <  requestScope.page.pageTotal}">
         <a href="client/bookServlet?action=page&pageNo=${requestScope.page.pageNo+1}">下一页</a>
         <a href="client/bookServlet?action=page&pageNo=${requestScope.page.pageTotal}">末页</a>
      </c:if>

      共${requestScope.page.pageTotal}页，${requestScope.page.pageTotalCount}条记录
      到第<input value="${requestScope.page.pageNo}" name="pn" id="pn_input"/>页
      <input id="searchPageNo" type="button" value="确定">
   </div>
   <%--分页条的结束--%>

</div>
```



### 分页条抽取

因为分页要给前台和后台用，所以可以把前台后台jsp地址中的的 manager/bookServlet?action=page

或者client/bookServlet?action=page改成${requestScope.page.url}

```
<c:if test="${requestScope.page.pageNo  >  1}">
   <a href="${requestScope.page.url}&pageNo=1">首页</a>
   <a href="${requestScope.page.url}&pageNo=${requestScope.page.pageNo-1}">上一页</a>
```

然后在page.java中加入url参数和get set方法

```
//分页条的请求地址
private String url;
```

在bookServlet和ClientBookServlet的page方法中分别设置url的值

```
//设置分页条的请求地址
page.setUrl("manager/bookServlet?action=page");
。。。。·
```

再把分页条抽取到一个新的jsp中，用静态包含去调用

```
<%--静态包含分页条--%>
<%@ include file="/pages/common/page_nav.jsp"%>
```



### 通过价格区间查询商品

![image-20221108184139134](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221108184139134.png)



省略bookservice和bookdao

clientBookServlet

```
protected void pageByPrice(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //获取请求的参数pageNo和pageSize

    int pageNo = WebUtils.parseInt(req.getParameter("pageNo"), 1);
    int pageSize = WebUtils.parseInt(req.getParameter("pageSize"), Page.PAGE_SIZE);
    int min = WebUtils.parseInt(req.getParameter("min"),0);
    int max = WebUtils.parseInt(req.getParameter("max"),Integer.MAX_VALUE);
    //调用BookService.page(pageNo,pageSize):page对象
    Page<Book> page = bookService.pageByPrice(pageNo,pageSize,min,max);

    StringBuilder stringBuilder = new StringBuilder("client/bookServlet?action=pageByPrice");
    //如果有最小参数的参数，追加到分页条的地址参数中
    if (req.getParameter("min")!=null){
        stringBuilder.append("&min=").append(req.getParameter("min"));
    }
    //如果有最大参数的参数，追加到分页条的地址参数中
    if (req.getParameter("max")!=null){
        stringBuilder.append("&max=").append(req.getParameter("max"));
    }
    //设置分页条的请求地址
    page.setUrl(stringBuilder.toString());
    //保存page对象到request域中
    req.setAttribute("page",page);
    System.out.println(page);

    //请求转发到pages/manager/book_manager.jsp页面
    req.getRequestDispatcher("/pages/client/index.jsp").forward(req,resp);
}
```

**StringBuilder类**：字符串缓冲区，底层是一个没有被final修饰的数组，可以改变长度，可以提高字符串的操作效率。

StringBuilder(String str)构造一个字符串生成器，并初始化为指定的字符串内容

append(String str1)方法把str1添加到str后

if (req.getParameter("min")!=null)要进行判断min是否有值，且要带回到url中，因为在分页时，若点了第二页，没有min和max的值，就会变成普通的分页，而不是按照价格分页

page.setUrl(stringBuilder.toString());把stringBuilder的值输出出来





## Cookie

1、Cookie翻译过来是饼干的意思。

2、Cookie是服务器通知客户端保存键值对的一种技术。（要传入name和value）

3、客户端有了cookie后，每次请求都发送给服务器。

4、每个Cookie的大小不能超过4kb



### Cookie创建

```
<li><a href="cookieServlet?action=creatCookie" target="target">Cookie的创建</a></li>

//1.创建cookie对象
Cookie cookie = new Cookie("key1","value1");
//2.通知客户端保存cookie
resp.addCookie(cookie);
resp.getWriter().write("Cookie创建成功");
```

可同时传递多个cookie

resp.addCookie是核心，只有这个才能通知

服务器通过响应头Set-Cookie 发送到浏览器，通知客户端保存Cookie

Set-Cookie:key1=value1

客户端收到响应后，发现有set-cookie响应头就去看一下，有没有这个Cookie。没有就创建,有就修改。



### Cookie获取

```
<li><a href="cookieServlet?action=getCookie" target="target">Cookie的获取</a></li>

protected void getCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

    Cookie[] cookies = req.getCookies();
    for (Cookie cookie : cookies) {
        //getName方法返回cookie的key(名)
        //getValue方法返回cookie的values值
        resp.getWriter().write("Cookie[" + cookie.getName()+ "=" + cookie.getValue() + "] <br/>" );
    }


}
```

req.getCookies()返回Cookie[]数组，通过这一行来获取



查找想要的cookies

```
Cookie iwantcookie = null;
for (Cookie cookie : cookies) {
    if (cookie.getName().equals("key2")){
        iwantcookie=cookie;
        break;
    }
}
//如果不等于null，就代表赋过值了，找到cookies
if (iwantcookie != null){
    resp.getWriter().write("找到了需要的cookies");
}
```

把其封装成工具类

CookiesUtils

```
/**
 * 查找指定名称的cookies对象
 * @param name
 * @param cookies
 * @return
 */

public static Cookie findCookies(String name,Cookie[] cookies){
    //先做判断name和cookies是否为空
    if (name == null ||cookies == null|| cookies.length==0){
        //若为空，表示没有传值进来，所以返回null
        return null;
    }
    for (Cookie cookie : cookies) {
        if (name.equals(cookie.getName())){
            return cookie;
        }
    }
    return null;
}
```

cookiesServlet就变成

```
Cookie iwantcookie = CookiesUtils.findCookies("key2",cookies);
```





### Cookie修改

#### 方案一

1、先创建一个要修改的同名的Cookie对象

2、在构造器，同时赋于新的Cookie 值。

3、调用response.addCookie( Cookie );

```
//        1、先创建一个要修改的同名的Cookie对象
//        2、在构造器，同时赋于新的Cookie 值。
        Cookie cookie=new Cookie("key1","newValue1");

//        3、调用response.addCookie( Cookie );
        resp.addCookie(cookie);
        resp.getWriter().write("key1的cookies已经修改");
```



#### 方案二

1、先查找到需要修改的Cookie对象

2、调用setValue()方法赋于新的Cookie值。

3、调用response.addCookie()通知客户端保存修改

```
//        1、先查找到需要修改的Cookie对象
            Cookie cookie = CookiesUtils.findCookies("key2",req.getCookies());
//        2、调用setValue()方法赋于新的Cookie值。
        if (cookie != null){
            cookie.setValue("newValue2");
        }
//
//        3、调用response.addCookie()通知客户端保存修改
        resp.addCookie(cookie);

        resp.getWriter().write("key2的cookie已经修改");
```

修改创建时，都不支持中文，逗号，空格等等，要用BASE64编码



### Cookies生命控制

cookie 的生命控制指的是如何管理cookie 什么时候被销毁（删除)

setMaxAge()

正数，表示在指定的秒数后过期（浏览器关了也还在）

```
protected void Life3600(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie cookie = new Cookie("Life3600","Life3600");
    cookie.setMaxAge(60*60);//设置Cookie一小时之后删除
    resp.addCookie(cookie);
    resp.getWriter().write("已创建存活一小时的cookie");

}
```

负数,表示浏览器一关，cookie就会被删除（默认值是-1）

```
protected void defaultLife(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie cookie = new Cookie("defaultLife","defaultLife");
    cookie.setMaxAge(-1);//设置存活时间
    resp.addCookie(cookie);

}
```

零，表示马上删除cookie

```
protected void deleteNow(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //先找到要删除的cookie
    Cookie cookie = CookieUtils.findCookies("key3",req.getCookies());
    //判断cookie是否为空
    if (cookie != null){
        cookie.setMaxAge(0);//马上删除cookie，不需要等浏览器关闭
    }
    resp.addCookie(cookie);
    resp.getWriter().write("key3的cookie被删除");
}
```



### Cookie的有效路径设置

```
protected void testPath(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie cookie =new Cookie("path1","path1");
    //req.getContextPath()  得到  工程路径
    cookie.setPath(req.getContextPath() + "/abc");  // 得到 /工程路径/abc
    resp.addCookie(cookie);
    resp.getWriter().write("创建了一个带有path路径的cookie");

}
```

这时要访问 	/工程路径/abc才能找到cookie	默认的cookie的路径为 /工程路径，包含了/工程路径/abc。	所以其他的cookie都能在/工程路径/abc中看到





### 练习：Cookie免用户名登录

login页面

```
<form action="http://localhost:8080/13_cookie_session/loginServlet" method="get">
    用户名：<input type="text" name="username" value="${cookie.username.value}"><br/>
    密码：<input type="password" name="password" value=""><br/>
    <input type="submit" value="登录">
</form>
```

value="${cookie.username.value}"

cookie为EL表达式的隐含对象，第一次没有cookie，所以用户名为空，登录成功一次之后，就有cookie，保存了username，在用户名上显示



loginservlet

```
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    String username=req.getParameter("username");
    String password=req.getParameter("password");
    if ("ljxxx".equals(username) && "123456".equals(password)){
        //登录成功
        Cookie cookie = new Cookie("username",username);
        cookie.setMaxAge(60 * 60 * 24 * 7);     //Cookie存活一星期
        resp.addCookie(cookie);
        System.out.println("登录成功");

    }else System.out.println("登录失败");
}
```



## Session会话

什么是 Session会话?

1、session就一个接口（HttpSession） 。

2、Session 就是会话。它是用来维护一个客户端和服务器之间关联的一种技术。

3、每个客户端都有自己的一个 Session会话。

4、Session会话中，我们经常用来保存用户登录之后的信息。**！！！**

Cookie保存在客户端，session保存在服务器端



### 创建Session和获取

request.getsession()

第一次调用是:创建session会话

之后调用都是:获取前面创建好的session会话对象。

isNew();判断到底是不是刚创建出来的

true表示刚创建		false表示获取之前创建

每个会话都有一个身份证号。也就是ID值。而且这个ID是唯一的。

getld()得到session的会话id值。

```
protected void creatorGetSession(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //创建和获取session会话对象
    HttpSession session = req.getSession();
    //判断当前session会话是否为新创建出来的
    boolean isNew = session.isNew();
    String id = session.getId();
    resp.getWriter().write("得到的sesion的id值为:"+ id +"<br/>");
    resp.getWriter().write("得到的sesion是否为新创建:"+ isNew +"<br/>");

}
```



### Session域中数据的存取

```

protected void setAttribute(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    req.getSession().setAttribute("key1","key1");

}

protected void getAttribute(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Object key1 = req.getSession().getAttribute("key1");
    resp.getWriter().write("从session读取key1的数据为:"+key1);

}
```



### Session生命周期控制

public void setMaxlnactiveInterval(int interval)设置session的超时时间，超过指定的时长，session就会被销毁。

值为正数的时候，设定session的超时时长。

负数表示永不超时(极少使用)

public void getMaxinactivelnterval()获取session的超时时间

public void invalidate()让当前session会话马上超时无效。

session的时间单位为**（秒）**

#### 默认时长 1800秒

```
protected void defaultLife(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    int maxInactiveInterval = req.getSession().getMaxInactiveInterval();
    resp.getWriter().write("session的默认超时时长为"+ maxInactiveInterval +"秒");


}
```

tomcat服务器的配置文件web.xml中有对session时间配置30分钟

<session-config>

<session-timeout>30</session-timeout>

</session-config>

若要修改，就在自己工程的web.xml文件下配置上方的代码



#### 设置三秒后超时

```
protected void life3(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //获取session对象
    HttpSession session = req.getSession();
    //设置当前session三秒之后超时
    session.setMaxInactiveInterval(3);

    resp.getWriter().write("当前session已经设置三秒后超时");

}
```

超时为	三秒内，未调用session。若调用了session，则timeout的时间会重置为三秒。

超时指客户端两次请求的最大间隔时长



#### Session马上超时

```
protected void deleteNow(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //获取session对象
    HttpSession session = req.getSession();
    //让session对象马上超时
    session.invalidate();

    resp.getWriter().write("Session对象已经被设置为超时");
}
```



### 浏览器和session的关系

一开始客户端中没有cookie，向服务器发起请求，服务器会创建session。

服务器每次创建Session会话的时候,都会创建一个Cookie对象。这个Cookie对象的key永远是:JSESSIONID		值是新创建出来的Session的id值。

通过响应头（set-Cookie）把新创建出来的session的id值返回给客户端

浏览器收到数据后，马上创建出一个cookie。

有了cookie之后，浏览器每次请求都会把session的id以cookie的形式发送给服务器（getSession()后面都是获取不是创建，能获取到就是通过id）

若把cookie删除之后，浏览器再次发起请求，这时服务器没有收到id，找不到之前的session，只能新创建一个session会话



### session是基于cookie技术实现的





## 书城第六阶段



### 显示登录的用户信息

![image-20221109142130794](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221109142130794.png)



韩总就是用户信息

在userSerlvet中

```
User loginUser = userService.login(new User(null,username,password,null) );

    req.getSession().setAttribute("user",loginUser);
```

把user信息保存到session域中（request域只能使用一次请求，不满足一直使用）



在首页时，未登录时显示 登录/注册，登录之后就不显示 登录/注册

在index.jsp

```
<div>
   <%--如果用户未登录，则显示  【登录/注册】的菜单--%>
   <c:if test="${empty sessionScope.user}">
      <a href="pages/user/login.jsp">登录</a>
      <a href="pages/user/regist.jsp">注册</a> &nbsp;&nbsp;
   </c:if>
      <%--如果用户未登录，则显示  用户信息--%>
      <c:if test="${not empty sessionScope.user}">
         <span>欢迎<span class="um_span">${sessionScope.user.username}</span>光临尚硅谷书城</span>
         <a href="pages/order/order.jsp">我的订单</a>
		<a href="index.jsp">注销</a>&nbsp;
      </c:if>

   <a href="pages/cart/cart.jsp">购物车</a>
   <a href="pages/manager/manager.jsp">后台管理</a>
</div>
```

判断	sessionScope.user	是否为空



### 注销用户



1、销毁session中用户登录的信息(或者销毁session)

2、重定向到首页（或登录页面)



### 表单重复提交

表单重复提交有三种常见的情况:

#### 第一种情况

提交完表单。服务器使用请求转来进行页面跳转。这个时候，用户按下功能键F5，就会发起最后一次的请求。造成表单重复提交问题。

##### 解决方法

使用重定向来进行跳转	resp.sendRedirect()

#### 第二种情况

二:用户正常提交服务器，但是由于网络延迟等原因，迟迟未收到服务器的响应，这个时候，用户以为提交失败，就会着急，然后多点了几次提交操作，也会造成表单重复提交。

用Thread.sleep(5000)	使当前线程休眠	模拟延迟时间	单位为毫秒 5秒钟

##### 解决方法：

使用验证码



#### 第三种情况

用户正常提交服务器。服务器也没有延迟，但是提交完成后，用户回退浏览器。重新提交。也会造成表单重复

##### 解决方法：

使用验证码



### 验证码

#### 验证码底层原理

1、当用户第一次访问表单的时候,就要给表单生成一个随机的验证码字符串

2、要把验证码保存到Session域中

3、要把验证码生成为验证码图片显示在表单中



客户端把表单信息发送给服务器registServlet



RegistServlet程序操作

1、获取Session中验证码保存到其他参数中，并删除Session中的验证码

2、获取表单中的表单项信息

3、比较Session中的验证码保存到的参数和表单项中的验证码是否相等

这时若是第一次，则相同，允许操作

若重复提交第二次，这时session的验证码信息已经被删除，不相等，阻止操作



#### 谷歌验证码

新建一个项目temp	用于实验

第一步，导入谷歌验证码的jar包

第二步，在web.xml中配置servlet（为jar包中自带的servlet程序KaptchaServlet）

```
<servlet>
    <servlet-name>KaptchaServlet</servlet-name>
    <servlet-class>com.google.code.kaptcha.servlet.KaptchaServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>KaptchaServlet</servlet-name>
    <url-pattern>/kaptcha.jpg</url-pattern>
</servlet-mapping>
```

第三步，在表单中使用img标签去显示验证码图片并使用它

```
验证码：<input type="text" name="code" style="width: 60px " >
<img src="http://localhost:8080/temp/kaptcha.jpg" width="100px" height=28px alt=""><br/>
<input type="submit" value="登录">
```

第四步，在服务器获取谷歌生成的验证码和客户端发送过来的验证码比较使用。

```
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // 获取Session中的验证码
    String token= (String)req.getSession().getAttribute(KAPTCHA_SESSION_KEY);
    // 删除 Session中的验证码
    req.getSession().removeAttribute(KAPTCHA_SESSION_KEY);
    //获取用户输入的验证码
    String code = req.getParameter("code");
    // 获取用户名
    String username = req.getParameter("username");
    if (token!=null && token.equals(code)){
        System.out.println("保存数据"   +  username +   "到数据库");
        resp.sendRedirect(req.getContextPath()+ "/ok.jsp");
    }else {

        req.getSession().setAttribute("msg","验证码错误");
        resp.sendRedirect(req.getContextPath()+ "/regist.jsp");
        System.out.println("请不要重复提交");
    }

}
```

KAPTCHA_SESSION_KEY为temp\web\lib\kaptcha2.3.2.jar!\com\google\code\kaptcha\Constants.class

下的固定值，代表了生成的验证码内容

第五步，把上四步导入到书城项目中.......

// 获取Session中的验证码
    String token= (String)req.getSession().getAttribute(KAPTCHA_SESSION_KEY);
    // 删除 Session中的验证码
    req.getSession().removeAttribute(KAPTCHA_SESSION_KEY);

把写死的abcde改成 token  			

为img加上id

```
<img id="code_img" src="kaptcha.jpg"  style="float: right;margin-right: 50px; width:100px; height:40px;"  alt=""><br/>
```

为其绑定单击事件

```
$("#code_img").click(function () {
//在事件响应的function函数中有一个this对象。这个this对象，是当前正在响应事件的dom对象。
   this.src="${basePath}/kaptcha.jpg";
})
```

使其能点击刷新

在事件响应的function函数中有一个this对象。这个this对象，是当前正在响应事件的dom对象。



#### 问题

使用火狐ie等浏览器时，点击验证码只能刷新一次，后面再点就没反应

因为浏览器为了让请求速度更快。就会每次请求的内容缓存到了浏览器端(要么硬盘上，要么内存中)。

第一次请求的图片地址为 http://localhost:8080/book/kaptcha.jpg

第二次请求地址还是为http://localhost:8080/book/kaptcha.jpg

浏览器会直接从缓存中获取原来的图片返回

##### 解决

缓存的名称由最后的资源名和参数组成

在kaptcha.jpg后加上每次不相同的参数即可（时间戳）

"http://localhost:8080/book/kaptcha.jpg?d="+ new Date();

```
$("#code_img").click(function () {
   //在事件响应的function函数中有一个this对象。这个this对象，是当前正在响应事件的dom对象。
   this.src="${basePath}kaptcha.jpg?d=" + new Date();
})
```

new Date()为时间戳



### 购物车模块

市面上购物车的实现技术版本有:

1、Session版本（把购物车信息保存到Session域中）（必学）

2、数据库版本(把购物车信息，保存到数据库)

3.、redis+数据库+Cookie（使用CookieiRedis缓存，和数据库)



使用session版本

![image-20221109175727170](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221109175727170.png)

购物车模块有四个功能

加入购物车，删除商品项，清空购物车，修改商品数量

结账属于订单模块

因为使用了session，不需要和数据库交互，也就没有dao和service层（合二为一变成cart购物车）

#### 编写购物车模块bean,dao,service

购物车分为两个部分，一个为购物车商品项CartItem，一个为购物车对象Cart

```
public class CartItem {
    private Integer id;
    private String name;
    private Integer count;
    private BigDecimal price;
    private BigDecimal totalPrice;
```

```
public class Cart {
    private Integer totalCount;
    private BigDecimal totalPrice;
    private List<CartItem> items = new ArrayList<CartItem>();
```

因为没有dao和service，所以直接在pojo中写入方法



先写cart购物车

##### 添加商品

```
private Map<Integer,CartItem> items = new LinkedHashMap<Integer,CartItem>();

//添加商品
public void addCartItem(CartItem cartItem){
    //先查看购物车中是否已经添加过此商品，如果已添加，则数量累加，总金额更新，如果没有添加过，直接放到集合中即可
    CartItem cartItem1 = items.get(cartItem.getId());
    if (cartItem == null){
        //之前未添加过此商品
        items.put(cartItem.getId(),cartItem);
    }else   {
        //已经添加过此商品
        cartItem1.setCount(cartItem1.getCount() +1);//更新商品数量
        cartItem1.setTotalPrice(cartItem1.getPrice().multiply(new BigDecimal(cartItem1.getCount())));//更新商品总价格
    }

}
```

通过Map.get(Object key) 方法返回指定键所映射的值，来获取要添加商品的key值

若已经添加过商品，则商品数量+1，总价格等于 单价乘以商品数量

BigInteger.multiply(BigInteger val)	用于BigDecimal之间相乘（即使一个是BigDecimal也要用）



##### 修改商品信息

```
//修改商品信息
public void updateCount(Integer id,Integer count){
    //先查看购物车中是否有此商品。如果有，修改商品数量，更新总金额
    CartItem cartItem = items.get(id);
    if (cartItem != null){
        cartItem.setCount(cartItem.getCount() +1);//更新商品数量
        cartItem.setTotalPrice(cartItem.getPrice().multiply(new BigDecimal(cartItem.getCount())));//更新商品总价格
    }

}
```

因为总金额和总商品数不能随便设置，把set方法删除，在get方法中获得

并且在get方法中建立int totalCount，不需要在一开始建立

```
public Integer getTotalCount() {
    int totalCount = 0;
    for (Map.Entry<Integer,CartItem>entry : items.entrySet()){
        totalCount=entry.getValue().getCount() + totalCount;
    }
    return totalCount;
}


public BigDecimal getTotalPrice() {
    BigDecimal totalPrice = new BigDecimal(0);
    for (Map.Entry<Integer,CartItem>entry : items.entrySet()){
        totalPrice=totalPrice.add(entry.getValue().getTotalPrice());
    }
    return totalPrice;
}
```

Map.Entry是为了更方便的输出map键值对。

Map.Entry是Map声明的一个内部接口，此接口为泛型，定义为Entry<K,V>。

一般情况下，要输出Map中的key 和 value 是先得到key的集合keySet()，然后再迭代（循环）由每个key得到每个value。values()方法是获取集合中的所有值，不包含键，没有对应关系。而Entry可以一次性获得这两个值。

entrySet(): 获取到Map集合中所有的键值对对象的集合(Set集合)

int bigdecimal中add()方法为	相加

Map的entrySet()方法返回一个实现Map.Entry接口的对象集合



##### 删除商品

```
public void deleteCartItem(Integer id){
    items.remove(id);

}
```

列表.remove() 方法通过指定元素的值来移除列表中某个元素的第一个匹配项



##### 清空购物车

```
public void clear(CartItem cartItem){
    items.clear();

}
```

clear() 用于清除所有元素



##### 添加商品到购物车

```
<%@include file="/pages/common/head.jsp"%>
	<script type="text/javascript">
		$(function () {
			$("button.addToCart").click(function () {
				alert(123);
				var bookId = $(this).attr("bookId");
				location.href="http://localhost:8080/book/cartServlet?action=addItem&id=" + bookId;
			})
		})
	</script>

<button bookId="${books.id}" class="addToCart">加入购物车</button>
```

对购物车按钮绑定单击事件

注意  script要在静态包含之下，静态包含中有text/javascript开启的语句

attr() 方法设置或返回被选元素的属性值



```
protected void addItem(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

    System.out.println("加入到购物车");
    System.out.println("商品编号为："+ req.getParameter("id"));

    //获取请求的参数商品编号
    int id = WebUtils.parseInt(req.getParameter("id"), 0);
    //调用bookservice.queryBookById(id): Book得到图书的信息
    Book book = bookService.queryBookById(id);
    //把图书信息，转换成为cartItem商品项
    CartItem cartItem=new CartItem(book.getId(),book.getName(),1,book.getPrice(),book.getPrice());
    //调用cart.addItem( cartItem);添加商品项

    //先判断一下购物车cart是否被创建，没被创建就创建，创建了就不用再创建了
    Cart cart =(Cart) req.getSession().getAttribute("cart");
    if (cart==null){
        cart=new Cart();
        req.getSession().setAttribute("cart",cart);
    }

    cart.addCartItem(cartItem);
    System.out.println(cart);
    // 重定向回商品列表页面
    System.out.println(req.getHeader("Referer"));
    resp.sendRedirect(req.getHeader("Referer"));

}
```



Cart cart =(Cart) req.getSession().getAttribute("cart");
    if (cart==null){
        cart=new Cart();
        req.getSession().setAttribute("cart",cart);
    }

一定要先判断购物车是否创建



在第二第三个分页点了加入购物车，重定向会自动返回首页，所以要用请求头referer

resp.sendRedirect(req.getHeader("Referer"));

在Http协议中有一个请求头，叫Referer,它可以把请求发起时,浏览器地址栏中的地址发送给服务器.，

不是发送请求的那个地址，而是请求发起之前的地址！！！



##### 购物车展示

```
<table>
      <tr>
         <td>商品名称</td>
         <td>数量</td>
         <td>单价</td>
         <td>金额</td>
         <td>操作</td>
      </tr>
      <%--若购物车为空，显示的内容--%>
      <c:if test="${empty sessionScope.cart.items}">
         <tr>
            <td colspan="5">亲，当前购物车为空，快去购物吧！！</td>
         </tr>
         <tr>
            <td colspan="5"><a href="index.jsp">点击跳转到首页</a></td>
         </tr>
      </c:if>
      <%--若购物车不为空，显示的内容--%>
      <c:if test="${not empty sessionScope.cart.items}">
         <c:forEach items="${sessionScope.cart.items}" var="entry">
            <tr>
               <td>${entry.value.name}</td>
               <td>${entry.value.count}</td>
               <td>${entry.value.price}</td>
               <td>${entry.value.totalPrice}</td>
               <td><a href="#">删除</a></td>
            </tr>
         </c:forEach>
      </c:if>

      
   </table>
   <%--如果购物车不为空，才显示--%>
   <c:if test="${not empty sessionScope.cart.items}">
   <div class="cart_info">
      <span class="cart_span">购物车中共有<span class="b_count">${sessionScope.cart.totalCount}</span>件商品</span>
      <span class="cart_span">总金额<span class="b_price">${sessionScope.cart.totalPrice}</span>元</span>
      <span class="cart_span"><a href="#">清空购物车</a></span>
      <span class="cart_span"><a href="pages/cart/checkout.jsp">去结账</a></span>
   </div>
   </c:if>
</div>
```

<td colspan="5">亲，当前购物车为空，快去购物吧！！</td>

colspan为跨其他单元格，总共有五列，

<c:forEach items="${sessionScope.cart.items}" var="entry">
            <tr>
               <td>${entry.value.name}</td>

因为items为map类型，entry.value取出其value的值，再取name的值



##### 删除商品项

cart.jsp

```
<script type="text/javascript">
		$(function () {
			$("a.deleteItem").click(function () {
				return confirm("是否要删除【"+$(this).parent().parent().find("td:first").text()+"】")
			})

		})
	</script

<td><a  class="deleteItem" href="cartServlet?action=deleteItem&id=${entry.value.id}">删除</a></td>
```

CartServlet.java

```
public void addCartItem(CartItem cartItem){
    //先查看购物车中是否已经添加过此商品，如果已添加，则数量累加，总金额更新，如果没有添加过，直接放到集合中即可
    CartItem cartItem1 = items.get(cartItem.getId());
    if (cartItem1 == null){
        //之前未添加过此商品
        items.put(cartItem.getId(),cartItem);
    }else   {
        //已经添加过此商品
        cartItem1.setCount(cartItem1.getCount() +1);//更新商品数量
        cartItem1.setTotalPrice(cartItem1.getPrice().multiply(new BigDecimal(cartItem1.getCount())));//更新商品总价格
    }
```

$(this).parent().parent().find("td:first").text()！！！找到指定a标签的父标签的父标签的第一个子标签的文本



##### 清空购物车

CartServlet.java

```
protected void clearCart(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cart cart = (Cart)req.getSession().getAttribute("cart");
    //若购物车不为空，则清空购物车
    if (cart!=null){
        cart.clear();
        //重定向回购物车展示页面
        resp.sendRedirect(req.getHeader("Referer"));

    }
```

cart.jsp

```
$("a.clearCart").click(function () {
   return confirm("是否要清空购物车")
});
```

同上



##### 修改商品数量

cart.jsp

```
//为修改商品数量绑定 onchange时间，内容发送改变响应事件
$(".updateCount").change(function () {
   //获取商品名称
   var name = $(this).parent().parent().find("td:first").text();
   //获取商品id
   var id = $(this).attr("bookId");
   //获取商品数量
   var count = this.value;
   //确认是否修改
   if (confirm("您确定要将【"+name+"】商品的数量修改为："+count)){
   //确认修改，跳转到servlet服务器，并把id，count传入
      location.href="cartServlet?action=updateCount&id="+id+"&count="+count;

   }else {
      /// defaultValue属性是表单dom对象的属性。它表示默认的value属性值,即第一次赋给value的值
      this.value = this.defaultValue;
   }
   
   <td><input bookId="${entry.value.id}"  style="width: 60px" type="text" class="updateCount" value="${entry.value.count}"></td>
```

onchange事件为更改才会响应事件

defaultValue属性是表单dom对象的属性。它表示默认的value属性值,即第一次赋给value的值！！！



CarServlet.java

```
protected void updateCount(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //获取要修改商品的id和count值
    int id = WebUtils.parseInt(req.getParameter("id"),0);
    int count = WebUtils.parseInt(req.getParameter("count"),0);

    Cart cart = (Cart)req.getSession().getAttribute("cart");
    //若购物车不为空，则修改商品项
    if (cart!=null){
        cart.updateCount(id,count);
        //重定向回购物车展示页面
        resp.sendRedirect(req.getHeader("Referer"));

    }
```



##### 回显购物车数据

在首页上显示购物车商品数量，最后一次添加的商品名称

保存最后一次添加商品信息，在cartservlet.additem中，保存到session域中





### 订单模块

![image-20221110170735755](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221110170735755.png)

#### Dao

OrderDao

```
public int saveOrder(Order order) {
    String sql = "insert into t_order(`order_id`,`create_time`,`price`,`status`,`user_id`) values(?,?,?,?,?)";

    return updata(sql,order.getOrderId(),order.getCreateTime(),order.getPrice(),order.getStatus(),order.getUserId());
}
```



OrderItemDao

```
public int saveOrderItem(OrderItem orderItem) {
    String sql = "insert into t_order_item(`name`,`count`,`price`,`total_price`,`order_id`) values(?,?,?,?,?)";

    return updata(sql,orderItem.getName(),orderItem.getCount(),orderItem.getPrice(),orderItem.getTotalPrice(),orderItem.getOrderId());
}
```



#### Service

```
public String createOrder(Cart cart, Integer userId) {
    //订货号==唯一性
    String orderId = System.currentTimeMillis()+""+userId;

    //创建订单对象
    Order order =new Order(orderId,new Date(),cart.getTotalPrice(),0,userId);

    //保存订单
    orderDao.saveOrder(order);

    //遍历购物车中的每一个商品项转换成为订单号保存到数据库
    for (Map.Entry<Integer, CartItem> entry:cart.getItems().entrySet()){
        //获取每一个购物车商品项
        CartItem cartItem = entry.getValue();
        //转换成为订单项
        OrderItem orderItem = new OrderItem(null,cartItem.getName(),cartItem.getCount(),cartItem.getPrice(),cartItem.getTotalPrice(),orderId);
        //保存到数据库
        orderItemDao.saveOrderItem(orderItem);

    }
    cart.clear();
    return orderId;
}
```

System.currentTimeMillis()时间戳，userId用户id保证订单唯一性



#### Servlet

```
protected void creatOrder(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

    //获取购物车对象
    Cart cart = (Cart) req.getSession().getAttribute("cart");
    //获取userId
    User loginUser = (User)req.getSession().getAttribute("user");
    if (loginUser == null){
        req.getRequestDispatcher("/pages/user/login.jsp").forward(req,resp);
        return;
    }
    Integer userId = loginUser.getId();

    String orderId = orderService.createOrder(cart, userId);
    req.getSession().setAttribute("orderId",orderId);

    resp.sendRedirect(req.getContextPath()+"/pages/cart/checkout.jsp");

}
```

要用重定向防止重复提交订单		还要判断loginUser是否为空，空表示未登录，跳转到登录界面

if (loginUser == null){
        req.getRequestDispatcher("/pages/user/login.jsp").forward(req,resp);
        return;
    }

在请求转发之后通常不要执行任何代码了。所以用return（**技巧！！！！**）

且要在orderservice中加入更新库存操作，每次购物车下单，库存销量都要改变

```
//遍历购物车中的每一个商品项转换成为订单号保存到数据库
for (Map.Entry<Integer, CartItem> entry:cart.getItems().entrySet()){
    //获取每一个购物车商品项
    CartItem cartItem = entry.getValue();
    //转换成为订单项
    OrderItem orderItem = new OrderItem(null,cartItem.getName(),cartItem.getCount(),cartItem.getPrice(),cartItem.getTotalPrice(),orderId);
    //保存到数据库
    orderItemDao.saveOrderItem(orderItem);
    //更新库存和销量
    Book book = bookDao.queryBookById(cartItem.getId());
    book.setSales(book.getSales() + cartItem.getCount());
    book.setStock(book.getStock() - cartItem.getCount());
    bookDao.updateBook(book);

}
```





















## Filter过滤器

filter是 web的三大组件之一

Filter过滤器它的作用是:拦截请求,过滤响应。





使用

adminfliter.java

```
public class AdminFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
    }
    /**
     * doFilter方法，专门用于拦截转发，可做权限检查
     * @param servletRequest
     * @param servletResponse
     * @param filterChain
     * @throws IOException
     * @throws ServletException
     */
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {

        HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
        HttpSession session = httpServletRequest.getSession();
        Object user = session.getAttribute("user");
        //若等于空，表示未登录
        if (user == null){
            servletRequest.getRequestDispatcher("/login.jsp").forward(servletRequest,servletResponse);
            return;
        }else
            //让程序继续访问
            filterChain.doFilter(servletRequest,servletResponse);
    }
    @Override
    public void destroy() {

    }
}
```

doFilter方法，专门用于拦截转发，可做权限检查

filterChain.doFilter(servletRequest,servletResponse);表示什么都不做，让程序继续执行

getSession()是HttpServletRequest的方法，

HttpServletRequest继承于ServletRequest



在web中的配置

```
<filter>
    <filter-name>AdminFilter</filter-name>
    <filter-class>com.atguigu.filter.AdminFilter</filter-class>
</filter>
<!--filter-mapping配置Filter过滤器的拦截路径-->
<filter-mapping>
    <!--filter-name表示当前的拦截路径给哪个filter使用-->
    <filter-name>AdminFilter</filter-name>
    <!--url-pattern配置拦截路径
    /表示请求地址为: http://ip:port/工程路径/映射到IDEA的web目录
    /admin/*表示请求地址为: http://ip:port/工程路径/admin/-->
    <url-pattern>/admin/*</url-pattern>
</filter-mapping>
```

<url-pattern>/admin/*</url-pattern>表示拦截的地址！！



### 生命周期

Filter的生命周期包含几个方法

1、构造器方法(即有参无参函数)

2、init初始化方法

第1，2步，在web工程启动的时候执行（ Filter已经创建）

3、doFilter过滤方法

第3步，每次拦截到请求，就会执行

4、destroy销毁

第4步，停止web工程的时候，就会执行（停止 web工程，也会销毁Filter过滤器)



### FilterConfig

filterconfig是init方法中的参数

作用

1.获取Filter的名称filter-name的内容

2.获取在web.xmL中配置的init-param初始化参数

3.获取ServletContext对象

```
public void init(FilterConfig filterConfig) throws ServletException {
    //1.获取Filter的名称filter-name的内容
    System.out.println("filter-name的值为："+filterConfig.getFilterName());
    //2.获取在web.xmL中配置的init-param初始化参数
    System.out.println("初始化参数username的值为："+filterConfig.getInitParameter("username"));
    System.out.println("初始化参数url的值为："+filterConfig.getInitParameter("url"));
    //3.获取ServletContext对象
    System.out.println(filterConfig.getServletContext());
    //


}
```

初始化参数在web.xml中配置

```
<filter>
    <filter-name>AdminFilter</filter-name>
    <filter-class>com.atguigu.filter.AdminFilter</filter-class>

    <init-param>
        <param-name>username</param-name>
        <param-value>root</param-value>
    </init-param>
    <init-param>
        <param-name>url</param-name>
        <param-value>jdbc:mysql://localhost:3306/test</param-value>
    </init-param>
</filter>
```





### FilterChain

![image-20221114191911073](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221114191911073.png)

FilterChain过滤链(多个过滤器一起工作)

FilterChain.doFilter()方法的作用

1、执行下一个Filter过滤器(如果有Filter)

2、执行目标资源(没有Filter)



工作顺序为

Filter1前置代码
Filter2前置代码
target.jsp代码被执行
Filter2后置代码
Filter1后置代码

先往前走到资源处，再往回走后置代码



且执行filter1或filter2的顺序由web.xml中配置顺序决定



若filter2中的dofilter方法去掉，就会执行到Filter2前置代码，不往下



### 多个Filter过滤器执行的特点:

1、所有iter和百标资源默认都执行在同一个线程中

2、多个Filter共同执行的时候,它们都使'用同一个Request对象。（**重！！**）



### 后缀名匹配

<ur1-pattern>*.html</ur1-pattern>
以上配置的路径，表示请求地址必须以.html结尾才会拦截到，且不以	/	开头



### Filter过滤器它只关心请求的地址是否匹配，不关心请求的资源是否存在





## 书城第七阶段

### Filter

/pages/manager下的资源为登录之后才能访问的后台管理

为其设置过滤器

ManagerFilter.java

```
public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {


    HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
    Object user = httpServletRequest.getSession().getAttribute("user");

    if (user == null){
        httpServletRequest.setAttribute("msg","您还未登录，请先登录");
        httpServletRequest.getRequestDispatcher("/pages/user/login.jsp").forward(servletRequest,servletResponse);
    }else
        filterChain.doFilter(servletRequest,servletResponse);
}
```

web.xml

```
<filter>
    <filter-name>ManagerFilter</filter-name>
    <filter-class>com.atguigu.filter.ManagerFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>ManagerFilter</filter-name>
    <url-pattern>/pages/manager/*</url-pattern>
    <url-pattern>/manager/bookServlet</url-pattern>
</filter-mapping>
```

<url-pattern>/pages/manager/*</url-pattern>
    <url-pattern>/manager/bookServlet</url-pattern>

可一次性设置两行拦截		懂的人会直接访问bookservlet来访问后台，所以也要设置拦截



### ThreadLocal

ThreadLocal的作用，它可以解决多线程的数据安全问题。

ThreadLocal它可以给当前线程关联一个数据（可以是普通变量，可以是对象，也可以是数组，集合)

ThreadLocal特点：

1、ThreadLocal可以为当前线程关联一个数据。(它可以像Map一样存取数据，key为当前线程)

2、每一个ThreadLocal对象，只能为当前线程关联一个数据，如果要为当前线程关联多个数据，就需要使用多个ThreadLocal对象实例。

3、每个ThreadLocal对象实例定义的时候，一般都是static类型

4、ThreadLocal中保存数据，在线程销毁后。会由JⅣM虚拟自动释放。



### 使用事务管理

有些操作需要一起完成，或者都不完成

例如提交订单

```
//保存订单
orderDao.saveOrder(order);

//遍历购物车中的每一个商品项转换成为订单号保存到数据库
for (Map.Entry<Integer, CartItem> entry:cart.getItems().entrySet()){
    //获取每一个购物车商品项
    CartItem cartItem = entry.getValue();
    //转换成为订单项
    OrderItem orderItem = new OrderItem(null,cartItem.getName(),cartItem.getCount(),cartItem.getPrice(),cartItem.getTotalPrice(),orderId);
    //保存到数据库
    orderItemDao.saveOrderItem(orderItem);
    //更新库存和销量
    Book book = bookDao.queryBookById(cartItem.getId());
    book.setSales(book.getSales() + cartItem.getCount());
    book.setStock(book.getStock() - cartItem.getCount());
    bookDao.updateBook(book);

}
//清空购物车
cart.clear();
```

若在保存订单和保存订单项之间发生错误，就会导致订单保存了，订单项没保存

要确保所有操作要么都成功。要么都失败，就必须要使用数据库的事务。

要确保所有操作都在一个事务内，就必须要确保，所有操作都使用同一个Connection连接对象。

使用threadlocal可确保	所有操作都是用	同一个connection对象

前提是，所有操作要在同一个线程中完成

![image-20221115125144494](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221115125144494.png)

所以要大改	思路就是把关闭连接的操作	放在orderservlet中执行完所有提交（表单提交，表单项提交，book库存向量修改等等）	之后再手动关闭

jdbcUtils.java

```
private static ThreadLocal<Connection> conns = new ThreadLocal<Connection>();


/**
     * 获取数据库连接池中的连接
     * @return 如果返回null,说明获取连接失败<br/>有值就是获取连接成功
     */
    public static Connection getConnection(){

        Connection conn = conns.get();

        if (conn == null){
            try {
                conn= dataSource.getConnection();//从数据库连接池获取连接

                conns.set(conn);        //保存到ThreadLocal对象中，供后面的jdbc操作使用

                conn.setAutoCommit(false);
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        return conn;
    }

    /**
     * 提交事务，并关闭释放连接
     */
    public static void commitAndClose(){
        Connection connection = conns.get();
        if (connection != null){    //如果不等于空，说明之前使用过连接，操作过数据库
            try {
                connection.commit();    //提交事务
            } catch (SQLException e) {
                e.printStackTrace();
            }finally {
                try {
                    connection.close(); //关闭连接
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
        //一定要执行remove操作！！！ 否则回出错
        conns.remove();

    }

    /**
     * 回滚事务，并关闭释放连接
     */
    public static void rollbackAndClose(){
        Connection connection = conns.get();
        if (connection != null){    //如果不等于空，说明之前使用过连接，操作过数据库
            try {
                connection.rollback();    //回滚事务
            } catch (SQLException e) {
                e.printStackTrace();
            }finally {
                try {
                    connection.close(); //关闭连接
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
        //一定要执行remove操作！！！ 否则回出错
        conns.remove();
    }
```

ThreadLocal<Connection>表示创建	一个thread对象	与	一个connection对象关联

之后conns.get()就表示调用关联的那个对象



BaseDao.java		所有的方法都要改成

```
catch (SQLException e){
    e.printStackTrace();
    throw new RuntimeException(e);
}
```

把之前的finally的关闭连接删除，放在orderservlet中执行完	手动关闭

改成throw new RuntimeException(e);	把获取的异常外抛，抛到orderservlet中，让其分辨是要回滚还是正常提交（一定要runtime外抛，否贼 orderservlet中找不到异常）



OrderServlet.java

```
try {
    orderId = orderService.createOrder(cart, userId);

    JdbcUtils.commitAndClose();     //没异常提交事务
} catch (Exception e) {
    JdbcUtils.rollbackAndClose();   //有异常回滚事务
    e.printStackTrace();
}
```



### 使用filter—给所有Service方法都加上try-catch来管理事务

![image-20221116130140766](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221116130140766.png)



按照上面事务管理，要给每个setvice方法都要加上try-catch，可用filter过滤器

filter过滤器可以调用servlet程序，servlet会调用service.....继续调用下去，所以给filter过滤器加try-catch

异常还是会抛到外面



TransactionFilter.java

```
public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {

    try {
        filterChain.doFilter(servletRequest,servletResponse);
        JdbcUtils.commitAndClose();     //提交事务，关闭连接
    } catch (Exception e) {
        e.printStackTrace();
        JdbcUtils.rollbackAndClose();   //回滚事务，关闭连接
    }

}
```

web.xml

```
<filter>
    <filter-name>TransactionFilter</filter-name>
    <filter-class>com.atguigu.filter.TransactionFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>TransactionFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

设置拦截地址为/*	全部拦截



在BaseServlet.java

```
catch (Exception e) {
    e.printStackTrace();
    throw new RuntimeException(e);
}
```

要把异常抛出，不要断在里面，要抛到Filter



OrderServlet中变成之前的样子

```
String orderId =orderService.createOrder(cart, userId);
```

由filter捕获异常





### 将所有异常都统一交给Tomcat，让Tomcat展示友好的错误信息页面

在TransactionFilter.java中

```
catch (Exception e) {
    JdbcUtils.rollbackAndClose();   //回滚事务，关闭连接
    e.printStackTrace();
    throw new RuntimeException(e);

}
```

再把异常往外抛，抛给tomcat解决



在web.xml中配置错误信息

```
<!--error-page标签配置，服务器出错之后，自动跳转的页面-->
<error-page>
    <!--error-code是错误类型-->
    <error-code>500</error-code>
    <!--Location标签表示。要跳转去的页面路径-->
    <location>/pages/error/error500.jsp</location>
</error-page>

<!--error-page标签配置，服务器出错之后，自动跳转的页面-->
    <error-page>
        <!--error-code是错误类型-->
        <error-code>404</error-code>
        <!--Location标签表示。要跳转去的页面路径-->
        <location>/pages/error/error404.jsp</location>
    </error-page>
```





## JSON

JSON(JavaScr ipt Object Notaticn)是一种轻量级的数据交换格式。易于人阅读和编写。同时也易于机器解析和生成。

轻量级指的是跟xnl做比较。
数据交换指的是客户端和服务器之间业务数据的传递格式。



### JSON定义

json是由键值对组成，并且由花括号（大括号)包围。每个键由引号引起来，键和值之间使用冒号进行分隔，多组键值对之间进行逗号进行分隔。

键值可以是任何类型，整形，字符串，数组，json也行，甚至数组中的对象也可以是json类型，布尔类型

```
var jsonObj={
   "key1":123,
   "key2":"abc",
   "key3":[11,"abc",true],
   "key5":{
      "key5_1":551,
      "key5_2":"key5_2_value"
   },
   "key6":[
      {
         "key6_1_1":6611,
         "key6_1_2":"key6_1_2value"
      },{
         "key6_2_1":6621,
         "key6_2_2":"key6_2_2value"
      }
   ]
};
```



### JSON的访问

json本身就是一个对象，访问就像访问map一样

json对象.key

```
alert(jsonObj.key1);
alert(jsonObj.key2);
for (var i = 0; i<jsonObj.key3.length;i++){
   alert(jsonObj.key3[i]);
}
alert(jsonObj.key4.key4_1);
alert(jsonObj.key4.key4_2);
var key50 = jsonObj.key5[0];
alert(key50.key5_1_1);
alert(key50.key5_1_2);
var key51 = jsonObj.key5[1];
alert(key51.key5_2_1);
alert(key51.key5_2_2);
```



### JSON常用方法

json的存在有两种形式。

一种是:对象的形式存在，我们叫它json对象。

一种是:字符串的形式存在，我们叫它json字符串。

一般我们要操作json中的数据的时候，需要json对象的格式。

一般我们要在客户端和服务器之间进行数据交换的时候，使用json字符串。

JSON.stringify()		   		  把json对象转换成为 json字符串

JSON.parse()						 把json字符串转换成为json对象

```
//把json对象转换为json字符串
var stringify = JSON.stringify(jsonObj);
alert(stringify);

//把json字符串转换为json对象
var parse = JSON.parse(stringify);
alert(parse);
```



### JSON在java中的使用

#### javaBean和 json的互转

```
public void test1(){
    Person person = new Person(1,"ljxxx");
    //创建Gson对象实例
    Gson gson = new Gson();
    //toJson方法可以把任何类型的java对象转换成为Json字符串
    String PersonJsonString = gson.toJson(person);
    System.out.println(PersonJsonString);
    //fromJson把json字符串转换回Java对象
    //第一个参数是json字符串
    //第二个参数是转换回去的Java对象类型
    Person person1 = gson.fromJson(PersonJsonString, Person.class);
    System.out.println(person1);
}
```

//toJson方法可以把任何类型的java对象转换成为Json字符串

//fromJson把json字符串转换回Java对象
//第一个参数是json字符串
//第二个参数是转换回去的Java对象类型		！！！！



#### List和 json的互转（重！！！）



```
public void test2() {
    List<Person> personList = new ArrayList<>();
    personList.add(new Person(1,"ljxxx"));
    personList.add(new Person(2,"cxa"));

    //创建Gson对象实例
    Gson gson = new Gson();

    //toJson方法可以把任何类型的java对象转换成为Json字符串
    String PersonListJsonString = gson.toJson(personList);
    System.out.println(PersonListJsonString);

    //把Json字符串对象转换成为List对象
        List<Person> list = gson.fromJson(PersonListJsonString, new PersonListType().getType());
        System.out.println(list);
        Person person = list.get(0);
        System.out.println(person);
    }
```

把Json字符串对象转换成为List对象，还是用fromJson方法，第二个参数不能再写Person.class，这样转换出来是一个map类型，要调用gson包中的TypeToken类

PersonListType.java

```
public class PersonListType extends TypeToken<ArrayList<Person>> {

}
```

PersonListType继承TypeToken<ArrayList<Person>>类，在<>中写入要转换的类型

可以是List<>,也可以是ArrayList<>，但Person一定要写

再在fromJson第二个参数中实例化一个PersonListType对象，调用getType方法获取type类型

！！！！



#### map 和 json的互转

```
public void test3(){
    Map<Integer,Person> personMap = new HashMap<>();
    personMap.put(1,new Person(1,"ljxxx"));
    personMap.put(2,new Person(2,"cxa"));
    
    Gson gson = new Gson();
    String PersonMapJsonString = gson.toJson(personMap);
    System.out.println(PersonMapJsonString);
    Map<Integer,Person> personMap1 = gson.fromJson(PersonMapJsonString, new TypeToken<Map<Integer, Person>>() {}.getType());

    System.out.println(personMap1);
    Person person = personMap1.get(1);
    System.out.println(person);


}
```

方法可以和list集合转换一样，创建一个PersonMapType去转换，但这样换占据大量内存，所以可以用匿名类new TypeToken<Map<Integer, Person>>() {}.getType());

new TypeToken<Map<Integer, Person>>() {}	这里就是创建一个类对象，{}要写，然后调用getType方法





## AJAX

ajax是一种浏览器通过js异步发起请求。局部更新页面的技术。



### 实例运用

ajax.html

```
//在这里使用javascript语言发起Ajax请求，访问服务器AjaxServlet中javascriptAjax
         function ajaxRequest() {
//              1、我们首先要创建XMLHttpRequest
            var xmlHttpRequest = new XMLHttpRequest();

//              2、调用open方法设置请求参数
            xmlHttpRequest.open("Get","http://localhost:8080/16_Json_ajax_i18n/ajaxServlet?action=javaScripAjax",true);

            //4、在send方法前绑定onreadystatechange事件，处理请求完成后的操作。
            xmlHttpRequest.onreadystatechange = function () {
               if (xmlHttpRequest.readyState == 4 && xmlHttpRequest.status == 200) {
                  var parse = JSON.parse(xmlHttpRequest.responseText);
                  
                  document.getElementById("div01").innerHTML = "编号为："+parse.id+"，姓名为："+parse.name;
               }

            }

//              3、调用send方法发送请求
            xmlHttpRequest.send();

//
         }
```

xmlHttpRequest.open()方法三个参数，第一个请求方法类型（get post），第二个请求地址，第三个是否异步（true为异步，false为同步），通常网页都为异步，用户体验好



AjaxServlet.java

```
protected void javaScripAjax(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    System.out.println("Ajax请求过来了");
    Person person = new Person(1,"ljxxx");

    //服务器与客户端之间传输数据，用JSON字符串
    Gson gson = new Gson();
    String PersonJsonString = gson.toJson(person);

    //把数据带回客户端
    resp.getWriter().write(PersonJsonString);
}
```

原理就是在html页面发起ajax请求，请求到servlet程序中，再由servlet程序把数据传入到resp中（resp专门用于发送给客户端），再由html页面显示出来



XMLHttpRequest中由两个参数readyState，status表示状态

readyState

0:请求未初始化

1:服务器连接已建立

2:请求已接收

3:请求处理中

4:请求已完成,且响应已就绪



status

200: "OK”

404:未找到页面

且每次readyState发生变化，就会触发onreadystatechange事件，我们要给onreadystatechange绑上事件，当变化到readyState=4	status=200时表示成功从服务器传数据





### JQuery中的Ajax请求



#### $.ajax方法

参数

url				表示请求的地址
type			 表示请求的类型GET或 POST请求
data			表示发送给服务器的数据(就是servlet?action=xxx的action=xxx部分，即参数)
					格式有两种:
								一: name=value&name=value

​								二:{key: value}
success		请求成功，响应的回调函数

dataType		响应的数据类型

```
// ajax请求
$("#ajaxBtn").click(function(){
   $.ajax({
      url:"http://localhost:8080/16_Json_ajax_i18n/ajaxServlet",
      data:"action=JQueryAjax",
      type:"Get",
      //data为服务器传输过来的数据
      success:function (data) {
         // alert("服务器返回的数据是：" + data);
         var jsonObj = JSON.parse(data);
         $("#msg").html(" ajax 编号：" + jsonObj.id + " , 姓名：" + jsonObj.name);
      },
      dataType : "text"

   })
```

success:function (data)中的data就为服务器传输回来的数据

data:"action=JQueryAjax"有两种写法 data:{action:JQuertAjax}

当dataType: "text"	即传输回来的数据为text文本时，要我们JSON.parse(data)，把数据转成JSON对象，但dataType: "json"	不需要我们转换，可直接输出



#### $.get/$.post

为简化的$.ajax方法

```
// ajax--get请求
$("#getBtn").click(function(){

   $.get("http://localhost:8080/16_Json_ajax_i18n/ajaxServlet","action=JQueryGet",function (data) {
      // alert("服务器返回的数据是：" + data);
      var jsonObj = JSON.parse(data);
      $("#msg").html(" get 编号：" + jsonObj.id + " , 姓名：" + jsonObj.name);},"text");
   
});
```

直接在get()里面写参数，url,data,success,dataType四个参数，type参数由getpost决定



#### $.getJSON方法

```
$.getJSON("http://localhost:8080/16_Json_ajax_i18n/ajaxServlet","action=JQueryPost",
      function (data) {
         $("#msg").html(" post 编号：" + data.id + " , 姓名：" + data.name);
         },);
```

再次少了一个参数	dataType	参数类型固定为JSON





#### 表单序列化serialize()

serialize()可以把表单中所有表单项的内容都获取到，并以name=value&name=value的形式进行拼接。

```
$.getJSON("http://localhost:8080/16_Json_ajax_i18n/ajaxServlet","action=JQueryserialize&"+$("#form01").serialize(),
      function (data) {
         $("#msg").html(" post 编号：" + data.id + " , 姓名：" + data.name);
      },);
```

能把表单中的各项信息传到servlet中



#### 上面的方法都是	不跳转页面，只局部改变页面





## 书城第八阶段

### 使用ajax验证用户名是否可用

regist.jsp

```
$("#username").blur(function () {
   //获取用户名
   var username = this.value;
   $.getJSON("http://localhost:8080/book/userServlet","action=ajaxExistsUsername&username="+ username,function (data) {
      if (data.exitsUsername){
         $("span.errorMsg").text("用户名已被占用！！");
      }else $("span.errorMsg").text("用户名可用！！");
   });


})
```

为用户名文本框绑上失去焦点事件

UserServlet.java

```
protected void ajaxExistsUsername(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    String username = req.getParameter("username");

    boolean exitsUsername = userService.exitsUsername(username);

    Map<String,Object> resultMap = new HashMap<>();
    resultMap.put("exitsUsername",exitsUsername);

    Gson gson = new Gson();

    String json = gson.toJson(resultMap);

    resp.getWriter().write(json);


}
```

要转成map类型，因为jsp中data要调用参数，要给他参数名exitsUsername

