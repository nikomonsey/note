# 创建mvc入门工程

## 1.添加web模块

打包方式选为war后，会自动添加web模块，创建一下web.xml就行

## 2.打包方式选择war

```
<version>1.0-SNAPSHOT</version>
<packaging>war</packaging>
```

## 3.引入依赖

```
<dependencies>
    <!-- SpringMVC -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- 日志 -->
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.3</version>
    </dependency>
    <!-- ServletAPI -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
    </dependency>
    <!-- Spring5和Thymeleaf整合包 -->
    <dependency>
        <groupId>org.thymeleaf</groupId>
        <artifactId>thymeleaf-spring5</artifactId>
        <version>3.0.12.RELEASE</version>
    </dependency>
</dependencies>
```

通过maven的传递性，不需要配置spring依赖，mvc中包含spring

## 4.配置web.xml

注册SpringMVC的前端控制器DispatcherServlet，mvc就是通过DispatcherServlet对请求和响应进行统一处理

DispatcherServlet会对所有的请求和响应进行拦截操作！！

```
<!-- 配置SpringMVC的前端控制器，对浏览器发送的请求统一进行处理 -->
<servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>

<servlet-mapping>
<servlet-name>SpringMVC</servlet-name>
<!--
    设置springMVC的核心控制器所能处理的请求的请求路径
    /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
    但是/不能匹配.jsp请求路径的请求
    -->
<url-pattern>/</url-pattern>
</servlet-mapping>
```

<url-pattern>/</url-pattern>和<url-pattern>/*</url-pattern>区别

/不能拦截.jsp的文件请求，/*可以拦截所有类型的文件请求，但在tomcat中，有专门写了关于.jsp文件的请求控制器，所以只能写/	

## springmvc /*的拦截机制(重)

若为/ 则不拦截除静态资源(html,css,js等)以外的路径	.jsp(动态资源 有jspservlet 被解析为java程序) .action(自定义路径) 都不会被拦截

若为/* 则都拦截 若路径为/a.html，去controller层中找 GetMapping("/a.html")方法，若没有，则会交给tomcat静态资源默认处理器，直接去webapp目录下找 a.html文件，找到直接访问，找不到爆404
若路径为a.jsp或a.action，则会去除掉jsp和action的后缀！！！去找GetMapping("/a")的方法！！！

## 5.创建请求控制器

一定要用Controller注解，否则mvc识别不到

```
@Controller
public class HelloController {
    
}
```



## 6.**创建**SpringMVC的配置文件

springmvc的配置文件就是spring的配置文件

SpringMVC的配置文件默认的位置和名称:
位置:WEB-INF 下
名称: <servlet-name>-servLet.xml，当前配置下的配置文件名为springMVC-servLet.xml

```
<！--开启注解解析-->
<context:component-scan base-package="com.ljxxx.mvc.controller"></context:component-scan>

<!-- 配置Thymeleaf视图解析器 -->
<bean id="viewResolver"
      class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
    <property name="order" value="1"/>
    <property name="characterEncoding" value="UTF-8"/>
    <property name="templateEngine">
        <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
            <property name="templateResolver">
                <bean                     class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                    <!-- 视图前缀 -->
                    <property name="prefix" value="/WEB-INF/templates/"/>
                    <!-- 视图后缀 -->
                    <property name="suffix" value=".html"/>
                    <property name="templateMode" value="HTML5"/>
                    <property name="characterEncoding" value="UTF-8" />
                </bean>
            </property>
        </bean>
    </property>
</bean>
```

**服务器和客户端有视图的请求，mvc的前端控制器DispatcherServlet会自动调用Thymeleaf视图解析器**

试图分为 逻辑视图，物理视图	index为逻辑视图，
物理视图就是加上了视图前缀和视图后缀，mvc配置中的视图前缀为/WEB-INF/templates/	视图后缀为.html

所以物理视图为/WEB-INF/templates/index.html	！！！！

所以这时我们在/WEB-INF/templates/下	创建一个index.html文件



## 7.测试HelloController

配置tomcat服务器，设置好

![image-20230227135407821](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230227135407821.png)

直接启动发现会404，找不到主页，因为主页在/WEB-INF/templates/index.html

而服务器是不能直接访问WEB-INF文件的，就需要跳转

在Controller中

```
@Controller
public class HelloController {

    @RequestMapping("/")
    public String portal(){
        return "index";
    }

}
```

RequestMapping请求映射，此时浏览器的地址为http://localhost:8080/SpringMVC/

而RequestMapping中/被服务器解析为http://localhost:8080/SpringMVC/	就能对应上，找到portal()方法，方法返回值为index，会直接调用Thymeleaf视图解析器，在逻辑视图加入前后缀，返回给浏览器

这时再启动服务器访问，就能访问成功



## 8.测试超链接跳转

在index.html中

```
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<h2>index.html</h2>
<a th:href="@{/hello}">hello</a>
<a href="/hello">hello</a>
</body>
```

使用th:标签，前提是xmlns:th="http://www.thymeleaf.org“，即使用thymeleaf

《a th:href="@{/hello}">hello</a>	和《a href="/hello">hello</a>区别

若直接用a标签，则/hello会被浏览器解析	解析为http://localhost:8080/hello	并没有上写文(即工程具体名)

但是使用th:标签，/hello会被解析为http://localhost:8080/SpringMVC/hello

```
@RequestMapping("/hello")
public String hello(){
    return "hello";
}
```

在RequestMapping中写入/hello，就能找到success方法



## 总结

浏览器发送请求，若请求地址符合前端控制器的url-pattern，该请求就会被前端控制器DispatcherServlet处理。

前端控制器会读取SpringMVC的核心配置文件，通过扫描组件找到控制器，将请求地址和控制器中
@RequestMapping注解的value属性值进行匹配。

若匹配成功，该注解所标识的控制器方法就是处理请求的方法。

处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会被视图解析器解析，加上前缀和后缀组成视图的路径，通过Thymeleaf对视图进行渲染，最终转发到视图所对应页面（不是请求重定向，而是请求转发）



## 优化（重）

```
<servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
<!--SpringMVC配置文件的位置和目录-->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc.xml</param-value>
    </init-param>
<!--配置DispatcherServlet初始化的时间，1为服务器启动时配置-->
    <load-on-startup>1</load-on-startup>
</servlet>
```

使用<init-param>标签进行配置springmvc文件位置
因为resource文件夹会和java文件夹最后放在一起，所以在resource文件夹下创建springmvc.xml，则可以直接写classpath:springmvc.xml

<load-on-startup>配置DispatcherServlet初始化的时间，若不配置，默认就是在第一次使用它时初始化，这样就需要耗费许多时间，而值设置为1，则表示在服务器启动时，也把这个servlet也初始化了



# @RequestMapping注解

配置一个新的mvc工程

## @RequestMapping注解的位置

@RequestMapping标识一个类：设置映射请求的请求路径的初始信息

@RequestMapping标识一个方法：设置映射请求请求路径的具体信息

```
@RequestMapping("/test")
public class TestRequestMappingController {

    @RequestMapping("/hello")
    public String hello(){
        return "success";
    }
```

这时hello方法的路径为 /test/hello

若还有一个类也有hello方法

```
@Controller
@RequestMapping("/test")
public class PortalController {
    @RequestMapping("/")
    public String portal(){
        return "index";
    }

    @RequestMapping("/hello")
    public String hello(){
        return "success";
    }
```

则会报错

## @RequestMapping注解的value属性

作用:通过请求的请求路径匹配请求

查看requestmapping类中得，value属性为数组类型，所以可写多个value值

```
@RequestMapping({"/hello","/abc"})
public String hello(){
    return "success";
}
```

```
<a th:href="@{/hello}">测试RequestMapping标签位置</a>
<a th:href="@{/abc}">测试RequestMapping标签value属性</a>
```

只要一个满足就跳转



## @RequestMapping注解的method属性

```
@RequestMapping(value = {"/hello","/abc"},method = RequestMethod.GET)
public String hello(){
        return "success";
    }
```

RequestMethod为枚举类型，有多个值 GET POST PUT等等



***大部分请求类型都为GET类型，除了表单提交设置为POST***



设置表单	提交方法为 get，能访问

```
<form th:action="@{/hello}" method="get">
    <input type="submit" value="测试RequestMapping标签method属性">
</form>
```

同时method也是数组类型

```
@RequestMapping(value = {"/hello","/abc"},method = {RequestMethod.GET,RequestMethod.POST})
```



在@RequestMapping注解的基础上，衍生出了 派生注解	只需要写value值

@GetMapping,@PostMapping,@DeLeteMapping, @PutMapping

## @RequestMapping注解的params属性(了解)

作用:通过请求的请求参数匹配请求，即浏览器发送的请求的请求参数必须满足params属性到的设置

params可以使用四种表达式:

"param":表示当前所匹配请求的请求参数中必须携带param参数

" ! param":表示当前所匹配请求的请求参数中一定不能携带param参数

"param=value":表示当前所匹配请求的请求参数中必须携带param参数且值必须为value

"param!=value":表示当前所匹配请求的请求参数中可以不携带param，若携带值一定不能是value

param可以是"username" value可以是"ljxxx"

params也是数组类型

```
@RequestMapping(value = {"/hello","/abc"},method = {RequestMethod.GET},params = {"username","!password","age=20","gender!=男"})
```

且数组内的条件要都满足，一项不满足，则失败



表单添加参数方法	效果都一样

```
<form th:action="@{/hello(username='admin'}" method="post">
    <input type="submit" value="测试RequestMapping标签params属性">
</form>

<form th:action="@{/hello?username=admnin}" method="post">
    <input type="submit" value="测试RequestMapping标签params属性">
</form>
```

第一种要有单引号！！



## @RequestMapping注解的headers属性(了解)

@RequestMapping注解的headers属性通过请求的请求头信息匹配请求映射

用法和params属性一样

注：若当前请求满足value和method属性，但是不满足headers属性，页面会显示404错误，即资源未被找到。



## SpringMVC支持ant风格的路径

```
?∶表示任意的单个字符 （不支持/,? 因为在url中  ?是地址与参数的分隔符）

*:表示任意的0个或多个字符（不支持/,? 因为在url中  ?是地址与参数的分隔符）

**:表示任意层数的任意目录 即表示/一个目录，是一层目录！！！！

注意:在使用时**， 只能使用/**/xxx的方式,即/**/test,不能单独/**这样写
```

```
@RequestMapping("/a*a/test")
public String testant(){
    return "success";
}
```

地址写	localhost:8080/springMVC/aabca/test	也能访问



## @RequestMapping注解中使用占位符

正常来说 路径和参数springMVC/test/rest?id=1

但可以写为springMVC/test/rest/1

```
<form th:action="@{/test/rest/1}" method="get">
    <input type="submit" value="测试RequestMapping标签占位符">
</form>
```

这时controller中要写

```
@RequestMapping("/test/rest/{id}")
public String testRest(@PathVariable("id") Integer id){
    System.out.println("id="+id);
    return "success";
}
```

通过{id}把1存储下来，然后用@PathVariable注解（路径参数）赋给一个参数

字符串也行

```
<form th:action="@{/test/rest/ljxxx}" method="get">
    <input type="submit" value="测试RequestMapping标签占位符">
</form>
```

```
@RequestMapping("/test/rest/{username}")
public String testRest(@PathVariable("username") String username){
    System.out.println("username="+username);
    return "success";
}
```



# SpringMVC获取请求参数

## 1.通过ServletAPI获取

在控制器方法中设置HttpServletRequest类型的参数，servlet会自动往参数中注入浏览器传过来的参数

通过getParameter方法

```
@RequestMapping("/params/servletAPI")
public String getParamsByServletAPI(HttpServletRequest request){
    String username = request.getParameter("username");
    String password = request.getParameter("password");
    System.out.println("username="+username+",password="+password);
    return "success";
}
```

```
<form th:action="@{/params/servletAPI}" method="get">
    用户名：<input type="text" name="username"><br>
    密码：<input type="password" name="password"><br>
    <input type="submit" value="登录">

</form>
```



## 2.通过控制器方法的参数获取

只需要保持控制器方法中的参数名和浏览器端传入的参数名相同就行

```
@RequestMapping("/params")
public String getParams(String username,String password){
    System.out.println("username="+username+",password="+password);
    return "success";
}
```

```
<form th:action="@{/params}" method="get">
    用户名：<input type="text" name="username"><br>
    密码：<input type="password" name="password"><br>
    <input type="submit" value="登录">
</form>
```



## 3.通过@RequestParam注解获取

```
@RequestMapping("/params/RequestParam")
public String getParamsByRequestParam(@RequestParam(value = "username",required = false,defaultValue = "hello") String name, @RequestParam("password") String word){
    System.out.println("username="+name+",password="+word);
    return "success";
}
```

@RequestParam("password") String word	把浏览器请求的参数password和控制器方法的word参数绑定

@RequestParam有三个参数，第一个值，
第二个required表示是否一定要赋值，true表示，username参数一定要有值，若为空，则报错，false表示不一定要有值，若为空，则为默认值，
第三个defaultValue	设置默认值，这时与required的值无关，若浏览器请求参数为空时，会把默认值赋给请求参数



## @RequestHeader和@CookieValue注解

和RequestParam参数一样

```
@RequestMapping("/header/cookie")
public String getHeaderAndCookie(@RequestHeader("referer") String referer,@CookieValue("JSESSIONID") String jsessionid){
    System.out.println("referer="+referer);
    System.out.println("JSESSIONID="+jsessionid);
    return "success";
}
```

RequestHeader注解用于获取头信息，CookieValue注解用于获取cookie信息

先在servletAPI方法中加入 HttpSession session = request.getSession();获取session，浏览器会自动加入一个cookie，以JSESSIONID为键名

```
@RequestMapping("/params/servletAPI")
public String getParamsByServletAPI(HttpServletRequest request){
    HttpSession session = request.getSession();
```

所以先执行一次getParamsByServletAPI，再执行getHeaderAndCookie

结果为referer=http://localhost:8080/springMVC/

JSESSIONID=BC60225558008AD4AC75AABBB56B8432



## 通过pojo获取参数

设置user类，保证类中属性名和浏览器的请求参数名一致，就可获取参数

```
@RequestMapping("/params/pojo")
public String getParamsByPojo(User user ){
    System.out.println(user);
    return "success";
}
```

```
<form th:action="@{/params/pojo}" method="get">
    用户名：<input type="text" name="username"><br>
    密码：<input type="password" name="password"><br>
    <input type="submit" value="登录">
</form>
```



## 获取请求参数乱码问题

若为tomcat7，则get和post都有乱码，
get请求 解决办法

在tomcat包中的conf文件夹下的server.xml文件的71行

<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />

加上<Connector port="8080" URIEncoding="UTF-8" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />

若为tomcat8，get方法没有乱码

设置post方法无乱码

在javaweb中，

```
public String getParamsByServletAPI(HttpServletRequest request){}
```

调用request.setCharacterEncoding()方法

但是在springmvc中，请求的参数先传给了DispatcherServlet控制器，控制器再给了HttpServletRequest，所以设置了也没用



### 解决

在web.xml中设置过滤器CharacterEncodingFilter，再设置它的映射

```
<!--配置spring的编码过滤器-->
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

<init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>

encoding为CharacterEncodingFilter类中的参数，设置编码为UTF-8

<init-param>
        <param-name>forEncoding</param-name>
        <param-value>true</param-value>
    </init-param>

设置不管是请求还是响应，即request还是Response都能用到UTF-8编码



### 注意：

SpringMVC中处理编码的过滤器一定要配置到其他过滤器之前，否则无效



# 域对象共享数据

## 使用ServletAPI向request域对象共享数据(不多用)

```
@RequestMapping("/testServletAPI")
publicStringtestServletAPI(HttpServletRequest request){
  request.setAttribute("testScope","hello,servletAPI");  
  return"success";
}
```



## 使用ModelAndView向request域对象共享数据(重)

ModelAndView包含ModeL和View的功能：

Model :向请求域中共享数据
View:设置逻辑视图实现页面跳转

在mvc中，任何控制器方法的返回值，都会被DispatcherServlet控制器转换为ModelAndView对象
若返回值为字符串，则设置给view



```
@RequestMapping("/test/mav")
public ModelAndView testMAV(){
    ModelAndView mav = new ModelAndView();
    /**
     * ModelAndView包含ModeL和View的功能
     * Model :向请求域中共享数据
     * view:设置逻辑视图实现页面跳转
     */
    //向请求域共享数据
    mav.addObject("username","ljxxx");
	//设置视图，实现页面跳转
    mav.setViewName("success");
    return mav;
}
```

```
<a th:href="@{/test/mav}">测试通过ModelAndView向请求域中共享数据</a>
```

在success.html中，用p标签获取request域中对象

```
<p th:text="${username}"></p>
```

注意：若使用了ModelAndView，则一定要将其返回，即将方法类型设置为ModelAndView，否则不起作用



## 使用Model,map,ModelMap向request域对象共享数据

方法大同小异

```
@RequestMapping("/test/model")
public String testModel(Model model){
    model.addAttribute("username","ljxxx");
    return "success";
}

@RequestMapping("/test/modelmap")
public String testModelMap(ModelMap modelmap){
    modelmap.addAttribute("username","ljxxx");
    return "success";
}

@RequestMapping("/test/map")
public String testMap(Map<String,Object> map){
    map.put("username","ljxxx");
    return "success";
}
```

```
<a th:href="@{/test/model}">测试通过model向请求域中共享数据</a><br>
<a th:href="@{/test/modelmap}">测试通过modelmap向请求域中共享数据</a><br>
<a th:href="@{/test/map}">测试通过map向请求域中共享数据</a><br>
```

### 三者关系

Model、ModelMap、Map类型的参数其实本质上都是 BindingAwareModelMap 类型的

在底层中

```
public interface Model{}
public class ModelMap extends LinkedHashMap<String,Object>{}
public class ExtendedModelMap extends ModelMap implementsModel{}
public class BindingAwareModelMap extends ExtendedModelMap{}
```



## 向session，application域共享数据

都是用servletAPI

```
@RequestMapping("/test/session")
public String testSession(HttpSession session){
    session.setAttribute("session","session");
    return "success";
}

@RequestMapping("/test/application")
public String testApplication(HttpSession session){
    ServletContext servletContext = session.getServletContext();
    servletContext.setAttribute("application","application");
    return "success";
}
```

application通过session.getServletContext();方法获取

```
<a th:href="@{/test/session}">测试向会话域(session)中共享数据</a><br>
<a th:href="@{/test/application}">测试向应用域(application)中共享数据</a><br>
```

输出通过p标签	session.数据名 application.数据名	输出

```
<p th:text="${session.session}"></p>
<p th:text="${application.application}"></p>
```

若点完测试session，再点击application，会发现session也在

因为session的作用域为浏览器，只有浏览器关闭时，session域中才会清空
application的作用域为服务器，只有服务器关闭重启才会清空，但是idea中，服务器重启也会导致session域中的数据被清空

![image-20230228155100980](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230228155100980.png)

在tomcat设置中，把Preserve勾选，重启服务器就不会清空session







# SpringMVC的视图

SpringMVC中的视图是View接口，视图的作用渲染数据，将模型Model中的数据展示给用户

SpringMVC视图的种类很多，默认有转发视图和重定向视图

**当工程引入jstl的依赖，转发视图会自动转换为JstlView**

若使用的视图技术为Thymeleaf，在SpringMVC的配置文件中配置了Thymeleaf的视图解析器，由此视

图解析器解析之后所得到的是ThymeleafView



##  ThymeleafView

当控制器方法中所设置的视图名称没有任何前缀时，此时的视图名称会被SpringMVC配置文件中所配置

的视图解析器解析，视图名称拼接视图前缀和视图后缀所得到的最终路径，会通过转发的方式实现跳转

```
@RequestMapping("/test/thymeleafView")
public String testThymeleafView(){
    return "success";
}
```

这种什么前缀也没有就是ThymeleafView视图



## 转发视图

SpringMVC中默认的转发视图是InternalResourceView

```
@RequestMapping("/test/view/forward")
public String testForward(){
    return "forward:/test/session";
}
```

转发到/test/session，直接通过服务器，而不是浏览器，浏览器中的地址不变
基本不用

会去找controller层下的/test/session方法！！！！！

使用转发视图，不会通过ThymeleafView渲染，若要解析页面中的ThymeleafView语法

```
<a th:href="@{/test/view/forward}">
```

就不能用

若直接转发到一个网页，这样就不会通过ThymeleafView渲染，也不能共享session等数据

```
@RequestMapping("/test/view/forward")
public String testForward(){
    return "forward:/try.jsp";
}
```



## 重点

mvc的DispatcherServlet作用是，所有浏览器的地址/springMVC/test/view/forward，都会去controller层中找路径相同的方法，若没找到则报错

所以不能直接写/springMVC/try.html	会报404错误	能直接访问try.jsp，过滤器中没过滤 .jsp文件





## 重定向视图

SpringMVC中默认的重定向视图是RedirectView

```
@RequestMapping("/test/view/redirect")
public String testRedirect(){
    return "redirect:/test/session";
}
```

和转发分组区别在于，是把地址给浏览器，浏览器去访问，这时是由浏览器解析地址/test/session，正常来说

第一个/被解析成http://localhost:8080/没有项目名，但是mvc会自动帮我加上项目名

若重定向是 redirect:test/session	没有第一个/，则会把地址中的/test/view/redirect	redirect去掉，把剩下的接上！！！

会去controller层中找/test/session方法，若没有找到，则会使用默认处理，可能会去找静态资源







## 视图控制器view-controller

在springmvc.xml配置文件中

```
<!--开启mvc的注解驱动-->
<mvc:annotation-driven></mvc:annotation-driven>

<!--开启视图控制器-->
<mvc:view-controller path="/" view-name="index.html"></mvc:view-controller>
```

通过mvc:view-controller path="/" view-name="index.html"	开启视图控制器
path为请求路径	view-name为逻辑视图

当开启视图控制器后，只有视图控制器中的所设置的请求才会被处理，外面写的

```
@RequestMapping("/test/model")
public String testModel(Model model){
    model.addAttribute("username","ljxxx");
    return "success";
}
@RequestMapping("/test/modelmap")
public String testModelMap(ModelMap modelmap){
    modelmap.addAttribute("username","ljxxx");
    return "success";
}
```

等等不会被处理，所以需要开启mvc驱动注解

```
<mvc:annotation-driven></mvc:annotation-driven>
```



# RESTful

***重要的是开启处理请求方式过滤器HiddenHttpMethodFilter***

就是一种编码风格

HTTP 协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE。

它们分别对应四种基本操作：GET 用来获取资源，POST 用来新建资源，PUT 用来更新资源，DELETE 用来删除资源。

REST 风格提倡 URL 地址使用统一的风格设计，从前到后各个单词使用斜杠分开，不使用问号键值对方
式携带请求参数，而是将要发送给服务器的数据作为 URL 地址的一部分，以保证整体风格的一致性。

| **操作** | **传统方式**     | **REST风格**            |
| -------- | ---------------- | ----------------------- |
| 查询操作 | getUserById?id=1 | user/1-->get请求方式    |
| 保存操作 | saveUser         | user-->post请求方式     |
| 删除操作 | deleteUser?id=1  | user/1-->delete请求方式 |
| 更新操作 | updateUser       | user-->put请求方式      |



## 查询操作

查询所有用户信息	/user---->get

根据id查询用户信息	/user/1---->get

```
@RequestMapping("/user")
public String getAllUser(){
    System.out.println("查询到所有用户信息");
    return "success";
}

@RequestMapping("/user/{id}")
public String getUserById(@PathVariable("id") Integer id){
    System.out.println("查询id为"+id+"到用户信息");
    return "success";
}
```

```
<a th:href="@{/user}">查询所有用户信息</a>
<a th:href="@{/user/1}">根据id查询用户信息</a>
```



## 添加操作

```
<form th:action="@{/user}" method="post">
    <input type="submit" value="添加用户">
</form>
```

```
@RequestMapping(value = "/user",method = RequestMethod.POST)
public String insertUser(){
    System.out.println("添加用户信息");
    return "success";
}
```



## 修改操作

因为浏览器只支持get，post请求方式

所以需要用到HiddenHttpMethodFilter过滤器

```
<!--配置处理请求方法过滤器-->
<filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

用于隐藏网页的处理方法

HiddenHttpMethodFilter处理put和delete方法的条件为：
当前请求的请求方式必须为post
当前请求必须传输请求参数_method，才是最终的请求方式
value值写真实的处理方法

```
<form th:action="@{/user}" method="post">
    <input type="hidden" name="_method" value="put">
    <input type="submit" value="修改用户">
</form>
```

```
@RequestMapping(value = "/user",method = RequestMethod.PUT)
public String updateUser(){
    System.out.println("修改用户信息");
    return "success";
}
```

<input type="hidden" name="_method" value="put">	



## 删除操作

和修改操作相似

```
<form th:action="@{/user/1}" method="post">
    <input type="hidden" name="_method" value="delete">
    <input type="submit" value="删除用户">
</form>
```

```
@RequestMapping(value = "/user/{id}",method = RequestMethod.DELETE)
public String deleteUser(@PathVariable("id")Integer id){
    System.out.println("删除id为"+id+"的用户信息");
    return "success";
}
```





## 案例（重！！！）

使用固定的employeeDao

```
@Repository
public class EmployeeDao {

    private static Map<Integer, Employee> employees = null;
    static{
        employees = new HashMap<Integer, Employee>();
        employees.put(1001, new Employee(1001, "E-AA", "aa@163.com", 1));
        employees.put(1002, new Employee(1002, "E-BB", "bb@163.com", 1));
        employees.put(1003, new Employee(1003, "E-CC", "cc@163.com", 0));
        employees.put(1004, new Employee(1004, "E-DD", "dd@163.com", 0));
        employees.put(1005, new Employee(1005, "E-EE", "ee@163.com", 1));
    }
    private static Integer initId = 1006;
    public void save(Employee employee){
        if(employee.getId() == null){
            employee.setId(initId++);
        }
        employees.put(employee.getId(), employee);
    }
    public Collection<Employee> getAll(){
        return employees.values();
    }
    public Employee get(Integer id){
        return employees.get(id);
    }
    public void delete(Integer id){
        employees.remove(id);
    }
}
```

静态map和静态代码块static	实现类似数据库的操作，并放入数据

通过save方法，进行修改和增加操作，增加操作id为null，会调用Integer initId = 1006；然后自增加一

### 查询所有employee

Controller

```
@Autowired
private EmployeeDao employeeDao;

@RequestMapping("/employee")
public String getAllEmployee(Model model){
    Collection<Employee> allEmployee = employeeDao.getAll();
    model.addAttribute("allEmployee",allEmployee);
    return "employee_list";
}
```

使用request域共享

employee_list.html

```
<head>
    <meta charset="UTF-8">
    <title>employee_list.html</title>
    <link rel="stylesheet" th:href="@{/static/css/index_work.css}">
</head>
<body>
<table>
    <tr>
    //合并单元格 合并5格
        <th colspan="5">employee list</th>
    </tr>
    <tr>
        <th>id</th>
        <th>lastName</th>
        <th>email</th>
        <th>gender</th>
        <th>options</th>
    </tr>
    <tr th:each="employee : ${allEmployee}">
        <td th:text="${employee.id}"></td>
        <td th:text="${employee.lastName}"></td>
        <td th:text="${employee.email}"></td>
        <td th:text="${employee.gender}"></td>
        <td>
            <a href="">update</a>
            <a href="">delete</a>
        </td>
    </tr>
</table>
</body>
```

<th>标签，表标题单元格

<td>标签，表格数据单元格

使用th:each循环输出<tr th:each="employee : ${allEmployee}">

employee 为当次循环的对象



### 添加样式(重)

![image-20230301130958019](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230301130958019.png)

在employee_list.html中添加

```
<head>
    <meta charset="UTF-8">
    <title>employee_list.html</title>
    <link rel="stylesheet" th:href="@{/static/css/index_work.css}">
</head>
```

但此时也没用，

因为当前工程的web.xml配置的前端控制器Dispatcherservlet的url-pattern是/
tomcat的web.xmL配置的Defaultservlet的url-pattern也是/
此时，浏览器发送的请求会优先被DispatcherservLet进行处理，但是DispatcherServLet无法处理静态资源

所以要在springmvc.xml中配置

```
<!--配置默认的servlet处理静态资源-->
<mvc:default-servlet-handler/>
<!--开启mvc的注解驱动-->
    <mvc:annotation-driven></mvc:annotation-driven>
```

若只配置了第一个，则所有的请求都会被Defaultservlet处理，

而加上第二个开启注解驱动，则是DispatcherservLet处理不了的，交给Defaultservlet处理



### 添加employee

在所有employee页面中的options单元格设置跳转

```
<th>options (<a th:href="@{/to/add}" >add</a>)</th>
```

因为除了跳转没有任何作用，所有用视图控制器进行跳转

```
<mvc:view-controller path="/to/add" view-name="employee_add"></mvc:view-controller>
```

emplyee_add.html

```
<form th:action="@{/employee}" method="post">
    <table>
        <tr>
            <th colspan="2">add employee</th>
        </tr>
        <tr>
            <td>lastName</td>
            <td>
                <input type="text" name="lastName">
            </td>
        </tr>
        <tr>
            <td>email</td>
            <td>
                <input type="text" name="email">
            </td>
        </tr><tr>
            <td>gender</td>
            <td>
                <input type="radio" name="gender" value="1">male
                <input type="radio" name="gender" value="0">female
            </td>
        </tr>
        <tr>
            <th colspan="2">
                <input type="submit" value="提交">
            </th>
        </tr>
    </table>
</form>
</body>
```

使用重定向跳转回查询所有employee

```
@RequestMapping(value = "/employee",method = RequestMethod.POST)
public String insertEmployee(Employee employee ){
    employeeDao.save(employee);
    return "redirect:/employee";
}
```



### 修改employee(重)

```
<td>
    <a th:href="@{'/employee/'+${employee.id}}">update</a>
```

使用拼接来传输id值

```
@RequestMapping(value = "/employee/{id}",method = RequestMethod.GET)
public String toUpdate(@PathVariable("id")Integer id,Model model){
    System.out.println("toupdate");
    Employee employee = employeeDao.get(id);
    model.addAttribute("employee",employee);
    return "employee_update";
}
```

toUpdate用于查询单条数据，用于修改

employee_update.html

<input type="hidden" name="_method" value="put">用于设置请求方法为put修改

<input type="hidden" name="id" th:value="${employee.id}">用于传输修改的id值
***住：要用thymeleaf风格，所以后面要用到request域共享都要用th:xxx***

单选框使用	th:field="${employee.gender}	用于设置默认值

```
<form th:action="@{/employee}" method="post">
    <input type="hidden" name="_method" value="put">
    <input type="hidden" name="id" th:value="${employee.id}">
    <table>
        <tr>
            <th colspan="2">update employee</th>
        </tr>
        <tr>
            <td>lastName</td>
            <td>
                <input type="text" name="lastName" th:value="${employee.lastName}">
            </td>
        </tr>
        <tr>
            <td>email</td>
            <td>
                <input type="text" name="email" th:value="${employee.email}">
            </td>
        </tr><tr>
        <td>gender</td>
        <td>
            <input type="radio" name="gender" value="1" th:field="${employee.gender}">male
            <input type="radio" name="gender" value="0" th:field="${employee.gender}">female
        </td>
    </tr>
        <tr>
            <th colspan="2">
                <input type="submit" value="提交">
            </th>
        </tr>
    </table>
```

返回到employee	查找所有employee

```
@RequestMapping(value = "/employee",method = RequestMethod.PUT)
public String updateEmployee(Employee employee){
    employeeDao.save(employee);
    return "redirect:/employee";
}
```



### 删除employee(重)

难点：要通过一个超链接完成删除操作，即要设置请求方法为delete，使用vue技术（后面把vue学完）

```
<td>
<a @click="deleteEmployee()" th:href="@{'/employee/'+${employee.id}}">delete</a>
</td>

<form method="post">
    <input type="hidden" name="_method" value="DELETE">
</form>
<script type="text/javascript" th:src="@{/static/js/vue.js}"></script>
<script type="text/javascript">
    var vue = new Vue({
        el:"#app",
        methods:{
            deleteEmployee(){
                //获取form表单
                var form = document.getElementsByTagName("form")[0];
                //将超链接的href属性值赋值给form表单的action属性
                //event.target表示当前触发事件的标签
                form.action = event.target.href;
                //阻止超链接的默认行为
                event.preventDefault();
                //表单提交
                form.submit();
            }
        }
    })

</script>
```



# 测试SpringMVC处理ajax

ajax是用于在不刷新页面的情况下，局部变化页面

```
<div id="app">
    <h1>index.html</h1>
    <input type="button" value="测试SpringMVC处理ajax" @click="testAjax()"><br>
</div>
<script type="text/javascript" th:src="@{/js/vue.js}"></script>
<script type="text/javascript" th:src="@{/js/axios.min.js}"></script>
<script type="text/javascript">
    var vue = new Vue({
        el:"#app",
        methods:{
            testAjax(){
                axios.post(
                    "/SpringMVC/test/ajax?id=1001",
                    {username:"admin",password:"123456"}
                ).then(response=>{
                    console.log(response.data);
                });
            },
        }
    });
</script>
```

## 回顾axios

基本用法为

```
axios({
      url:"",//请求路径
      method:"",//请求方式
      
      //以name=value&name=value的方式发送的请求参数
      //不管使用的请求方式是get或post，请求参数都会被拼接到请求地址后
      //此种方式的请求参数可以通过request.getParameter()获取
      params:{},
      
      //以json格式发送的请求参数
      //请求参数会被保存到请求报文的请求体传输到服务器
      //此种方式的请求参数不可以通过request.getParameter()获取
      data:{}
      
   }).then(response=>{
       console.log(response.data);
   });

```

有axios.get(url[, config])，axios.post(url[, data[, config]])等方法

axios.get(url[, config])	method参数直接为get，只需要写url，但是没有params和data参数，若要写params参数，则直接拼在url后 例如/test?id=1，且get没有data参数，因为没有请求体，只有post才有

axios.post(url[, data[, config]])	method参数直接为post，只需要写url和data参数，post有请求体，所以有data参数，

### data参数和params参数的区别

params:{},：
以name=value&name=value的方式发送的请求参数
不管使用的请求方式是get或post，请求参数都会被拼接到请求地址后
此种方式的请求参数可以通过request.getParameter()获取

data:{}   ：
以json格式发送的请求参数
请求参数会被保存到请求报文的请求体传输到服务器
此种方式的请求参数不可以通过request.getParameter()获取

```
var vue = new Vue({
        el:"#app",
        methods:{
            testAjax(){
                axios.post(axios.post(
           "/SpringMVC/test/ajax?id=1001",
           {username:"admin",password:"123456"}
            ).then(response=>{
               console.log(response.data);
            });
```

then()方法，表示ajax请求成功后，服务器请求回来之后的结果，这里表示的是把response服务器响应的数据都打印到浏览器的控制台中



## 继续测试

```
@RequestMapping("/test/ajax")
public void testAjax(Integer id, HttpServletResponse httpServletResponse) throws Exception {
    System.out.println("id="+id);
    httpServletResponse.getWriter().write("hello,ajax");
}
```

使用httpServletResponse设置服务器响应的内容，且id为params参数，可以直接得到，但是得不到username，password数据，因为为data数据，存储在请求体中



## 使用@RequestBody注解处理json格式的请求参数

axios.post(url,{data})方法中的参数data会被存储在请求体中，请求体就是json格式的数据

### 1.引入依赖

```
<!--jackson的依赖-->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.12.1</version>
</dependency>
```

### 2.开启mvc注解驱动

```
<!--开启mvc的注解驱动-->
<mvc:annotation-driven></mvc:annotation-driven>
```

### 使用

在控制器方法的形参位置，设置json格式的请求参数要转换成的java类型（实体类或map）的参

数，并使用@RequestBody注解标识

```
@RequestMapping("/test/RequestBody/Json")
public void testRequestBody(@RequestBody User user, HttpServletResponse response) throws IOException {
    System.out.println(user);
    response.getWriter().write("hello,requestBody");
}

public void testRequestBodyToMap(@RequestBody Map<String,Object> map, HttpServletResponse response) throws IOException {
    System.out.println(map);
    response.getWriter().write("hello,requestBody");
}
```

如有和参数名相同属性名的实体类，可选择使用实体类	
若无，则使用map集合

```
testRequestBody(){
    axios.post(
        "/springMVC/test/RequestBody/Json",
        {username:"admin",password:"1234",gender:"男",age:20}
    ).then(response=>{
        console.log(response.data);
    })
}
```

方法中传输的是username，password等等	User类中有，就可以用



## @ResponseBody注解

使用和RequestBody注解一样，引入依赖，开启注解驱动

用于标识一个控制器方法，可以将该方法的返回值直接作为响应报文的响应体响应到浏览器

### 返回值为String类型

```
<a th:href="@{/test/ResponseBody/Json}">测试ResponseBody注解处理ajax</a><br>
<input type="button" value="测试ResponseBody注解处理ajax" @click="testResponseBody()"><br>
testResponseBody(){
                axios.post(
                    "/springMVC/test/ResponseBody/Json",
                ).then(response=>{
                    console.log(response.data);
                })
            }
```

一个是链接跳转，一个是按钮

```
@RequestMapping("/test/ResponseBody/Json")
@ResponseBody
public String testResponseBody(){
    return "success";
}
```

链接跳转会直接跳转到一个新的网页，内容为success，因为a标签不知道处理服务器响应的数据，所以直接输出，而按钮我们写了方法，所以会输出到浏览器控制台。



### 返回值为实现类，map，list

常用的ava对象转换为json的结果:

实体类-->json对象
map-->json对象
List-->json数组

```
@RequestMapping("/test/ResponseBody/Json")
 @ResponseBody
 public User testResponseBody(){
     User user=new User(123,"ljxxx","1234","男",22);
     return user;
}
```

即使是实体类，a标签也会用类似toString方法输出到网页上



## @RestController注解

@RestController注解是springMVC提供的一个复合注解，标识在控制器的类上，就相当于为类添加了@Controller注解，并且为其中的每个方法添加了@ResponseBody注解

相当于controller加上responseBody注解，给类中每个方法都加上responseBody注解



# 文件上传与下载

## 文件下载

写法基本固定，getRealPath为要下载文件的路径
is.available()为is中的字节数	
Content-Disposition响应头	为下载方式	attachment为附件下载

```
@RequestMapping("/test/down")
public ResponseEntity<byte[]> testResponseEntity(HttpSession session) throws IOException {
    //获取ServletContext对象
    ServletContext servletContext = session.getServletContext();
    //获取服务器中文件的真实路径
    String realPath = servletContext.getRealPath("/static/img/1.jpg");
    //创建输入流
    InputStream is = new FileInputStream(realPath);
    //创建字节数组
    byte[] bytes = new byte[is.available()];
    //将流读到字节数组中
    is.read(bytes);
    //创建HttpHeaders对象设置响应头信息
    MultiValueMap<String, String> headers = new HttpHeaders();
    //设置要下载方式以及下载文件的名字
    headers.add("Content-Disposition", "attachment;filename=1.jpg");
    //设置响应状态码
    HttpStatus statusCode = HttpStatus.OK;
    //创建ResponseEntity对象
    ResponseEntity<byte[]> responseEntity = new ResponseEntity<byte[]>(bytes, headers, statusCode);
    //关闭输入流
    is.close();
    return responseEntity;
}
```

通过ResponseEntity对象的构造方法，第一个参数为要下载文件路径的字节数组，第二个参数为响应头，第三个参数为响应状态码



## 文件上传

使用了fileupload	jar包

```
<!--文件上传依赖-->
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>
```

并且要在springmvc.xml中开启文件解析器，id值一定要是multipartResolver！！！

```
<!--开启文件解析器-->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
</bean>
```



input type="file" name="photo"	name值为photo
且enctype="multipart/form-data"一定要设置

```
<form th:action="@{/test/up}" method="post" enctype="multipart/form-data">
    头像:<input type="file" name="photo">
    <input type="submit" value="提交">
</form>
```

使用文件解析器MultipartFile	可直接获得要上传文件，获取和获取参数一致，名字要和表单中name属性一致
其中有getOriginalFilename()方法，获取文件名	transferTo()方法，用于上传文件，把全路径传入即可

File.pathSeparator为分隔符，会自动识别该用什么分隔符！！！！常用

```
@RequestMapping("/test/up")
public String testUp(HttpSession session, MultipartFile photo) throws IOException {
    //获取上传文件的文件名
    String originalFilename = photo.getOriginalFilename();
    ServletContext servletContext = session.getServletContext();
    //获取服务器中photo目录的路径,即要上传到服务器哪个位置
    String photoPath = servletContext.getRealPath("photo");
    //判断photo目录是否存在，不存在则创建
    File file = new File(photoPath);
    if (!file.exists()){
        file.mkdir();
    }
    //实现文件上传功能
    String finalPath = photoPath + File.pathSeparator + originalFilename;
    photo.transferTo(new File(finalPath));
    return "success";
}
```



## 文件重名问题

更改文件名，使用UUID，和时间戳差不多效果

```
String hzName = originalFilename.substring(originalFilename.lastIndexOf("."));
originalFilename = UUID.randomUUID().toString() + hzName;

xxxxxx

String finalPath = photoPath + File.separatorChar + originalFilename;
```

重新拼接一个新的文件名

substring()方法：通过指定字符分隔字符串，取后面一节的字符串

lastIndexOf()方法：获取指定字符在字符串中最后一次出现的位置fa

UUID.randomUUID()方法，获取随机数（乱码）



# 拦截器

## 单拦截器

SpringMVC中的拦截器用于拦截控制器方法的执行

SpringMVC中的拦截器需要实现HandlerInterceptor类

先创建一个拦截器	即创建一个类，继承HandlerInterceptor类
并对HandlerInterceptor中的三个方法进行重写（三个方法虽然都是抽象方法，但是都有自己的方法体，所以要手动ctrl+O进行重写）

```
public class FirstInterceptor implements HandlerInterceptor {
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle");
        return true;
    }

    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle");
    }

    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion");
    }
}
```

**preHandle为控制器方法之前执行	且返回值为false时，表示对控制器方法进行拦截，不让控制器执行操作，true表示对控制器方法进行放行**
postHandle为控制器方法之后执行
afterCompletion为控制器方法之后，且渲染完视图之后执行

然后在springmvc.xml配置拦截器

```
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/*"/>
        <mvc:exclude-mapping path="/abc"/>
        <!--<bean class="com.ljxxx.mvc.interceptor.FirstInterceptor"></bean>-->
        <ref bean="firstInterceptor"></ref>
    </mvc:interceptor>
</mvc:interceptors>
```

interceptors中的interceptor标签设置一个拦截器，可用bean 直接配置firstInterCeptor类
也可以用ref bean：" " 配置（前提是要对firstInterCeptor进行注解配置）

<mvc:mapping path="/*" />表示一层目录下进行拦截	如/a,/test，但是不能拦截/test/abc这样两层的目录

要拦截所有请求则要改为<mvc:mapping path="/**"/>	两个星号

<mvc:exclude-mapping path="/abc"/>表示不拦截某个目录



## 多个拦截器

执行顺序与在springmvc的配置顺序有关

配置顺序为

```
<mvc:interceptors>
    <ref bean="firstInterceptor"></ref>
    <ref bean="secondInterceptor"></ref>
</mvc:interceptors>
```

执行顺序为

```
FirstInterceptor-->preHandle
SecondInterceptor-->preHandle
SecondInterceptor-->postHandle
FirstInterceptor-->postHandle
SecondInterceptor-->afterCompletion
FirstInterceptor-->afterCompletion
```

即preHandle()方法和配置顺序一致
postHandle()和afterCompletion()方法和配置顺序相反



## 拦截器的preHandle()返回false

若拦截器中有某个拦截器的preHandle( )返回了false

拦截器的preHandle()返回false和它之前的拦截器的preHandle()都会执行
所有的拦截器的postHandLe()都不执行
拦截器的preHandle()返回false之前的拦截器的aftercompletion()会执行



# 异常处理器

## 使用xml配置处理器

SpringMVC提供了自定义的异常处理器SimpleMappingExceptionResolver

在springmvc配置文件中配置

```
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
    <property name="exceptionMappings">
        <props>
            <prop key="java.lang.ArithmeticException">error</prop>
        </props>
    </property>
    <property name="exceptionAttribute" value="ex"></property>
</bean>
```

exceptionMappings参数为异常映射
key为异常类型，ArithmeticException为算数异常，中间为value，设置的是出现异常时，要跳转页面的逻辑视图

exceptionAttribute参数为异常属性，即具体的错误信息，存储在request请求域中，value为存储请求域中键值为

在/test/hello控制器中添加算术异常

```
@RequestMapping("/test/hello")
public String testHello(){
    System.out.println(1/0);
    return "success";
}
```

在错误页面输出错误信息

```
<h2>error.html</h2>
<p th:text="${ex}"></pth:text>
```

结果为

![image-20230302125545022](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230302125545022.png)



## 使用注解配置处理器

```
@ControllerAdvice
public class ExceptionController {
    @ExceptionHandler(ArithmeticException.class)
    public ModelAndView handleException(Throwable ex){
        ModelAndView modelAndView=new ModelAndView();
        modelAndView.addObject("ex",ex);
        modelAndView.setViewName("error");
        return modelAndView;
    }
}
```

新建一个类用于异常处理，用@ControllerAdvice注解标识
方法用@ExceptionHandler注解标识	value值为要**处理异常类**

注：这时ModelAndView要在方法中新建，不能在参数中写，会500





# 注解配置SpringMVC

## 注解配置web.xml文件

***我们的类扩展了AbstractAnnotationConfigDispatcherServletInitializer并将其部署到Servlet3.0容器的时候，容器会自动发现它，并用它来配置Servlet上下文***
所以不需要做什么，写完之后直接开启服务器就行。

写一个配置类，通过继承AbstractAnnotationConfigDispatcherServletInitializer类



并重写方法来替代web.xml

```
public class WebUtil extends AbstractAnnotationConfigDispatcherServletInitializer {
	@Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[0];
    }
	@Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[0];
    }
	@Override
    protected String[] getServletMappings() {
        return new String[0];
    }
}
```

注：重写的是web.xml，所有要写的是过滤器和servlet

```
//获取spring的配置类
@Override
protected Class<?>[] getRootConfigClasses() {

    return new Class[]{WebConfig.class};
}

//获取SpringMVC的配置类
@Override
protected Class<?>[] getServletConfigClasses() {
    return new Class[]{SpringConfig.class};
}

//获取DispatcherServlet的映射规则，即url-pattern
@Override
protected String[] getServletMappings() {
    return new String[]{"/"};
}
```

getRootConfigClasses()为获取spring配置类的方法，返回值为数组，因为有时候有多个spring和springmvc配置文件，可用逗号隔开

getServletConfigClasses()为获取SpringMVC的配置类的方法，返回值也为数组

getServletMappings()为获取DispatcherServlet过滤器的映射规则，返回值也为数组

若要实现其他filter过滤器

则要重写getServletFilters方法

```
@Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter encodingFilter = new CharacterEncodingFilter();
        encodingFilter.setEncoding("UTF-8");
        encodingFilter.setForceEncoding(true);
        HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
        return new Filter[]{encodingFilter,hiddenHttpMethodFilter};
    }
```



## 注解配置springmvc.xml文件（重）

***通过继承WebMvcConfigurer类来实现***

且要用@Configuration注解表示为工具类

当前使用springmvc.xml文件实现了
扫描组件，视图控制器，默认的servlet，mvc注解驱动，视图控制器，文件上传解析器，拦截器，异常解析器

### 开启扫描组件

@ComponentScan注解用于开启扫描组件

### 开启mvc注解驱动

@EnableWebMvc注解用于开启mvc注解驱动

### 默认servlet

重写configureDefaultServletHandling()方法，调用参数的enable()方法，启用默认servlet

### 视图控制器

重写addViewControllers()方法，addViewController方法设置访问路径，setViewName方法设置逻辑视图，实现视图控制器

### 文件上传解析器

文件上传解析器为bean对象，所以用bean标签把方法返回值注册为bean对象，bean的id为方法名，所以方法名必须为multipartResolver，直接返回一个对象CommonsMultipartResolver就行

### 拦截器

写好拦截器类，重写addInterceptors方法，addInterceptor方法设置拦截器类，addPathPatterns方法设置拦截路径，excludePathPatterns方法设置不拦截路径

### 异常解析器（重）

异常解析器虽然在sprigmvc.xml中是bean，但是WebMvcConfigurer中有方法就用，使用configureHandlerExceptionResolvers方法，先新建一个SimpleMappingExceptionResolver类对象
在springmvc.xml中

```
<!--        <property name="exceptionMappings">-->
<!--            <props>-->
<!--                <prop key="java.lang.ArithmeticException">error</prop>-->
<!--            </props>-->
<!--        </property>-->
<!--        <property name="exceptionAttribute" value="ex"></property>-->
```

用的是property，所以新建一个property类，通过setProperty方法设置要处理异常的类和要跳转页面的逻辑视图，再调用simpleMappingExceptionResolver的setExceptionMappings方法，把property放入，再调用setExceptionAttribute方法，设置异常信息放入请求域中的键名
最后调用参数resolvers的add方法，把simpleMappingExceptionResolver对象放入



```
@Configuration
//开启扫描组件
@ComponentScan("com.ljxxx.mvc.controller")
//开启mvc注解驱动
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    //默认的servlet处理静态资源
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        //表示可用
        configurer.enable();
    }

    //视图控制器
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("index");
    }

    //文件上传解析器
    @Bean
    //@Bean注解可以识别方法的返回值注册成bean对象来管理，bean的id就是方法名
    public CommonsMultipartResolver multipartResolver(){
        return new CommonsMultipartResolver();
    }

    //拦截器
    public void addInterceptors(InterceptorRegistry registry) {
        FirstInterceptor firstInterceptor = new FirstInterceptor();
        SecondInterceptor secondInterceptor = new SecondInterceptor();
        registry.addInterceptor(firstInterceptor).addPathPatterns("/**").excludePathPatterns("/abc");
        registry.addInterceptor(secondInterceptor).addPathPatterns("/abc")；
    }

    //异常解析器
    public void configureHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
        SimpleMappingExceptionResolver simpleMappingExceptionResolver = new SimpleMappingExceptionResolver();
        Properties properties = new Properties();
        properties.setProperty("java.lang.ArithmeticException","error");
        //设置异常映射
        simpleMappingExceptionResolver.setExceptionMappings(properties);
        //设置共享异常信息的键
        simpleMappingExceptionResolver.setExceptionAttribute("ex");
        resolvers.add(simpleMappingExceptionResolver);
    }

    //配置生成模板解析器
    @Bean
    public ITemplateResolver templateResolver() {
        WebApplicationContext webApplicationContext =
                ContextLoader.getCurrentWebApplicationContext();
        // ServletContextTemplateResolver需要一个ServletContext作为构造参数，
        // 可通过WebApplicationContext的方法获得
        ServletContextTemplateResolver templateResolver = new ServletContextTemplateResolver(webApplicationContext.getServletContext());
        templateResolver.setPrefix("/WEB-INF/templates/");
        templateResolver.setSuffix(".html");
        templateResolver.setCharacterEncoding("UTF-8");
        templateResolver.setTemplateMode(TemplateMode.HTML);
        return templateResolver;
    }
    //生成模板引擎并为模板引擎注入模板解析器
    @Bean
    public SpringTemplateEngine templateEngine(ITemplateResolver
                                                       templateResolver) {
        SpringTemplateEngine templateEngine = new SpringTemplateEngine();
        templateEngine.setTemplateResolver(templateResolver);
        return templateEngine;
    }
    //生成视图解析器并未解析器注入模板引擎
    @Bean
    public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {
        ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
        viewResolver.setCharacterEncoding("UTF-8");
        viewResolver.setTemplateEngine(templateEngine);
        return viewResolver;
    }
```

生成视图控制器有模板





# SpringMVC执行流程

## SpringMVC常用组件

DispatcherServlet：**前端控制器**，不需要工程师开发，由框架提供
作用：统一处理请求和响应，整个流程控制的中心，由它调用其它组件处理用户的请求

HandlerMapping：**处理器映射器**，不需要工程师开发，由框架提供
作用：根据请求的url、method等信息查找Handler，即控制器方法

Handler：**处理器**，需要工程师开发
作用：在DispatcherServlet的控制下Handler对具体的用户请求进行处理

HandlerAdapter：**处理器适配器**，不需要工程师开发，由框架提供
作用：通过HandlerAdapter对处理器（控制器方法）进行执行

ViewResolver：**视图解析器**，不需要工程师开发，由框架提供
作用：进行视图解析，得到相应的视图，例如：ThymeleafView、InternalResourceView、RedirectView

View：**视图**
作用：将模型数据通过页面展示给用户



## SpringMVC的执行流程

需要记住标粗的几点



**1)** 用户向服务器发送请求，请求被SpringMVC 前端控制器 DispatcherServlet捕获。



**2) **DispatcherServlet对请求URL进行解析，得到请求资源标识符（URI），判断请求URI对应的映射：

a) 不存在

i. 再判断是否配置了mvc:default-servlet-handler

ii. 如果没配置，则控制台报映射查找不到，客户端展示404错误

iii. 如果有配置，则访问目标资源（一般为静态资源，如：JS,CSS,HTML），找不到客户端也会展示404

错误

b) 存在则执行下面的流程



**3)** 根据该URI，调用HandlerMapping获得该Handler配置的所有相关的对象（包括Handler对象以及

Handler对象对应的拦截器），最后以HandlerExecutionChain执行链对象的形式返回。



**4) **DispatcherServlet 根据获得的Handler，选择一个合适的HandlerAdapter。



**5)** 如果成功获得HandlerAdapter，此时将开始执行拦截器的preHandler(…)方法【正向】



6) 提取Request中的模型数据，填充Handler入参，开始执行Handler（Controller)方法，处理请求。

在填充Handler的入参过程中，根据你的配置，Spring将帮你做一些额外的工作：

a) HttpMessageConveter： 将请求消息（如Json、xml等数据）转换成一个对象，将对象转换为指定

的响应信息

b) 数据转换：对请求消息进行数据转换。如String转换成Integer、Double等

c) 数据格式化：对请求消息进行数据格式化。 如将字符串转换成格式化数字或格式化日期等

d) 数据验证： 验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中

7) Handler执行完成后，向DispatcherServlet 返回一个ModelAndView对象。



**8) **此时将开始执行拦截器的postHandle(...)方法【逆向】。



**9) **根据返回的ModelAndView（此时会判断是否存在异常：如果存在异常，则执行

HandlerExceptionResolver进行异常处理）选择一个适合的ViewResolver进行视图解析，根据Model

和View，来渲染视图。



**10)** 渲染视图完毕执行拦截器的afterCompletion(…)方法【逆向】。



**11) **将渲染结果返回给客户端。





