# BigInteger和BigDecimal类

BigInteger适合保存比较大的整数

BigDecimal适合保存精度更高的浮点型，且不会丢失精度，double会丢失精度



若直接创建Integer integer = new Integer(12321313123123123);则会报错
也不能直接BigInteger bigInteger = new BigInteger(12321313123123123);	编译器无法解析12321313123这么大的数据，需要先转为String类型，通过BigInteger的构造器	传入String类型，会自动转换

```
public BigInteger(String val) {
    this(val, 10);
}
```

```
BigInteger bigInteger = new BigInteger("12321313123123123");
System.out.println(bigInteger);
```



## 常用方法

Big数据不能直接+ - * / 要通过方法

1) add 加

2) subtract 减

3) multiply 乘

4) divide 除

BigInteger

```
BigInteger bigInteger = new BigInteger("10000");
BigInteger bigInteger1 = new BigInteger("1000");
System.out.println("相加"+bigInteger.add(bigInteger1));
System.out.println("相减"+bigInteger.subtract(bigInteger1));
System.out.println("相乘"+bigInteger.multiply(bigInteger1));
System.out.println("相除"+bigInteger.divide(bigInteger1));
```



BigDecimal

```
double b = 12.1111111111111111111111111111111;
BigDecimal bigDecimal = new BigDecimal("12.1212121212");
BigDecimal bigDecimal1 = new BigDecimal("3.33333333");
System.out.println("相加"+bigDecimal.add(bigDecimal1));
System.out.println("相减"+bigDecimal.subtract(bigDecimal1));
System.out.println("相乘"+bigDecimal.multiply(bigDecimal1));
System.out.println("相除"+bigDecimal.divide(bigDecimal1));//可能会抛出异常，除不尽循环
```

若相除为无限循环，则会抛出异常

解决办法	在divide()方法后加个参数BigDecimal.ROUND_CEILING)	指定保留几位小数，保留除数的小数位数！！！	除数有几位，就留几位

```
System.out.println("相除"+bigDecimal.divide(bigDecimal1,BigDecimal.ROUND_CEILING));
```





# 日期类

## 第一代日期类

1) Date:精确到毫秒，代表特定的瞬间

2) SimpleDateFormat:格式和解析日期的类
	SimpleDateFormat格式化和解析日期的具体类。它允许进行格式化(日期->文本)、解析(文本->日期）和规范化.



Date用于获取当前时间，也可通过指定毫秒数来获取时间，默认格式为国外时间格式	星期月日具体时间年

SimpleDateFormat用于指定日期格式，通过format方法把Date类型转换为String类型
也可通过parse方法把String类型转换为Date类型	
String中的格式和SimpleDateFormat格式要一致！！！

```
Date date = new Date(); //获取当前系统时间
System.out.println(date);
Date date1 = new Date(8328382); //通过指定毫秒数获取时间
System.out.println(date1);

//创建SimpleDateFormat    可以指定日期格式
SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy年MM月dd日 hh:mm:ss E");
String format = simpleDateFormat.format(date);  //format将日期转换为指定字符串
System.out.println(format);

//可以把格式化的String转换为Date类型
String s = "2010年03月04日 10:20:49 星期一";
Date parse = simpleDateFormat.parse(s);
System.out.println(parse);
```

SimpleDateFormat格式说明

![image-20230430100254349](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230430100254349.png)



## 第二代日期类

1)第二代日期类,主要就是Calendar类(日历)。

public abstract class Calendar extends Object implements Serializable,Cloneable, Comparable<Calendar>

2) Calendar类是一个抽象类，它为特定瞬间与一组诸如YEAR、MONTH.DAY_OF MONTH、HOUR等日历字段之间的转换提供了一些方法,并为操作日历字段（例如获得下星期的日期)提供了一些方法。

Calendar抽象类	且构造方法为private，只能通过getInstance()方法获取实例
Calendar c中存放了所有时间信息，都放在字段中，通过输出字段来获取时间	
Calendar.HOUR_OF_DAY为24小时进制的小时字段
Calendar类没有专门格式化的方法，需要自己组合显示

```
//1. Calendar是一个抽象类，并且构造器是private
//2. 可以通过getInstance()来获取实例
//3．提供大量的方法和字段提供给程序员
Calendar c = Calendar.getInstance();//创建日历类对象，比较简单，自由
System.out.println("c=" + c);
//2.获取日历对象的某个日历字段
System.out.println("年:" + c.get(Calendar.YEAR));
//月要+1，因为是从0开始计算
System.out.println("月:" + (c.get(Calendar.MONTH) + 1));
System.out.println("日:" + c.get(Calendar.DAY_OF_MONTH));
System.out.println("小时:" + c.get(Calendar.HOUR));
System.out.println("分钟:" +c.get(Calendar.MINUTE));
System.out.println("秒:" + c.get(Calendar.SECOND));
//Calendar类没有专门格式化的方法，需要自己组合显示
System.out.println(c.get(Calendar.YEAR)+"年"+(c.get(Calendar.MONTH) + 1)+"月"+      c.get(Calendar.DAY_OF_MONTH)+"日"+c.get(Calendar.HOUR)+"时"+c.get(Calendar.MINUTE)+"分"+
        c.get(Calendar.SECOND)+"秒");
        
输出结果为	2023年4月30日10时15分38秒
```



## 第三代日期类

目的是为了解决第二代日期线程不安全，无法格式等问题，在jdk8加入

1) LocalDate(日期/年月日)、LocalTime(时间/时分秒)、LocalDateTime(日期时间)

LocalDate只包含日期，可以获取日期字段
LocalTime只包含时间，可以获取时间字段
LocalDateTime包含日期+时间，可以获取日期和时间字段

```
LocalDateTime now = LocalDateTime.now();
System.out.println(now);
//获取具体日期信息
System.out.println("年:"+now.getYear());
System.out.println("月:"+now.getMonth());
System.out.println("月:"+now.getMonthValue());
System.out.println("日:"+now.getDayOfMonth());
System.out.println("星期:"+now.getDayOfWeek());
//获取从1月1日到现在过了几天
System.out.println("日子:"+now.getDayOfYear());
```



2) DateTimeFormatter格式日期类
类似于SimpleDateFormat
DateTimeFormat dtf = DateTimeFormatter.ofPattern(格式);
String str = dtf.format(日期对象);

```
DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
String format = dateTimeFormatter.format(now);
System.out.println(format);
```



3) Instant时间戳
类似于Date

提供了一系列和Date类转换的方式
Instant—>Date:		Date date = Date.from(instant);
Date>lnstant:			Instant instant = date.tolnstant();

```
Instant now = Instant.now();
System.out.println(now);

//instant --> Date
Date from = Date.from(now);
System.out.println(from);

//Date --> instant
Instant instant = from.toInstant();
System.out.println(instant);
```



第三代日期类其他方法

LocalDateTime类

MonthDay类:检查重复事件是否是闰年

增加日期的某个部分

使用plus方法测试增加时间的某个部分	相当于加法

使用minus方法测试查看一年前和一年后的日期	相当于减法

```
//获取当前时间加上120天
LocalDateTime localDateTime = now.plusDays(120);
String format1 = dateTimeFormatter.format(localDateTime);
System.out.println(format1);
//获取当前时间减去320分钟
LocalDateTime localDateTime1 = now.minusMinutes(320);
String format2 = dateTimeFormatter.format(localDateTime1);
System.out.println(format2);
```

