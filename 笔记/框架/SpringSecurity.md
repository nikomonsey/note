# 一.快速入门

> 使用Springboot工程搭建Spring Security项目。
>
> > springboot构建中有spring Security可选

## 引入依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

## 创建controller

```
@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello(){
        return "hello security";
    }
}
```

## 访问localhost:8080/hello

自动跳转到

![image-20230327093923179](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230327093923179.png)

用户名 user

密码在 idea控制台中打印	Using generated security password: e6226504-c1f8-48c1-99ed-0b0d3c59d7d2



# 二.Spring Security基础概念

## 1.过滤器链

> - WebAsyncManagerIntegrationFilter：将SecurityContext集成到Spring MVC中用于管理异步请求处理的WebAsyncManager中。
> - SecurityContextPersistenceFilter：在当前会话中填充SecurityContext，SecurityContext即Security的上下文对象，里面包含了当前用户的认证及权限信息等。
> - HeaderWriterFilter：向请求的Header中添加信息
> - CsrfFilter：用于防止CSRF（跨域请求伪造）攻击。Spring Security会对所有post请求验证是否包含系统生成的CSRF的信息，如果不包含则报错。
> - LogoutFilter：匹配URL为“/logout”的请求，清除认证信息，实现用户注销功能。
> - UsernamePasswordAuthenticationFilter：认证操作的过滤器，用于匹配URL为“/login”的POST请求做拦截，校验表单中的用户名和密码。
> - DefaultLoginPageGeneratingFilter：如果没有配置登陆页面，则生成默认的认证页面
> - DefaultLogoutPageGeneratingFilter：用于生成默认的退出页面
> - BasicAuthenticationFilter：用于Http基本认证，自动解析Http请求头中名为Authentication的内容，并获得内容中“basic”开头之后的信息。
> - RequestCacheAwareFilter：用于缓存HttpServletRequest
> - SecurityContextHolderAwareRequestFilter：用于封装ServletRequest，让ServletRequest具备更多功能。
> - AnonymousAuthenticationFilter：对于未登录情况下的处理，当SecurityContextHolder中认证信息为空，则会创建一个匿名用户存入到SecurityContextHolder中
> - SessionManagementFilter：限制同一用户开启多个会话
> - ExceptionTranslationFilter：异常过滤器，用来处理在认证授权过程中抛出异常。
> - FilterSecurityInterceptor：获取授权信息，根据SecurityContextHolder中存储的用户信息判断用户是否有权限访问

## 2.认证方式

分为HTTP基本认证，Form表单认证(默认)，HTTP摘要认证

## 3.基本认证

HTTP基本认证是在RFC2616标准中定义的一种认证模式，它以一种很简单的方式与用户进行交互。HTTP基本认证可以分为如下4个步骤：

- 客户端首先发起一个未携带认证信息的请求；

- 然后服务器端返回一个401 Unauthorized的响应信息，并在WWW-Authentication头部中说明认证形式：当进行HTTP基本认证时，WWW-Authentication会被设置为Basic realm=“被保护的页面”；

- 接下来客户端会收到这个401 Unauthorized响应信息，并弹出一个对话框，询问用户名和密码。当用户输入后，客户端会将用户名和密码使用冒号进行拼接并用Base64编码，然后将其放入到请求的Authorization头部并发送给服务器；

- 最后服务器端对客户端发来的信息进行解码得到用户名和密码，并对该信息进行校验判断是否正确，最终给客户端返回响应内容。

  HTTP基本认证是一种无状态的认证方式，与表单认证相比，HTTP基本认证是一种基于HTTP层面的认证方式，无法携带Session信息，也就无法实现Remember-Me功能。另外，用户名和密码在传递时仅做了一次简单的Base64编码，几乎等同于以明文传输，极易被进行密码窃听和重放攻击。所以在实际开发中，很少会使用这种认证方式来进行安全校验。	且请求头信息放在session中，所以在浏览器重启或关闭前，无法进行注销等操作



新建配置类	使用@EnableWebSecurity注解 开启AuthenticationConfiguration配置类
继承WebSecurityConfigurerAdapter类

```
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
       //配置基本认证方式
        http.authorizeRequests().anyRequest().authenticated()
                //开启基本认证
                .and().httpBasic();
    }
}
```

authorizeRequests开启权限认证，anyRequest对所有请求开启，authenticated认证，使用and进行连接
httpBasic开启基本认证
也可以	同时开启两种认证

```
.and().httpBasic().and().formLogin();
```



在SpringBootWebSecurityConfiguration类中默认开启了 基本认证，表单认证

```
SecurityFilterChain defaultSecurityFilterChain(HttpSecurity http) throws Exception {
    ((AuthorizedUrl)http.authorizeRequests().anyRequest()).authenticated();
    http.formLogin();
    http.httpBasic();
    return (SecurityFilterChain)http.build();
}
```



### 安全问题

在登录过后的页面中 F12可查看到	Authorization请求头	后面存储着用户名和密码	用Base64编码
这时用postman软件，加上个请求头Authorization	就可以直接访问到页面，不需要登录操作

![image-20230327104506739](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230327104506739.png)





# 三.表单认证

## 1.表单认证中的预置url和页面

> 默认的formLogin配置中，自动配置了一些url和页面:
>
> - **/login(get)**: get请求时会跳转到这个页面，只要我们访问任意一个需要认证的请求时，都会跳转到这个登录界面。
> - **/login(post)**: post请求时会触发这个接口，在登录页面点击登录时，默认的登录页面表单中的action就是关联这个login接口。
> - **/login?error**: 当用户名或密码错误时，会跳转到该页面。
> - **/:** 登录成功后，默认跳转到该页面，如果配置了index.html页面，则 ”/“ 会重定向到index.html页面，当然这个页面要由我们自己实现。
> - **/logout:** 注销页面。
> - **/login?logout:** 注销成功后跳转到的页面。
>
> 由此可见，SpringSecurity默认有两个login，即登录页面和登录接口的地址都是 /login:
>
> - GET http://localhost:8080/login
> - POST http://localhost:8080/login
>
> 如果是 GET 请求，表示你想访问登录页面；如果是 POST 请求，表示你想提交登录数据。
> 对于这几个URL接口，我们简单了解即可。



## 2.自定义登录页首页失败页等信息(重！！)

***一定要在最后加上.csrf().disable();	关闭csrf功能，否则登录不成功！！！***

loginPage设置登录页的位置 放在static 静态资源下	
permitAll()无条件允许访问	即之前的所有配置对所有用户都生效	可以理解成一个开始函数	
defaultSuccessUrl	登录成功后跳转的页面，也是放在static静态资源下，是否跳转到指定页面，默认值为false

> 这里本来在controller写好方法GetMapping("/index.html")用于跳转，但是静态资源下若有名字相同的文件，例如这时static文件下有叫index.html文件，会自动去找到它，不需要我们再写方法！！
>
> 使用springboot创建的web工程，没有webapp文件夹，只要静态资源放在类路径下： `/static` or `/public` or `/resources` or `/META-INF/resources`都相当于放在webapp文件夹下	若在controller中没有写对应的跳转方法，则会直接去上面的文件夹找同名的文件

loginProcessingUrl方法设置的是post请求的/login	即你在登录页面输入账户密码，点击提交的跳转地址

> 这里其实要在controller中写上方法，因为是Url路径，服务器会去控制层中找@GetMapper路径匹配的方法，但是security会自动帮我们写好这个方法，不需要我们写！！

```
<form action="/login" method="post">
```

这里表单中的action为login，loginProcessingUrl中写的就是login	方法一定要为post	默认值也是为login

> 这里的aciton中的值和loginProcessingUrl中的值一定要一致

failureUrl设置登录失败页面 放在static静态资源下
usernameParameter和passwordParameter设置表单中用户名和密码属性的name值	默认为username等
logout()开启注销功能，表单认证默认开启	logoutUrl设置注销访问的地址
logoutSuccessUrl设置注销成功后跳转的页面

```
@Override
protected void configure(HttpSecurity http) throws Exception {
    //配置基本认证方式
    http.authorizeRequests().anyRequest().authenticated()
            //开启基本认证
            .and().formLogin()
            .loginPage("/login.html")
            .permitAll()
            //当登录成功后，是否跳转到指定页面	默认值为false
            .defaultSuccessUrl("/index.html",true)
            //post请求的登录接口
            .loginProcessingUrl("/login")
            .failureUrl("/error.html")
            //登录时 携带的用户名和密码的键   表单中的属性name的值
            .usernameParameter("username")
            .passwordParameter("password")
            .and()
            //注销接口
            .logout()
            .logoutUrl("/logout")
            //注销成功后跳转的页面
            .logoutSuccessUrl("/login.html")
            .permitAll()
            //删除自定义cookie
            .deleteCookies("MyCookie")
            .and()
            //需要关闭csrf防护功能，否则登录不成功
            .csrf()
            .disable();
```



## 3.设置拦截路径

上面是http.authorizeRequests().anyRequest()表示对所有路径进行拦截

```
http.authorizeRequests().antMatchers(“/xxxx).permitAll()
```

antMatchers()配置对哪些路径不进行拦截 使用permitAll()表示对之前的操作对所有的用户生效



anyRequests和authenticated为一对，表示对所有请求都拦截

```
anyRequests().authenticated()
```





## 4.WebSecurity和HttpSecurity区别

> Spring Security内部是如何加载我们自定义的登录页面的？需要了解这两个类：WebSecurity和HttpSecurity。
>
> - WebSecurity
>
> 在这个类里定义了一个securityFilterChainBuilders集合，可以同时管理多个SecurityFilterChain过滤器链，
>
> 当WebSecurity在执行时，会构建出一个名为 **”springSecurityFilterChain“** 的 **Spring BeanFilterChainProxy代理类**，它的作用是来 **定义哪些请求可以忽略安全控制，哪些请求必须接受安全控制**；以及在合适的时候 **清除SecurityContext** 以避免内存泄漏，同时也可以用来 **定义请求防火墙和请求拒绝处理器**，也可以在这里 **开启Spring Security 的Debug模式**。
>
> - HttpSecurity
>
> HttpSecurity用来构建包含一系列的过滤器链SecurityFilterChain，平常我们的配置就是围绕着这个SecurityFilterChain进行。

即先执行的是configure(WebSecurity web)方法，进行基础的配置
再执行configure(HttpSecurity http)方法，对过滤器进行具体设置



## 5.Http摘要认证(不常用)

不常用

- 概念

> HTTP摘要认证和HTTP基本认证一样，也是在RFC2616中定义的一种认证方式，它的出现是为了弥补HTTP基本认证存在的安全隐患，但该认证方式也并不是很安全。**HTTP摘要认证会使用对通信双方来说都可知的口令进行校验，且最终以密文的形式来传输数据，所以相对于基本认证来说，稍微安全了一些**。
>
> **HTTP摘要认证与基本认证类似，基于简单的“挑战-回应”模型。**当我们发起一个未经认证的请求时，服务器会返回一个401回应，并给客户端返回与验证相关的参数，期待客户端依据这些参数继续做出回应，从而完成整个验证过程。

- 摘要认证核心参数

> 服务端给客户端返回的验证相关参数如下：

```
username: 用户名。

password: 用户密码。

realm: 认证域，由服务器返回。

opaque: 透传字符串，客户端应原样返回。

method: 请求的方法。

nonce: 由服务器生成的随机字符串，包含过期时间(默认过期时间300s)和密钥。

nc: 即nonce-count,指请求的次数，用于计数，防止重放攻击。qop被指定时，nc也必须被指定。

cnonce: 客户端发给服务器的随机字符串，qop被指定时，cnonce也必须被指定。

qop: 保护级别，客户端根据此参数指定摘要算法。若取值为 auth,则只进行身份验证；若取值为auth-int，则还需要校验内容完整性，默认的qop为auth。

uri: 请求的uri。

response: 客户端根据算法算出的摘要值，这个算法取决于qop。

algorithm: 摘要算法，目前仅支持MD5。

entity-body: 页面实体，非消息实体，仅在auth-int中支持。
```

> 通常服务器端返回的数据包括realm、opaque、nonce、qop等字段，如果客户端需要做出验证回应，就必须按照一定的算法得到一些新的数据并一起返回。**在以上各种参数中，对服务器而言，最重要的字段是nonce；对客户端而言，最重要的字段是response。**

- 摘要认证的实现

```java
package com.qf.my.spring.security.demo.config;

import com.qf.my.spring.security.demo.service.MyUserDetailService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.web.authentication.www.DigestAuthenticationEntryPoint;
import org.springframework.security.web.authentication.www.DigestAuthenticationFilter;

/**
 * 摘要认证的配置
 * @author Thor
 * @公众号 Java架构栈
 */
@EnableWebSecurity
public class DigestConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private DigestAuthenticationEntryPoint digestAuthenticationEntryPoint;

    @Autowired
    private MyUserDetailService userDetailService;

    //配置认证入口端点，主要是设置认证参数信息
    @Bean
    public DigestAuthenticationEntryPoint digestAuthenticationEntryPoint(){
        DigestAuthenticationEntryPoint point = new DigestAuthenticationEntryPoint();
        point.setKey("security demo");
        point.setRealmName("thor");
        point.setNonceValiditySeconds(500);
        return point;
    }

    public DigestAuthenticationFilter digestAuthenticationFilter(){
        DigestAuthenticationFilter filter = new DigestAuthenticationFilter();
        filter.setAuthenticationEntryPoint(digestAuthenticationEntryPoint);
        filter.setUserDetailsService(userDetailService);
        return filter;
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/hello").hasAuthority("role")
                .anyRequest().authenticated()
                .and().csrf().disable()
                //当未认证时访问某些资源,则由该认证入口类来处理.
                .exceptionHandling()
                .authenticationEntryPoint(digestAuthenticationEntryPoint)
                .and()
                //添加自定义过滤器到过滤器链中
                .addFilter(digestAuthenticationFilter());

    }
}

```



# 四.自定义用户名和密码

## 1.使用application.properties

```
spring.security.user.password=1234
spring.security.user.name=ljxxx
```

若此时登录出现无法登录	即一直在login.html页面	需要为IOC容器注入PasswordEncoder接口

若我们自己配置了用户名和密码，则PasswordEncoder不会被注入到容器中，而PasswordEncoder是用于给密码加解密，实现类为BCryptPasswordEncoder类

```
@Bean
    PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
```

在配置类中返回一个BCryptPasswordEncoder对象即可

> PasswordEncoder是一个密码解析器	默认用BASE-64编码
>
> > PasswordEncoder的常见实现类BCryptPasswordEncoder



## 2.通过配置类实现设置

第一种使用configure(AuthenticationManagerBuilder auth)方法进行配置

```
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    PasswordEncoder passwordEncoder=new BCryptPasswordEncoder();
    String password = passwordEncoder.encode("1234");
    auth.inMemoryAuthentication().withUser("Ljxxx").password(password).roles("admin");
}
```

AuthenticationManagerBuilder类	用来处理身份认证，构建内存身份验证，LDAP身份验证，基于JDBC的身份验证等	所以也可以写个jdbc	在数据库中读取数据

使用PasswordEncoder接口的实现类BCryptPasswordEncoder	encode方法对密码进行加密
调用auth的inMemoryAuthentication方法的withUser把用户名写入，password写入密码



第二种

当security在properties和configure中都没有找到用户名和密码的设置，就会去找UserDetailsService接口

auth的userDetailsService方法可设置用户名密码等，参数为UserDetailsService接口的实现类

passwordEncoder方法 写入一个密码加解密器

```
@Autowired
private MyUserDetailsService myUserDetailsService;

@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {

    auth.userDetailsService(myUserDetailsService).passwordEncoder(passwordEncoder()) ;
}
```

编写MyUserDetailsService类，实现UserDetailsService接口

```
@Service
public class MyUserDetailsService implements UserDetailsService {
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("role");
        
        return new User("ljx",new BCryptPasswordEncoder().encode("1234"),auths);
    }
}
```

重写loadUserByUsername方法	用于设置用户名和密码，
返回参数为User类型(这里的User类型，不是我们写的，是security自带的类org.springframework.security.core.userdetails.User;)	
第一个参数为用户名，第二个参数为编码后的密码，第三个参数为权限，这里先随便写一个；





## 3.实现数据库认证来完成用户登录

引入依赖

```
<!--mybatis-plus-->
 <dependency>
 	<groupId>com.baomidou</groupId>
 	<artifactId>mybatis-plus-boot-starter</artifactId>
 	<version>3.0.5</version>
 </dependency>
 
 <!--mysql-->
 <dependency>
 	<groupId>mysql</groupId>
 	<artifactId>mysql-connector-java</artifactId>
 </dependency>
 
 <!--druid连接-->
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid-spring-boot-starter</artifactId>
	<version>1.1.10</version>
</dependency>
```

建立security数据库和t_user表	id,username,password字段
创建Users类和UsersMapper类

修改配置实现的第二种方法的代码

自动装配usersMapper	通过usersMapper查询数据，判断是否为空，空则抛出异常
方法loadUserByUsername(String username)的参数为我们在浏览器输入的用户名

```
@Autowired
private UsersMapper usersMapper;

@Override
public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
    QueryWrapper<Users> usersQueryWrapper = new QueryWrapper<>();
    usersQueryWrapper.eq("user_Name",username);
    Users users = usersMapper.selectOne(usersQueryWrapper);
    if (users == null){
        throw new UsernameNotFoundException("用户名不存在！！");
    }
    List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("role");
    return new User(users.getUserName(),new BCryptPasswordEncoder().encode(users.getPassword()),auths);
}
```







#  五.基于角色或权限进行访问控制



## 1.hasAuthority方法(单个权限)

如果当前的主体具有指定的权限，则返回 true,否则返回 false

```
http.authorizeRequests().antMatchers("/user").hasAnyAuthority("admin")
```

设置要访问"/user"路径，必须拥有"admin"权限	这时必须要登录！！！	无法做到不拦截

可在MyUserDetailsService类中设置

```
List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("admin");
return new User(users.getUserName(),new BCryptPasswordEncoder().encode(users.getPassword()),auths);
```

AuthorityUtils.commaSeparatedStringToAuthorityList("admin")	用于创建权限集合对象

若权限不同，则会爆403错误



## 2.hasAnyAuthority方法(多个权限)

如果当前的主体有任何提供的角色（给定的作为一个**逗号**分隔的字符串列表）的话，返回true

hasAnyAuthority()中的参数为可变长度，直接用逗号隔开即可

```
.antMatchers("/user").hasAnyAuthority("abc,adc")
```

而MyUserDetailsService类中AuthorityUtils.commaSeparatedStringToAuthorityList方法参数也为可变参数，也直接用逗号隔开即可

```
List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("abc,admins");
```



## 3.hasRole方法(单个角色)

如果用户具备给定角色就允许访问,否则出现 403。
如果当前主体具有指定的角色，则返回 true。

```
.antMatchers("/user").hasRole("sale")
```

底层代码为

![image-20230328124442998](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230328124442998.png)

会给我们加上ROLE_前缀

在MyUserDetailsService类中，commaSeparatedStringToAuthorityList方法也可以创建角色集合

```
List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("abc,admins,ROLE_sale");
```

所以加上,ROLE_sale就算加入角色



## 4.hasAnyRole方法(多个角色)

方法和hasAnyAuthority类似

```
.antMatchers("/user").hasAnyRole("sale,role")
```

```
AuthorityUtils.commaSeparatedStringToAuthorityList("abc,admins,ROLE_sale,ROLE_role");
```



## 5.自定义403页面

### 1.在配置类中配置

```
//配置没有权限访问跳转自定义页面
http.exceptionHandling().accessDeniedPage("/unauth.html");
```

这时 若静态资源下有叫unauth.html的文件，则不需要再去controller层中编写跳转方法



### 2.springboot方法(快)

在templates和static下创建error目录都会被读取	error/404.html	error/5xx.html
有精准的错误状态码页面就匹配精准，若没有则找4xx或5xx，都没有就触发白页

在static或templates创建error目录，在其下创建403.html文件即可



## 6.注解使用

### @Secured

判断是否具有角色，匹配的字符串需要添加前缀“ROLE_“

使用前要在启动类上加EnableGlobalMethodSecurity注解开启

```
@EnableGlobalMethodSecurity(securedEnabled = true)
```

在控制层中	使用@Secured注解，设置只有ROLE_sale和ROLE_manager两个角色才能访问testSecured方法
注解中，可用{}大括号写多个角色

```
@GetMapping("/test/Secured")
@Secured({"ROLE_sale","ROLE_manager"})
@ResponseBody
public String testSecured(){
    return "hello,Secured";
}
```



### @PreAuthorize

进入方法前的权限验证，可检验是否有角色或权限，即弥补Secured注解不能判断权限的不足

也要在启动类中先开启注解EnableGlobalMethodSecurity 用于开启功能

```
@EnableGlobalMethodSecurity(securedEnabled = true,prePostEnabled = true)
```

PreAuthorize()中直接写上方法，可以是hasAnyAuthority，也可以是hasAnyRole	参数用单引号！！

```
@GetMapping("/test/PreAuthorize")
@PreAuthorize("hasAnyAuthority('admin1,adc')")
@ResponseBody
public String testPreAuthorize(){
    return "hello,PreAuthorize";

}
```



### @PostAuthorize(不常用)

和PreAuthorize注解类似，只是在方法执行之后再进行权限验证，通常验证带有返回值的权限



### @PostFilter

权限验证之后对数据进行过滤

表达式中的 filterObject 引用的是方法返回值List 中的某一个元素	即默认写法
filterObject.userName=='ljx'	查询返回值中userName属性等于ljx的值，在网页中输出

这时在idea控制层会全部输出，因为postFilter是权限检验之后再过滤，而权限检验是在方法执行之后，这时已经输出了

```
@GetMapping("/test/PostFilter")
@PreAuthorize("hasAnyAuthority('admin')")
@PostFilter("filterObject.userName == 'ljx'")
@ResponseBody
public List<Users> testPostFilter(){
    ArrayList<Users> list = new ArrayList<>();
    list.add(new Users(5,"ljx","123321"));
    list.add(new Users(6,"mary","mary"));
    list.forEach(System.out::println);
    return list;
}
```



### @PreFilter

进入控制器之前对数据进行过滤	用法与PostFilter相近

注意筛选器目标必须是集合、数组、映射或流类型	不能单单是String类型或int类型

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](file:///C:\Users\ljxxx\AppData\Local\Temp\ksohtml14612\wps4.png) |

先登录，然后使用postman 进行测试

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](file:///C:\Users\ljxxx\AppData\Local\Temp\ksohtml14612\wps5.jpg) |

![img](file:///C:\Users\ljxxx\AppData\Local\Temp\ksohtml14612\wps6.jpg)![img](file:///C:\Users\ljxxx\AppData\Local\Temp\ksohtml14612\wps7.jpg)![img](file:///C:\Users\ljxxx\AppData\Local\Temp\ksohtml14612\wps8.jpg) 

![img](file:///C:\Users\ljxxx\AppData\Local\Temp\ksohtml14612\wps9.jpg)





# 六.基于数据库的记住我(好玩)

之前可通过web中的cookie技术	实现免登录
而在security中，大部分技术都帮我们封装好了

## 原理

RememberMeService中调用JdbcTokenRepositoryImpl类进行数据库的操作和token创建写入cookie操作

![image-20230328162823715](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230328162823715.png)



security框架认证成功后，会在浏览器中放入cookie，存储着加密串，并在数据库中存储加密串和用户信息字符串
再次访问时，获取cookie信息去数据库中对比，如果查询到，则认证成功直接登录

![image-20230328162936651](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230328162936651.png)





## 1.数据库中创建表

创建的表用于存储 加密串和用户信息字符串

```
CREATE TABLE `persistent_logins` (
`username` varchar(64) NOT NULL,
`series` varchar(64) NOT NULL,
`token` varchar(64) NOT NULL,
`last_used` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
PRIMARY KEY (`series`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

## 2.在配置类中配置

创建PersistentTokenRepository类型方法，PersistentTokenRepository为JdbcTokenRepositoryImpl类的父类，更安全一些

在方法中创建jdbcTokenRepository对象，调用setDataSource为其设置数据源，让其能增删改查要保存的数据
jdbcTokenRepository.setCreateTableOnStartup(true);	开启自动建表功能，若这里开启，第一步可省略

```
@Autowired
private DataSource dataSource;

@Bean
public PersistentTokenRepository persistentTokenRepository(){
    JdbcTokenRepositoryImpl jdbcTokenRepository = new JdbcTokenRepositoryImpl();
    jdbcTokenRepository.setDataSource(dataSource);
    //设置是否自动在数据库中建表，可手动建立
    jdbcTokenRepository.setCreateTableOnStartup(true);
    return jdbcTokenRepository;
}
```

在configure方法中 rememberMe()开启记住我功能，tokenRepository()把persistentTokenRepository填入
persistentTokenRepository就是作用就是创建一个token存储在cookie中
tokenValiditySeconds设置有效时间	userDetailsService给予其登录信息和加解密器

```

protected void configure(HttpSecurity http) throws Exception {
.and()
.rememberMe().tokenRepository(persistentTokenRepository())
.tokenValiditySeconds(60)//设置自动登录有效时间，默认为2周，单位为秒
.userDetailsService(myUserDetailsService)
```

## 3.在登录页设置

```
<input type="checkbox" name="remember-me"> 记住我
```

这时 复选框checkbox的name一定要为remember-me固定值，服务器才能找到



这时登录之后，把浏览器关闭 再直接访问localhost:8080/hello	能直接访问不需要登录



# 七.CSRF

## 1.CSRF 理解

跨站请求伪造（英语：Cross-site request forgery），也被称为 **one-click** **attack** 或者 **session riding**，通常缩写为 **CSRF** 或者 **XSRF**， 是一种挟制用户在当前已

登录的 Web 应用程序上执行非本意的操作的攻击方法。跟[跨网站脚本](https://baike.baidu.com/item/跨网站脚本)（XSS）相比，**XSS** 利用的是用户对指定网站的信任，CSRF 利用的是网站对用户网页浏览器的信任。

跨站请求攻击，简单地说，是攻击者通过一些技术手段欺骗用户的浏览器去访问一个自己曾经认证过的网站并运行一些操作（如发邮件，发消息，甚至财产操作如转账和购买商品）。由于浏览器曾经认证过，所以被访问的网站会认为是真正的用户操作而去运行。这利用了web 中用户身份验证的一个漏洞：简单的身份验证只能保证请求发自某个用户的浏览器，却不能保证请求本身是用户自愿发出的。

从 Spring Security 4.0 开始，默认情况下会启用CSRF 保护，以防止CSRF 攻击应用程序，

> Spring Security CSRF 会针对 PATCH，POST，PUT 和DELETE 方法进行防护。

对get请求不进行防护



## 2.原理

和基于数据库记住我相似

生成 csrfToken 保存到HttpSession 或者 Cookie 中。

请求到来时，从请求中提取 csrfToken，和保存的 csrfToken 做比较，进而判断当前请求是否合法。主要通过 CsrfFilter 过滤器来完成。



## 3.实现

在表单中加入一个隐藏域

```
<input type="hidden" th:value="${_csrf.token}"name="${_csrf.token}"/>
```

用于获取csrfToken 	用于与保存在session或cookie中的csrfToken 进行对比

不用get请求，对get不进行防护





