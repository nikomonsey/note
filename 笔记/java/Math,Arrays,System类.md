# Math类

Math类包含用于执行基本数学运算的方法,如初等指数、对数、平方根和三角函数。

方法都为静态方法！！！！



## 常用方法

1) abs绝对值

2) pow求幂

3) ceil向上取整	返回大于等于该参数的最小整数，转换成***double类型***！！

4) floor向下取整	返回小于等于该参数的最小整数，转换成***double类型***！！

5) round四舍五入	该参数+0.5，再向下取整  3.5转成4，3.4转成3	返回***int类型***！！

6) sqrt求开方

```
//        1) abs绝对值
        int abs = Math.abs(-9);
        System.out.println(abs);
//        2) pow求幂
        double pow = Math.pow(2,4);
        System.out.println(pow);
//        3) ceil向上取整   返回大于等于该参数的最小整数，转换成double类型！！
        double ceil = Math.ceil(-3.001);
        System.out.println(ceil);
//        4) floor向下取整  返回小于等于该参数的最小整数，转换成double类型！！
        double floor = Math.floor(3.001);
        System.out.println(floor);
//        5) round四舍五入  该参数+0.5	再向下取整，3.4转成3,3.5转成4
			-3.4 加0.5变为-2.9，向下取整为-3 	-3.5 加0.5变为-3，向下取整为-3
        long round = Math.round(3.4);
        System.out.println(round);
//        6) sqrt求开方
        double sqrt = Math.sqrt(9);
        System.out.println(sqrt);
```



### 7) random求随机数（重！！）

思考:请写出获取a-b之间的一个随机整数,a,b均为整数?	例如2-7

公式为(int)(a+ Math.random()*(b-a+1))

```
//7) random求随机数 生成[0,1)大于等于0，小于1的随机数
//思考:请写出获取a-b之间的一个随机整数,a,b均为整数[a,b]范围     例如2-7
//公式：(int)(a+ Math.random()*(b-a+1))
//(int)(2+Math.random()*(7-2+1)
//解读：假设生成随机数为x，因为random生成数小于1，则范围为  2<= x <8
//而(int)强转int类型，会丢失掉小数位 x最大取值为7.99999 丢失小数位 为7，所以最大值就为7

for (int i = 0; i <10; i++) {
     System.out.println((int)(2+Math.random()*(7-2+1)));
}
```



8) max求两个数的最大值

9) min求两个数的最小值

```
//        8) max求两个数的最大值
        int max = Math.max(1, 9);
        System.out.println(max);

//        9) min求两个数的最小值
        int min = Math.min(3, 6);
        System.out.println(min);
```



# Random类

在java.util包中有random类，用于生成随机数(可以指定范围)

生成10个 0到100的随机数

```
Random random = new Random();
for (int i = 0; i < 10; i++) {
    int n= random.nextInt(100);
    System.out.println(n);
}
```

new Random();生成一个随机数种子

通过调用nextInt(n)生成int类型的0到n的随机数

## 常用方法

nextInt()	生成int类型的随机数

nextInt(n)	生成0~n范围的int类型随机数

nextFloat()	生成float类型的随机数

nextDouble	生成double类型的随机数







# Arrays类

这里讲的是 Arrays这个类，而不是 new Arrays()创建对象之后的操作

Arrays里面包含了一系列静态方法，用于管理或操作数组(比如排序和搜索)。



## 常用方法

### 1) toString返回数组的字符串形式

Arrays.toString(arr)

```
Integer[] integers = {1,12,133};
System.out.println(Arrays.toString(integers));
输出结果为：	[1,12,133]
```

arrays类toString方法源码为：	会在开头和结尾自动加上"["，"]"	最后调用stirng类的toStirng方法

```
StringBuilder b = new StringBuilder();
b.append('[');
for (int i = 0; ; i++) {
    b.append(String.valueOf(a[i]));
    if (i == iMax)
        return b.append(']').toString();
    b.append(", ");
}
```



### 2) sort排序(自然排序和定制排序重！！)

Integer arrl[]={1,-1,7,0,89};

```
自然排序，即按照从小到大的顺序排序
Integer arr[]={1,-1,7,0,89};
Arrays.sort(arr);
System.out.println(Arrays.toString(arr));
输出结果为：	[-1, 0, 1, 7, 89]

定制排序	按照从大到小的顺序排序
//sort排序，自定义排序顺序	通过传入一个接口Comparator，可自定义排序
Integer arr1[]={1,-1,7,0,89};
Arrays.sort(arr1, new Comparator<Integer>() {
      @Override
      public int compare(Integer o1, Integer o2) {
           return o2-o1;
      }
});
System.out.println(Arrays.toString(arr1));
输出结果为[89, 7, 1, 0, -1]

```

底层中sort方法源码：	体现了接口编程+动态绑定+匿名内部类的使用

```
1.执行TimSort.sort()方法
2.sort方法中继续执行binarySort(a, lo, hi, lo + initRunLen, c);方法
3.而binarySort()方法中，c.compare()通过动态绑定，获取我们传入的匿名内部类
while (left < right) {
                int mid = (left + right) >>> 1;
                if (c.compare(pivot, a[mid]) < 0)
                    right = mid;
                else
                    left = mid + 1;
            }
4.传入的匿名内部类	返回值是o2-o1 还是 o1-o2 会直接影响最后排序的顺序
new Comparator<Integer>() {
      @Override
      public int compare(Integer o1, Integer o2) {
           return o2-o1;
      }
    
```



### 3) binarySearch查找数据在数组中的索引值

通过二分搜索法进行查找，要求必须排好序，结果为数组中的索引值

int index = Arrays.binarySearch(arr, 3);

```
Integer[] integer ={1,5,133,244};
int i = Arrays.binarySearch(integer, 133);
System.out.println(i);
结果为	3	索引值为3
```

若查找数组中没有数据，则返回该数在数组中排的顺序的负数	return -(low + 1);
-3在数组中没有，所以返回负数	但是在数组中排第二个-5，-3，5，133，244，所以返回-2

```
Integer[] integer ={-5,5,133,244};
int i = Arrays.binarySearch(integer, -3);
System.out.println(i);
```



### 4) copyOf()数组元素的复制

Integer[] new Arr = Arrays.copyOf(arr, arr.length);

copyOf(arr,length)从arr数组中拷贝length个元素到新的数组中

若length长度大于arr数组的长度，则在新的数组后加上null

若length小于0，则抛出异常NegativeArraySizeException

```
Integer[] arr = {1,23,122,145};
Integer[] integers = Arrays.copyOf(arr, arr.length);
System.out.println(Arrays.toString(integers));
```

该底层使用	System.arraycopy()方法



### 5)fill()数组元素的填充

fill()第一个参数为目标数组，第二个参数为填充的数，会把目标数组中每一个元素都替换成填充的数

```
Integer[] num={1,12,23};
Arrays.fill(num,55);
System.out.println(Arrays.toString(num));
输出结果为：[55, 55, 55]
```



### 6)equals()比较两个数组元素内容是否完全一致

boolean equals = Arrays.equals(arr, arr2);

```
Integer[] arr = {1,23,122,145};
Integer[] arr2= {1,23,122,144};
boolean equals = Arrays.equals(arr, arr2);
System.out.println("equals="+equals);
```



### 7) asList()将一组值，转换成list

List<lnteger> asList = Arrays.asList(2,3,4,5,6,1);
System.out.println("asList=" + asList);

```
//asList()将一组值，转换成list
List<Integer> list = Arrays.asList(2, 3, 4, 5, 6, 1);
System.out.println("list="+list);
System.out.println("list的运行类型="+list.getClass());
```

1.asList方法,会将〔2,3,4,5,6,1)数据转成一个List集合

2.返回的 asList 编译类型 List(接口)

3.asList运行类型java.util.Arrays#ArrayList，是Arrays类的

静态内部类 private static class ArrayList<E> extends AbstractList<E>
							implements RandomAccess,java.io.Serializable





# System类

## 常用方法

1) exit()退出当前程序

```
System.out.println("ok1");
//exit(0) 表示程序退出    0表示一个状态，正常的状态
System.exit(0);
System.out.println("ok2");
输出结果为 ok1	没有ok2，因为程序已经退出
```



2) arraycopy():复制数组元素，比较适合底层调用,一般使用Arrays.copyOf完成复制数组.

五个参数	第一原数组，第二从原数组中哪个索引值开始复制，第三目标数组，第四复制到目标数组哪个索引值，第五从原数组中复制几个元素

```
int[] src = {1,2,3};
int[] dest = new int[3];//当前dest为{0,0,0}
//src原数组，0从元素中索引值0开始复制，dest目标数组，0从目标数组索引值0开始复制，3从原数组中复制3个元素
System.arraycopy(src,0,dest,0,3);

for (int i = 0; i <dest.length; i++) {
    System.out.print(dest[i]);
}
输出结果为 1 2 3 	即把src数组赋值到dest数组中
```



3) currentTimeMillis():返回当前时间与1970-1-1之间的时间差的毫秒数

```
System.out.println(System.currentTimeMillis());
```



4)gc:运行垃圾回收机制System.gc();