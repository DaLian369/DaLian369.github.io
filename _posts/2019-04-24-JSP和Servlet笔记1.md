---
title: JSP和Servlet笔记1
layout: post
categories: JavaWeb
tags: JSP和Servlet
---
## 1.xml是什么

&emsp;&emsp;xml是一种可扩展标记语言，它的设计宗旨是传输数据，而不是显示数据。它的标签没有预定义，需要自行定义标签。

## 2.一个xml文件代码

```
<?xml version="1.0" encoding="UTF-8"?>
<goodslist>
<!-- 	下面是商品信息 -->
	<good id="1001" birthday="2018-4-1">
		<price>12</price>
		<name>香蕉</name>
		<place>广州</place>
	</good>
	<good id="1002" birthday="2018-4-2">
		<price>13</price>
		<name>苹果</name>
		<place>陕西</place>
	</good>
	<Good>xml里区分大小写,属性自己定义,每一个xml必须有一个根标签</Good>
	
<!-- 约束：约束文档，DTD，Schema -->
</goodslist>
```
## 3.注意事项

```java
$lt;    <  小于
&gt;    >  大于
%anp;   &  和号
&apos;  '  单引号
&quot;  "  引号
```

ctrl + shift +c 添加注释  <!-- 	下面是商品信息 -->

## 4.dtd约束


dtd约束：内部引入
```
<?xml version="1.0"?>
<!DOCTYPE note [
  <!ELEMENT note (to,from,heading,body)> note下有且只能有这四个标签
  <!ELEMENT to      (#PCDATA)>
  <!ELEMENT from    (#PCDATA)>
  <!ELEMENT heading (#PCDATA)>
  <!ELEMENT body    (#PCDATA)>
]>
<note>
  <to>George</to>
  <from>John</from>
  <heading>Reminder</heading>
  <body>Don't forget the meeting!</body>
</note>
```
dtd约束：外部引入本地约束文件，还可以引入网络上的约束
```
<!ELEMENT note (to,from,heading,body)>
<!ELEMENT to (#PCDATA)>
<!ELEMENT from (#PCDATA)>
<!ELEMENT heading (#PCDATA)>
<!ELEMENT body (#PCDATA)>

xml里引入dtd
<?xml version="1.0"?>
<!DOCTYPE note SYSTEM "note.dtd">
```
## 5.schema约束

&emsp;&emsp;schema约束文档本身也是一个xml文档，后缀为xsd,语法更加容易阅读，功能更加强大。

&emsp;&emsp;我们也不需要写schema的约束文档，我们只需要直接使用框架提供给我们的约束文档，里面有命名空间。

```
<?xml version="1.0"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"  //给xmlns起别名xs
targetNamespace="http://www.w3school.com.cn"
xmlns="http://www.w3school.com.cn"	//命名空间
elementFormDefault="qualified">

<xs:element name="note">	//xs:表示使用xs里的标签
    <xs:complexType>
      <xs:sequence>
	<xs:element name="to" type="xs:string"/>
	<xs:element name="from" type="xs:string"/>
	<xs:element name="heading" type="xs:string"/>
	<xs:element name="body" type="xs:string"/>
      </xs:sequence>
    </xs:complexType>
</xs:element>

</xs:schema>
```

## 6.xml里面的dom

&emsp;&emsp;用来解析标签，解析成一个树，并得到一个document对象。

&emsp;&emsp;我们可以使用dom4j来进行dom方式的解析，dom4j是一个开源xml解析软件包。

&emsp;&emsp;解析的时候需要做哪些事情
&emsp;&emsp;&emsp;&emsp;(1) 解析根元素
&emsp;&emsp;&emsp;&emsp;(2) 解析有哪些子元素
&emsp;&emsp;&emsp;&emsp;(3) 解析一个元素有哪些属性
&emsp;&emsp;&emsp;&emsp;(4) 得到元素的文本内容
&emsp;&emsp;&emsp;&emsp;(5) 修改、添加、删除某个元素节点
&emsp;&emsp;&emsp;&emsp;(6) 修改、添加、删除某个属性

## 7.dom4j解析xml

```java
import java.util.Iterator;

import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

public class ParseXML {
	public static void main(String[] args) throws Exception{
		SAXReader reader  = new SAXReader();
		Document document = reader.read("NewFile.xml");
		Element root = document.getRootElement();
//		System.out.println(root.getName()+":"+root.getData());
		
		Iterator<Element> it = root.elementIterator();
		while(it.hasNext()) {
			Element ele = it.next();
			if(ele.getName().equals(""good")){
				Element name = ele.element("name");
				if(name!=null) System.out.println(name.getText());
			}

			System.out.println(ele.getName());
			Iterator<Attribute> attributes = ele.attributeIterator();
			while(attributes.hasNext()) {
				Attribute ab = attributes.next();
				System.out.println(ab.getName()+":"+ab.getValue());
			}
		}
		//xml：Element Attribute
		Element ele = null;
		ele.elementIterator("good")
	}
}
//Element.getText()获得标签内容
```

## 7.JavaWeb和JavaEE的区别

&emsp;&emsp;JavaWeb是以Java语言为基础，使用JSP和Servlet来开发Web程序

&emsp;&emsp;JavaEE是Java的企业级应用，里面包含的功能比较多。JavaEE是一个大杂烩，包括Applet、EJB、JDBC、JNDI、Servlet、JSP等技术的标准，运行再一个完整的应用服务器上，用来开发大规模、分布式、健壮的网络应用。

&emsp;&emsp;可以粗略的认为JavaWeb就是JavaEE的一部分。

&emsp;&emsp;后续学习 SSM、SSH框架

## 8.JSP

```java
//jsp中引入java类
<%@page import="java.util.Date" %>

//注释
<%-- --%>

//jsp定义表达式
<%! %>

<%
	//接受请求这种的数据
	String usernmae = request.getParameter("username");
%>
//
```

## 9.http协议

&emsp;&emsp;http协议规定了我们在发起http请求的时候，这个请求的数据包里面都包含了什么样的数据以及数据按照什么样的先后顺序存放在数据包里。

```
1.http协议
	协议：协议的甲乙双方，就是客户端（浏览器）和服务器！
		理解成双方通信的格式！
		* 请求协议
		* 响应协议
2.体系结构
	C/S 客户端/服务器结构模型
	B/S 浏览器/服务器结构模型
3.Web资源
	html静态资源
	JSP/Servlet 动态资源
4.Tomcat服务器
	默认端口号8080   若要修改 在 server.xml
	* bin 可运行文件
	* conf 配置文件
	* lib jar包
	* webapps 存放web项目的目录
	* work 运行时生成的文件
响应头 Referer：显示请求来自哪个页面，统计请求量  和 作防盗链
Content-Type：表单的数据类型   提交给服务器的格式化编码数据

状态码：
200 OK 请求成功;
202 Accepted 服务器已经接受请求，但尚未处理;
205 Reset Content 服务器成功处理了请求，且没有返回任何内容；
302 Move temporarily 重定向

4开头 请求错误
404 NotFound 没有找到该资源

5开头 服务器端错误
500 Internal Server Error 无法处理请求

用GET提交数据时，数据会加在路径后面，
而POST请求则不会，Content-Length表示请求体的长度，POST请求有该头。
```