# Object类

为类层次的根类，每个累都可以使用object类中的方法



## equal和==方法

==和equals对比

==是一个比较运算符

既可以判断基本类型，又可以判断引用类型
如果判断基本类型，则是判断值是否相等，int i=10;double d=10.0，有基本数据类型，就是判断值是否相等
如果是判断引用类型，则是判断地址是否相等

```
public static void main(String[] args) {
        A a=new A();
        A b =a;
        A c=a;
        System.out.println(a==c);//true 本质上就是把a指向的地址付给了b
        System.out.println(b==c);//true
        B bobj = a;
        System.out.println(bobj == c);//true
        A a2 =new A();
        System.out.println(a ==a2);//false
    }
}
class A extends B{}
class B {}
```

equals只能用于引用类型比较

若没有重写equals方法，则object默认和==相同，比较地址是否相同
String和Integer类内部已经重写了equals方法

判断若为String类型，则把两个字符串拆分成char数组，一个一个比较

```
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

### 特殊情况

但是要注意Integer类的情况，因为java中有常量池的概念 在直接赋值的情况下，即用等号赋值，若范围在-128到127之间，则对象会直接从常量池中取对象，而不是新建对象

```
Integer n3 = 5;
Integer n4 = 5;
System.out.println(n3 == n4);//true
```

但是 若n3和n4等于500时，结果就为false，因为这时超过了常量池的范围，则会新建对象放入堆中，而==比较的时地址，则返回false

```
Integer n3 = 500;
Integer n4 = 500;
System.out.println(n3 == n4);//false
```

若是用新建 new Integer()的方式	则不管是不是在常量池范围内都会新建对象

```
Integer n1 = new Integer(5);
Integer n2 = new Integer(5);
System.out.println(n1 == n2);//false
```

### 练习

新建Person类 重写equals方法	

先判断 两个对象是否为同一地址，即用==判断，若同一地址 则直接返回true

obj instanceof Person 再判断obj是否为Person类型，Integer类型总不能和String类型对比吧	
instanceof运算符 判断是否为某一类型	只能用于数组和实例对象！！！

若为同一类型，则把obj向下转型为Person类型 用于获取Person中的属性
依次比较age gender name属性，若都相同，返回true

```
private String name;
    private int age;
    private char gender;

@Override
public boolean equals(Object obj) {
    if (this==obj)return true;
    if (obj instanceof Person){
        Person person = (Person)obj;
        return this.age==person.age &&
                this.gender==person.gender &&
                this.name.equals(person.name);
    }
        return false;

}
```



## hashCode方法

1)提高具有哈希结构的容器的效率！

2)有两个引用，如果指向的是同一个对象，则哈希值肯定是一样的！

3)有两个引用，如果指向的是不同对象，则哈希值是不一样的

4)哈希值主要根据地址号来的！，不能完全将哈希值等价于地址。

6)后面在集合中，hashCode 如果需要的话，也会重写

默认没重写hashCode方法，Object中的hashCode() 返回对象的32位jvm内存地址。也就是说如果对象不重写该方法，则返回相应对象的32为JVM内存地址。



## toString方法

Object类 默认返回:全类名+@+哈希值的十六进制，【查看Object 的 toString方法】

```
//Object类 toString方法
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

子类往往重写toString方法，用于返回对象的属性信息

当直接输出一个对象时，toString方法会被默认调用



## finalize()

当垃圾回收器确定不存在对该对象的更多引用时，由对象的垃圾回收器调用finalize()方法。

当对象被回收时，系统自动调用该对象的finalize方法。子类可以重写该方法,做一些释放资源的操作

什么时候被回收:当某个对象没有任何引用时，则jvm就认为这个对象是一个垃圾对象，就会使用垃圾回收机制来销毁该对象,在销毁该对象前，会先调用finalize方法。

垃圾回收机制的调用，是由系统来决定,也可以通过System.gc()主动触发垃圾回收机制。

垃圾回收机制会在程序结束之后再执行



# 变量

## 基本数据类型

数值型 ： 存放整数	byte[1]，short[2]，int[4]，long[8]	中括号内为占的字节数

​				存放小数 浮点型	float[4]，double[8]

字符型：	存放单个字符 'a' char[2]	c语音中char占1个字节 java中占2个字节

布尔类型：	boolean[1]	存放true和false



### 存储范围

byte	-128~127	一个字节八位	2的7次方	从0开始

short	-(2的15次方)~(2的15次方-1)	2的16次方

int	-(2的31次方)~(2的31次方-1)	2的31次方

long	-(2的63次方)~(2的63次方-1)	2的63次方



不能存在下面的情况，因为2L为long类型 占了8个字节 int只占了4个字节，放不下，会溢出

```
int n3 =2L;
```

但是下面可以，可以多不能少

```
int n1 = 1;
long n2 = n1;
```



### 浮点数使用细节

浮点数在机器中存放形式： 浮点数=符号位(第一位)+指数位+尾数位

浮点型常量默认为double类型，声明floatll类型要加 f或F



***若我们对运算结果是小数的进行相等判断要小心***

```
double n3 = 2.7;
double n4 = 8.1/3;
System.out.println(n3);//输出结果为2.7
System.out.println(n4);//输出结果不为2.7，而是一个很接近2.7的数字2.6999999999999997
```

n4为double类型 全写为	8.10000000000001/3	机器不知道能不能除完，就会造成结果不为2.7

应该要计算两个数的差值的绝对值，若在某个范围内，则说明相等
Math.abs方法为计算差值的绝对值，若小于0.00001则说明相等

```
if (Math.abs(n3-n4)<0.00001){
    System.out.println("相等");
}
```



### 字符类型使用细节

字符类型可表示单个字符，可存放'a'，'\t'，'罗'，97	即转义字符，汉字，数字等

字符在底层对应的就是数字

char类型可以保存int的常量值，但是不能保存int的变量值，需要强转

```
int n1=5;
char n2=5;
char n3=n1;//编译错误	要用强转char n3=(char)n1;
```

判断字符是否为数字或大写或小写

```
c>='a'&&c<='z'	c>='A'&&c<='Z'	c>='0'&&c<='9'
Character.isDigit('a')//判断传入的值是否为数字
Character.isLetter('a')		//判断是不是字母
Character.isUpperCase('a')		//判断是不是大写
Character.isLowerCase('a')		//判断是不是小写
```





## 自动类型转换

当java在进行赋值或运算时，精度小的类型自动转换为精度大的数据类型

char ---> int ---> long ---> float ---> double

byte ---> short --->int ---> long ---> float ---> double



### 自动转换细节

#### 1.多种类型的数据混合运算

有多种类型的数据混合运算时，

系统首先自动将所有数据转换成容量最大的那种数据类型，然后再进行计算

n1为int类型 1.1为double类型 n1+1.1默认转换成double最大的类型，比float精度大，无法自动转换

```
int n1 =5;
float n2 = n1+1.1;//编译报错
```



#### 2.(byte,short)和char之间不会相互自动转换



#### 3.把具体数赋值给byte

当把具体数赋值给byte时，不会因为具体数默认为int类型就不能自动转换，而是先判断该数是否在byte的范围内[-128,127]，若在，则直接赋值

```
byte n2 =10;//虽然10默认为int类型，但是先判断10在byte的范围内，所以直接赋值，不会报错
```

short和char和byte一样，都可直接赋值

但是float和double不行	不会先判断是否在float的范围内，因为为浮点型，精确度不够，会丢失数据

```
float n3 =1.1;//错
```



#### 4.byte, short,char三者计算

byte, short,char三者可以计算，在计算时首先转换为int类型，不管是byte和byte之间相加减，都会直接转换为int类型

```
byte n2 =10;
char n3 =15;
short n4 =20;

byte n5 =n2+n3;//错，byte和char相加会转换为int类型
byte n6 =n2+n2;//错，byte和byte相加也会转换为int类型，用byte接收不行
```



#### 5.boolean类型不参与类型自动转换



## 强制类型转换

可将占字节数高的数据类型转换为字节数小的数据类型，可能会导致精度降低或溢出



### 强制转换细节

char类型可以保存int的常量值，但是不能保存int的变量值，需要强转

```
int n1=5;
char n2=5;
char n3=n1;//编译错误
```



## 基本数据类型和String类型转换



### 基本数据转换为String类型

将基本数据类型后面加上+""即可

```
int n1=5;
char n2=5;

String s1=n1+"";
String s2 =n2+"";
System.out.println(s1+" "+s2);
```



### String类型转换为基本数据

使用基本数据类型的包装类，parsexxx方法，可将String类型转换为对应基本数据类型

```
String s3="100";
String s4="123.12";
int i = Integer.parseInt(s3);
System.out.println(i);
double v = Double.parseDouble(s4);
System.out.println(v);
```

而将String类型转换为char类型比较特殊
在计算机中，String转换为char类型的含义为，输出String的第一个字符

String的charAt(index)方法	返回处于index位置上的字符	检索值，从0开始

```
String s5="abc";
System.out.println(s5.charAt(0));
```

