# 一、JAVA基础

## 1.JDK 和JRE有什么区别？

- jre ，java开发工具包，提供了java的开发环境和运行环境

- jre， java运行环境 为java的运行提供了所需环境

## 2. == 和 equals区别是什么？

- ==解读
  - 基本类型 比较的值是否相同
  - 引用类型 比较的是引用是否相同

- equals解读
  - equals本质上就是==，只不过String和Integer等重写了equals方法，把它变成了值比较
  - ==对于基本类型来说是指比较，对于引用类型来说是比较的引用，而quals默认的情况下是引用比较，只是很多类重写了quals方法，比如String、Integer等把它变成了值比较，所以一般情况下equal比较的是值是否相等

## 3.final在java中有什么作用？

- final修饰的类叫做最终类，该类不能被继承
- final修饰的方法不能被重写
- final修饰的变量叫常量 ，常量必须初始化，初始化就不能改变值

## 4.String类型属于基本类型吗？

- String类型不是基本类型，String属于对象，常见的基本类型有 int，fload，double，bool，char，short，long

## 5.java操作字符串都有哪些类，他们之间有什么区别？

- Stringbuffer，Stringbuilder，String

- String和Stringbuffer、Stringbuilder的区别在于String是不可变对象，每次操作都会产生新的对象，然后指针指向新的String对象，而Stringbuffer，Stringbuilder可以在原有对象进行操作，所以在经常改变字符串内容，最好不要使用String

- Stringbuffer、Stringbuider的区别是Stringbuffer是线程安全的，Stringbuilder线程是不安全的，但是Stringbuider运行效率要高于Stringbuffer，所以在单线程这里推荐使用Stringbuilder，多线程环境下推荐使用Stringbuffer

## 6.String str = “i” 和 String str = new String（“i“）一样吗？

- 不一样，是内存分配的不一样，第一种是分配到常量池中，而new的是要被分配到堆中

## 7.如何将字符串进行反转？

- 使用StringBuilde或者StringBuffer中的reverse()方法

  ~~~java
  StringBuffer x = new StringBuffer("abcdefg");
  System.out.println("x = " + x);
  x.reverse();
  System.out.println("x = " + x);
  ~~~

## 8.String中的常用方法有哪些？

- indexof() : 返回值指定索引
- charAt() :返回指定字符串的索引处
- length() : 返回字符串长度
- equals()：字符串比较
- substring() :截取字符串

## 9.抽象类必须要有抽象方法吗？

- 不需要 抽象类不一定得有抽象方法

## 10.普通类和抽象类有什么区别？

- 普通类不能包含抽象方法，抽象类可以包含抽象方法
- 抽象类不能直接实例化，普通类可以直接实例化

## 11.抽象类可以用final修饰吗？

- 不能，定义抽象类就是让其它类继承，如果final修饰了就不能继承，会出现矛盾，编译器会报错

## 12接口和抽象类有什么区别？

- 实现：抽象类的子类用extends来继承，接口必须要用inmplements来实现接口
- 构造函数，抽象类可以有构造函数，接口不能
- main方法，抽象类可以有main方法，接口不行
- 实现数量，类可以实现多个接口，但是只能继承一个抽象类
- 修饰符：接口默认使用public，抽象类可以使用任何修饰符

## 15.java中io分为几种？

- 按功能来分类 输入流(input) 输出流(output)

- 按类型来分： 字节流和字符流
- 字节流和字符流的区别是：字节流是8位传输以单位输入输出数据，字符流是按照16位传输字符为单位输入输出数据

## 16.BIO、NIO、AIO有什么区别

- BIO、同步阻塞IO，就是我们平常用的传统IO，它的特点就是简单使用方便，并发处理能力低
- NIO、New IO同步非阻塞IO，是传统的IO升级，客户端和服务端通过通道通讯，实现乐多路复用
- AIO、实现乐异步非阻塞IO，异步IO的操作基于时间和回调机制

## 17.Files的常用方法有哪些？

- File.write（） 写入
- File.Read（） 读取
- File.size ()  查看文件数量
- File.move 移动文件
- File.copy 赋值文件
- File.delete(): 删除文件

# 二、容器

## 1.常用容器有哪些？

- Hashset、List、ArryLis、Haspmap、map、vetror、Set、Hashmap、Treemap

![image-20200705202135425](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200705202135425.png)



## 2.List、Set、Map之间有什么区别？

![image-20200705202357419](C:\Users\15371\AppData\Roaming\Typora\typora-user-images\image-20200705202357419.png)

## 3.HashMap和Hashtable有什么区别？

- HashMap去掉了Hashtable的contains方法，但是加上containsValue()和containsKey()方法
- HashTable是同步，HashMap是非同步，效率要比hashtable要高

- hashMap允许空键值，hashtable不允许

## 4.如果决定使用HashMap还是TreeMap？

- 对于插入、删除定位元素使用HashMap对于查找有序的key使用TreeMap，基于你的collection的大小，也许向HashMap添加元素更快

## 5.说一下HashMap的底层实现原理？

- HashMap概述、HashMap是基于哈希表的Map接口的非同步实现，此实现提供了所有可选的映射操作，并且必须使用null值和null键，此类不保证映射顺序，特别是它不保证该顺序恒久衰变

- HashMap的数据结构，在java遇见中，最基本的结构就是两种，数组加引用指针，所有数据结构底层都可以用这两个基本结构来构造，HashMap也不列外，HashMap底层实现上是一个数组加链表 的结合体
- 当我们往HashMap放入元素，首先根据key的hashcode重新计算hash值，根据hash值得到这个元素在数组中的下标，如果该数组已经存放了其它元素，那么在这个位置上的元素将链表形式存放，新加入的放在链表头，最先加入的放在链表尾，如果数组中该位置没有元素就直接将元素放在该位置上

注意：Jdk1.8中对HashMap的实现做了优化，当链表中的节点数据超过了八个以后，该链表会转换为红黑树来提高查询效率，从原来的O在（n）到O(logn)

## 6.说一下HashSet的实现原理

- HashSet的底层是由HashMap来实现
- HashSet的值存放于HashMap的key上
- HashMap的value统一为PRESENT

## 7.ArrayList和LinkedList的区别是什么?

最明显的区别是ArrayList底层的数据结构是数组，支持随机访问，而LinkedList底层数据结构是双向循环链表，不支持随机访问，使用下标访问一个元素，ArrayList的时间复杂度是O(1)，而LinkedList是O(n)

## 8.如何实现数组和List之间的转换？

- List转换数组使用toArray方法
- 数组转换为List：调用Array的asList方法

## 9.ArrayList和vector的区别是什么？

- verctor是同步的，而ArrayList不是，然而，如果你寻求在迭代的时候对列表进行变更
- ArrayList比Vector，因为有同步，不会过载
- ArrayList更加通用，因为我们可以使用Collection工具类轻易获取同步列表和只读列表

## 10.Array和ArrayList有什么区别？

- Array可以容纳基本类型和对象，ArrayList只能容纳对象
- Array是指定大小，而ArrayList大小是动态
- Array没有提供ArrayList那么多功能，比如addAll，removeAll和ieerator等

## 11.在Queue中poll（）和remove()有什么区别？

- poll和remove()都是从队列中取出元素，但是poll（）在获取元素失败会返回空，remove失败会抛出异常

## 12.那些集合类是线程安全的？

- vector 但是效率低
- statck 堆栈类，先进后出
- hashtable 就比hashmap多了个线程安全
- enumeration 枚举 相当于迭代器

## 13.迭代器Iterator是什么？

- 迭代器是一种设计模式 它是一个对象，它可以遍历并选择序列中的对象，而开发人员不需要了解该序列的底层结构，迭代器比较轻量级创建它的代价较少