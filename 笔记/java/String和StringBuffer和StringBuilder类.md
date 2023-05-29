# String和StringBuffer和StringBuilder类

## String类

1.String对象用于保存字符串，也就是一组字符序列

2."jack”字符串常量，双引号括起的字符序列

3.字符串的字符使用Unicode字符编码，一个字符(不区分字母还是汉字)占两个字节

4.String类有很多构造器，构造器的重载

常用的有String s1 = new String();
			   String s2 = new String(String original);
			   String  s3 = new String(char[] a);
			   String s4 =new String(char[] a,int startIndex, int count)
			   String s5 = new String(byte[] b)

5.String 类实现了接口Serializable 【String 可以串行化:可以在网络传输】
						   接口Comparable 【String对象可以比较大小】

6.String是final类,不能被其他的类继承

7.String有属性 private final char value[];用于存放字符串常量

### 8.value是一个final类型，“不可以修改”

不可修改的意思是，不可以修改地址，不可以修改value数组指向的地址

即 value-->[tom]指向了 一个地址内容为tom，不可以再指向value-->[mary]一个新的地址块

但是地址块内容是可以修改的	可以改成value-->[toh]！！！

9.String有char[]的构造器，可通过字符数组创建String

```
public String(char value[]) {
    this.value = Arrays.copyOf(value, value.length);
}

String string = new String(chars);
```

10.String有byte[]的构造器，可通过字节数组创建String

```
public String(byte bytes[]) {
    this(bytes, 0, bytes.length);
}
```



final修饰基本类型，不能修改值
final int n1=100;不能修改值

final修饰引用类型，不能修改指向地址
final char value[]	不能修改指向地址，但是可以修改值







### String创建机制(重！！！)

方式一:直接赋值String s = "hsp";

方式二:调用构造器String s2 = new String("hsp");



方法一	会直接在方法区常量池中创建"hsp"	s对象直接指向"hsp"的地址

方法二	会在堆中使用value数组，用于存放字符串内容，也会在常量池中创建"hsp"若有，则会直接指向，这时s2对象指向 value数组的地址 ，而value数组，则会转向"hsp"的地址

所以s==s2 为false

![image-20230419150301821](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230419150301821.png)

若String s2 = new String("hsp");想指向常量池中"hsp"的地址，可以使用intern()方法

String.intern()作用：若常量池中已经包含了一个等于此String对象内容的字符串，就返回字符串常量池中该字符串的引用，返回了地址,没有则***创建***再返回

String s1 = new String("aaa");

String s2 = s1.intern();

String s3 = "aaa";

s1==s2  false   一个是堆内存的对象，一个是字符串常量池的对象

s2==s3 true	因为intern()方法相当于让s2直接指向了常量池中"aaa"的地址



在java大注意点.md文件中	String类--->String创建对象情况（重！！）	中有详细说明





### 面试题（重！！！）

#### 一。

String a ="hello"+"abc";
创建了几个对象？1个
String a = "hello"+"abc"; ==>编译器会自动优化，等价String a = "helloabc";



#### 二。

String a = "hello";
String b ="abc";
String c=a+b;
创建了几个对象？4个	且c指向堆中，而不是常量池

底层运行过程：
1.先创建一个StringBuilder sb = StringBuilder()
2.执行sb.append("hello");
3.执行sb.append("abc");
4.String c= sb.toString()

而String的toString方法		是通过new String来创建对象的，所以创建了两个对象，且指向堆中value

```
public string toString() {
	return new String(value,0,count);
}
```



两道题的结论

String c1 = "ab" + "cd";常量相加,看的是池。
String c1 =a+b;变量相加,是在堆中



#### 三。老韩P470节课（重！！！）

仔细思考输出什么？

```
public class Work1 {
    public static void main(String[] args) {
        Test1 ex = new Test1();
        ex.change(ex.str, ex.ch);
        System.out.print(ex.str + " and ");
        System.out.println(ex.ch);
    }
}

class Test1 {

    String str = new String("hsp");
    final char[] ch = {'j', 'a', 'v', 'a'};
    
    public void change(String str, char ch[]) {
        str = "java";
        ch[0] = 'h';
    };
}
```

堆区存放变量，栈区存放方法，在运行中，有方法，则会在栈中开辟空间，用于存放方法的一些变量等等

一开始为：

![image-20230419215438493](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230419215438493.png)

开始运行change方法，change方法中有两个变量 String类型 str char数组类型 ch

str = "java";	因为value不能修改	所以这时str变量会直接指向常量池中"java"常量，没有则创建
而ch类型，则还是直接指向java，且j变为h

![image-20230419215716880](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230419215716880.png)

这时结束change方法，

ex.str仍然指向堆区中的value，value指向"hsp"没有发生变化
而ex.ch 指向了hava，发生了变化





### 常用方法

equals	//区分大小写，判断内容是否相等

equalsIgnoreCase	//忽略大小写的判断内容是否相等

length	//获取字符的个数,字符串的长度

indexOf	//获取字符在字符串中第1次出现的索引,索引从0开始,如果找不到,返回-1

lastIndexOf	//获取字符在字符串中最后1次出现的索引,索引从0开始,如找不到,返回-1



substring 	//截取指定范围的子串	
substring(i,j)	截取j-i个元素，从索引值i开始	，包括i 	！！！
substring(i)	截取索引值i之后的内容，包括i	！！！
不会改变str的值，底层new String 新建一个String对象

```
String str="hello,world";
System.out.println(str.substring(6));	//截取索引值6之后的内容	包括6
System.out.println(str.substring(1,5));//截取5-1个元素，从索引值1开始
```



trim	//去前后空格

charAt	//获取某索引处的字符,注意不能使用Str[index]这种方式.

```
String str ="hello";
//str[0]不对
//str.charAt[0]	=>	h	对
```



toUpperCase	//把字符串转换成大写

toLowerCase	//把字符串转换成小写

concat		//拼接字符串	可多次使用

```
String s1="ljxxx";
s1 = s1.concat("12345").concat("tom");
System.out.println(s1);		//输出结果为ljxxx12345tom
```



replace		//替换字符串中的字符	符合的会全部替换

s1.replace()	方法执行之后，对s1本身没有影响，返回的结果才是替换后的

```
s1 = s1.replace("tom", "mary");
System.out.println(s1);		//输出结果为ljxxx12345mary	即把tom替换成mary
```



split 分割字符串,对于某些分割字符，我们需要转义比如| \\\等

```
String poem = "锄禾日当午,汗滴禾下土,谁知盘中餐,粒粒皆辛苦";
String[] split = poem.split(",");		//通过,进行分隔
System.out.println("================");
for (String s2:split) {
    System.out.println(s2);
}
```

当字符串中包含\ |等字符时，且需要用其分隔，需要加入转义字符	\

```
poem="E:\\aaa\\bbb";
String[] split1 = poem.split("\\\\");	//一个\要一个\来转义	所以\ \ 转义为\\ \\
System.out.println("================");
for (String s2:split1) {
    System.out.println(s2);
}
```



compareTo 	//比较两个字符串的大小（ASCLL码）

如果参数字符串等于此字符串，则返回值0；
如果此字符串小于字符串参数，则返回一个小于0的值	相减的值
如果此字符串大于字符串参数，则返回一个大于0的值	相减的值

但是若两个字符串长度不同，且前面都相等，则用length的值相减！！！！！！

```
String s3="jac";
String s4="jack";
System.out.println(s3.compareTo(s4));	//最后会用s3.length-s4.length 结果为-1
```



toCharArray 	//转换成字符数组char

```
String s2="hello";
char[] chars = s2.toCharArray();
for (char ch:chars) {
    System.out.println(ch);
}
```



format 	//格式字符串，%s字符串	%c字符	%d整型	%.2f 浮点型

```
String s5="ljxxx";
int n1 =100;
double d1=65.578;
char gender='男';
String format = String.format("我的名字为%s,年龄为%d,成绩为%.2f,性别为%c", s5, n1, d1, gender);
System.out.println(format);
```

%s , %d , %.2f  %c称为占位符	这些占位符由后面变量来替换
%s  表示后面由字符串来替换
%d  是整数来替换
%.2f 表示使用小数来替换，替换后，只会保留小数点两位，并且进行四舍五入的处理
%c  使用char类型来替换



getBytes	//转换成字节数组byte

```
String b = "ljxxx";
byte[] bytes = b.getBytes();
```



## StringBuffer类

java.lang.StringBuffer代表可变的字符序列，可以对字符串内容进行增删。
很多方法与String相同，但StringBuffer是可变长度的。	不是final
StringBuffer是一个容器。

### 特点

1.StringBuffer的直接父类是 AbstractStringBuilder

2.StringBuffer 实现了Serializable，即StringBuffer的对象可以串行化

3.在父类中AbstractStringBuilder有属性char[] value，用于存放字符串变量，不是final类型，所以是可变长度的，该value数组存放的字符串不在常量池中，而是在堆中(因为不是final类型，只有final才会存放在常量池中)

4.StringBuffer类也是一个final类，不能被继承



### 构造器

三种构造器，构造出的char[] 存放字符串变量 大小不同

```
//源码中，创建大小为16的 char[]，用于存放字符串变量
StringBuffer stringBuffer = new StringBuffer();

//通过构造器，指定char[] 大小，这里是100
StringBuffer stringBuffer1 = new StringBuffer(100);

//char[]大小为 str.length()+16
StringBuffer hello = new StringBuffer("hello");
```





### StringBuffer常用方法

1)增append()	直接对stringBuffer对象进行修改
若插入null,则会把 n u l l四个字符插入

```
StringBuffer stringBuffer = new StringBuffer("hello");
stringBuffer.append("ljxxx").append(true);	//helloljxxxtrue
```



2)删delete(start,end)	[start,end)	删除索引值从start开始，到end的数据，不包含end
会直接修改stringBuffer，而不是返回值才是删除之后的结果

```
StringBuffer delete = stringBuffer.delete(5, 10);
System.out.println(delete);	//hellotrue
```



3)改replace(start,end,string)	[start,end) 将start----end间的内容替换掉,不含end
把ljxxx替换为ljx

```
StringBuffer stringBuffer = new StringBuffer("hello");
stringBuffer.append("ljxxx").append(true);
StringBuffer ljx = stringBuffer.replace(5, 10, "ljx");
System.out.println(ljx);		//helloljxtrue
```



4)查indexOf //查找子串在字符串第1次出现的索引,如果找不到返回-1

```
System.out.println(stringBuffer.indexOf("ljx"));
```



5)插insert(index) 从索引值index位置插入数据，原来位置的数据后移
且也会直接修改stringBuffer的值

```
StringBuffer tom = stringBuffer.insert(5, "tom");
System.out.println(stringBuffer);	//hellotomljxtrue
```



6)获取长度length



## String和StringBuffer比较

1) String保存的是字符串常量，里面的值不能更改，value数组指向常量池
每次String类的更新实际上就是更改地址，效率较低

2) StringBuffer保存的是字符串变量，里面的值可以更改,value数组指向堆中
每次StringBuffer的更新实际上可以更新内容,不用每次更新地址,效率较高，只有空间不够时，才会更新地址



## String和StringBuffer转换

String-->StringBuffer	可通过构造器，或append方法拼接

```
//String-->StringBuffer
String str = new String("hello");

//方法1   通过构造器
StringBuffer stringBuffer = new StringBuffer(str);
//方法2
StringBuffer stringBuffer1 = new StringBuffer();
stringBuffer1 = stringBuffer1.append(str);
```

StringBuffer-->String	可通过构造器，或toString方法

```
//StringBuffer-->String
StringBuffer stringBuffer2 = new StringBuffer("hello");

//方法1   使用StringBuffer自带的toString方法
String s = stringBuffer2.toString();

//方法2   使用String的构造器实现
String s1 = new String(stringBuffer2);
```





## 练习题（重！！）

### 第一题

下列代码输出了什么

```
String str=null;
StringBuffer stringBuffer = new StringBuffer();
stringBuffer.append(str);
System.out.println(stringBuffer.length());

System.out.println(str);
StringBuffer stringBuffer1 = new StringBuffer(str);
System.out.println(stringBuffer1);
```

#### 先看第一部分

String str=null;
StringBuffer stringBuffer = new StringBuffer();
stringBuffer.append(str);
System.out.println(stringBuffer.length());

在底层中 append()方法，会调用父类AbstractStringBuilder的append方法，
而父类的append方法有如下判断

```
if (str == null)
    return appendNull();
```

若传入的字符串str为空，则会调用appendNull方法

而appendNull方法	会把null，这四个字符存入value数组中

```
private AbstractStringBuilder appendNull() {
    int c = count;
    ensureCapacityInternal(c + 4);
    final char[] value = this.value;
    value[c++] = 'n';
    value[c++] = 'u';
    value[c++] = 'l';
    value[c++] = 'l';
    count = c;
    return this;
}
```

所以这时输出stringBuffer.length()	结果为4

#### 第二部分

StringBuffer stringBuffer1 = new StringBuffer(str);
System.out.println(stringBuffer1);

StringBuffer(String str)构造器	调用str.length()	会导致空指针异常

```
public StringBuffer(String str) {
    super(str.length() + 16);
    append(str);
}
```



### 第二题

题目

```
输入商品名称和商品价格，要求打印效果示例,使用前面学习的方法完成:
商品名	商品价格
手机	123,564.591	//比如价格3,456,789.88
要求:价格的小数点前面每三位用逗号隔开,在输出。
```



```
String price = "1234567.123456";
StringBuffer stringBuffer = new StringBuffer(price);

for ( int i = stringBuffer.lastIndexOf(".")-3; i >0; i-=3) {
    stringBuffer=stringBuffer.insert(i,",");
    System.out.println(i);
}
System.out.println(stringBuffer);
```



### 第三题

```
输入用户名、密码、邮箱，如果信息录入正确，则提示注册成功，否则生成异常对象要求:
(1)用户名长度为2或3或4
()密码的长度为6，要求全是数字		新写isDigital()方法，用于判断是否全为数字
(3)邮箱中包含@和.并且@在.的前面		
```



isDigital()方法，把字符串转换为char数组，若chars[i]的值在‘0’到‘9’之间，则说明为数字

```
public static String register(String username,String password,String email){
    if (username.length()>4 &&username.length()<2){
        throw new RuntimeException("用户名错误");
    }
    if (!(password.length()==6&& isDigital(password))){
        throw new RuntimeException("密码错误");
    }
    int i = email.indexOf("@");
    int j = email.indexOf(".");
    if (!(i>0 && j>i)){
        throw new RuntimeException("邮箱错误");
    }
    return "注册成功";
}
public static boolean isDigital(String str){
    char[] chars = str.toCharArray();
    for (int i = 0; i <chars.length; i++) {
        if (chars[i]<'0'|| chars[i]>'9'){
            return false;
        }
    }
    return true;
}
```



### 第四题

```
(1)编引java程序，输入形式为:Han shun Ping的人名，以Ping,Han .S的形式打印
出来。其中.S是中间单词的首字母。
(2)例如输入“Willian Jefferson Clinton”，输出形式为:Clinton,Willian .J
```



```
public static void main(String[] args) {
    String s = trimString("Jack Tom Ljxxx");
    System.out.println(s);
}

public static String trimString(String str){
    String[] s = str.split(" ");
    if (s.length!=3){
        throw new RuntimeException("长度错误");
    }
    String format = String.format("%s,%s .%c", s[2], s[0], s[1].toUpperCase().charAt(0));
    return format;
}
```

## StringBuiler类

1)一个可变的字符序列。
此类提供一个与StringBuffer 兼容的API,但不保证同步(StringBuilder不是线程安全)。
用在字符串缓冲区被单个线程使用的时候。
优先采用StringBuilder，因为通常情况下，会比StringBuffer更快

2)在StringBuilder上的主要操作是append和insert方法，可重载这些方法,以接受任意类型的数据。



### 特点

1.StringBuilder继承 AbstractStringBuilder 类
2.实现了Serializable，说明StringBuilder 对象可以串行化（对象可以网络传输，可以保存到文件）

3.StringBuilder是final类,不能被继承

4.StringBuilder对象字符序列仍然是存放在其父类 AbstractStringBuilder的 char[] value;
	因此，字符序列是堆中，而不是常量池

5.StringBuilder方法，都没有做互斥处理，即没有syncgribuzed关键字，因此只能在单线程情况下使用



### 常用方法

StringBuilder和 StringBuffer 均代表可变的字符序列，方法是一样的。



## 三者比较

String、StringBuffer 和StringBuilder的比较

1) StringBuilder和 StringBuffer非常类似，均代表可变的字符序列，而且方法也一样

2) String:不可变字符序列,效率低,但是复用率高。
若大量创建String str = "ljxxx"，则都会是常量池中同一个对象"ljxxx"不会重复创建

3) StringBuffer:可变字符序列、效率较高(增删)、线程安全

4) StringBuilder:可变字符序列、效率最高、线程不安全

String使用注意说明:

string s="a";		//创建了一个字符串
s +="b";		//实际上原来的"a"字符串对象已经丢弃了，现在又产生了一个字符串s+"b”(也就是"ab")。
如果多次执行这些改变串内容的操作,会导致大量副本字符串对象存留在内存中，降低效率。如果这样的操作放到循环中，会极大影响程序的性能。

结论:如果我们对String做大量修改,不要使用String



使用的原则,结论:

1.如果字符串存在大量的修改操作，一般使用 StringBuffer或StringBuilder

2.如果字符串存在大量的修改操作，并在单线程的情况,使用 StringBuilder

3.如果字符串存在大量的修改操作，并在多线程的情况,使用 StringBuffer

4.如果我们字符串很少修改，被多个对象引用，使用String,比如配置信息等

