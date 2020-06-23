









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
	
	依赖注入：当一个类需要另一个类时，就意味着依赖，一旦出现依赖，就可以把另一个类作为本类的成员变量，最终通过Spring配置文件进行注入(赋值)。
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

![image-20200614201006006](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200614201006006.png)

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

  - ImitializingBean接口

    ~~~java
    //程序员根据需求，实现的方法，完成初始化操作
    public void afterProperitesSet(){
        
    }
    ~~~

  - 对象中提供一个普通的方法

    ~~~java
    public void myInit(){
        
    }
    <bean id="product" class="xxx.Product" init-method="myInit"/>
    ~~~

  - 细节分析

    1. 如果一个对象即实现lnitializingBean同时又提供的普通的初始化方法 顺序

       ~~~markdown
       1. 普通初始化方法
       2. InitiaIizingBean
       ~~~

    2. 注入一定发生在初始化操作的前面

       3.什么叫初始化操作

    ~~~markdown
    1. 资源的初始化:数据库 IO 网络 .....
    ~~~


- 销毁阶段

  ~~~markdown
  Spring销毁对象前,会调用对象的销毁方法，完成销毁操作
  
  	1. Spring什么时候销毁所创建的对象?
  		ctx.close();
  	2. 销毁方法:程序员根据自己的需求 定义销毁方法 完成销毁操作
      	调用:Spring工厂完成调用
  ~~~

  - DisposableBean

    ~~~java
    public void destroy() throws Exception{
        
    }
    ~~~

  - 定义一个普通的销毁方法

    ~~~java
    public void myDestroy() throws Exception{
        
    }
    <bean id="" class="" init-method="" destroy-method="myDestroy"/>
    ~~~

  - 细节分析

    1. 销毁方法的操作只适用于 scope="singleton"
    2. 什么叫做销毁操作
       - 主要是指就是资源的释放操作 io.close() connection.close();



# 第十一章、配置文件参数化

~~~markdown
把Spring配置文件中需要经常修改的字符串信息，转移到一个更小的配置文件中

1. Spring的配置文件中存在需要经常修改的字符串？
	存在 以数据库连接相关的参数 代表
2. 经常变化字符串，在Spring的配置文件中，直接修改
	不利于项目的维护(修改)
3. 转移到一个小的配置文件(.properties)
	利于维护(修改)

配置文件参数化: 利于Spring配置文件的维护(修改)
~~~

## 1.配置文件参数的开发步骤

- 提供一个小的配置文件(.properities)

  ~~~properties
   名字:随便
   放置位置:随便
   
   jdbc.driverClassName=com.mysql.cj.jdbc.Driver
   jdbc.url=jdbc:mysql://localhost:3306/test
   jdbc.username=root
   jdbc.password=
  ~~~

-  Spring的配置文件与小配置文件进行整合

      ~~~xml
applicationContext.xml
<context:property-placeholder location="classpath：/db.properties"/>
      ~~~

- 在Spring配置文件中通过${key}获取小配置文件中对应的值

![image-20200614230348314](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200614230348314.png)



# 第十二章、自定义类型转换器

## 1.类型转换器

~~~markdown
作用:Spring通过类型转换器把配置文件中字符串类型的数据，转换成了对象中成员变量对应的数据，进而完成了注入
~~~



![image-20200615082950797](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200615082950797.png)



## 2.自定义类型转换器

~~~markdown
 原因:当Spring内部没有提供特定类型转换器时,而程序员在应用的过程中还需要使用,那么就需要程序员自己定义类型转换器
~~~

- 类 implements Converter接口

  ~~~java
  public class MyDateConverter implements Converter<String, Date> {
  
      /*
          convert方法作用:  String--->DAte
                          SimpleDateFormat sdf = new SimpleDateFormat();
                          sdf.parset(String) ---> Date
          param: source代表的是配置文件中 日期字符串<value>2020-06-15</value>
  
          returun: 当把转换好的Date作为convert方法的返回值后,Spring自动的为birthday属性进行注入(赋值)
       */
      @Override
      public Date convert(String source) {
          Date date = null;
          try {
              SimpleDateFormat sdf = new SimpleDateFormat("yyyy--MM--dd");
              date = sdf.parse(source);
          } catch (ParseException e) {
              e.printStackTrace();
          }
          return date;
      }
  }
  ~~~

  

- 在Spring的配置文件中进行配置

  - MyDateConverter对象创建出来

    ~~~xml
     <bean id="myDateConverter" class="priv.yangkuncheng.converter.MyDateConverter"/>
    ~~~

  - 类型转换器的注册

    ~~~xml
    目的:告知Spring框架，我们所创建的MyDateConverter是一个类型转换器
    <!--    用于注册类型转换器-->
    <bean id="conversionservice" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <ref bean="myDateConverter"/>
            </set>
        </property>
    </bean>
    ~~~

    

## 3.Spring框架内置日期转换器

~~~markdown
日期格式: 2020/05/01 (2020-05-01)
~~~



# 第十三章、后置处理Bean

~~~markdown
BeanPostProcessor作用:对Spring工厂所创建的对象,进行在加工。

AOP底层实现:

注意:BeanPostProcessor接口
			xxxx(){
			
			}
~~~

- 后置处理Bean的运行原理分析

  ![image-20200615135707460](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200615135707460.png)

~~~markdown
程序员实现BeanPostProcessor规定接口中的方法:

Objict postProcessBeforeInitiallization(Object bean String beanName)
作用:Spring创建完对象,并进行注入后,可以运行Before方法进行加工
获得Spring创建好的对象:通过方法的参数
最终通过返回值交给Spring框架

Object postProcessAfterInitiallization(Object bean String beanName)
作用:Spring执行完对象的初始化操作后,可以运行After方法进行加工
获得Spring创建好的对象:通过方法的参数
最终通过返回值交给Spring框架

实战中:
很少处理Spring的初始化操作:没有必要区分Before After。只需要实现其中一个After方法即可
注意:
	postProcessBeforeInitiallization
	return bean对象
~~~

- BeanPostProcessor的开发步骤

  1. 类实现BeanPostProcessor接口

  ~~~java
  public class MyBeanPostProcessor implements BeanPostProcessor {
  
      @Override
      public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
          return bean;
      }
  
      @Override
      public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
  		
          //判断是否是Categroy类型
          if (bean instanceof Categroy) {
              Categroy categroy = (Categroy) bean;
              categroy.setName("xiaoyang");
          }
          return bean;
      }
  }
  ~~~

   2. Spring的配置文件中进行配置

      ~~~xml
      <bean in="myBeanPostProcessor" class="xxx.MyBeanPostProcessor"/>
      ~~~

3. BeanPostProcessor细节

    ~~~markdown
BeanPostProcessor会对Spring工厂中所有创建的对象进行加工
    ~~~



# ----------------------------

------



# **Sting之AOP编程**

# 第一章、静态代理设计模式

## 1.为什么需要代理设计模式

### 1.1问题

- 在javaEE分层开发中，那个层次对于我们来讲最重要

  ~~~markdown
  DAO ---> Service --> Controller
  
  JavaEE分层开发中，最为重要的是Service层
  ~~~

- Service层包含了哪些代码？

  ~~~markdown
  Service层中 = 核心功能(几十行 上百行)+额外功能(附加功能)
   1. 核心功能
   	业务运算
   	DAO调用
   2. 额外功能
   	1. 不属于讹误
   	2. 可有可无
   	3. 代码量小少
   	
   	事物、日志、性能...
  
  ~~~

- 额外功能书写在Service层中好不好

~~~markdown
Service层的调用者的角度(Controller):需要在Service层书写额外功能.
	软件设计者:Service层中不需要额外功能
~~~



- 现实生活中的解决方式

![image-20200616113857197](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200616113857197.png)

## 2.代理设计模式

### 2.1 概念

~~~markdwon
通过代理类,为原始类(目标)增加额外的功能
好处: 利于原始类(目标)的维护
~~~

### 2.2名词解释

~~~markdown
 1. 目标类 原始类
 	指的是 业务类 (核心功能 --> 业务运算 DAO调用)
 2. 目标方法 原始方法
 	目标类(原始类)中的方法 就是目标方法(原始方法)
 3. 额外功能(附加功能)
 	日志,事务,性能
~~~

### 2.3代理开发的核心要素

~~~markdown
代理类 = 目标类(原始类) + 额外功能 + 原始类(目标类)实现相同的接口

房东 ---> public interface UserService{
			m1
			m2
}
		UserServiceImp implements UserService{
			m1 ---> 业务运算 DAO调用
            m2  
		}
		UserServiceProxy implements UserService{
			m1
			m2
		}
~~~

### 2.4编码

静态代理： 为每一个原始类，手工编写一个代理类(.java .class)

![image-20200616170946353](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200616170946353.png)

### 2.5静态代理存在的问题

~~~markdown
1. 静态类文件数量过多，不利于项目管理
   UserServiceImpl userServiceProxy
   OrderServiceImpl OrderServiceProxy
2. 额外功能维护性差
   代理类中 额外功能修改复杂(麻烦)
~~~



# 第二章、Spring动态代理开发

## 1.Spring动态代理的概念

~~~markd
概念:通过代理类为原始类(目标类)增加额外功能
好处:利于原始类(目标类)的维护
~~~

## 2.搭建开发环境

~~~xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-aop -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>5.2.7.RELEASE</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjrt -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
    <version>1.9.5</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.5</version>
</dependency>
~~~

## 3.Spring动态代理的开发步骤

### 3.1 创建原始对象(目标对象)

~~~java
public class UserServicelmpl implements UserService {
    public void register(User user) {
        System.out.println("UserServicelmpl.register");
    }

    public boolean login(String name, String password) {
        System.out.println("UserServicelmpl.login");
        return true;
    }
}
~~~

~~~xml
<bean id="userService" class="priv.yangkuncheng.proxy.UserServicelmpl"></bean>
~~~

### 3.2 额外功能

​	**MethodBeforeAdvice接口**

~~~markdown
额外的功能书写在接口的实现中,运行在原始方法执行之前运行额外功能.
~~~

~~~java
public class Before implements MethodBeforeAdvice {
    //作用：需要吧运行在原始方法执行之前运行的额外功能，书写在before方法中
    public void before(Method method, Object[] objects, Object o) throws Throwable {
        System.out.println("---- method before advice log ----");
    }
}
~~~

~~~xml
<bean id="before" class="priv.yangkuncheng.dynamic.Before"/>
~~~

### 3.3定义切入点

~~~markdown
切入点:额外功能加入的位置

目的:由程序员根据自己的需要，决定额外功能加入给那个原始方法
register
login

简单的测试:所有方法都作为切入点,都加入额外的功能。
~~~

~~~xml
<aop:config>
    <aop:pointcut id="pc" expression="execution(* *(..))"/>
</aop:config>
~~~

### 3.4 组装(2 3整合)

~~~xml
表达的含义:所有的方法 都加入before的额外功能
<aop:advisor advice-ref="before" pointcut-ref="pc"/>
~~~

### 3.5 调用

~~~java
 //目的:获得Spring工厂创建的动态代理对象，并进行调用
 ApplicationContext ctx = new ClassPathXmlApplicationContext("/applicationContext.xml");
 //注意:
 //		1.spring的工厂通过原始对象的id值获得的是代理对象
 //		2.获得代理对象后，可以通过声明接口类型，进行对象的存储

UserService userService = (UserService)ctx.getBean("userService");

userService.login("");
userService.register();
~~~

## 4.动态代理细节分析

### 1.Spring创建的动态代理在哪里？

~~~markdown
1. Spring框架在运行时，通过动态字节码技术，在JVM创建的，运行在JVM内部，等程序结束后，回合JVM一起消失

2. 什么叫动态字节码技术
	通过第三方动态字节码框架，在JVM中创建对应类的字节码，进而创建对象，当虚拟机结束，动态字节码跟着消失。
	
	结论:动态代理不需要定义类文件，都是JVM运行过程中动态创建的，所以不会造成静态代理，类文件数量过多，影响项目管理问题

~~~



![image-20200616184926863](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200616184926863.png)

### 2.动态代理编程简化代理的开发

~~~markdwon
在额外功能不改变的前提下，创建其他目标类(原始类)的代理对象时,只需要指定原始(目标)对象即可。
~~~

### 3. 动态代理额外功能的维护性大大增强



# 第三、Spring动态代理详解

## 1.额外功能的详解

- MethodBeforeAdvice分析

  ~~~java
  1. MethodBeforeAdvice接口作用:额外功能运行在原始方法执行之前,进行额外功能操作.
      
  public class Before1 implements MethodBeforeAdvice{
      /*
          作用:需要吧运行在原始方法执行之前运行的额外功能，书写在before方法中
  
          Method:额外功能所增加给的那个原始方法
          login方法
  
          register方法
  
          showOrder方法
  
          Object[]:额外所增加给的那个原始方法的参数.String name,String password
                                              User
          Object:  额外功能所增加给的那个原始对象 UserServiceImpl
                                             OrderServiceImpl
       */
      @Override
      public void before(Method method, Object[] objects, Object o) throws Throwable {
          System.out.println("---- new method before advice log ----");
      }
  }
  
  2. before方法的3个参数在实战中，该如何使用？
      before方法的参数，在实战中，会根据需要进行使用，不一定都会用到，也有可能都不用
  ~~~

  

- Methodinterceptor(方法拦截器)

  ~~~markdown
  Methodinterceptor接口:额外功能可以根据需要运行在原始方法执行 前、后、前后。
  ~~~

  ~~~java
  public class Arround implements MethodInterceptor {
      /*
          invoke方法的作用:额外功能书写在invoke
                         额外功能 原始方法之前
                                 原始方法之后
                                 原始方法执行之前 之后
         确定:原始方法怎么运行
  
         参数:MethodInvocation (Method): 额外功能所增加给的那个原始方法
          login
          register
          invocation.proceed() --> login运行
                                   register运行
          返回值:Object:原始方法的返回值
          Date covert(String name)
       */
      @Override
      public Object invoke(MethodInvocation methodInvocation) throws Throwable {
          System.out.println("--- 额外功能 log---");
          Object ret = methodInvocation.proceed();
          return  ret;
      }
  }
  ~~~

- 额外功能运行在原始方法执行之后

```java
@Override
public Object invoke(MethodInvocation methodInvocation) throws Throwable {
    Object ret = methodInvocation.proceed();
    System.out.println("--- 额外功能 log---");

    return  ret;
}
```

- 额外功能运行在原始方法执行之前和执行之后

~~~java
什么样的额外功能 运行在原始方法执行之前，之后都要添加？
    事务
@Override
public Object invoke(MethodInvocation methodInvocation) throws Throwable {

    System.out.println("--- 原始方法执行之前 ---");

    Object   ret = methodInvocation.proceed();

    System.out.println("--- 原始方法执行之后---");
    return  ret;
}
~~~

- 额外功能在原始方法抛出异常的时候

~~~java
@Override
public Object invoke(MethodInvocation methodInvocation) throws Throwable {

    Object ret = null;
    try {
        ret = methodInvocation.proceed();
    } catch (Throwable throwable) {
        System.out.println("--- 原始方法抛出异常 执行的额外功能 ---");
        throwable.printStackTrace();
    }
    return  ret;
}
}
~~~

- MethodInterceprot影响方法的返回值

```
原始方法的返回值,直接作为invoke方法的返回值返回,MethodInterceprot不会影响方法的返回值

MethodInterceprot影响方法的返回值
invoke方法的返回值，不要直接返回原始方法运行结果即可

@Override
public Object invoke(MethodInvocation methodInvocation) throws Throwable {
System.out.println("--- log---");
Object ret = methodInvocation.proceed();
return  false;
}
```

## 2.切入点详解

~~~xml
切入点决定额外功能加入位置(方法)

<aop:pointcut id="pc" expression="execution(* *(..))""/>
exection(* *(..)) ---> 匹配了所有方法 a b c

 1. execution()     切入点函数                                                  
 2. * *(..) 		切入点表达式                                                    
~~~

### 2.1切入点表达式

1. 方法切入点表达式

![image-20200617140320071](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200617140320071.png)



~~~markdown
 * *(..) -->所有方法
 
  * ---> 	修饰符 返回值
  * ---> 	方法名
  ()--->	参数表
  ..--->	对于参数没有要求(参数有没有,参数有几个都行,参数是什么类型都行)
~~~

- 定义login方法作为切入点

~~~markdown
 * login(..)
 
 # 定义register作为切入点
 * register(..)
~~~

- 定义login方法且login方法有两个字符串类型的参数作为切入点

~~~markdown
 * login(String,String)
 
 # 注意:非java.long包中的类型，必须要写全限定名
 * register(priv.yangkuncheng.proxy.User)
 
 #	..可以和具体的参数类型连用
 * login(String,..) -->login(String),login(String,String),login(String,priv.yangkuncheng.proxy.User)
 
~~~

- 精准方法切入点限定

  ~~~markdown
   修饰符 返回值 		包.类.方法(参数)
   	*				priv.yangkuncheng.proxy.UserServiceImpl.login(..)
   	*				priv.yangkuncheng.proxy.UserServiceImpl.login(String,String)
  ~~~

### 2.2 类切入点

~~~markdown
指定特定类作为切入点(额外功能加入的位置),自然这个类中的所有方法,都会加上对应的额外功能
~~~

- 语法1

  ~~~mark
   # 类中的所有方法加入了额外功能
   * priv.yangkuncheng.proxy.UserServiceImpl.*(..)
  ~~~

- 语法2

  ~~~markdown
  # 忽略包
  1. 类只存在一级包 priv.UserServiceImpl
  * *.UserServiceImpl.*(..)
  
  2. 类存在多级包 priv.yangkuncheng.proxy.UserServiceImpl
  * *..UsserServiceImpl.*(..)
  ~~~

  

### 2.3 包切入点表达式 实战

~~~mark
 指定包作为额外功能加入的位置，自然包中的所有类及其方法都会加入额外的功能
~~~

- 语法1

  ~~~markdown
   # 切入点包中所有的类 必须在proxy中，不能再proxy包的子包中
   * priv.yangkuncheng.proxy.*.*(..)
  ~~~

- 语法2

  ~~~markdown
   # 切入点当前包及其子包都生效
   * priv.yangkuncheng.proxy..*.*(..)
  ~~~

  

### 2.4 切入点函数

~~~markdown
切入点函数:用于执行切入点表达式
~~~

#### 1. execution

~~~markdown
 最为重要的切入点函数,功能最全
 执行 方法切入点表达式 类切入点表达式 包切入点表达式
 
 弊端：execution执行切入点表达式,书写麻烦
 	exectuion(* priv.yangkuncheng.proxy..*.*(..))
 	
 注意：其他的切入点函数 简化是execution书写复杂度，功能上完全一致	
~~~

#### 2.args

~~~markdown
 作用：主要用于函数(方法)参数的匹配
 
 切入点:方法参数必须得是2个字符串类型的参数
 
 execution(* *(String,String))
 
 args(String,Sting)
~~~

#### 3.within

~~~mark
 作用：主要用于进行类、包切入点表达式的匹配
 
 切入点：UserServiceImpl这个类
 
 execution(* *..UserServiceImpl.*(..))
 
 within(*..UserServicelmpl)
 
 execution(* priv.yangkuncheng.proxy..*.*(..))
 
 within(priv.yangkuncheng.proxy..*)
~~~

#### 4.@annotation

~~~xml
作用:为具有特殊注解的方法加入额外功能

<aop:pointcut id="pc" expression="@annotation(priv.yangkuncheng.Log)"/>
~~~

#### 5.切入点函数的逻辑运算

~~~markwodn
 值得是 整合多个切入点函数一起配合工作,进而完成更为复杂的需求
~~~

- and与操作

~~~markdown
案列:login 同时 参数 2个字符串

1. execution(* login(String,String))

2. execution(*login(..) and args(String,Sring))

注意:与操作不同用于同种类型的切入点函数

案例: register方法 和 login方法作为切入点

execution(* login(..)) and execution(* register(..))	//错误
execution(* login(..)) or execution(* register(..))		//正确	
~~~

- or或操作

~~~markdown
案例: register方法 和 login方法作为切入点

execution(* login(..)) or execution(* register(..))
~~~



# 第四、AOP编程

## 1.AOP概念

~~~markdown
AOP(Aspect Oriented Programing)  面向切面编程 = String动态代理开发
以切面为基本单位的程序开发,通过切面间的彼此协同,相互调用,完成程序的构建
切面 = 加入点 + 额外功能

OOP(Object Oriented Programing)  面向对象编程 Java
以对象为基本单位的程序开发，通过对象间的彼此协同,相互调用.完成程序的构建

POP(Producer Oriented Programing) 面向过程(方法，函数)编程 C
以过程为基本单位的程序开发，通过过程间的彼此协同，相互调用，完成程序的构建
~~~

~~~markdown
AOP的概念:
		本质就是Spring的动态代理开发，通过代理类为原始类增加额外功能。
		好处:利于原始类的维护

注意:AOP编程不可能取代OOP，OOP编程有意补充。
~~~

## 2.AOP编程的开发步骤

~~~markdown
 1. 原始对象
 2. 额外功能(MethodInterceptor)
 3. 切入点
 4. 组装切面(额外功能+切入点)	
~~~

## 3.切面的名词解释

~~~markdown
 切面 = 切入点+额外功能
 
 几何学
 	面 = 点 + 相同的性质
~~~

![image-20200618202800978](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200618202800978.png)

# 第五、AOP的底层实现原理

## 1.核心问题

~~~markdown
 1. AOP如何创建动态代理类(动态字节码技术)
 2. Spring工厂如何加工创建代理对象
 3. 通过原始对象的id值，获得的是代理对象
~~~

## 2.动态代理类的创建

### 2.1 JDK的动态代理

- Proxy.newProxyInstance方法参数详解

![image-20200620093949446](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200620093949446.png)

![image-20200620094017966](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200620094017966.png)

- 编码

~~~java
public class TestJDKProxy {
    public static void main(String[] args) {
        /*
            1. 借用类加载器 TestJDKProxy
                          UserServicelmpl
            2. JDK8.x前
            final UserService userService = new UserServicelmpl();
         */
        //创建原始对象
        UserService userService = new UserServicelmpl();

        //jdk动态代理
        InvocationHandler handler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println("-------proxy log ----------");
                Object ret = method.invoke(userService, args);
                return ret;
            }
        };
        UserService userServiceProxy = (UserService) Proxy.newProxyInstance(TestJDKProxy.class.getClassLoader(), userService.getClass().getInterfaces(), handler);
        userServiceProxy.login("yang", "123456");
        userServiceProxy.register(new User());

    }
}
~~~

### 2.2 CGlib的动态代理

~~~markdown
CGlib创建动态代理的原理:父子继承关系创建代理对象，原始类作为父类，代理类作为子类，这样既可以保证二者方法一致，同时在代理类中提供新的实现(额外功能+原始方法)
~~~

![image-20200620101007405](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200620101007405.png)

- 编码

  ~~~java
  public class TestCglib {
      public static void main(String[] args) {
          // 创建原始对象
          UserService userService = new UserService();
  
          Enhancer enhancer = new Enhancer();
  
          enhancer.setClassLoader(TestCglib.class.getClassLoader());
          enhancer.setSuperclass(userService.getClass());
  
          MethodInterceptor interceptor = new MethodInterceptor() {
              @Override
              public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                  System.out.println("--- cglib log ----");
                  Object invoke = method.invoke(userService, objects);
                  return invoke;
              }
          };
          enhancer.setCallback(interceptor);
  
          UserService userService1 = (UserService) enhancer.create();
  
          userService1.login("yang","1234");
          userService1.register(new User());
  
      }
  }
  ~~~

### 2.3 总结

~~~markdown
1. JDK动态代理 Proxy.newProxyInstance() 	通过接口创建代理的实现类
2. CGlib动态代理   Enhancer					通过继承父类创建的代理类
~~~



## 3.Spring工厂如何加工原始对象

- 思路分析
- ![image-20200620125950281](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200620125950281.png)

- 编码

  ~~~java
  public class ProxyBeamPostProcessor implements BeanPostProcessor {
  
  
      @Override
      public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
          return bean;
      }
  
      @Override
      public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
          InvocationHandler handler = new InvocationHandler() {
              @Override
              public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                  System.out.println("new log ---");
                  Object ret = method.invoke(bean, args);
                  return ret;
              }
          };
  
          return Proxy.newProxyInstance(ProxyBeamPostProcessor.class.getClassLoader(),bean.getClass().getInterfaces(),handler);
      }
  }
  ~~~

- 配置文件

     ~~~xml
     <bean id="userService" class="priv.yangkuncheng.factory.UserServicelmpl"/>
     <!--        1, 实现benanPostProcessor 进行加工-->
     <!--        2, 配置文件中对BeanPostProcessor进行配置-->
     <bean id="proxyBeanPostProcessor" class="priv.yangkuncheng.factory.ProxyBeamPostProcessor"/>
     ~~~

# 第六章、基于注解的AOP编程

## 1.基于注解的AOP编程开发步骤

### 1.原始对象

### 2.额外功能

### 3.切入点

### 4.组装切面

~~~java
	# 通过切面类 定义了 额外功能 @Around
	  		   定义了 切入点   @Around("execution(* login(..))")
	  		   @Aspect 切面类
	  		   
@Aspect
public class MyAspect {
    @Around("execution(* login(..))")
    public Object arround(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("----log ----");

        Object ret = joinPoint.proceed();
        return ret;
    }
}	  		   
~~~

~~~xml
<bean id="userService" class="priv.yangkuncheng.aspect.UserServicelmpl"/>
<bean id="arround" class="priv.yangkuncheng.aspect.MyAspect"/>

<!--        告知Spring基于注解进行AOP编程-->
<aop:aspectj-autoproxy/>
~~~



## 2.细节

### 1.切入点复用

~~~java
切入点复用:在切面类中定义一个函数 上面@Pointcut注解 通过这种方式，定义切入点表达式，后续更加有利于切入点复用。
@Aspect
public class MyAspect {
    @Pointcut("execution(* login(..))")
    public void myPointuct() {}
    
    @Around(value = "myPointuct()")
    public Object arround(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("----log ----");

        Object ret = joinPoint.proceed();
        return ret;
    }
}    
~~~

### 2.动态代理的创建方式

~~~markdown
AOP底层实现 2种代创建方式
 1. JDK 通过实现接口 做新的实现类方式 创建代理对象
 2. Cglib通过继承父类 做新的子类	  创建代理对象
 
 默认情况 AOP编程 底层应用JDK动态代理创建方式
 如果切换Cglib
 	1. 基于注解AOP开发
 		<aop:aspectj-autoproxy proxy-target-class="true"/>
 	2. 传统的AOP开发
    	<aop:config proxy-target-class="true">
    	</aop>
~~~

# 第八章、AOP阶段知识总结

![image-20200623113959208](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200623113959208.png)



------

# ---------------------------

# Spring - 持久层整合

# 第一章、持久层整合

## 1.Spring框架为什么要与持久层技术进行整合？

~~~markdown
1. JavaEE开发需要持久层进行数据库的访问操作
2. JDBC Hibernate MyBatis进行持久开发过程存在大量的代码冗余
3. Spring基于模板设计模式对于上述的持久层技术进行了封装
~~~

## 2.Spring可以与那些持久层技术进行整合？

~~~markdown
1. JDBC
		|- JDBCTemlate
2. Hibernate (JPA)
		|- HibernateTemplate
3. MyBatis
		|- SqlSessionFactoryBean MapperScannerConfigure
~~~



# 第二章、Spring与MyBatis整合

## 1.MyBatis开发步骤的回顾

~~~markdown
1. 实体
2. 实体别名
3. 表
4. 创建DAO接口
5. 实现Mapper文件
6. 注册Mapper文件
7. MybatisAPI调用
~~~

## 2.Mybatis在开发过程中存在问题

~~~markdown
配置繁琐 代码冗余

1. 实体
2. 实体别名 			配置繁琐
3. 表
4. 创建DAO接口
5. 实现Mapper文件
6. 注册Mapper文件		配置繁琐
7. MybatisAPI调用      代码冗余
~~~

## 3.Spring与Mybatis整合思路分析

![image-20200623152137244](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200623152137244.png)

## 4.Spring与Mybatis整合的开发步骤

- 配置文件(ApplicationContext.xml)进行相关配置

  ~~~xml
  #配置 是需要配置一次
  <bean id="dataSource" class=" "/>
  
  <!-- 创建SqlSessionFactory -->
  <bean id="ssfb" class="SqlSessionFactoryBean">
      <property name="dataSource" ref=" "/>
      <property name="typeAliasesPackage">
          指定 实体类所在的包 priv.yangkuncheng.entity User
          										  Product
      </property>
      <property name="mapperLocations">
          	指定 配置文件(映射文件)的路径 还有通用配置
          	priv.yangkuncheng.mapper/*Mapper.xml
      </property>
  </bean>
  <!-- DAO接口的实现
  	session ---> session.getMapper() --- xxxDAO实现类对象
  	XXXDAO	---> XXXDAO
  -->
  <bean id="scanner" class="MapperScannerConfigure">
      <property name="sqlSessionFactoryBeanName" value="ssfb"/>
      <property name="basePacakge">
          指定 DAO接口放置的包 priv.yangkuncheng.dao
      </property>
  </bean>    
  ~~~

- 编码

     ~~~markdown
# 实战经常根据需求 写的代码
1. 实体
2. 表
3. 创建DAO接口
4. 实现Mapper文件
     ~~~

## 5.Spring与Mybatis整合编码

- 搭建开发环境 (jar)

     ~~~xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.7.RELEASE</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>2.0.5</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.22</version>
</dependency>

<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.20</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.5</version>
</dependency>
     ~~~

- Spring配置文件的配置

  ~~~xml
  <!--  连接池-->
  <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
      <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
      <property name="url" value="jdbc:mysql://localhost:3306/book ?serverTimezone=UTC"/>
      <property name="username" value="root"/>
      <property name="password" value=""/>
  </bean>
  
  <!--    创建SqlSessionFactory SqlSessionFactoryBean-->
  <bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
      <property name="dataSource" ref="dataSource"></property>
      <property name="typeAliasesPackage" value="priv.yangkuncheng.entity"></property>
      <property name="mapperLocations">
          <list>
              <value>classpath:priv.yangkuncheng.mapper/*Mapper.xml</value>
          </list>
      </property>
  </bean>
  
  <!--    创建DAO对象 MapperScannerConfigure&ndash;&gt;-->
  
  <bean id="scanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
      <property name="sqlSessionFactoryBeanName" value="sqlSessionFactoryBeam"></property>
      <property name="basePackage" value="priv.yangkuncheng.dao"></property>
  </bean>
  ~~~

- 编码

~~~markdown
1. 实体
2. 表
3. 创建DAO接口
4. 实现Mapper文件
~~~

## 6.Spring与Mybatis整合细节

- 问题: Spring与Mybatis整合后，为什么DAO不提交事物，但是数据能够插入数据库中？

~~~markdown
Connection --> tx
Mybatis(Connection)

本质上控制连接对象(Connection) ---> 连接池(DataSource)
1. Mybatis提供的连接池对象 ---> 创建Connection
	Connection.setAutoCommit(false) 手工的控制了事物 ，操作完成后， 手工提交
2. Druid(C3P0 DBCP)作为连接池 --->创建Connection
	Connection.setAutoCommit(true)true默认值 保持自动控制事物 ，一条sql自动提交
答案:因为Spring与Mybatis整合时，引入了外部连接池对象，保持自动的事物提交这个机制(Connection.setAutoCommit(true)),不需要手工进行事物的操作，也能进行事物的提交

注意：未来实战中，还会手工控制事物(多条sql一起成功，一起失败)，后续Spring通过事物控制解决这个问题
~~~



# 第三章、Spring的事务处理

## 1.什么是事务？

~~~markdown
保证业务操作完成性的一种数据库机制

事物的四个特点 ： A C I D
1. A 原子性
2. C 一致性
3. I 隔离性
4. D 持久性
~~~

## 2.如何控制事务？

~~~markdown
JDBC:
	Connection.setAutoCommit(false);
	Connection.commit();
	Connection.rollback();
Mybatis:
	Mybatis自动开启事务
	
	sqlSession(Connection).commit();
	sqlSession(Connection).rollback();
	
结论:控制事务的底层 都是Connection对象完成。
~~~

## 3.Spring控制事务的开发

~~~markdown
Spring是通过AOp的方式进行事物开发
~~~

### 1.原始对象

~~~markdown
public class XXXUserServiceImpl{
	private xxxDAO xxxDAO
	set get
	
	1. 原始对象 -->原始方法 --> 核心功能 (业务处理+DAO调用）
	2. DAO作为Service的成员变量，依赖注入的方式进行赋值
}
~~~

### 2.额外功能

~~~markdown
 1. org.springframework.jdbc.datasource.DataSourceTransactionManager
 2. 注入DataSource
 1. MethodInterceptor
 public Object invoke(MethodInvocation invocation){
 	try{
 		Connection.setAutoCommit(false);
 		Object ret = invocation.proceed();
 		Connection.commit();
 	}catch(Exception e){
 	Connection.rollback();
 	}
 	return ret;
 }
 2. @Aspect
 	@Around
~~~

### 3.切入点

~~~markdown
@Transactional
事务的额外功能加入给那些业务方法

1. 类上：类中所有的方法都会加入事务
2. 方法上：这个方法会加入事务
~~~

### 4.组装切面

~~~markdown
1. 切入点
2. 额外功能

<tx:annotation-driven transaction-manager=" "/>
~~~

