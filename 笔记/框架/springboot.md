笔记网址：

https://www.yuque.com/atguigu/springboot

# springboot2入门

## 1.maven设置

在\maven3\conf 下的settings.xml中加入 下载源和jdk1.8设置

```
<mirrors>
      <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>central</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
      </mirror>
  </mirrors>
 
  <profiles>
         <profile>
              <id>jdk-1.8</id>
              <activation>
                <activeByDefault>true</activeByDefault>
                <jdk>1.8</jdk>
              </activation>
              <properties>
                <maven.compiler.source>1.8</maven.compiler.source>
                <maven.compiler.target>1.8</maven.compiler.target>
                <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
              </properties>
         </profile>
  </profiles>
```



## 2.引入依赖

parent父工程，把boot引入

只需要引一个依赖，即web，表示要把项目变为web工程，通过这个依赖可把所有web要用的依赖
例如springmvc等等都引入


    <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>2.3.4.RELEASE</version>
        </parent>
        
         <dependencies>
                <dependency>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-web</artifactId>
                </dependency>
            </dependencies>


​       


3.创建主程序类

```
/**
 * 主程序类
 * @SpringBootApplication：这是一个SpringBoot应用
 */
@SpringBootApplication
public class MainApplication {
    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class,args);
    }
}
```

@SpringBootApplication注解，表示类为springboot类

在类中主方法调用SpringApplication的run方法，把主程序类和参数填入

SpringApplication.run一共做了两件事

创建SpringApplication对象；在对象初始化时保存事件监听器，容器初始化类以及判断是否为web应用，保存包含main方法的主配置类。
调用run方法；准备spring的上下文，完成容器的初始化，创建，加载等。会在不同的时机触发监听器的不同事件。

## 3.编写事务

```
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String hello(){
        return "hello,springboot";
    }
}
```

@RestController注解为 Controller ResponseBody合二为一，表示类中所有方法的返回值直接作为响应报文的响应体响应到浏览器



注意：springboot只会识别主程序类包下的文件，若放在主程序类包外，则读取不到



最后直接执行主程序方法，在浏览器中输入localhost:8080/hello可直接访问

![image-20230306183930408](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230306183930408.png)





## jar包和war包区别

jar 文件格式以流行的 zip文件格式为基础，可被编译器、jvm 等工具直接使用

war 包就是 web 项目压缩包。war 是一个可以直接运行的 web 模块，一个 war 包可以理解成就是一个 web 项目。



## 4.简化配置

在resources文件夹下创建文件application.properties（文件名固定）

可在里面修改配置

```
server.port=8888
server.address=
```

修改端口号为8888等等操作



## 5.简化部署

可直接将项目打包为jar包，使用电脑cmd直接运行，或者其他目标服务器运行

引入插件

```
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>2.3.4.RELEASE</version>
        </plugin>
    </plugins>
</build>
```

再clean和package	清除和重新打包

![image-20230306184524246](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230306184524246.png)

找到目录，使用cmd直接执行

![image-20230306184802901](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230306184802901.png)

这样也能在浏览器输入localhost:8080/hello访问



注意：***要取消cmd的快速编辑模式***





# 自动配置原理

## springboot特点

### 依赖管理

#### 默认版本号，自动版本仲裁

使用父项目作为依赖管理

```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.4.RELEASE</version>
</parent>
```

父项目的父项目为

```
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-dependencies</artifactId>
  <version>2.3.4.RELEASE</version>
</parent>
```

里面有适配的各个依赖的版本号

```
<properties>
  <activemq.version>5.15.13</activemq.version>
  <antlr2.version>2.7.7</antlr2.version>
  <appengine-sdk.version>1.9.82</appengine-sdk.version>
  <artemis.version>2.12.0</artemis.version>
  <aspectj.version>1.9.6</aspectj.version>
  <assertj.version>3.16.1</assertj.version>
```

等等

所以后面若要引入依赖，不需要写版本号，springboot会自动写入，自动版本仲裁
但引入非版本仲裁的依赖，即父项目中没有的依赖，则需要写版本号



#### 修改默认版本号

先进入父项目spring-boot-dependencies中规定的版本号的key 即<mysql.version></mysql.version>

若默认版本号为8.1.3，要改为5的版本

在当前项目中重写配置

```
<properties>
    <mysql.version>5.1.43</mysql.version>
</properties>
```



#### 开发导入starter场景启动器

```zhon
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

中spring-boot-starter-web，表示web场景

1.很多 spring-boot-starter-* ： *就某种场景

2.只要引入starter，这个场景的所有常规需要的依赖我们都自动引入

3、SpringBoot所有支持的场景
https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter

4、见到的  *-spring-boot-starter： 第三方为我们提供的简化开发的场景启动器。

5、所有场景启动器最底层的依赖

<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter</artifactId>
  <version>2.3.4.RELEASE</version>
  <scope>compile</scope>
</dependency>



### 自动配置

- 自动配好Tomcat

- 在spring-boot-starter-web中引入了tomcat

- 自动配好SpringMVC

- 在spring-boot-starter-web中引入了springmvc相关的组件依赖

- 自动配好Web常见功能，如：字符编码问题

- - SpringBoot帮我们配置好了所有web开发的常见场景

- 默认的包结构

- - 主程序所在包及其下面的所有子包里面的组件都会被默认扫描进来
  - 无需以前的包扫描配置
  - 想要改变扫描路径
  - @SpringBootApplication(scanBasePackages=**"com.atguigu"**)

- - - 或者@ComponentScan 指定扫描路径

```java
@SpringBootApplication
等同于
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan("com.atguigu.boot")
```

- 各种配置拥有默认值

- - 默认配置最终都是映射到某个类上，如：MultipartProperties类
  - 配置文件的值最终会绑定每个类上，这个类会在容器中创建对象

- 按需加载所有自动配置项

- - 非常多的starter
  - 引入了哪些场景这个场景的自动配置才会开启
  - SpringBoot所有的自动配置功能都在 spring-boot-autoconfigure 包里面
  - 

- ......

- ```
  //返回IOC容器
  ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);
  
  //查看容器里面的组件
  String[] beanDefinitionNames = run.getBeanDefinitionNames();
  for (String name:beanDefinitionNames) {
      System.out.println(name);
  }
  ```

通过SpringApplication.run方法获取IOC容器中所有的组件



## 容器功能

### 组件添加

#### 1.configuration标签

配置类标签

```
@Configuration(proxyBeanMethods=true)
public class MyConfig {

    @Bean   
    public User user1(){
        return new User("ljxxx",18,new Cat("tom"));
    }

    @Bean("mary")
    public Cat cat1(){
        return new Cat("mary");
    }
}
```

@Bean注解可代替spring配置文件中的<bean></bean>标签

给容器中添加组件。以方法名作为组件的id。返回类型就是组件类型。返回的值，就是组件在容器中的实例

也可自己取名

##### Full模式与Lite模式

使用proxyBeanMethods属性，

full模式：若proxyBeanMethods属性为true，则表示每个@Bean方法被调用多少次返回的组件都是单实例的
Lite模式：若proxyBeanMethods属性为false，则表示每个@Bean方法被调用多少次返回的组件都是新创建的

组件依赖必须要用full模式，其他默认lite模式

组件依赖即，若user对象中的cat属性想用

```
@Bean("mary")
public Cat cat1(){
    return new Cat("mary");
}
```

中创建的属性mary，则需要组件依赖，即直接调用cart1方法，这时的cat对象不是新创建的，而是在IOC容器中本身有的

```
public User user1(){
    User user =new User("ljxxx",18);
    user.setCat(cat1());
    return user;
}
```



#### 2.@Bean、@Component、@Controller、@Service、@Repository

用法和spring中类似



#### 3.@ComponentScan、@Import

```
@Import({User.class, DBHelper.class})
@Configuration
public class MyConfig {
```

import注入标签，容器中会自动创建出这两个类型的组件、默认组件的名字就是全类名

验证

```
//返回IOC容器
ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

System.out.println("=========");
String[] beanNamesForType = run.getBeanNamesForType(User.class);
for (String name:beanNamesForType) {
    System.out.println(name);
}

String[] beanNamesForType1 = run.getBeanNamesForType(DBHelper.class);
for (String name:beanNamesForType1) {
    System.out.println(name);
}
```

getBeanNamesForType通过类型获取所有bean对象的名字

得到的结果为

=========
com.ljxxx.boot.pojo.User
user1
ch.qos.logback.core.db.DBHelper

两个user对象	一个是通过import获取的，名字是全类名，第二个是public User user1(){}方法获取的



@ComponentScan()	根据规定的扫描路径，把bean装入到容器中



#### @Conditional

条件装配，满足conditional的条件，才会执行，可用名字，也可用类名等等判断

```
@ConditionalOnBean(name = "mary")
@Bean   //给容器中添加组件。以方法名作为组件的id。返回类型就是组件类型。返回的值，就是组件在容器中的实例
public User user1(){
    User user =new User("ljxxx",18);
    user.setCat(cat1());
    return user;
}

@Bean("mary")
public Cat cat1(){
    return new Cat("mary");
}
```

组件中有“mary”组件，才进行user1注册

主方法类MainApplication中

```
boolean user1 = run.containsBean("user1");
System.out.println("user1="+user1);
boolean mary = run.containsBean("mary");
System.out.println("mary="+mary);
```

containsBean为判断是否有xxx组件存在

结果为

user1=false
mary=true

因为user1的顺序在mary之前，即使有mary组件，也不能注册user1

![image-20230306214857458](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230306214857458.png)



### 原生配置文件引入

#### @ImportResource

只能引入配置文件xml

若已经有了spring配置文件 bean.xml

```
<bean id="nn" class="com.ljxxx.boot.pojo.User">
</bean>

<bean id="mm" class="com.ljxxx.boot.pojo.Cat">
</bean>
```

想要引入id为nn和mm的bean

在配置类中

```
@ImportResource("classpath:bean1.xml")
public class MyConfig {
```

写入要引入文件目录

```
boolean nn = run.containsBean("nn");
System.out.println("nn="+nn);
boolean mm = run.containsBean("mm");
System.out.println("mm="+mm);
```

结果都为true



### 配置绑定

#### @ConfigurationProperties注解

读取外部的properties文件中的内容

在application.properties文件中写

```
mycar.name=ljxxx
mycar.price=10000
```

在car类上加上ConfigurconfiguationProperties()和Component注解，
Component注解的作用是把car类放入ioc容器中，只有在容器中的组件，才会拥有SpringBoot提供的强大功能

ConfigurconProperties用于读取外部properties文件，prefix为文件内容标签名

```
@Component
@ConfigurationProperties(prefix = "mycar")
public class Car {
    private String name;
    private double price;
```

测试结果成功

```
@Autowired
private Car car;


@RequestMapping("/car")
public Car car(){
    return car;
}
```

第二种方式为

@EnableConfigurationProperties + @ConfigurationProperties

在配置类中

```
@EnableConfigurationProperties(Car.class)
public class MyConfig {
```

把Car类放入IOC容器中，这时Car类不需要再@Component，只需要@ConfigurationProperties读取properties文件

用途：用第三方jar包时，在IOC容器中需要用某些类中的某些方法，但是不能直接在类上加@Component，就要用第二种方法



## 自动配置原理源码

### 引导加载自动配置类

```
@SpringBootApplication
由@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
三个注解组合而成
```

#### 1.@SpringBootConfiguration

@Configuration，代表当前是一个配置类



#### 2.@ComponentScan

指定扫描哪些包，spring注解;



#### 3.@EnableAutoConfiguration

```
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
由两个注解组合而成
```



##### ①@AutoConfigurationPackage

```
@Import({Registrar.class})
```

通过引入Registrar.class

```
static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports {
    Registrar() {
    }

    public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
        AutoConfigurationPackages.register(registry, (String[])(new AutoConfigurationPackages.PackageImports(metadata)).getPackageNames().toArray(new String[0]));
    }

    public Set<Object> determineImports(AnnotationMetadata metadata) {
        return Collections.singleton(new AutoConfigurationPackages.PackageImports(metadata));
    }
}
```

利用Registrar给容器中导入一系列组件
将指定的一个包下的所有组件导入进来	MainApplication 所在包下。



##### ②@Import({AutoConfigurationImportSelector.class})

利用getAutoConfigurationEntry(annotationMetadata)给容器中批量导入一些组件

调用List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes)获取到所有需要导入到容器中的配置类

![image-20230307090500225](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230307090500225.png)

利用工厂加载Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) 得到所有组件

从META-INF/spring.factories位置来加载一个文件。默认扫描我们当前系统里面所有META-INF/spring.factories位置的文件
从spring-boot-autoconfigure-2.3.4.RELEASE.jar包下面的META-INF/spring.factories找到要加载的配置类

```
protected AutoConfigurationImportSelector.AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
    if (!this.isEnabled(annotationMetadata)) {
        return EMPTY_ENTRY;
    } else {
        AnnotationAttributes attributes = this.getAttributes(annotationMetadata);
        List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
        configurations = this.removeDuplicates(configurations);
        Set<String> exclusions = this.getExclusions(annotationMetadata, attributes);
        this.checkExcludedClasses(configurations, exclusions);
        configurations.removeAll(exclusions);
        configurations = this.getConfigurationClassFilter().filter(configurations);
        this.fireAutoConfigurationImportEvents(configurations, exclusions);
        return new AutoConfigurationImportSelector.AutoConfigurationEntry(configurations, exclusions);
    }
}

protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
        List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
        Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
        return configurations;
    }
```



文件里面写死了spring-boot一启动就要给容器中加载的所有配置类
spring-boot-autoconfigure-2.3.4.RELEASE.jar/META-INF/spring.factories

Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.context.LifecycleAutoConfiguration,\
org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveRepositoriesAutoConfiguration

等等127个



### 按需开启自动配置项

虽然我们127个场景的所有自动配置启动的时候默认全部加载。xxxxAutoConfiguration
按照条件装配规则（@Conditional），最终会按需配置。

```
@ConditionalOnClass({DataSource.class, EmbeddedDatabaseType.class})
```

使用@ConditionalOnClass(xxx.class)判断是否有类，有再加载



## 最佳实践

- 引入场景依赖

- - https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter

- 查看自动配置了哪些（选做）

- - 自己分析，引入场景对应的自动配置一般都生效了
  - 配置文件中debug=true开启自动配置报告。Negative（不生效）\Positive（生效）

- 是否需要修改

- - 参照文档修改配置项

- - - https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties
    - 自己分析。xxxxProperties绑定了配置文件的哪些。

- - 自定义加入或者替换组件

- - - @Bean、@Component。。。

- - 自定义器  **XXXXXCustomizer**；

# 开发技巧

## 1.lombok

***简化javabean的开发，简化日志开发***

引入依赖

springboot会自动版本仲裁

```
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
```

安装插件lombok

在idea中搜索安装settings--Plugins

### 简化javabean的开发

```
@ToString
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Cat {
    private String name;
    
}
```

@ToString注解	生成tostring方法
@Data注解	生成getset方法和tostring方法！！
@AllArgsConstructor	生成有参构造方法（所有参数）
@NoArgsConstructor	生成无参构造器

在bean中不需要再写那么多内容，简化开发



### 简化日志开发

```
@RestController
@Slf4j
public class HelloController {

    @Autowired
    private Car car;

    @RequestMapping("/car")
    public Car car(){
        log.info("请求进来了！！！");
        return car;
    }
```

这时访问 localhost/8888/car日志就会输出 请求进来了！！！



## 2.dev-tools

热更新部署

引入依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```

项目或者页面修改以后：Ctrl+F9；可自动检测是否发生修改，修改了则自动重启





## 3.Spring Initailizr（项目初始化向导）

![image-20230307130508564](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230307130508564.png)

![image-20230307130538174](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230307130538174.png)

可帮我们自动引入依赖

自动创建项目结构

自动编好主配置类

![image-20230307130813660](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230307130813660.png)

# lambda 表达式

用于匿名内部类	可以对某些接口进行简单的实现	！！！

**lambda 表达式只能实现函数式接口** **所谓函数式接口，就是指在这个接口中有且只有一个抽象方法，即只能有一个抽象方法！！！**

```
public interface NoReturnMultiParam {
    void method(int a, int b);
}

NoReturnNoParam noReturnNoParam = () -> {
            System.out.println("NoReturnNoParam");
        };
```

语法形式为 () -> {}，其中 () 用来描述参数列表，{} 用来描述方法体，-> 为 lambda运算符

方法体中只有一个语句，则不需要{} 只有一个参数，则不需要()

```
// 1. 不需要参数,返回值为 5  
() -> 5  
  
// 2. 接收一个参数(数字类型),返回其2倍的值  
x -> 2 * x  
  
// 3. 接受2个参数(数字),并返回他们的差值  
(x, y) -> x – y  
  
// 4. 接收2个int型整数,返回他们的和  
(int x, int y) -> x + y  
  
// 5. 接受一个 string 对象,并在控制台打印,不返回任何值(看起来像是返回void)  
(String s) -> System.out.print(s)
```

若要有返回值，且只有一条返回语句，也可以不用写return和大括号
若只有一个参数，则小括号(放参数列表的)可以不写





# springboot2核心技术

## 配置文件

两种配置文件  properties，yaml，且只要名字为application，就不需要主动在类上写ConfigurationPropertie读取，会自动读取，但是数据要写

### yaml

是一种标记语言	用来做以数据为中心的配置文件

### 基本语法（重）

- key: value；kv之间有空格
- 大小写敏感
- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格（在idea中可以使用tab）
- 缩进的空格数不重要，只要相同层级的元素左对齐即可
- '#'表示注释
- 字符串无需加引号，如果要加，''与""表示字符串内容 会被 转义/不转义



@ConfigurationProperties(prefix = "person")读取person开头的数据信息

```
@Data
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
    private String userName;
    private Boolean boss;
    private Date birth;
    private Integer age;
    private Pet pet;
    private String[] interests;
    private List<String> animal;
    private Map<String, Object> score;
    private Set<Double> salarys;
    private Map<String, List<Pet>> allPets;
}
```

```
@Data
public class Pet {
    private String name;
    private Double weight;
}
```

创建application.yml文件或者.yaml文件	若有.xml文件和.yml文件同时在，则会先读取.xml中的数据，若有重复，则不再读取.yml中的数据
属性与数据中间要用空格！！

```
person:
  userName: ljxxx
  boss: true
  birth: 2002/08/08
  age: 18
  pet:
    name: tom
    weight: 99
  # interests: [篮球，足球]
  interests:
    - 篮球
    - 足球
  animal: [dog,cat]
  score: {math:80,english:70}
  salarys:
    - 9999
    - 8888
  allPets:
    sick:
      - {name: tom,weight: 70}
      - {name: mary,weight: 60}
    health:
      - name: sail
        weight: 80
```

集合可用 list: [xxx,xxx]
也可以用 list:

​					- xxx

​					- xxx

集合可用 map: {key:value}
也可用 map:
						english: 80
						math: 80

若username为

```
userName: 'ljxxx \n ljx'
```

\n为转义字符，若用单引号会进行转义，把转义字符转义为普通字符串，即输出为 ljxx \n ljx

若用双引号，则不会进行转义，输出为 ljxxx
																	ljx	进行换行处理



### 配置提示

在配置自定义类和配置文件绑定时，例如上面的person和properties文件数据绑定时，没有提示

引入依赖和插件

```
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-configuration-processor</artifactId>
<optional>true</optional>
</dependency>


<build>
<plugins>
    <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
            <excludes>
                <exclude>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-configuration-processor</artifactId>
                </exclude>
            </excludes>
        </configuration>
    </plugin>
</plugins>
</build>
```

依赖是提示的依赖，插件是在打包时，不打包spring-boot-configuration-processor即提示的依赖，因为打包了也没有什么用，只有在配置的时候才用到，会拖慢打包的进度



## web开发

### 1、SpringMVC自动配置概览

Spring Boot provides auto-configuration for Spring MVC that **works well with most applications.(大多场景我们都无需自定义配置)**

The auto-configuration adds the following features on top of Spring’s defaults:

- Inclusion of `ContentNegotiatingViewResolver` and `BeanNameViewResolver` beans.

- - 内容协商视图解析器和BeanName视图解析器

- Support for serving static resources, including support for WebJars (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content))).

- - 静态资源（包括webjars）

- Automatic registration of `Converter`, `GenericConverter`, and `Formatter` beans.

- - 自动注册 `Converter，GenericConverter，Formatter `

- Support for `HttpMessageConverters` (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-message-converters)).

- - 支持 `HttpMessageConverters` （后来我们配合内容协商理解原理）

- Automatic registration of `MessageCodesResolver` (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-message-codes)).

- - 自动注册 `MessageCodesResolver` （国际化用）（通常不用）

- Static `index.html` support.

- - 静态index.html 页支持

- Custom `Favicon` support (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-favicon)).

- - 自定义 `Favicon`  

- Automatic use of a `ConfigurableWebBindingInitializer` bean (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-web-binding-initializer)).

- - 自动使用 `ConfigurableWebBindingInitializer` ，（DataBinder负责将请求数据绑定到JavaBean上）

If you want to keep those Spring Boot MVC customizations and make more [MVC customizations](https://docs.spring.io/spring/docs/5.2.9.RELEASE/spring-framework-reference/web.html#mvc) (interceptors, formatters, view controllers, and other features), you can add your own `@Configuration` class of type `WebMvcConfigurer` but **without** `@EnableWebMvc`.

**不用@EnableWebMvc注解。使用** `**@Configuration**` **+** `**WebMvcConfigurer**` **自定义规则**



If you want to provide custom instances of `RequestMappingHandlerMapping`, `RequestMappingHandlerAdapter`, or `ExceptionHandlerExceptionResolver`, and still keep the Spring Boot MVC customizations, you can declare a bean of type `WebMvcRegistrations` and use it to provide custom instances of those components.

**声明** `**WebMvcRegistrations**` **改变默认底层组件**



If you want to take complete control of Spring MVC, you can add your own `@Configuration` annotated with `@EnableWebMvc`, or alternatively add your own `@Configuration`-annotated `DelegatingWebMvcConfiguration` as described in the Javadoc of `@EnableWebMvc`.

**使用** `**@EnableWebMvc+@Configuration+DelegatingWebMvcConfiguration 全面接管SpringMVC**`



### 2.简单功能分析

#### 静态资源访问

##### 静态资源路径

只要静态资源放在类路径下： `/static` or `/public` or `/resources` or `/META-INF/resources`

访问 ： 当前项目根路径/ + 静态资源名 ，即使是`/META-INF/resources`也不用加上META-INF

相当于放在之前web项目的 /webapp下

原理： 静态映射为/**	拦截所有请求

请求进来，先去找Controller看能不能处理。不能处理的所有请求又都交给静态资源处理器。静态资源也找不到则响应404页面

我们也可以自己修改默认的静态资源路径

在application.yml中配置！！！！不能再.properties文件中

resources和mvc相同级别

```
spring:
  mvc:
    static-path-pattern: /res/**

  resources:
    static-locations: classpath:/haha/
```

即，只有在/haha 文件夹下的静态资源才能被访问

##### 静态资源访问前缀

默认是没有前缀的	直接通过 当前项目根目录/静态资源名 访问

在application.yml中配置！！！！不能再.properties文件中

```
spring:
	mvc:
		static-path-pattern: /res/**
```

加上之后，当前项目根目录/res/静态资源名



#### webjar

自动映射 /[webjars](http://localhost:8080/webjars/jquery/3.5.1/jquery.js)/**

用于帮我们自动引入**jQuery**等的jar包

引入依赖

```
<dependency>
    <groupId>org.webjars</groupId>
    <artifactId>jquery</artifactId>
    <version>3.5.1</version>
</dependency>
```

访问地址：[http://localhost:8080/webjars/**jquery/3.5.1/jquery.js**](http://localhost:8080/webjars/jquery/3.5.1/jquery.js)   后面地址要按照依赖里面的包路径

![image-20230307153921589](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230307153921589.png)



#### 欢迎页支持(welcome page)

只需要将index.html放在静态资源路径下，会默认访问index.html

这时可以配置静态资源路径，但是不能配置静态资源前缀，否则会导致不能默认访问index.html

controller可以处理 /index



#### 自定义(Favicon)

即 网页的小图标

![image-20230307190756956](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230307190756956.png)

取名为favicon.ico 放在静态资源目录下即可。

会自动读取，且也是不能配置静态资源前缀



#### 静态资源配置原理

springboot启动默认加载xxxAutoConfiguration类(自动配置类)

springMVC功能的自动配置类WebMvcAutoConfiguration生效
满足所有条件，有servlet类，有dispatcherServlet类等等

```
@Configuration(proxyBeanMethods = false)
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
@AutoConfigureAfter({ DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class,
		ValidationAutoConfiguration.class })
public class WebMvcAutoConfiguration {}
```

其中还有内部类WebMvcAutoConfigurationAdapter	给容器中配了WebMvcProperties,ResourceProperties

```
@Configuration(
    proxyBeanMethods = false
)
@Import({WebMvcAutoConfiguration.EnableWebMvcConfiguration.class})
@EnableConfigurationProperties({WebMvcProperties.class, ResourceProperties.class})
@Order(0)
public static class WebMvcAutoConfigurationAdapter implements WebMvcConfigurer {
```

```
@ConfigurationProperties(
    prefix = "spring.mvc"
)
public class WebMvcProperties {
```

```
@ConfigurationProperties(
    prefix = "spring.resources",
    ignoreUnknownFields = false
)
public class ResourceProperties {
```

即WebMvcProperties==spring.mvc，ResourceProperties==spring.resource



内部类WebMvcAutoConfigurationAdapter	只有一个构造方法

通常 配置类只有一个有参构造器的情况，所有的参数的值都会从容器中确定(从容器中拿)

```
public WebMvcAutoConfigurationAdapter(ResourceProperties resourceProperties, WebMvcProperties mvcProperties, ListableBeanFactory beanFactory, ObjectProvider<HttpMessageConverters> messageConvertersProvider, ObjectProvider<WebMvcAutoConfiguration.ResourceHandlerRegistrationCustomizer> resourceHandlerRegistrationCustomizerProvider, ObjectProvider<DispatcherServletPath> dispatcherServletPath, ObjectProvider<ServletRegistrationBean<?>> servletRegistrations) {
    
    this.resourceProperties = resourceProperties;
    this.mvcProperties = mvcProperties;
    this.beanFactory = beanFactory;
    this.messageConvertersProvider = messageConvertersProvider;
    this.resourceHandlerRegistrationCustomizer = (WebMvcAutoConfiguration.ResourceHandlerRegistrationCustomizer)resourceHandlerRegistrationCustomizerProvider.getIfAvailable();
    this.dispatcherServletPath = dispatcherServletPath;
    this.servletRegistrations = servletRegistrations;
}
```

//ResourceProperties resourceProperties；获取和spring.resources绑定的所有的值的对象
//WebMvcProperties mvcProperties 获取和spring.mvc绑定的所有的值的对象
//ListableBeanFactory beanFactory Spring的beanFactory
//HttpMessageConverters 找到所有的HttpMessageConverters
//ResourceHandlerRegistrationCustomizer 找到 资源处理器的自定义器。=========
//DispatcherServletPath  
//ServletRegistrationBean   给应用注册Servlet、Filter....



##### webjar默认处理(重)

```
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
    } else {
        Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
        CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
        if (!registry.hasMappingForPattern("/webjars/**")) {
            this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{"/webjars/**"}).addResourceLocations(new String[]{"classpath:/META-INF/resources/webjars/"}).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
        }

        String staticPathPattern = this.mvcProperties.getStaticPathPattern();
        if (!registry.hasMappingForPattern(staticPathPattern)) {
            this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{staticPathPattern}).addResourceLocations(WebMvcAutoConfiguration.getResourceLocations(this.resourceProperties.getStaticLocations())).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
        }

    }
}
```

if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
    } else {	
即如果isAddMappings的值为false，则执行日志输出，并结束语句，不执行webjar和其他静态资源处理

在application.xml中配置

```
spring.resources.add-mappings=false
```

可以控制是否处理静态资源	false表示不加载不处理，true表示处理



Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
获取了缓存策略，即我们可以在配置文件中配置缓存相关的设置

```
spring.resources.cache.period= 11000
```

配置缓存的时间11000



if (!registry.hasMappingForPattern("/webjars//**")) {

​            this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{"/webjars/**"}).addResourceLocations(new String[]{"classpath:/META-INF/resources/webjars/"}).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));



若访问的路径是/warjars/**	则映射到 classpath:/META-INF/resources/webjars/
即我们之前的[http://localhost:8080/webjars/jquery/3.5.1/jquery.js]	
其实是[http://localhost:8080//META-INF/resources/webjars/jquery/3.5.1/jquery.js]



##### 静态资源默认处理

```
String staticPathPattern = this.mvcProperties.getStaticPathPattern();
if (!registry.hasMappingForPattern(staticPathPattern)) {
    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{staticPathPattern}).addResourceLocations(WebMvcAutoConfiguration.getResourceLocations(this.resourceProperties.getStaticLocations())).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
}
```

getStaticPathPattern()获取我们在applicaiton.yml中配置的，且有默认值为 /

```
spring:
#  mvc:
#    static-path-pattern: /res/**
```

和webjar类似，访问staticPathPattern路径	都会通过getResourceLocations(this.resourceProperties.getStaticLocations())方法去找

getStaticLocations()方法为获取一个staticLocations对象，
这个对象有个默认值为：

```
private static final String[] CLASSPATH_RESOURCE_LOCATIONS = new String[]{"classpath:/META-INF/resources/", "classpath:/resources/", "classpath:/static/", "classpath:/public/"};
```



##### 欢迎页默认处理

HandlerMapping：处理器映射。保存了每一个Handler能处理哪些请求。	



```
@Bean
public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext, FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
    WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(new TemplateAvailabilityProviders(applicationContext), applicationContext, this.getWelcomePage(), this.mvcProperties.getStaticPathPattern());
    welcomePageHandlerMapping.setInterceptors(this.getInterceptors(mvcConversionService, mvcResourceUrlProvider));
    welcomePageHandlerMapping.setCorsConfigurations(this.getCorsConfigurations());
    return welcomePageHandlerMapping;
}
```

this.mvcProperties.getStaticPathPattern());同样也是获取我们配置的静态资源前缀

进入到WelcomePageHandlerMapping的构造方法中

```
WelcomePageHandlerMapping(TemplateAvailabilityProviders templateAvailabilityProviders, ApplicationContext applicationContext, Optional<Resource> welcomePage, String staticPathPattern) {
    if (welcomePage.isPresent() && "/**".equals(staticPathPattern)) {
        logger.info("Adding welcome page: " + welcomePage.get());
        this.setRootViewName("forward:index.html");
    } else if (this.welcomeTemplateExists(templateAvailabilityProviders, applicationContext)) {
        logger.info("Adding welcome page template: index");
        this.setRootViewName("index");
    }
}
```

分了两种情况	第一种"/**".equals(staticPathPattern

即静态资源前缀为默认值/**	则直接请求转发到index.html页面中

若修改了静态资源前缀，则交给controller去处理，即传过去一个逻辑视图index



### 3.请求参数处理

#### rest使用和原理

核心Filter；HiddenHttpMethodFilter

用法： 表单method=post，隐藏域  _method=put	

​			SpringBoot中手动开启

matchIfMissing = false	默认为关闭，需要我们手动开启

```
@Bean
@ConditionalOnMissingBean(HiddenHttpMethodFilter.class)
@ConditionalOnProperty(prefix = "spring.mvc.hiddenmethod.filter", name = "enabled", matchIfMissing = false)
public OrderedHiddenHttpMethodFilter hiddenHttpMethodFilter() {
    return new OrderedHiddenHttpMethodFilter();
}

protected void doFilterdoInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        HttpServletRequest requestToUse = request;
        if ("POST".equals(request.getMethod()) && request.getAttribute("javax.servlet.error.exception") == null) {
            String paramValue = request.getParameter(this.methodParam);
            if (StringUtils.hasLength(paramValue)) {
                String method = paramValue.toUpperCase(Locale.ENGLISH);
                if (ALLOWED_METHODS.contains(method)) {
                    requestToUse = new HiddenHttpMethodFilter.HttpMethodRequestWrapper(request, method);
                }
            }
        }

```

通过OrderedHiddenHttpMethodFilter类中的doFilterdoInternal方法
HttpServletRequest requestToUse = request;获取一个原生的request，如果request中的方法为post
String paramValue = request.getParameter(this.methodParam);则读取参数_method的值

String method = paramValue.toUpperCase(Locale.ENGLISH);
requestToUse = new HiddenHttpMethodFilter.HttpMethodRequestWrapper(request, method);
把request中的方法改为参数_method中的方法(改为大写形式)



Rest原理（表单提交要使用REST的时候）

- 表单提交会带上**_method=PUT**
- **请求过来被**HiddenHttpMethodFilter拦截

- - 请求是否正常，并且是POST

- - - 获取到**_method**的值。
    - 兼容以下请求；**PUT**.**DELETE**.**PATCH**
    - **原生request（post），包装模式requesWrapper重写了getMethod方法，返回的是传入的值。**
    - **过滤器链放行的时候用wrapper。以后的方法调用getMethod是调用****requesWrapper的。**

默认情况下是关闭的

在.yml中	手动开启

```
spring:
  mvc:
    hiddenmethod:
      filter:
        enabled: true
```

且rest只能用于表单提交时，其他通过客户端工具直接发送put，delete请求，则没用



##### 自定义修改默认的_method参数名(重)

新建配置类

```
@Configuration(proxyBeanMethods = false)
public class WebConfig {
    @Bean
    public HiddenHttpMethodFilter hiddenHttpMethodFilter(){
        HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
        hiddenHttpMethodFilter.setMethodParam("_m");
        return hiddenHttpMethodFilter;
    }
}
```

自己注册一个HiddenHttpMethodFilter的bean放入容器中，新建一个hiddenHttpMethodFilter，修改其methodParam的值，再返回到容器中。



#### 请求映射原理

请求映射使用spring MVC的dispatcherService	本质上还是使用了servlet中的doget或dopost方法

![image-20230308150644454](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230308150644454.png)

而doget方法在frameworkServlet类中实现，调用了processRequest方法下的doService方法

在DIspatcherSetvice类中实现了doService方法，且调用了doDIspatch方法

也就是每个请求映射都会调用doDispatch方法

```
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
		HttpServletRequest processedRequest = request;
		HandlerExecutionChain mappedHandler = null;
		boolean multipartRequestParsed = false;

		WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

		try {
			ModelAndView mv = null;
			Exception dispatchException = null;

			try {
				processedRequest = checkMultipart(request);
				multipartRequestParsed = (processedRequest != request);
				// 找到当前请求使用哪个Handler（Controller的方法）处理
				mappedHandler = getHandler(processedRequest);
```

getHandler方法，找到当前请求该用哪个Handler，即controller方法	其中有HandlerMapper属性

HandlerMapper：处理器映射

![image-20230308151223810](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230308151223810.png)

**RequestMappingHandlerMapping**：保存了所有@RequestMapping 和handler的映射规则。

![image-20230308151441594](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230308151441594.png)

所有的请求映射都会在HandlerMapping中

- SpringBoot自动配置欢迎页的 WelcomePageHandlerMapping 。访问 /能访问到index.html；
- SpringBoot自动配置了默认 的 RequestMappingHandlerMapping
- 请求进来，挨个尝试所有的HandlerMapping看是否有请求信息，从RequestMapping开始

- - 如果有就找到这个请求对应的handler
  - 如果没有就是下一个 HandlerMapping

- 我们需要一些自定义的映射处理，我们也可以自己给容器中放**HandlerMapping**。自定义 **HandlerMapping**



#### 普通参数与基本注解

注解：

##### @PathVariable、@RequestHeader、@RequestParam、、@CookieValue、

```
//<a href="/user/2/name/ljxxx?age=18&gender=man">测试PathVariable</a>
    @RequestMapping("/user/{id}/name/{username}")
    public Map<String,Object> test(@PathVariable("id")Integer id,
                                   @PathVariable("username")String username,
                                   @RequestHeader("User-Agent")String user_Agent,
                                   @RequestHeader Map<String,Object> header,
                                   @RequestParam("age")String age,
                                   String gender){
        Map<String,Object> map=new HashMap<>();
        map.put("id",id);
        map.put("username",username);
        map.put("user_Agent",user_Agent);
        map.put("header",header);
        map.put("age",age);
        map.put("gender",gender);
        return map;
    }
```

##### @RequestBody

获取请求体

使用@RequestBody接收数据的时候，前端的请求方法要使用post方式来进行提交
因为只有post才有请求体

```
<form action="/save" method="post">
    用户名：<input type="text" name="username"><br>
    密码：<input type="password" name="password"><br>
    <input value="RequestBody注解测试" type="submit">
</form>

@RequestMapping("/save")
    public Map postMethod(@RequestBody String context){
        Map<String,Object> map=new HashMap<>();
        map.put("context",context);
        return map;
    }
```

结果为{"context":"username=Jerry&password=123"}



##### @RequestAttribute

获取请求域中数据

```
@RequestMapping("/tiaozhuan")
public String tiaozhuan(HttpServletRequest request){
    request.setAttribute("username","ljxxx");
    request.setAttribute("password","1234");
    return "forward:success";
}

@ResponseBody
@RequestMapping("/success")
public Map success(HttpServletRequest request, @RequestAttribute("username") String username,@RequestAttribute("password")String password){
    Map<String,Object> map = new HashMap<>();
    Object username1 = request.getAttribute("username");
    map.put("username1",username1);
    map.put("username",username);
    map.put("password",password);
    return map;

}
```



##### @MatrixVariable(矩阵变量！！)

场景，若网页禁用了cookie，且还要获取sesison域中数据

session--->SESSIONID--->cookie，这时就要用矩阵变量，是存放在session域中的

使用占位符，在占位符中进行传输参数

```
<a href="/cars/sell;color=yellow;brand=aodi,dy;brand=hy">测试MatrixVariable</a>

    @ResponseBody
    @RequestMapping("/cars/{path}")
    public Map carsSell(@MatrixVariable("color")String color,
                        @MatrixVariable("brand")String brand,
                        @PathVariable("path")String path){
        Map<String,Object> map =new HashMap<>();

        map.put("color",color);
        map.put("brand",brand);
        map.put("path",path);
        return map;
    }
```

参数之间用分号分开，使用注解MatrixVariable(key)获取value的值

@PathVariable("path")String path	获取path的值，值为sell，不包括后面要传输的参数

SpringBoot默认禁用了矩阵变量的功能
手动开启：
对于路径的处理，都是通过UrlPathHelper进行解析，
UrlPathHelper类中的removeSemicolonContent（移除分号内容）变量支持矩阵变量的
把removeSemicolonContent的值设置为false，不移除分号内容
矩阵变量必须有url路径变量才能被解析

第一种方法 在配置类中继承WebMvcConfigurer，重写configurePathMatch方法，new一个urlPathHelper对象
setRemoveSemicolonContent设置不移除分号内容，调用configurer.setUrlPathHelper方法把新创建的对象返回

```
public class WebConfig implements WebMvcConfigurer {
@Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        UrlPathHelper urlPathHelper = new UrlPathHelper();
        urlPathHelper.setRemoveSemicolonContent(false);
        configurer.setUrlPathHelper(urlPathHelper);
    }
}
```

第二种方法 和第一种方法类似，多注册一个webMvcConfigurer对象放入IOC容器中，重写其中的configurePathMatch方法，后面和第一种方法一样

```
@Bean
public WebMvcConfigurer webMvcConfigurer(){
    WebMvcConfigurer webMvcConfigurer = new WebMvcConfigurer() {
        @Override
        public void configurePathMatch(PathMatchConfigurer configurer) {
            UrlPathHelper urlPathHelper=new UrlPathHelper();
            urlPathHelper.setRemoveSemicolonContent(false);
            configurer.setUrlPathHelper(urlPathHelper);
        }
    };
    return webMvcConfigurer;
}
```



若出现参数名相同的情况

```
<a href="/boss/1;age=10/2;age=20">测试MatrixVariable重复</a><br>
```

直接@MatrixVariable("age")，找不到是哪一个

要用@MatrixVariable的pathVar参数，确定路径的值(pathVar)

```
//<a href="/boss/1;age=10/2;age=20">测试MatrixVariable重复</a>
    @ResponseBody
    @RequestMapping("/boss/{bossId}/{empId}")
    public Map boss(@MatrixVariable(value = "age",pathVar = "bossId")String bossId,
                        @MatrixVariable(value = "age",pathVar = "empId")String empId){
        Map<String,Object> map =new HashMap<>();

        map.put("bossId",bossId);
        map.put("empId",empId);
        return map;
    }
```





#### Servlet API：

WebRequest、ServletRequest、MultipartRequest、 HttpSession、javax.servlet.http.PushBuilder、Principal、InputStream、Reader、HttpMethod、Locale、TimeZone、ZoneId



#### 复杂参数：

**Map**、**Model（map、model里面的数据会被放在request的请求域  request.setAttribute）、**Errors/BindingResult、**RedirectAttributes（ 重定向携带数据）**、**ServletResponse（response）**、SessionStatus、UriComponentsBuilder、ServletUriComponentsBuilder











### 4.视图解析与模板引擎



#### thymeleaf使用

##### 引入依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```



##### 自动配置好了thymeleaf

```
@Configuration(proxyBeanMethods = false)
@EnableConfigurationProperties({ThymeleafProperties.class})
@ConditionalOnClass({TemplateMode.class, SpringTemplateEngine.class})
@AutoConfigureAfter({WebMvcAutoConfiguration.class, WebFluxAutoConfiguration.class})
public class ThymeleafAutoConfiguration {}
```

帮我们自动配置好了

- 1、所有thymeleaf的配置值都在 ThymeleafProperties
- 2、配置好了 **SpringTemplateEngine** 视图引擎
- **3、配好了** **ThymeleafViewResolver** 视图解析器
- 4、我们只需要直接开发页面

	public static final String DEFAULT_PREFIX = "classpath:/templates/";
	
	public static final String DEFAULT_SUFFIX = ".html";  //xxx.html

已经配置好了前缀和后缀



##### 页面开发

```
@RequestMapping("/atguigu")
public String atguigu(Model model){
    model.addAttribute("msg","你好，guigu");
    model.addAttribute("link","http://www.baidu.com");
    return "success";
}

<h1 th:text="${msg}">蛤蛤</h1>
<h2>
    <a href="www.atguigu.com" th:href="${link}">去百度</a><br/>
    <a href="www.atguigu.com" th:href="@{/link}">去百度2</a>
</h2>
```







### 5.web实验后台管理基本功能

使用重定向，防止post请求表单重复提交

```
@GetMapping(value ={ "/","/login"})
public String login(){
    return "login";
}

@PostMapping("/login")
public String mainPage(User user, HttpSession session, Model model){
    if (StringUtils.hasLength(user.getUsername())&&"1234".equals(user.getPassword())){
        session.setAttribute("user",user);
        //重定向，防止表单重复提交
        return "redirect:/main.html";
    }else {
        model.addAttribute("msg", "账号或密码错误！");
        return "login";
    }
}

@GetMapping("/main.html")
public String mainreal(HttpSession session,Model model){
    Object user = session.getAttribute("user");
    if (user!=null){
        return "main";
    }else {
        model.addAttribute("msg", "请先登录！");
        return "login";
    }
}
```

输出提示信息msg
<label>标签通常和<input>标签一起使用，<label>标签为input元素定义标注（标记）
label 元素不会向用户呈现任何特殊的样式。

```
<label style="color: red" th:text="${msg}"></label>
<input type="text" name="username" class="form-control" placeholder="用户名" autofocus>
<input type="password" name="password" class="form-control" placeholder="密码">
```

使用[[${session.xxx}]]在**html**网页上直接写出session域中数据，而不是通过th:text=""

```
[[${session.user.username}]]
```

在jsp网页中 可直接${session.user.username}输出



### 6.抽取公共页面(重！！)

公共部分	使用th:fragement标签进行标记，或者使用id进行标记

```
<head th:fragment="commonhead">
    <!--common-->
    <link href="css/style.css" rel="stylesheet" th:href="@{/css/style.css}">
    <link href="css/style-responsive.css" rel="stylesheet" th:href="@{/css/style-responsive.css}">

    <!-- HTML5 shim and Respond.js IE8 support of HTML5 elements and media queries -->
    <!--[if lt IE 9]>
    <script src="js/html5shiv.js" th:src="@{/js/html5shiv.js}"></script>
    <script src="js/respond.min.js" th:src="@{/js/respond.min.js}"></script>
    <![endif]-->

</head>
<body>

<!-- left side start-->
<div id="leftmenu" class="left-side sticky-left-side"></div>
<!-- left side end-->


<!-- header section start-->
<div th:fragment="headmenu" class="header-section">
</div>
<!-- header section end-->




<div id="commonscript">
    <!-- Placed js at the end of the document so the pages load faster -->
    <script th:src="@{/js/jquery-1.10.2.min.js}"></script>
    <script th:src="@{/js/jquery-ui-1.9.2.custom.min.js}"></script>
    <script th:src="@{/js/jquery-migrate-1.2.1.min.js}"></script>
    <script th:src="@{/js/bootstrap.min.js}"></script>
    <script th:src="@{/js/modernizr.min.js}"></script>
    <script th:src="@{/js/jquery.nicescroll.js}"></script>
    <!--common scripts for all pages-->
    <script th:src="@{js/scripts.js}"></script>
</div>
```

在要引入的页面中用 th:replace和th:include和rh:insert引入

区别 insert为插入，replace为替换当前页的div标签	include为替换公共页中带有th:fragement的标签

footer为公共页面的页名！！！！！

```
<footer th:fragnent="copy">
    &copy; 2011 The Good Thynes virtual Grocery
    </footer>
```

```
<body>

<div th:inscrt="footer :: copy"></div>
<div th:replace="footer :: copy"></div>
<div th:includc="footer :: copy"></div>

</body>
```

```
<body>

<div>
    <footer>&copy;2011 The Good Thynes virtual Grocery</footer>
</div>

<footer>&copy;2011 The Good Thynes virtual Grocery< /footer></footer>
    
    <div>&copy; 2011 The Good Thynes Virtual Grocery</div>
    
</body>
```

若用id标识	则用"common :: #id"来获取公共页面

```
<div th:replace="common :: #commonscript"></div>
```

若用th:fragement标识	则用"common :: fragement值"

```
<div th:include="common :: commonhead"></div>
```



### 7.拦截器

若每个页面都要做登录检查太麻烦了
所以用拦截器

建立拦截器类

```
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session = request.getSession();
        Object user = session.getAttribute("user");
        if (user!=null){
            return true;
        }
        request.setAttribute("msg","请先登录");
        request.getRequestDispatcher("/").forward(request,response);
        return false;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
    }
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
    }
```

获取session域中的user对象，判断是否为空，不为空则返回true放行，为空，则使用请求转发，因为msg提示信息是存放在request域中的，若使用重定向则会传入一个新的request对象

config类

```
@Configuration
public class AdminWebConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor()).addPathPatterns("/**").
                excludePathPatterns("/","/login","/css/**","/fonts/**","/images/**","/js/**");

    }
}
```

web相关的配置类都要继承WebMvcConfigurer类，重写addInterceptors方法
addPathPatterns添加拦截路径/**为全部拦截	但是也会拦截静态资源
excludePathPatterns不拦截路径	把主页和静态资源填入



### 8.文件上传

form表单为post方法 且要加enctype="multipart/form-data	标识为文件上传

multiple标签为多文件上传！！

```
<div class="form-group">
    <label for="exampleInputFile">头像</label>
    <input type="file" name="headerImg" id="exampleInputFile">
</div>
<div class="form-group">
    <label for="exampleInputFile">生活照</label>
    <input type="file" name="photo" multiple>
</div>
```

#### @RequestPart

用在`multipart/form-data`表单提交请求的方法上	获取除String类型之外的参数上

```
@PostMapping("/upload")
public String upload(String email, String username,
                     @RequestPart("headerImg") MultipartFile headerImg,
                     @RequestPart("photos") MultipartFile[] photos) throws IOException {
    
    File file = new File("E:\\photo");
    String path="E:\\photo";
    if (!file.exists()){
        file.mkdir();
    }
    if (!headerImg.isEmpty()){
        headerImg.transferTo(new File(path+"\\"+headerImg.getOriginalFilename()));
    }
    for (MultipartFile photo:photos) {
        if (!photo.isEmpty()){
            photo.transferTo(new File(path+"\\"+photo.getOriginalFilename()));
        };
    }

    return "main";
}
```

获取一个file	写入文件要下载到的位置	如果不存在!file.exists()，则创建file.mkdir();
直接调用transferTo方法写入文件位置，即可完成

在properties文件中修改 单个文件的最大大小 和多文件总大小

```
spring.servlet.multipart.max-file-size=10MB
spring.servlet.multipart.max-request-size=100MB
```



### 9.异常处理

错误处理的默认规则

- 默认情况下，Spring Boot提供`/error`处理所有错误的映射

- 对于机器客户端，它将生成JSON响应，其中包含错误，HTTP状态和异常消息的详细信息。对于浏览器客户端，响应一个“ whitelabel”错误视图，以HTML格式呈现相同的数据

  json保存在request域中！！！

  ![image-20230310203049441](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230310203049441.png)



- 要对其进行自定义，添加View解析为error
- 要完全替换默认行为，可以实现 `ErrorController `并注册该类型的Bean定义，或添加`ErrorAttributes类型的组件`以使用现有机制但替换其内容。
- error/下的4xx，5xx页面会被自动解析；

- 

#### 自定义错误页

在templates或static目录下创建目录error都会被读取，error/404.html   error/5xx.html；有精确的错误状态码页面就匹配精确，没有就找 4xx.html；如果都没有就触发白页

即5xx.html代表所有5开头的错误页

也可在错误页中 th:text="${message}"/th:text="${error}"等等直接打印出错误信息！！



### 10.Web原生组件注入（Servlet、Filter、Listener）

#### 1.使用Servlet API

注 Filter过滤器和interceptor拦截器
区别直观区别是，filter在web.xml这个最根本“配置文件”中定义，是servlet定义的原生组件，而interceptor是spring定义的接口，filter是归属容器级别的，脱离了spring也能使用，interceptor归属spring范畴。

filter对请求进入容器之前就进行处理，是容器对请求进行深入包装之前就进行处理的阶段
interceptor对“容器已经对其进行了一定包装处理之后的请求”进行处理，所有要进入后续servlet(特指action)



主类	
使用@ServletComponentScan(basePackages = "boot.web.admin")扫描原生servlet组件！！

```
@ServletComponentScan(basePackages = "boot.web.admin")
@SpringBootApplication
public class AdminApplication {
```

MyServlet	使用WebServlet	Servlet为我们提供的自动装配
且用urlPatterns指定servlet处理的路径，且不会被拦截器拦截(前几节配置的拦截器)
因为原生servlet没有走spring，而之前配置的拦截器是在MVC中的dispatcherServlet设置的，所以不会被拦截

```
@WebServlet(urlPatterns = "/my")
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("MyServlet注入");
    }
}
```

WebFilter	urlPatterns指定要过滤的路径，这里配置的是静态资源	/*为servlet中的写法，也是表示css目录下所有的文件，

/**是spring中的写法

```
@Slf4j
@WebFilter(urlPatterns = {"/css/*","/images/*"})
public class MyFilter implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        log.info("filter工作");
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        log.info("filter初始化完成");
    }

    @Override
    public void destroy() {
        log.info("filter销毁");
    }
```



```
@Slf4j
@WebListener
public class MyListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        log.info("Listener初始化");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        log.info("Listener销毁");

    }
}
```



#### 2.使用RegistrationBean

ServletRegistrationBean`, `FilterRegistrationBean, ServletListenerRegistrationBean类	在配置类中配置并放入容器中

ServletRegistrationBean构造器可直接指定处理的路径

fliter有两种方式指定要过滤的路径，第一种直接把servlet()方法放入构造器，这样会处理servlet()中配置的路径
第二种调用setUrlPatterns()方法配置，参数为集合类型，所以要用Arrays.asList()写入集合

```
@Bean
public ServletRegistrationBean servlet(){
    MyServlet myServlet = new MyServlet();
    return new ServletRegistrationBean(myServlet,"/my1","/my2");
}

@Bean
public FilterRegistrationBean fliter(){
    MyFilter myFilter = new MyFilter();
    FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean(myFilter);
    filterRegistrationBean.setUrlPatterns(Arrays.asList("/my3","/my4","/my1"));
    //return new FilterRegistrationBean(myFilter,servlet());
    return filterRegistrationBean;
}

@Bean
public ServletListenerRegistrationBean myListener(){
    MyListener myListener = new MyListener();
    return new ServletListenerRegistrationBean(myListener);
}
```



### 11.嵌入式Servlet容器

#### 1.切换嵌入式Servlet容器

- 默认支持的webServer

- - `Tomcat`, `Jetty`, or `Undertow`
  - `ServletWebServerApplicationContext 容器启动寻找ServletWebServerFactory 并引导创建服务器`

- 切换服务器

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-undertow</artifactId>
</dependency>
```

exclusions标签把tomcat服务器除去，再引入undertow服务



- 原理

- - SpringBoot应用启动发现当前是Web应用。web场景包-导入tomcat
  - web应用会创建一个web版的ioc容器 `ServletWebServerApplicationContext` 
  - `ServletWebServerApplicationContext` 启动的时候寻找 `**ServletWebServerFactory**``（Servlet 的web服务器工厂---> Servlet 的web服务器）` 
  - SpringBoot底层默认有很多的WebServer工厂；`TomcatServletWebServerFactory`, `JettyServletWebServerFactory`, or `UndertowServletWebServerFactory`
  - `底层直接会有一个自动配置类。ServletWebServerFactoryAutoConfiguration`
  - `ServletWebServerFactoryAutoConfiguration导入了ServletWebServerFactoryConfiguration（配置类）`
  - `ServletWebServerFactoryConfiguration 配置类 根据动态判断系统中到底导入了那个Web服务器的包。（默认是web-starter导入tomcat包），容器中就有 TomcatServletWebServerFactory`
  - `TomcatServletWebServerFactory 创建出Tomcat服务器并启动；TomcatWebServer 的构造器拥有初始化方法initialize---this.tomcat.start();`
  - `内嵌服务器，就是手动把启动服务器的代码调用（tomcat核心jar包存在）`

#### 2.定制Servlet容器

- 实现  **WebServerFactoryCu**stomizer<ConfigurableServletWebServerFactory> 

- - 把配置文件的值和`**ServletWebServerFactory 进行绑定**`

- 修改配置文件 **server.xxx**

  在applicaiton.properties中修改	服务器的修改都在server.中

  ```
  server.undertow.accesslog.dir=/tmp
  ```

- 直接自定义 **ConfigurableServletWebServerFactory** 



xxxxxCustomizer：定制化器，可以改变xxxx的默认规则

```
import org.springframework.boot.web.server.WebServerFactoryCustomizer;
import org.springframework.boot.web.servlet.server.ConfigurableServletWebServerFactory;
import org.springframework.stereotype.Component;

@Component
public class CustomizationBean implements WebServerFactoryCustomizer<ConfigurableServletWebServerFactory> {
    @Override
    public void customize(ConfigurableServletWebServerFactory server) {
        server.setPort(9000);
    }

}
```



### 12.定制化原理

#### 定制化的常见方式 

- 修改配置文件；
- **xxxxxCustomizer；**
- **编写自定义的配置类   xxxConfiguration；+** **@Bean替换、增加容器中默认组件；视图解析器** 
  - **Web应用 编写一个配置类实现** **WebMvcConfigurer 即可定制化web功能；+ @Bean给容器中再扩展一些组件**		可直接配置视图解析器等，不需要通过@bean定制化web功能

```
@Configuration
public class AdminWebConfig implements WebMvcConfigurer
```

![image-20230313131622405](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230313131622405.png)

- @EnableWebMvc + WebMvcConfigurer —— @Bean  可以全面接管SpringMVC，所有规则全部自己重新配置； 实现定制和扩展功能

注：@EnableWebMvc注解使用后，静态资源，视图解析器，欢迎页等等都会失效，要我们手动一个一个设置

```
/**
 * 定制静态资源行为
 * @param registry
 */
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    /**
     * 访问/aa/**所有的请求都会去 classpath:/static/下进行匹配
     */
    registry.addResourceHandler("/aa/**").addResourceLocations("classpath:/static/");
}
```

访问/aa下的所有请求，aa会被替换成static，然后去匹配





## 数据访问

### SQL

数据源的自动配置-**HikariDataSource**

#### 1.导入JDBC场景

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jdbc</artifactId>
</dependency>
```

![image-20230313220327021](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230313220327021.png)

默认版本为<mysql.version>8.0.21</mysql.version>

数据库版本和驱动版本要对应

想要修改版本
1、直接依赖引入具体版本（maven的就近依赖原则）
2、重新声明版本（maven的属性的就近优先原则）

```
<properties>
    <java.version>8</java.version>
    <mysql.version>5.1.49</mysql.version>
</properties>
```

没有帮我们导入驱动，官方也不知道我们要用什么数据库



引入mysql依赖

```
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```





#### 2.分析自动配置

自动配置的类

- DataSourceAutoConfiguration ： 数据源的自动配置

- - 修改数据源相关的配置：**spring.datasource**
  - **数据库连接池的配置，是自己容器中没有DataSource才自动配置的**
    - 底层配置好的连接池是：**HikariDataSource**	默认连接池

```java
	@Configuration(proxyBeanMethods = false)
	@Conditional(PooledDataSourceCondition.class)
	@ConditionalOnMissingBean({ DataSource.class, XADataSource.class })
	@Import({ DataSourceConfiguration.Hikari.class, DataSourceConfiguration.Tomcat.class,
			DataSourceConfiguration.Dbcp2.class, DataSourceConfiguration.OracleUcp.class,
			DataSourceConfiguration.Generic.class, DataSourceJmxConfiguration.class })
	protected static class PooledDataSourceConfiguration
```



- DataSourceTransactionManagerAutoConfiguration： 事务管理器的自动配置
- JdbcTemplateAutoConfiguration： **JdbcTemplate的自动配置，可以来对数据库进行crud**

- - 可以修改这个配置项@ConfigurationProperties(prefix = **"spring.jdbc"**) 来修改JdbcTemplate
  - @Bean@Primary    JdbcTemplate；容器中有这个组件

- JndiDataSourceAutoConfiguration： jndi的自动配置
- XADataSourceAutoConfiguration： 分布式事务相关的



#### 3.修改配置项

```
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/boot
    username: root
    password: 1234
    driver-class-name: com.mysql.jdbc.Driver
```

把自身数据库信息填入



因为boot自动帮我们把JdbcTemplate组件放入容器，，所以可以使用其测试





#### 使用druid数据源

##### 自定义方式

https://github.com/alibaba/druid

引入依赖

```
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.17</version>
</dependency>
```

把数据库信息用ConfigurationProperties注解填入

```
@Bean
@ConfigurationProperties("spring.datasource")
public DataSource druid(){
    DruidDataSource druidDataSource = new DruidDataSource();
    return druidDataSource;
}

spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/ssm
    username: root
    password: 1234
```



###### 配置监控页

需要注册一个servlet	StatViewServlet
StatViewServlet的用途包括：

- 提供监控信息展示的html页面
- 提供监控信息的JSON API

内置监控页面的首页是/druid/index.html

```
@Bean
public ServletRegistrationBean statViewServlet(){
    StatViewServlet statViewServlet = new StatViewServlet();
    ServletRegistrationBean<StatViewServlet> statViewServletServletRegistrationBean =
            new ServletRegistrationBean<StatViewServlet>(statViewServlet,"/druid/*");
    return statViewServletServletRegistrationBean;

}
```

这时访问 /druid就会去监控页

![image-20230314131431521](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230314131431521.png)

若要开启sql防火墙和sql监控功能，则需要在druidDataSource中设置两个参数，就代表开启
stat监控，wall防火墙

```
DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setFilters("stat,wall");
        return druidDataSource;
```

也可在yml中配置		spring.datasource.druid.filters: stat,wall

```
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/ssm
    username: root
    password: 1234

    druid:
      filters: stat,wall
```



###### 监控web

需要注册一个filter	WebStatFilter

WebStatFilter用于采集web-jdbc关联监控的数据。

监控所有访问/*的请求，且不监控静态资源和自身页面	exclusions参数为不监控的范围

```
@Bean
public FilterRegistrationBean webStatFilter(){
    WebStatFilter webStatFilter = new WebStatFilter();
    FilterRegistrationBean<WebStatFilter> webStatFilterFilterRegistrationBean = new FilterRegistrationBean<>(webStatFilter);
    webStatFilterFilterRegistrationBean.setUrlPatterns(Arrays.asList("/*"));
    webStatFilterFilterRegistrationBean.addInitParameter("exclusions","*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*");
    return webStatFilterFilterRegistrationBean;
}
```



###### 配置监控页访问密码

需要配置Servlet的 `loginUsername` 和 `loginPassword`这两个初始参数。

```
statViewServletServletRegistrationBean.addInitParameter("loginUsername","ljxxx");
statViewServletServletRegistrationBean.addInitParameter("loginPassword","1234");
return statViewServletServletRegistrationBean;
```

参数名称固定





##### 使用官方starter方法

https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter

###### 引入依赖

```
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.17</version>
</dependency>
```

###### 分析自动配置

- 扩展配置项 **spring.datasource.druid**
- DruidSpringAopConfiguration.**class**,   监控SpringBean的；配置项：**spring.datasource.druid.aop-patterns**
- DruidStatViewServletConfiguration.**class**, 监控页的配置：**spring.datasource.druid.stat-view-servlet；默认开启**
-  DruidWebStatFilterConfiguration.**class**, web监控配置；**spring.datasource.druid.web-stat-filter；默认开启**
- DruidFilterConfiguration.**class**}) 所有Druid自己filter的配置spring.datasource.druid.filters:



在application.yml中配置

```
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/ssm
    username: root
    password: 1234

    druid:
      aop-patterns: com.atguigu.admin.*  #监控SpringBean
      filters: stat,wall		# 底层开启功能，stat（sql监控），wall（防火墙）
      stat-view-servlet:
        enabled: true
        login-username: ljxxx
        login-password:  1234
      
      web-stat-filter:		# 配置监控页功能
        enabled: true
        url-pattern: /*
        exclusions: '*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*'
	
      filter:			# 对上面filters里面的stat的详细配置
        stat:
          slow-sql-millis: 1000
          log-slow-sql: true
```



#### 整合mybatis

https://github.com/mybatis

starter

SpringBoot官方的Starter：spring-boot-starter-*

第三方的： *-spring-boot-starter

引入依赖

```
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.4</version>
</dependency>
```

##### 配置模式

- 全局配置文件
- SqlSessionFactory: 自动配置好了
- SqlSession：自动配置了 **SqlSessionTemplate 里面组合了SqlSession**
- @Import(**AutoConfiguredMapperScannerRegistrar**.**class**）；
- Mapper： 只要我们写的操作MyBatis的接口标准了 **@Mapper 就会被自动扫描进来**！！！

```java
@EnableConfigurationProperties(MybatisProperties.class) ： MyBatis配置项绑定类。
@AutoConfigureAfter({ DataSourceAutoConfiguration.class, MybatisLanguageDriverAutoConfiguration.class })
public class MybatisAutoConfiguration{}

@ConfigurationProperties(prefix = "mybatis")
public class MybatisProperties
```

可以修改配置文件中 mybatis 开始的所有；



配置mybatis规则

```
mybatis:
  config-location: classpath:mybatis/mybatis-config.xml
  mapper-locations:  classpath:mybatis/mapper/*.xml
```

指定mybatis全局配置文件位置

指定mapper文件的位置

后面的操作就和 mybaits一样
UserMapper	要加上@Mapper注解，向springboot声明他是mapper类

```
@Mapper
public interface UserMapper {

    public User getUserById(Integer id);
}
```

```
mapper namespace="boot.web.admin.mapper.UserMapper">
    <select id="getUserById" resultType="boot.web.admin.bean.User">
        select * from t_user where id=#{id}
    </select>
</mapper>
```

```@RequestMapping("/getUserById")
@Service
public class UserService {
    @Autowired
    UserMapper userMapper;

    public User getUserById(Integer id){
        User user = userMapper.getUserById(id);
        return user;
    }
}
```

```
@RequestMapping("/getUserById")
@ResponseBody
public User getUserById(@RequestParam("id")Integer id){
    User userById = userService.getUserById(id);
    return userById;
}
```

可查得数据

可在application配置文件中修改mybatis相关的配置

mybatis.configuration下面的所有，就是相当于改mybatis全局配置文件中的值

```
mybatis:
#  	config-location: classpath:mybatis/mybatis-config.xml  
	mapper-locations:  classpath:mybatis/mapper/*.xml
  	configuration:
    	map-underscore-to-camel-case: true
  	type-aliases-package: boot.web.admin
```

开启驼峰，扫描包别名 功能

注意 若要在springboot文件中配置mybatis相关的配置，则不能用	config-location: classpath:mybatis/mybatis-config.xml  	会导致冲突，不知道该选哪一个



###### 操作顺序

- 导入mybatis官方starter
- 编写mapper接口。标准@Mapper注解
- 编写sql映射文件并绑定mapper接口
- 在application.yaml中指定Mapper配置文件的位置，以及指定全局配置文件的信息 （建议；**配置在mybatis.configuration**）







##### 注解模式

```
@Mapper
public interface CityMapper {

    @Select("select * from city where id=#{id}")
    public City getCityById(int id);

    @Insert("insert into city (name,state,country) values(#{name},#{state},#{country})")
    @Options(useGeneratedKeys = true ,keyProperty = "id")
    public void insert(City city);

}
```

直接在mapper接口方法上用注解@select或@insert

@options注解为一些属性的配置，这里配置的是获取自增主键，保存到“id”中



可在主类中使用@MapperScan注解简化，在mapper接口中就不用@Mapper注解标识

```
@MapperScan("boot.web.admin.mapper")
```

可混合使用，复杂的sql就用配置模式，简单就用注解模式！！



##### **最佳实战：**

- 引入mybatis-starter
- **配置application.yaml中，指定mapper-location位置即可**
- 编写Mapper接口并标注@Mapper注解
- 简单方法直接注解方式
- 复杂方法编写mapper.xml进行绑定映射
- *@MapperScan("com.atguigu.admin.mapper") 简化，其他的接口就可以不用标注@Mapper注解*



#### 整合MyBatis-Plus

[MyBatis-Plus](https://github.com/baomidou/mybatis-plus)（简称 MP）是一个 [MyBatis](http://www.mybatis.org/mybatis-3/) 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

[mybatis plus 官网][(https://baomidou.com/)]

安装 **MybatisX** 插件 



引入依赖

```
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.1</version>
</dependency>
```

若引入了mybatis-plus的依赖，则不需要有mybatis的依赖

自动配置

- MybatisPlusAutoConfiguration 配置类，MybatisPlusProperties 配置项绑定。**mybatis-plus：xxx 就是对****mybatis-plus的定制**
- **SqlSessionFactory 自动配置好。底层是容器中默认的数据源**
- **mapperLocations 自动配置好的。有默认值。****classpath\*:/mapper/\**/\*.xml；任意包的类路径下的所有mapper文件夹下任意路径下的所有xml都是sql映射文件。  建议以后sql映射文件，放在 mapper下**
- **容器中也自动配置好了** **SqlSessionTemplate**
- **@Mapper 标注的接口也会被自动扫描；建议直接** @MapperScan(**"com.atguigu.admin.mapper"**) 批量扫描就行



**优点：**

-  只需要我们的Mapper继承 **BaseMapper** 就可以拥有crud能力！！！

继承BaseMapper类，不需要写任何方法，可直接调用父类中写好的增删改查方法，泛型要写User！！指明类型

```
@Mapper
public interface UserMapper extends BaseMapper<User> {

}
```

mybatis-plus的特点：user类中所有属性都会和数据库user表中字段对应
但是username和password是登录用的
使用TableField注解，用于对应数据库表中字段，exist=false表示没有要对应的！！！

```
@Data
public class User {
   private Long id;
   private String name;
   private Integer age;
   private String email;

   @TableField(exist = false)
   private String username;
   @TableField(exist = false)
   private String password;
}
```

若数据库中表名和类名不同，则在类上加上注解@TableName	指明确定的表名

```
@TableName("userxxx")
public class User {
```



##### CRUD功能

service也有	像mapper中类似继承BaseMapper的操作

接口继承IService<User>顶级类，是service中最高级的类，泛型为user

```
public interface UserService extends IService<User> {
}
```

实现类中 继承ServiceImpl<UserMapper,User>类，实现了IService类中所有方法，不需要我们写了，把要操作的mapper类和pojo类传入

```
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper,User> implements UserService {

}
```

这时在controller可直接调用方法	list查询所有数据，getById通过id查询

```
@Autowired
UserService userService;

@GetMapping("/dynamic_table")
public String dynamic(Model model){
    List<User> list = userService.list();
    model.addAttribute("list",list);
    return "table/dynamic_table";
}
```



##### 分页功能实现（重！！）

使用userService类中的page方法，新建page分页类，pn为当前页，2 为一页共有2条数据，放入page方法中,null条件为空

```
@GetMapping("/dynamic_table")
public String dynamic(@RequestParam(value = "pn",defaultValue = "1")Integer pn, Model model){
    Page<User> userPage = new Page<User>(pn,2);
    Page<User> page = userService.page(userPage, null);

    model.addAttribute("page",page);
    return "table/dynamic_table";
}
```

##### mybatis分页拦截器

mybatis-plus要使用分页，还要再注册分页拦截器到容器中

```
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor(){
    MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
    //设置请求的页面大于最大页后操作， true调回到首页，false继续请求默认false
    // paginationInterceptor.setOverflow(false)
    //设置最大单页限制数量，默认 500条，-1不受限制
    // paginationInterceptor.setLimit( 500 ) ;
    //开启count 的join优化，只针对部分left join

    //这是分页拦截器
    PaginationInnerInterceptor paginationInnerInterceptor = new PaginationInnerInterceptor();
    paginationInnerInterceptor.setOverflow(true);
    paginationInnerInterceptor.setMaxLimit(500L);
    mybatisPlusInterceptor.addInnerInterceptor(paginationInnerInterceptor);
    return mybatisPlusInterceptor;
}
```

${page.records}为page中存放的查询出来的数据，循环输出
${page.current}当前页码	${page.pages}]] 总页数  ${page.total}总记录数

```
<tr>
    <th>#</th>
    <th>id</th>
    <th>name</th>
    <th>age</th>
    <th>email</th>
    <th>操作</th>
</tr>
</thead>
<tbody>
<tr th:each="user,stats:${page.records}">
    <td th:text="${stats.count}"></td>
    <td th:text="${user.id}"></td>
    <td th:text="${user.name}"></td>
    <td th:text="${user.age}"></td>
    <td>[[${user.email}]]</td>
    <td>
        <a type="button" th:href="@{/user/delete/{id}(id=${user.id},pn=${page.current})}">删除</a>
    </td>
</tr>
</tfoot>
</table>
    <div class="row-fluid">
        <div class="span6">
            <div class="dataTables_info" id="hidden-table-info_info">当前第[[${page.current}]]页
                总计 [[${page.pages}]] 页 共 [[${page.total}]] 条记录</div>
        </div>
        <div class="span6">
            <div class="dataTables_paginate paging_bootstrap pagination">
                <ul>
                    <li class="active"><a th:href="@{/dynamic_table(pn=${page.current}-1)}">← Previous</a>
                            </li>
                            
                            <li th:class="${num == page.current?'active':''}" th:each="num:${#numbers.sequence(1,page.pages)}">
                                <a th:href="@{/dynamic_table(pn=${num})}">[[${num}]]</a>
                            </li>
                            
                            <li class="active">
                                <a th:href="@{/dynamic_table(pn=${page.current}+1)}">Next → </a>
                            </li>
                            
                </ul>
            </div>
        </div>
    </div>
```

<li th:class="${num == page.current?'active':''}"	th:each="num:${#numbers.sequence(1,page.pages)}">
 《a th:href="@{/dynamic_table(pn=${num})}">[[${num}]]</a> </li>

循环的条件#numbers.sequence(1,page.pages)从1，循环到page.pages！！！***新用法！！！***

跳转条件a th:href="@{/dynamic_table(pn=${num})}	括号里代表着要带的参数，好处就是可带request域中的数据！！！，即可以带着要传送的页码跳转！！！！	***新用法！！！***



##### 删除功能

使用新用法，带着当前页码pn=${page.current}，当前id=${user.id}跳转

```
<td>
    <a type="button" th:href="@{/user/delete/{id}(id=${user.id},pn=${page.current})}">删除</a>
</td>
```

接收id和pn	创建参数RedirectAttributes	用于重定向中传输参数！！！

```
@GetMapping("/user/delete/{id}")
public String deleteUser(@PathVariable("id")Integer id,
                         @RequestParam(value = "pn",defaultValue = "1")Integer pn,
                         RedirectAttributes ra){
    userService.removeById(id);
    ra.addAttribute("pn",pn);
    return "redirect:/dynamic_table";
}
```



### NoSQL

Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、**缓存**和消息中间件。 它支持多种类型的数据结构，如 [字符串（strings）](http://www.redis.cn/topics/data-types-intro.html#strings)， [散列（hashes）](http://www.redis.cn/topics/data-types-intro.html#hashes)， [列表（lists）](http://www.redis.cn/topics/data-types-intro.html#lists)， [集合（sets）](http://www.redis.cn/topics/data-types-intro.html#sets)， [有序集合（sorted sets）](http://www.redis.cn/topics/data-types-intro.html#sorted-sets) 与范围查询， [bitmaps](http://www.redis.cn/topics/data-types-intro.html#bitmaps)， [hyperloglogs](http://www.redis.cn/topics/data-types-intro.html#hyperloglogs) 和 [地理空间（geospatial）](http://www.redis.cn/commands/geoadd.html) 索引半径查询。 Redis 内置了 [复制（replication）](http://www.redis.cn/topics/replication.html)，[LUA脚本（Lua scripting）](http://www.redis.cn/commands/eval.html)， [LRU驱动事件（LRU eviction）](http://www.redis.cn/topics/lru-cache.html)，[事务（transactions）](http://www.redis.cn/topics/transactions.html) 和不同级别的 [磁盘持久化（persistence）](http://www.redis.cn/topics/persistence.html)， 并通过 [Redis哨兵（Sentinel）](http://www.redis.cn/topics/sentinel.html)和自动 [分区（Cluster）](http://www.redis.cn/topics/cluster-tutorial.html)提供高可用性（high availability）。

#### redis自动配置

引入依赖

```
<dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-data-redis</artifactId>
    </dependency>
```

自动配置：

- RedisAutoConfiguration 自动配置类。RedisProperties 属性类 --> **spring.redis.xxx是对redis的配置**
- 连接工厂是准备好的。**Lettuce**ConnectionConfiguration、**Jedis**ConnectionConfiguration
- **自动注入了RedisTemplate**<**Object**, **Object**> ： xxxTemplate；
- **自动注入了StringRedisTemplate；k：v都是String**
- **key：value**
- **底层只要我们使用** **StringRedisTemplate、**RedisTemplate就可以操作redis





**redis环境搭建**

在阿里云官网中购买

**1、阿里云按量付费redis。经典网络**

**2、申请redis的公网连接地址**

**3、修改白名单  允许0.0.0.0/0 访问**

[https://www.aliyun.com/?utm_content=se_1013083955]: 



在yml中配置密码和公网连接地址
host为公网连接地址

```
redis:
    host: r-bp1y1ke9m9cp2xw3qmpd.redis.rds.aliyuncs.com
    port: 6379
    password: ljx:1390691258Ljx
#    url: redis://ljx:1390691258Ljx@r-bp1y1ke9m9cp2xw3qmpd.redis.rds.aliyuncs.com:6379
```

测试类中自动装配StringRedisTemplate类的redisTemplate，用于操控redis数据库

set方法放入数据	key和value都为String类型

```
@Autowired
StringRedisTemplate redisTemplate;

@Test
void testRedis(){
    ValueOperations<String, String> stringStringValueOperations = redisTemplate.opsForValue();
    stringStringValueOperations.set("hello","world");
    String hello = stringStringValueOperations.get("hello");
    log.info(hello);
}
```

数据库中显示为：

![image-20230315090731194](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230315090731194.png)



#### Lettuce和Jedis 

底层默认使用服务器为Lettuce	像mysql的连接池	druid和HikariDataSource

要切换到Jedis 

```
<!--导入Jedis-->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>

 redis:
    host: r-bp1y1ke9m9cp2xw3qmpd.redis.rds.aliyuncs.com
    port: 6379
    password: ljx:1390691258Ljx
    client-type: jedis
```

使用client-type	声明服务器类型





## 单元测试

### JUnit5 的变化

**JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage**

**JUnit Platform**: Junit Platform是在JVM上启动测试框架的基础，不仅支持Junit自制的测试引擎，其他测试引擎也都可以接入。

**JUnit Jupiter**: JUnit Jupiter提供了JUnit5的新的编程模型，是JUnit5新特性的核心。内部 包含了一个**测试引擎**，用于在Junit Platform上运行。

**JUnit Vintage**: 由于JUint已经发展多年，为了照顾老的项目，JUnit Vintage提供了兼容JUnit4.x,Junit3.x的测试引擎。

注意：
springBoot2.4以上的版本移除了对Vintage的依赖，如果还需要兼容junit4，则需要自行引入

```
<dependency>
    <groupId>org.junit.vintage</groupId>
    <artifactId>junit-vintage-engine</artifactId>
    <scope>test</scope>
    <exclusions>
        <exclusion>
            <groupId>org.hamcrest</groupId>
            <artifactId>hamcrest-core</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```



要使用Junit5	引入依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

使用@SpringBootTest注解标注	只有加上之后，才能用spring的功能，比如拿取容器中的组件等等

```
@SpringBootTest
class Boot05WebAdminApplicationTests {
    @Test
    void contextLoads() {
    }
}
```

SpringBoot整合Junit之后的功能：

- 编写测试方法：@Test标注（注意需要使用junit5版本的注解	org.junit.jupiter.api.Test）
- Junit类具有Spring的功能，@Autowired、比如 @Transactional 标注测试方法，测试完成后自动回滚

![image-20230315133159842](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230315133159842.png)



### JUnit5 常用注解

JUnit5的注解与JUnit4的注解有所变化

https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations

- **@Test :**表示方法是测试方法。但是与JUnit4的@Test不同，他的职责非常单一不能声明任何属性，拓展的测试将会由Jupiter提供额外测试

- **@ParameterizedTest :**表示方法是参数化测试，下方会有详细介绍

- **@RepeatedTest :**表示方法可重复执行，下方会有详细介绍

- **@DisplayName :**为测试类或者测试方法设置展示名称

- **@BeforeEach :**表示在每个单元测试之前执行

- **@AfterEach :**表示在每个单元测试之后执行

- **@BeforeAll :**表示在所有单元测试之前执行

- **@AfterAll :**表示在所有单元测试之后执行

- **@Tag :**表示单元测试类别，类似于JUnit4中的@Categories

- **@Disabled :**表示测试类或测试方法不执行，类似于JUnit4中的@Ignore

- **@Timeout :**表示测试方法运行如果超过了指定时间将会返回错误      value为值 unit为单位

- **@ExtendWith :**为测试类或测试方法提供扩展类引用

  - @SpringBootTest注解中	

    ```
    @BootstrapWith(SpringBootTestContextBootstrapper.class)
    @ExtendWith({SpringExtension.class})
    ```

    就有用到ExtendWith引入新的类

此时没有使用@SpringBootTest，则不能使用@AutoWind自动装配，拿不到容器中的组件

```
@DisplayName("Junit5测试类")
public class Junit5Test {

    @Test
    @DisplayName("测试DisplayName注解")	//设置展示名称
    void testDisplayName(){
        System.out.println(1);
    }

    @Test
    @Disabled		//该测试方法不执行
    @DisplayName("test1")
    void test1(){
        System.out.println(2);
    }

    @BeforeEach		//测试方法开始前执行
    void testBeforeEach(){
        System.out.println("测试开始！！");
    }

    @AfterEach		//测试方法开始后执行
    void testAfterEach(){
        System.out.println("测试结束！！");
    }
    
    @BeforeAll		//测试类开始前执行
    static void testBeforeAll(){
        System.out.println("总测试开始");
    }
    
    @Timeout(value = 500,unit = TimeUnit.MILLISECONDS)
	//设置超时时间，若超时500毫秒，则不执行	MILLISECONDS毫秒
    @Test
    void testTimeout() throws InterruptedException {
        Thread.sleep(600);
    }
    
    @AfterAll		//测试方法开始后执行
    static void testAfterAll(){
        System.out.println("总测试结束");
    }
    
    @RepeatedTest(5)		//测试方法执行5次
    @Test
    void testRepeatedTest(){
        System.out.println(5);
    }
}
```





### 断言（Assertions类 重要）

断言（assertions）是测试方法中的核心部分，用来对测试需要满足的条件进行验证。**这些断言方法都是 org.junit.jupiter.api.Assertions 的静态方法**。JUnit 5 内置的断言可以分成如下几个类别：

**检查业务逻辑返回的数据是否合理。**

**所有的测试运行结束以后，会有一个详细的测试报告；**

#### 1.简单断言

用来对单个值进行简单的验证.

| 方法            | 说明                                 |
| --------------- | ------------------------------------ |
| assertEquals    | 判断两个对象或两个原始类型是否相等   |
| assertNotEquals | 判断两个对象或两个原始类型是否不相等 |
| assertSame      | 判断两个对象引用是否指向同一个对象   |
| assertNotSame   | 判断两个对象引用是否指向不同的对象   |
| assertTrue      | 判断给定的布尔值是否为 true          |
| assertFalse     | 判断给定的布尔值是否为 false         |
| assertNull      | 判断给定的对象引用是否为 null        |
| assertNotNull   | 判断给定的对象引用是否不为 null      |

assertEquals()第一个参数为预期值，第二个参数为实际值，第三个参数为错误显示的信息

```
@Test
@DisplayName("测试简单断言")
void testSimpleAssertions(){
    int cal = cal(3, 2);
    assertEquals(5,cal,"业务计算错误");
    Object o1 = new Object();
    Object o2 = new Object();
    assertSame(o1,o2,"两个对象不一样");
}

int cal(int i,int j){
    return i+j;
}
```

错误信息

```
org.opentest4j.AssertionFailedError: 两个对象不一样 ==> 
Expected :java.lang.Object@6e38921c
Actual   :java.lang.Object@64d7f7e0
```



#### 2.数组断言

使用assertArrayEquals

```
@Test
@DisplayName("测试数组断言")
public void array() {
    assertArrayEquals(new int[]{1, 2}, new int[] {1, 2});
}
```



#### 3.组合断言(lambda表达式)

使用assertAll

assertAll 方法接受多个 org.junit.jupiter.api.Executable 函数式接口的实例作为要验证的断言，可以通过 lambda 表达式很容易的提供这些断言	接收多个接口用于实现多个断言

```
@Test
@DisplayName("组合断言")
public void all() {
    assertAll("Math",
            () -> assertEquals(2, 1 + 1),
            () -> assertTrue(1 > 0)
    );
}
```



#### 4.异常断言

使用assertThrows

断定业务一定会出现某种异常，若没有异常则才会报错	ArithmeticException算数异常
10/2	没有算数异常，所以断言没通过，输出"业务居然通过了"
若改为10/0	有算数异常，断言通过，则不输出

```
@Test
@DisplayName("异常测试")
public void exceptionTest(){
    //断定业务一定会出现某种异常 ArithmeticException算数异常
    assertThrows(ArithmeticException.class,()->{int i=10/2;},"业务居然通过了");

}
```



#### 5.超时断言(Duration类)

使用assertTimeout

```
@Test
@DisplayName("超时测试")
public void timeoutTest(){
    assertTimeout(Duration.ofMillis(1000),()->{Thread.sleep(1500);},"超时");
}
```

Duration.ofMillis() 方法用于表示此 Duration 中的给定毫秒数。

Duration.ofMillis() 方法超过给定毫秒时间，会抛出异常。



#### 6.快速失败

```
@Test
@DisplayName("快速失败")
public void shouldFail(){
    if (2==2) {
        fail("测试失败");
    }
}
```





项目做完之后，直接运行项目的clean和test	可得到一份完整的test报告

![image-20230315144837847](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230315144837847.png)



### 前置条件（Assumptions类）

JUnit 5 中的前置条件（**assumptions【假设】**）类似于断言，不同之处在于**不满足的断言会使得测试方法失败**，而不满足的**前置条件只会使得测试方法的执行终止**。

！！前置条件可以看成是测试方法执行的前提，当该前提不满足时，就没有继续执行的必要。

```
@DisplayName("前置条件")
@Test
void testassumptions(){
    Assumptions.assumeTrue(true,"结果不是true");
    System.out.println("11111");
}
```

只有满足了Assumptions.assumeTrue(true,"结果不是true");	即第一个值为true，才会执行下面的输出语句

也可以用Assumptions.assumeFalse();	只有等于false，才会执行下面的输出语句



### 嵌套测试

JUnit 5 可以通过 Java 中的内部类和@Nested 注解实现嵌套测试，从而可以更好的把相关的测试方法组织在一起。在内部类中可以使用@BeforeEach 和@AfterEach 注解，而且嵌套的层次没有限制。

且外层的Test不能驱动内层的BeforeEach等方法
但是内部的Test可以驱动外层的BeforeEach等方法

```
@DisplayName("A stack")
class TestingAStackDemo {

    Stack<Object> stack;

    @Test
    @DisplayName("is instantiated with new Stack()")
    void isInstantiatedWithNew() {
        new Stack<>();
    }

    @Nested
    @DisplayName("when new")
    class WhenNew {

        @BeforeEach
        void createNewStack() {
            stack = new Stack<>();
        }

        @Test
        @DisplayName("is empty")
        void isEmpty() {
            assertTrue(stack.isEmpty());
        }

        @Test
        @DisplayName("throws EmptyStackException when popped")
        void throwsExceptionWhenPopped() {
            assertThrows(EmptyStackException.class, stack::pop);
        }

        @Test
        @DisplayName("throws EmptyStackException when peeked")
        void throwsExceptionWhenPeeked() {
            assertThrows(EmptyStackException.class, stack::peek);
        }

        @Nested
        @DisplayName("after pushing an element")
        class AfterPushing {

            String anElement = "an element";

            @BeforeEach
            void pushAnElement() {
                stack.push(anElement);
            }

            @Test
            @DisplayName("it is no longer empty")
            void isNotEmpty() {
                assertFalse(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when popped and is empty")
            void returnElementWhenPopped() {
                assertEquals(anElement, stack.pop());
                assertTrue(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when peeked but remains not empty")
            void returnElementWhenPeeked() {
                assertEquals(anElement, stack.peek());
                assertFalse(stack.isEmpty());
            }
        }
    }
}
```





### 参数化测试

使用@ParameterizedTest注解标注，表示为参数化测试

参数化测试可以使用不同的参数机械能多次单元测试	

利用**@ValueSource**等注解，指定入参，我们将可以使用不同的参数进行多次单元测试，而不需要每新增一个参数就新增一个单元测试，省去了很多冗余代码。

**@ValueSource**: 为参数化测试指定入参来源，支持八大基础类以及String类型,Class类型

**@NullSource**: 表示为参数化测试提供一个null的入参

**@EnumSource**: 表示为参数化测试提供一个枚举入参

**@CsvFileSource**：表示读取指定CSV文件内容作为参数化测试入参

**@MethodSource**：表示读取指定方法的返回值作为参数化测试入参(注意方法返回需要是一个流)

```
@ParameterizedTest
@ValueSource(strings = {"one", "two", "three"})
@DisplayName("参数化测试1")
public void parameterizedTest1(String string) {
    System.out.println(string);
    Assertions.assertTrue(StringUtils.isNotBlank(string));
}

@ParameterizedTest
@MethodSource("method")    //指定方法名
@DisplayName("方法来源参数")
public void testWithExplicitLocalMethodSource(String name) {
    System.out.println(name);
    Assertions.assertNotNull(name);
}

static Stream<String> method() {
    return Stream.of("apple", "banana");
}
```

点击一次开始测试，会测试"one", "two", "three"三次

![image-20230315151705126](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230315151705126.png)





## 指标监控

### SpringBoot Actuator

未来每一个微服务在云上部署以后，我们都需要对其进行监控、追踪、审计、控制等。SpringBoot就抽取了Actuator场景，使得我们每个微服务快速引用即可获得生产级别的应用监控、审计等功能。

#### 引入依赖

```
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```

#### 使用

- 引入场景
- 访问 http://localhost:8080/actuator/**
- 暴露所有监控信息为HTTP



在yml中配置

```
management:
  endpoints:
    enabled-by-default: true #暴露所有端点信息
    web:
      exposure:
        include: '*'  #以web方式暴露
```

端点其实就是一个网页，若开启了health端点，则可以访问http://localhost:8080/actuator/health，查看是否健康

默认情况下是JMX	java自带的一种监控模式，端点全部开启
我们要用web，即http，则需要手动开启



***JMX访问监控方式***

打开cmd	输入jconsole	（java自带的监控）	选择要监控的端口号



### Actuator Endpoint端点

#### 最常使用的端点

| ID                 | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| `auditevents`      | 暴露当前应用程序的审核事件信息。需要一个`AuditEventRepository组件`。 |
| `beans`            | 显示应用程序中所有Spring Bean的完整列表。                    |
| `caches`           | 暴露可用的缓存。                                             |
| `conditions`       | 显示自动配置的所有条件信息，包括匹配或不匹配的原因。         |
| `configprops`      | 显示所有`@ConfigurationProperties`。                         |
| `env`              | 暴露Spring的属性`ConfigurableEnvironment`                    |
| `flyway`           | 显示已应用的所有Flyway数据库迁移。 需要一个或多个`Flyway`组件。 |
| `health`           | 显示应用程序运行状况信息。                                   |
| `httptrace`        | 显示HTTP跟踪信息（默认情况下，最近100个HTTP请求-响应）。需要一个`HttpTraceRepository`组件。 |
| `info`             | 显示应用程序信息。                                           |
| `integrationgraph` | 显示Spring `integrationgraph` 。需要依赖`spring-integration-core`。 |
| `loggers`          | 显示和修改应用程序中日志的配置。                             |
| `liquibase`        | 显示已应用的所有Liquibase数据库迁移。需要一个或多个`Liquibase`组件。 |
| `metrics`          | 显示当前应用程序的“指标”信息。                               |
| `mappings`         | 显示所有`@RequestMapping`路径列表。                          |
| `scheduledtasks`   | 显示应用程序中的计划任务。                                   |
| `sessions`         | 允许从Spring Session支持的会话存储中检索和删除用户会话。需要使用Spring Session的基于Servlet的Web应用程序。 |
| `shutdown`         | 使应用程序正常关闭。默认禁用。                               |
| `startup`          | 显示由`ApplicationStartup`收集的启动步骤数据。需要使用`SpringApplication`进行配置`BufferingApplicationStartup`。 |
| `threaddump`       | 执行线程转储。                                               |



最常用的Endpoint

- **Health：监控状况**
- **Metrics：运行时指标**
- **Loggers：日志记录**



#### Health Endpoint

健康检查端点，我们一般用于在云平台，平台会定时的检查应用的健康状况，我们就需要Health Endpoint可以为平台返回当前应用的一系列组件健康状况的集合。

重要的几点：

- health endpoint返回的结果，应该是一系列健康检查后的一个汇总报告
- 很多的健康检查默认已经自动配置好了，比如：数据库、redis等
- 可以很容易的添加自定义的健康检查机制

若检查中一个不健康，例如数据库连接出现错误，则返回down，都健康，则返回up

在yml配置文件中	把health具体的信息显示	management.endpoint.health.show-details：always设置！！

```
management:
  endpoints:
    enabled-by-default: true #暴露所有端点信息
    web:
      exposure:
        include: '*'  #以web方式暴露
  endpoint:
    health:
      show-details: always
```

![image-20230315194002525](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230315194002525.png)



#### Metrics Endpoint

提供详细的、层级的、空间指标信息，这些信息可以被pull（主动推送）或者push（被动获取）方式得到；

- 通过Metrics对接多种监控系统
- 简化核心Metrics开发
- 添加自定义Metrics或者扩展已有Metrics

![image-20230315194553925](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230315194553925.png)

页面中有很多信息，可通过http://localhost:8080/actuator/metrics/xxx 进行直接访问
例如http://localhost:8080/actuator/metrics/jvm.gc.pause



#### 管理Endpoints

```
management:
  endpoints:
    enabled-by-default: true #暴露所有端点信息
    web:
      exposure:
        include: '*'  #以web方式暴露
```

配置中把所有的端点都打开，十分危险，通常是选择开启几个
使用	management:endpoint:beans:enabled: true	开启

```
management:
	endpoint:
  		health:
    		show-details: always
    		enabled: true
  		info:
    		enabled: true
  		beans:
   		 	enabled: true
 		metrics:
    		enabled: true
```

开启常用的 info，beans，metrics，health

这时不管使用JMX还是web都不能全部访问端点





#### 定制 Endpoint

##### 1.定制 Health 信息

自定义HealthIndicato类	名字一定要以HealthIndicato结尾，前面My就是浏览器显示的名字
继承AbstractHealthIndicator类！！！！	实现doHealthCheck方法

且只需要放入容器中就能生效，不需要配置类做什么

```
@Component
public class MyHealthIndicator extends AbstractHealthIndicator {
    @Override
    protected void doHealthCheck(Health.Builder builder) throws Exception {
        HashMap<String, Object> map = new HashMap<>();
        if (1==1){
            builder.up();
            map.put("count",1);
            map.put("ms",1000);
        }else {
            builder.status(Status.DOWN);
            map.put("err", "连接超时");
            map.put("ms", 3000);
        }
        builder.withDetail("code",100).withDetails(map);
    }
```

状态设置可用两种方式builder.up();	本质就是去设置变量status的值为up
所以可以直接设置status的值	builder.status(Status.DOWN);	为DOWN

使用builder.withDetail方法可传递信息，把信息放入map中，再传递
或者直接withDetail("code",100)	传递key=code value=100	

访问	http://localhost:8080/actuator/health	结果为：

![image-20230315204016061](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230315204016061.png)



##### 2.定制info信息

直接在yml配置文件中编写

注意：此时的info不是在	management:endpoint:下的，而是独立的

```
info:
  appName: boot-admin
  appVersion: 1.0.0
  mavenProjectName: @project.artifactId@
  mavenVersion: @project.version@
```

设置app版本为1.0.0，但又是版本号不是写死的，而是在pom文件中

可用@xxx@	调用pom中文件信息，	project为第一个父标签，artifactId为项目名字的标签



或者自定义类	这时类名不需要像health一样有规定，随便写就行
实现InfoContributor类，放入容器，把要传输的信息写入	用withDetail方法传输

```
@Component
public class AppInfoContributor implements InfoContributor {
    @Override
    public void contribute(Info.Builder builder) {
        builder.withDetail("hello","您好").withDetail("world",1234);
    }
```

![image-20230315205632433](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230315205632433.png)



##### 3.定制Metrics

使用我们之前的cityService类	只要是任何一个类都行

写一个构造方法	因为构造方法会在类使用时先被调用
传入参数meterRegistry	meterRegistry.counter	
设置一个计数的metrics数据名称为	myservice.getCityById.counter

```
public class CityService {
Counter counter;
public CityService(MeterRegistry meterRegistry){
    counter = meterRegistry.counter("myservice.getCityById.counter");

}

public void saveCity(City city) {
        counter.increment();
        cityMapper.insert(city);
    }
```

在要计数的方法中把调用counter.increment()	自增加一方法，记录方法被调用的次数

再去执行save方法	(saveCity之前写的要用postman软件去发送post请求)

访问http://localhost:8080/actuator/metrics/xxx	我们自定义的数据名称myservice.getCityById.counter

![image-20230315211428891](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230315211428891.png)





##### 4.定制Endpoint

只需要使用Endpoint注解	使用id属性设置端点的名称	再放入容器中即可

用@ReadOperation注解，标注一个方法为读方法，再把要传入的信息返回即可

用@WriteOperation注解，标注一个方法为写方法

```
@Component
@Endpoint(id = "myService")
public class MyServiceEndPoint {

    @ReadOperation
    public Map getStudent(){
        HashMap<String , Object> map = new HashMap<>();
        map.put("studentInfo","student start....");
        return map;
    }

    @WriteOperation
    public void stopStudent(){
        System.out.println("student stop.....");
    }
}
```

![image-20230315212632232](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230315212632232.png)



###  可视化监控

新建一个项目	作为服务器

引入依赖

```
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-server</artifactId>
    <version>2.3.1</version>
</dependency>
```

修改端口号，不要和本机的客户端的端口号相同

```
server.port=8888
```

并在主方法中加入@EnableAdminServer注解	启动即可

```
@EnableAdminServer
@SpringBootApplication
public class BootAdminserverApplication {
```





在客户端中

引入依赖

```
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-client</artifactId>
    <version>2.3.1</version>
</dependency>
```

在yml中修改配置信息

```
spring:
	boot:
  		admin:
    		client:
      			url: http://localhost:8888
      			instance:
        			prefer-ip: true   #使用ip注册
	application:
    	name: boot_web_admin  #修改在服务器中显示的名称
```

默认情况下 会使用spring-boot-application作为服务器中显示的名称

且会使用本机名	即Deskepxxxx进行解析	http://Deskepxxx:8080	我们需要用ip	即localhost进行解析





这时访问服务器	localhost:8888

![image-20230315215151881](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230315215151881.png)

可看到各种信息，应用墙中可看到更多信息



## 原理解析

### Profile功能

为了方便多环境适配，springboot简化了profile功能。

通常会有多个配置文件yml	对于同一个属性有不同的值，有的是test中用的，有的是prod生产时用的

若要一个一个改太麻烦了

#### 1、application-profile功能

- 默认配置文件  application.yaml；任何时候都会加载
- 指定环境配置文件  application-{env}.yaml
- 激活指定环境

- - 配置文件激活
  - 命令行激活：java -jar xxx.jar --**spring.profiles.active=prod  --person.name=haha**

- - - **修改配置文件的任意值，命令行优先**

- 默认配置与环境配置同时生效
- 同名配置项，profile配置优先



controller类

@Value("${person.name:李四}")	${}读取application配置文件的数据
 :李四表示，默认值为李四

```
@RestController
public class HelloController {


    @Value("${person.name:李四}")
    private String name;

    @GetMapping("/")
    public String hello(){
        return "hello "+name;
    }
}
```

application.properties为空

application-prod.yml	-的后缀就是环境名称

```
person:
  name: prod-张三
```

application-test.yml	-的后缀就是环境名称

```
person:
  name: test-张三
```

这时有三种环境，第一种自身，第二种test，第三种prod

要切换为name赋值，可application本身环境的配置文件中

```
spring.profiles.active=test	#标识为test环境
```

可选哪种环境为name赋值

因为配置文件application.properties任何时候都会加载
有相同配置项，指定环境的配置文件会覆盖默认配置文件





#### 2、@Profile条件装配功能

使用@Profile("xxx") 只有满足xxx环境时，才会加载

定义person接口

```
public interface Person {

    String getName();
    Integer getAge();
}
```

创建boss和work	实现person	@Profile("prod")只有prod环境下，才会加载类

```
@Profile("prod")
@Component
@ConfigurationProperties("person")
@Data
public class Boss implements Person{

    private String name;
    private Integer age;
}
```

```
@Profile("test")
@ConfigurationProperties("person")
@Component
@Data
public class Work implements Person{

    private String name;
    private Integer age;
}
```

在controller中	可以设置prod环境下 加载的是boss类，test环境下	加载的是work类

```
@Autowired
private Person person;

@GetMapping("person")
public String person(){
    return person.getClass().toString();
}
```



@Profile()也可以放在方法上 不是只能用在类上



#### 3、profile分组

```
spring.profiles.active=myprod

spring.profiles.group.myprod[0]=prod1
spring.profiles.group.myprod[1]=prod2

spring.profiles.group.mytest[0]=test
```

使用spring.profiles.group.xxx进行分组，分成myprod和mytest两组
创建配置文件 application-prod1和application-prod2	可同时读取，进行互补





### 外部化配置

#### 1、外部配置源

常用：**Java属性文件**、**YAML文件**、**环境变量**、**命令行参数**；

```
@Value("${MAVEN_HOME}")
private String msg;

@Value("${os.name}")
    private String osName;

@GetMapping("/maven")
public String maven(){
    return msg+"==>"+osName;
}
```

Value(${xxx})	查看系统的环境变量，也可直接获取属性文件中的数据
os.name为获取当前操作系统名称Window 10

在主程序中	可用run.getEnvironment()方法获取系统环境相关的信息

```
public static void main(String[] args) {
    ConfigurableApplicationContext run = SpringApplication.run(BootProfileApplication.class, args);
    ConfigurableEnvironment environment = run.getEnvironment();
    Map<String, Object> systemEnvironment = environment.getSystemEnvironment();
    MutablePropertySources propertySources = environment.getPropertySources();
    System.out.println(systemEnvironment);
    System.out.println(propertySources);


}
```



#### 2、配置文件查找位置

(1) classpath 根路径	resource文件夹

(2) classpath 根路径下config目录

(3) jar包当前目录

(4) jar包当前目录的config目录

(5) /config子目录的直接子目录（只在linux系统中才能用）

#### 3、配置文件加载顺序：

1. 　当前jar包内部的application.properties和application.yml
2. 　当前jar包内部的application-{profile}.properties 和 application-{profile}.yml
3. 　引用的外部jar包的application.properties和application.yml
4. 　引用的外部jar包的application-{profile}.properties 和 application-{profile}.yml

#### 4、指定环境优先，外部优先(cmd等)，后面的可以覆盖前面的同名配置项

配置文件查找位置，从(5)到(1)优先级越来越低







### SrpingBoot原理



#### starter启动原理

- starter-pom引入 autoconfigurer 包
- autoconfigure包中配置使用 **META-INF/spring.factories** 中 **EnableAutoConfiguration 的值，使得项目启动加载指定的自动配置类**
- **编写自动配置类 xxxAutoConfiguration -> xxxxProperties**

- - **@Configuration**
  - **@Conditional**
  - **@EnableConfigurationProperties**
  - **@Bean**
  - ......

**引入starter** **--- xxxAutoConfiguration --- 容器中放入组件 ---- 绑定xxxProperties ----** **配置项**



#### 自定义starter

创建两个项目，启动器用于把自动配置包引入

**atguigu-hello-spring-boot-starter（启动器）**

**atguigu-hello-spring-boot-starter-autoconfigure（自动配置包）**







