# 简介

spring security是spring家族中一个安全管理框架

一般Web应用的需要进行认证和授权。
	认证:验证当前访问系统的是不是本系统的用户，并且要确认具体是哪个用户
	授权:经过认证后判断当前用户是否有权限进行某个操作

而认证和授权也是Springsecurity作为安全框架的核心功能。



# 入门案例

使用springboot创建项目	引入security相关的依赖

```java
<!--springSecurity依赖    -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

编写controller代码	启动项目访问，提示需要输入用户名密码

```java
@RequestMapping("/hello")
public String hello(){
    return "hello";
}
```

默认的用户名为user	密码会打印在控制台中



# 认证

## 登录流程

![image-20230522211024805](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522211024805.png)

第4步中，jwt相应给前端后，前端会自动保存



## 完整流程

图中只展示了入门案例中的过滤器，即核心过滤器

![image-20230522211725061](C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522211725061.png)

UsernamePasswordsAuthenticationFilter：负责处理我们在登陆页面填写了用户名密码后的登陆请求。入门案例的认证工作主要有它负责。

ExceptionTranslationFilter：处理过滤器链中抛出的任何AccessDeniedException和AuthenticationException 
FilterSecuritylnterceptor：负责权限校验的过滤器。



## 认证流程详解

<img src="C:\Users\ljxxx\Desktop\student\java\笔记\tempphoto\image-20230522213146203.png" alt="image-20230522213146203"  />

Authentication接口: 它的实现类，表示当前访问系统的用户，封装了用户相关信息。

AuthenticationManager接口：定义了认证Authentication的方法 

UserDetailsService接口：加载用户特定数据的核心接口。里面定义了一个根据用户名查询用户信息的方法。

UserDetails接口：提供核心用户信息。通过UserDetailsService根据用户名获取处理的用户信息要封装成UserDetails对象返回。然后将这些信息封装到Authentication对象中。



security自动生成和调用的接口实现类存在些问题

UserDetailsService中检验用户名密码是否正确，是去内存中查找，即我们在application.properties文件中写的用户名密码或默认生成的用户名密码都存在内存中
但是大部分情况下，要去数据库中查找

UsernamePasswordsAuthenticationFilter中无法直接向浏览器返回一个token 存储着jwt信息



### 解决思路

登录

​	①自定义登录接口  

​				调用ProviderManager的方法进行认证 如果认证通过生成jwt

​				把用户信息存入redis中，用于之后请求的校验
​				不使用mysql	因为mysql每次都查询过于慢 redis可以解决

​	②自定义UserDetailsService 

​				在这个实现类中去查询数据库

校验：

​	①定义Jwt认证过滤器

​				获取token

​				解析token获取其中的userid

​				从redis中获取用户信息

​				存入SecurityContextHolder
​				之后的过滤器和资源从SecurityContextHolder中可以获取当前请求的用户信息

JWT 认证可以有效**避免 CSRF 攻击**，因为 JWT 一般是存在在 localStorage 中，使用 JWT 进行身份验证的过程中是**不会涉及到 Cookie** 的。