# 			MySQL数据库

# 一、MySQL初识及安装

## 1.数据库的分类

- RDBMS 关系型数据库
  - Oracle
  - MySQL
  - MSSQL
  - PG
- NoSQL 非关系型数据库
  - MongDB
  - ES
  - Redis
- 云数据库
  - RDS
  - PolarDB
  - TDSQL

- NewSQL
  - TiDB

## 2.Oracle和MySQL企业版本选择

- 5.6    5.6.36 | 5.6.38 | 5.6.40 | 5.6.46	（GA时间 6-12月）

- 5.7(目前用的企业最多) 5.7.20 | 5.7.22 | 5.7.24 | 5.7.24 | 5.7.26 | 5.7.28	

- 8.0     8.0.11 | 8.0.17 | 8.0/18

- **查看MySQL的版本**

- select version();

- 目前安装的版本是8.0.20

  

C : 社区版  开源

E : 企业版  收费

MySQL下载地址

https://downloads.mysql.com/archives/community/

- 安装方式
  - 通用二进制版 	解压即用
  - rpm，yum版     下载rpm包或者配置yum源码  （自己研究）
  - 源码包                编译安装 非常慢                         （自己研究）

- 基础命令

  ~~~shell
  启动数据库服务			格式: net  start  数据库名
  关闭数据库服务			格式: net  stop  数据库名
  登录数据库			 mysql -u root -p
  ~~~

# 二、基础SQL命令

## 1.基本命令

~~~sql
-- 显示 数据库服务器上所有的数据库
show databases;
--创建某个数据库
create database 数据库名称;
--删除数据库
drop database  数据库名称;
~~~

## 2.进入数据库

~~~sql
--进入数据库
use 数据库名称;
--查询数据库有多少表
show tables;
--查询表的结构
desc 表名;
~~~

## 3.增加表以及删除表

~~~sql
--创建表
create table 表名(
	name char(4),			
	age  int(20)			
	);
	
--删除表
drop table 表名;
~~~

## 4.修改表

~~~sql
--修改表

--修改数据表名
rename table 原表名 to 新表名;
示例 rename table a1 to a2;

--添加列(属性)
alter table 表名 add 属性名 数据类型;
示例 alter table a1 add hello int;

--删除列(属性)
alter table 表名 drop 属性名;
示例 alter table a1 drop hello;

--修改表中的字段
alter table 表名 modify 字段名 新的数据类型;
示例 alter table a1 modify hello varchar;

--修改字段的字段名
alter table 表名 change 原字段名 新字段名 数据类型;
示例 alter table a1 change hello world varchar;

--插入值
insert into 表名 values(值);
示例 insert into a1 values('a','b');
~~~

## 5.查询表

~~~sql
select 查询列表 from 表名
select * from a1;
--特点：
	1.查询列表可以是 表中的字段 常量值 表达式 函数
	2.查询的结构是一个虚拟的表格

--细节：
	select `name` from a1;
	``代表不是关键字 
	
--查询常量值:
	-- 查询整形
	select 100;
	-- 查询字符
	select 'zhangsan';
	
--查询表达式	
	select 100*20;

--查询函数
	select version();
~~~

## 6.其它

### 1.起别名:

~~~sql
	as
-- 1.便于理解
-- 2.如果要查询的字段有重名，使用别名可以区分	
--方式一：
select last_name as 姓 from employees;	
--方式二:
select last_name 姓 from employees;
~~~

### 2.去重:

~~~sql
	distinct
select distinct last_name from employees;
~~~

## 3.连接字符串、拼接

~~~sql
concat();
select concat('a','b','c');
~~~

### 4.转意

~~~sql
escape 
select
	last_name
from
	employees
where
	last_name like '_$_%'escape '$';
	
~~~



# 三、进阶语法

## 1.查询进阶

~~~sql
-- 条件查询
语法：
select			
		查询列表		3
from
		表名			 1 执行顺序
where
		筛选条件;		2	  	
~~~

### 1.1按条件表达式查询

~~~sql
--	1.按条件表达式筛选
	简单条件运算符 > < = <> =><=
	
-- 示例:按条件表达式筛选
select
		id
from
	 employees
where
	  id>1;			-- 筛选id大于为1
~~~

### 1.2按逻辑表达式查询

~~~sql
--	2.按逻辑表达式筛选
	逻辑运算符 && || ! and or not
	
	&&和and:两个条件都为true，结果为true，反之为false
	||或or :只要有一个条件为true，结果为true，反之为false
	|或not :如果连接的条件本身为false，结果为true，反之为false
	
-- 示例:按逻辑表达式筛选
select department_id from department where department_id>70||department_id<90;
select department_id from department where department_id>30&&department_id<120;
~~~

### 1.3 模糊查询 

~~~sql
--  3.模糊查询	
	like
-- like的使用
select department_id from department where department_id like '%0%';	-- 搭配*使用 %的意思是任意多个字符
select department_name from department where department_name like '%小%';
select department_name from department where department_name like '_五%'; -- _为任意单个字符	

    between and
-- between and的使用
select department_id from department where department_id between 50 and 120;	--between and 只是为了简洁

	in
-- in的使用
select department_id department_name from department where department_id in(60,80);
-- 判断某字段的值是否属于in列表中的某一项	

	is null
-- is null的使用
select department_id department_name from department where department_name is  null;
-- =或<>不能用于判断null值
is null 或 not null 可以判断null值
~~~

### 1.4排序查询

~~~sql
asc| desc
语法：
	select 查询列表
	from	表
	where  筛选条件
	order by
	排序列表 asc | desc
示例：
	select *
	from    employees
    order by
    salary;	
特点：
	1.asc代表升序 desc代表降序
	2.如果不写 默认是升序
~~~

## 2.函数进阶

### 函数的概念、大意

~~~markdown
概念:类似于java方法 将一组逻辑语句封装在方法体中，对外暴露方法名
好处:1、隐藏了实现细节 2、提高代码的重用性
调用: select 函数名(参数) from 表;
特点: 
	1. 叫什么 (函数名)
	2. 干什么 (函数功能)
分类
	1、单行函数
	如 concat、length、ifnull等
	2、分组函数
	
	功能： 做统计使用，又称为统计函数、聚合函数、组函数
~~~

### 1.字符函数

~~~sql
-- length			获取参数的字节个数
select legth();
select length('aaa');	--3个字符

-- concat			拼接字符串
select concat();
select concat('aaa,bbb');

-- upper、lower		将字符串转换大写，小写
select upper();
select lower();
SELECT upper('aaa');
select lower('AAA');

-- substr、substring		截取字符
select substr();
select substring();
select substr('你好世界',2);	-- 截取第二个以后所有字符
select substring('你好世界',2,1);	-- 截取第二个字符一个

-- instr	返回字符串第一次出现的索引，如果找不到返回0
select instr();
select instr('heelloworldheleloworldhelllohelloworld','helloworld');

-- lpad 	左填充 rpad右填充
select lpad('张三',10,'*');
select rpad('张三',10,'*');

-- replace 替换字符串
select replace();
select replace('你好世界','世界','大家');
~~~

### 2.数学函数

~~~sql
-- round 四舍五入
select round(1.5);	-- 2

-- ceil 向上取整
select ceil(1.1);	-- 2

-- floor 向下取整
select floor(2.99);	-- 2

-- truncate 小数点保留
select truncate(2.99,1); -- 2.9

-- mod 取余
select mod(10,8);	-- 2
~~~

### 3.日期函数

~~~sql
-- now 返回当前系统时间日期
select now();

-- curdate 返回当前系统日期 不包含时间
select curdate();

-- str_to_date 将字符通过指定格式转行成日期
select str_to_date('2020-3-5','%Y-%c-%d')年月日;
~~~

### 4.其它函数

~~~sql
select version();	-- 数据库版本
select database();	-- 表所在的数据库
select user();		-- 用户
~~~

### 5.流程控制函数

~~~sql
-- 流程控制函数
-- if函数 ifelse效果
select if(10>5,'大','小');	--条件满足返回第二，不满住返回第三



-- 		case函数的使用第一种方式
-- case 要判断的字段或者表达式
-- when 常量1 then 要显示的值或者语句1;
-- when 常量2 then 要显示的值或者语句2;
-- else 要显示的值 或语句 ;
-- end
案列:
select salary 原始工资,employee_id,
case employee_id
when 1 then salary*1.1
when 2 then salary*1.2
when 3 then salary*1.3
else salary
end 新工资
from employee1;

-- case第二种使用方式
-- case
-- when 条件1 then 要显示的值1或语句1
-- when 条件2 then 要显示的值2或语句2
-- else 要显示的值或语句
-- end
案列:
select salary,
case
when salary>15000 then 'A'
when salary>10000 then 'B'
when salary>5000 then 'C'
else 'D'
end 工资级别
from employee1;
~~~

## 3.分组函数

### 1.分组函数的介绍

~~~sql
-- 功能: 用作统计使用，又称为聚合函数或统计函数或组函数

-- 分类:
-- sum 求和 ，avg 平均值 ，max最大值 ， min最小值 ， count 计算个数
-- 特点:
		1. sum、avg一般用于处理数值型
  		   max、min、count可以处理任何类型
		2. 以上分组函数都忽律null值
		3. 可以和distinct搭配实现去重的运算
		4. 一般使用count(*)用作统计行数
~~~

### 2.简单的使用

~~~sql
-- sum求和
select sum(salary) from employee1;

-- avg平均值
select avg(salary) from employee1;

-- max最大值
select max(salary) from employee1;

-- min最小值
select min(salary) from employee1;

-- count计算个数
select count(salary) from employee1;
~~~

~~~sql
分组查询
select  分组函数 , 列(要求出现在group by的后面)
from	 表
where	 筛选条件
group by 子句
注意:
	查询列表必须特殊，要求是分组函数和group by后面出现的字段
	
特点:
	1、分组查询中的筛选条件分为两类
			数据源			位置					关键字
分组前筛选	 原始表		 group by子句的前面	   where
分组后筛选	 分组后的结果集   group by子句的后面	 having

1.分组函数做条件肯定放在having子句中
2.能用分组前筛选的 就优先考虑使用分组前筛选

-- 筛选id为1
select avg(salary),employee_id
FROM employee1
WHERE employee_id LIKE '%1%'
GROUP BY employee_id;
~~~

## 4.连接查询

~~~sql
-- 连接查询
含义: 又称多表查询，当查询的字段来自于多个表时，就会用到连接查询
分类:	
	按年代分类:
			sql99标准
	按功能分类：
    		内连接:	等值连接 非等值连接 自连接
    		外连接:	左外连接 右外连接 全外连接
    		交叉连接
    				
    		
~~~



