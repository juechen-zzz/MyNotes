[TOC]

# SpringMVC

> B站狂神视频

* **MVC**：模型（**model**：dao和service层）、视图（**view**：jsp）、控制器（**controller**：servlet）的简写，是一种软件设计规范
* <img src="../images/image-20201125093837068.png" alt="image-20201125093837068" style="zoom:50%;" />
	* Model：数据模型，提供要展示的数据，因此包含数据和行为，可以认为是领域模型或JavaBean组件（包含数据和行为），不过现在一般都分离开来：Value Object(数据Dao)和服务层(行为Service)。也就是模型提供了数据查询和模型数据的状态更新等功能，包括数据和业务。
	* View：负责进行模型的展示，一般就是我们见到的用户界面，客户想看到的东西
	* Controller：接收用户请求，委托给模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示，也就是说控制器做了调度员的工作
* 将业务逻辑、数据、显示分离的方法来组织代码
* MVC主要作用是**降低了视图与业务逻辑间的双向耦合**
* MVC不是一种设计模式，是一种**架构模式**，不同的MVC存在差异

<img src="../images/image-20201125093031673.png" alt="image-20201125093031673" style="zoom:50%;" />

## 1 servlet环境搭建

* 父工程pom.xml

	```xml
	<dependencies>
	    <!--junit-->
	    <dependency>
	        <groupId>junit</groupId>
	        <artifactId>junit</artifactId>
	        <version>4.12</version>
	    </dependency>
	    <!--spring-webmvc -->
	    <dependency>
	        <groupId>org.springframework</groupId>
	        <artifactId>spring-webmvc</artifactId>
	        <version>5.3.1</version>
	    </dependency>
	    <!--Servlet+JSP+JSTL-->
	    <dependency>
	        <groupId>javax.servlet</groupId>
	        <artifactId>servlet-api</artifactId>
	        <version>2.5</version>
	    </dependency>
	    <dependency>
	        <groupId>javax.servlet.jsp</groupId>
	        <artifactId>jsp-api</artifactId>
	        <version>2.2</version>
	    </dependency>
	    <dependency>
	        <groupId>javax.servlet</groupId>
	        <artifactId>jstl</artifactId>
	        <version>1.2</version>
	    </dependency>
	</dependencies>
	```

* 添加框架支持
	![image-20201125103347567](../images/image-20201125103347567.png)

* 1 新建一个类，实现Servlet接口

	```java
	public class HelloServlet extends HttpServlet {
	    @Override
	    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
	        // 1 获取前端参数
	        String method = req.getParameter("method");
	        if (method.equals("add")){
	            req.getSession().setAttribute("msg", "执行了add方法");
	        }
	        if (method.equals("delete")){
	            req.getSession().setAttribute("msg", "执行了delete方法");
	        }
	
	        // 2 调用业务层
	
	        // 3 视图转发或重定向
	        req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req, resp);
	    }
	
	    @Override
	    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
	        doGet(req, resp);
	    }
	}
	```

* 2 在WEB-INF下新建jsp文件夹，新建test.jsp

	```xml
	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	<head>
	    <title>Title</title>
	</head>
	<body>
	${msg}
	</body>
	</html>
	```

* 3 新建form.jsp表单

	```xml
	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	<head>
	    <title>Title</title>
	</head>
	<body>
	
	<form action="/hello" method="post">
	    <input type="test" name="method">
	    <input type="submit">
	</form>
	
	</body>
	</html>
	```

* 4 Web.xml注册servlet

	```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
	         version="4.0">
	    
	    <servlet>
	        <servlet-name>hello</servlet-name>
	        <servlet-class>com.komorebi.servlet.HelloServlet</servlet-class>
	    </servlet>
	    <servlet-mapping>
	        <servlet-name>hello</servlet-name>
	        <url-pattern>/hello</url-pattern>
	    </servlet-mapping>
	
	<!--    <session-config>-->
	<!--        <session-timeout>15</session-timeout>-->
	<!--    </session-config>-->
	
	<!--    <welcome-file-list>-->
	<!--        <welcome-file>index.jsp</welcome-file>-->
	<!--    </welcome-file-list>-->
	</web-app>
	```

* 5 配置Tomcat，测试

![image-20201129091711490](../images/image-20201129091711490.png)



## 2 SpringMVC特性

* 轻量级，简单易学
* 高效，基于请求响应的MVC框架
* 与Spring兼容性好
* 约定大于配置
* 功能强大：RESTful、数据验证、格式化、本地化、主题
* **以请求为驱动，围绕一个中心Servlet分派请求及提供其他功能，DispatchServlet是一个实际的Servlet**

<img src="../images/image-20201125135139804.png" alt="image-20201125135139804" style="zoom:50%;" />

* **步骤**

	* 1 web.xml

		```xml
		<?xml version="1.0" encoding="UTF-8"?>
		<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
		         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
		         version="4.0">
		    <!--1 注册DispatcherServlet-->
		    <servlet>
		        <servlet-name>springmvc</servlet-name>
		        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		        <!--关联一个springmvc的配置文件：[servlet-name]-servlet.xml-->
		        <init-param>
		            <param-name>contextConfigLocation</param-name>
		            <param-value>classpath:springmvc-servlet.xml</param-value>
		        </init-param>
		        <!--启动级别-1-->
		        <load-on-startup>1</load-on-startup>
		    </servlet>
		
		    <!-- / 匹配所有的请求：(不包括.jsp) -->
		    <!-- /* 匹配所有的请求：(包括.jsp) -->
		    <servlet-mapping>
		        <servlet-name>springmvc</servlet-name>
		        <url-pattern>/</url-pattern>
		    </servlet-mapping>
		</web-app>
		```

	* 2 springmvc-servlet.xml

		```xml
		<?xml version="1.0" encoding="UTF-8"?>
		<beans xmlns="http://www.springframework.org/schema/beans"
		       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		       xsi:schemaLocation="http://www.springframework.org/schema/beans
		       http://www.springframework.org/schema/beans/spring-beans.xsd">
		
		    <!--处理器映射器-->
		    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
		    <!--处理器适配器-->
		    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
		
		    <!--添加视图解析器-->
		    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
		        <!--前缀-->
		        <property name="prefix" value="/WEB-INF/jsp/"/>
		        <!--后缀-->
		        <property name="suffix" value=".jsp"/>
		    </bean>
		
		    <!--Handler-->
		    <bean id="/hello" class="com.komorebi.controller.HelloController"/>
		</beans>
		```

	* 3 helloController.java

		```java
		public class HelloController implements Controller {
		    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
		        // ModelAndView  模型和视图
		        ModelAndView mv = new ModelAndView();
		
		        // 封装对象，放在ModelAndView中
		        mv.addObject("msg", "HelloSpringMVC!");
		
		        // 封装要跳转的视图，放在ModelAndView中
		        mv.setViewName("hello");    // ：/WEB-INF/jsp/hello.jsp
		        return mv;
		    }
		}
		```

	* 4 配置Tomcat，测试

	* <img src="../images/image-20201125145601081.png" alt="image-20201125145601081" style="zoom:50%;" />

### 2.1 执行原理

<img src="../images/image-20201125160106066.png" alt="image-20201125160106066" style="zoom:50%;" />

1. DispatcherServlet表示前置控制器，是整个SpringMVC的控制中心。用户发出请求，DispatcherServlet接收请求并拦截请求<img src="../images/image-20201125160354276.png" alt="image-20201125160354276" style="zoom:50%;" />
2. HanderMapping为处理器映射。DispatcherServlet调用HanderMapping，HanderMapping根据请求url查找Handler
3. HandlerExecution表示具体Handler。其主要作用是根据url查找控制器，如上url被查找控制器为：hello
4. HandlerExecution将解析后的信息传递给DispatcherServlet，如解析控制器映射等
5. HandlerAdapter表示处理器适配器，其按照特定的规则去执行Handler
6. Handler让具体的Controller执行
7. Controller将具体的执行信息返回给HandlerAdapter，如ModelAndView
8. HandlerAdapter将视图逻辑名或模型传递给DispatcherServlet
9. DispatcherServlet调用视图解析器（ViewResolver）来解析HandlerAdapter传递的逻辑视图名
10. 视图解析器将解析的逻辑视图名传给DispatcherServlet
11. DispatcherServlet根据视图解析器解析出的视图结果，调用具体的视图
12. 最终视图呈现给用户



## 3 注解开发SpringMVC

步骤：

1. 配置web.xml（同上）

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
  
      <!--1 注册DispatcherServlet-->
      <servlet>
          <servlet-name>springmvc</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
          <!--关联一个springmvc的配置文件：[servlet-name]-servlet.xml-->
          <init-param>
              <param-name>contextConfigLocation</param-name>
              <param-value>classpath:springmvc-servlet.xml</param-value>
          </init-param>
          <!--启动级别-1-->
          <load-on-startup>1</load-on-startup>
      </servlet>
  
      <!-- / 匹配所有的请求：(不包括.jsp) -->
      <!-- /* 匹配所有的请求：(包括.jsp) -->
      <servlet-mapping>
          <servlet-name>springmvc</servlet-name>
          <url-pattern>/</url-pattern>
      </servlet-mapping>
      
      <!--2 SpringMVC的乱码配置-->
      <filter>
          <filter-name>encoding</filter-name>
          <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
          <init-param>
              <param-name>encoding</param-name>
              <param-value>utf-8</param-value>
          </init-param>
      </filter>
      <filter-mapping>
          <filter-name>encoding</filter-name>
          <url-pattern>/*</url-pattern>
      </filter-mapping>
  </web-app>
  ```

2. 配置springmvc-servlet.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:mvc="http://www.springframework.org/schema/mvc"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context
         https://www.springframework.org/schema/context/spring-context.xsd
         http://www.springframework.org/schema/mvc
         https://www.springframework.org/schema/mvc/spring-mvc.xsd">
  
      <!--自动扫描包，让指定包下的注解生效，由IOC容器统一管理-->
      <context:component-scan base-package="com.komorebi.controller"/>
  
      <!--让Spring MVC不处理静态资源-->
      <mvc:default-servlet-handler/>
  
      <!--
      支持MVC注解驱动
          在Spring中一般采用@RequestMapping注解完成映射关系
          要想使@RequestMapping注解生效
          必须向上下文中注册DefaultAnnotationHandlerMapping和一个AnnotationMethodHandlerAdapter实例
          这两个实例分别在类级别和方法级别处理
          而annotation-driven配置帮助我们自动完成上述两个实例的注入
      -->
      <mvc:annotation-driven>
          <mvc:message-converters register-defaults="true">
              <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                  <constructor-arg value="UTF-8"/>
              </bean>
              <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                  <property name="objectMapper">
                      <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                          <property name="failOnEmptyBeans" value="false"/>
                      </bean>
                  </property>
              </bean>
          </mvc:message-converters>
      </mvc:annotation-driven>
  
      <!--添加视图解析器-->
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
          <!--前缀-->
          <property name="prefix" value="/WEB-INF/jsp/"/>
          <!--后缀-->
          <property name="suffix" value=".jsp"/>
      </bean>
  
  </beans>
  ```

3. 创建Controller

	```java
	@Controller								// 代表这个类会被Spring接管
	public class HelloController {
	
	    @RequestMapping("/hello")           // 真实访问地址   Localhost:8080/nhp/hello
	    public String hello(Model model){
	        // 封装数据
	        model.addAttribute("message", "Hello, SpringMVC Annotation!");
	        return "hello";         		// 会被视图解析器处理, /WEB-INF/jsp/hello.jsp
	    }
	}
	```

4. 注册Tomcat，测试



## 4 Controller及RestFul风格

控制器Controller：

* 控制器负责提供访问应用程序的行为，通常通过接口定义或注解两种方法实现
* 控制器负责解析用户的请求并将其转换为一个模型
* 在Spring MVC中，一个控制器类可以包含多个方法
* 在Spring MVC中，对于Controller的配置方法有很多种

### 4.1 RestFul风格

**概念**：

​		Restful就是一个资源定位及资源操作的风格。不是协议也不是标准，只是一种风格。基于这种风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。

**功能**：

* 资源：互联网所有的事物都可以被抽象为资源
* 资源操作：使用Post、Delete、Put、Get，使用不同方法对资源进行操作
* 分别对应增、删、改、查

<img src="../images/image-20201126102450886.png" alt="image-20201126102450886" style="zoom:50%;" />

* 原来的controller

```java
@Controller
public class RestFulController {
    @RequestMapping("/add")
    public String test1(int a, int b, Model model){
        int res = a + b;
        model.addAttribute("message", "结果为：" + res);
        return "test";
    }
}
```

![image-20201126103010212](../images/image-20201126103010212.png)

### 4.2 RestFul测试

1. 新建一个类，使用**@PathVariable**注解，让方法参数的值对应一个URL模板变量上

	```java
	@Controller
	public class RestFulController {
	    // 原来:      http://localhost:8080/add?a=2&b=3
	    // RestFul:  http://localhost:8080/add/2/3
	
	    @RequestMapping("/add/{a}/{b}")
	    public String test1(@PathVariable int a, @PathVariable int b, Model model){
	        int res = a + b;
	        model.addAttribute("message", "结果为：" + res);
	        return "test";
	    }
	}
	```

2. 使用Method属性指定请求类型
	用于约束请求的类型，可以收窄请求范围。指定请求谓词的类型如GET、POST、HEAD、OPTIONS、PUT、PATCH、DELETE、TRACE等，默认是GET方式

	```java
	@RequestMapping(value = "/add/{a}/{b}", method = {RequestMethod.GET})
	
	// 组合注解
	@GetMapping			// 扮演一个@@RequestMapping(method = {RequestMethod.GET})的快捷方式
	@PostMapping
	@PutMapping
	@DeleteMapping
	@PatchMapping
	```

3. 特点：安全



## 5 结果跳转方式

### 5.1 ModelAndView

设置ModelAndView对象，根据View的名称，和视图解析器跳到指定的页面

页面：**[视图解析器前缀] + viewName + [视图解析器后缀]**

```xml
<!-- 视图解析器 -->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
     id="internalResourceViewResolver">
   <!-- 前缀 -->
   <property name="prefix" value="/WEB-INF/jsp/" />
   <!-- 后缀 -->
   <property name="suffix" value=".jsp" />
</bean>
```

对应的controller

```java
public class ControllerTest1 implements Controller {
   public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
       //返回一个模型视图对象
       ModelAndView mv = new ModelAndView();
       mv.addObject("msg","ControllerTest1");
       mv.setViewName("test");
       return mv;
  }
}
```

### 5.2 ServletAPI

通过设置ServletAPI , 不需要视图解析器 .

1、通过`HttpServletResponse`进行输出

2、通过`HttpServletResponse`实现重定向

3、通过`HttpServletRequest`实现转发

```java
@Controller
public class ResultGo {

   @RequestMapping("/result/t1")
   public void test1(HttpServletRequest req, HttpServletResponse rsp) throws IOException {
       rsp.getWriter().println("Hello,Spring BY servlet API");
  }

   @RequestMapping("/result/t2")
   public void test2(HttpServletRequest req, HttpServletResponse rsp) throws IOException {
       rsp.sendRedirect("/index.jsp");
  }

   @RequestMapping("/result/t3")
   public void test3(HttpServletRequest req, HttpServletResponse rsp) throws Exception {
       //转发
       req.setAttribute("msg","/result/t3");
       req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req,rsp);
  }
}
```

### 5.3 SpringMVC

**通过SpringMVC来实现转发和重定向 - 无需视图解析器；**

测试前，需要将视图解析器注释掉

```java
@Controller
public class ResultSpringMVC {
   @RequestMapping("/rsm/t1")
   public String test1(){
       //转发
       return "/index.jsp";			// 全路径。从WEB-INF开始写
  }

   @RequestMapping("/rsm/t2")
   public String test2(){
       //转发二
       return "forward:/index.jsp";
  }

   @RequestMapping("/rsm/t3")
   public String test3(){
       //重定向
       return "redirect:/index.jsp";
  }
}
```

**通过SpringMVC来实现转发和重定向 - 有视图解析器；**

重定向 , 不需要视图解析器 , 本质就是重新请求一个新地方嘛 , 所以注意路径问题.

可以重定向到另外一个请求实现 .

```java
@Controller
public class ResultSpringMVC2 {
   @RequestMapping("/rsm2/t1")
   public String test1(){
       //转发
       return "test";
  }

   @RequestMapping("/rsm2/t2")
   public String test2(){
       //重定向
       return "redirect:/index.jsp";		// 重定向不能访问WEB-INF下的内容
       //return "redirect:hello.do"; //hello.do为另一个请求/
  }
}
```

### 5.4 数据处理

**1、提交的域名称和处理方法的参数名一致或者不一致**

提交数据 : http://localhost:8080/user/t1?name=komorebi

处理方法

```java
@Controller
public class ControllerUserTest05 {
    // localhost:8080/user/t1?name=xxx
    @GetMapping("/user/t1")
    public String test1(@RequestParam("name") String name1, Model model){
        // 1 接收前端参数
        System.out.println("get : " + name1);

        // 2 将返回的结果传递给前端
        model.addAttribute("message", name1);

        // 3 跳转视图
        return "test";
    }
}
```

**2、提交的是一个对象**

要求提交的表单域和对象的属性名一致  , 参数使用对象即可

```java
// localhost:8080/user/t2?id=1&name=xxx&age=18
@GetMapping("/user/t2")
public String test2(User user){
    System.out.println(user);
    return "test";
}
```

* 如果使用对象，前端传递的参数名和对象名必须一致，否则就是NULL

### 5.5 数据显示到前端

**第一种 : 通过ModelAndView**

```java
public class ControllerTest1 implements Controller {
   public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
       //返回一个模型视图对象
       ModelAndView mv = new ModelAndView();
       mv.addObject("msg","ControllerTest1");
       mv.setViewName("test");
       return mv;
  }
}
```

**第二种 : 通过ModelMap**

```java
@RequestMapping("/hello")
public String hello(@RequestParam("username") String name, ModelMap model){
   //封装要显示到视图中的数据
   //相当于req.setAttribute("name",name);
   model.addAttribute("name",name);
   System.out.println(name);
   return "hello";
}
```

**第三种 : 通过Model**

```java
@RequestMapping("/ct2/hello")
public String hello(@RequestParam("username") String name, Model model){
   //封装要显示到视图中的数据
   //相当于req.setAttribute("name",name);
   model.addAttribute("msg",name);
   System.out.println(name);
   return "test";
}
```

* **对比**
	* Model 只有寥寥几个方法只适合用于储存数据，简化了新手对于Model对象的操作和理解；
	* ModelMap 继承了 LinkedMap ，除了实现了自身的一些方法，同样的继承 LinkedMap 的方法和特性；
	* ModelAndView 可以在储存数据的同时，可以进行设置返回的逻辑视图，进行控制展示层的跳转。

### 5.6 乱码问题

1、我们可以在首页编写一个提交的表单form.jsp

```jsp
<form action="/e/t1" method="post">
 <input type="text" name="name">
 <input type="submit">
</form>
```

2、后台编写对应的处理类

```java
@Controller
public class ControllerEncodingTest06 {
    @RequestMapping("/e/t1")
    public String test1(String name, Model model){
        model.addAttribute("message",name);              	//获取表单提交的值
        return "test";                                      //跳转到test页面显示输入的值
    }
}
```

**解决方案**：

乱码问题通过过滤器解决 , 而SpringMVC给我们提供了一个过滤器 , 可以在web.xml中配置 

```java
<filter>
   <filter-name>encoding</filter-name>
   <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
   <init-param>
       <param-name>encoding</param-name>
       <param-value>utf-8</param-value>
   </init-param>
</filter>
<filter-mapping>
   <filter-name>encoding</filter-name>
   <url-pattern>/*</url-pattern>
</filter-mapping>
```

* 有些极端情况下.这个过滤器对get的支持不好 .

	* 处理方法 1 : 修改tomcat配置文件 ：设置编码！

		```xml
		<!--Tomcat/conf/server.xml-->
		<Connector URIEncoding="utf-8" port="8080" protocol="HTTP/1.1"
		          connectionTimeout="20000"
		          redirectPort="8443" />
		```

	* 处理方法 2：自定义过滤器，然后在web.xml中配置这个过滤器即可！

		```java
		package com.kuang.filter;
		
		import javax.servlet.*;
		import javax.servlet.http.HttpServletRequest;
		import javax.servlet.http.HttpServletRequestWrapper;
		import javax.servlet.http.HttpServletResponse;
		import java.io.IOException;
		import java.io.UnsupportedEncodingException;
		import java.util.Map;
		
		/**
		* 解决get和post请求 全部乱码的过滤器
		*/
		public class GenericEncodingFilter implements Filter {
		
		   @Override
		   public void destroy() {
		  }
		
		   @Override
		   public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
		       //处理response的字符编码
		       HttpServletResponse myResponse=(HttpServletResponse) response;
		       myResponse.setContentType("text/html;charset=UTF-8");
		
		       // 转型为与协议相关对象
		       HttpServletRequest httpServletRequest = (HttpServletRequest) request;
		       // 对request包装增强
		       HttpServletRequest myrequest = new MyRequest(httpServletRequest);
		       chain.doFilter(myrequest, response);
		  }
		
		   @Override
		   public void init(FilterConfig filterConfig) throws ServletException {
		  }
		
		}
		
		//自定义request对象，HttpServletRequest的包装类
		class MyRequest extends HttpServletRequestWrapper {
		
		   private HttpServletRequest request;
		   //是否编码的标记
		   private boolean hasEncode;
		   //定义一个可以传入HttpServletRequest对象的构造函数，以便对其进行装饰
		   public MyRequest(HttpServletRequest request) {
		       super(request);// super必须写
		       this.request = request;
		  }
		
		   // 对需要增强方法 进行覆盖
		   @Override
		   public Map getParameterMap() {
		       // 先获得请求方式
		       String method = request.getMethod();
		       if (method.equalsIgnoreCase("post")) {
		           // post请求
		           try {
		               // 处理post乱码
		               request.setCharacterEncoding("utf-8");
		               return request.getParameterMap();
		          } catch (UnsupportedEncodingException e) {
		               e.printStackTrace();
		          }
		      } else if (method.equalsIgnoreCase("get")) {
		           // get请求
		           Map<String, String[]> parameterMap = request.getParameterMap();
		           if (!hasEncode) { // 确保get手动编码逻辑只运行一次
		               for (String parameterName : parameterMap.keySet()) {
		                   String[] values = parameterMap.get(parameterName);
		                   if (values != null) {
		                       for (int i = 0; i < values.length; i++) {
		                           try {
		                               // 处理get乱码
		                               values[i] = new String(values[i]
		                                      .getBytes("ISO-8859-1"), "utf-8");
		                          } catch (UnsupportedEncodingException e) {
		                               e.printStackTrace();
		                          }
		                      }
		                  }
		              }
		               hasEncode = true;
		          }
		           return parameterMap;
		      }
		       return super.getParameterMap();
		  }
		
		   //取一个值
		   @Override
		   public String getParameter(String name) {
		       Map<String, String[]> parameterMap = getParameterMap();
		       String[] values = parameterMap.get(name);
		       if (values == null) {
		           return null;
		      }
		       return values[0]; // 取回参数的第一个值
		  }
		
		   //取所有值
		   @Override
		   public String[] getParameterValues(String name) {
		       Map<String, String[]> parameterMap = getParameterMap();
		       String[] values = parameterMap.get(name);
		       return values;
		  }
		}
		```

		```xml
		<filter>
			<filter-name>encoding</filter-name>
			<filter-class>com.komorebi.filter.GenericEncodingFilter</filter-class>
		</filter>
		<filter-mapping>
			<filter-name>encoding</filter-name>
			<url-pattern>/*</url-pattern>
		</filter-mapping>
		```

		

## 6 JSON

**JSON 和 JavaScript 对象互转**

要实现从JSON字符串转换为JavaScript 对象，使用 JSON.parse() 方法：

```javascript
var obj = JSON.parse('{"a": "Hello", "b": "World"}');
//结果是 {a: 'Hello', b: 'World'}
```

要实现从JavaScript 对象转换为JSON字符串，使用 JSON.stringify() 方法：

```javascript
var json = JSON.stringify({a: 'Hello', b: 'World'});
//结果是 '{"a": "Hello", "b": "World"}'
```

### 6.1 Jackson

* 1 导包

	```xml
	<!--jackson-core-->
	<dependency>
	   <groupId>com.fasterxml.jackson.core</groupId>
	   <artifactId>jackson-databind</artifactId>
	   <version>2.10.0</version>
	</dependency>
	```

* 2 web.xml

* 3 springmvc-servlet.xml

* 4 User.java

* 5 UserController.java

	```java
	@Controller
	public class UserController {
	    @RequestMapping(value = "/j1", produces = "application/json;charset=utf-8")		// 乱码问题
	    @ResponseBody           // 不走视图解析器，直接返回一个字符串
	    public String json1() throws JsonProcessingException {
	        // jsakson, ObjectMapper
	        ObjectMapper mapper = new ObjectMapper();
	
	        User user = new User("komorebi1", 18, "man");
	
	        String s = mapper.writeValueAsString(user);
	        return s;
	    }
	}
	```

	​       在类上直接使用 **@RestController** ，这样子，里面所有的方法都只会返回 json 字符串了，不用再每一个都添加@ResponseBody ！我们在前后端分离开发中，一般都使用 @RestController ，十分便捷！

	```java
	@RestController
	public class UserController {
	    @RequestMapping("/j1")
	//    @ResponseBody           // 不走视图解析器，直接返回一个字符串
	    public String json1() throws JsonProcessingException {
	        // jsakson, ObjectMapper
	        ObjectMapper mapper = new ObjectMapper();
	
	        User user = new User("komorebi1", 18, "man");
	
	        String s = mapper.writeValueAsString(user);
	        return s;
	    }
	    
	    @RequestMapping("/j2")
	    public String json2() throws JsonProcessingException{
	        ObjectMapper mapper = new ObjectMapper();
	
	        List<User> userList = new ArrayList<User>();
	
	        User user1 = new User("komorebi1", 18, "man");
	        User user2 = new User("komorebi2", 18, "man");
	        User user3 = new User("komorebi3", 18, "man");
	        User user4 = new User("komorebi4", 18, "man");
	
	        userList.add(user1);
	        userList.add(user2);
	        userList.add(user3);
	        userList.add(user4);
	
	        String s = mapper.writeValueAsString(userList);
	        return s;
	    }
	}
	```

* 对于输出时间的情况，可以抽象为工具类

	```java
	package com.komorebi.utils;
	
	import com.fasterxml.jackson.core.JsonProcessingException;
	import com.fasterxml.jackson.databind.ObjectMapper;
	import com.fasterxml.jackson.databind.SerializationFeature;
	
	import java.text.SimpleDateFormat;
	
	public class JsonUtils {
	    public static String getJson(Object object) {
	        return getJson(object,"yyyy-MM-dd HH:mm:ss");
	    }
	
	    public static String getJson(Object object, String dateFormat) {
	        ObjectMapper mapper = new ObjectMapper();
	        //不使用时间差的方式
	        mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);
	        //自定义日期格式对象
	        SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);
	        //指定日期格式
	        mapper.setDateFormat(sdf);
	        try {
	            return mapper.writeValueAsString(object);
	        } catch (JsonProcessingException e) {
	            e.printStackTrace();
	        }
	        return null;
	    }
	}
	```

	```java
	@RequestMapping("/j4")
	public String json5() throws JsonProcessingException {
	   Date date = new Date();
	   String json = JsonUtils.getJson(date);
	   return json;
	}
	```

### 6.2 FastJson

fastjson.jar是阿里开发的一款专门用于Java开发的包，可以方便的实现json对象与JavaBean对象的转换，实现JavaBean对象与json字符串的转换，实现json对象与json字符串的转换。实现json的转换方法很多，最后的实现结果都是一样的。![image-20201128104248693](../images/image-20201128104248693.png)

* 1 导包

	```xml
	<!--fastjson-->
	<dependency>
	    <groupId>com.alibaba</groupId>
	    <artifactId>fastjson</artifactId>
	    <version>1.2.75</version>
	</dependency>
	```

* 2 测试

	```java
	@RequestMapping("/j5")
	public String json5(){
	    //创建一个对象
	    User user1 = new User("K1号", 30, "男");
	    User user2 = new User("K2号", 30, "男");
	    User user3 = new User("K3号", 30, "男");
	    User user4 = new User("K4号", 30, "男");
	    List<User> list = new ArrayList<User>();
	    list.add(user1);
	    list.add(user2);
	    list.add(user3);
	    list.add(user4);
	
	    System.out.println("*******Java对象 转 JSON字符串*******");
	    String str1 = JSON.toJSONString(list);
	    System.out.println("JSON.toJSONString(list)==>" + str1);
	    String str2 = JSON.toJSONString(user1);
	    System.out.println("JSON.toJSONString(user1)==>" + str2);
	
	    System.out.println("\n****** JSON字符串 转 Java对象*******");
	    User jp_user1 = JSON.parseObject(str2, User.class);
	    System.out.println("JSON.parseObject(str2,User.class)==>" + jp_user1);
	
	    System.out.println("\n****** Java对象 转 JSON对象 ******");
	    JSONObject jsonObject1 = (JSONObject) JSON.toJSON(user2);
	    System.out.println("(JSONObject) JSON.toJSON(user2)==>" + jsonObject1.getString("name"));
	
	    System.out.println("\n****** JSON对象 转 Java对象 ******");
	    User to_java_user = JSON.toJavaObject(jsonObject1, User.class);
	    System.out.println("JSON.toJavaObject(jsonObject1, User.class)==>" + to_java_user);
	    return str1;
	}
	```

	

## 7 整合SSM框架

### 7.1 Mybatis层

1. 构建数据库

	```sql
	CREATE DATABASE `ssmbuild`;
	
	USE `ssmbuild`;
	
	DROP TABLE IF EXISTS `books`;
	
	CREATE TABLE `books` (
	`bookID` INT(10) NOT NULL AUTO_INCREMENT COMMENT '书id',
	`bookName` VARCHAR(100) NOT NULL COMMENT '书名',
	`bookCounts` INT(11) NOT NULL COMMENT '数量',
	`detail` VARCHAR(200) NOT NULL COMMENT '描述',
	KEY `bookID` (`bookID`)
	) ENGINE=INNODB DEFAULT CHARACTER SET=utf8;
	
	INSERT INTO `books`(`bookID`,`bookName`,`bookCounts`,`detail`) VALUES
	(1,'Java',1,'从入门到放弃'),
	(2,'MySQL',10,'从删库到跑路'),
	(3,'Linux',5,'从进门到进牢');
	```

2. 新建一个Maven项目，导包

3. Idea连接数据库

4. 建立各级目录，resource下新建**mybatis-config.xml**和**applicaionContext.xml**

	```xml
	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE configuration
	        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
	</configuration>
	```

	```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans
	       http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	</beans>
	```

	<img src="../images/image-20201205100128800.png" alt="image-20201205100128800" style="zoom:50%;" />

5. 新建**database.properties**

	```properties
	jdbc.driver=com.mysql.jdbc.Driver
	jdbc.url=jdbc:mysql://localhost:3306/ssmbuild?useSSL=true&useUnicode=true&characterEncoding=utf8
	jdbc.username=root
	jdbc.password=123456
	```

6. 编写**typeAliases**和**mappers**（mybatis-config.xml）

	```xml
	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE configuration
	        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
	    <typeAliases>
	        <package name="com.komorebi.pojo"/>
	    </typeAliases>
	    <mappers>
	        <mapper class="com.komorebi.dao.BookMapper"/>
	    </mappers>
	
	</configuration>
	```

7. 新建实体类**Books**

	```java
	@Data
	@AllArgsConstructor
	@NoArgsConstructor
	public class Books {
	
	    private int bookID;
	    private String bookName;
	    private int bookCounts;
	    private String detail;
	
	}
	```

8. 新建接口**BookMapper**，定义要实现的功能接口

	```java
	public interface BookMapper {
	    //增加一个Book
	    int addBook(Books book);
	
	    //根据id删除一个Book
	    int deleteBookById(@Param("bookID") int id);
	
	    //更新Book
	    int updateBook(Books books);
	
	    //根据id查询,返回一个Book
	    Books queryBookById(@Param("bookID") int id);
	
	    //查询全部Book,返回list集合
	    List<Books> queryAllBook();
	
	    //根据书名查询,返回一个Book
	    Books queryBookByName(@Param("bookName") String bookName);
	}
	```

9. 新建相应的**BookMapper.xml**

	```xml
	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="com.komorebi.dao.BookMapper">
	
	    <!--增加一个Book-->
	    <insert id="addBook" parameterType="Books">
	      insert into ssmbuild.books(bookName,bookCounts,detail)
	      values (#{bookName}, #{bookCounts}, #{detail})
	   </insert>
	
	    <!--根据id删除一个Book-->
	    <delete id="deleteBookById" parameterType="int">
	      delete from ssmbuild.books
	      where bookID=#{bookID}
	   </delete>
	
	    <!--更新Book-->
	    <update id="updateBook" parameterType="Books">
	      update ssmbuild.books
	      set bookName = #{bookName},bookCounts = #{bookCounts},detail = #{detail}
	      where bookID = #{bookID}
	   </update>
	
	    <!--根据id查询,返回一个Book-->
	    <select id="queryBookById" resultType="Books">
	      select * from ssmbuild.books
	      where bookID = #{bookID}
	   </select>
	
	    <!--查询全部Book-->
	    <select id="queryAllBook" resultType="Books">
	      select * from ssmbuild.books
	   </select>
	
	    <!--根据书名查询,返回一个Book-->
	    <select id="queryBookByName" resultType="Books">
	      select * from ssmbuild.books
	      where bookName = #{bookName}
	   </select>
	
	</mapper>
	```

10. 新建BookService

	```java
	//BookService:底下需要去实现,调用dao层
	public interface BookService {
	    //增加一个Book
	    int addBook(Books book);
	
	    //根据id删除一个Book
	    int deleteBookById(int id);
	
	    //更新Book
	    int updateBook(Books books);
	
	    //根据id查询,返回一个Book
	    Books queryBookById(int id);
	
	    //查询全部Book,返回list集合
	    List<Books> queryAllBook();
	
	    //根据书名查询,返回一个Book
	    Books queryBookByName(String bookName);
	}
	```

11. 新建BookServiceImpl

	```java
	public class BookServiceImpl implements BookService{
	    //调用dao层的操作，设置一个set接口，方便Spring管理
	    private BookMapper bookMapper;
	
	    public void setBookMapper(BookMapper bookMapper) {
	        this.bookMapper = bookMapper;
	    }
	
	    public int addBook(Books book) {
	        return bookMapper.addBook(book);
	    }
	
	    public int deleteBookById(int id) {
	        return bookMapper.deleteBookById(id);
	    }
	
	    public int updateBook(Books books) {
	        return bookMapper.updateBook(books);
	    }
	
	    public Books queryBookById(int id) {
	        return bookMapper.queryBookById(id);
	    }
	
	    public List<Books> queryAllBook() {
	        return bookMapper.queryAllBook();
	    }
	
	    public Books queryBookByName(String bookName) {
	        return bookMapper.queryBookByName(bookName);
	    }
	}
	```

### 7.2 Spring层

1. 新建**spring-dao.xml**

	```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:context="http://www.springframework.org/schema/context"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans
	       http://www.springframework.org/schema/beans/spring-beans.xsd
	       http://www.springframework.org/schema/context
	       https://www.springframework.org/schema/context/spring-context.xsd">
	
	    <!-- 配置整合mybatis -->
	    <!-- 1.关联数据库文件 -->
	    <context:property-placeholder location="classpath:database.properties"/>
	
	    <!-- 2.数据库连接池 -->
	    <!--数据库连接池
	        dbcp 半自动化操作 不能自动连接
	        c3p0 自动化操作（自动的加载配置文件 并且设置到对象里面）
	    -->
	    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	        <!-- 配置连接池属性 -->
	        <property name="driverClass" value="${jdbc.driver}"/>
	        <property name="jdbcUrl" value="${jdbc.url}"/>
	        <property name="user" value="${jdbc.username}"/>
	        <property name="password" value="${jdbc.password}"/>
	
	        <!-- c3p0连接池的私有属性 -->
	        <property name="maxPoolSize" value="30"/>
	        <property name="minPoolSize" value="10"/>
	        <!-- 关闭连接后不自动commit -->
	        <property name="autoCommitOnClose" value="false"/>
	        <!-- 获取连接超时时间 -->
	        <property name="checkoutTimeout" value="10000"/>
	        <!-- 当获取连接失败重试次数 -->
	        <property name="acquireRetryAttempts" value="2"/>
	    </bean>
	
	    <!-- 3.配置SqlSessionFactory对象 -->
	    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	        <!-- 注入数据库连接池 -->
	        <property name="dataSource" ref="dataSource"/>
	        <!-- 配置MyBaties全局配置文件:mybatis-config.xml -->
	        <property name="configLocation" value="classpath:mybatis-config.xml"/>
	    </bean>
	
	    <!-- 4.配置扫描Dao接口包，动态实现Dao接口注入到spring容器中 -->
	    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	        <!-- 注入sqlSessionFactory -->
	        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
	        <!-- 给出需要扫描Dao接口包 -->
	        <property name="basePackage" value="com.komorebi.dao"/>
	    </bean>
	
	</beans>
	```

2. **spring-service.xml**

	```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:context="http://www.springframework.org/schema/context"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans
	   http://www.springframework.org/schema/beans/spring-beans.xsd
	   http://www.springframework.org/schema/context
	   http://www.springframework.org/schema/context/spring-context.xsd">
	
	    <!-- 扫描service相关的bean -->
	    <context:component-scan base-package="com.komorebi.service" />
	
	    <!--BookServiceImpl注入到IOC容器中-->
	    <bean id="BookServiceImpl" class="com.komorebi.service.BookServiceImpl">
	        <property name="bookMapper" ref="bookMapper"/>
	    </bean>
	
	    <!-- 配置事务管理器 -->
	    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	        <!-- 注入数据库连接池 -->
	        <property name="dataSource" ref="dataSource" />
	    </bean>
	
	</beans>
	```

### 7.3 SpringMVC层

1. 增加web支持，web.xml

	```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
	         version="4.0">
	
	    <!--DispatcherServlet-->
	    <servlet>
	        <servlet-name>DispatcherServlet</servlet-name>
	        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	        <init-param>
	            <param-name>contextConfigLocation</param-name>
	            <!--一定要注意:我们这里加载的是总的配置文件，之前被这里坑了！-->
	            <param-value>classpath:applicationContext.xml</param-value>
	        </init-param>
	        <load-on-startup>1</load-on-startup>
	    </servlet>
	    <servlet-mapping>
	        <servlet-name>DispatcherServlet</servlet-name>
	        <url-pattern>/</url-pattern>
	    </servlet-mapping>
	
	    <!--encodingFilter-->
	    <filter>
	        <filter-name>encodingFilter</filter-name>
	        <filter-class>
	            org.springframework.web.filter.CharacterEncodingFilter
	        </filter-class>
	        <init-param>
	            <param-name>encoding</param-name>
	            <param-value>utf-8</param-value>
	        </init-param>
	    </filter>
	    <filter-mapping>
	        <filter-name>encodingFilter</filter-name>
	        <url-pattern>/*</url-pattern>
	    </filter-mapping>
	
	    <!--Session过期时间-->
	    <session-config>
	        <session-timeout>15</session-timeout>
	    </session-config>
	
	</web-app>
	```

2. Spring-mvc.xml

	```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:context="http://www.springframework.org/schema/context"
	       xmlns:mvc="http://www.springframework.org/schema/mvc"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans
	   http://www.springframework.org/schema/beans/spring-beans.xsd
	   http://www.springframework.org/schema/context
	   http://www.springframework.org/schema/context/spring-context.xsd
	   http://www.springframework.org/schema/mvc
	   https://www.springframework.org/schema/mvc/spring-mvc.xsd">
	
	    <!-- 配置SpringMVC -->
	    <!-- 1.开启SpringMVC注解驱动 -->
	    <mvc:annotation-driven />
	    <!-- 2.静态资源默认servlet配置-->
	    <mvc:default-servlet-handler/>
	
	    <!-- 3.配置jsp 显示ViewResolver视图解析器 -->
	    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />
	        <property name="prefix" value="/WEB-INF/jsp/" />
	        <property name="suffix" value=".jsp" />
	    </bean>
	
	    <!-- 4.扫描web相关的bean -->
	    <context:component-scan base-package="com.komorebi.controller" />
	
	</beans>
	```

3. **applicatonContext.xml**

	```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans
	       http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	    <import resource="classpath:spring-dao.xml"/>
	    <import resource="classpath:spring-service.xml"/>
	    <import resource="classpath:spring-mvc.xml"/>
	</beans>
	```

### 7.4 实现功能

1. BookController.java

	```java
	@Controller
	@RequestMapping("/book")
	public class BookController {
	    // 查询
	    @Autowired
	    @Qualifier("BookServiceImpl")
	    private BookService bookService;
	
	    @RequestMapping("/allBook")
	    public String list(Model model) {
	        List<Books> list = bookService.queryAllBook();
	        model.addAttribute("list", list);
	        return "allBook";
	    }
	
	    // 新增
	    @RequestMapping("toAddBook")
	    public String toAddPaper(){
	        return "addBook";
	    }
	    @RequestMapping("/addBook")
	    public String addPaper(Books books) {
	        System.out.println(books);
	        bookService.addBook(books);
	        return "redirect:/book/allBook";
	    }
	
	    // 更新
	    @RequestMapping("/toUpdateBook")
	    public String toUpdateBook(Model model, int id) {
	        Books books = bookService.queryBookById(id);
	        System.out.println(books);
	        model.addAttribute("book",books);
	        return "updateBook";
	    }
	    @RequestMapping("/updateBook")
	    public String updateBook(Model model, Books book) {
	        System.out.println(book);
	        bookService.updateBook(book);
	        Books books = bookService.queryBookById(book.getBookID());
	        model.addAttribute("books", books);
	        return "redirect:/book/allBook";
	    }
	
	    // 删除
	    @RequestMapping("/del/{bookId}")
	    public String deleteBook(@PathVariable("bookId") int id) {
	        bookService.deleteBookById(id);
	        return "redirect:/book/allBook";
	    }
	
	    // 查询书籍
	    @RequestMapping("/queryBook")
	    public String queryBook(Model model, String queryBookName){
	        Books book = bookService.queryBookByName(queryBookName);
	        List<Books> list = new ArrayList<Books>();
	        list.add(book);
	        if (book == null){
	            list = bookService.queryAllBook();
	            model.addAttribute("error", "未查到");
	        }
	        model.addAttribute("list", list);
	        return "allBook";
	    }
	}
	```

2. Index.jsp

	```jsp
	<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
	<!DOCTYPE HTML>
	<html>
	<head>
	  <title>首页</title>
	
	  <style type="text/css">
	    a {
	      text-decoration: none;
	      color: black;
	      font-size: 18px;
	    }
	    h3 {
	      width: 180px;
	      height: 38px;
	      margin: 100px auto;
	      text-align: center;
	      line-height: 38px;
	      background: deepskyblue;
	      border-radius: 4px;
	    }
	  </style>
	</head>
	<body>
	
	  <h3>
	    <a href="${pageContext.request.contextPath}/book/allBook">点击进入列表页</a>
	  </h3>
	</body>
	</html>
	```

3. allBook.jsp

	```jsp
	<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	<head>
	    <title>书籍列表</title>
	    <meta name="viewport" content="width=device-width, initial-scale=1.0">
	    <!-- 引入 Bootstrap -->
	    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
	</head>
	<body>
	
	<div class="container">
	
	    <div class="row clearfix">
	        <div class="col-md-12 column">
	            <div class="page-header">
	                <h1>
	                    <small>书籍列表 —— 显示所有书籍</small>
	                </h1>
	            </div>
	        </div>
	    </div>
	
	    <div class="row">
	        <div class="col-md-4 column">
	            <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/toAddBook">新增</a>
	            <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/allBook">显示全部书籍</a>
	        </div>
	        <div class="col-md-4 column"></div>
	        <div class="col-md-4 column">
	            <form action="${pageContext.request.contextPath}/book/queryBook" method="post" style="float: right">
	                <span style="color:red;font-weight: bold">${error}</span>
	                <input type="text" name="queryBookName" class="form-inline" placeholder="请输入要查询的书籍名称">
	                <input type="submit" value="查询" class="btn btn-primary">
	            </form>
	        </div>
	    </div>
	
	    <div class="row clearfix">
	        <div class="col-md-12 column">
	            <table class="table table-hover table-striped">
	                <thead>
	                <tr>
	                    <th>书籍编号</th>
	                    <th>书籍名字</th>
	                    <th>书籍数量</th>
	                    <th>书籍详情</th>
	                    <th>操作</th>
	                </tr>
	                </thead>
	
	                <tbody>
	                <c:forEach var="book" items="${requestScope.get('list')}">
	                    <tr>
	                        <td>${book.getBookID()}</td>
	                        <td>${book.getBookName()}</td>
	                        <td>${book.getBookCounts()}</td>
	                        <td>${book.getDetail()}</td>
	                        <td>
	                            <a href="${pageContext.request.contextPath}/book/toUpdateBook?id=${book.getBookID()}">更改</a> |
	                            <a href="${pageContext.request.contextPath}/book/del/${book.getBookID()}">删除</a>
	                        </td>
	                    </tr>
	                </c:forEach>
	                </tbody>
	            </table>
	        </div>
	    </div>
	</div>
	```

4. addBook.jsp

	```jsp
	<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	
	<html>
	<head>
	    <title>新增书籍</title>
	    <meta name="viewport" content="width=device-width, initial-scale=1.0">
	    <!-- 引入 Bootstrap -->
	    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
	</head>
	<body>
	<div class="container">
	
	    <div class="row clearfix">
	        <div class="col-md-12 column">
	            <div class="page-header">
	                <h1>
	                    <small>新增书籍</small>
	                </h1>
	            </div>
	        </div>
	    </div>
	    <form action="${pageContext.request.contextPath}/book/addBook" method="post">
	        书籍名称：<input type="text" name="bookName"><br><br><br>
	        书籍数量：<input type="text" name="bookCounts"><br><br><br>
	        书籍详情：<input type="text" name="detail"><br><br><br>
	        <input type="submit" value="添加">
	    </form>
	
	</div>
	```

5. updateBook.jsp

	```jsp
	<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	<head>
	    <title>修改信息</title>
	    <meta name="viewport" content="width=device-width, initial-scale=1.0">
	    <!-- 引入 Bootstrap -->
	    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
	</head>
	<body>
	<div class="container">
	
	    <div class="row clearfix">
	        <div class="col-md-12 column">
	            <div class="page-header">
	                <h1>
	                    <small>修改信息</small>
	                </h1>
	            </div>
	        </div>
	    </div>
	
	    <form action="${pageContext.request.contextPath}/book/updateBook" method="post">
	        <input type="hidden" name="bookID" value="${book.getBookID()}"/>
	        书籍名称：<input type="text" name="bookName" value="${book.getBookName()}"/>
	        书籍数量：<input type="text" name="bookCounts" value="${book.getBookCounts()}"/>
	        书籍详情：<input type="text" name="detail" value="${book.getDetail() }"/>
	        <input type="submit" value="提交"/>
	    </form>
	
	</div>
	```



## 8 Ajax

![image-20201207081101164](../images/image-20201207081101164.png)

### 8.1 测试

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>iframe测试体验页面无刷新</title>

    <script>
        function go() {
            var url = document.getElementById("url").value;
            document.getElementById("iframe1").src=url;
        }
    </script>
</head>
<body>

<div>
    <p>请输入地址：</p>
    <p>
        <input type="text" id="url" value="https://www.baidu.com/">
        <input type="button" value="提交" onclick="go()">
    </p>
</div>

<div>
    <iframe id="iframe1" style="width: 100%;height: 500px"></iframe>
</div>


</body>
</html>
```

### 8.2 JQuery

<img src="../images/image-20201207104040570.png" alt="image-20201207104040570" style="zoom:50%;" />

1. 配置web.xml 和 springmvc的配置文件

2. 编写一个**AjaxController**

	```java
	@Controller
	public class AjaxController {
	
	   @RequestMapping("/a1")
	   public void ajax1(String name , HttpServletResponse response) throws IOException {
	       if ("admin".equals(name)){
	           response.getWriter().print("true");
	      }else{
	           response.getWriter().print("false");
	      }
	  }
	
	}
	```

3. 编写**index.jsp**测试

	```jsp
	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	 <head>
	   <title>$Title$</title>
	   <%--<script src="https://code.jquery.com/jquery-3.1.1.min.js"></script>--%>
	   <script src="${pageContext.request.contextPath}/statics/js/jquery-3.1.1.min.js"></script>
	   <script>
	       function a1(){
	           $.post({
	               url:"${pageContext.request.contextPath}/a1",
	               data:{'name':$("#txtName").val()},
	               success:function (data,status) {
	                   alert(data);
	                   alert(status);
	              }
	          });
	      }
	   </script>
	 </head>
	 <body>
	
	<%--onblur：失去焦点触发事件--%>
	用户名:<input type="text" id="txtName" onblur="a1()"/>
	
	 </body>
	</html>
	```

### 8.3 返回json

1. 实体类user

	```java
	@Data
	@AllArgsConstructor
	@NoArgsConstructor
	public class User {
	
	   private String name;
	   private int age;
	   private String sex;
	
	}
	```

2. 获取一个集合对象，展示到前端页面

	```java
	@RequestMapping("/a2")
	public List<User> a2(){
	    List<User> userList = new ArrayList<User>();
	    userList.add(new User("k1", 18, "man"));
	    userList.add(new User("k2", 18, "man"));
	    userList.add(new User("k3", 18, "man"));
	    return userList;
	}
	```

3. 前端页面 **test2.jsp**

	```jsp
	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	<head>
	    <title>Title</title>
	
	    <script src="${pageContext.request.contextPath}/statics/js/jquery-3.5.1.js"></script>
	    <script>
	        $(function () {
	            $("#btn").click(function () {
	                // $.post(url, param, success)
	                $.post("${pageContext.request.contextPath}/a2", function (data) {
	                    // console.log(data);
	                    var html = "";
	                    for (let i = 0; i < data.length; i++) {
	                        html += "<tr>" +
	                                "<td>" + data[i].name + "</td>" +
	                                "<td>" + data[i].age + "</td>" +
	                                "<td>" + data[i].sex + "</td>" +
	                                "</tr>"
	                    }
	                    $("#content").html(html);
	                });
	            })
	        });
	    </script>
	
	    </head>
	<body>
	
	<input type="button" value="加载数据" id="btn">
	<table>
	    <tr>
	        <td>姓名</td>
	        <td>年龄</td>
	        <td>性别</td>
	    </tr>
	    <tbody id="content">
	        <%--数据：后台--%>
	    </tbody>
	</table>
	
	</body>
	</html>
	```

### 8.4 注册提示结果

1. **AjaxController**

	```java
	@RequestMapping("/a3")
	public String ajax3(String name,String pwd){
	   String msg = "";
	   //模拟数据库中存在数据
	   if (name!=null){
	       if ("admin".equals(name)){
	           msg = "OK";
	      }else {
	           msg = "用户名输入错误";
	      }
	  }
	   if (pwd!=null){
	       if ("123456".equals(pwd)){
	           msg = "OK";
	      }else {
	           msg = "密码输入有误";
	      }
	  }
	   return msg; //由于@RestController注解，将msg转成json格式返回
	}
	```

2. login.jsp

	```jsp
	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	<head>
	    <title>Title</title>
	
	    <script src="${pageContext.request.contextPath}/statics/js/jquery-3.5.1.js"></script>
	
	    <script>
	        function a1(){
	            $.post({
	                url:"${pageContext.request.contextPath}/a3",
	                data:{'name':$("#name").val()},
	                success:function (data) {
	                    if (data.toString() == 'OK'){
	                        $("#userInfo").css("color","green");
	                    }else {
	                        $("#userInfo").css("color","red");
	                    }
	                    $("#userInfo").html(data);
	                }
	            });
	        }
	        function a2(){
	            $.post({
	                url:"${pageContext.request.contextPath}/a3",
	                data:{'pwd':$("#pwd").val()},
	                success:function (data) {
	                    if (data.toString() == 'OK'){
	                        $("#pwdInfo").css("color","green");
	                    }else {
	                        $("#pwdInfo").css("color","red");
	                    }
	                    $("#pwdInfo").html(data);
	                }
	            });
	        }
	    </script>
	</head>
	<body>
	
	<p>
	    用户名：<input type="text" id="name" onblur="a1()">
	    <span id="userInfo"></span>
	</p>
	<p>
	    密码：<input type="text" id="pwd" onblur="a2()">
	    <span id="pwdInfo"></span>
	</p>
	
	</body>
	</html>
	```



## 9 拦截器

SpringMVC的处理器拦截器类似于Servlet开发中的过滤器Filter,用于对处理器进行预处理和后处理。开发者可以自己定义一些拦截器来实现特定的功能。

**过滤器与拦截器的区别：**拦截器是**AOP思想**的具体应用。

**过滤器**

- servlet规范中的一部分，任何java web工程都可以使用
- 在url-pattern中配置了/*之后，可以对所有要访问的资源进行拦截

**拦截器** 

- 拦截器是SpringMVC框架自己的，只有使用了SpringMVC框架的工程才能使用
- 拦截器只会拦截访问的控制器方法， 如果访问的是jsp/html/css/image/js是不会进行拦截的

### 9.1 配置

1. 新建Maven模块，配置web.xml和applicationContext.xml

2. 编写一个拦截器

	```java
	package com.komorebi.config;
	
	import org.springframework.web.servlet.HandlerInterceptor;
	import org.springframework.web.servlet.ModelAndView;
	
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;
	
	public class MyInterceptor implements HandlerInterceptor {
	    //在请求处理的方法之前执行
	    //如果返回true执行下一个拦截器
	    //如果返回false就不执行下一个拦截器
	    public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
	        System.out.println("------------处理前------------");
	        return true;
	    }
	
	    //在请求处理方法执行之后执行
	    public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {
	        System.out.println("------------处理后------------");
	    }
	
	    //在dispatcherServlet处理后执行,做清理工作.
	    public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {
	        System.out.println("------------清理------------");
	    }
	}
	```

3. 在springmvc的配置文件中配置拦截器

	```xml
	<!--关于拦截器的配置-->
	<mvc:interceptors>
	   <mvc:interceptor>
	       <!--/** 包括路径及其子路径-->
	       <!--/admin/* 拦截的是/admin/add等等这种 , /admin/add/user不会被拦截-->
	       <!--/admin/** 拦截的是/admin/下的所有-->
	       <mvc:mapping path="/**"/>
	       <!--bean配置的就是拦截器-->
	       <bean class="com.kuang.interceptor.MyInterceptor"/>
	   </mvc:interceptor>
	</mvc:interceptors>
	```

4. 编写一个Controller，接收请求

	```java
	package com.kuang.controller;
	
	import org.springframework.stereotype.Controller;
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.ResponseBody;
	
	//测试拦截器的控制器
	@Controller
	public class InterceptorController {
	
	   @RequestMapping("/interceptor")
	   @ResponseBody
	   public String testFunction() {
	       System.out.println("控制器中的方法执行了");
	       return "hello";
	  }
	}
	```

5. 前端 index.jsp

	```xml
	<a href="${pageContext.request.contextPath}/interceptor">拦截器测试</a>
	```

6. test

### 9.2 登录判断验证

![image-20201208100429140](../images/image-20201208100429140.png)

1. **index.jsp**

	```jsp
	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	  <head>
	    <title>$Title$</title>
	  </head>
	  <body>
	
	  <a href="${pageContext.request.contextPath}/interceptor">拦截器测试</a>
	
	  <a href="${pageContext.request.contextPath}/user/goLogin">登录页面</a>
	  <a href="${pageContext.request.contextPath}/user/main">首页</a>
	
	  </body>
	</html>
	
	```

2. **login.jsp**

	```jsp
	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	<head>
	    <title>Title</title>
	</head>
	<body>
	
	<h1>登录页面</h1>
	
	<form action="${pageContext.request.contextPath}/user/login" method="post">
	    用户名：<input type="text" name="username"/>
	    密码：<input type="text" name="password"/>
	    <input type="submit" value="提交">
	</form>
	
	</body>
	</html>
	```

3. **main.jsp**

	```jsp
	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	<head>
	    <title>Title</title>
	</head>
	<body>
	
	<h1>首页</h1>
	
	<span>${username}</span>
	
	<p>
	    <a href="${pageContext.request.contextPath}/user/goOut">注销</a>
	</p>
	
	</body>
	</html>
	```

4. **LoginController**

	```java
	@Controller
	@RequestMapping("/user")
	public class LoninController {
	    @RequestMapping("/main")
	    public String main(){
	        return "main";
	    }
	
	    @RequestMapping("/goLogin")
	    public String goLogin(){
	        return "login";
	    }
	
	    @RequestMapping("/login")
	    public String login(HttpSession session, String username, String password, Model model){
	        // 把用户的信息存在Session中
	        session.setAttribute("userLoginInfo", username);
	        model.addAttribute("username", username);
	        return "main";
	    }
	
	    @RequestMapping("/goOut")
	    public String goOut(HttpSession session){
	        session.removeAttribute("userLoginInfo");
	        return "redirect:main";
	    }
	}
	```

5. **loginInterceptor**

	```java
	public class LoginInterceptor implements HandlerInterceptor {
	    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
	        // 判断什么情况下没有登录
	        // 如果是登陆页面则放行
	        System.out.println("uri: " + request.getRequestURI());
	        if (request.getRequestURI().contains("login")) {
	            return true;
	        }
	
	        HttpSession session = request.getSession();
	
	        // 如果用户已登陆也放行
	        if(session.getAttribute("userLoginInfo") != null) {
	            return true;
	        }
	
	        // 用户没有登陆跳转到登陆页面
	        request.getRequestDispatcher("/WEB-INF/jsp/login.jsp").forward(request, response);
	        return false;
	    }
	}
	```

6. 在Springmvc的配置文件中注册拦截器

	```xml
	<mvc:interceptor>
	    <mvc:mapping path="/user/**"/>
	    <bean class="com.komorebi.config.LoginInterceptor"/>
	</mvc:interceptor>
	```



## 10 文件上传

* 前端表单要求：为了能上传文件，必须将表单的method设置为**POST**，并将enctype设置为multipart/form-data。只有在这样的情况下，浏览器才会把用户选择的文件以二进制数据发送给服务器；

* 1 导包

	```xml
	<!--文件上传-->
	<dependency>
	    <groupId>commons-fileupload</groupId>
	    <artifactId>commons-fileupload</artifactId>
	    <version>1.3.3</version>
	</dependency>
	<!--servlet-api导入高版本的-->
	<dependency>
	    <groupId>javax.servlet</groupId>
	    <artifactId>javax.servlet-api</artifactId>
	    <version>4.0.1</version>
	</dependency>
	```

* 2 applicationContext.xml

	```xml
	<!--文件上传配置-->
	<bean id="multipartResolver"  class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
	   <!-- 请求的编码格式，必须和jSP的pageEncoding属性一致，以便正确读取表单的内容，默认为ISO-8859-1 -->
	   <property name="defaultEncoding" value="utf-8"/>
	   <!-- 上传文件大小上限，单位为字节（10485760=10M） -->
	   <property name="maxUploadSize" value="10485760"/>
	   <property name="maxInMemorySize" value="40960"/>
	</bean>
	```

* 3 前端

	```jsp
	<form action="/upload" enctype="multipart/form-data" method="post">
	 <input type="file" name="file"/>
	 <input type="submit" value="upload">
	</form>
	```

* 4 **Controller**

	```java
	package com.komorebi.controller;
	
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.RequestParam;
	import org.springframework.web.bind.annotation.RestController;
	import org.springframework.web.multipart.commons.CommonsMultipartFile;
	
	import javax.servlet.http.HttpServletRequest;
	import java.io.*;
	
	@RestController
	public class FileController {
	    //@RequestParam("file") 将name=file控件得到的文件封装成CommonsMultipartFile 对象
	    //批量上传CommonsMultipartFile则为数组即可
	    @RequestMapping("/upload")
	    public String fileUpload(@RequestParam("file") CommonsMultipartFile file , HttpServletRequest request) throws IOException {
	
	        //获取文件名 : file.getOriginalFilename();
	        String uploadFileName = file.getOriginalFilename();
	
	        //如果文件名为空，直接回到首页！
	        if ("".equals(uploadFileName)){
	            return "redirect:/index.jsp";
	        }
	        System.out.println("上传文件名 : " + uploadFileName);
	
	        //上传路径保存设置
	        String path = request.getServletContext().getRealPath("/upload");
	        //如果路径不存在，创建一个
	        File realPath = new File(path);
	        if (!realPath.exists()){
	            realPath.mkdir();
	        }
	        System.out.println("上传文件保存地址：" + realPath);
	
	        InputStream is = file.getInputStream(); //文件输入流
	        OutputStream os = new FileOutputStream(new File(realPath, uploadFileName)); //文件输出流
	
	        //读取写出
	        int len=0;
	        byte[] buffer = new byte[1024];
	        while ((len=is.read(buffer)) != -1){
	            os.write(buffer,0,len);
	            os.flush();
	        }
	        os.close();
	        is.close();
	        return "redirect:/index.jsp";
	    }
	
	    /*
	     * 采用file.Transto 来保存上传的文件
	     */
	    @RequestMapping("/upload2")
	    public String  fileUpload2(@RequestParam("file") CommonsMultipartFile file, HttpServletRequest request) throws IOException {
	        //上传路径保存设置
	        String path = request.getServletContext().getRealPath("/upload");
	        File realPath = new File(path);
	        if (!realPath.exists()){
	            realPath.mkdir();
	        }
	        //上传文件地址
	        System.out.println("上传文件保存地址：" + realPath);
	
	        //通过CommonsMultipartFile的方法直接写文件（注意这个时候）
	        file.transferTo(new File(realPath +"/"+ file.getOriginalFilename()));
	
	        return "redirect:/index.jsp";
	    }
	}
	```



## 11 文件下载

* 1 controller

	```java
	@RequestMapping(value="/download")
	public String downloads(HttpServletResponse response ,HttpServletRequest request) throws Exception{
	   //要下载的图片地址
	   String  path = request.getServletContext().getRealPath("/upload");
	   String  fileName = "基础语法.jpg";
	
	   //1、设置response 响应头
	   response.reset(); //设置页面不缓存,清空buffer
	   response.setCharacterEncoding("UTF-8"); //字符编码
	   response.setContentType("multipart/form-data"); //二进制传输数据
	   //设置响应头
	   response.setHeader("Content-Disposition",
	           "attachment;fileName="+URLEncoder.encode(fileName, "UTF-8"));
	
	   File file = new File(path,fileName);
	   //2、 读取文件--输入流
	   InputStream input=new FileInputStream(file);
	   //3、 写出文件--输出流
	   OutputStream out = response.getOutputStream();
	
	   byte[] buff =new byte[1024];
	   int index=0;
	   //4、执行 写出操作
	   while((index= input.read(buff))!= -1){
	       out.write(buff, 0, index);
	       out.flush();
	  }
	   out.close();
	   input.close();
	   return null;
	}
	```

* 前端

	```jsp
	<a href="${pageContext.request.contextPath}/download">点击下载</a>
	
	<a href="${pageContext.request.contextPath}/statics/dog_and_cat.jpeg">点击下载2</a>
	```

	