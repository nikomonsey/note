java	 class定义类  	在类中定义变量函数

this调用本类中定义的函数和变量		this()调用构造方法

构造块
直接在类中定义的代码块
用{}		执行顺序高于构造方法
静态变量static

当某个对象修改一次所有变量一起修改

对象创建之后 非静态成员才会被分配内存

extends
继承父类		只能继承一个父类
可以调用父类中的方法变量等

而implement是继承接口  可以继承多个接口

接口定义  若一个抽象类所有方法都是抽象的，就把这个类定义成为接口

public interface 接口名 



抽象类 abstract class ……
方法只需要声明不需要实现



对象类型转化 		向上转型

子对象转换为父对象

父类类型 父类对象=子类实例;

这时调用的方法为子类重写的方法或父类自己独有的方法



父对象转换为子对象		向下转型

父类类型 父类对象=子类实例;

子类类型 子类对象=()父类对象;

向下转型前要先向上转型





# 方法重载

参数列表不同（个数或者类型或者顺序不同！！！！）

可以改变返回类型

可以改变访问修饰符



# 方法重写

参数列表必须和重写方法相同

返回类型必须和重写方法相同

访问权限不能比父类被重写方法低

父类成员方法只能被子类重写

声明为final方法不能被重写

声明为static方法不能被重写，但能被再次声明





# JVM

![image-20221117211606321](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221117211606321.png)

## 1、堆区(heap):

用于存放所有对象，是线程**共享**的(注:数组也属于对象)(但不是所有数组都存放在堆区)

分为 新生代 ( Young )、老年代 ( Old )，新生代 ( Young ) 又被划分为三个区域：Eden、From Survivor、To Survivor。

垃圾回收器 GC	将内存中不再被使用的对象进行回收，GC中用于回收的方法称为收集器

对新生代的对象的收集称为minor GC

对老年代的对象的收集称为Full GC

 程序中主动调用System.gc()强制执行的GC为Full GC

在Minor GC的时候，我们都留一个存活区用来存放存活的对象，真正进行的区域是Eden+其中一个存活区，当我们的对象时长超过一定年龄时（默认15，可以通过参数设置），将会把对象放入老生代，当然大的对象会直接进入老生代，老生代采用的回收算法是标记整理算法。

只要是一个对象，就会得到一块新的内存空间，而不是一个类的对象都在一个内存空间中



## 3、方法区(method):

用于存放**类**信息、常量、静态变量、编译后的字节码等，是线程**共享**的(也被称为非堆，即None-Heap)



## 2、栈区(stack):

用于存放基本数据类型的数据和对象的引用，是线程**私有**的(分为:虚拟机栈和本地方法栈)

## 虚拟机栈

每个线程在创建时都会创建一个虚拟机栈，其内部保存一个个的栈帧（Stack Frame），对应着一次次的Java方法调用。

每个方法被执行的时候都会创建一个”栈帧”,用于存储局部变量表(包括参数)、操作栈、方法出口等信息。

每个方法被调用到执行完的过程，就对应着一个栈帧在虚拟机栈中从入栈到出栈的过程。

最顶部的栈帧称为当前栈帧，栈帧所关联的方法称为当前方法，定义这个方法的类称为当前类，该线程中，虚拟机有且也**只会对当前栈帧进行操作**

## 本地方法栈

作用与虚拟机栈相似

其区别不过是虚拟机栈为虚拟机执行Java 方法（也就是字节码）服务，而本地方法栈则是为虚拟机使用到的Native 方法服务。

Java通过native方法调用底层方法，java为跨平台语言，会失去对底层的控制，需要通过native方法来控制底层（由JVM调用，底层是c/c++实现）

## 程序计数器（PC Register）

当线程正在执行一个Java方法，程序计数器记录的是正在执行的JVM字节码指令的地址；如果正在执行的是一个Natvie（本地方法），那么这个计数器的值则为空（Underfined）。

程序计数器占用的内存空间很少，也是唯一一个在JVM规范中没有规定任何OutOfMemoryError（内存不足错误）的区域。

## 注：

程序计数器、虚拟机栈、本地方法栈	随线程而生、随线程而灭



# 数组初始化

## 静态

int[] arr0 = new int[]{1,2,3}

第二个[]中不能指定元素个数



## 动态

只指定数组长度不指定数组元素值

int[] arr1 = new int[4];

arr1[0] = 1;

arr1[1] = 2;

arr1[2] = 3;

...



静态和动态区别在于，前者是声明的时候就初始化，后者是先声明，在动态初始化



# Math类

| 方法   | 用途                       |
| ------ | -------------------------- |
| abs    | 绝对值                     |
| pow    | 求幂                       |
| ceil   | 向上取整(返回double类型)   |
| floor  | 向下取整数(返回double类型) |
| round  | 四舍五入(返回int类型)      |
| sqrt   | 求开方                     |
| random | 生成一个区间[0,1)的随机数  |
| max    | 求两个数的最大值           |
| min    | 求两个数的最小值           |

Math.floor(-4.2) = -5.0	返回double类型

Math.ceil(5.6) = 6.0		返回double类型

Math.round(-4.6) = -5	返回int类型	向数据大的方向四舍五入(数据+0.5再向下取整)

Math.round(-11.5) = -11



# Runtime类

Runtime类是一个运行时的描述类，在每一个JVM中都存在Runtime类的对象

该类主要代表了应用程序的运行环境。一个RunTime就代表一个运行环境。

作用：

- 执行一个进程。
- 调用垃圾回收(gc)。
- 查看总内存和剩余内存。

Runtime是单例的，可以通过`Runtime.getRuntime()`得到这个单例。

即 Runtime runtime = Runtime.getRuntime()获取runtime对象

RunTime类常用的方法：

(1) getRuntime()：该方法用于返回当前应用程序的运行环境对象。

(2) exec(String command)：该方法用于根据指定的路径执行对应的可执行文件。

## API列表

| public static Runtime getRuntime()                    | 返回单例的Runtime实例                           |
| ----------------------------------------------------- | ----------------------------------------------- |
| public void exit(int status)                          | 终止当前的虚拟机                                |
| public void addShutdownHook(Thread hook)              | 增加一个JVM关闭后的钩子                         |
| public Process exec(String command)throws IOException | 执行command指令，启动一个新的进程               |
| public int availableProcessors()                      | 获得JVM可用的处理器数量（一般为CPU核心数）      |
| public long freeMemory()                              | 获得JVM已经从系统中获取到的总共的内存数【byte】 |
| public long totalMemory()                             | 获得JVM中剩余的内存数【byte】                   |
| public long maxMemory()                               | 获得JVM中可以从系统中获取的最大的内存数【byte】 |

runtime.availableProcessors()方法 返回cpu核心个数！！



# 编译时类型与运行时类型



Dog dog = new Animal();

Dog就是 dog编译时的类型	Animal就是运行时的类型

编译时**引用变量（即dog）**只能调用其编译时类型所具有的方法

即dog只能调用Dog中的方法

运行时类型由实际**赋给**该变量的对象决定

引用变量在运行时则执行它运行时类型所具有的方法

若Dog和Animal中都有run方法，则执行Animal的方法体



# Int底层

```
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

封装成Integer类	high被设置为128 low-127，所以-127,128被缓存在内存中，内存地址都相同，所以若没超过-127,128，则直接从缓存中取，若超过，则需要新创建对象放入常量池内存中



# 实现GBK编码字节流转换到UTF-8

dst=new String(src,"GBK").getBytes("UTF-8")

就是新建一个String类 值为src 编码为GBK，通过getByte得到UFT-8编码的字节数组

## getBytes()方法

如果不指定字符集，则得到的是一个操作系统默认的编码格式的字节数组；如果指定字符集，则得到的是在指定字符集下的**字节数组**！！！！

dst=String.encode(String.decode(src,"GBK),"UTF-8") 	是错的

## decode:其它码–>unicode	解码

## encode:unicode–>其它码	编码

转换的思路都是 先解码，再编码

## 但String类没有encode和decode方法

new String(src,"GBK")相当于解码得到字符串	getBytes相当于编码得到字节数组



# JAVA位运算符

## <<左移运算符

二进制位往左挪

5 << 2 = 20

5的二进制为 101	向左两位	为10100	等于20

-2	<<	2 = -8

-2的二进制为 110(带符号位！！) 向左两位 等于 11000 第一位为符号位 等于-8

## >>（右移运算符）

二进制位往右挪

5 >> 2 = 1

5的二进制为 101	向右两位		为1 等于1

-2	>> 1  = -1

-2的二进制为 110(带符号位！！) 向右一位 等于 11 第一位为符号位 等于-1

## 向右位移一位 相当于十进制/2



## >>>（无符号右移运算符）

**Java中所有的数以补码的形式存储**！！！！运算之后，还要再补码回来！！！

与右移运算符的区别就是在于负数的运算。往右移，正数左边第一位补0，负数也补0

-2	>>> 1  = 1

-2二进制为 110 但无符号，所以第一位为0 所以为 010 往右一位  01 等于1



## ~（取反运算符）

~ 5 = -6

5二进制 0000 0101 原码（反码） 1111 1010 

由于他的符号位是1，系统认为这是一个负数的补码

负数输出时，先将补码减一得1111 1001

再取反得 1000 0110	(负数符号位不取反)

值为-6

正数做非运算时：取反、-1

负数做非运算时：+1，取反、



## 取反运算公式： ~a = -(a+1)!!!



# 各类型占字节数

## java采用uincode编码，两个字节表示一个字符

byte			1个字节

short	 	 2个字节

int		 	  4个字节

long			8个字节

float			4个字节

double		8个字节

char			2个字节

boolean	 若声明基本类型变量4个字节，若声明数组类型1个字节



# Java异常

![image-20221123102631484](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221123102631484.png)

Throwable分为

## Error	

不希望被程序捕获，或者程序无法处理的错误

ThreadDeath 程序死锁		VirtualMachineError	虚拟机错误(不受检查异常)



## Exception	

用户程序可能捕捉的异常情况，或者说程序可以处理的异常

RuntimeException运行时异常 		在运行时在会发现错误

非运行异常		在编译时就会发现错误



运行时异常分为：

NUllPointerException	空指针异常

ClassCastException	类型转换异常

ArithmeticException	算数异常

ArratIndexOutOfBoundsException	数组下标越界异常



非运行异常分为	(要手动抛出)

IOException	文件异常	包含了IO异常

SQLException	SQL异常

用户自定义异常

# 常量池

JVM中常量池分为三种

运行时常量池，Class常量池，字符串常量池

![image-20221123161209304](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221123161209304.png)

![image-20221123161227495](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221123161227495.png)

当Class文件被加载到内存后，Java虚拟机 **将Class文件常量池里的内容转移到运行时常量池里(运行时常量池也是每个类都有一个)**

而String str = "123"				返回的是字符串常量池的引用

String str =new String(“123”) 返回的是new出来对象存放的位置的引用

# equal方法

equal比较的规则由程序员控制，可重写方法

## object类

object类对象的里面的equal方法

```
public boolean equals(Object obj) {
    return (this == obj);
}
```

Object类中equals返回的是==的判断，所以在Object类中equals和==没有区别



## String类

String类对equal方法进行重写

比较是同一个常量池（都是从一个池里面出来的肯定相同）

其次再比较内容是否相同

String a="1234"; 

String b=new String("1234") ; 

b在创建之初会判断字符串常量池中有没有“1234”对象，有的话就将字符串常量池”1234“的地址赋值给b,所以a.equals(b)为true，但只是常量的地址，对象的地址仍然在堆区中



## Integer类

Integer类里的equals方法

转换成int类型，比较的值是否相同

new Integer(127)和new Integer(127)	equals和==都相同 

### 注：

但new Integer(127)和直接赋值127	==不相同	因为Integer的缓存数组大小为-128到127，超过就会创建新的对象

Integer n1=new Integer(5)和Integer n2=5	区别在于 若用new的方法创建n1，则n1不会从常量池中取，而是新建对象放在堆中，而n2=5，5大于-128小于127，在常量池范围内，所以会直接指向常量池的地址
这时n1 ==n2 就为false， 若超过了常量池范围，则都会新建一个对象放入堆中





# String类

## String创建对象情况（重！！）

String str1 = “aaa”;

在常量池中获取对象，（"aaa"属于字符串字面量，在编译时会为其创建一个字符串对象）



String str2 = new String("aaa");

一共会创建两个字符串对象，一个在堆中，一个在字符串常量池中（前提是字符串常量池中没有"aaa"对象）	堆中的对象指向字符串常量池中的对象

而String常量池比较特殊	若为""双引号加的变量会直接存储在字符串变量池中

若不是""	则可以使用intern()方法 是native方法（底层）

String.intern()作用：若常量池中已经包含了一个等于此String对象内容的字符串，就返回字符串常量池中该字符串的引用,没有则创建再返回

String s1 = new String("aaa");

String s2 = s1.intern();

String s3 = "aaa";

s1==s2  false   一个是堆内存的对象，一个是字符串常量池的对象

s2==s3 true

![image-20221123161037400](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221123161037400.png)



## String不可变

String类由final修饰，不可变！！！！，变只能新指向一个对象

String 对象的不可变指的是，任何对于 String 对象的操作不是在原内存地址上修改数据，而是重新指向一个新对象，新地址。

String s = "123";

String s = "1234";	不是修改s原来指向的地址位置，而是指向一个新的对象，即"123"还在，没有被修改，只是新创建了对象"1234",由s指向！！！！

```
public static void main(String[] args) {
    String s = "123";
    char[] ar = {'a','b'};
    tsest tsest = new tsest();
    tsest.A(s,ar);
    System.out.println(s+"  "+ar[0]);

}
public void A(String str,char[] ar){
    str="1234";
    ar[0]='c';
}
```

结果为

```
123  c
```

因为char[] ar 在调用函数时将实际参数的地址传递到函数中,将影响到实际参数。!!

相当于新创建了一个对象str指向“1234”，原来的s还是指向“123”



# HashCode和equals

每个类都会继承Object类，而Object有 equals和hashCode方法

每个类都可以重写equals和hashCode方法，以用于未来的编程需要

```
class A{
    private int i;



    @Override
    public int hashCode() {
        return 1000;
    }
}
```

重写hashCode方法之后 new A(xxx);出来的对象，hashCode都相同！！！！

重写时用alt+insert

这的意思是 若name和age的值（不是地址）相同，则equals方法返回true

重写有模板，软件已经帮我们写好了

![image-20221124142435263](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221124142435263.png)



这的意思是 若name和age的值（不是地址）相同，则返回相同的hashCode值



![image-20221124142553009](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221124142553009.png)





# 内部类

内部类和外部类成员重名。优先用内部类，若要用外部类成员(外部类名.this.成员)

若要访问静态的，则可以创建外面类对象之后直接调用

不是静态，则需要创建外部类对象之后，还要再new一个内部类对象，再调用

内部类访问规则

成员内部类不是静态的:	外部类名.内部类名.对象名=new 外部类名().new  内部类名();

是静态的，	外部类名.内部类名 对象名=new 外部类名.内部类名()；

如果定义类在局部位置(方法中/代码块) :

## (1) 局部内部类 相当局部变量

内部类不能被public、private、static修饰；！！！！地位相当于局部变量，不能有修饰符

在外部类中不能创建内部类的实例；

创建内部类的实例只能在包含他的方法中；

内部类访问包含他的方法中的变量必须有final修饰；！！！！

外部类不能访问局部内部类，只能在方法体中访问局部内部类，且访问必须在内部类定义之后。



有类名



## (2) 匿名内部类 相当局部变量

本质是类

内部类

该类没有名字

同时还是一个对象

匿名内部类的语法比较奇特，它既有定义类的特征，也有创建对象的特征，,因此可以调用匿名内部类方法。

可以直接访问外部类的所有成员，包含私有的

不能添加访问修饰符,因为它的地位就是一个局部变量

作用域:仅仅在定义它的方法或代码块中。

匿名内部类—访问---->外部类成员[访问方式:直接访问]

外部其他类—不能访问----->匿名内部类（因为匿名内部类地位是一个局部变量)

如果外部类和匿名内部类的成员重名时，匿名内部类访问的话，默认遵循就近原则,如果想访问外部类的成员，则可以使用(外部类名.this.成员)去访问









定义在成员位置

## (3) 成员内部类 相当成员

说明:成员内部类是定义在外部类的成员位置,并且没有static修饰。

1.可以直接访问外部类的所有成员，包含私有的

2.可以添加任意访问修饰符(public、protected、默认、private),因为它的地
位就是一个成员。

3.作用域和外部类的其他成员一样,为整个类体，在外部类的成员方法中创建成员内部类对象,再调用方法.

4.成员内部类—访问---->外部类成员(比如;属性)[访问方式:直接访问]

5.外部类—访问------>成员内部类 访问方式:创建对象，再访问

6.外部其他类—访问---->成员内部类

7.如果外部类和内部类的成员重名时，内部类访问的话，默认遵循就近原则，如果想访问外部类的成员，则可以使用(外部类名.this.成员)去访问





## (4)静态内部类

说明:静态内部类是定义在外部类的成员位置，并且有static修饰

1.可以直接访问外部类的所有静态成员，包含私有的，但不能直接访问非静态成员

2.可以添加任意访问修饰符(public、protected、默认、private),因为它的地位就是一个成员。

3.作用域:同其他的成员，为整个类体

4.静态内部类—访问---->外部类(比如:静态属性)[访问方式:直接访问所有静态成员]

5．外部类—访问------>静态内部类访问方式:创建对象，再访问

6．外部其他类—访问----->静态内部类

7．如果外部类和静态内部类的成员重名时，静态内部类访问的时，默认遵循就近
原则，如果想访问外部类的成员，则可以使用(外部类名.成员)去访问



# 数据类型转换

当使用+,-,*,/运算操作时

若两个操作数，一个是double类型，另外一个会自动转换成double类型，且结果也为double类型

若两个操作数，一个是float类型，另外一个会自动转换成float类型，且结果也为float类型

若两个操作数，一个是long类型，另外一个会自动转换成long类型，且结果也为long类型

被final修饰的变量不会自动改变类型，当两个final修饰相操作，结果会根据左边变量而转化

当操作数为 byte,short,int,char两个数都会被转换成int类型，结果也为int类型



# volatile和synchronized

免程序在多线程竞争情况下读到不正确的值需要保证内存可见性，即当一个线程修改了volatile修饰的变量的值，volatile会保证新值立即同步到主内存，以及每次使用前立即从主内存读取。



## volatile可以避免程序在多线程竞争中读到不正确的值，且修饰变量



## synchronized可以修饰方法、代码块或对象，并不修饰变量。





# GC垃圾回收器

一个对象成为垃圾是因为	不再引用着它，但是线程并非如此

线程的释放是在run方法结束以后，此时线程的引用可能并未释放，只有离开了run方法才能把它清理掉





# 接口和抽象类





# a=i++和i++和i=i++

a=i++操作后，a的值不发生改变，其值仍然为i。

它等价于 a = i；i = i + 1；

i = i++ 相当于第一个i为全局变量 第二个i为局部变量,把第一个i设为i1 第二个i设为i2

i1 = i2; i2=i2+1; 而i1还是等于原来的i2值

即 int i=0;

i=i++；	局部变量	记！！！！

sout(i)结果为0！！！

要避免 就改成 i++;或i=++i;！！！！！1





# maven和ant

## Ant：一种基于java的build工具

能用ant编译java类生成class文件

ant能把相关层构建成jar包

ant把整个项目生成web包，并公布到Tomcat

优点：

跨平台性	操作简单(由一个内置任务和可选任务组成，执行时需要一个xml构建文件)	ant能集成到开发环境中	

ant能调用target树，能运行各种task



## maven：提供高级项目管理工具.....

maven	具备ant的功能

还有其余基本功能：

使用Project Object Model来软件项目管理

内置了很多隐式规则，使得构建文件更加简单

内置依赖管理和Repository来实现依赖的管理和统一存储

**内置了软件构件的生命周期**！！！！！！

优点：

拥有约定，知道你的代码在哪，放到哪去

拥有生命周期

仅需要定义一个pom.xml文件，然后把源代码放到默认的文件夹位置

拥有依赖管理，仓库管理





# Servlet和CGI

调用一个CGI程序，服务器就要重新启用一个进程

和CGI相比，Servlet移植性和效率更高，cgi会被servlet取代

setvlet处于服务器进程中，通过多线程方式运行service方法

Servlet是但西安测绘给你的，多线程情况下是非线程安全的



# Object方法

clone()	创建并返回此对象的一个副本

equals()	指示其他某个对象是否与此对象相等

**finalize()**	当垃圾收集器确定不存在对该对象的更多引用时，由对象的垃圾回收器调用此方法

getClass()	返回此Object的运行时类

hashCode()	返回该对象的哈希码值

notify()		唤醒此对象监视器上等待的单个线程

notifyAll()	唤醒此对象监视器上等待的所有线程

toString()	返回此对象的字符串表示

wait()		使当前线程等待





# 前台线程和后台线程

应用程序的主线程(***main***)以及使用Thread构造的线程都默认为**前台线程**

***使用new Thread方式创建的线程默认都是前台线程。***

通过BeginXXX方法运行的线程都是后台线程。

线程池线程也就是使用 ThreadPool.QueueUserWorkItem()和Task工厂创建的线程都默认为后台线程

托管线程池中的线程都是后台线程。

## 区别

应用程序必须运行完所有的前台线程才可以退出；

而对于后台线程，应用程序则可以不考虑其是否已经运行完毕而直接退出，所有的后台线程在应用程序退出时都会自动结束。！！！！

后台线程不会阻止进程的终止。属于某个进程的所有前台线程都终止后，该进程就会被终止，所有剩余的后台线程都会停止且不会完成。

可以在任何时候将前台线程修改为后台线程，方式是设置Thread.IsBackground 属性。

Thread th;       

th.IsBackground = true;

不管是前台线程还是后台线程，如果线程内出现了异常，都会导致进程的终止。



# final关键字

1.关键字final表示最终的，不可变的。

2.关键字final可以修饰变量、方法，还有类

3.final修饰的类无法被继承

4.final修饰的方法无法被覆盖，无法被重写

5.final控制不了能不能调用的问题，表示的是最后的，不能变的，不能改的。

6.final修饰的变量只能赋一次值

7.final修饰的引用：

该引用只能指向1个对象，并且它只能永远指向该对象，并且无法指向其他对象。

并且在该方法执行过程中，该引用指向对象之后，该对象不会被垃圾回收期回收

直到当前方法结束，才会释放空间。



8.虽然final的引用指向对象A后，不能再重新指向对象B，

但是对象A内部的数据可以被修改。

9.final修饰的实例变量，只能赋值一次。

结论：因为实例变量没有手动赋值，系统会赋默认值。

因而要求final修饰的实例变量必须手动赋值。这个手动赋值，在变量后面赋值可以，在构造器

中赋值也可以。（显示赋值、构造器赋值、代码块赋值）



10.final修饰的实例变量一般添加static修饰

终极结论：static final联合修饰的变量称为“常量”，常量名建议大写，每个单词间用下划线衔接。

常量和静态变量一样，区别在于：常量的值不能变。相同点在于：都是储存在方法区，并且都是

在类的加载时初始化。常量一般都是公共的，public修饰。



## finale修饰变量相加

***final被修饰的变量的类型不会变化了！！！***

```
byte b1=1,b2=2,b3,b6,b8;
final byte b4=4,b5=6,b7=125;
b6=b4+b5; //（1）不会发生编译错误
b3=(b1+b2); //（2）会发生编译错误
b8=b5+b7; //（3）会发生编译错误
```

因为当变量被final修饰之后就变成常量

运算是两个变量时，会先根据情况对类型进行提升，在进行运算

b1+b2,在相加之前就先把b1,b2转换成int类型，而b3为byte类型，所以编译错误

当运算是两个常量，会先进行运算，再判断结果是否满足类型范围

b4+b5，先相加，得10，而byte的范围为-128到127，未超过，编译成功

b5+b7，相加得131，大于byte范围，编译错误，可强转byte类型

通常来说byte会自动转换为int类型，但是final修饰的不会，所有要强转！！



byte运算会自动提升为int，而final修饰的变量不会提升类型，当2个final修饰相加时候会根据左边变量的类型而转化，作为这2个final变量的最终类型，final int和final byte相加是整形和byte型都可以，不需要强制转换。





# 值传递

StringBuffer为可变长String	所以value数组不会指向常量池，而是指向堆中另外一块区域

```
	StringBuffer a = new StringBuffer("A");
    StringBuffer b = new StringBuffer("B");
    operate(a,b);
    System.out.println(a+"."+b);
    
    operate(StringBuffer x,StringBuffer y){
        x.append(y);
        y=x;
    }
```

输出结果为 AB.B



<img src="C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20221203104209113.png" alt="image-20221203104209113"  />

a.append(b)，把b元素插入到a元素的结尾，会改变地址中具体的值，





# 各类型默认值和范围

int byte short float double	默认值为0

boolean	默认值为false

引用类型数组	默认值为null

byte	-2^7-1 到 2^7-1	一个字节

char	0 到 2^16 -1		两个字节	没有负数，字符负数没有意义

short	-2^15-1 到 2^15-1	两个字节	

int	-2^31-1 到 2^31-1		四个字节

long	-2^63-1 到 2^63-1	八个字节





# try catch fianlly执行顺序

若try中有return，先保存return的结果，执行finally之后，再执行try的retrun

```
try{
    a=1；
    return a;
    }
finally{
    a=2
    }
```

输出结果为1，因为是先保存了return中的结果，再执行finally

若finally中也有return，则会覆盖try中的return，执行finally的

执行了return就会结束函数！！！



# if中=和==区别

```
Boolean flag = false;
if (flag = true){
    System.out.println("flag="+flag);
    System.out.println("true");
}
else {
    System.out.println("flag="+flag);
    System.out.println("false");
}
```

=这时是赋值，最后就等于 if(flag)，判断的是flag的值





# main函数不能调用函数之外的变量

```
public int a=1
public static void main(String[] args) {
	int c;
	c=a;
}
```

不能调用，非静态变量不能被静态方法引用



# 添加多个数据到一个集合中

List<Book> books =Array.asList(book1,book2,book3);

相比books.add()方法，更加快速不繁琐



# 序列化 Serializable接口

一个类只有实现了Serializable接口，它的对象被序列化了。

## 什么是序列化？

轻松化这个对象与对象之间传输的状态转换的格式，为可以保持或传输的序列。序列化为相对化，结合流。两个过程中，可以传输数据。

序列化的意思是将对象的状态转换为字节流，序列化是将 Java 对象转换为静态字节流（序列），然后我们可以将其保存到文件、数据库或者是通过通过网络传输。





# 判断数据类型是否相同

三种运算符typeof instanceof == 

typeof 用于数组 字符串 布尔 undefined 函数类型之间比较

instanceof 用于数组和实例对象之间比较	判断obj实例对象是否为Person类！！

```
System.out.println(obj instanceof Person);
```

==	用于判断 null



# Comparator和Comparable的区别

Comparable和Comparator都是两个接口，接口都可以用来实现集合中元素的比较、排序

Comparable接口将比较代码嵌入自身类中**，**而Comparator既可以嵌入到自身类中，也可以在一个独立的类中实现比较

即comparable 能和自己比较，即只需要往方法中传入一个参数，这个参数与自身比较
而comparator需要传入两个参数，进行相互比较

![image-20230504134822770](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230504134822770.png)





# 引用类型传递的是地址(重！！)

```
B b =new B();		//创建B类对象b
int[] arr = {1,2,3};	//创建arr数组
b.add(arr);			//把arr作为参数传入B的add方法中
这时 若在add方法中修改arr数组中的值，则会直接修改
因为在main方法中 arr数组对象也存放在堆中，而向add方法传入arr对象时，为引用类型，所以传递的是地址
add方法栈 指向了堆中 arr的地址，所以修改会直接修改堆中arr数组的值
```

![image-20230517083841867](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230517083841867.png)

而若传入的是 int a = 10 这样的类型，传入的就值，不是其本身的地址 而int a =10 不会在堆中创建一块区域存储



```;
B b =new B();	
Person p=new Person();
p.name = "jack";
p.age = 18;
b.test200(p);	//这时传入的p也是地址传递	
System.out.println(p.age);

public void test200(Person p){
	p =null; 		//这时p=null，但是在main方法中输出没有报空指针异常，
						因为p=null相当于test200方法栈中的p 不再指向堆中的p对象，而是指向一个null
						但是main方法栈中的p仍然指向堆中的p对象
}

public void test200(Person p){
	p = new Person();
	p.age = 28; 		//这时也不会影响main方法中的输出，因为p = new Person，相当于指向了堆中一块新的区域地址，而main方法栈中的p任然指向原来堆中的p对象
}

```

