# 一.XML

## 1.名称规则

名称可以含字母、数字以及其他的字符名称

不能以数字或者标点符号开始

名称不能包含空格

&lt；转义为<

&gt；转义为>

文本区域(CDATA 区)

CDATA语法可以告诉xml解析器，我CDATA里的文本内容，只是纯文本，不需要xml语法解析

CDATA格式:

<![CDATA[这里可以把你输入的字符原样显示，不会解析xml ]]>





## 2.使用dom4j解析

```
public void test2() throws Exception {
    SAXReader reader=new SAXReader();
    Document document=reader.read("src/books.xml");
    Element element=document.getRootElement();
    List<Element> books=element.elements("book");
    for (Element book:books){
        String nameText=book.elementText("name");
        String priceText=book.elementText("price");
        String authorText=book.elementText("author");
        //获取属性的方法
        String snValue=book.attributeValue("sn");
        System.out.println(new Book(snValue,nameText, new BigDecimal(priceText),authorText));
```



asXML()     把标签对象。转换为标签字符串

1/直接获取指定标签名的文本内容
String priceText = book.elementText("price" ) ;

strjing authorText = book.elementText( "author" );

String snValue = book.attributevalue( "sn" );

System.out.println(new Book(snValue,nameText, new BigDecimal(priceText),authorText)); 打印信息