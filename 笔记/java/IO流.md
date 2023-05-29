# IO流

## 文件

文件在程序中时以流的形式来操作的

流：数据在数据源(文件)和程序(内存)之间经历的路径



### 创建文件

创建文件对象相关构造器和方法相关方法：

new File(String pathname)//根据路径构建一个File对象
new File(File parent,String child)/根据父目录文件+子路径构建
new File(String parent,String child)//根据父目录+子路径构建

再调用createNewFile方法 创建新文件



### 获取文件信息

getName文件名字，getAbsolutePath文件绝对路径，getParent文件父类目录，length文件大小字节(中文一个占3个字节utf-8，英文占一个字节)，exists文件是否存在，isFile，isDirectory是否是文件或目录

```
File file = new File("e:\\new1.txt");
        System.out.println("文件名字="+file.getName());
        System.out.println("文件绝对路径="+file.getAbsolutePath());
        System.out.println("文件父类目录="+file.getParent());
        System.out.println("文件大小(字节)="+file.length());
        System.out.println("文件是否存在="+file.exists());    //T
        System.out.println("是不是一个文件="+file.isFile());   //T
        System.out.println("是不是一个目录="+file.isDirectory());  //F
```





### 文件操作方法

目录的操作和文件删除

mkdir()创建一级目录、mkdirs()创建多级目录
delete()删除空目录或文件，必须为空目录，不能删除有文件的目录
返回类型都为boolean类型





## 流的分类

按操作数据单位不同分为:字节流(8 bit)二进制文件,字符流(按字符)文本文件，有中文的情况

按数据流的流向不同分为:输入流，输出流

按流的角色的不同分为:节点流，处理流/包装流

| (抽象基类) | 字节流       | 字符流 |
| ---------- | ------------ | ------ |
| 输入流     | InputStream  | Reader |
| 输出流     | OutputStream | Writer |

InputStream,OutputStream,Reader,Writer都为抽象类，不能直接创建，需要用它们的子类

使用字符流时，需要手动刷新flush或关闭close，否则数据无法写入





## 节点流和处理流

![image-20230506111533433](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230506111533433.png)

节点流可以从一个特定的数据源读写数据	如FileReader FileWriter
比较固定，要么只能文件File，要么只能数组Byte，要么只能二进制FileInputStream



处理流(也叫包装流)是“连接”在已存在的流（节点流或处理流)之上，为程序提供更为强大的读写功能,
如BufferedReader、BufferedWriter

BufferedReader类中有属性Reader，即可以封装一个节点流，该节点流可以是任意的，只要是Reader子类	Reader子类有FileReader等等，可以把这些封装到BufferedReader中

```
public class BufferedReader extends Reader {

    private Reader in;
```

![image-20230506111319879](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230506111319879.png)

BufferedWriter类中也有属性write，也可以封装字节流



### 节点流和处理流的区别和联系

节点流是底层流/低级流,直接跟数据源相接。
处理流(包装流)包装节点流，既可以消除不同节点流的实现差异，也可以提供更方便的方法来完成输入输出。
处理流(也叫包装流)对节点流进行包装，使用了修饰器设计模式，不会直接与数据源相连

处理流的功能主要体现在以下两个方面:
性能的提高:主要以增加缓冲的方式来提高输入输出的效率。
操作的便捷:处理流可能提供了一系列便捷的方法来一次输入输出大批量的数据,使用更加灵活方便



### 节点流

#### InputStream

字节输入流

InputStream抽象类是所有类字节输入流的超类

常用的子类：
FilelnputStream:文件输入流
BufferedInputStream:缓冲字节输入流
ObjectlnputStream:对象字节输入流



##### FilelnputStream

###### 构造方法

FileInput Strean(File file)通过打开一个到实际文件的连接来创建一个FileIrputStrean，该文件通过文件系统中的 File 对象 file 指定。

FileInput Strean(FilcDescriptor fdobj)通过使用文件描述符fdobj创建一个FileIputStrean，该文件描述符表示到文件系统中某个实际文件的现有连接。

FileInput Strean(String name)通过打开一个到实际文件的连接来创建一个 FileIputStrean，该文件通过文件系统中的路径名name指定。

###### 常用方法

intavailable()
返回下一次对此输入流调用的方法可以不受阻塞地从此输入流读取（或跳过）的估计剩余字节数。

void close()
关闭此文件输入流并释放与此流有关的所有系统资源。

preotectad void finalize()
确保在不再引用文件输入流时调用其close方法。

getChanmel()
返回与此文件输入流有关的唯— FileChannel对象。

getFD()
返回表示到文件系统中实际文件的连接的 FileDescriptor对象，该文件系统正被此 FileTnputStrean使用。

int read()

从该输入流读取一个字节的数据。如果没有输入可用，此方法将阻止。
如果返回-1，表示读取完毕从此输入流中读取一个数据字节。	
返回类型为int，即ascii码，强转为char类型即可

```
while ((readData = fileInputStream.read())!=-1){//若不为-1，即没到结尾，则一直循环输出
    System.out.println((char)readData);
}
```



int read (byte[] b)

从此输入流中将最多b.length个字节的数据读入一个 byte数组中。
如果返回-1，表示读取完毕从此输入流中读取一个数据字节。	
返回类型为int，表示读取的字节数！！！！！！不是ascii码，ascii码存在byte数组中了！！

文件中的数据为hello,world，超过了8个字节，第一次while循环，readLen=8,创建字符串,读取字节数组，从第0个读取readLen长度的数据，第二次读取3个字节readLen=3，再次创建字符串，第三次已经读完，返回-1

```
int readLen = 0;
//字节数组
byte[] b = new byte[8]; //一次性读取8个字节
try {
    fileInputStream = new FileInputStream(filePath);
    while ((readLen = fileInputStream.read(b))!=-1){
        System.out.print(new String(b,0,readLen));
    }
```



intread (byte[],b int off, int len)
从此输入流中将最多len个字节的数据读入一个byte数组中。

long skip (long n)
从输入流中跳过并丢弃n个字节的数据。



#### FileOutputStream

写入文件时，若文件不存在，则自动创建，前提是目录要存在！！！

且每次写入都会覆盖原有文件，若不想覆盖，调用构造器

```
public FileOutputStream(String name, boolean append){}
```

即，在文件目录后加参数true，表示写入内容时，追加到文件后

```
FileOutputStream fileOutputStream = new FileOutputStream(filePath，true);
```

##### 常用方法

close()关闭此文件输出流并释放与此流有关的所有系统资源。

finalize()清理到文件的连接，并确保在不再引用此文件输出流时调用此流的close方法。

getChannel()返回与此文件输出流有关的唯一FileCharel对象。

getFD()返回与此流有关的文件描述符。

write(byte[] b)
将b.length个字节从指定byte数组写入此文件输出流中。
String.getBytes方法可返回byte[]数组

```
String b = "ljxxx,hello";
fileOutputStream.write(b.getBytes());
```



write (byte[]_b.int off，int_ len)
将指定byte数组中从偏移量off开始的 len个字节写入此文件输出流。

```
fileOutputStream = new FileOutputStream(filePath);
//fileOutputStream.write('a'); //char会自动转换为int类型
String b = "ljxxx,hello";
fileOutputStream.write(b.getBytes(),0,5);
```



write(int b)
将指定字节写入此文件输出流。

```
fileOutputStream = new FileOutputStream(filePath);
fileOutputStream.write('a'); //char会自动转换为int类型
```



##### 练习

复制文件

使用byte[]字节数组来读取数据，一次读取1024个字节
使用fileOutputStream.write(b,0,readLen);来写入数据，当最后一次读，只有3个字节，没有把字节数组填满，只会把前三个字节替换，后面的字节还是原来的字节，若使用fileOutputStream.write(b),则会把后面的字节也给写入，使用(b,0,readLen)限制写入的字节个数，每次为读取的字节数

```
String srcFilePath="e:\\tom.jpg";
String destFilePath="e:\\tom2.jpg";
FileInputStream fileInputStream = null;
FileOutputStream fileOutputStream = null;
byte[] b = new byte[1024];
int readLen = 0;
try {
    fileInputStream = new FileInputStream(srcFilePath);
    fileOutputStream = new FileOutputStream(destFilePath);

    while ((readLen = fileInputStream.read(b)) != -1){
    	//边读边写	
        fileOutputStream.write(b,0,readLen);//一定要用这个
    }
} catch (IOException e) {
    e.printStackTrace();
}finally {
    try {
        if (fileInputStream!=null){
            fileInputStream.close();
        }
        if (fileOutputStream!=null){
            fileOutputStream.close();
        }
    }
    catch (IOException e) {
        e.printStackTrace();
    }
```





#### FileReader

##### FileReader相关方法:

1) new FileReader(File/String)
2) read:每次读取单个字符,返回该字符，如果到文件未尾返回-1

```
fileReader = new FileReader(filePath);
while ((data = fileReader.read()) != -1){
    System.out.print((char)data);
}
```

3) read(charD):批量读取多个字符到数组，返回读取到的字符数,如果到文件末尾返回-1

```
fileReader = new FileReader(filePath);
while ((dataLen = fileReader.read(c)) != -1){
    System.out.print(new String(c,0,dataLen));
}
```

相关API:
1) new String(char):将char[转换成String
2) new String(char[],off,len):将char[]的指定部分转换成String

#### FileWriter

FileWriter使用后，必须要关闭(close)或刷新(flush)，否则写入不到指定的文件！！！
close()方法等于 flush()+关闭，在flush()中才会真正的写入文件，若没有flush或close，数据只会在内存中！！

##### FileWriter常用方法:

1) new FileWriter(File/String):覆盖模式，相当于流的指针在首端
2) new FileWriter(File/String,true):追加模式，相当于流的指针在尾端
3) write(int):写入单个字符
4) write(char[):写入指定数组
5) write(char[],off,len):写入指定数组的指定部分
6) write (string):写入整个字符串
7) write(string,off, len):写入字符串的指定部分

相关APl: 
String类:toCharArray:将String转换成char





### 处理流

#### BufferedReader和 BufferedWriter

BufferedReader和 BufferedWriter属于字符流，是按照字符来读取数据的

关闭时处理流，只需要关闭外层流即可，因为处理流会调用节点流，最后操作的还是节点流，关闭外层流(处理流)也会关闭节点流

使用Buffered时，不要去操作二进制文件[声音，视频，doc，pdf，图片等等]，可能会导致文件损坏
因为操作的是字符，而不是字节！！！



##### 常用方法

readLine方法		一行一行的读取

若读取到换行符，则会终止此行的读取	读取不包括任何行的终止字符
若已经读取到行的末尾，则返回null

newLine()	向文件中传入一个换行符，即换行



拷贝文件

```
String srcFilePath = "e:\\hello.txt";
String destFilePath = "f:\\hello.txt";
BufferedReader bufferedReader = new BufferedReader(new FileReader(srcFilePath));
BufferedWriter bufferedWriter = new BufferedWriter(new FileWriter(destFilePath, true));
String line;
while ((line= bufferedReader.readLine())!=null){
    bufferedWriter.write(line);
    bufferedWriter.newLine();
}
bufferedReader.close();
bufferedWriter.close();
```



#### BufferedInputStream和BufferedOutputStream

BufferedInputStream是字节流，在创建BufferedInputStream时，会创建一个内部缓冲区数组

BufferedOutputStream是字节流，实现缓冲的输出流，可以将多个字节写入底层输出流中，而不必对每次字节写入调用底层系统



拷贝字节文件(图片，视频等等)

```
String srcFilePath = "e:\\tom.jpg";
String destFilePath = "f:\\tom.jpg";
BufferedInputStream bufferedInputStream = null;
BufferedOutputStream bufferedOutputStream = null;
byte[] b =new byte[1024];
int readLine = 0;
try {
    bufferedInputStream = new BufferedInputStream(new FileInputStream(srcFilePath));
    bufferedOutputStream = new BufferedOutputStream(new FileOutputStream(destFilePath,true));
    while ((readLine = bufferedInputStream.read(b))!=-1){
        bufferedOutputStream.write(b,0,readLine);
    }
    System.out.println("拷贝成功");
} catch (IOException e) {
    e.printStackTrace();
}finally {
    try {
        bufferedInputStream.close();
        bufferedOutputStream.close();
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```





#### 对象流（序列化）

ObjectlnputStream和ObjectOutputStream都为对象流，用于处理对象

int num =100；要将int类型的100保存到文件中，若用其他的保存方式，只会保存100值，而不会保存类型int
Dog dog = new dog("tom",12)；保存到文件中，并且能从文件中恢复。

而上面的步骤就是 将基本数据类型或者对象进行序列化和反序列化的操作

##### 序列化和反序列化

序列化就是在保存数据时,保存数据的值和数据类型
反序列化就是在恢复数据时，恢复数据的值和数据类型
需要让某个对象支持序列化机制，则必须让其类是可序列化的，为了让某个类是可序列化的，该类必须实现如下两个接口之一：
Serializable	//这是一个标记接口	没有方法
Externalizable	//该接口有方法需要实现，通常实现上面的Serializable接口 



##### 功能:

提供了对基本类型或对象类型的序列化和反序列化的方法
ObjectOutputStream提供序列化功能
ObjectlnputStream提供反序列化功能

序列化 保存int，boolean，char，double，String等类型	保存之后的格式为系统定义
Dog类要实现Serializable类，用于序列化

```
public static void main(String[] args) throws Exception {
        //序列化后，保存的文件格式，不是纯文本，而是按照它的格式来保存
        String filePath = "e:\\spring.txt";
        ObjectOutputStream objectOutputStream = new ObjectOutputStream(new FileOutputStream(filePath));
        objectOutputStream.write(100);  //底层int -->Integer (实现了Serializable)
        objectOutputStream.writeBoolean(true);  //底层boolean -->Boolean (实现了Serializable)
        objectOutputStream.writeChar('a');  //底层char -->Char (实现了Serializable)
        objectOutputStream.writeDouble(1.5);  //底层double -->Double (实现了Serializable)
        objectOutputStream.writeUTF("ljxxx");   //底层String(实现了Serializable)
        objectOutputStream.writeObject(new Dog("tom",12));
        objectOutputStream.close();
        System.out.println("文件保存完成");
    }
}

class Dog implements Serializable{
    String name;
    int age;
```

保存的信息为	乱码

>  wd a??       ljxxxsr com.ljxxx.outputStream_.DogEI添? I ageL namet Ljava/lang/String;xp   t tom



反序列化	顺序要与序列化中的顺序一致，即序列化时先int，boolean，反序列化时也要一样

```
String filePath = "e:\\spring.bat";
ObjectInputStream objectInputStream = new ObjectInputStream(new FileInputStream(filePath));
System.out.println(objectInputStream.readInt());
System.out.println(objectInputStream.readBoolean());
System.out.println(objectInputStream.readChar());
System.out.println(objectInputStream.readDouble());
System.out.println(objectInputStream.readUTF());
Object object = objectInputStream.readObject(); //底层 Object -->Dog
System.out.println(object.getClass());
System.out.println(object);
objectInputStream.close();
```



##### 注意事项

1)读写顺序要一致！！！

2)要求序列化或反序列化对象,需要实现 Serializable！！！

3)序列化的类中建议添加SerialVersionUID,为了提高版本的兼容性
当Dog类修改时，不会当Dog为一个新的类，而是一个新的版本

```
class Dog implements Serializable{
	private static final long serialVersionUID = 1L;
}
```

4)序列化对象时，默认将里面所有属性都进行序列化，但除了static或transient修饰的成员！！！
transient关键字，使成员变量不会被序列化！！

5)序列化对象时，要求里面属性的类型也需要实现序列化接口！！！

6)序列化具备可继承性,也就是如果某类已经实现了序列化，则它的所有子类也已经默认实现了序列化



## 标准输入输出流

|                     | 类型         | 默认设备 |
| ------------------- | ------------ | -------- |
| System.in 标准输入  | InputStream  | 键盘     |
| System.out 标准输出 | OutputStream | 显示器   |



```
//System类的 public final static InputStream in = null;
//编译类型为InputStream
//运行类型为BufferedInputStream
//表示的是标准输入 键盘
System.out.println(System.in.getClass());

//System类的 public final static PrintStream out = null;
//编译类型为PrintStream
//运行类型为PrintStream
//表示的是标准输出  显示器
System.out.println(System.out.getClass());
```



在控制台中输入文本	Scanner扫描类，把System.in传入，即从键盘中输入数据	
scanner.next();读取我们输入数据，不能换行

```
Scanner scanner = new Scanner(System.in);
System.out.println("输入内容");
String next = scanner.next();
System.out.println("next="+next);
```



## 转换流

InputStreamReader 和OutputStreamWriter为转换流

处理纯文本数据时，不管是BufferedReader还是FileReader 默认都为utf-8的编码格式，中文字符占3个字节，
但若文本是以gbk编码格式，中文字符占2个字节，则会出现乱码，这时就需要转换流。

只有字节流，即操作每个字节，才可以定义编码格式，但是处理文本信息时，用字符流效率更高，所以就需要将字节流设置编码，转换为字符流！！！！！

InputStreamReader:Reader的子类,可以将InputStream(字节流)包装成(转换)Reader(字符流)
OutputStreamWriter:Writer的子类,实现将OutputStream(字节流)包装成Writer(字符流)

转换流使用时可以指定转换的字节编码，可解决乱码问题

读取e:\\new1.txt的信息，编码格式为gbk	新建InputStreamReader类，把FileInputStream传入，再定义编码格式"gbk"，再把InputStreamReader传入BufferedReader类中，便于输出(这一步可有可无，只是便于输出)

```
String filePath = "e:\\new1.txt";
InputStreamReader inputStreamReader = new InputStreamReader(new FileInputStream(filePath), "gbk");
BufferedReader bufferedReader = new BufferedReader(inputStreamReader);
String s = bufferedReader.readLine();
System.out.println("s="+s);
bufferedReader.close();
```



## 打印流

printStream(字节流)和printWriter(字符流)	都为打印流，打印流只有输出流，没有输入流

### printStream类

```
//System.out为标准输出流，默认输出的位置为显示器
//System.out为PrintStream类对象
PrintStream printStream=System.out;
printStream.println("ljxxx");
//printIn方法底层调用的write方法，我们也可以直接调用
printStream.write("ljxxx".getBytes());
printStream.close();
//也可以修改标准输出流的输出位置，即setOut设置输出路径，传入PrintStream类对象，并指定输出位置
System.setOut(new PrintStream("e:\\f1.txt"));
System.out.println("ljxxx");
```



### printWriter类

传入System.out，因为其为PrintStream类对象，也属于节点流，可传入
不管是输出到文件中还是控制台，都需要关闭，否则数据之后在内存中，关闭了才会上传

```
//输出数据到显示器，控制台
	PrintWriter printWriter = new PrintWriter(System.out);
	printWriter.write("ljxxx");
	printWriter.close();
	
//输出数据到f2.txt文件
//	PrintWriter printWriter = new PrintWriter(new FileWriter("e:\\f2.txt"));
//	printWriter.write("ljxxx");
//	printWriter.close();
```



## *字节*数组输出流(小重！！)

ByteArrayOutputStream 是字节数组输出流。它继承于OutputStream。
ByteArrayOutputStream 中的数据被写入一个 byte 数组。缓冲区会随着数据的不断写入而自动增长。可使用 toByteArray() 和 toString() 获取数据。

特点：会在内存开辟缓冲区，不需要马上写入到特定目录，可以暂时保存在缓冲区中，且缓冲区会随着写入自动增长，在想要输出时，通过toByteArray()转换成字节数组byte[]，或toString()转换为String类型返回

```
//将输入流转换为byte[],读入到byte[]中
public byte[] streamToByteArray(InputStream is) throws IOException {
        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        byte[] b = new byte[1024];
        int readLen = 0;
        while ((readLen = is.read(b))!=-1){
            byteArrayOutputStream.write(b,0,readLen);
        }
        byte[] bytes = byteArrayOutputStream.toByteArray();
        byteArrayOutputStream.close();
        return bytes;
    }
```





## Properties类

用于读写配置文件的集合类	.properties
配置文件的格式:
	键=值
	键=值

键值对不需要有空格,值不需要用引号一起来。默认类型是String

配置文件mysql.properties

```
ip=102.168.252.2
user=ljxxx
pwd=1234
```



使用传统方法读取配置文件	显示IP值

```
BufferedReader bufferedReader = new BufferedReader(new FileReader("src\\mysql.properties"));
String line = "";
while ((line = bufferedReader.readLine()) != null){
    String[] split = line.split("=");
    if ("ip".equals(split[0])){
        System.out.println(split[0]+"值为"+split[1]);
    }
}
bufferedReader.close();
```



### Properties类常用方法

load:加载配置文件的键值对到Properties对象

list:将数据显示到指定设备	//输出流

getProperty(key):根据键获取值

setProperty(key,value):设置键值对到Properties对象

store:将Properties中的键值对存储到配置文件,在idea中，保存信息到配置文件,如果含有中文，会存储为uniccode码



通过Properties类 读取mysql.properties配置文件

```
public static void main(String[] args) throws IOException {
        Properties properties = new Properties();
        properties.load(new FileReader("src\\mysql.properties"));
        properties.list(System.out);
        String user = properties.getProperty("user");
        String pwd = properties.getProperty("pwd");
        System.out.println("user="+user);
        System.out.println("pwd="+pwd);
    }
```



通过Properties类 创建mysql2.properties配置文件

若调用store方法，传入的是store(new FileWriter("src\\mysql2.properties"),null);	即传入的是字符流，而不是字节流，则不会以uniccode码的形式保存，而是会直接保存中文
第二个参数为注释，会在生成的配置文件的最上面打印出来

```
Properties properties = new Properties();
properties.setProperty("charset","utf-8");
properties.setProperty("user","汤姆");
properties.setProperty("pwd","1234");
properties.store(new FileOutputStream("src\\mysql2.properties"),null);
System.out.println("保存配置文件成功");
```



使用Properties类完成对mysq12.properties的读取，并修改某个key-val



