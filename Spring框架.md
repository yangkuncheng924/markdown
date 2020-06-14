

# 第一章、Spring框架介绍

## 1.轻量级

​	1 对于运行环境是没有额外要求

​	2 .代码移至性高     

![image-20200611120314995](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200611120314995.png)



## 2.合设计模式

1. 工程

2. 代理

3. 模板

4. 策略

 

## 3.设计模式

​	1.广义概念

​	面向对象设计中 解决特点问题的经典代码

​	2.狭义概念

​	GOF4人帮定义的23种设计模式：工厂 单列 适配器 门面 代码 模板



### 3.1 工厂设计模式



​	1.概念：通过工厂类 创建对象

​				User user = new User();

​				UserDAO userDAO= new UserDAOImpl();

​	2.好处：解耦合

​		耦合：指定是代码间的强关联关系，一方的改变会影响到另一方

​		问题：不利于代码的维护

​		简单：把接口的实现类 硬编码在程序中

​					UserService userService= new UserServiceImpl();

​	

### 3.2 简单的工厂设计

~~~java
public class BeanFactory{
    private static properties env= new Properties();
    
    static{
        try{
            //第一步 获得IO输入流
       InputStream inputStream =  BeanFactory.class.getResourceAsStream("/applicationContext.properties");
            //第二步 文件内容 封装Properties集合中 key = userService value = com.baizhixx.UserServiceImpl
            env.load(inputStream);
            
            inputStream.close();
        }catch(IOException e){
            e.printStackTrace();
        }
        
        }
    
    
    /*
    对象的创建方式：
    1.直接调用构造方法 创建对象 UserService userService = new UserServiceImpl();
    2.通过反射的形式 创建对象 解耦合
     	class clazz =class.forName("com.baizhidu.basuc.UserServiceImpl");
     	UserService userSerivce = (UserServvice)clazz.newInstance();
    */
    
    
    public static UserService getUserService(){
        
        UserService userService = null;
        try{
            Class clazz = Class.forName(env.getProperty("userService"));
            userService = (UserService) clazz.newInstance();
        }catch(ClassNotFoundException e){
            e.printStackTrace();
        }catch(InstantiationException e){
            e.printStackTrace();
        }catch(IllegalAccessException e){
            e.printStackTrace();
        }
        
        return userService;
    }
    
    public static UserDAO getUserDAO(){
        
        UserDAO userDAO = null;
        try{
            Class clazz = Class.forName(env.getProperty("userDAO"));
            userDAO = (UserDAO)clazz.newInstance();
        }catch(ClassNotFoundException e){
            e.printStackTrace();
        }catch(){
            e.printStackTrace();
        }catch(){
            e.printStackTrace();
        }
        
        return userDAO;
         
    }
    }
    
}
~~~



## 4.通用工厂的设计

- 问题

  ~~~markdown
  1 简单工厂会产生大量的代码冗余
  ~~~

- 通用的工厂代码

  ~~~java
      public static Object getBean(String key)
      {
          Object ret = null;
          try{
              Class clazz = Class.forName(env.getProperty(key));
              ret=clazz.newInstance();
  
          }catch(Exception e){
              e.printStackTrace();
          }
          return ret;
      }
  
  ~~~

### 4.1通用工厂的使用方式

~~~markdown
1.定义类型（类）
2.通过配置文件的配置告知工厂(applicationContext.properties)
	key = value
3.通过工厂获得类的对象
	Objcet ret = BeanFactory.getBean("key")


~~~

## 5.总结

~~~markdown
Spring本质: 工厂 ApplicationContext(applicationContext.xml)
~~~



# 第二章、第一个Spring程序

## 1.软件版本

~~~markdown
1. jkd14.0
2. Maven 3.6.3
3. Spring 5.2.6
~~~

## 2.环境搭建

- Spring的jar包

~~~markdown
#设置pom 依赖
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
        <dependency><![CDATA[
        	]]><groupId>org.springframework</groupId>
        	<artifactId>spring-context</artifactId>
        	<version>5.2.6.RELEASE</version>
        </dependency>
      
~~~

- Spring的配置文件

  ~~~markdown
  1.配置文件的放置位置: 任意位置 没有硬性要求
  2.配置文件的命名 	: 没有硬性要求 建议:applicationContext.xml
  ~~~

  ![image-20200610122800117](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200610122800117.png)



## 3.Spring的核心API

- ApplicationContext

~~~m
1 作用: Spring提供的ApplicationContext这个工厂 用于对象的创建
2 好处: 解耦合
~~~

- ApplicationContext接口类型

  ~~~markdown
  1 接口: 屏蔽实现的差异
  2 非web环境 : ClasspathXmlApplicationContext (main junit)
  3 web环境 : XmlWebApplicationContext
  ~~~

![image-20200610131319038](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200610131319038.png)

- 重量级资源

  ~~~markdown
  1 ApplicationContext工厂的对象占用大量内存。
  2 不会频繁的创建对象 : 一个应用只会创建一个工厂对象。
  3 ApplicationContext工厂 : 一定是线程安全的(多线程并发访问)
  ~~~





## 4.程序开发

~~~markdown
1.创建类型
2.配置文件的配置 applicationContext.xml
	<bean id="person" class="basic.Person"/>
3.通过工程类，获得对象
	ApplicationContext
			|- ClassPathXmlApplicationContext
	ApplicationContext ctx = new ClassPathXmlapplicationContext("/applicationContext.xml");
	Persong persong = (Person)ctx.getBean("person");
~~~



## 5.细节分析

- 名词解释

  ~~~markdown
  Spring工厂创建的对象，叫做bean或者组件(componet)
  ~~~

- Spring工厂的相关方法

~~~java
        ApplicationContext ctx = new ClassPathXmlApplicationContext("/applicationContext.xml");
		//通过这种方式获得对象 不需要类型转换
        Person person = ctx.getBean("person",Person.class);
        System.out.println("person=" + person);
			
		//获取只能有一个Person
        Person person = ctx.getBean(Person.class);
        System.out.println("person=" + person);


        //获取的是Spring工厂配置文件中所有bean标签的id值 person personl
        String[] beanDefinitionNames = ctx.getBeanDefinitionNames();
        for (String beanDefinitionName:
             beanDefinitionNames) {
            System.out.println("beanDefinitionName="+ beanDefinitionName);
        }


        //根据类型获得String配置文件中对应的id值
        String[] beanNamesForType = ctx.getBeanNamesForType(Person.class);
        for (String id :
                beanNamesForType) {
            System.out.println("id=" + id);
        }


        //用于判断是否存在指定id值的bean  true
        System.out.println(ctx.containsBeanDefinition("person"));

        //用于判断是否存在指定id值的bean    false
        System.out.println(ctx.containsBean("a"));
~~~

- 配置文件中需要注意的细节

  ~~~markdown
  1. 只配置class属性
     <bean class=" ">
    1.2  上述这种配置 同样有id值 basic.Person#0
    1.3  应用场景: 如果这个bean只需要使用一次，那么就可以胜率Id值
    			  如果这个bean会使用多次，或者被其他bean引用这需要设置id值
  
  ~~~

- name属性

  ```markdown
  1. 作用：用于在Spring的配置文件中，为bean对象定义别名
     相同：
       1.ctx.getBean("id|name")-->object
       2.<bean id=" " class=" "
        等效
        <bean name=" " class=" "
    区别：
       1. 别名可以定义多个 但是id属性只能有一个值
       2. xml的id属性值，命名要求：必须以字母开头，字母 数字 下划线 连字符 不能以特殊字符开头
          name属性的值，命名没有要求 /person
          name属性会应用在特殊命名的场景下：/person (spring+struts1)
          
              XML发展到了今天：id属性的限制不存在 /person
   3.代码
   	containsBeanDefinition 只能判断id值 不能判断name值
   	containsBean		   可以判断id值 可以判断name值	
          
  ```

  

  

## 6. Spring工厂的底层实现原理(简易)

Spring工厂是可以调用对象私有的构造方法创建对象

## 7.思考

```markdown
1. 问题：未来在开发过程中，是不是所有的对象，都交给Spring工厂来创建呢？
2. 回答：理论上是，但是有特列：实体对象(entity)是不会交给Spring创建，它是由持久层框架进行创建。
```



# 第三章、Spring5.x与日志整合

~~~markdown
1. Spring与日志框架进行整合，日志框架就可以在控制台中，输出Spring框架运行过程中的一些重要的信息
2. 好处：便于了解Spring框架的运行过程，利于程序的调试
~~~

- Spring如何整合日志框架

  ~~~markdown
  1. 默认
  2.		Spring1.2.3早起都是与commons-logging.jar
  3.		Spring5.x默认整合的日志框架 logback log4j2
  4. 
  5. Spring5.x整合log4j
  6.	1. 引入log4j jar包
  7.  2. 引入log4.properties配置文件
  ~~~

  - pom

    ~~~xml
            <!-- 日志整合 -->
            <!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-core -->
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>1.2.17</version>
            </dependency>
    
            <!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-log4j12 -->
            <dependency>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
                <version>2.0.0-alpha1</version>
                <scope>test</scope>
            </dependency>
    ~~~

  - log4j.properties

    ~~~properties
    # resources文件夹目录下
    ### 配置根
    #Global logging configuration 开发时候建议使用 debug
    log4j.rootLogger=DEBUG, stdout
    # Console output...
    log4j.appender.stdout=org.apache.log4j.ConsoleAppender
    log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
    log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L -%m%n
    ~~~

    

# 第四章、注入（injection）

## 1.什么是注入

~~~markdown
通过Spring工厂及配置文件，为所创建对象的成员变量赋值
~~~

### 1.1为什么需要注入

- 通过编码的方式，为成员变量进行赋值，存在耦合

![img](file:///C:\Users\15371\AppData\Roaming\Tencent\Users\1537111657\QQ\WinTemp\RichOle\9K$JEL~%W1O0ZYNZ{8GIKMW.png)

### 1.2如何进行注入[开发步骤]

- 类的成员变量提供 set get 方法

- 配置spring的配置文件

  ~~~xml
      <!-- 配置注入信息 -->
      <bean id="person" class="basic.Person">
  
          <property name="id">
              <value>10</value>
          </property>
  
          <property name="name">
              <value>hello</value>
          </property>
  </bean>
  ~~~

  

### 1.3注入好处

- 解耦合



## 2.Spring注入的原理分析

- **Spring通过底层调用对象属性对应的set方法，完成成员变量的赋值，这种方式我们也称之为set注入**

  

![image-20200611120314995](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200611120314995.png)





# 第五章、Set注入详解

```xml
 针对不同类型的成员变量 在<property>标签，需要嵌套其他标签
 
     <property>
     	xxxx	
     </property>
 
```



![image-20200611123022896](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200611123022896.png)





## 1.JDK内置类型

### 1.1 String+8种基本类型

~~~xml
<value>suns</value>
~~~

### 1.2 数组

~~~xml
<list>
    <value>hello1</value>
    <value>hello2</value>
    <value>hello3</value>
 </list>
~~~

### 1.3 Set集合

~~~xml
<set>
     <value>12345</value>
     <value>123456</value>
     <value>1234567</value>
     <value>1234567</value>
     <value>1234567</value>
</set>
~~~

### 1.4 List集合

~~~xml
<list>
      <value>111111</value>
      <value>222222</value>
      <value>333333</value>
      <value>333333</value>
      <value>111111</value>
 </list>
~~~

### 1.5 Map集合

~~~xml
注意：map -- entry -- key有特定的标签 <key></key>
					 值根据对应的类型选择对应类型的标签
<map>
     <entry>
         <key>
             <value>kun</value>
         </key>
     </entry>

     <entry>
          <key>
             <value>yang</value>
          </key>
             <value>1234567</value>
      </entry>
</map>
~~~

### 1.6 Properites

~~~xml
Properties类型 特殊的Map key = String  vlue = String
<props>
    <prop key="key1">value1</prop>
    <prop key="key2">value2</prop>
</props>
~~~

### 1.7 复杂的JDK类型(Date)

- 需要程序员自定义类型转换，处理

  

## 2.用户自定义类型

### 2.1 第一种方式

- 为成员变量提供 set get方法

- 配置文件中进行注入 (赋值)

  ~~~xml
  <bean id="userService" class="xxxx.UserServiceImpl">
      <property name="userDAO">
      	<bean class="xxx.UserDAOImpl"/>
      </property>
  </bean>
  ~~~



### 2.2 第二种方式

- 第一种方式存在问题

  ~~~markdown
  1. 配置文件存在冗余
  2. 被注入的对象（UserDAO），多次创建 浪费(JVM)内存资源
  ~~~

- 为成员变量提供set get方法

- 配置文件中进行配置

  ~~~xml
  <bean id="userDAO" class="XXX.UserDAOImpl">
  </bean>
  <bean id="userService" class="xxx.UserServiceImpl">
  	<property name="userDAO">
      	<ref bean="userDAO"></ref>
      </property>
  </bean>
  
  #Spromg4.x废除了 <ref local=" "/> 基本等效 <ref bean= " "/>
  ~~~

  



## 3.Set注入的简化写法

### 3.1 基于属性简化

~~~xml
 JDK类型注入
<property name="name">
	<value>suns</value>
</property>

<property name="name" value="suns"></property>
注意： value属性 只能简化 8种基本类型+String 注入标签

用户自定义类型
<property name="userDAO">
	<ref bean="userDAO"></ref>
</property>

<property name="userDAO" ref="userDAO"></property>
~~~



### 3.2 基于p命名空间简化

~~~xml 
JDK类型注入
<property name="name">
	<value>suns</value>
</property>

<bean id="" class="" p:name="xxx"/>
注意： value属性 只能简化 8种基本类型+String 注入标签

用户自定义类型
<property name="userDAO">
	<ref bean="userDAO"></ref>
</property>

<bean id="" class"" p:userDAO-ref="userDAO"/>
~~~



# 第六章、构造注入

~~~markdown
 1. 注入：通过Spring的配置文件 为成员变量赋值
 2. Set注入：Spring调用set方法 通过配置文件 为成员变量赋值
 3. Spring调用构造方法 通过配置文件 为成员变量赋值
~~~

## 1.开发步骤

- 提供有参构造方法

  ~~~java
      private String name;
      private int age;
  
      public Customer(String name, int age) {
          this.name = name;
          this.age = age;
      }
  ~~~

- Spring的配置文件

  ~~~xml
      <bean id="customer" class="priv.yangkuncheng.Spring.basic.constructer.Customer">
          <constructor-arg>
              <value>yang</value>
          </constructor-arg>
  
          <constructor-arg>
              <value>102</value>
          </constructor-arg>
      </bean>
  ~~~



## 2.构造方法重载

### 2.1 参数个数不同时

- 通过控制 <constructor-arg> 标签的数量进行区分

### 2.2构造参数个数相同时

- 通过在标签引入 tpye 进行类型的区分  <constructor-arg type="">

## 

## 3. 注入总结

~~~ markdown
未来的实战中 应用set注入还是构造注入？
答案: set注入更多
	1.构造注入太麻烦(重载)
	2.Spring框架底层 大量应用了 set注入
~~~

![image-20200612211056931](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200612211056931.png)



# 第七章、反转控制与依赖注入

## 1.反转(转移)控制(IOC inverse of Control)

~~~markdown
控制： 对于成员变量赋值的控制权
反转控制：把对于成员变量赋值的控制权，从代码中反转(转移)到Spring工厂和配置文件中完成
	好处：解耦合
	底层实现：工厂设计模式
~~~

![image-20200613112707452](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200613112707452.png)





## 2.依赖注入 (Dependency Injection DI)

~~~markdown
	注入：通过Spring的工厂及配置文件，为对象（bean 组件）的成员变量赋值
	
	依赖注入：当一个雷需要另一个类时，就意味着依赖，一旦出现依赖，就可以把另一个类作为本类的成员变量，最终通过Spring配置文件进行注入(赋值)。
	好处:解耦合
~~~

![image-20200613113115221](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200613113115221.png)



# 第八章、Spring工厂创建复杂对象

![image-20200613115032562](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200613115032562.png)



## 1.什么是复杂对象

~~~markdown
	复杂对象:指的就是不能直接通过new构造方法创建的对象
	Connection
	SqlSessionfactory
~~~

## 2.Spring工厂创建复杂对象的3种方式

### 2.1 FactoryBean接口

- 开发步骤

  - 实现FactoryBean接口

    ![image-20200613115958876](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200613115958876.png)

  - Spring配置文件的配置

    ~~~markdown
    # 如果Class中指定的类型 是factoryBean接口的实现类，那么通过id值获得的是这个类创建的复杂对象 Connection
    <bean id="conn" class="priv.yangkuncheng.Spring.factorybean.ConnectionFactoryBean"/>
    ~~~

- 细节

  - 如果就想获得FactoryBean类型的对象  ctx.getBean("&conn")

    获得就是ConnectionFactoryBean对象

    

  - isSingleton方法

    返回 true只会创建一个复杂对象

    返回false 每一次都会创建新的对象

    问题 :根据这个对象的特点 决定是返回 true (SqlSessionFactory)还是false(Connection)

  - 依赖注入的体会(DI)

    ~~~xml
    把ConnectionFactoryBean中依赖4个字符串信息，进行配置文件的注入
    好处: 解耦合
     	
    <bean id="coon" class="priv.yangkuncheng.Spring.factorybean.ConnectionFactoryBean">
        <property name="diverClassName" value="com.mysql.cj.jdbc.Driver"></property>
        <property name="url" value="jdbc:mysql://localhost:3306/test"></property>
        <property name="username" value="root"></property>
        <property name="password" value=""></property>
       
    </bean>
    ~~~

- FactoryBean的实现原理

- ~~~markdown
  接口回调
   1. 为什么Spring规定FactoryBean接口 实现 并且getObject()?
   2. ctx.getBean("coon")获得是复杂对象 Connection 而没有 获得ConnectionFactoryBean(&)
   
  Spring内部运行流程
   1. 通过conn获得 ConnectionFactoryBean类的对象 ，进而通过instanceof判断出FactoryBean接口的实现类
   2. Spring按照规定 getObject() --->Connection
   3. 返回Connection
  ~~~

  ![image-20200613155330063](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200613155330063.png)

- FactoryBean总结
  
  - Spring中用于创建复杂对线的一种方式，也是Spring原生提供，后续Spring整合其它框架，大量应用FactoryBean

### 2.2 实例工厂

~~~markdown
1. 避免Spring框架的入侵
2. 整合遗留系统
~~~

- 开发步骤

  ~~~xml
  <bean id="connFactory" class="priv.yangkuncheng.Spring.factorybean.ConnectionFactory"></bean>
  <bean id="coon" factory-bean="connFactory" factory-method="getConnection"/>
  ~~~

### 2.3 静态工厂

~~~xml
<bean id="coon" class="priv.yangkuncheng.Spring.factorybean.StaticConnctionFactory" factory-method="getConnection"></bean>
~~~

## 3. Spring工厂创建对象的总结

![image-20200613165955901](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200613165955901.png)





# 第九章、控制Spring工厂创建对象的次数

## 1.如何控制简单对象的创建次数

~~~xml
<!-- 创建对象的次数  singleton：只会创建一次简单对象  prototype:每一次都会创建新的对象 -->
<bean id="account" scope="prototype|singleton" class="priv.yangkuncheng.spring.scope.Account"></bean>
~~~

## 2.如何控制复杂对象的创建次数

~~~markdown
FactoryBean{
	isSingleton(){
	  return true 只会创建一次
	  return false 每一次都会创建新的
	}
}
如没有isSingleton方法 还是通过scope属性 进行对象创建次数的控制
~~~

## 3.为什么要控制对象的创建次数

- 好处：节省不必要的内存浪费

- 什么样的对象只创建一次？

  ~~~markdown
  1. SqlSessionFactory
  2. DAO
  3. Service
  ~~~

- 什么样的对象每次都要创建新的？

  ~~~markdown
  1. Connection
  2. SqlSession | Session
  3. Struts2 Action
  ~~~





# 第十章、对象的生命周期

## 1.什么是对象的生命周期

- 指的是一个对象创建，存活，消亡的一个完整过程

## 2.为什么要学习对象的生命周期

- 由Spring负责对象的创建、存活、销毁，了解生命周期，有利于我们使用好Spring为我们创建的对象

## 3.生命周期的3个阶段

- 创建阶段

  ~~~markdown
  Spring工厂何时创建对象
  ~~~

  - scope="singleton"

    ~~~markdown
    Spring工厂创建的同时，对象的创建
    
    <!--测试生命周期  设置 scope="singleton" 这种情况下 也需要在获取对象的同时，创建对象 lazy-init="true"-->
    <bean lazy-init="true"></bean>
    ~~~

  - scope="prototype"

    ~~~markdown
    Spring工厂会在获取对象的同时，创建对象
    ctx.getBean("")
    ~~~

- 初始化阶段

  ~~~markdown
  Spring工厂在创建完对象后，调用对象的初始化方法 完成对应的初始化操作
  
  	1. 初始化方法提供：程序员根据需求，提供初始化方法 最终完成初始化操作
  	2. 初始化方法调用：Spring工厂进行调用
  ~~~

- 销毁阶段



