# Git、Github学习

# **一、版本控制系统**

## 1.Git的好处

- 记录历史版本信息(记录每一次修改记录)
-  方便团队相互之间协作开发



## 2.常用的版本控制系统

- CVS/SVN：集中式版本控制系统
- Git :分布式版本控制系统



## 3.Git的工作原理

- 工作区 ：我们能看到的 并且用来写代码的区域

- 暂存区  :  临时存储用

- 历史区  ：生成历史版本

  

  **工作区 -> 暂存区 -> 历史区**



# 二、Git命令

## 1.Git全局配置

**第一次安装完成git后 在全局配置下环境基本信息**

~~~markdown
	$ git config -l 			查看配置信息
	$ git config --global -l	查看全局配置信息
	
	$ git config --global user.name '123'				配置用户名名称
	$ git config --global user.email '123@qq.com'		配置用户邮箱
	$ git config --global user.password '123'			配置用户密码
~~~



## 2.创建仓库完成版本控制

**创建本地git仓库**

~~~markdown
	$ git init		生成隐藏文件夹".git" 存放了暂存区和历史区以及其它信息" 
~~~



**在本地编写完成代码(工作区),把一些文件提交到暂存区**

~~~markdown
	$ git add hello		把hello文件提交到暂存区
	$ git add .			把当前仓库中所有最新修改的文件都提交到暂存区
	$ git add -A
	
	$ git status		查看当前文件状态(红色代表工作区，绿色代表暂存区，看不见东西证明所有修改的信息都已经提交到了历史区)
~~~



**把暂存区内容提交到历史区**

~~~markdown
	$ git commit -m "描述信息:本次提交内存的一个描述"
	
	查看历史版本信息 已提交的文件 历史记录
	$ git log
	$ git reflog	包括回滚的信息
	
	回滚历史版本
	$ git reset --hard d12345	回归一个历史版本 d12345
~~~



# 三、GitHub

## 1.创建仓库

- new 	repository -> 填写信息 ->Createing repository

  - public 公共仓库作为开源的项目

  - private 私有仓库作为内部团队协作管理的项目

    

## 2.把本地仓库信息提交到远程仓库



### 2.1建立本地仓库和远程仓库的链接

~~~markdown
	$ git remote -v						查看本地仓库和那些远程仓库保持链接
	
	$ git remote add origin 仓库地址	 本地仓库链接远程仓库，origin是连接名可更改
	
	$ git remote rm origin				删除指定的链接 origin是你删除的链接ID
~~~



### 2.2 本地仓库代码向远程仓库提交

~~~markdown
	$ git pull origin master			提交之前需拉取
	
	$ git push origin master			push提交入库
~~~



## 3.GitHub的克隆

- **真实项目开发流程**

  - 1. 组长先创建中央仓库(增加协作者)

  -    2.小组成员基于 $ git clone 把远程仓库以及默认的内容克隆岛本地一份
  -    3.每个组员写完自己程序后，基于”git add/git commit“把自己修改的内容存放到历史区，然后通过”git pull/git push“把本地信息和远程仓库信息保持同步即可

~~~markdown
	$ git clone	远程仓库地址		
~~~



# 四、Git分支

## 1.git的分支是什么

~~~markdown
顾名思义，分支就是从主线上分离出来进行另外的操作，而又不影响主线，主线又可以继续干它的事，是不是有点像线程，最后分支做完事后合并到主线上而分支的任务完成可以删掉了。这样是不是很方便，主线继续做它的事，分支用来解决临时需求，二者互不相干
~~~

## 2.git分支相关命令

~~~markdown
	$ git branch				查看当前仓库有几个分支
	
	$ git branch -r				查看所有分支 远程和本地
	
	$ git branch master1		创建一个分支名字为master1 可自定义更改名
	
	$ git checkout -b mastetr1  创建并切换到分支
	
	$ git checkout master1		切换分支到master1 
	
	$ git branch -d master1		删除分支master1
	
	$ git merge dev				合并分支
~~~



