---
title: SpringMVC
layout: post
tags: SpringMVC
categories: JavaWeb
---
## 六、SpringMVC

**1.WEB.xml**

```xml
<!-- 1.注册监听器，监听服务器的启动，在启动时，自动加载spring相关的xml文件 -->
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<context-param>
	<param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value><!--配置service及以下 -->
</context-param>
<!-- 2.配置SpringMVC入口servlet，所有*.form请求，都会进入spring中 -->
<!-- 并在服务器启动时，默认加载位于web.inf文件夹中的viewSpace.xml，以配置springMVC -->
<!-- 配置service以上 -->
<servlet>
	<servlet-name>viewSpace</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup> <!-- 设置此选项后，servlet会在服务器一启动就创建-->
</servlet>
<servlet-mapping>
	<servlet-name>viewSpace</servlet-name>
    <url-pattern>*.form</url-pattern>
  	<url-pattern>*.css</url-pattern>
  	<url-pattern>*.jpg</url-pattern>
  	<url-pattern>*.js</url-pattern>
  	<url-pattern>*.png</url-pattern>
</servlet-mapping>
<!-- 3.配置字符集过滤器，解决中文乱码问题 -->
<filter>
	<filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

**2.viewSpace-servlet.xml**

```xml
<!-- controller及以上，在此处配置 -->
<!-- 开启扫描注解，指定要扫描的包，多个包的话，名字之间可以用逗号隔开,防止多次扫描，因为application也里也有扫描，该配置文件只扫描controller及以上-->
<context:component-scan base-package="cn.dlian">
    <context:exclude-filter type="annotation" expression="org.springframwork.stereotype.Service" />
    <context:exclude-filter type="annotation" expression="org.springframwork.stereotype.Repository" />	
</context:component-scan>
<!-- 开启此选项，才能在重定向时使用RedirectAttributes -->
<mvc:annotation-driven />

<!-- 开启validator校验器进行字段校验 -->
<bean id="validator" class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean" />

<!-- 给Controller注入Service -->
<bean id="userInfoController" class="cn.dlian.controllers.UserInfoController"
      p:userInfoService-ref="userInfoService"
/>

<!-- 屏蔽Repository和Service扫描 -->
<context:component-scan base-package="cn.dlian">
		<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Service" />
    	<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Repository" />	
</context:component-scan>

<!--配置拦截器 -->
<mvc:interceptors>
	<mvc:interceptor>
    	<mvc:mapping path="../**/*.form" /> <!--拦截器映射-->
        <mvc:exclude-mapping path="/**/login.form" /> <!--不包含的请求映射(不拦截) -->
        <mvc:exclude-mapping path="/**/index.form" />
        <bean class="cn.dlian.interceptors.LoginInterceptor" /> <!--处理拦截事务的拦截器类-->
    </mvc:interceptor>
</mvc:interceptors>

<!-- 外部访问资源文件，需要对路径进行映射 -->
<mvc:resources location="WEB-INF/css/" mapping="/css/**"/>
<mvc:resources location="WEB-INF/js/" mapping="/js/**"/>
<mvc:resources location="WEB-INF/img/" mapping="/img/**"/>
<mvc:resources location="WEB-INF/fonts/" mapping="/fonts/**"/>
<mvc:resources location="WEB-INF/plugins/" mapping="/plugins/**"/>
<mvc:resources location="WEB-INF/ajax/" mapping="/ajax/**"/>
```

> 

**3.Controller**

```java
//一、传递普通参数
@Controller("parController")//给该注解别名，其他地方调用的对象名就为parController
@RequestMapping("/")
public class ParamController{
    @RequestMapping("otherPage")
    public String jumpOtherPage(){
		System.out.println("第一个请求!");
        return "otherPage.jsp";
        //return "redirect:otherPage.jsp";
    }
    @RequestMapping("sendParam")
    public String sendParam(@RequestParam(value="username",required=false) String userName,@Reqeust("password") String userPass){
        System.out.println("账号"+userName);
        System.out.println("密码"+userPass);
        return "showParam.jsp";
    }
    @RequestMapping("sendParamWithRequest")
    public String sendParamWithRequest(@RequestParam("username") String userName,@Reqeust("password") String userPass,HttpServletRequest request){
        System.out.println("账号"+userName);
        System.out.println("密码"+userPass);
        
        request.setAttribute("userName",userName);
        return "showParam.jsp";
    }
    @RequestMapping("sendSameNameWithRequest")
    public String sendSameNameWithRequest(@RequestParam String username,@Reqeust String password,HttpServletRequest request){
        System.out.println("账号"+username);
        System.out.println("密码"+password);
        
        request.setAttribute("userName",username);
        return "showParam.jsp";
    }
    @RequestMapping("sendSameNameWithRequestRedirect")
    public String sendSameNameWithRequest(@RequestParam String username,@Reqeust String password) throws UnsupportedEncodingException{
        return "redirect:showParam.jsp?username="+URLEncoder.encode(username,"utf-8")+"&password="+password;
    }
    
    @RequestMapping("sendSameNameWithRequestRedirectSpring")
    public String sendSameNameWithRequestRedirectSpring(@RequestParam String username,@Reqeust String password,Redirect attrs) throws UnsupportedEncodingException{
        attrs.addAttribute("username",username);
        attrs.addAttribute("password",passwrod);
        return "redirect:showParam.jsp"	;
    }
}
```

```java
//路径参数
@Controller
@RequestMapping("/")
public class PathVariableController{
    //getPage3By100Items
    @Request String multiPageQuery(@PathVariable int pageNo,@PageVariable int countPage){
        System.out.println(pageNo+" "+countPage);
        return "otherPage.jsp";
    }
}
```

```java
//三、传递表单数据到实体对象中(模型属性)
@Controller
@RequestMapping("/")
public class UserController{
    @ReqeustMapping(value="login",method={RequestMethod.POST})
    public String login(@ModelAttribute("user") User pUser){
        //模型属性会自动放入reqeust中
    }
}
```

```java
//四、控制器跳转至另一个控制器
//转发
return "sendSameNameParamWithRequest.form";
//重定向利用RedirectAttributes attrs
```

**4.字段校验**

> hibernate-validator-x.jar
>
> jboss-logging-x.jar
>
> validation-api-x.jar

```java
public class User{
    @Pattern(regexp="^\\S{3,16}$",message="账号名必须是3-16位的非空白字符!")
    private String username;
    @NotNull(message="此字段不能为null!")
    @NotEmpty(message="此字段不能为空!")
    @Length(min=3,max=16,message="密码必须是3-16位!")
    priavte String password;
}

@Controller
@ReqeustMapping("/")
public class UserControlelr{
    @RequestMapping("login")
    public String login(@Valid @ModelAttribute User user,BindingResult bindingResult){
        //BindingResult存放出错信息
        //首先判断字段校验是否通过，若未通过，则不需要进行登陆验证!
        if(bindingResult.hasErrors()){
            return "index.jsp";
		}
        //登陆验证
        if("zhangsan".equals(user.getUserName())&&"123456".equals(user.getUserpass())){
            return "loginSuccess.jsp";
        }
        return "loginFailed.jsp";
    }
}
```

**5.ModelAndView**

```java
@Controller
@RequestMapping("/")
public class UserController{
    @RequestMapping("login")
    public ModelAndView login(@ModelAttribute User user){
        ModelAndView mav = new ModelAndView("index.jsp");
        mav.addObject("otherInfo","呵呵");
        return mav;
    }
}
```

**6.注解**

> @Repository  Dao层
>
> @Service  Service层
>
> @Controller	
>
> @Autowired   变量
>
> @Qualifier("userInfoDao") 跟在auto下面，只有匹配名字的bean才能注入进来
>
> @ResponseBody 添加此注解，表示返回的字符串不是页面地址，而是响应体正文

**7.application.xml**

```xml
去掉bean
<!-- 在application.xml里屏蔽controller扫描
	 在viewSpace-servlet.xml里屏蔽Repository和Service扫描
<!-- application和viewSpace都需要打开扫描注解 -->
<context:component-scan base-package="cn.dlian">
    <context:exclude-filter type="annotation" expression="org.springframwork.stereotype.Controller" />
</context:component-scan>
```

**8.自动生成实体类使用**

```xml
<!-- 下面添加一个bean的设置，使其能够扫描myBatis的Dao层接口，自动生成实体类使用 -->
<!-- applicationContext.xml -->
<bean class="org.mybatis.spring.mapper.MapperScannerConfiger"
      p:basePackage="cn.dlian.dao"
/>
```

**9.HandlerInterceptor接口**

```java
public class LoginInterceptor implements HandlerInterceptor{
    //请求执行之后，再执行的方法，一般在此处清理释放资源
    public void afterCompletion(HttpServletRequest req,HttpServletResponse resp,Object obj,Exception exc) throws Exception{}
    
    //正在执行请求，准备跳转页面之前执行的方法，一般在此处我们可以处理一部分请求或添加一部分响应头等
    public void postHandle(HttpServletRequest req,HttpServletResponse resp,Object obj,ModelAndView mav) throws Exception{}
    
    //正在执行请求，准备跳转页面之前执行的方法，一般在此处我们可以处理一部分请求或添加一部分响应头等
    public boolean preHandle(HttpServletRequest req,HttpServletResponse resp,Object obj) throws Exception{
        User user = req.getSession().getAttribute("loginUser");
        if(user==null){
            req.sendRedirect("index.jsp");
            return false;	//阻止继续向下传递请求
        }
        return true; //放行
    }
    
}
```

**HTTP请求处理流程-SpringMvc**

> 在SpringMVC的http请求处理过程中，包括了前端控制器（DispatcherServlet）、处理映射器(HandlerMapping)、处理适配器(HandlerAdapter)、处理器((Handler)Controller)、视图解析器(ViewReslover)、视图(View)这六大主要对象。他们负责对http请求做处理。

第一步：前端控制器dispatcher接受请求

> Client---url--->Dispatcher

第二步：前端控制器去发起handler映射查找请求

> Dispatcher---HttpServletRequest---> HandlerMapping

第三步：处理器映射器查找hanlder并返回HandlerExetuionChain

> Dispatcher <---HandlerExeutionChain---HandlerMapping

第四步：前端控制器发起请求处理器适配器请求执行

> Dispatcher---Handler---> HandlerAdapter

第五步：处理器适配器去调用handler执行

> HandlerAdapter---HttpServletRequest> Handler(Controller)

第六步：处理器处理后返回ModelAndView给HandlerAdapter

> HandlerAdapter <---ModelAndView---Handler(Controller)

第七步：处理器适配器将ModelAndView返回给前端控制器

> Dispatcher <---ModelAndView---HandlerAdapter

第八步：前端控制器请求视图解析器解析ModelAndView

> Dispatcher---ModelAndView---> ViewReslover

第九步：视图解析器解析视图后返回视图View给前端控制器

> Dispatcher <---View---ViewReslover

第十步：前端控制器请求视图要求渲染视图

> Dispatcher--->View--->render

第十一步：前端控制器返回响应

> Response <---Dispatcher