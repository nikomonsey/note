# 常用类(Wrapper)

1．针对八种基本数据类型相应的引用类型—包装类

2、有了类的特点，就可以调用类中的方法。

| 基本数据类型 | 包装类    |
| ------------ | --------- |
| boolean      | Boolean   |
| char         | Character |
| byte         | Byte      |
| short        | Short     |
| int          | Integer   |
| long         | Long      |
| float        | Float     |
| double       | Double    |

Boolean和Character的父类直接为 Object

其他包装类的父类都为Number类，Number的父类才为Object



## 包装类和基本数据类型转换

1) jdk5前的手动装箱和拆箱方式，装箱:基本类型->包装类型,反之，拆箱

2) jdk5以后(含jdk5)的自动装箱和拆箱方式

自动装箱底层仍然调用的是valueOf方法,比如Integer.valueOf()	
自动拆箱用的是integer.intValue()	integer为Integer类型的对象！！！

```
	int n1=100;
	
    //jdk5前
    //手动装箱  int -> Integer
    Integer integer = Integer.valueOf(n1);
    
    //手动拆箱  Integer -> int
    int i = integer.intValue();
    
    
    int n2=100;
    
    //jdk5后
    //自动装箱
    Integer integer1 =n2;	//底层仍然是调用valueOf方法
    
    //自动拆箱
    int n3 =integer1;		//底层仍然是调用intValue方法

```



## 测试题面试题(重！！！)

```
Object obj1 = true? new Integer(1) : new Double(2.0);
System.out.println(obj1):
```

输出什么？

Object obj1 = true? new Integer(1) : new Double(2.0);为三元运算符，为true，则obj1赋予Integer(1)的值，

但是三元运算符是一个整体，而整体运算时，会把精度提升到最高的那个，容量最大的那个，即会把精度提高到double类型，最后输出的是 1.0，而不是1 (***重！！！！！***)

变量章节	-->	自动类型转换	-->	自动转换细节	-->	多种类型的数据混合运算	有记录



```
Object obj2;
if(true)
	obj2 = new Integer(1);
else
	obj2 = new Double(2.0);
System.out.println(obj2):
```

输出什么？

这时不再是三元运算，不会当成一个整体，所以不会把精度提升到Double类型，所以输出1 (***重！！！***)



## 包装类和String类型转换

String类型转包装类，可通过Integer的构造器public Integer(String s)来转换
也可通过	Integer.valueOf	自动装箱完成

```
public static void main(String[] args) {
    //包装类(Integer)-->String
    Integer i=100;
    //方法1
    String str1 = i.toString();
    //方法2
    String str2 = i+ "";
    //方法3
    String str3 = String.valueOf(i);

    //String -->包装类(Integer)
    String str4="1234";
    //方法1   自动装箱
    Integer n1 = Integer.valueOf(str4);
    //方法2   构造器
    Integer n2 = new Integer(str4);

}
```



## Wrapper常用方法

lnteger.MIN_VALUE		//返回最小值

Integer.MAX_VALUE	//返回最大值

Character.isDigit('a')		//判断是不是数字

Character.isLetter('a')		//判断是不是字母

Character.isUpperCase('a')		//判断是不是大写

Character.isLowerCase('a')		//判断是不是小写

Character.isWhitespace('a')		//判断是不是空格

Character.toUpperCase('a')		//转成大写

Character.toLowerCase('A')		//转成小写





## 面试题测试题(重！！！) 也是Integer的创建机制

```
public void method1() {
    Integer i = new Integer(1);
    Integer j = new Integer(1);
    System.out.println(i ==j); 	//false
    
    Integer m = 1;
    Integer n = 1;
    System.out.println(m == n);	//true
    
    Integer x = 128;
    Integer y = 128;
    System.out.println(x == y);	//false
}
```

输出什么

false true false

第二个为true的原因的	调用了valueOf自动装箱

Integer中valueOf方法的底层为：
i >= IntegerCache.low && i <= IntegerCache.high会先判断传入的i是否在一个范围中（-128到127）若超过才会创建对象，若在范围内会直接赋值（常量池）

```
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

这些前提都是不是直接创建Integer对象，而是通过自动装箱调用valueOf方法，才会判断，若直接new Integer 则会直接创建对象，不进行判断是否在常量池范围内





### Integer的创建机制：

调用valueOf自动装箱时，先判断传入的值都否在常量池范围内，若在，则直接赋值，每次的对象都是同一个，地址相同，若不在，才是真正的创建新的对象。若直接new Integer()时，会直接创建新对象，不进行判断





```
Integer n1=128;
int n2=128;
System.out.println(n1==n2);
```

结果为True		因为== 若有基本数据类型，则都是判断值是否相等	拆箱

基本数据类型和包装类比较, 只要实际值相同, ==比较为true(拆箱)	！！！！



