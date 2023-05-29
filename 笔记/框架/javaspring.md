# 基础项目

test.java

```
@Test
public void testadd(){
    //1.加载spring配置文件
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");

    //2.获取配置创建的对象
    User user = context.getBean("user", User.class);

    System.out.println(user);
    user.add();


}
```

bean.xml

```
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user" class="com.ljxxx.User"></bean>
</beans>
```

id的值为你要取的别名，class为要获取bean的地址

## IOC容器

### 什么是IOC

(1）控制反转，把对象创建和对象之间的调用过程，交给Spring进行管理

(2）使用IOC目的:为了耦合度降低

### IOC底层原理

(1) xml解析、工厂模式、反射



### IOC(接口)

1.IOC思想基于IOC容器完成，IOC容器底层就是对象工厂

2.Spring提供IOC容器实现两个方式（两个接口）

(1).BeanFactroy

Spring内部使用的接口，通常不提供给开发人员使用

加载配置文件时，不会创建对象，再获取对象时，才创建 

(2).ApplicationContext

BeanFactory接口的子接口

加载配置文件时，就创建对象

ApplicatiohnContext有两个实现类

FileSystemXmlApplicationContext

ClassPathXmlApplicationContext

都是用于读取xml文件	

```
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("bean1.xml");
```

Class为项目工程的路径，即相对路径

File为在磁盘中的绝对路径

### IOC操作Bean管理

#### 1、什么是Bean管理

(0)Bean管理指的是两个操作

(1)Spring创建对象

(2) Spirng注入属性

#### 2.Bean管理操作的两种方式

##### (1).基于xml配置文件方式实现

###### 创建对象

```
    <bean id="user" class="com.ljxxx.spring.User"></bean>
```

创建对象时，默认执行无参构造方法完成对象创建

###### 注入属性

###### 1.使用set方法注入

```
private String bname;
private String bauthor;
public void setBname(String bname) {
    this.bname = bname;
}
public void setBauthor(String bauthor) {
    this.bauthor = bauthor;
}
public void test1() {
    System.out.println(bname+":"+bauthor);
}
```

先在Book.java中写好set方法用于spring框架调用

在bean1.xml文件中加入

```
<bean id="book" class="com.ljxxx.spring.pojo.Book">
    <property name="bname" value="活着"></property>
    <property name="bauthor" value="余华"></property>
</bean>
```

property标签用于注入对象

name表示类中属性名称

value表示向属性中注入的值



###### 2.使用有参构造方法注入

```
private String oname;
private String oauthor;
public Order(String oname, String oauthor) {
    this.oname = oname;
    this.oauthor = oauthor;
}
public void test2(){
    System.out.println(oname+":"+oauthor);
}
```

在xml中

```
<bean id="order" class="com.ljxxx.spring.pojo.Order">
    <constructor-arg name="oname" value="ljxxx"></constructor-arg>
    <constructor-arg name="oauthor" value="1234"></constructor-arg>
</bean
```

使用constructor标签，可用有参构造方法创建对象，name为参数名，value为参数具体值



###### 3.为变量赋值为null值

```
<property name="address"><null/></property>
```



###### 4.为变量赋值特殊字符 例如<<南京>>

两种方法

```
<property name="address" value="&lt;&lt;南京&gt;&gt;"></property>
```

使用转义

```
<property name="address"><value><![CDATA[<<南京>>]]></value>
```

把特殊字符写入到CDATA中，让其转义



###### 5.外部bean注入

即有UserService和UserDaoImpl有个类，

UserService类中要调用UserDaoImpl中的方法

```
public class UserService {
    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void add(){
        System.out.println("add....");
        userDao.update();
    }
}
```

先在UserService中先创建一个UserDao 对象，并写set方法（**xml的本质就是调用set方法，所以set方法一定要写！！**）

```
<bean id="userServiece" class="com.ljxxx.spring.service.UserService">
    <property name="userDao" ref="userDaoImpl"></property>
</bean>
<bean id="userDaoImpl" class="com.ljxxx.spring.dao.UserDaoImpl"></bean>
```

把UserDaoImpl在xml中写成bean后，用ref标签，写入到UserService中的UserDao变量！！



###### 6.内部bean注入

```
<bean id="userService" class="com.ljxxx.spring.service.UserService">
    <property name="userDao">
        <bean id="userImpl" class="com.ljxxx.spring.dao.UserDaoImpl">
        </bean>
    </property>
</bean>
```

效果与外部bean注入一样，区别就是在userDao变量赋值中，为其创建一个bean对象



###### 7.集合注入

```
<bean id="springstudent" class="com.ljxxx.spring.colletction.Springstudent">
    <property name="course">
        <array>
            <value>张三</value>
            <value>李四</value>
        </array>
    </property>
    <property name="list">
        <list>
            <value>tom</value>
            <value>lisa</value>
        </list>
    </property>
    <property name="map">
        <map>
            <entry key="123" value="123"></entry>
            <entry key="321" value="321"></entry>
        </map>
    </property>
</bean>
```

注：输出arrays集合时，可用Arrays.toString(course)输出！！！！



###### 8.在集合中设置对象类型值

在7的基础上，若list集合中为另外一个类，即

private List<Book> bookList , 这时注入方式为

```
<property name="bookList">
    <list>
        <bean id="book" class="com.ljxxx.spring.pojo.Book">
            <property name="address" value="123"></property>
            <property name="bname" value="ljxxx"></property>
        </bean>
    </list>
</property>
```

可用外部bean和内部bean两种方式



###### 9.把注入部分单独提取出来

启用名称空间

```
xmlns:util="http://www.springframework.org/schema/util"
xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                     http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">
```

写入

```
<util:list id="booklist">
    <value>123</value>
    <value>321</value>
    <value>1234</value>
</util:list>
```

list中用 ref标签

```
<property name="list" ref="booklist">
```

注：不能同时在bean内注入，再用util名称空间注入

###### 10.工厂bean

工厂bean和普通bean的区别

普通bean class中写的是什么类，返回的就是什么类

但是工厂bean，写的是什么类，返回的不一定是那个类

```
public class MyBean implements FactoryBean {

    @Override
    public Object getObject() throws Exception {
        return null;
    }

    @Override
    public Class<?> getObjectType() {
        return null;
    }

    @Override
    public boolean isSingleton() {
        return false;
    }
}
```

通过实现FactoryBean	，若想真正实现的类为Book就改为

```
public class MyBean implements FactoryBean<Book> {

    @Override
    public Book getObject() throws Exception {
        Book book = new Book();
        book.setBname("123");
        book.setAddress("china");
        return book;
    }
```

通过getObject方法修改

这时，在xml中还是写

```
<bean id="myBean" class="com.ljxxx.spring.factorybean.MyBean">
</bean>
```

但是在测试类中只能写Book.class，因为只能读取到book类了

```
@Test
public void test(){
    ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("bean3.xml");
    Book myBean = classPathXmlApplicationContext.getBean("myBean", Book.class);
    System.out.println(myBean);
}
```

###### 11.bean的作用域

单实例：一个类只有一个实例，每次用到类都是用到同一个实例

多实例：一个类有多个实例

bean默认为单实例，即

```
Book book1 = applicationContext.getBean("book", Book.class);
Book book2 = applicationContext.getBean("book", Book.class);
System.out.println(book1);
System.out.println(book2);
```

输出结果为

com.ljxxx.spring.pojo.Book@43a0cee9
com.ljxxx.spring.pojo.Book@43a0cee9

即地址相同，为同一实例

设置单多实例

```
<bean id="myBean" class="com.ljxxx.spring.factorybean.MyBean" scope="prototype">
```

scope标签设置实例，prototype为多实例，singleton为单实例

singleton为加载spring文件的时候就创建单实例对象

proototype为调用getBean方法时，才创建对象



###### 12.bean生命周期

(1）通过构造器创建bean实例（无参数构造）。

(2）为bean的属性设置值和对其他bean引用（调用set方法)

(3）调用bean的初始化的方法（需要进行配置初始化的方法)
使用init-method标签配置		init-method="方法名"，方法要在class=的类中有

(4)   bean可以使用了(对象获取到了)

(5）当容器关闭时候，调用bean的销毁的方法（需要进行配置销毁的方法)
使用destroy-method标签配置		destroy-method="方法名"，方法要在class=的类中有

```
@Test
public void OrderTest(){
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("bean1.xml");
    Order order = applicationContext.getBean("order", Order.class);
    System.out.println(order);
    order.test2();
    //手动销毁
    ((ClassPathXmlApplicationContext)applicationContext).close();
}
```

((ClassPathXmlApplicationContext)applicationContext).close();

ClassPathXmlApplicationContext中有close方法，为结束销毁bean

用applicationContext强转为ClassPathXmlApplicationContext类型



###### 13.自动装配

通过autowire标签进行自动装配

byName为，id为dept和emp类中dept属性相匹配

```
public class Emp {
    private Dept dept;

    public void setDept(Dept dept) {
        this.dept = dept;
    }

    @Override
    public String toString() {
        return "Emp{" +
                "dept=" + dept +
                '}';
    }
}
```

```
<bean id="emp" class="com.ljxxx.spring.autowirte.Emp" autowire="byName"><bean>
<bean id="dept" class="com.ljxxx.spring.autowirte.Dept"></bean>
```

bytype为，id为dept的类型为dept，和emp类中的dept类型的属性匹配

```
<bean id="emp" class="com.ljxxx.spring.autowirte.Emp" autowire="byType"></bean>
<bean id="dept" class="com.ljxxx.spring.autowirte.Dept"></bean>
```

###### 14.引入外部文件进行配置

建立德鲁伊数据库连接

```
xmlns:context="http://www.springframework.org/schema/context"
   xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

<context:property-placeholder location="classpath:jdbc.properties"/>

<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${mysql.driver}"></property>
    <property name="url" value="${mysql.url}"></property>
    <property name="username" value="${mysql.username}"></property>
    <property name="password" value="${mysql.password}"></property>
</bean>
```

开启context空间

并写好jdbc.properties文件

```
mysql.driver=com.mysql.jdbc.Driver
mysql.url=jdbc:mysql://localhost:3306/mybatis
mysql.username=root
mysql.password=12345
```

使用<context:property-placeholder location="classpath:jdbc.properties"/> 引入到xml文件中

在bean中就可以用${xxxx}xxxx为jdbc.properties文件中数据前的名字

###### 15.p名称标签注入

注入只需要写xmlns:p="http://www.springframework.org/schema/p"

```
xmlns:p="http://www.springframework.org/schema/p"
```

```
<bean id="user" class="com.ljxxx.spring.pojo.User" p:uname="ljxxx">
```



##### (2).基于注解方式实现

spring容器一般来说不支持接口注入，即@Service也好 @Controller通常不加在接口上，而是实现类上
但是使用 @Autowind 自动装配还是用接口

@Component	用于任何层次

@Controller		用于控制层

@Service			用于业务逻辑层

@Repository	用于数据访问层

```
public class UserService {
    @Resource
    private UserDao userDao;
```

@Value		指定bean实例的注入值

```
@Value("123")
private String bname;
```



###### 1.开启组件扫描

```
<context:component-scan base-package="com.ljxxx.spring"></context:component-scan>
```

这样在ljxxx.spring包下所有的类都可以使用注解

```
@Service
public class UserService {
    private UserDao userDao;

    public void add(){
        System.out.println("add....");
        userDao.update();
    }
}
```

可用@Service(value="xxx") value等于之前的id	不用value，则默认为类名小写

###### 2.组件扫描配置


```
/*
use-default-filters标签 是否开启默认扫描配置
include-filter标签 包含哪些过滤器
type标签 什么类型 annotation为注解类型
expressio标签 具体类型 
合起来就是 只扫描Controller标签
*/
<context:component-scan base-package="com.ljxxx.spring" use-default-filters="false">
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan

/*
exclude-filter标签 不包含哪些过滤器
意思就是 不扫描Controller标签
*/

<context:component-scan base-package="com.ljxxx.spring">
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

###### 3.属性注入

@Value		指定bean实例的注入值

```
@Value("123")
private String bname;
```

@Autowired	通过类型注入

@Qualifier	通过名称注入，和autowired配合使用，若有多个实现类，则可用给每个实现类不同的id，通过qualifier来注入

```
@Autowired
@Qualifier(value = "userDaoImpl1")
private UserDao userDao;
```

@Resource	可类型注入，可名称注入，不写name就是类型注入

```
@Resource(name = "userDaoImpl1")
private UserDao userDao;
```

###### 4.完全注解开发

即把开启组件扫描也变成一个配置类

配置类	configuration标签表示 是配置类	componentScan为组件扫描	basePackages为路径

```
@Configuration
@ComponentScan(basePackages = {"com.ljxxx.spring"})
public class SpringConfig {
}
```

```
public void test5(){
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfig.class);
    }
```

若某些类不能用注解创建bean，例如一些jar包中的类

可在配置类中有@Bean注解创建

```
@Bean
public DruidDataSource druidDataSource(){
    DruidDataSource druidDataSource = new DruidDataSource();
    druidDataSource.setDriverClassName("com.mysql.jdbc.Driver");
    druidDataSource.setUrl("jdbc:mysql://localhost:3306/spring");
    druidDataSource.setUsername("root");
    druidDataSource.setPassword("1234");
    return druidDataSource;
}
```



## AOP切面

底层使用动态代理

第一种 有接口情况，使用jdk动态代理

创建接口实现类代理对象

第二种 没有接口情况，使用CGLIB动态代理

创建子类的代理对象



### （重！）jdk动态代理

调用newProxyInstance方法

三个参数 

第一个 当前类加载器

第二个 要增强方法所在的类实现的所有接口，这个类实现的接口，支持多个接口

第三个 实现接口InvocationHandler 写增强方法的对象

```
public class OrderProxy implements InvocationHandler {
    private OrderDao orderdao;
    public Object creatProxy(OrderDao orderDao){
        this.orderdao=orderDao;
        
        ClassLoader classLoader = OrderProxy.class.getClassLoader();
        Class<?>[] interfaces = orderdao.getClass().getInterfaces();
        
        return Proxy.newProxyInstance(classLoader,interfaces, this::invoke);

    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

        MyAspect myAspect = new MyAspect();
        myAspect.check();

        Object invoke = method.invoke(orderdao, args);

        myAspect.log();

        return invoke;
    }
}
```

新建代理类 继承InvocationHandler类，并调用Proxy.newProxtInstance方法

OrderProxy.class.getClassLoader方法获取当前类的类加载器

orderDao.getClass().getInterfaces()获取orderDao类所有实现的接口

this::invoke	表示要增强方法为自身invoke方法

### 相关术语



### AOP操作

引入依赖

#### 切入点表达式

作用：知道对哪些类里面的哪个方法进行增强

语法结构：

execution([权限修饰符] [返回类型] [类全路径] [方法名称] ([参数列表]))

举例1 对com.atguigu.dao.BookDao类里面的add方法进行增强

execution(* com.atguigu.dao.BookDao.add(..))

举例2 对com.atguigu.dao.BookDao类里面的所有方法进行增强

execution(* com.atguigu.dao.BookDao.*(..))

举例3 对com.atguigu.dao中所有的类和所有方法进行增强

exection(* com.atguigu.dao*****.*****(..))

*表示所有权限修饰符 空格表示返回类型

#### AspectJ注解

##### 1.开启注解扫描和生成代理对象

要开启context和aop空间！！！

```
<!--开启注解扫描-->
<context:component-scan base-package="com.ljxxx.spring.aopanno"></context:component-scan>

<!--开启生成代理对象-->
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

##### 2.加强类和被加强类 加上Component注解，解析成bean

```
@Component
public class User {
    public void add(){
        System.out.println("这是add方法");
    }
}
```

在加强类上 加Aspect注解	生成代理对象

```
@Component
@Aspect
public class UserProxy {
    @Before(value = "execution(* com.ljxxx.spring.aopanno.User.add(..))")
    public void before(){
        System.out.println("before");
    }
    @After(value = "execution(* com.ljxxx.spring.aopanno.User.add(..))")
    public void after(){
        System.out.println("after");
    }
    @AfterReturning(value = "execution(* com.ljxxx.spring.aopanno.User.add(..))")
    public void afterReturning(){
        System.out.println("afterReturning");
    }
    @AfterThrowing(value = "execution(* com.ljxxx.spring.aopanno.User.add(..))")
    public void afterThrowing(){
        System.out.println("afterThrowing");
    }
    /*@Around(value = "execution(* com.ljxxx.spring.aopanno.User.add(..))")
    public void around(){
        System.out.println("around");
    }
*/

}
```

##### 各种注解

Before注解	前置通知	***exection为切入点表达式***

After注解	后置通知（最终通知）不论是否异常，只要执行被增强方法了就会执行after方法

AfterReturning	返回通知			若有异常，则被增强方法没有返回值，就不执行

AfterThrowing	异常通知			被增强方法有异常时执行

Around				环绕通知			在被增强方法之前和之后各执行一次

```
@Around(value = "execution(* com.ljxxx.spring.aopanno.User.add(..))")
public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {

    System.out.println("around之前");
    proceedingJoinPoint.proceed();
    System.out.println("around之后");
}
```

***用proceedingJoinPoint参数的proceed()方法来执行被增强方法***

若没有proceed方法，则执行顺序会混乱，没有办法确定 around中的方法是在(被执行方法)之前执行,还是之后执行

##### 抽取切入点表达式

```
@Pointcut(value = "execution(* com.ljxxx.spring.aopanno.User.add(..))")
public void pointdemo(){
}

@Before(value = "pointdemo()")
```

使用pointcut注解，将重复的表达式抽取出来

##### 多个增强类对同一方法进行增强，设置优先级

在增强类上加注解@Order(数字类型值)数字越小优先级越高

```
@Order(1)
public class UserProxy {
```

##### 完全注解开发

```
@Configuration
@ComponentScan(value = "com.ljxxx.spring.aopanno")
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class anno {
}
```

即再加上EnableAspectJAutoProxy注解，用于开启生成代理对象



#### AspectJ xml配置

```
<bean id="user" class="com.ljxxx.spring.aopxml.User"></bean>
<bean id="userProxy" class="com.ljxxx.spring.aopxml.UserProxy"></bean>

<aop:config>
    <aop:pointcut id="p" expression="execution(* com.ljxxx.spring.aopxml.User.add(..))"/>
    <aop:aspect ref="userProxy">
        <aop:before method="before" pointcut-ref="p"/>
    </aop:aspect>
</aop:config>
```

在aop:config标签中配置

aop:pointcut切入点		

aspect切面，即增强类



## JdbcTemplate

### 实现增删改功能

#### 引入jar包

spring-tx spring-orm spring-jdbc mysql-connector druid

#### 设置配置文件

```
<context:component-scan base-package="com.ljxxx.spring"></context:component-scan>

    <!--数据库连接池-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/spring"/>
        <property name="username" value="root"/>
        <property name="password" value="1234"/>

    </bean>

    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
</beans>
```

开启注解扫描

建立数据库连接池bean	！！！！

再建立jdbcTemplate bean 类中有属性 DataSource dataSource把数据库连接池注入

在dao和service中设置注解，并把jdbcTemplate引入到dao中，调用其update方法进行添加操作

```
@Repository
public class UserDaoImpl implements UserDao{
    @Autowired
    private JdbcTemplate jdbcTemplate;
    public int add(User user){
        String sql="insert into user values(?,?,?)";
        int update = jdbcTemplate.update(sql, user.getId(), user.getName(), user.getPhone());
        return update;
    }

}
```

```
@Service
public class UserService {
    @Autowired
    private UserDao userDao;
    public void add(User user){
        int add = userDao.add(user);
        System.out.println(add);
    }
}
```

#### sql语句

```
String sql="insert into user values(?,?,?)";
String sql="update user set name=?,phone=? where id=?";
String sql="delete from user where id=?";
```

### 查询功能

当查询有几条记录时 用方法queryForObject(a,b)第一个参数sql语句，第二个参数为方法返回值的类型

当查询单条记录 用方法queryForObject(a,b,c)第一个参数sql语句，第二个参数RowMapper 接口，用于返回不同类型数据，第三个参数sql语句值

当查询集合时，用方法query(a,b)第一个参数sql语句，第二个参数RowMapper 接口，用于返回不同类型数据

RowMapper的写法为 BeanPropertyRowMapper<返回类型class>(返回类型class)

```
/**
 * 查询表中有几条记录
 * queryForObject(sql,返回类型的Class)
 * 第二个参数为方法返回值的类型
 * @return
 */
@Override
public int selectCount() {
    String sql="select count(*) from user";
    Integer count = jdbcTemplate.queryForObject(sql, Integer.class);
    return count;
}

/**
 * 查询单条数据
 * queryForObject(sql,RowMapper，sql语句参数值)
 * @param id
 * @return
 */
@Override
public User select(int id) {
    String sql="select * from user where id=?";
    User user = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<User>(User.class), id);
    return user;
}

/**查询集合
 * query(sql,RowMapper)
 * @return
 */
@Override
public List<User> selectUser() {
    String sql="select * from user";
    List<User> userList = jdbcTemplate.query(sql, new BeanPropertyRowMapper<User>(User.class));
    return userList;
}
```

### 批量增删改功能

使用batchUpdate方法 batch批量的意思

再定义一个list数组，数组中的元素为数组，用于存储要增删改的数据

```
/**
 * 批量添加
 * @param batchArgs
 * @return
 */
@Override
public int[] batchAddUser(List<Object[]> batchArgs) {
    String sql="insert into user values(?,?,?)";
    int[] ints = jdbcTemplate.batchUpdate(sql, batchArgs);
    return ints;
}
@Override
public int[] batchDeleteUser(List<Object[]> batchArgs) {
    String sql="delete from user where id=?";
    int[] ints = jdbcTemplate.batchUpdate(sql, batchArgs);
    return ints;
}
@Override
public int[] batchUpdateUser(List<Object[]> batchArgs) {
    String sql="update user set name=?,phone=? where id=?";
    int[] ints = jdbcTemplate.batchUpdate(sql, batchArgs);
    return ints;
}
```

新建batchArgs数组，把数据都写进去

```
public void testBatchAddUser(){
    ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("jdbc.xml");
    UserService userService = classPathXmlApplicationContext.getBean("userService", UserService.class);
    List<Object[]> batchArgs = new ArrayList<>();
    Object[] o1 = {"5","555","5555"};
    Object[] o2 = {"6","666","6666"};
    Object[] o3 = {"7","777","7777"};
    batchArgs.add(o1);
    batchArgs.add(o2);
    batchArgs.add(o3);
    int[] ints = userService.batchAddUser(batchArgs);
    System.out.println(Arrays.toString(ints));
}
```



## 事务管理

### 事务四个特性

原子性：事务要么都成功，要么都失败

一致性：事务提交前后的数据完整性和状态保持一致

隔离性：多事务操作时，之间不会产生影响

持久性：提交后，表中数据发送变化



### 事务管理过程

1.开启事务

2.进行业务逻辑

3.没发生异常，提交事务

4.发生异常，回滚事务



### 情景搭建

A给B转账，同上，写好dao和service包，开启注解扫描，连接好数据库，写sql语句等待

若在调用加钱和扣钱方法中出现错误，即int n=10/0;	则加钱方法会被调用，而扣钱不会

```
int i = userDao.addMoney(id,node);
int n=10/0;
int i1 = userDao.reduceMoney(id,node);
```



### 两种事务管理方式：

编程式事务管理和声明式事务管理



#### 声明事务管理：

底层使用了AOP原理

**基于注解方式**

基于xml配置文件方式



### Spring事务管理API

提供了一个接口，代表事务管理器，这个接口针对不同的框架提供了不同的实现类



### 事务操作(注解式事务管理)

#### 1.在配置文件中配置事务管理器

```
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"/>
</bean>
```

#### 2.开启事务注解

引入名称空间tx，把事务管理器引入到事务注解中

```
<tx:annotation-driven transaction-manager="transactionManager"/>
```

#### 3.在Service类上(或者Service类中的方法上)添加事务注解(@Transactional)			

通常在Service上开启事务！！！！

在类上添加注解表示	类中所有方法都开启事务

在方法上添加注解表示	单个方法开启事务

```
@Transactional
public class UserService {
```

这时，再运行，不会出现只加钱不扣钱的情况



### 事务操作(声明式事务管理参数配置)

![image-20230217145534310](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230217145534310.png)



#### 1、propagation:事务传播行为

```
@Transactional(propagation = Propagation.REQUIRED)
```

当一个事务方法被另外一个事务方法调用时，这个方法如何进行

![image-20230217150022767](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230217150022767.png)



REQUIRED：有方法1 事务A，方法2 事务B	单独开启方法1，则开启事务B

方法2开启事务B，同时调用方法1，则方法1会加入到事务B中

REQUIRED_NEW：

![image-20230217150304329](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230217150304329.png)

和REQUIRED的区别在于	

方法B即使被方法A调用后，也会开启自己的事务B，作为内部事务。
若方法A出现问题，方法B仍然可以提交，不受影响。

SUPPORTS：

若方法B为SUPPORTS行为	调用方法B，可以非事务状态下运行

若方法A为REQUIRED行为	方法A调用方法B，则方法B加入到事务A中！！！



#### 2、ioslation:事务隔离级别

```
@Transactional(propagation = Propagation.REQUIRED,isolation = Isolation.REPEATABLE_READ)
```

（1）事务有特性成为隔离性，多事务操作之间不会产生影响。不考虑隔离性产生很多问题
（2）有三个读问题:脏读、不可重复读、虚(幻)读

脏读:一个未提交事务读取到另一个未提交事务的数据

不可重复读:一个未提交事务读取到另一提交事务修改数据

虚读:一个未提交事务读取到另一提交事务添加数据



设置事务隔离性，解决读问题

![image-20230217153752595](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230217153752595.png)

默认为REPEATABLE READ



#### 3、timeout:超时时间

(1)事务需要在一定时间内进行提交，如果不提交进行回滚

(2)默认值是-1，即不超时

设置时间以秒单位进行计算

#### 4、readOnly:是否只读

(1）读:查询操作，写:添加修改删除操作

(2) readOnly默认值false，表示可以查询，可以添加修改删除操作

(3)设置readOnly值是true，设置成true之后，只能查询

#### 5、rollbackFor:回滚

(1)设置出现哪些异常进行事务回滚

```
rollbackFor = NullPointerException.class
```

表示出现空指针异常则回滚

#### 6、noRollbackFor:不回滚

(1)设置出现哪些异常不进行事务回滚



### 事务操作(XML声明式事务管理)

1.配置事务管理器

2.配置通知

3.配置切入点的切面

```
<!--配置通知-->
<tx:advice id="txadvice" >
    <!--配置事务参数-->
    <tx:attributes>
        <!--指定哪种规则的方法上添加事务-->
        <tx:method name="zhuanzhang" propagation="REQUIRED"/>
    </tx:attributes>
</tx:advice>

<aop:config>
    <!--配置切入点-->
    <aop:pointcut id="pt" expression="execution(* com.ljxxx.spring.Service.UserService.*(..))"/>
    <!--配置切面-->
    <aop:advisor advice-ref="txadvice" pointcut-ref="pt"/>
</aop:config>
```

注意，这时配置切面用的是aop:advisor，而不是aop:aspect	！！

所以要把配置的通知注入到切面中

这样的好处就是

### 事务操作(完全注解)

@EnableTransactionManagement 开启事务注解

因为都是jar包中的类，所以用bean标签手动写

用set方法写入参数

getJdbcTemplate(DruidDataSource druidDataSource)方法中

druidDataSource参数相当于xml文件中的

<property name="dataSource" ref="dataSource"></property>

会根据类型自动去IOC容器中找到创建的druidDataSource	！！！

不能写成jdbcTemplate.setDataSource(getDruidDataSource())	

因为这样会创建两个id为getDruidDataSource的bean对象，会导致重名

```
@Configuration
@ComponentScan(basePackages = "com.ljxxx.spring")
@EnableTransactionManagement
public class TxConfig {
    @Bean
    public DruidDataSource getDruidDataSource(){
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName("com.mysql.jdbc.Driver");
        druidDataSource.setUrl("jdbc:mysql://localhost:3306/spring");
        druidDataSource.setUsername("root");
        druidDataSource.setPassword("1234");
        return druidDataSource;
    }

    @Bean
    public JdbcTemplate getJdbcTemplate(DruidDataSource druidDataSource){
        //到IOC容器中根据类型找到druidDataSource
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(druidDataSource);
        return jdbcTemplate;
    }

    @Bean
    public DataSourceTransactionManager getDataSourceTransactionManager(DruidDataSource druidDataSource){
        DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
        dataSourceTransactionManager.setDataSource(druidDataSource);
        return dataSourceTransactionManager;
    }

}
```

### 注解和xml事务管理同时存在

会出现问题 两个事务管理器不同名（id不同）

一个叫getDataSourceTransactionManager	一个叫transactionManager

UserService中 @transaction注解	不知道该识别哪个，所以要给他取名

```
@Transactional(transactionManager=""
```





## Spring新功能

1、整个Spring5框架的代码基于Java8，运行时兼容JDK9，许多不建议使用的类和方法在代码库中删除

2、Spring 5.o框架自带了通用的日志封装

(1) Spring5已经移除Log4jConfigListener，官方建议使用Log4j2

(2) Spring5框架整合Log4j2



### 1.log4j2日志功能

#### 1.引入jar包

log4j-api-2.11.2.jar
log4j-core-2.11.2.jar
log4j-slf4j-impl-2.11.2.jar
slf4j-api-1.7.30.jar



#### 2.引入log4j2.xml文件

```
<?xml version="1.0" encoding="UTF-8"?>
<!--日志级别以及优先级排序:OFF> FATAL> ERROR> INFO> DEBUG> TRACE> ALL  越往右优先级越高，显示的越多 比如INFO能显示ERROR—->
<!-lonfiguration后面的status用于设置log4.2自身内部的信息输出，可以不设置，当设置成trace时，可以看到log4.2内部各种详细输出-->
<Configuration status="INFO">
    <!--先定义所有的appender-->
    <appenders>
        <!--输出日志信息到控制台-->
        <console name="Console" target="SYSTEM_OUT">
            <!--控制日志输出的格式-->
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </console>
    </appenders>
    <!--然后定义logger，只有定义了logger并引入的appender，appender才会生效-->
    <!--root:用于指定项目的根日志，如果没有单独指定Logger，则会使用root作为默认的日志输出-->
    <loggers>
        <root level="info">
            <AppenderRef ref="Console"/>
        </root>
    </loggers>
</Configuration>
```

模板，都一样



#### 3.手动输出

新建类UserLog

```
public class UserLog {
    public static final Logger log= LoggerFactory.getLogger(UserLog.class);

    public static void main(String[] args) {
        log.info("info");
        log.debug("debug");
        log.error("error");
    }
}
```

LoggerFactory.getLogger(xxx.class)	可以在控制台打印日志，便于开发



2.Spring5支持@Nullable注解

(1) @Nullable注解可以使用在方法上面，属性上面，参数上面，表示方法返回可以为空，属性值可以为空，参数值可以为空

```
@Nullable
private Resource[] configResources;

public void abc(@Nullable String abc){}

@Nullable
private String number;
```



### 2.支持函数式注册对象

使用GenericApplicationContext类

有registerBean方法 第一个参数为beanname可为空，第二个参数为要注册对象的类class，第三个参数照写

```
@org.junit.Test
public void test4(){
    GenericApplicationContext genericApplicationContext = new GenericApplicationContext();
    genericApplicationContext.refresh();
    //不为对象取名。则用类名得到bean
    genericApplicationContext.registerBean(User.class,()->new User());
    User user1 = (User)genericApplicationContext.getBean("com.ljxxx.spring.pojo.User");
    //为对象取名
    genericApplicationContext.registerBean("user",User.class,() -> new User());
    User user = (User)genericApplicationContext.getBean("user");
    System.out.println(user);
    System.out.println(user1);
}
```



### 4.支持JUnit4,5

JUnit4,5作用：在测试中，可用不用让我们再输入new ClassPathXmlApplicationContext()

JUnit4	@RunWith	输入版本	ContextConfiguration输入外部文件位置

```
@RunWith(SpringJUnit4ClassRunner.class) //单元测试框架
@ContextConfiguration("classpath:jdbc.xml") //加载配置文件
public class JUnit4Test {
    @Autowired
    private UserService userService;

    @Test
    public void test(){
        userService.zhuanzhang(1,10);
    }

}
```

JUnit5	先把5的包先引入	用的是ExtendWith注解

或者用二合一标签

@SpringJUnitConfig(locations ="classpath:jdbc.xml")

```
@ExtendWith(SpringExtension.class)
@ContextConfiguration("classpath:jdbc.xml") //加载配置文件
public class JUnit4Test {
    @Autowired
    private UserService userService;

    @Test
    public void test(){
        userService.zhuanzhang(1,10);
    }

}
```

​	





