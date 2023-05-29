





















# 坦克大战

## 案例：绘制一个圆

定义MyPanel类，继承JPanel类，用于绘画图形
在主类中继承JFrame框架，相当于一个窗口，重写paint方法，用于绘画，drawOval方法为绘制椭圆，前两个参数为x,y值，即左上角点在窗口中的位置，后两个参数为圆的宽高
再在构造方法中把画板面板初始化放入窗口中(add方法)	而第一次屏幕显示时，会自动调用paint方法

```
public class DrawCircle extends JFrame{ //JFrame为一个框架，对应了一个窗口，可以理解成画框
    //定义一个面板
    private MyPanel mp =null;
    public static void main(String[] args) {
        new DrawCircle();
    }
    public DrawCircle(){    //构造方法
        //初始化面板
        mp=new MyPanel();
        //把面板放入窗口中
        this.add(mp);
        this.setSize(400,300);  //设置窗口大小
        //设置当点击窗口的小x，程序会完全退出
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        this.setVisible(true);  //可以显示
    }
}

//定义MyPanel类，继承JPanel类，用于画图形，相当于画板
class MyPanel extends JPanel{
    @Override
    //Graphics g相当于创建一根画笔
    public void paint(Graphics g) {     //绘画方法
        super.paint(g);     //调用父类的有参构造器完成初始化，不能去
        g.drawOval(10,10,100,100);

    }
}
```

![image-20230505125854917](C:\Users\ljxxx\AppData\Roaming\Typora\typora-user-images\image-20230505125854917.png)



## 绘图原理

Component类提供了两个和绘图相关最重要的方法:
1.paint(Graphics g)绘制组件的外观
2.repaint()刷新组件的外观。

当组件第一次在屏幕显示的时候,程序会自动的调用paint()方法来绘制组件

在以下情况paint()将会被调用:
1.窗口最小化再最大化
2.窗口的大小发生变化
3.repaint函数被调用



## Graphics类(画笔)

1.画直线 drawLine(int x1,int y1,int x2,int y2)

2.画矩形边框drawRect(int x, int y, int width, int height)

3.画椭圆边框drawOval(int x, int y, int width, int height)

4.填充矩形 fillRect(int x, int y, int width, int height)
先	g.setColor设置填充的颜色，默认为黑色

```
g.setColor(Color.blue);
g.fillRect(10,10,100,100);
```

5.填充椭圆fillOval(int x, int y, int width, int height)

6.画图片drawImage(Image img, int x, int y, ..)

7.画字符串drawString(String str, int x, int y)

8.设置画笔的字体 setFont(Font font)

9.设置画笔的颜色 setColor(Color c)









