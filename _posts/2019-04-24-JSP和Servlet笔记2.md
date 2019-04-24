---
title: JSP和Servlet笔记2
layout: post
categories: JavaWeb
tags: JSP和Servlet
---
## 1.JSP
```
1.JSP概述
	* JSP(Java Serve Pages) 是Java Web服务器端的动态资源。它与html页面的作用是相同的，显示数据和获取数据
	* JSP = html + Java脚本 + JSP动态标签
	* 作用
		* Servlet：
			> 缺点：不适合设置html响应体，需要大量的response.getWriter().print("<html>");
			> 有点：动态资源，可以编程
		* html：
			> 缺点：html是静态页面，不能包含动态信息
			> 优点：不用为输出html标签而发愁
		* jsp：
			> 优点：在原有html的基础上添加java脚本，构成jsp页面
	* jsp和Servle的分工
		* JSP：	（相当于一个服务员）
			> 作为请求发起页面，例如显示表单、超链接。
			> 作为请求结束页面，例如显示数据
		* Servlet：
			> 作为请求中处理数据的环节
	* JSP的组成	（相当于一个厨师）
		* JSP = html + Java脚本 + JSP动态标签
		* jsp中无需创建即可使用的对象一共有9个，被称之为9大内置对象。例如：request对象、out对象
		* 3中java脚本：
			** <%...%>：java代码片段（常用），用于定义0~N条Java语句！方法内能写什么，它就可以放什么！
			** <%=...%>：java表达式，用于输出（常用），用于输出一条表达式的结果。prnt里可以放什么，它就可以放什么
			** <%!...%>：声明，用来创建类的成员变量和成员方法（基本不用，但容易考到），类体力可以放什么，它就可以放什么
2.jsp原理（理解）
	* jsp其实是一种特殊的Servlet
	  > 当jsp页面第一次被访问时，服务器会把jsp编译成java文件（这个java其实是一个servlet类）
	  > 然后再把java编译成.class
	  > 然后常见该类对象
 	  > 最后调用它的service()方法
	  > 第二次请求同一jsp时，直接调用service()方法
	* 在tomcat的work目录下可以找到jsp对应的.java源代码
	* 查看jsp对于java文件
	  > java脚本
	  > html
* jsp注释
	<%-- ... -- %>：当服务器把jsp编译成java文件时已经忽略了注释部分！
```

```
2. JSP三大指令
	一个jsp页面中可以有0~N个指令的定义：
	(1) page --> 最复杂：<%@page language="java" info="xxx"...%>
		* pageEncoding和contentType：
			> pageEncoding：它指定当前jsp页面的编码，只要不说谎，就不会有乱码！在服务器要把jsp编译成.java时需要使用此！
			> contentType：它表示添加一个响应头:Content-Type！等同与response.setContentType("text/html;charset=utf-8")；
			> 如果两个属性只提供一个，name另一个的默认值为设置那一个。
			> 如果两个属性都没有设置，name默认为isc
		* import：导包！可以出现多次
		* errorPage和isErrorPage
			> errorPage：当前页面如果抛出异常，那么要转发到哪一个页面，由errorPaget来指定
			> isErrorPage：它指定当前页面是否为处理错误的页面！
				当该属性为true时这个页面会设置状态码为500！而且这个页面可以使用9大内置对象中的exception!
		* autoFlush和buffer
			> autoFlush：指定jsp的输出流缓冲区满时，是否自动刷新！默认为true，如果为false，那么在缓冲区满时抛出异常！
			> buffer：指定缓冲区大小，默认为8kb，通常不需要修改！
		* isELignored：是否忽略el表达式，默认值为false，不忽略，即支持！
		* 基本没用：
			> language：指定当前jsp编译后的语言类型，默认值为java。
			> info：信息
			> isThreadSafe：当前的jsp是否支持并发访问！
			> session：当前页面是否支持session，如果为false，那么当前页面就没有session这个内置对象！
			> extends：让jsp生成的servlet去继承该属性指定的类！
	(2) include --> 静态包含
		* 与 RequestDispatcher的include()方法的功能相似！
		* <%@include%>它是在jsp编译成java文件时完成的！它们共同生成一个java(就是一个servlet)文件，然后再生成一个class!
		* RequestDispatcher的include()是一个方法，包含和被包含的是两个servlet，即两个.class！它们只是把响应的内容在运行时合并了！
		* 作用：把页面分解了，使用包含的方式组合在一起，这样一个页面中不变的部分，就是一个独立的Jsp,而我们只需要处理变化的页面。
	(3) tglib --> 导入标签库
		* 两个属性：
			> prefix：指定标签库在本页面中的前缀！由我们自己来起名称
			> uri：指定标签库的位置！
			> <$@taglib prefix="pre" uri="/struts-tags"%>   前缀的用法：<pre:text>
```

```
3. 九大内置对象
	* out --> jsp的输出流，用来向客户端响应
	* page --> 当前jsp对象！ 它的引用类型是Object，即真身中有如下代码：Object page = this ;
	* config --> 它对应真身中的ServletConfig对象！
	* pageContext --> 一个顶9个！
	* request --> HttpServletEequest
	* response --> HttpServletResponse
	* exception --> Throwable
	* session --> HttpSession
	* application --> ServletContext

① request
	request.getParameter(string);
	//注意编码问题，可以将字符串转为字节数组，再创建字符串
	request.setAttribute(key,value); value是Object类型 
	request.getAttribute(key);
	//转发
	request.getRequestisDispatcher("login.jsp").forward(request,response);
② response
	PrintWriter writer = response.getWriter();
③ pageContext
	* 一个顶9个！
	* Servlet中有三大域，而JSP中有四大域，它就是最后一个域对象！
		> ServletContext：整个应用程序
		> session：整个会话（一个会话只有一个用户）
		> request：一个请求链(指转发、重定向)！
		> pageContext：一个jsp页面！这个域是在当前jsp页面和当前jsp页面中使用的标签之间共享数据！ 
			** 域对象
			** 代理其他域：pageContext.setAttribute("xxx","XXX",ageContext.SESSION_SCOPE);
			** 全域查找：pageConte
```
```
4. HttpSession概述
	* HttpSession是由JvaWeb提高的，用来会话跟踪的类。session是服务器端对象，保存在服务器端！！！
	* HttpSession是Servlet三大域对象之一（request、session、appliaction(ServletContext)），所以它也有setAttribute()、getAttribute()、removeAttribute()方法
	* HttpSession底层依赖Cookie，或是URL重写！
5. HttpSession的作用
	* 会话范围：会话范围是某个用户从首次访问服务器开始，到该用户关闭浏览器结束！
		< 会话：一个用户对服务器的多次连贯性请求！所谓连贯性请求，就是该用户多次请求中间没有关闭浏览器！
	* 服务器会为每个客户端创建一个session对象，session就好比客户在服务器端的账户，他们被服务器保存到一个Map中，这个Map被称之为session缓存！
		> Servlet中的到session对象：HttpSession session = request.getSession()；
		> Jsp中得到session对象：session是jsp内置对象之下，不用创建就可以直接使用！
	* session域相关方法
		> void setAttribute(String name,Object value)	
		> Object getAttribute(String name);
		> void removeAttribute(String name);
```


## 2.Servlet

```
1.什么Servlet
	Servlet（Server Applet）是Java Servlet的简称，称为小服务程序或服务连接器，用Java编写的服务器端程序，具有独立于平台和协议的特性，主要功能在于交互式地浏览和生成数据，生成动态Web内容。
	狭义的Servlet是指Java语言实现的一个接口，广义的Servlet是指任何实现了这个Servlet接口的类，一般情况下，人们将Servlet理解为后者。Servlet运行于支持Java的应用服务器中。从原理上讲，Servlet可以响应任何类型的请求，但绝大多数情况下Servlet只用来扩展基于HTTP协议的Web服务器

	* Serblet 是JavaWeb的三大组件之一（Servlet、Filter、Listener），属于动态资源，服务器小程序
	* 作用：处理请求，服务器会把接收到的请求交给Servlet处理
		接收请求数据  处理请求  完成响应
	* Servlet 功能需要我们自己编写
```

```
2.实现Servlet的方式（由我们编写）
	实现Servlet有三种方式：
	* 实现 javax.servlet.Servlet接口
	* 继承 javax.servlet.GenericServlet类
	* 继承 javax.servlet.http.HttpServlet类
	通常我们会去继承 HttpServlet类来完成我们的Servlet，但是从1 接口学习
	** Servlet中的方法大多数不由我们来调用,而是由Tomcat来调用。并且Servlet的对象也不由我们来创建，由Tomcat创建

	生命周期方法
	* void init(ServletConfig)：出生之后（1次）
	* void service(ServletRequest request,ServletResponse response)：每次处理请求时都会被调用
	* void destory()：临死之前（1次）
	特性：
	* 单例，一个类只有一个对象；当然可能存在多个Servlet类！
	* 线程不安全的，所以他的效率是高的

	Servlet类由我们来写，但对象由服务器来创建，并且由服务器来调用相应的方法
```
```
3.GenericServlet
	是Servlet接口的实现类，我们可以通过继承GenericServlet来编写自己的Servlet
	
	ServletConfig是什么？ 见图9_3

4.Servlet细节（线程安全）
	* 不要创建成员变量，使用局部变量
	* 可以创建无状态的成员
	* 可以创建有状态的成员，但状态必须为只读的 
一个类型的Servlet只有一个实例对象，有可能一个Servlet同时处理多个请求

* 在第一次访问时创建Servlet
* 在服务器启动时创建Servlet,必须在xml写语句,定义启动顺序
	<load-on-startup>0</load-on-startup>

<url-pattern>/AServlet</url-pattern> 路径   也可以写通配符  /路径匹配（优先级最高）   .扩展名匹配
	*只能放在两边，放前面 路径匹配  放后面 扩展名匹配
	/*匹配所有url（优先级最低）
	*.do  匹配扩展名
5.web.xml文件继承（了解）
在\conf\web.xml中的内容，相当于写到了每个项目的web.xml中，它是所有web.xml的父文件
```
```
6.ServletContext（重要）
	一个项目只有一个ServletContext对象
	我们可以咋N多个Servlet中来获取这个唯一的对象，使用它可以给多个Servlet传递数据
	* ServletContext对象的创建是在服务器启动时完成的
	* ServletContext对象的销毁是在服务器关闭时完成的
	* ServletContext对象的作用是在整个Web应用的动态资源之间共享数据！
	
	* ServletContext的获取
		在GenericeServlet或HttpServlet中获取ServletContext对象   (ServletConfig、ServletContextEvent也有这个方法
			getServletContext()方法
		this.getServletConfig().getServletContext();
```

```
7.域对象的功能
	域对象就是用来在多个Servlet中传递数据，域对象内部有一个Map用来存储数据
		* 域对象必须有要存数据功能
		* 域对象必须要有取数据功能
	ServletContext是JavaWeb四大域对象之一
	（PageContext、ServletRequest、ServletContext）
	* ServeletContext对象用来操作数据的方法

		* void setAttribute(String name,Object value)：用来存储一个对象（存出一个域属性） 多次调用该方法，并且使用相同的nmae 那么么会覆盖上一次的值
		* Object getAttribute(String name)：用来获取ServletContext中的数据
		* void removeAttribute(String name)：用来移除ServletContext中的域属性
		* Enumeration getAttributeNames()：获取所有域属性的名称
	* Sevlet也可以获取初始化参数，但他是局部的参数；
		一个Servlet只能获取自己的初始化参数，不能获取别人的，即初始化参数只为一个Serlvet准备
	* 可以配置公共的初始化参数，为所有Servlet而用！这需要使用ServletContext才能使用
```

```
8.获取资源的方法（**************************）
	* 获取真实路径
		String path = this.getServletContext().getRealPath("/index.jsp");
	* 获取资源流
		 * 获取资源的路径后，再创建输出流对象！
		InputStream input = this.getServletContext().getResourceAsStream("index.jsp");
	* 获取所有资源路径
		 * 获取当前路径下所有资源路径
		Set<String> paths = this.getServletContext().getResourcePaths("/WEB-INF");
```
```
9.练习：访问量统计
	一个项目中所有的资源被访问都要对访问量进行累加
	创建一个int类型的变量，用来保存访问量，然后把它保存到ServletContext的域中，这样可以保存所有的Servlet都可以访问到
	* 最初时，ServletContext中没有保存访问量相关的属性；
	* 当本站被第一次访问时，创建一个变量，设置其值为1；保存到ServletContext中；
	* 当以后的访问时，就可以从ServletContext中获取这个变量，然后在其基础上加1。

	* 获取ServletContext对象，查看是否存在名为count的属性，如果存在，说明不是第一次访问，如果不存在，说明是第一次访问
		* 第一次访问：调用ServletContext的setAttribute()传递一个属性，名为count，值为1；
		* 后续访问：调用ServletContext的getAttribute()方法获取原来的访问量，给访问量+1，再调用
			ServletContetx()的setAttribute()方法进行设置
	* 代码
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		ServletContext app = this.getServletContext();
		Integer count =(Integer)app.getAttribute("count");
		if(count==null){
			app.setAttribute("count", 1);
		}else{
			app.setAttribute("count", count+1);
		}
		/*
		 * 向浏览器输出
		 *  需要使用响应对象
		 */
		PrintWriter pw = response.getWriter();
		pw.print("<h1>"+count+"</h1>");
	}
```