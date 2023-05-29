# 	入门案例

创建springboot工程

## 引入依赖

```
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.5.1</version>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.48</version>
</dependency>
```

当引入mabatisplus依赖时，不需要再引入mybatis依赖

在yml文件中配置

```
spring:
  datasource:
    type: com.zaxxer.hikari.HikariDataSource
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mybatis_plus?characterEncoding=utf-8&userSSL=false
    username: root
    password: 1234
```

## 建立数据库mybatis_plus	和表user

主键id的属性为bigint类型，而不是int	因为要用到雪花算法，数字大

```
CREATE DATABASE `mybatis_plus` /*!40100 DEFAULT CHARACTER SET utf8mb4 */;
use `mybatis_plus`;
CREATE TABLE `user` (
`id` bigint(20) NOT NULL COMMENT '主键ID',
`name` varchar(30) DEFAULT NULL COMMENT '姓名',
`age` int(11) DEFAULT NULL COMMENT '年龄',
`email` varchar(50) DEFAULT NULL COMMENT '邮箱',
PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```



```
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

在接口UserMapper不需要再写方法和实现类，只需要继承BaseMapper<User>	泛型为User
BaseMapper中有许多的sql方法

```
@Repository
public interface UserMapper extends BaseMapper<User> {

}
```

测试类

```
@Autowired
private UserMapper userMapper;

@Test
public void testSelectList(){
	//通过条件构造器查询一个list集合，若没有条件，则为空
    List<User> users = userMapper.selectList(null);
    users.forEach(System.out::println);
}
```

//通过条件构造器查询一个list集合，若没有条件，则为空
    List<User> users = userMapper.selectList(null);



## 加入日志功能

在yml配置文件中

```
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```



# BaseMapper类

## 增加功能

可直接用user.getId()获取自增的主键
这时	id:1637645315664375809	因为用了雪花算法	且主键id的类型为bigint

```
@Test
public void testInsert(){
    User user = new User();
    user.setName("张三");
    user.setAge(18);
    user.setEmail("123@qq.com");
    int insert = userMapper.insert(user);
    System.out.println("insert:"+insert);
    System.out.println("id:"+user.getId());
}
```



## 删除功能

baseMapper中有多种删除

deleteById通过id删除	以L结尾，表示为long类型
deleteByMap通过map删除，map中存放要删除的条件，map.put("name","张三") 删除name等于张三的数据
deleteBatchIds批量删除，通过Arrays.asList创造集合idlist集合，放入方法

```
    @Test
    public void delete(){

        //int i = userMapper.deleteById(1637645315664375809L);
//        HashMap map = new HashMap();
//        map.put("name","张三");
//        map.put("age",18);
//        int i = userMapper.deleteByMap(map);
        List<Long> list = Arrays.asList(1L, 2L, 3L);
        int i = userMapper.deleteBatchIds(list);
        System.out.println("delete:"+i);
    }
```



## 更新功能

```
@Test
public void update(){
    User user = new User();
    user.setId(4L);
    user.setName("张三");
    user.setAge(18);
    user.setEmail("123@qq.com");
    int i = userMapper.updateById(user);
    System.out.println("i:"+i);
}
```



## 查询功能

可通过id id集合 map集合 条件构造器查询数据

```
@Test
    public void select(){
        //根据id查询数据
        //User user = userMapper.selectById(1L);
        //System.out.println("user:"+user);
        //根据id集合 查询数据集合
//        List<Long> list = Arrays.asList(1L, 2L, 3L);
//        List<User> users = userMapper.selectBatchIds(list);
//        users.forEach(System.out::println);
        //根据map集合中的条件 查询数据
//        Map<String,Object> map =new HashMap();
//        map.put("name","jack");
//        map.put("age",23);
//        List<User> users = userMapper.selectByMap(map);
//        users.forEach(System.out::println);
        //根据条件构造器查询数据
        List<User> users = userMapper.selectList(null);
        users.forEach(System.out::println);

    }
```



# ServiceImpl类

service接口	继承IService类，泛型为User

```
public interface UserService extends IService<User> {
}
```

实现类 实现UserService类的同时，继承ServiceImpl类，ServiceImpl实现了IService类中的方法

```
@Repository
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {
}
```

测试类中 查询总记录数和批量添加数据，baseMapper类中没有真正批量添加的语句	id in (1,2,3,xxx)
所以本质还是调用多次添加语句

```
@Autowired
private UserService userService;

@Test
public void testSelect(){

    long count = userService.count();
    System.out.println("总记录数为："+count);
}

@Test
public void insertMore(){
    List<User> list = new ArrayList();
    for (int i = 1; i <=10; i++) {
        User user = new User();
        user.setName("ljxxx"+i);
        user.setAge(20+i);
        user.setEmail(i+"@qq.com");
        list.add(user);
    }
    boolean b = userService.saveBatch(list);
    System.out.println(b);
}
```





# 实体类和数据库中表名不一致

若数据库中表名为 t_user	而实体类名为user

使用@TableName注解

```
@TableName("t_user")
public class User {
```

或者	在yml配置文件中设置实体类所对应的表的统一前缀	为t_

```
mybatis-plus:
  global-config:
    db-config:
      table-prefix: t_
```



# 主键名不为id

若主键不为id	为uid

实体类中也为uid

```
private Long uid;
```

这时执行添加操作，mybatis就不会自动对uid进行赋值，因为mybatis只会对id进行赋值，即把id当成主键

可使用@TableId	将属性所对应的字段指定为主键

```
@TableId
private Long id;
```



## @TableId

有两个属性

value属性用于指定对应数据库表中主键的字段

若此时 实现了中 为Long id 表中为uid	用value ="uid"指明主键字段

```
@TableId(value = "uid",type = IdType.AUTO)
private Long id;
```

type属性设置主键生成策略

默认mybatisplus为雪花生成主键值，若要以+1的形式生成主键值，则type = IdType.AUTO

| **值**                   | **描述**                                                     |
| ------------------------ | ------------------------------------------------------------ |
| IdType.ASSIGN_ID（默认） | 基于雪花算法的策略生成数据id，与数据库id是否设置自增无关     |
| IdType.AUTO              | 使用数据库的自增策略，注意，该类型请确保数据库设置了id自增， |

若手动设置id值，则不会遵循主键生成策略，其只有自动增长，即没有设置id值时才用



也可以在yml配置文件中配置

```
mybatis-plus:
  global-config:
    db-config:
      id-type: auto
```

设置统一的主键生成策略



# 普通字段和实体类中属性名不一致

mybatis默认开启驼峰

若数据库中名字为 user_name	而实体类中为userName，则会自动使用驼峰



也可用@TableField标签进行指定属性所对应的字段名

```
@TableField("t_name")
private String name;
```



# @TableLogic注解(逻辑删除)

用于逻辑删除	即在表中数据还在，但是select不到

在数据库表中加入字段 is_delete int类型 默认值为0	0表示未被删除

实现类中	使用@TableLogic标识

```
@TableLogic
private Integer isDelete;
```

这时执行删除操作	数据库中执行的是更新语句，把is_delete改为1	待删除状态

```
UPDATE t_user SET is_delete=1 WHERE uid IN ( ? ) AND is_delete=0
```

这时 查询不到数据



# 条件构造器(wapper)

![image-20230320194240799](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230320194240799.png)

Wrapper ： 条件构造抽象类，最顶端父类

​		AbstractWrapper ： 用于查询条件封装，生成 sql 的 where 条件

​				QueryWrapper ： 查询条件封装

​				UpdateWrapper ： Update 条件封装

​				AbstractLambdaWrapper ： 使用Lambda 语法

​						LambdaQueryWrapper ：用于Lambda语法使用的查询Wrapper

​						LambdaUpdateWrapper ： Lambda 更新封装Wrapper



## QueryWrapper

lt：小于	le：小于等于	eq：等于	ne：不等于	ge：大于等于	gt：大于

### 组装查询条件

like("name", "a")中name为字段名，而不是实现类中属性名

like模糊查询，会自动加上'%值%'，between区间，isNotNull不为空

```
@Test
public void test01(){
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    //查询用户名包含a,年龄为20到30，邮箱信息不为空的用户信息
    wrapper.like("name", "a").
            between("age", 20, 30).
            isNotNull("email");
    List<User> users = userMapper.selectList(wrapper);
    users.forEach(System.out::println);
}
```



### 组装排序条件

使用orderByDesc() 降序排序	orderByAsc() 升序排序

```
@Test
public void test02(){
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    //查询用户信息，按照年龄的降序排序，若年龄相同，则按照id的升序排序
    wrapper.orderByDesc("age").orderByAsc("uid");
    List<User> users = userMapper.selectList(wrapper);
    users.forEach(System.out::println);

}
```



### 组装删除条件

```
@Test
public void test03(){
    QueryWraqupper<User> wrapper = new QueryWrapper<>();
    //删除邮箱为空的用户信息
    wrapper.isNull("email");
    int delete = userMapper.delete(wrapper);
    System.out.println("delete:"+delete);
}
```



### 修改功能

wrapper 条件之间默认以并且and间隔，若要用或者分隔，则需要手动写上or()方法

```
@Test
public void test04(){
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    //将(年龄大于20并且名用户名中包含a)，或邮箱为空的用户修改
    wrapper.gt("age",20).like("name","a").or().isNull("email");
    User user = new User();
    user.setName("kart");
    user.setEmail("test");
    int update = userMapper.update(user,wrapper);
    System.out.println("update:"+update);
}
```

update()方法 有两个参数，第一个参数为修改之后的数据	第二个参数为修改的条件，条件构造器



### 条件优先级

将用户名中包含有a并且(年龄大于20或邮箱为null)的用户信息修改

会还是like("name","a").and().gt("age",20).isNull("email");	and会先支持，就会变成包含a并且年龄大于20	或邮箱为null

这时就要用到Lambda语句	Lambda中的条件优先执行and(i->i.gt("age",20).or().isNull("email"));

```
@Test
public void test05() {
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    //将用户名中包含有a并且(年龄大于20或邮箱为null)的用户信息修改
    wrapper.like("name","a").and(i->i.gt("age",20).or().isNull("email"));
    User user = new User();
    user.setName("kart");
    user.setEmail("test");
    int update = userMapper.update(user,wrapper);
    System.out.println("update:"+update);
}
```



### 组装select字句

若只想查询几个字段的数据，例如只想查询name和age的数据	就可以用wrapper.select()

```
@Test
public void test06(){
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    wrapper.select("name","age","email");
    List<Map<String, Object>> maps = userMapper.selectMaps(wrapper);
    maps.forEach(System.out::println);

}
```



### 子查询

先用wrapper.inSql方法查询出所有age小于25的uid，放入查询语句中查询	(只是举例)

在数据库中写法为：SELECT * FROM t_user WHERE uid IN (SELECT uid FROM t_user WHERE age <25)
即要用到in	相当于inSql方法

```
@Test
public void test07(){
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    wrapper.inSql("uid","select uid from t_user where age < 25");
    List<User> users = userMapper.selectList(wrapper);
    users.forEach(System.out::println);
}
```



## UpdateWrapper 



### 修改功能

和QueryWrapper区别在于	修改之后的数据不是在 userMapper的update方法中传输，而是在updateWrapper.set()中进行设置

update(null,updateWrapper);	因为修改之后的数据放入wrapper中，所以第一个参数为空

```
@Test
public void test08(){
    UpdateWrapper<User> updateWrapper = new UpdateWrapper<>();
    //将用户名中包含有a并且(年龄大于20或邮箱为null)的用户信息修改
    updateWrapper.like("name","a")
            .and(i-> i.gt("age",20).or().isNull("email"));
    updateWrapper.set("name","vert").set("age",20);
    int update = userMapper.update(null,updateWrapper);
    System.out.println("update:"+update);
}
```



### 模拟开发中组装条件

从页面中传入username和age的最小值20，最大值30

StringUtils.isNotBlank方法用于判断某字符串是否不为空字符串，不为null，不为空白符
这里的StringUtils为com.baomidou.mybatisplus.core.toolkit.StringUtils	mybatis中的StringUtils！！！

```
@Test
public void test09(){
    String username="";
    Integer ageBegin=20;
    Integer ageEnd=30;
    UpdateWrapper<User> updateWrapper = new UpdateWrapper<>();
    if (StringUtils.isNotBlank(username)){
        //isNotBLank判断某个字符串是否不为空字符串、不为null、不为空白符
        updateWrapper.like("name",username);
    }
    if (ageBegin !=null){
        updateWrapper.ge("age",ageBegin);
    }
    if (ageEnd !=null){
        updateWrapper.le("age",ageEnd);
    }
    List<User> users = userMapper.selectList(updateWrapper);
    users.forEach(System.out::println);

}
```

也可以用

like gt le 中的另外一种方法，第一个参数写条件，第二个写字段名，第三个写具体数据

```
@Test
public void test10(){
    String username="";
    Integer ageBegin=19;
    Integer ageEnd=30;
    UpdateWrapper<User> updateWrapper = new UpdateWrapper<>();
    updateWrapper.like(StringUtils.isNotBlank(username),"name",username);
    updateWrapper.gt(ageBegin!=null,"age",ageBegin);
    updateWrapper.le(ageEnd!=null,"age",ageEnd);
    List<User> users = userMapper.selectList(updateWrapper);
    users.forEach(System.out::println);
}
```



## LambdaUpdateWrapper(常用)

写的shi实现类中的属性，而不是数据库中的字段，不用担心数据库字段错误

Lambda中要给字段赋值	要用User::getName获取实体类中的属性！！！！	函数式接口

```
@Test
public void test12(){
    LambdaUpdateWrapper<User> lambdaUpdateWrapper = new LambdaUpdateWrapper<>();
    //将用户名中包含有a并且（年龄大于20或邮箱为null）的用户信息修改
    lambdaUpdateWrapper.like(User::getName,"a")
            .and(i->i.gt(User::getAge,19).or().isNull(User::getEmail));
    lambdaUpdateWrapper.set(User::getName,"小黑").set(User::getEmail,"abc@qq,com");
    int update = userMapper.update(null,lambdaUpdateWrapper);
    System.out.println("update:"+update);
}
```



## LambdaQueryWrapper

```
@Test
public void test11(){
    String username="";
    Integer ageBegin=19;
    Integer ageEnd=30;
    LambdaQueryWrapper<User> lambdaQueryWrapper = new LambdaQueryWrapper<>();
    lambdaQueryWrapper.like(StringUtils.isNotBlank(username),User::getName,username)
          .gt(ageBegin!=null,User::getAge,ageBegin).le(ageEnd!=null,User::getAge,ageEnd);
    List<User> users = userMapper.selectList(lambdaQueryWrapper);
    users.forEach(System.out::println);
}


```



# 插件

## 分页插件

新建配置类	MybatisPlusInterceptor 把PaginationInnerInterceptor分页插件装入

```
@Configuration
public class MybatisPlusConfig {

    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor(){
        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
        PaginationInnerInterceptor paginationInnerInterceptor = new PaginationInnerInterceptor(DbType.MYSQL);
        mybatisPlusInterceptor.addInnerInterceptor(paginationInnerInterceptor);
        return mybatisPlusInterceptor;
    }
}
```

直接用userMapper.selectPage(page,null)第一个参数为分页信息，第二个为条件构造器
Page<User> page = new Page<>(1,3)	设置当前页码为1	每页显示3条数据
这时userPage和page为同一个元素，地址都相同

```
@Test
public void testPage(){
    Page<User> page = new Page<>(1,3);
    Page<User> userPage = userMapper.selectPage(page, null);
    System.out.println(userPage);
    System.out.println(page);

}
```



### 自定义分页功能

在UserMapper类中

```
/**
 * 通过年龄查询用户信息并且分页
 * @param page  MybatisPlus提供的分页对象，必须位于第一个参数
 * @param age
 * @return
 */
Page<User> selectPageVo(@Param("page") Page<User> page,@Param("age")Integer age);
```

自定义方法，page对象一定要放在第一个参数！！！	且返回值也必须是Page类型！！

```
<select id="selectPageVo" resultType="User">
    select uid,name,age,email from t_user where age> #{age}
</select>
```

在测试类中调用方法一致

```
@Test
public void testPageVo(){
    Page<User> page = new Page<>(1,3);
    Page<User> userPage = userMapper.selectPageVo(page, 20);
    System.out.println(page.getRecords());
}
```



## 乐观锁

悲观锁：当一个用户操作一个数据时，其他用户不能操作这个数据，要等第一个用户操作完才能操作

乐观锁：加入版本号字段，若要进行修改 先检查数据库中版本号和当前是否相同，相同则修改，且把数据库中的版本号+1，若不相同，则不修改

### 模拟修改冲突

建立product表，product实现类，ProductMapper类等
多加入个字段 版本号，用于开启乐观锁

```
CREATE TABLE t_product
(
id BIGINT(20) NOT NULL COMMENT '主键ID',
NAME VARCHAR(30) NULL DEFAULT NULL COMMENT '商品名称',
price INT(11) DEFAULT 0 COMMENT '价格',
VERSION INT(11) DEFAULT 0 COMMENT '乐观锁版本号',
PRIMARY KEY (id)
);

@Data
public class Product {
    private Long id;
    private String name;
    private Integer price;
    private Integer version;
}

public interface ProductMapper extends BaseMapper<Product> {}
```

boss的原意是  商品价格由原来的100 增加50，又降低30	最后为120

但是小李小王同时获得商品价格为100，同时增加50，降低30，小王后提交，会把小李修改的价格覆盖，最后价格为70

```
@Test
public void testProduct1(){
    //小李查询商品价格
    Product productLi = productMapper.selectById(1);
    System.out.println("小李查询价格为："+productLi.getPrice());
    //小王查询商品价格
    Product productWang = productMapper.selectById(1);
    System.out.println("小王查询价格为："+productWang.getPrice());
    //小李把商品价格+50
    productLi.setPrice(productLi.getPrice()+50);
    productMapper.updateById(productLi);
    //小王把商品价格-30
    productWang.setPrice(productWang.getPrice()-30);
    productMapper.updateById(productWang);

    //Boss查询商品价格
    Product productBoss = productMapper.selectById(1);
    System.out.println("Boss查询价格为："+productBoss.getPrice());
}
```



### 开启乐观锁插件

在配置类中加入乐观锁插件

```
//乐观锁插件
OptimisticLockerInnerInterceptor optimisticLockerInnerInterceptor = new OptimisticLockerInnerInterceptor();
mybatisPlusInterceptor.addInnerInterceptor(optimisticLockerInnerInterceptor);
return mybatisPlusInterceptor;
```

在实现类中	用@Version标识乐观锁版本号字段

```
@Version    //标识乐观锁版本号字段
private Integer version;
```

这时再执行测试类中方法	小李能修改成功，但是会把版本号+1，这时数据库中版本号就和小王获取的数据中的版本号不同，所以小王修改失败	Boss查询到的数据为150，只有小李增加的



### 优化修改流程、

判断小王修改受影响行数是否为0，0表示未修改，则再次获取id为1的数据！！目的是为了获取新的版本号

```
//小王把商品价格-30
productWang.setPrice(productWang.getPrice()-30);
int result = productMapper.updateById(productWang);
if (result ==0){
            Product productWangNew = productMapper.selectById(1);
            productWangNew.setPrice(productWangNew.getPrice()-30);
            productMapper.updateById(productWangNew);
}
```



# 通用枚举

使用 ***@EnumValue***注解

在t_user表中加入	sex字段 属性为int

建立枚举类

属性为 Integer sex;String sexName;	设置构造方法
并设置枚举值MALE(1,"男"),FEMALE(2,"女");	MALE表示1为男，FEMALE表示2为女

```
@Getter
public enum  SexEnum {
    MALE(1,"男"),
    FEMALE(2,"女");

    @EnumValue  //将注解所标识的属性的值存储到数据库中
    private Integer sex;
    private String sexName;

    SexEnum(Integer sex, String sexName) {
        this.sex = sex;
        this.sexName = sexName;
    }
}
```

在user实现类中加入sex属性 类型为 SexEnum类型	private SexEnum sex;

这时	会提示报错，因为在java中 sex为枚举类型，但是数据库中为int

```
@Test
public void test(){
    User user = new User();
    user.setName("admin");
    user.setAge(33);
    user.setSex(SexEnum.MALE);
    int insert = userMapper.insert(user);
    System.out.println("insert:"+insert);
}
```

要在枚举类中	sex属性上	加上@EnumValue注解，标识该属性的值存储到数据库中

```
@EnumValue  //将注解所标识的属性的值存储到数据库中
private Integer sex;
```

且在yml配置文件中开启枚举的扫描

```
mybatis-plus:
  type-enums-package: com.ljxxx.mybatisplus.enums
```

这时再运行	就会把MALE的sex值存储到数据库中



# 代码生成器

引入依赖
generator为代码生成器的核心依赖	freemarker为生成器默认生成的引擎类型吗，可修改

```
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.5.1</version>
</dependency>
<dependency>
    <groupId>org.freemarker</groupId>
    <artifactId>freemarker</artifactId>
    <version>2.3.31</version>
</dependency
```



快速生成	
可修改夫包名，输出目录，mapper文件生成目录 生成的表名，过滤表的前缀等等

直接执行即可

```
public class FastAutoGeneratorTest {
    public static void main(String[] args) {
        FastAutoGenerator.create("jdbc:mysql://localhost:3306/mybatis_plus?characterEncoding=utf-8&userSSL=false", "root", "1234")
                        .globalConfig(builder -> {
                            builder.author("ljxxx") // 设置作者
                            // .enableSwagger() // 开启 swagger 模式
                                    .fileOverride() // 覆盖已生成文件
                                    .outputDir("D://mybatis_plus"); // 指定输出目录
                        })
                        .packageConfig(builder -> {
                            builder.parent("com.ljxxx") // 设置父包名
                                    .moduleName("mybatisplus") // 设置父包模块名
                                    .pathInfo(Collections.singletonMap(OutputFile.mapperXml, "D://mybatis_plus"));// 设置mapperXml生成路径
                        })
                        .strategyConfig(builder -> {
                            builder.addInclude("t_user") // 设置需要生成的表名
                                    .addTablePrefix("t_", "c_"); // 设置过滤表前缀
                        })
                        .templateEngine(new FreemarkerTemplateEngine()) // 使用Freemarker 引擎模板，默认的是Velocity引擎模板
                        .execute();
    }
}
```



# 多数据源环境

若要同时操控两个以上的数据库

1.引入依赖	最重要的就是<artifactId>dynamic-datasource-spring-boot-starter</artifactId>

```
<dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.5.1</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.48</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>dynamic-datasource-spring-boot-starter</artifactId>
            <version>3.5.0</version>
        </dependency>
```

在yml配置文件中配置	primary：写的是默认数据源的别名，可以随便写

```
spring:
  # 配置数据源信息
  datasource:
    dynamic:
      # 设置默认的数据源或者数据源组,默认值即为master
      primary: master
      # 严格匹配数据源,默认false.true未匹配到指定数据源时抛异常,false使用默认数据源
      strict: false
      datasource:
        master:
          url: jdbc:mysql://localhost:3306/mybatis_plus?characterEncoding=utf-8&useSSL=false
          driver-class-name: com.mysql.jdbc.Driver
          username: root
          password: 1234
        slave_1:
          url: jdbc:mysql://localhost:3306/mybatis_plus_1?characterEncoding=utf-8&useSSL=false
          driver-class-name: com.mysql.jdbc.Driver
          username: root
          password: 1234
```

在mybatis_plus	即master中创建t_user表
在mybatis_plus_1	即slave_1中创建pruduct表

创建product，user的pojo，mapper，service等类，主类中用@MapperScan注解扫描mapper组件

并在serviceImpl中	使用@DS注解，用于声明该类操作的是哪个数据库	加在接口实现类上，而不是接口上

```
@DS("master")
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {
}

@DS("slave_1")
public class ProductServiceImpl extends ServiceImpl<ProductMapper, Product> implements ProductService {
}
```



@DS注解用于切换数据源
可用于方法上和类上，同时存在就近原则，方法优于类上注解



测试成功

```
@Test
void test() {
    Product product = productService.getById(1);
    System.out.println(product);
    User user = userService.getById(1);
    System.out.println(user);
}
```





# MyBatis-X插件

先安装MyBatis-x插件

代码快速生成

![image-20230321190917074](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230321190917074.png)



![image-20230321190936448](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230321190936448.png)

生成数据库操作面板	选中要操作的表

![image-20230321191009624](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230321191009624.png)

最后生成

![image-20230321191017909](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230321191017909.png)



