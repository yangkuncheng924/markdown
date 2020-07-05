# SpringMVC

# 一、MVC和SpringMVC

- 将url映射到java类或java类的方法
- 封装用户提交的数据
- 处理请求 -- 调用相关的业务处理 --封装响应数据
- 将响应的数据进行渲染 .jsp/html等表示层数据

说明:

常见的服务器端MVC框架有 ：Struts、SpringMVC、ASP.ne MVC、Zend Framework、JSF；常见前端MVC框架:vue、angularjs、react、backbone；由MVC演化出了另外一些模式如:MVP、MVVM等等....、



- **我们为什么要学习MVC？**
  - 轻量级，简单易学
  - 高效，基于请求响应的MVC框架
  - 约定优于配置
  - 功能强大:RESTful、数据验证、格式化、本地化、主题等
  - 简洁灵活





# 二、回顾

## 1.配置父项目的基本pom.xml

~~~xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.13</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.7.RELEASE</version>
</dependency>

<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>jsp-api</artifactId>
    <version>2.2.1-b03</version>
</dependency>

<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
    <version>2.5</version>
</dependency>

<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
~~~

## 2.回顾servlet

- servlet 需要的pom.xml

~~~xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
    <version>2.5</version>
</dependency>

<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>jsp-api</artifactId>
    <version>2.2.1-b03</version>
</dependency>
~~~

- test.jsp

~~~jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
${msg}
</body>
</html>
~~~

- from.jsp

~~~jsp
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
~~~

- web.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    
    <servlet>
        <servlet-name>hello</servlet-name>
        <servlet-class>priv.yangkuncheng.servlet.HelloServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
~~~

- java测试代码

~~~java
package priv.yangkuncheng.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       // 1.获取前端参数
        String method = req.getParameter("method");
        if(method.equals("add")){
            req.getSession().setAttribute("msg","执行了add方法");
        }
        if(method.equals("delete")){
            req.getSession().setAttribute("msg","执行了delete方法");
        }
        //2. 调用了业务层
        //3. 试图转发或者重定向
        req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req,resp);
    }
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req,resp);
    }
}
~~~



## 3.springmvc小测试

- 配置SpringMVC.xml文件

  ~~~xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
         xsi:schemaLocation=" http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd">
      <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
      <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
  
      <!-- 配置视图解析器 -->
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
          <property name="prefix" value="/WEB-INF/jsp/"/>
          <property name="suffix" value=".jsp"></property>
      </bean>
      <!-- Handler -->
      <bean id="/hello" class="priv.yangkuncheng.controller.HelloController"></bean>
  
  </beans>
  ~~~

- 配置web.xml

  ~~~xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
      
      <!-- 注册DispatcherServlet -->
      <servlet>
          <servlet-name>springmvc</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
          <!--关联一个SpringMVC配置文件: servlet-name -servlet.xml -->
          <init-param>
              <param-name>contextConfigLocation</param-name>
              <param-value>classpath:springmvc-servlet.xml</param-value>
          </init-param>
          <!-- 启动级别 -->
          <load-on-startup>1</load-on-startup>
  
      </servlet>
          <servlet-mapping>
              <servlet-name>springmvc</servlet-name>
              <url-pattern>/</url-pattern>
          </servlet-mapping>
      
  </web-app>
  ~~~

- 配置hello.jsp

  ~~~jsp
  <%--
    Created by IntelliJ IDEA.
    User: 15371
    Date: 2020/7/2
    Time: 13:49
    To change this template use File | Settings | File Templates.
  --%>
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  <html>
  <head>
      <title>Title</title>
  </head>
  <body>
  ${msg}
  </body>
  </html>
  ~~~

- java代码编写

  ~~~java
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  
  public class HelloController implements Controller {
  
      @Override
      public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
          //ModelAndView 模型和视图
          ModelAndView mv = new ModelAndView();
  
          //封装对象 ，放在ModelandView中，Model
          mv.addObject("msg","HelloSpringMVC!");
  
          //封装要跳转的视图，放在ModelAndView中
          mv.setViewName("hello");
          return mv;
      }
  }
  ~~~

  

# 三、SpringMVC使用

## 1.第一种方式 (不建议使用)

- 配置 web.xml

  ~~~xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
  
      <!--    配置DispatchServlet: 这个是SpringMVC的核心，请求分发器，前端控制器-->
      <servlet>
          <servlet-name>springmvc</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  
          <!--        DispatcherServlet要绑定Spring的配置文件-->
          <init-param>
              <param-name>contextConfigLocation</param-name>
              <param-value>classpath:springmvc-servlet.xml</param-value>
          </init-param>
          <!--启动级别 ： 1 -->
          <load-on-startup>1</load-on-startup>
      </servlet>
  
      <!--    在SpringMVC中 '/' 和 '/*'的区别           -->
      <!--    '/':  只匹配到所有的请求，不会匹配jsp页面       -->
      <!--    '/*'： 匹配所有的请求，包括jsp页面           -->
      <servlet-mapping>
          <servlet-name>springmvc</servlet-name>
          <url-pattern>/</url-pattern>
      </servlet-mapping>
  </web-app>
  ~~~

- 配置springmvc-servlet.xml

  ~~~xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
         xsi:schemaLocation=" http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd">
      <!-- 处理器映射器 -->
      <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
      <!--    处理器适配器-->
      <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
      <!--    视图解析器-->
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
          <!-- 前缀-->
          <property name="prefix" value="/WEB-INF/jsp/"/>
          <!-- 后缀-->
          <property name="suffix" value=".jsp"/>
      </bean>
      <bean id="/hello" class="priv.yangkuncheng.controller.HelloController"/>
  </beans>
  ~~~

- 配置test.jsp

  ~~~xml
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  <html>
  <head>
      <title>Title</title>
  </head>
  <body>
  ${msg}
  </body>
  </html>
  ~~~

- 配置实现类

  ~~~java
  public class HelloController implements Controller {
      @Override
      public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
          ModelAndView mv = new ModelAndView();
          //业务代码
          String result = "HelloSpringMVC";
          mv.addObject("msg",result);
  
          //试图跳转
          mv.setViewName("test");
          return mv;
      }
  }
  ~~~

## 2.第二种方式，注解实现(推荐)

- 配置 web.xml

  ~~~xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
      <!--    配置DispatchServlet: 这个是SpringMVC的核心，请求分发器，前端控制器-->
      <servlet>
          <servlet-name>SpringMVC</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  
          <!--        DispatcherServlet要绑定Spring的配置文件-->
          <init-param>
              <param-name>contextConfigLocation</param-name>
              <param-value>classpath:springmvc-servlet.xml</param-value>
          </init-param>
          <!--启动级别 ： 1 -->
          <load-on-startup>1</load-on-startup>
      </servlet>
  
      <!--    在SpringMVC中 '/' 和 '/*'的区别           -->
      <!--    '/':  只匹配到所有的请求，不会匹配jsp页面       -->
      <!--    '/*'： 匹配所有的请求，包括jsp页面           -->
      <servlet-mapping>
          <servlet-name>SpringMVC</servlet-name>
          <url-pattern>/</url-pattern>
      </servlet-mapping>
  </web-app>
  ~~~

- 配置springmvc-servlect.xml

  ~~~xml
  <?xml version="1.0" encoding="UTF-8"?>
  
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:p="http://www.springframework.org/schema/p"
         xmlns:mvc="http://www.springframework.org/schema/mvc"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc-3.0.xsd">
  
      <!--开启注解扫描-->
      <context:component-scan base-package="priv.yangkuncheng.controller"/>
      <mvc:default-servlet-handler/>
      <mvc:annotation-driven/>
  
  <!--    视图解析器对象-->
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
          <!--表示到/WEB-INF/pages目录下去找-->
          <property name="prefix" value="/WEB-INF/jsp/"/>
          <!--所找文件后缀名为.jsp-->
          <property name="suffix" value=".jsp"/>
      </bean>
  
  </beans>
  ~~~

- 配置text.jsp

  ~~~jsp
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  <html>
  <head>
      <title>Title</title>
  </head>
  <body>
  ${msg}
  </body>
  </html>
  ~~~

- 配置实现类

  ~~~java
  package priv.yangkuncheng.controller;
  
  import org.springframework.stereotype.Controller;
  import org.springframework.ui.Model;
  import org.springframework.web.bind.annotation.RequestMapping;
  
  @Controller
  public class HelloController {
      @RequestMapping("hello")
      public String hello(Model model){
          model.addAttribute("msg","hello,springmvc");
          return "hello";
      }
  }
  ~~~


# 四、RestFul风格

- Restful就跟一个资源定位及资源操作的风格，不是标准也不是协议，只是一种风格，基于这个风格设计的软件可以简洁，更有层次，更易于实现缓存等机制



- 功能

  - 资源: 互联网所有的事务都可以被抽象为资源
  - 自愿操作:使用POST、DELETE、PUT、GET,使用不同方法对资源进行操作。
  - 分别对应 添加、删除、修改、查询

  

# 五、JSON

- pom.xml

  ~~~xml
  <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.11.0</version>
  </dependency>
  
  <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.12</version>
  </dependency>
  ~~~

- web.xml

  ~~~xml
  <servlet>
      <servlet-name>springmvc</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
      <init-param>
          <param-name>contextConfigLocation</param-name>
          <param-value>classpath:springmvc-servlet.xml</param-value>
      </init-param>
      <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
      <servlet-name>springmvc</servlet-name>
      <url-pattern>/</url-pattern>
  </servlet-mapping>
  ~~~

- springmvc-servlet.xml

  ~~~xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:p="http://www.springframework.org/schema/p"
         xmlns:mvc="http://www.springframework.org/schema/mvc"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc-3.0.xsd">
      <context:component-scan base-package="priv.yangkuncheng.controller"/>
      <mvc:default-servlet-handler/>
      <mvc:annotation-driven/>
  
      <!--    视图解析器-->
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
          <!-- 前缀-->
          <property name="prefix" value="/WEB-INF/jsp/"/>
          <!-- 后缀-->
          <property name="suffix" value=".jsp"/>
      </bean>
  </beans>
  ~~~

- java代码

  ~~~java
  package priv.yangkuncheng.pojo;
  
  public class User {
      private String name;
      private int age;
      private String sex;
  	/*
  		set get 构造器
  	*/
  }
  ~~~

- java实现代码

  ~~~java
  import com.fasterxml.jackson.core.JsonProcessingException;
  import com.fasterxml.jackson.databind.ObjectMapper;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.ResponseBody;
  import org.springframework.web.bind.annotation.RestController;
  import priv.yangkuncheng.pojo.User;
  import priv.yangkuncheng.utils.JsonUtils;
  import java.text.SimpleDateFormat;
  import java.util.ArrayList;
  import java.util.Date;
  import java.util.List;
  
  //@Controller
  @RestController
  public class UserController {
      @RequestMapping("tiyi")
      @ResponseBody   //它就不会走视图解析器
      public String json1() throws JsonProcessingException {
          ObjectMapper mapper = new ObjectMapper();
          User user = new User("yangyi",3,"男");
          String str = mapper.writeValueAsString(user);
          return user.toString();
      }
  
      @RequestMapping("js")
      public String json2() {
          ObjectMapper mapper = new ObjectMapper();
          List<User> list = new ArrayList<User>();
  
          User user = new User("yangyi",3,"男");
          User user1 = new User("yanger",4,"男");
          User user2 = new User("yangsan",5,"男");
          User user3 = new User("yangsi",6,"男");
  
          list.add(user);
          list.add(user1);
          list.add(user2);
          list.add(user3);
  
          String str = null;
          try {
              str = mapper.writeValueAsString(list);
          } catch (JsonProcessingException e) {
              e.printStackTrace();
          }
  
          return str;
      }
      @RequestMapping("js1")
      public String json3() throws JsonProcessingException {
          ObjectMapper mapper = new ObjectMapper();
          Date date = new Date();
          
          //自定义日期的格式 转换时间
          SimpleDateFormat std = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
  
          return mapper.writeValueAsString( std.format(date));
      }
      
      @RequestMapping("js2")
      public String json4() {
          Date date = new Date();
  //        return JsonUtils.getJson(date,"yyyy-MM-dd HH:mm:ss");
          return JsonUtils.getJson(date);
      }
  }
  
  ~~~

- 封装的类实现

  ~~~java
  import com.fasterxml.jackson.core.JsonProcessingException;
  import com.fasterxml.jackson.databind.ObjectMapper;
  import com.fasterxml.jackson.databind.SerializationFeature;
  import java.text.SimpleDateFormat;
  
  
  public class JsonUtils {
      public static String getJson(Object object){
          return getJson(object,"yyyy-MM--dd HH:mm:ss");
      }
  
      public static String getJson(Object object,String dateFormat){
          ObjectMapper mapper = new ObjectMapper();
  
          mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);
  
          SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);
  
          mapper.setDateFormat(sdf);
  
          try {
              return mapper.writeValueAsString(object);
          } catch (JsonProcessingException e) {
              e.printStackTrace();
          }
          return null;
      }
  }
  
  ~~~

阿里巴巴提供的json开源

~~~xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.70</version>
</dependency>
~~~



# 六、SSM整合

## 1.搭建pom.xml环境

- ```markdown
  依赖：junit 数据库驱动，连接池，servlet，jsp，mybatis，mybatis-spring，spring
  ```