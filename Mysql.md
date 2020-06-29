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

# 二、SQL命令

## 1.刚入门的基本命令

~~~shell
# 显示 数据库服务器上所有的数据库
show databases;
#创建某个数据库
create database 数据库名称;
#删除数据库
drop database  数据库名称;
~~~

## 2.进入数据库增删改查创建

~~~shell
#进入数据库
use 数据库名称;
#查询数据库有多少表
show tables;
#查询表的结构
desc 表名;
#创建表
create table 表名(
	name char(4),			
	age  int(20)			
	);
~~~









