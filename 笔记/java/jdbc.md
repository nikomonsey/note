## 编写步骤

1.注册驱动				加载Driver类

Driver 每个驱动程序类必须实现的端口

方法 connect(url,properties)	获取连接    尝试使数据库连接到给定的url中，若错误 返回null，正确 抛出sqlexception

### url

类似网络连接的方式

jdbc:mysql://规定好的协议，通过jdbc方法连接mysql数据库

localhost 主机，若数据库不在主机，写ip

3306，mysql监听的端口

String url="jdbc:mysql://localhost:3306/mybatis";

### properties

创建properties对象 将用户名密码放入

Properties properties = new Properties();

user和password为固定

properties.setProperty("user","root");

properties.setProperty("password","1234");



2.获取连接				得到Connection



Connection connect = driver.connect(url, properties);





3.执行增删改查		发送SQL给mysql执行

String sql="insert into actor value(null,'ljxxx','男','2001-12-12','112')";

statement用于执行静态sql语句，并返回生成的结果的对象

创建statement对象

Statement statement = connect.createStatement();

rows表示影响的行数

int rows = statement.executeUpdate(sql);		executeUpdate返回值为影响的行数

System.out.println(rows >0 ? "正确":"错误");

4.释放资源

statement.close();
connect.close();





## 连接方式一

```
//        1.注册驱动             加载Driver类
        Driver driver = new Driver();

//        2.获取连接            得到Connection
        /*
        类似网络连接的方式
        jdbc:mysql://规定好的协议，通过jdbc方法连接mysql数据库
        localhost 主机，若数据库不在主机，写ip
        3306，mysql监听的端口
        */
        String url="jdbc:mysql://localhost:3306/mybatis";
        //创建properties对象 将用户名密码放入
        Properties properties = new Properties();
        //user和password固定
        properties.setProperty("user","root");
        properties.setProperty("password","1234");

        Connection connect = driver.connect(url, properties);

//        3.执行增删改查    发送SQL给mysql执行
        String sql="insert into actor value(null,'ljxxx','男','2001-12-12','112')";
        //用于执行静态sql语句，并返回生成的结果的对象
        Statement statement = connect.createStatement();
        //rows表示影响的行数
        int rows = statement.executeUpdate(sql);
        System.out.println(rows >0 ? "正确":"错误");
//        4.释放资源
        statement.close();
        connect.close();
```



## 方式二

使用反射类加载Driver类 动态加载 减少依赖性，更加灵活

```
//使用反射类加载Driver类 动态加载 减少依赖性，更加灵活
Class<?> aClass = Class.forName("com.mysql.jdbc.Driver");
//通过 Class 类的 newInstance() 方法创建对象
Driver driver = (Driver) aClass.newInstance();

String url="jdbc:mysql://localhost:3306/mybatis";
Properties properties = new Properties();
//user和password固定
properties.setProperty("user","root");
properties.setProperty("password","1234");
Connection connect = driver.connect(url, properties);
```

区别在于

Class<?> aClass = Class.forName("com.mysql.jdbc.Driver");

Driver driver = (Driver) aClass.newInstance();

等于

Driver driver = new Driver(“com.mysql.jdbc.Driver”);



通过 Class 类的 newInstance() 方法创建对象



反射类Class.forName()要写完整类的地址

## 方法三

使用DriverManager替代driver

```
Class<?> aClass = Class.forName("com.mysql.jdbc.Driver");
Driver driver = (Driver) aClass.newInstance();
String url="jdbc:mysql://localhost:3306/mybatis";
String user="root";
String password="1234";

DriverManager.registerDriver(driver);	//注册driver驱动
DriverManager.getConnection(url,user,password);
```

DriverManager.registerDriver()用于注册给定的jdbc驱动程序 如driver

优点

可有更多拓展方法 更方便管理

## 方法四

```
Class.forName("com.mysql.jdbc.Driver");
String url="jdbc:mysql://localhost:3306/mybatis";
String user="root";
String password="1234";
Connection connection = DriverManager.getConnection(url, user, password);
System.out.println("第四种方法"+connection);
```

Class.forName()方法 可自动注册

跳过DriverManager.registerDriver(driver);

直接使用DriverManager



## 方法五

```
Properties properties = new Properties();
properties.load(new FileInputStream("src/mysql.properties"));

String url=properties.getProperty("mysql.url");
String user=properties.getProperty("mysql.username");
String password=properties.getProperty("mysql.password");
String driver=properties.getProperty("mysql.driver");
Class.forName(driver);
Connection connection = DriverManager.getConnection(url, user, password);
System.out.println("第五种方法"+connection);
```

在方法四之上	通过建立读取mysql.properties文件来简化



## ResultSet

```
Properties properties = new Properties();
properties.load(new FileInputStream("src/mysql.properties"));

String url=properties.getProperty("mysql.url");
String user=properties.getProperty("mysql.username");
String password=properties.getProperty("mysql.password");
String driver=properties.getProperty("mysql.driver");
Class.forName(driver);
Connection connection = DriverManager.getConnection(url, user, password);


Statement statement = connection.createStatement();



String sql="select id,name,sex,borndate from actor";


ResultSet resultSet = statement.executeQuery(sql);

while (resultSet.next()){
    int id = resultSet.getInt(1);
    String name = resultSet.getString(2);
    String sex = resultSet.getString(3);
    String borndate = resultSet.getString(4);
    System.out.println(id + "\t"+name + "\t" + sex + "\t" + borndate);
}

resultSet.close();
statement.close();
connection.close();
```

executeQuery执行查询语句，返回结果集ResultSet对象

ResultSet

一次返回一个对象，

一开始指向数据上面一行

ResultSet.next()指向下一行

当resultSet.next()为空时 表示数据查询完毕  返回false

resultSet.getInt(1);		  获取该行第1列数据

resultSet.getInt(“id”);	  通过列名来获取数据 

resultSet.getString(2);	获取该行第2列数据

resultSet.getObject(2);	返回object类型

1	ljxxx	   男	2001-12-12	 112

2	刘德华	男	1997-12-12 	120



ResultSet.previous()	指向上一行





建立连接之后对数据库访问

Statement[存在SQL注入问题]

PreparedStatement[预处理]

CallableStatement[存储过程]

## Statement

```
Scanner scanner = new Scanner(System.in);
    System.out.print("请输入用户名:");
    String admin_name=scanner.nextLine();
    System.out.print("请输入密码:");
    String admin_pwd=scanner.nextLine();
    

    Properties properties = new Properties();
    properties.load(new FileInputStream("src/mysql.properties"));
    String url=properties.getProperty("mysql.url");
    String user=properties.getProperty("mysql.username");
    String password=properties.getProperty("mysql.password");
    String driver=properties.getProperty("mysql.driver");
    Class.forName(driver);
    Connection connection = DriverManager.getConnection(url, user, password);
    Statement statement = connection.createStatement();
    String sql="select name, pwd from admin where name='"
            + admin_name +"' and pwd='" +admin_pwd+ "'";
    ResultSet resultSet = statement.executeQuery(sql);
    if (resultSet.next()){
        System.out.println("恭喜，登陆成功");
    }else System.out.println("对不起，登陆失败");
}
```



String sql="select name,pwd from admin where name=’" + admin_name + "‘ and pwd=’" +admin_pwd+ "‘";

admin_name，admin_pwd为我们设定的参数

这时要加上+号



用户名	1'or

数据库中万能密码： or '1'='1

这时就是sql注入

sql语句为

"select name,pwd from admin where name=‘1’or’ and pwd=‘’or '1'='1";



## PreparedStatement

```
Scanner scanner = new Scanner(System.in);
    System.out.print("请输入用户名:");
    String admin_name = scanner.nextLine();
    System.out.print("请输入密码:");
    String admin_pwd = scanner.nextLine();

    Properties properties = new Properties();
    properties.load(new FileInputStream("src/mysql.properties"));
    String url = properties.getProperty("mysql.url");
    String user = properties.getProperty("mysql.username");
    String password = properties.getProperty("mysql.password");
    String driver = properties.getProperty("mysql.driver");
    Class.forName(driver);
    Connection connection = DriverManager.getConnection(url, user, password);
    String sql="select name , pwd  from admin where name = ? and pwd = ? ";
    PreparedStatement preparedStatement = connection.prepareStatement(sql);
    preparedStatement.setString(1,admin_name);
    preparedStatement.setString(2,admin_pwd);
    ResultSet resultSet = preparedStatement.executeQuery();
    if (resultSet.next()){
        System.out.println("恭喜，登陆成功");
    }else System.out.println("对不起，登陆失败");

    resultSet.close();
    preparedStatement.close();
    connection.close();
}
```

和Statement区别

PreparedStatement preparedStatement = connection.prepareStatement(sql);

在这执行sql语句

preparedStatement.setString(1,admin_name);

 preparedStatement.setString(2,admin_pwd);

 ResultSet resultSet = preparedStatement.executeQuery();

这个executeQuery()方法不执行sql语句 已经在prepareStatement()方法执行过了



预处理了sql语句

PreparedStatement执行的sql语句中的参数由？代替 			？相当于占位符

调用setxxx()方法来设置参数	第一个参数为索引值 表示第几个问好

可有效解决sql注入问题



## JDBCUtils

JDBCUtils工具类 

```
public class JDBCUtils {
    private static String driver;
    private static String url;
    private static String username;
    private static String password;
    static {

        try {
            Properties properties = new Properties();
            properties.load(new FileInputStream("src/mysql.properties"));
            driver=properties.getProperty("mysql.driver");
            url=properties.getProperty("mysql.url");
            username=properties.getProperty("mysql.username");
            password=properties.getProperty("mysql.password");
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
    public static Connection getConnection(){
        try {
            return DriverManager.getConnection(url,username,password);
        } catch (SQLException e) {
            throw new  RuntimeException(e);
        }
    }

    public static void close(ResultSet resultSet, Statement statement,Connection connection){

            try {
                if (resultSet !=null){
                resultSet.close();
            }
                if (statement !=null){
                    statement.close();
                }
                if (connection !=null){
                connection.close();}

            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
```



## 事务

jdbc程序中当有一个connection对象创建时，默认情况是自动提交

调用Connection的setAutoCommit(false)	可以取消自动提交事务，相当于开启了事务

在所有的SQL语句都成功执行后，调用commit();方法提交事务

在其中某个操作失败或出现异常时，调用rollback();方法回滚事务





## 批处理Batch

```
Connection connection = JDBCUtils.getConnection();
    String sql = "insert  into admin2 values(null,?,?)";
    PreparedStatement preparedStatement = connection.prepareStatement(sql);
    System.out.println("开始执行");
    long start = System.currentTimeMillis();

    for (int i=0;i<5000;i++){
        preparedStatement.setString(1,"jack"+1);
        preparedStatement.setString(2,"123");
        preparedStatement.addBatch();
        if((i+1)/1000==0){
            preparedStatement.executeBatch();
            preparedStatement.clearBatch();
        }

    }
    long end = System.currentTimeMillis();
    System.out.println("批量耗时为" + (end-start));//    批量耗时为611
    JDBCUtils.close(null,preparedStatement,connection);


}
```

基本介绍：

当需要成批插入或者更新记录时。可以采用Java的批量更新机制，这一机制允许多条语句一次性提交给数据库批量处理

JDBC的批量处理语句包括下面方法:

addBatch():添加需要批量处理的SQL语句或参数

executeBatch():执行批量处理语句;

clearBatch():清空批处理包的语句

JDBC连接MySQL时，如果要使用批处理功能,请再url中加参数**?rewriteBatchedStatements=true**

批处理往往和PreparedStatement一起搭配使用，可以既减少编译次数，又减少运行次数，效率大大提高

preparedStatement.addBatch();
        if((i+1)/1000==0){
            preparedStatement.executeBatch();
            preparedStatement.clearBatch();
        }

存够1000条sql就执行 且addBatch()中不写sql



# 连接池

程序不直接连接到数据库，而是先连接到连接池，再连接到数据库，因为同时连接可能过多导致数据库不可用，所以，先在连接池规定一定数量的连接，程序用完连接就放空，留给下个程序而不是关闭。





## C3P0

用于建立连接池的jar包

第一种方法，一个个导入url，username，password，driver，最大连接数等等

```
ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource();
    Properties properties = new Properties();
    properties.load(new FileInputStream("src/mysql.properties"));
    String driver=properties.getProperty("mysql.driver");
    String url=properties.getProperty("mysql.url");
    String username=properties.getProperty("mysql.username");
    String password=properties.getProperty("mysql.password");

    //连接管理由comboPooledDataSource来完成
    comboPooledDataSource.setDriverClass(driver);
    comboPooledDataSource.setJdbcUrl(url);
    comboPooledDataSource.setUser(username);
    comboPooledDataSource.setPassword(password);

    comboPooledDataSource.setInitialPoolSize(10);
    comboPooledDataSource.setMaxPoolSize(50);

    Connection connection = comboPooledDataSource.getConnection();      //核心方法
    System.out.println("连接成功");

}
```

Connection connection = comboPooledDataSource.getConnection(); 建立连接

//连接管理由comboPooledDataSource来完成，向comboPooledDataSource导入用户名密码driver等等

​    comboPooledDataSource.setDriverClass(driver);

​    comboPooledDataSource.setJdbcUrl(url);

​    comboPooledDataSource.setUser(username);

​    comboPooledDataSource.setPassword(password);

设置连接数

comboPooledDataSource.setInitialPoolSize(10);

设置最大连接数

   comboPooledDataSource.setMaxPoolSize(50);



第二种方法 使用配置文件进行配置

```
public void testC3P0_02() throws Exception {
    ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource("ljxxx");
    Connection connection = comboPooledDataSource.getConnection();      //核心方法
    System.out.println("连接成功");
    connection.close();

}


c3p0-config.xml

<named-config name="ljxxx">

    <property name="driverClass">com.mysql.jdbc.Driver</property>
    <property name="jdbcUrl">jdbc:mysql://localhost:3306/mybatis</property>
    <property name="user">root</property>
    <property name="password">1234</property>
    <property name="acquireIncrement">5</property>
    <property name="initialPoolSize">5</property>
    <property name="minPoolSize">5</property>
    <property name="maxPoolSize">10</property>
    <property name="checkoutTimeout">3000</property>



  </named-config>
```

ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource("ljxxx");

ljxxx为配置文件中<named-config name="ljxxx">的值





## Druid

```
@Test
@SuppressWarnings({"all"})
public void testdruid() throws Exception {
    Properties properties = new Properties();
    properties.load(new FileInputStream("src/druid.properties"));

    //创建指定参数的数据库连接池，druid连接池
    DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
    dataSource.getConnection();
    System.out.println("连接成功");


}
```

通过	DruidDataSourceFactory	工厂来创建数据库连接池



## Apache-DBUtils

### 执行查询语句

```
Connection connection = DruidUtils.connection();
//创建QueryRunner
QueryRunner queryRunner = new QueryRunner();
String sql="select * from actor where id >= ?";

//(1) query方法就是执行sql语句，得到resultset ---封装到-->ArrayList集合中
//(2)返回集合
//(3) connection:连接1//(4)sql :执行的sql语句
//(5） new BeanListHandLer<>(Actor.class):在将resultset -> Actor对象->封装到 ArrayList
// 底层使用反射机制去获取Actor类的属性,然后进行封装
//(6)1就是给 sql 语句中的?赋值，可以有多个值，因为是可变参数Object... params
//(7)底层得到的resultset,会在query 关闭，关闭PreparedStatment

List<Actor> query = queryRunner.query(connection, sql, new BeanListHandler<>(Actor.class),1);
System.out.println(query);
DruidUtils.close(null,null,connection);
```

commons-dbutils是 Apache组织提供的一个开源JDBC工具类库，它是对JDBC的封装，使用dbutils能极大简化jdbc编码的工作量

DbUtils类

QueryRunner类:该类封装了SQL的执行，是线程安全的。可以实现增、删、改、查、批处理

使用QueryRunner类实现查询

ResultSetHandler接口:该接口用于处理java.sql.ResultSet,将数据按要求缓换为另一种形式



//(1) query方法就是执行sql语句，得到resultset ---封装到-->ArrayList集合中

//(2)返回集合

//(3) connection:连接1//(4)sql :执行的sql语句

//(5） new BeanListHandLer<>(Actor.class):在将resultset -> Actor对象->封装到 ArrayList

// 底层使用反射机制去获取Actor类的属性,然后进行封装

//(6)1就是给 sql 语句中的?赋值，可以有多个值，因为是可变参数Object... params

//(7)底层得到的resultset,会在query 关闭，关闭PreparedStatment

List<Actor> query = queryRunner.query(connection, sql, new BeanListHandler<>(Actor.class),1);

BeanListHandler<>()用于多行多列

单行用BeanHandler<>()

单行单列用 ScalarHandler<>()		单行单列不带class类的参数，只有sql	和	sql语句中问号的参数

### 执行dml语句

```
@Test
public void testtoDML() throws Exception {
    Connection connection = DruidUtils.connection();
    //创建QueryRunner
    QueryRunner queryRunner = new QueryRunner();
    String sql="insert into actor values(null,?,?,?,?)";

    int update = queryRunner.update(connection, sql, "张三丰","男","1996-12-12","123");

    System.out.println("影响行数为:" + update);
    DruidUtils.close(null,null,connection);


}
```

int update = queryRunner.update(connection, sql, "张三丰","男","1996-12-12","123");

不用ResultSetHandler接口

用update方法，不用query方法





## BasicDAO

```
public class BasicDAO<T> {

    private QueryRunner queryRunner;


    //执行dml语句，针对任何表
    @Test
    public int update(String sql, Object ... paramenters){
        Connection connection=null;
        try {
            connection= DruidUtils.connection();
            int update = queryRunner.update(sql, paramenters);
            return update;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }finally {
            DruidUtils.close(null,null,connection);
        }

    }
    //执行select语句，针对任何表,查询多行多列
    @Test
    public List<T> selectArrayList(String sql, Class<T> clazz , Object ... paramenters){
        Connection connection=null;
        try {
            connection= DruidUtils.connection();
            List<T> query = queryRunner.query(sql, new BeanListHandler<T>(clazz), paramenters);
            return query;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }finally {
            DruidUtils.close(null,null,connection);
        }

    }

    //执行select语句，针对任何表,查询单行
    @Test
    public T selectSingle(String sql, Class<T> clazz , Object ... paramenters){
        Connection connection=null;
        try {
            connection= DruidUtils.connection();
            T query = queryRunner.query(sql, new BeanHandler<T>(clazz), paramenters);
            return query;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }finally {
            DruidUtils.close(null,null,connection);
        }

    }

    //执行select语句，针对任何表,查询单行单列
    @Test
    public Object selectScaral(String sql, Object ... paramenters){
        Connection connection=null;
        try {
            connection= DruidUtils.connection();
            Object query = queryRunner.query(sql, new ScalarHandler(), paramenters);
            return query;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }finally {
            DruidUtils.close(null,null,connection);
        }

    }
}
```

基础dao	充当父类	其他dao继承它，自身也可以有特有的操作

作用

1.把所有dao共同操作放到在里

2.简化代码提高维护性和可读性



public class BasicDAO<T>，方法中要用到泛型要在类名上声明！！！！！



List<T> query = queryRunner.query(sql, new BeanListHandler<T>(clazz), paramenters);

new BeanListHandler<T>(clazz)  <T>要写T，因为这时的clazz还是抽象未赋确定的类，所有yaoyongT泛型来暂时替代，List<T>中的T就是要返回的类型，也需要T要暂时替代