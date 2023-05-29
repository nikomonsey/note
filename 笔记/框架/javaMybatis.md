mysql不同版本的注意事项

1、驱动类driver-class-name

MySQL 5版本使用jdbc5驱动，驱动类使用: com.mysql.jdbc.Driver
MySQL8版本使用jdbc8驱动，驱动类使用: com.mysql.cj.jdbc.Driver

2、连接地址url

MySQL 5版本的url:	jdbc:mysql://localhost:3306/ssm

MySQL_8版本的url:	jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC





# 搭建Mybatis

## 创建maven工程

打包方式：jar

```
<groupId>com.ljxxx.mybaits</groupId>
<artifactId>mybatis</artifactId>
<version>1.0-SNAPSHOT</version>
<packaging>jar</packaging>
```

引入依赖

```
<!-- Mybatis核心 -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.7</version>
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
    <version>5.1.47</version>
</dependency>
```

## 创建MyBatis核心配置文件

在resource目录下创建mybatis-config.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--配置连接数据库的环境-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>

    <!--引入mybatis的映射文件-->
    <mappers>
        <mapper resource=""/>
    </mappers>
</configuration>
```

映射文件为我们未来自己写的文件

## 创建mapper接口

在mapper包下创建 UserMapper接口

和之前的dao相似，区别在于不需要实现类

```
public interface UserMapper {

    int insertUser();
} 
```





## 创建MyBatis映射文件

在resources目录下创建mapper文件夹，再创建mapper.xml文件

并再mybatis-config文件中写入mapper文件地址	<mapper resource="mapper/UserMapper.xml"/>

```
<mapper namespace="com.ljxxx.mybatis.mapper.UserMapper">
        <!--
            mapper接口和映射文件要保证两个一致:
            2, mapper接口中的方法的方法名要和映射文件中的sqL的id保持一致
             1.mapper接口的全类名和映射文件的namespace一致
       -->

    <!--int insertUser();-->
    <insert id="insertUser">
        insert into t_user values(null,"admin","1234",23,"男","1234@qq.com")
    </insert>
</mapper>
```

1、映射文件的命名规则:
表所对应的实体类的类名+Mapper.xml

例如︰表t_user，映射的实体类为User，所对应的映射文件为UserMapper.xml
因此一个映射文件对应一个实体类，对应一张表的操作

MyBatis映射文件用于编写SQL，访问以及操作表中的数据

MyBatis映射文件存放的位置是src/main/resources/mappers目录下

2、MyBatis中可以面向接口操作数据，要保证两个—致:

a>mapper接口的全类名和映射文件的命名空间(namespace)保持—致
b>mapper接口中方法的方法名和映射文件中编写SQL的标签的id属性保持—致



## 测试添加用户功能(重！！！)

```
@Test
public void testInsert() throws IOException {
    //获取核心配置文件的输入流
    InputStream resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
    //获取sqlSessionFactoryBuilder对象，用于创建sqlSessionFactory工厂
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
    //获取sqlSessionFactory工厂对象
    SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(resourceAsStream);
    //通过工厂获取sql的会话对象sqlSession，是mybatis提供操作数据库的对象
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //因为usermapper没有实现类，通过getMapper方法，mybatis自动帮我们创建实现类
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    int i = mapper.insertUser();
    System.out.println(i);
    //提交事务
    sqlSession.commit();
    //关闭会话
    sqlSession.close();
}
```

sqlsession是操作数据库的对象，必要创建

usermapper没有实现类，所以要用sqlsession对象的getMapper方法创建实现类

### 优化部分

还有一种方法，不需要创建实现类

```
//提供sql以其的唯一标识符找到sql并执行，唯一标识是namespace.sqlId
int insert = sqlSession.insert("com.ljxxx.mybatis.mapper.UserMapper.insertUser");
```

直接执行insert方法，括号内的参数为sql语句的具体位置，可又UserMapper.xml中的sql id值找到

#### 自动提交事务

```
SqlSession sqlSession = sqlSessionFactory.openSession(true);
```

调用openSession的有参方法，true开启自动提交事务，后面就可以把sqlSession.commit()去掉

#### 引入日志

引入依赖和log4j.xml文件

```
<!-- log4j日志 -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
    <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
        <param name="Encoding" value="UTF-8" />
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS}
%m (%F:%L) \n" />
        </layout>
    </appender>
    <logger name="java.sql">
        <level value="debug" />
    </logger>
    <logger name="org.apache.ibatis">
        <level value="info" />
    </logger>
    <root>
        <level value="debug" />
        <appender-ref ref="STDOUT" />
    </root>
</log4j:configuration>
```

日志级别

FATAL(致命)>ERROR(错误)>WARN(警告)>INFO(信息)>DEBUG(调试)

越往右越详细，且打印的信息会包含左边所有级别



## 测试修改删除用户功能（工具类重要）

可先建立工具类，把连接数据库，创建sqlsession的过程挪过去

```
public static SqlSession getSqlSession(){
    SqlSession sqlSession=null;
    try {
        //获取配置文件输入流
        InputStream resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
        //获取SqlSessionFactoryBuilder对象
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(resourceAsStream);
          sqlSession= sqlSessionFactory.openSession(true);
    } catch (IOException e) {
        e.printStackTrace();
    }
    return sqlSession;
}
```

用静态static方法，调用的时候不需要创建类对象，直接类.方法名	可直接调用

且用try catch捕获异常，这样会把异常往外抛出

```
@Test
public void testUpdate(){
    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    mapper.updateUser();
    sqlSession.close();
}
```

## 测试查询用户功能

### 查询单条记录

要在sql中，设置结果类型

resultType:设置结果类型，即查询结果要转换成的java类型
resultMap：自定义映射，用于数据库中字段和pojo类中属性名不同的情况，用于处理多对一和一对多的情况
例如	pojo中叫userId，数据库中叫user_id

```
<!--
        resultType:设置结果类型，即查询结果要转换成的java类型
        resultMap：自定义映射，用于数据库中字段和pojo类中属性名不同的情况，例如pojo中叫userId，数据库中叫user_id
    -->
<!--    User getUserById();-->
<select id="getUserById" resultType="com.ljxxx.mybatis.pojo.User">
    select * from t_user where id=1;
</select>
```

### 查询集合

查询所有User对象

```
<!--    List<User> getAllUser();-->
    <select id="getAllUser" resultType="com.ljxxx.mybatis.pojo.User">
        select * from t_user
    </select>
```

resultType中也写的是com.ljxxx.mybatis.pojo.User，而不是集合

因为要先把查询的结果转换成User对象，再装入集合中

```
List<User> allUser = mapper.getAllUser();
//System.out.println(allUser.toString());
allUser.forEach(System.out::println);
```

输出时有两种写法，第二种为在foreach中调用system.out的prinln方法，固定写法



## 核心配置文件(重)

```
<configuration>
    <!--
        MyBatis核心配置文件中，标签的顺序：
        properties?,settings?,typeAliases?,typeHandlers?,
        objectFactory?,objectWrapperFactory?,reflectorFactory?,
        plugins?,environments?,databaseIdProvider?,mappers?
    -->

    <!--引入properties文件,此后可在当前文件中使用${key}的方式访问value-->
    <properties resource="jdbc.properties" />

    <!--设置类型别名-->
    <typeAliases>
        <!--
        typeAlias：设置某个类型的别名
        属性：
        type：设置需要设置别名的类型
        alias：设置某个类型的别名，若不设置该属性，那么该类型拥有默认的别名，即类名
        且不区分大小写
        -->
        <!--以包为单位，将包下所有的类型设置默认的类型别名，即类名且不区分大小写-->
        <!--<typeAlias type="com.ljxxx.mybatis.pojo.User"></typeAlias>-->
        <package name="com.ljxxx.mybatis.pojo"/>
    </typeAliases>
    <!--配置连接数据库的环境-->
    <environments default="development">
        <!--
        environment：配置某个具体的环境
            属性：
                id：表示连接数据库的环境的唯一标识，不能重复
        -->
        <environment id="development">
            <!--
            transactionManager：设置事务管理方式
                属性：
                type="JDBC|MANAGED"
                JDBC：表示当前环境中，执行SQL时，使用的是JDBC中原生的事务管理方式，事务的提交或回滚需要手动处理
                MANAGED：被管理，例如Spring
        -->
            <transactionManager type="JDBC"/>
            <!--
                dataSource：配置数据源
                    属性：
                        type：设置数据源的类型
                        type="POOLED|UNPOOLED|JNDI"
                            POOLED：表示使用数据库连接池缓存数据库连接
                            UNPOOLED：表示不使用数据库连接池
                            JNDI：表示使用上下文中的数据源
	-->
            <dataSource type="POOLED">
                <property name="driver" value="${mysql.driver}"/>
                <property name="url" value="${mysql.url}"/>
                <property name="username" value="${mysql.username}"/>
                <property name="password" value="${mysql.password}"/>
            </dataSource>
        </environment>
    </environments>

    <!--引入mybatis的映射文件-->
    <mappers>
        <!--<mapper resource="mappers/UserMapper.xml"/>-->
        <!--
        以包为单位引入映射文件
        要求：
        1、mapper接口所在的包要和映射文件所在的包一致
        2、mapper接口要和映射文件的名字一致
        -->
        <!--<mapper resource="mapper/UserMapper.xml"/>-->
        <package name="com.ljxxx.mybatis.mapper"/>
    </mappers>
</configuration>
```

MyBatis核心配置文件中，标签的顺序：
properties?,settings?,typeAliases?,typeHandlers?,objectFactory?,objectWrapperFactory?,reflectorFactory?,
plugins?,environments?,databaseIdProvider?,mappers?

<!--设置类型别名-->
    <typeAliases>
        <!--
        typeAlias：设置某个类型的别名
        属性：
        type：设置需要设置别名的类型
        alias：设置某个类型的别名，若不设置该属性，那么该类型拥有默认的别名，即类名
        且不区分大小写
        -->
        <!--以包为单位，将包下所有的类型设置默认的类型别名，即类名且不区分大小写-->
        <!--<typeAlias type="com.ljxxx.mybatis.pojo.User"></typeAlias>-->
        <package name="com.ljxxx.mybatis.pojo"/>
    </typeAliases>

<!--引入mybatis的映射文件-->
    <mappers>
        <!--<mapper resource="mappers/UserMapper.xml"/>-->
        <!--
        以包为单位引入映射文件
        要求：
        1、mapper接口所在的包要和映射文件所在的包一致
        2、mapper接口要和映射文件的名字一致
        -->
        <!--<mapper resource="mapper/UserMapper.xml"/>-->
        <package name="com.ljxxx.mybatis.mapper"/>
    </mappers>

注意：mapper中如果要用package标签，要在resource中创建文件夹com/ljxxx/mybatis/mapper

一定要用/而不是.，在磁盘中/才是创建文件夹，

且mapper接口所在的包要和映射文件所在的包一致！！！！
因为运行时，java会把resource和src文件夹放在一起

![image-20230221105635366](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230221105635366.png)



## MyBatis获取参数值的两种方式

MyBatis获取参数值的两种方式:${}和#{}

${}的本质就是字符串拼接，#{}的本质就是占位符赋值

$使用字符串拼接的方式拼接sql，若为字符串类型或日期类型的字段进行赋值时，需要手动加单引号;

但是#(}使用占位符赋值的方式拼接sql，此时为字符串类型或日期类型的字段进行赋值时，可以自动添加单引号，可防止sql注入



### 单个字面量（重！！）

在{xxx}大括号内，填什么都能查得到，除了数字特殊字符等等

#### ${}赋值

```
<select id="selectUserByUsername" resultType="User">
    select * from t_user where username = '${username}'
</select>
```

```
User user = mapper.selectUserByUsername("admin");
```

username = '${username}'	要加单引号，因为$不会自动加，

如果不加就会变成select * from t_user where username = admin	，相当于字符串拼接在后面
加了就变成username = ‘admin’，才是赋值！！！

或者用转义字符把单引号转出来！！

```
User user = mapper.selectUserByUsername("\'admin\'");
```

#### #{}赋值

```
<select id="selectUserByUsername" resultType="User">
    select * from t_user where username = #{username}
</select>
```

不需要加单引号，会自动加







### 多个字面量(1)，传入多个参数,只能用于select

若mapper接口方法的参数为多个的字面量类型

此时MyBatis会将参数放在map集合中，以两种方式存储数据

a>以arg0, arg1...为键key，以参数为值value
b>以param1 , param2...为键，以参数为值

因此，只需要通过#{}和${}访问map集合的键，就可以获取相对应的值

用username，passowrd查询用户

```
<select id="checkLog" resultType="User">
	<!--select * from t_user where username = #{arg0} and password = #{arg1}-->
        select * from t_user where username = #{param1} and password = #{param2}
    </select>
```

用$要注意单引号！！！



### 多个字面量(2	重)，传入集合

本质上，mybatis就是帮我们创建一个集合，然后把传过来的参数，放进去

但我们也可以自己建一个集合，传过去，这样mybaits就不会帮我创建集合存放参数

**好处**:键值由我们自己决定，不用写arg0, arg1等

```
User checkLoginByMap(Map<String,Object> map);
```

```
<select id="checkLoginByMap" resultType="User">
    select * from t_user where username = #{username} and password = #{password}
</select>
```

```
map.put("username","admin");
map.put("password","1234");
User user = mapper.checkLoginByMap(map);
```



### 多个字面量(3)，传入实现类，例如User

```
<insert id="insertUser">
    insert into t_user value(null,#{username},#{password},#{age},#{gender},#{email})
</insert>
```

```
User user1 = new User(null, "root", "123456", 20, "男", "1221@qq.com");
int user = mapper.insertUser(user1);
```

实现类和集合有相似之处，属性对应具体值，和key对应value相似

所有可以直接填属性名来传入参数

#### （注意）属性的含义：

只有get set方法的，才会是属性，没有成员变量，但是有getset方法也算属性！！！



### 字面量（注解方式）

用了注解之后，mybatis有两种方法进行存储

a>以@Param注解的value属性值为键，
以参数为值b>以param1 , param2...为键，以参数为值

```
User selectUserByParam(@Param("username") String username, @Param("password") String password);
```

"username"相当于value值，也就是相当于创建了集合，把username当key放入

```
<select id="selectUserByParam" resultType="User">
    select * from t_user where username=#{username} and password=#{password}
</select>
```

```
User admin = mapper.selectUserByParam("admin", "1234");
```

## MyBatis查询功能

### 查询记录数

select Count(*) from xxx，查询表中记录数，包含null	结果假设为5条

select Count(age) from xxx，通过age查询表中记录数，若有一行的age值为null，则不包含	结果就为4条

```
Integer selectCount();
```

```
<select id="selectCount" resultType="int">
    select Count(*) from t_user
</select>
```

MyBatis中为Java中常用的类型设置了类型别名

Integer : Integer，int

int:	 _int	_integer

Map : map

string: string



### 查询单条记录，使用map存储

```
Map<String ,Object> selectAllUserToMap();
```

```
<select id="selectAllUserToMap" resultType="map">
    select * from t_user
</select>
```

可用map集合返回数据

返回值为：{password=1234, gender=男, id=1, age=23, email=1234@qq.com, username=admin}

会把数据库字段名当成键名，具体值为value	

一条数据为一个map集合



### 查询多条记录，使用map存储

如果查询的数据有多条，且每条都要转换成map集合

#### 1.使用List<Map<String,Object>>

```
List<Map<String ,Object>> selectAllUserToList();
```

```
<select id="selectAllUserToList" resultType="map">
    select * from t_user
</select>
```

```
List<Map<String, Object>> list = mapper.selectAllUserToList();
System.out.println(list);
```

### 2.使用@MapKey注解

之所以要用list集合，是因为要把map集合存储在另外一个map集合中，要有key值，而直接存储只会存储到value中	使用注解可以指定key的值

```
@MapKey("id")
Map<String,Object> selectAllUserToMap2();
```

@MapKey("id"),表示用id作为键名	id为数据库中的字段名

输出为：

{1={password=1234, gender=男, id=1, age=23, email=1234@qq.com, username=admin}, 4={password=123456, gender=男, id=4, age=20, email=1221@qq.com, username=root}}



# 特殊SQL查询（#和$）

## 模糊查询（重）

使用通配符%和_	使用操作符like

"%" 百分号通配符: 表示任何字符出现任意次数 (可以是0次)。

"_" 下划线通配符:表示只能匹配单个字符,不能多也不能少,就是一个字符。

查询含有”网“字的数据

select * from xxx where name like ’%网%‘

注：如果like后面没有%_	就等于等号

select * from xxx where name like ’网‘	等于 select * from xxx where name = ’网‘

以网结尾	like '%网'

以网开头	like '网%'

含有xxx网xxx车xxx的字段	like '%网%车%'

以网结尾，三个字的字段	like '__网'



### 问题：

```
List selectUserByLike(@Param("mohu")String mohu);
```

```
<select id="selectUserByLike" resultType="User">
    select * from t_user where username like '%#{mohu}%'
</select>
```

若用#{mohu}进行查询，则会报错

#{}为占位符赋值，会把#{mohu}变成?	在sql语句中为	like '%?%'	

而在sql语句中	'%?%' 是一个字符串，	?无法被占位符解析

### 解决：

```
<!--select * from t_user where username like '%${mohu}%'-->
<!--select * from t_user where username like concat('%',#{mohu},'%')-->
select * from t_user where username like "%"#{mohu}"%"
```

三种解决方法，

第一种直接用${}赋值

第二种	用concat()方法，拼接字符串	‘%’+‘a’+'%'	变成	'%a%'	因为#{}会自动加单引号

第三种	变成	"%" 'a' "%"	也就是'%a%'	也就是不把{mohu}放在字符串中，这样就可以被占位符解析





#### 重！！！！！！！

第一种方法   就是 	like"+" '% " + "a" +" %' "	最后拼接变成	like '%a%'

第三种	就是	like"+ " "%" " + " 'a' " + " "%" "	最后变成	like "%"'a'"%"







## 批量删除

数据库中两种批量删除

delete from t_uer where id=5 or id=6;

delete from t_uer where id in(5,6);

都是删除id为5和6的

第一种用传入多个参数解决，但如果要删除很多行，就得用第二种

第二种：使用拼接的方法	传入字符串"5,6"来完成

传入一个参数

```
void deleteMoreUser(@Param("ids") String ids);
```

```
<delete id="deleteMoreUser">
    delete from t_user where id in(${ids})
</delete>
```

```
mapper.deleteMoreUser("5,6");
```

这里不能用#占位符，因为占位符会自动加单引号，变成id in('5,6')	而不是id in(5，6)

但是如果用mysql老版本，这时不会报错，而是只会删除写的第一个id



## 动态设置表名

```
List selectSetTable(@Param("tableName")String tableName);
```

```
<select id="selectSetTable" resultType="User">
    select * from ${tableName}
</select>
```

```
List user = mapper.selectSetTable("t_user");
user.forEach(System.out::println);
```

也不能用#占位符，因为会自动加单引号，而表名不加单引号



# 特殊SQL语句

## 获取自增主键

创建班级时，通常需要获取刚创建班级的主键，用于添加学生

### 在jdbc中其中的方法

```
Connection connection = DriverManager.getConnection("","","");
String sql="insert into t_user values()";
PreparedStatement preparedStatement = connection.prepareStatement(sql);
preparedStatement.executeUpdate();
ResultSet resultSet = preparedStatement.getGeneratedKeys();
resultSet.next();
int anInt = resultSet.getInt(1);
```

调用getGeneratedKeys()方法，可获得创建的主键，放入resultSet结果集中，

用next方法，让指针往下移动一位，getInt方法	获取指针指向行，(1)为列索引，第一列



### 使用mybatis完成

```
<insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
    insert into t_user values(null,#{username},#{password},#{age},#{gender},#{email})
</insert>
```

```
User user = new User(null, "lisi", "1234", 19, "男", "abc@qq.com");
mapper.insertUser(user);
System.out.println(user.getId());
```

useGeneratedKeys参数：是否使用自增的主键

keyProperty参数：将添加的数据的自增主键为实体类类型的参数的属性赋值

即主键存储在哪个属性中，=”id“	表示存储在user的id属性中

因为增删改的返回值固定，只能是int和void，所以只能把获取到的自增主键放在user中的某个属性中

在new User()中，id为空，但是指向insert之后，输出结果为自增主键值





## 数据库中字段名和类中属性名不一致的情况

字段名就是数据库中的emp_id等等 

![image-20230222185802878](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230222185802878.png)

数据库中字段名为表名_xxx，java pojo类中都是 表明xxx，少了 _

若直接查询	结果为：Emp{empId=null, empName='null', age=20, gender='男'}

只有和类中属性名对上的age和gender能查到

且若多表联查，则怎么把字段名和另外一个表中的属性名对上



### 解决

两种方法：

1，为查询的字段设置别名，和属性名保持一致

```
<select id="selectEmpById" resultType="Emp">
    select emp_id empId,emp_name empName,age,gender from t_emp where emp_id=#{empId}
</select>
```



2，当字段符合MysQL的要求使用_，而属性符合java的要求使用驼峰
此时可以在NyBatis的核心配置文件中设置一个全局配置，可以自动将下划线映射为驼峰

相当于把emp_id——>empId	emp_name ——>empName，把下划线在配置中去掉

mybatis-config配置文件		mapUnderscoreToCamelCase将下划线映射成驼峰，true

```
<settings>
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```



3.使用ResultMap实现自定义映射

resultMap:设置自定义的映射关系

id:唯一标识

type:处理映射关系的实体类的类型

常用的标签
​id:处理主键和实体类中属性的映射关系
result:处理普通字段和实体类中属性的映射关系

column :设置映射关系中的字段名，必须是sql查询出的某个字段
property:设置映射关系中的属性的属性名，必须是处理的实体类类型中的属性名

```
<resultMap id="empResultMap" type="Emp">
    <id property="empId" column="emp_id"></id>
    <result property="empName" column="emp_name"></result>
    <result property="age" column="age"></result>
    <result property="gender" column="gender"></result>
</resultMap>

<select id="selectEmpById" resultMap="empResultMap">
    select * from t_emp where emp_id=#{empId}
</select>
```

自己写一个resultMap，type为要映射的对象类



### 多表联查

有A,B两个表	A公司表，B员工表

四种情况 联结，左连接，右连接，内连接

#### 联结

从两张表中查询

SELECT a.name , b.name ,b.sal, 

FROM A, B

WHERE A.id = B.comid;

#### 左连接

SELECT * FROM A LEFT JOIN B  

ON A.id = B.comid

左表,即A表	满足条件的记录会查询出来、左表中【不满足】条件的也会查出来，运行结果也以左表为主。



#### 右连接

SELECT * FROM A RIGHT JOIN B  

ON A.id = B.comid

右表,即B表	满足条件的记录会查询出来、左表中【不满足】条件的也会查出来，运行结果也以左表为主。



内连接

SELECT * FROM A INNER JOIN B  

ON A.id = B.comid

查询的是满足条件的记录



### 多对一的查询

回到emp和dept表中

想要查询emp表中所有数据，包含dept表中数据，要使用多表联查

```
<select id="selectEmpAndDeptById" resultMap="empResultMap">
    select * from t_emp LEFT JOIN t_dept on t_emp.dept_id=t_dept.dept_id where emp_id=#{empId}
</select>
```

select * from t_emp LEFT JOIN t_dept on t_emp.dept_id=t_dept.dept_id where emp_id=#{empId}

on t_emp.dept_id=t_dept.dept_id	on后面写的是条件 t_emp表中的dept_id和t_dept表中的dept_id相同

结果为

![image-20230222195053273](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230222195053273.png)

有两个dept_id

加个select  t_emp.*

t_dept.*



Student{stuId=2, stuName='tom', age=19, gender='男', clazz=Clazz{claId=2, claName='2班'}}



#### 方法1	级联方式

因为在emp.class中，dept为这样设置

```
private Dept dept;
```

所以在resultMap property中直接 dept.属性 column对应的字段名

```
<resultMap id="empAndDeptResultMap" type="Emp">
    <id property="empId" column="emp_id"></id>
    <result property="empName" column="emp_name"></result>
    <result property="age" column="age"></result>
    <result property="gender" column="gender"></result>
    <result property="dept.deptId" column="dept_id"></result>
    <result property="dept.deptName" column="dept_name"></result>
</resultMap>
```

#### 方法2	assciation

association :处理多对一的映射关系(处理实体类类型的属性)，需要设置javaType类型 得要映射的实体类

```
<resultMap id="empAndDeptResultMap" type="Emp">
    <id property="empId" column="emp_id"></id>
    <result property="empName" column="emp_name"></result>
    <result property="age" column="age"></result>
    <result property="gender" column="gender"></result>
    <association property="dept" javaType="Dept">
        <id property="deptId" column="dept_id"></id>
        <result property="deptName" column="dept_name"></result>
    </association>
</resultMap>
```

#### 方法3	分步查询（重）

好处就是	降低耦合度

思路就是 分成两步查询，先查询t_emp中所有的数据，再查询t_dept中所有的数据

先在EmpMapper.xml中查询数据

```
<select id="selectEmpAndDeptByStepOne" resultMap="empAndDeptByStepResultMap">
    select * from t_emp where emp_id=#{empId}
</select>
```

在DeptMapper.xml中写好第二步查询的方法

```
Dept selectEmpAndDeptByStepTwo(@Param("deptId")String deptId);
```

```
<select id="selectEmpAndDeptByStepTwo" resultType="Dept">
    select * from t_dept where dept_id=#{deptId}
</select>
```

再写ResultMap

```
<resultMap id="empAndDeptByStepResultMap" type="Emp">
    <id property="empId" column="emp_id"></id>
    <result property="empName" column="emp_name"></result>
    <result property="age" column="age"></result>
    <result property="gender" column="gender"></result>
    <association property="dept"
                 select="com.ljxxx.mybatis.mapper.DeptMapper.selectEmpAndDeptByStepTwo"
                 column="dept_id">
    </association>
</resultMap>
```

association中 property写的是selectEmpAndDeptByStepTwo方法的返回值
即第二步查出数据的类型

但是这里不能是由第二步来决定property中该写什么类型

因为在Emp.class中

```
private Dept dept;
```

第一步查出来的数据类型就为dept类型，所以只能property中只能写dept！！！

select为第二步查询sql语句的唯一标识符 即id

column 将查询出的某个字段作为分步查询的sql的条件 为第二步查询所要的参数



### 延迟加载

若使用分步查询，只想查emp表中信息	emp.getEmpName()	而不需要查dept中的数据，但每次都会执行dept的sql语句，就需要开启分步查询

```
<!--开启延迟加载-->
    <setting name="lazyLoadingEnabled" value="true"/>
<!--按需加载-->
    <setting name="aggressiveLazyLoading" value="false"/>
```

lazyLoadingEnabled	默认值为false	aggressiveLazyLoading 默认值就为false	表示是否全局加载，若为true，则会把emp和dept中查到的数据全加载出来

这时，若在test类中只获取了dept类的属性，sout(dept.getDeptName()),就只会执行一个sql语句

若想在某些sql单独开启全局加载

```
<association property="dept" fetchType="eager"
```

fetchType标签	在开启延迟加载的环境下，通过标签设置当前的分步查询是否要使用延迟加载

eager立即加载	lazy延迟加载



### 一对多的查询

#### 方法1.collection

即部门对员工，一个部门有多个员工

和多对一不同，多对一用的是	实现类

```
private Dept dept;
```

而一对多是	集合

```
private List<Emp> emps;
```

使用collection标签

oftype：设置集合类型中的属性，存储的数据类型

```
<resultMap id="empAndDeptResultMap" type="Dept">
    <id property="deptId" column="dept_id"></id>
    <result property="deptName" column="dept_name"></result>
    <collection property="emps" ofType="Emp">
        <id property="empId" column="emp_id"></id>
        <result property="empName" column="emp_name"></result>
        <result property="age" column="age"></result>
        <result property="gender" column="gender"></result>
    </collection>

</resultMap>

<select id="selectEmpAndDeptByDeptId" resultMap="empAndDeptResultMap">
    select * from t_dept LEFT JOIN t_emp on t_dept.dept_id=t_emp.dept_id where t_dept.dept_id=#{deptId}
</select>
```



#### 方法2 分步查询（重）

先写 查询emp表中数据

注意 这里是List<Emp>集合，因为是一对多，一个部门对应多个员工，且参数为deptId，而不是empId
因为要通过查询到的deptId来找到员工信息！！！

```
List<Emp> selectDeptAndEmpByStepTwo(@Param("deptId")Integer deptId);
```

```
<select id="selectDeptAndEmpByStepTwo" resultType="Emp">
    select * from t_emp where dept_id=#{deptId}
</select>
```

再写查询dept表中数据

```
Dept selectDeptAndEmpByStepOne(@Param("deptId")Integer deptId);
```

```
<resultMap id="deptAndEmpByStepResultMap" type="Dept">
    <id property="deptId" column="dept_id"></id>
    <result property="deptName" column="dept_name"></result>
    <collection property="emps"
                select="com.ljxxx.mybatis.mapper.EmpMapper.selectDeptAndEmpByStepTwo"
                column="dept_id"
    ></collection>

</resultMap>
<select id="selectDeptAndEmpByStepOne" resultMap="deptAndEmpByStepResultMap">
    select * from t_dept where dept_id=#{deptId}
</select>
```



# 动态sql

Mybatis框架的动态SQL技术是一种根据特定条件动态拼装SQL语句的功能，它存在的意义是为了解决拼接SQL语句字符串时的痛点问题。

## 场景：多条件查询

网页有表单，表单中有姓名，年龄，性别，用于查询数据

可能用户只写了姓名或性别，其余为空，则需要动态sql

## if标签

```
List<Emp> getEmpByCondition(Emp emp);
```

```
<select id="getEmpByCondition" resultType="Emp">
    select * from t_emp where 
    <if test="empName != '' and empName != null">
        emp_name=#{empName}
    </if>
    <if test="age != '' and age != null">
        and age=#{age}
    </if>
    <if test="gender != '' and gender != null">
        and gender=#{gender}
    </if>
</select>
```

使用if标签	通过test属性中的表达式判断标签中的内容是否有效（是否会拼接到sql）

```
Emp emp = new Emp(null,"张三",null,null);
List<Emp> empByCondition = mapper.getEmpByCondition(emp);
System.out.println(empByCondition);
```

输出时，创建emp对象，把要查询的条件填出



## 问题

若name为空，这样sql语句就变成 select * from t_emp where and age=? 	错×

## 解决

在where 后加个1=1恒等式，再在name前加个and

```
<select id="getEmpByCondition" resultType="Emp">
    select * from t_emp where 1=1
    <if test="empName != '' and empName != null">
        and emp_name=#{empName}
    </if>
    <if test="age != '' and age != null">
        and age=#{age}
    </if>
    <if test="gender != '' and gender != null">
        and gender=#{gender}
    </if>
</select>
```

### 或者使用where标签

### 或者使用trim标签

## where标签

通常和if标签一起使用

```
<where>
<if test="empName != '' and empName != null">
     emp_name=#{empName}
</if>
<if test="age != '' and age != null">
    and age=#{age}
</if>
<if test="gender != '' and gender != null">
    and gender=#{gender}
</if>
</where>
```

### 作用

1.若where标签中有条件成立，会自动生成where关键字

2.会自动将where标签中内容前多余的and去掉

即若name为空，则正常sql语句变成select * from t_emp where and age=?
但where标签会自动去掉多余and	变成 select * from t_emp where age=?

***但内容后多余的and不能去掉！！！***

3.若where标签中没有任何一个条件成立，则where没有任何功能



## trim标签

用于在标签前后添加删除内容

prefix，suffix:在标签中内容前面或后面添加指定内容
prefixoverrides.，suffixoverrides:在标签中内容前面或后面去掉指定内容

第一种suffixOverrides	把and放在前面

会自动帮我们去掉and

```
<select id="getEmpByCondition" resultType="Emp">
    select * from t_emp
    <trim prefix="where" suffixOverrides="and">
    <if test="empName != '' and empName != null">
         emp_name=#{empName}
    </if>
    <if test="age != '' and age != null">
        and age=#{age} 
    </if>
    <if test="gender != '' and gender != null">
       	and gender=#{gender}
    </if>
    </trim>
</select>
```

第二种suffixoverrides	把and放在后面

会自动帮我们去掉and

```
<select id="getEmpByCondition" resultType="Emp">
    select * from t_emp
    <trim prefix="where" suffixOverrides="and">
        <if test="empName != '' and empName != null">
            emp_name=#{empName}
        </if>
        <if test="age != '' and age != null">
            and age=#{age}
        </if>
        <if test="gender != '' and gender != null">
            and gender=#{gender}
        </if>
    </trim>
</select>
```



## Choose，when，otherwise标签

choose 相当于 switch

when 相当于 if语句	choose中只能写when，不能写if标签

otherwise 相当于 default	用于所有when都不满足，做什么



通过name或者age或者gender查询emp数据

```
<select id="getEmpByChoose" resultType="Emp">
    select * from t_emp where 
    <where>
    <choose>
        <when test="empName != '' and empName != null">
            emp_name=#{empName}
        </when>
        <when test="age != '' and age != null">
            age=#{age}
        </when>
        <when test="gender != '' and gender != null">
            gender=#{gender}
        </when>
        <otherwise>
            1=1
        </otherwise>
    </choose>
    </where>
</select>
```

switch default结构一样，且自动在最后加上break，所有一个满足直接执行跳出！！！





## foreach标签

### 批量添加

sql语句为 insert into t_emp values(),(),()

传入的参数为数组，所以要用foreach标签进行循环输出

```
void insertMoreEmp(@Param("emps") List<Emp> emps);
```

```
<insert id="insertMoreEmp">
    insert into t_emp values
    <foreach collection="emps" item="emp" separator=",">
        (null,#{emp.empName},#{emp.age},#{emp.gender},null)
    </foreach>
</insert>
```

collection标签 设置要循环的数组或集合

item标签 当前循环的对象

separator标签 分隔符	表示每次循环之间用什么分隔！！



### 批量删除

sql语句为 delete from t_emp where emp_id in ()

```
delete from t_emp where emp_id in
(
<foreach collection="empIds" item="empId" separator=",">
    #{empId}
</foreach>
)
```



或者用

```
delete from t_emp where emp_id in
<foreach collection="empIds" item="empId" separator="," open="(" close=")">
    #{empId}
</foreach>
```

open标签，设置循环以什么开始（不是每次循环，而是在所有内容前）

close标签，设置循环以什么结束



## Sql标签

sql片段，用于记录一段sql语句，在需要的地方用include标签引入

```
<sql id="empColumns">
    emp_id,emp_name,age,gender
</sql>
<select id="getEmpByCondition" resultType="Emp">
        select <include refid="empColumns"></include> from t_emp
```

refid中	写sql标签的id值

注意，sql标签是记录一段sql语句，是任何sql语句，放在sql语句中的哪都行



# Mybatis缓存

## 一级缓存（默认开启）

—级缓存是SqlSession级别的，通过同一个SqlSession查询的数据会被缓存，下次查询相同的数据，就会从缓存中直接获取，不会从数据库重新访问

```
SqlSession sqlSession = SqlSessionUtil.getSqlSession();
CacheMapper mapper = sqlSession.getMapper(CacheMapper.class);
Emp emp1 = mapper.getEmpById(1);
Emp emp2 = mapper.getEmpById(1);
System.out.println(emp1);
System.out.println(emp2);

SqlSession sqlSession1 = SqlSessionUtil.getSqlSession();
CacheMapper mapper1 = sqlSession1.getMapper(CacheMapper.class);
Emp emp3= mapper1.getEmpById(1);
System.out.println(emp3);
```

得到三条查询结果，执行了两次sql语句

```
DEBUG 02-24 13:37:30,003==>  Preparing: select * from t_emp where emp_id=? (BaseJdbcLogger.java:137)
        DEBUG 02-24 13:37:30,028==> Parameters: 1(Integer) (BaseJdbcLogger.java:137)
        DEBUG 02-24 13:37:30,040<==      Total: 1 (BaseJdbcLogger.java:137)
        Emp{empId=1, empName='张三', age=20, gender='男'}
        Emp{empId=1, empName='张三', age=20, gender='男'}
        DEBUG 02-24 13:37:30,071==>  Preparing: select * from t_emp where emp_id=? (BaseJdbcLogger.java:137)
        DEBUG 02-24 13:37:30,071==> Parameters: 1(Integer) (BaseJdbcLogger.java:137)
        DEBUG 02-24 13:37:30,072<==      Total: 1 (BaseJdbcLogger.java:137)
        Emp{empId=1, empName='张三', age=20, gender='男'}
```

证明一级缓存是存放在SqlSession中，若新建一个sqlsession，则找不到缓存



### 使一级缓存失效的四种情况:

1)不同的sqlsession对应不同的一级缓存

2)同一个sqlsession但是查询条件不同

3)同一个sqLsession两次查询期间执行了任何一次增删改操作

增删改操作可能会影响之前查询过的数据，所以会自动清空缓存！！

4)同一个sqlsession两次查询期间手动清空了缓存

```
Emp emp1 = mapper.getEmpById(1);
sqlSession.clearCache();
Emp emp2 = mapper.getEmpById(1);
System.out.println(emp1);
System.out.println(emp2);
```



## Mybatis二级缓存

二级缓存是SqlSessionFactory级别，通过同一个SqlSessionFactory创建的SqlSession查询的结果会被缓存;此后若再次执行相同的查询语句，结果就会从缓存中获取

sqlSession是由SqlSessionFactory创建的

### 二级缓存开启的条件:

a>在核心配置文件中，设置全局配置属性cacheEnabled="true"，默认为true，不需要设置

```
<!--开启二级缓存(默认值为true)-->
    <setting name="cacheEnabled" value="true"/>
```

b>在映射文件中设置标签<cache/>

```
<mapper namespace="com.ljxxx.mybatis.mapper.CacheMapper">
    <cache/>
```

c>二级缓存必须在SqlSession关闭或提交之后有效

因为二级缓存要用同一个sqlSessionFactory创建的sqlsession，所以不能用工具类，要自己写一个

sqlSession1.close();	关闭sqlSession1

```
public void testCache() throws Exception {
    InputStream resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
    SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(resourceAsStream);

    SqlSession sqlSession1 = sqlSessionFactory.openSession(true);
    CacheMapper mapper1 = sqlSession1.getMapper(CacheMapper.class);
    Emp emp1 = mapper1.getEmpById(1);
    System.out.println(emp1);
    sqlSession1.close();

    SqlSession sqlSession2 = sqlSessionFactory.openSession(true);
    CacheMapper mapper2 = sqlSession2.getMapper(CacheMapper.class);
    Emp emp2 = mapper2.getEmpById(1);
    System.out.println(emp2);
    sqlSession2.close();
}
```



d>查询的数据所转换的实体类类型必须实现序列化的接口

```
public class Emp implements Serializable{
```



### 使二级缓存失效的情况:

两次查询之间执行了任意的增删改，会使一级和二级缓存同时失效

执行sqlsession.clearCache()	只会清空一级缓存，不清空二级缓存

### 二级缓存的相关配置(了解一下)

eviction属性:缓存回收策略，默认的是 LRU。

LRU (Least Recently Used)-最近最少使用的:移除最长时间不被使用的对象。
FIFO (First in First out)-先进先出:按对象进入缓存的顺序来移除它们。
SOFT-软引用:移除基于垃圾回收器状态和软引用规则的对象。
WEAK–弱引用:更积极地移除基于垃圾收集器状态和弱引用规则的对象。

②flushlnterval属性:刷新间隔，单位毫秒
默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句时刷新

③size属性:引用数目，正整数
代表缓存最多可以存储多少个对象，太大容易导致内存溢出

④readOnly属性:只读, true/false
true:只读缓存;会给所有调用者返回缓存对象的相同实例。因此这些对象不能被修改。这提供了很重要的性能优势。
false:读写缓存;会返回缓存对象的拷贝(通过序列化)。这会慢一些，但是安全，因此**默认是false。**



## MyBatis缓存查询的顺序

先查询二级缓存，因为二级缓存中可能会有其他程序已经查出来的数据，可以拿来直接使用。

如果二级缓存没有命中，再查询—级缓存

如果一级缓存也没有命中，则查询数据库

sqlSession关闭之后，一级缓存中的教据会写入二级缓存

所以二级缓存的本质也就是收取一级缓存的数据



## 整合第三方缓存EHCache

### 引入依赖

```
<!-- Mybatis EHCache整合包 -->
<dependency>
    <groupId>org.mybatis.caches</groupId>
    <artifactId>mybatis-ehcache</artifactId>
    <version>1.2.1</version>
</dependency>
<!-- slf4j日志门面的一个具体实现 -->
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

mybatis-ehcache 		    Mybatis和EHCache的整合包

ehcache 						   EHCache核心包

slf4j-api 							SLF4J日志门面包

logback-classic 				支持SLF4J门面接口的一个具体实现

EHCache中包含了SLF4J日志jar包



### 创建ehcache.xml文件

```
<?xml version="1.0" encoding="utf-8" ?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="../config/ehcache.xsd">
    <!-- 磁盘保存路径 -->
    <diskStore path="D:\javamodule\SSM\mybatis_cache\cache"/>
    <defaultCache
            maxElementsInMemory="1000"
            maxElementsOnDisk="10000000"
            eternal="false"
            overflowToDisk="true"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
    </defaultCache>
</ehcache>
```

### 设置二级缓存的类型

```
<cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
```

### 加入logback日志文件

注：存在SLF4J时，作为简易日志的log4j将失效，此时我们需要借助SLF4J的具体实现logback来打印日志

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">
    <!-- 指定日志输出的位置 -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <!-- 日志输出的格式 -->
            <!-- 按照顺序分别是： 时间、日志级别、线程名称、打印日志的类、日志主体内容、换行
            -->
            <pattern>[%d{HH:mm:ss.SSS}] [%-5level] [%thread] [%logger]
                [%msg]%n</pattern>
        </encoder>
    </appender>
    <!-- 设置全局日志级别。日志级别按顺序分别是： DEBUG、INFO、WARN、ERROR -->
    <!-- 指定任何一个日志级别都只打印当前级别和后面级别的日志。 -->
    <root level="DEBUG">
        <!-- 指定打印日志的appender，这里通过“STDOUT”引用了前面配置的appender -->
        <appender-ref ref="STDOUT" />
    </root>
    <!-- 根据特殊需求指定局部日志级别 -->
    <logger name="com.atguigu.mybatis.mapper" level="DEBUG"/>
</configuration>
```



# 逆向工程

正向工程：先创建Java实体类，由框架负责根据实体类生成数据库表。 Hibernate是支持正向工程的。

逆向工程：先创建数据库表，由框架负责根据数据库表，反向生成如下资源：

Java实体类

Mapper接口

Mapper映射文件



## 1.添加依赖和插件

```
<dependencies>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.7</version>
    </dependency>
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    <!-- log4j日志 -->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.16</version>
    </dependency>
</dependencies>
<!-- 控制Maven在构建过程中相关配置 -->
<build>
<!-- 构建过程中用到的插件 -->
<plugins>
    <!-- 具体插件，逆向工程的操作是以构建过程中插件形式出现的 -->
    <plugin>
        <groupId>org.mybatis.generator</groupId>
        <artifactId>mybatis-generator-maven-plugin</artifactId>
        <version>1.3.0</version>
        <!-- 插件的依赖 -->
        <dependencies>
            <!-- 逆向工程的核心依赖 -->
            <dependency>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-core</artifactId>
                <version>1.3.2</version>
            </dependency>
            <!-- MySQL驱动 -->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>8.0.16</version>
            </dependency>
        </dependencies>
    </plugin>
</plugins>
</build>
```

## 2.创建MyBatis的核心配置文件



## 3.创建逆向工程的配置文件

文件名必须是：generatorConfig.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!--
    targetRuntime: 执行生成的逆向工程的版本
    MyBatis3Simple: 生成基本的CRUD（清新简洁版）
    MyBatis3: 生成带条件的CRUD（奢华尊享版）
    -->
    <context id="DB2Tables" targetRuntime="MyBatis3Simple">
        <!-- 数据库的连接信息 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC"
                        userId="root"
                        password="1234">
        <property name="useInformationSchema" value="true"/>
        </jdbcConnection>
        <!-- javaBean的生成策略-->
<!--        targetPackage：生成bean的包位置
            targetProject：生成java的项目位置
-->
        <javaModelGenerator targetPackage="com.ljxxx.mybatis.pojo" targetProject=".\src\main\java">
        <!--是否使用子包，若为false，则包名的逗号不能被解析-->
            <property name="enableSubPackages" value="true" />
        <!--截取数据库中字段名前后的空格，用于生成实体类中的属性-->
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        <!-- SQL映射文件的生成策略 -->
        <sqlMapGenerator targetPackage="com.ljxxx.mybatis.mapper"
                         targetProject=".\src\main\resources">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>
        <!-- Mapper接口的生成策略 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.ljxxx.mybatis.mapper" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>
        <!-- 逆向分析的表 -->
        <!-- tableName设置为*号，可以对应所有表，此时不写domainObjectName -->
        <!-- domainObjectName属性指定生成出来的实体类的类名 -->
        <table tableName="t_emp" domainObjectName="Emp"/>
        <table tableName="t_dept" domainObjectName="Dept"/>
    </context>
</generatorConfiguration>
```

<context id="DB2Tables" targetRuntime="MyBatis3Simple">为普通版，
只能生成五个sql，增删改查询单个，查询所有

connectionURL="jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC"

要写上?serverTimezone=UTC	不然会提示时区错误

***会自动调用驼峰***

## 4.执行MBG插件的generate目标

![image-20230225155543800](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230225155543800.png)

## 逆向工程尊享版

把逆向工程配置文件改为<context id="DB2Tables" targetRuntime="MyBatis3">

这样会生成多个sql

和普通版的区别就是多了根据条件增删改功能

并多了EmpExample类，用于写入条件

### 查询

```
//根据id查询
//Emp emp = mapper.selectByPrimaryKey(1);
//System.out.println(emp);
//查询所有数据
//List<Emp> emps = mapper.selectByExample(null);
//emps.forEach(System.out::println);
//根据条件查询数据  查询名字为张三,年龄为20，或者性别为男的
//EmpExample empExample = new EmpExample();
//empExample.createCriteria().andEmpNameEqualTo("张三").andAgeEqualTo(20);
//empExample.or().andGenderEqualTo("男");
//List<Emp> emps1 = mapper.selectByExample(empExample);
//emps1.forEach(System.out::println);
```

查询所有数据，就是没有条件，所以条件写为null

而createCriteria()方法，创建条件，后面会自动用and连接

而用or()方法，后面会自动用or连接

### 修改

```
//普通修改功能		根据主键修改
Emp emp = new Emp(1,"小黑",null,"男");
//mapper.updateByPrimaryKey(emp);
//选择修改功能		根据主键和条件修改
mapper.updateByPrimaryKeySelective(emp);
```

区别在于，普通修改，若数据库中age不为空，则会把null值赋给数据库中的age

但选择修改，若数据库中age不为空，则不修改，保持原值



### 注意：不管是普通版还是尊享版，都只是对单表





# 分页插件

limit index,pageSize

pageSize：每页显示的条数

pageNum：当前页的页码

index：当前页的起始索引，即当前页第一个数据的索引，index=(pageNum-1)*pageSize

count：总记录数

totalPage：总页数

totalPage = count / pageSize;

if(count % pageSize != 0){

totalPage += 1;

}

pageSize=4，pageNum=1，index=0 limit 0,4

pageSize=4，pageNum=3，index=8 limit 8,4

pageSize=4，pageNum=6，index=20 limit 8,4



## 分页相关数据

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



## 1.添加依赖

```
<!--分页插件-->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.2.0</version>
</dependency>
```



## 2.配置分页插件

***在mybatis配置文件中配置***

```
<plugins>
    <!--设置分页插件-->
    <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
```

且要注意标签顺序



## 3.使用分页插件

在查询功能之前使用PageHelper.startPage(int pageNum, int pageSize)开启分页功能

```
@Test
public void testPage(){
    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    //开启分页插件
    PageHelper.startPage(1,4);
    List<Emp> emps = mapper.selectByExample(null);
    emps.forEach(System.out::println);
}
```

startPage( ，)方法	第一个参数pageNum：当前页的页码	第二个参数pageSize：每页显示的条数

输出结果为

```
Emp{empId=1, empName='小黑', age=20, gender='男', deptId=null}
        Emp{empId=2, empName='李四', age=21, gender='男', deptId=2}
        Emp{empId=3, empName='王五', age=22, gender='男', deptId=3}
        Emp{empId=4, empName='赵六', age=23, gender='男', deptId=1}
```

只输出了前四条



## 获取分页相关数据

```
//查询之前开启分页插件
Page<Object> page = PageHelper.startPage(2, 4);
List<Emp> emps = mapper.selectByExample(null);
//查询之后,获取分页相关的所有数据
PageInfo<Emp> pageInfo = new PageInfo<>(emps,3);
System.out.println(page);
System.out.println(pageInfo);
emps.forEach(System.out::println);
```

查询之前的page对象中，有一部分的分页相关数据

pageInfo对象中有所有分页相关的数据，PageInfo<>(emps,3)方法
第一个参数为，查询结果的集合，
第二个参数为：导航分页的页码数，即当前导航页 首页 上一页 xxx 下一页 末页	其中xxx需要显示几页，若写为3，则显示首页 上一页 1，2，3 下一页 末页

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