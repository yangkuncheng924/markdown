# 					**SpringBoot**

# 一、Spring入门

## 1. 第一个SpringBoot的Hello world

### 	1.1 创建一个maven工程



### 	1.2 导入依赖的springboot相关依赖

~~~xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot</artifactId>
	<version>2.3.1.RELEASE</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
	<version>2.3.1.RELEASE</version>
</dependency>

<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.3.1.RELEASE</version>
</dependency>
~~~

###  1.3 编写一个主程序

~~~java
/**
 * @SpringBootApplication   来标注一个主程序类，说明这是一个Spring boot应用
 */
@SpringBootApplication
public class Helloworld {
    public static void main(String[] args){
      // Spring应用启动run
        SpringApplication.run(Helloworld.class,args);
    }
}
~~~

### 1.4 编写相关的Conteroller、Service

~~~java
@Controller
public class HelloController {
    @ResponseBody
    @RequestMapping("/hello")
    public String hello(){
        return "hello world!";
    }
}
~~~

### 1.5 运行

~~~java
/*
运行 该主类 访问localhosh：8080/hello 
*/
@SpringBootApplication
public class Helloworld {
    public static void main(String[] args){
      // Spring应用启动run
        SpringApplication.run(Helloworld.class,args);
    }
}
~~~

### 1.6自动打包插件

~~~xml
<!--可将写好代码 一键打包 需导入该插件 -->
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-maven-plugin -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <version>2.3.1.RELEASE</version>
</dependency>
~~~

- 选择maven 选择package即可打包

![image-20200628174500925](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200628174500925.png)

- 生成文件即放在target

  ![image-20200628174614837](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200628174614837.png)

## 2.使用Spring Boot快速创建Spring Boot项目

IDEA都支持使用Spring的项目创建向导快速创建一个Spring Boot项目

选择new -> project ->Spring Initializr

- resources文件夹目录结构

  - static:保存所有的静态资源；js css images；

    

  - templates：保存所有的模板页面；(Sping Boot默认jar包使用嵌入式的Tomcat，默认不知道JSP页面)；可以使用模板引擎（freemarker、thymeleaf）；

    

  - application.properties:Spring Boot应用的配置文件;可以修改一些默认配置





# 二、配置文件

## 1.SpringBoot使用一个全局的配置文件 配置文件名是固定的

- application.properties
- application.yml



配置文件的作用 ：修改SpringBoot自动配置默认值；SpringBoot在底层都给我们自动配置好



YAML(YAML Ain't Markup Language)

​	YAML A Markup Language : 是一个标记语言

​	YAML isn't Markup Language : 不是一个标记语言；

标记语言 :

以前的配置文件大多都使用的是 xxx.xml文件

YAML : 以数据为中心 ，比json xml等更适合做配置文件

YAML ：配置列子

~~~yaml
server:
  port: 8081
~~~

XML :

~~~xml
<server>
	<port>8081</port>
</server>
~~~



## 2、YAML语法

### 1.基本语法

k: v :表示一对键值对（空格必须有）

以**空格**的缩进来控制层级关系 只要左对齐的一列数据，都是同一个层级

~~~yml
server:
	port: 8081
	path: /hello
~~~

属性和值也是大小写敏感

### 2.值的写法

**字面量 ： 普通的值(数字，字符串，布尔)**

~~~markdown
	K：V  ：字面直接来写 

				字符串默认不用加上单引号或者双引号

				"" : 双引号 不会转义字符串里面的特殊字符 特殊字符会作为本身想表示的意思

						name : "zhangsan \n list" 
						输出  :  zhangsan
								list
				'' : 单引号 会转义特殊字符，特殊字符最终只是一个普通的字符串数据
                		name : 'zhangsan \n list' 
                		输出  : zhangsan \n list								
~~~

**对象、Map(属性和值)（键值对）:**

~~~markdown
	k: v :在下一行来写对象的属性和值的关系 ，注意缩进
		对象还是k: v的方式	
~~~

~~~yml
friends:
		lastName: zhangsan
		age: 20	
~~~

**行内写法 :**

~~~yml
friends : {lastname: zhangsan,age: 18}
~~~

**数组(List、Set) :**

~~~yml
#用 - 值表示数组中的一个元素
pets:
	- cat
	- dog
	- pig
~~~

**行内写法**

~~~yml
pets: [cat,dog,pig]
~~~

## 3.application.yml配置文件值注入

- 配置文件

~~~yml
person:
  lastName: zhangsan
  age: 18
  boss: false
  birth: 2017/2/1
  maps: {k1: v1,k2: 12}
  lists:
    - lisi
    - zhaoliu
  dog:
    name: 小狗
    age: 2
~~~

- java代码

~~~java
   /*
    将配置文件中配置的每一个属性的值，映射到这个组件中
   @ConfigurationProperties 告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定
   prefix = "person" 配置文件中那个下面的所有属性进行一一映射

   只有这个组件是容器中的组件，才能容器提供的@ConfigurationProperties功能
 */
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
	private String lastname;
    private Integer age;
    private Boolean boss;
    private Date birth;
    private Map<String, Object> maps;
    private List<Object> lists;
    private Dog dog;
    set
    get
    toString    
}
~~~

- 可以导入配置文件处理器，以后编写配置就有提示

~~~xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
~~~

## 4.application.properties配置文件注入

~~~yml
#配置person
person.lastname=你好
person.age=18
person.boss=false
person.birth=2020/02/01
person.lists=1,2,3
person.maps.k1=k1
person.maps.k2=k2
person.maps.v1=v1
person.maps.v2=v2

person.dog.name=dog
person.dog.age=12
~~~

- **java代码同application.yml配置文件**

## 5.使用@Bean注解给容器添加组件

~~~java
/*
@Configuration 指明当前是一个配置类，就是来替代之前的spring配置文件

在配置文件中使用<bean></bean>标签添加组件
 */
@Configuration
public class MyAppConfig {
    //将方法的返回值添加到容器中，容器中这个组件默认的id就是方法名
    @Bean
    public HelloService helloService() {
        System.out.println("MyAppConfig.helloService");
        return new HelloService();
    }
}
~~~

Test

~~~java
@Test
void testHelloService(){
    boolean b = ioc.containsBean("helloService");
    System.out.println(b);
}

// 输出 true
~~~

## 6.自动配置原理

配置文件到底能写什么？

~~~markdown
自动配置原理:
1. SpringBoot启动的时候加载主配置类，开启了自动配置功能 @EnableAutoConfiguration
2. @EnableAutoConfiguration 作用 :
	利用EnableAutoConfigurationImportSelector给容器中导入一些组件？
	
	可以插入selectImports()方法
	
	List<String>configurations = getCandidateConfigurations(annotationMetadata,arrtbutes); 获取候选的配置
~~~

~~~java
SpringFactorisesLoader.loadFactoryNames()
    扫描所有jar包类路径下 META-INF/spring.factories
    把扫描到的这些文件的内容包装成properties对象
    从properties中获取到EnableAutoConfiguration.class类对应的值，然后把他们添加在容器中
~~~

# 三、日志

SpringBoot : 底层是Spring框架 Spring框架默认是用JCL

​	SpringBoot选用 SLF4j和logback；

- 如何在系统中使用SLF4j

  ![image-20200629132959681](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200629132959681.png)

- 默认配置

  ```java
  @SpringBootTest
  class LogApplicationTests {
      //记录器
      Logger logger = LoggerFactory.getLogger(getClass());
      @Test
      void contextLoads() {
          /*
          日志的级别：
          由低到高 trace<debug<info<warn<error
          可以调整输出的日志级别，日志就只会在这个级别以后的高级别生效
           */
  
          logger.trace("这是trace日志...");
          logger.debug("这是dug日志");
          //springboot默认给我们使用的是info级别 没有指定级别就用springboot默认规划的级别 
          logger.info("这是info日志");
          logger.warn("这是warn日志");
          logger.error("这是error日志");
      }
  }
  ```

- SpringBoot修改日志的默认配置

  ~~~properties
  logging.level.priv.yangkuncheng=trace
  
  #当前项目生成一个日志文件
  logging.file.name=D:/spirngboot.log
  #在控制台输出的日志格式
  logging.pattern.console=
  #指定文件中日志输出的格式
  logging.pattern.file=
  ~~~



# 四、Web开发

使用SpringBoot

- 1. 创建SpringBoot应用，选中我们需要的模块
  2. SpringBoot已经默认将这些场景配置好了，只需要在配置文件中指定少量配置就可以运行
  3. 自己编写业务代码



**自动配置原理？**

这个场景SpringBoot帮我们配置了什么？能不能修改？能修改哪些配置？能不能扩展？

~~~markdown
xxxxAutoConfiguration : 帮我们给容器中自动配置组件
xxxxProperties		: 配置类来封装配置文件的内容

~~~

