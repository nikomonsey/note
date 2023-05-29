# ContextLoaderListener

Spring提供了监听器ContextLoaderListener，实现ServletContextListener接口，可监听

ServletContext的状态，在web服务器的启动，读取Spring的配置文件，创建Spring的IOC容器。web

应用中必须在web.xml中配置



***web.xml中是标签执行顺序为***

> <context-param>	<listener>	<filter>	<servlet>

而springmvc是controller层，需要依赖spring的service层，所以spring要在MVC之前创建

就要用到listener监听器和context-param上下文初始化参数

在web.xml中

```
<listener>
    <!--
        配置Spring的监听器，在服务器启动时加载Spring的配置文件
        Spring配置文件默认位置和名称：/WEB-INF/applicationContext.xml
        可通过上下文参数自定义Spring配置文件的位置和名称
    -->
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

<!--自定义Spring配置文件的位置和名称-->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring.xml</param-value>
</context-param>
```

配置ContextLoaderListener监听器



## 重

spring和springmvc的配置文件分开写，就是创建两个不同的IOC容器，用于管理service和controller，

而springmvc的DispatcherServlet初始化时	执行了代码

```
//设置父容器
wac.setParent(parent);
```

而springmvc的父容器就是spring的IOC，子容器能够访问父容器，但是父容器不能访问子容器，所以springmvc能访问spring的IOC容器	类似于子类和父类



# 准备工作

## 引入依赖

```
packaging>war</packaging>

<properties>
    <spring.version>5.3.1</spring.version>
</properties>

<dependencies>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>${spring.version}</version>
</dependency>

<!--springmvc-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>${spring.version}</version>
</dependency>

<!-- Mybatis核心 -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.7</version>
</dependency>

<!--mybatis和spring的整合包-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>2.0.7</version>
</dependency>

<!-- 连接池 -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.0.9</version>
</dependency>

<!-- junit测试 -->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>

<!-- MySQL驱动 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.16</version>
</dependency>

<!-- log4j日志 -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>

    <!-- https://mvnrepository.com/artifact/com.github.pagehelper/pagehelper -->
    <dependency>
        <groupId>com.github.pagehelper</groupId>
        <artifactId>pagehelper</artifactId>
        <version>5.2.0</version>
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
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.12.1</version>
    </dependency>
    <dependency>
        <groupId>commons-fileupload</groupId>
        <artifactId>commons-fileupload</artifactId>
        <version>1.3.1</version>
    </dependency>

    <!-- Spring5和Thymeleaf整合包 -->
    <dependency>
        <groupId>org.thymeleaf</groupId>
        <artifactId>thymeleaf-spring5</artifactId>
        <version>3.0.12.RELEASE</version>
    </dependency>
</dependencies>
```



## 数据库建表

```
CREATE TABLE `t_emp` (
`emp_id` int(11) NOT NULL AUTO_INCREMENT,
`emp_name` varchar(20) DEFAULT NULL,
`age` int(11) DEFAULT NULL,
`sex` char(1) DEFAULT NULL,
`email` varchar(50) DEFAULT NULL,
PRIMARY KEY (`emp_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```



# 配置MVC

```
<context:component-scan base-package="com.ljxxx.ssm.contorller"></context:component-scan>

<bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
    <property name="order" value="1"/>
    <property name="characterEncoding" value="UTF-8"/>
    <property name="templateEngine">
        <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
            <property name="templateResolver">
                <bean
                        class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
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

<mvc:default-servlet-handler></mvc:default-servlet-handler>

<mvc:view-controller path="/" view-name="index"></mvc:view-controller>

<mvc:annotation-driven></mvc:annotation-driven>
```



# 配置spring

```
<!--扫描组件（除controller注解以外）-->
<context:component-scan base-package="com.ljxxx.ssm">
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>

<!--配置数据源-->
<context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>

<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${mysql.driver}"></property>
    <property name="url" value="${mysql.url}"></property>
    <property name="username" value="${mysql.username}"></property>
    <property name="password" value="${mysql.password}"></property>
</bean>
```

注：此时扫描的组件不扫描controller注解，所以用org.springframework.stereotype.Controller表示controller注解



# 整合spring和mybatis

## 在spring配置文件中设置mybatis相关的配置

mybatis需要	new SqlSessionFactorybuild()等获取SqlSessionFactory对象

可以直接在spring中

```
<bean class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="configLocation" value="classpath:mybatis-config.xml"></property>
    <property name="dataSource" ref="dataSource"></property>
</bean>
```

使用bean注册SqlSessionFactoryBean	直接获取SqlSessionFactory对象，参数configLocation为mybatis配置文件路径，若在spring中配置了dataSource，则在mybatis配置文件中不需要再配置，即

```
<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="${mysql.driver}"/>
            <property name="url" value="${mysql.url}"/>
            <property name="username" value="${mysql.username}"/>
            <property name="password" value="${mysql.password}"/>
        </dataSource>
    </environment>
</environments>
```

可以删除

<transactionManager type="JDBC"/>事务管理也可以在spring中配置



其他在mybatis中配置的信息都可以在spring中配置

```
<bean class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="configLocation" value="classpath:mybatis-config.xml"></property>
    <!--配置数据源-->
    <property name="dataSource" ref="dataSource"></property>
    <!-- 设置类型别名所对应的包 -->
    <property name="typeAliasesPackage" value="com.ljxxx.ssm.pojo"></property>
    <!--
        设置映射文件的路径
        若映射文件所在路径和mapper接口所在路径一致，则不需要设置
    -->
    <!--<property name="mapperLocations" value="classpath:mapper/*/xml"></property>-->
    
    <!--配置分页插件-->
    <property name="plugins">
        <array>
            <bean class="com.github.pagehelper.PageInterceptor"></bean>
        </array>
    </property>
</bean>
```

mapperLocations参数表示映射文件路径，若映射文件和mapper接口路径一致，则不需要配置，但是要配置MapperScannerConfigurer接口bean！！！！

spring中有MapperScannerConfigurer接口，可以直接把包下所有接口注册为bean，交给springIOC容器管理

```
<!--
    配置mapper接口的扫描配置
    由mybatis-spring提供，可以将指定包下所有的mapper接口创建动态代理
    并将这些动态代理作为IOC容器的bean管理
    -->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.ljxxx.ssm.mapper"></property>
</bean>
```

这样就可以直接在service类中直接自动装配mapper接口

```
@Service
public class EmpServiceImpl implements EmpService {

    @Autowired
    private EmpMapper empMapper;
}
```

## 创建日志文件log4j.xml



## 设置事务管理器

在spring.xml中

```
<!--引入事务管理器-->
 <bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager"></bean>
<!--
    开启事务的注解驱动
    将使用注解@Transactional标识的方法或类中所有的方法进行事务管理工
    -->
<tx:annotation-driven transaction-manager="dataSourceTransactionManager"></tx:annotation-driven>
```

若事务管理器的id为transactionManager	则不需要开启事务注解驱动，直接使用注解@transactionl



# 实现分页功能

controller层

```
@RequestMapping(value = "/emp/page/{pageNum}",method = RequestMethod.GET)
public String getEmpPage(@PathVariable("pageNum")Integer pageNum,Model model){
    PageInfo<Emp> page=empService.getEmpPage(pageNum);
    model.addAttribute("page",page);
    return "emp_list";
}
```

把pageInfo对象放入请求域中，pageInfo中有所有分页的信息，包括了我们查询出来的数据，即list属性

PageInfo{

pageNum=8, pageSize=4, size=2, startRow=29, endRow=30, total=30, pages=8,

list=Page{count=true, pageNum=8, pageSize=4, startRow=28, endRow=32, total=30,

pages=8, reasonable=false, pageSizeZero=false},

prePage=7, nextPage=0, isFirstPage=false, isLastPage=true, hasPreviousPage=true,

hasNextPage=false, navigatePages=5, navigateFirstPage4, navigateLastPage8,

navigatepageNums=[4, 5, 6, 7, 8]

}

pageNum：当前页的页码

pageSize：每页显示的条数

size：当前页显示的真实条数

total：总记录数

pages：总页数

prePage：上一页的页码

nextPage：下一页的页码

isFirstPage/isLastPage：是否为第一页/最后一页

hasPreviousPage/hasNextPage：是否存在上一页/下一页

navigatePages：导航分页的页码数

navigatepageNums：导航分页的页码，[1,2,3,4,5]



service层

```
public PageInfo<Emp> getEmpPage(Integer pageNum) {
    PageHelper.startPage(pageNum,4);
    List<Emp> list =empMapper.getAllEmp();
    PageInfo<Emp> page =new PageInfo<Emp>(list,5);
    return page;
}
```

分页插件是在查询功能之前使用PageHelper.startPage(int pageNum, int pageSize)开启分页功能

pageNum为查询第几页，pageSize为一页多少个数据，

PageInfo<Emp> page =new PageInfo<Emp>(list,5);
再新建一个PageInfo对象，把查询的所有数据list集合放入，第二个参数为，导航分页的页码数



Mapper

```
<select id="getAllEmp" resultType="Emp">
    select * from t_emp
</select>
```



网页设置

```
<table>
    <tr>
        <th colspan="6">员工信息</th>
    </tr>
    <tr>
        <td>流水号</td>
        <td>姓名</td>
        <td>年龄</td>
        <td>性别</td>
        <td>邮箱</td>
        <td>操作</td>
    </tr>
    <tr th:each="emp,status:${page.list}">
        <td th:text="${status.count}"></td>
        <td th:text="${emp.empName}"></td>
        <td th:text="${emp.age}"></td>
        <td th:text="${emp.gender}"></td>
        <td th:text="${emp.email}"></td>
        <td>
            <a href="">添加</a>
            <a href="">删除</a>
        </td>
    </tr>
</table>

<div style="text-align: center">
    <a th:if="${page.hasPreviousPage}" th:href="@{/emp/page/1}">首页</a>
    <a th:if="${page.hasPreviousPage}" th:href="@{'/emp/page/'+${page.prePage}}">上一页</a>
    <span th:each="num : ${page.navigatepageNums}">
        <a th:if="${page.pageNum ==num}" style="color: red" th:href="@{'/emp/page/'+${num}}" th:text="'['+${num}+']'"></a>
        <a th:if="${page.pageNum !=num}" th:href="@{'/emp/page/'+${num}}" th:text="'['+${num}+']'"></a>
    </span>
    <a th:if="${page.hasNextPage}" th:href="@{'/emp/page/'+${page.nextPage}}">下一页</a>
    <a th:if="${page.hasNextPage}" th:href="@{'/emp/page/'+${page.pages}}">末页</a>

</div>
```

首页和上一页的判断条件为hasPreviousPage是否有上一页，为真，则显示首页和上一页的超链接

末页和下一页的判断条件为hasNextPage是否有下一页，为真，则显示末页和下一页的超链接

中间使用navigatepageNums参数，当前页的导航分页页码，循环navigatepageNums，即可得到每页的导航分页

再通过判断th:if="${page.pageNum ==num}" 当前循环的页码是否等于当前页，是的话则加个红色	