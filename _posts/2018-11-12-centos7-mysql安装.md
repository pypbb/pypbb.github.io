---
layout: post
title: centos7上安装mysql5.7版本
date: 2018-11-12 13:20:20
categories: mysql学习笔记
tags: mysql centos7
author: Epoch
---

* content
{:toc}

**注意：因mysql8.0和之后版本的目前使用范围并不大，当前仅介绍5.7版本的安装。
当前只针对centos7或者redhat7以上的版本进行介绍安装，其他版本暂不介绍。**

如下只介绍rpm安装方式(5.7版本任意版本安装都是一样的)

### **1.下载需要的文件**


官方下载mysql的rpm安装包，通常我们只需要下载4个包即可。

下载地址如下：

https://dev.mysql.com/downloads/mysql/

关于centos的安装包，下载：

7或者7版本以上 下载redhat7 X64或者32

6版本下载redhat6 X64或者32

此下安装过程也可适用redhat

只需要下载mysql-5.7.20-1.el7.x86_64.rpm-bundle.tar) 即可，他包含了下面的所有文件。

mysql安装包有点多，在官方有一句话是这样说的：
在大多数情况下，你只需要安装MySQL-server和MySQL-client安装包就可以
安装上一个标准功能的MySQL。对于一个标准安装来说，其他的安装包不是必需的。
所以我们不必头疼。

而client和server又依赖于一些其他包,所以我们只需要4个包即可。

mysql-community-client-5.7.20-1.el7.x86_64.rpm

mysql-community-common-5.7.20-1.el7.x86_64.rpm

mysql-community-libs-5.7.20-1.el7.x86_64.rpm

mysql-community-server-5.7.20-1.el7.x86_64.rpm

### **2.安装**

当前操作在root用户下

**1).安装之前的卸载**

rpm -qa|grep mariadb
此命令是查询之前版本的mysql
卸载命令
rpm -e --nodeps mariadb-libs-5.5.41-2.el7_0.x86_64
检测自带的mysql以及rpm包
rpm -qa | grep -i mysql 

yum -y remove mysql 移除

**2).安装**

虽然mysql有如此之多的安装包，但是我们仅需要安装我们需要的就可以了

再新建之前我们需要先创建用户组
groupadd mysql 
useradd -r -g mysql mysql 

按照步骤安装如下即可。不要跨越步骤，会导致依赖失败。

rpm -ivh mysql-community-common-5.7.20-1.el7.x86_64.rpm

rpm -ivh mysql-community-libs-5.7.20-1.el7.x86_64.rpm

rpm -ivh mysql-community-client-5.7.20-1.el7.x86_64.rpm 

rpm -ivh mysql-community-server-5.7.20-1.el7.x86_64.rpm 

注意：有可能会出现如下类似错误

[root@localhost upload]# rpm -ivh mysql-community-server-5.7.20-1.el7.x86_64.rpm
error: Failed dependencies:

libaio.so.1()(64bit) is needed by MySQL-server-5.5.25a-1.rhel5.x86_64

libaio.so.1(LIBAIO_0.1)(64bit) is needed by MySQL-server-5.5.25a-1.rhel5.x86_64

libaio.so.1(LIBAIO_0.4)(64bit) is needed by MySQL-server-5.5.25a-1.rhel5.x86_64

这是因为环境缺失libaio库导致的，使用yum命令可以安装

yum install libaio，其他问题类似这样处理。

至此mysql安装成功

### **3.mysql设置密码和远程访问**

第一次进入是不知道mysq密码的，可以在var/log/mysqld.log找到mysql的日志，来找到mysql默认设置的初始密码

搜索关键字generated其中有类似：A temporary  password is generated for root@localhost: 密码

注意：会出现日志为空的现象，这时候需要启动一下mysql就可以看到日志了，启动命令：systemctl start mysqld.service

mysql -u root -p 进入后需要设置密码，密码可能会设置失败，因为复杂度安全度太低

set password=password("Password@123");

mysql授予远程访问权限，类似如下，其中的 ON *.* TO 是授予任何主机访问，假若我们只针对具体某类IP也可以进行设置

GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'Password@123' WITH GRANT OPTION;

GRANT ALL PRIVILEGES ON *.* TO 'mysql'@'%' IDENTIFIED BY 'Password@123' WITH GRANT OPTION;

授予后需要刷新一下权限才能使用，如下

FLUSH PRIVILEGES;

### **4.centos7的mysql启动等命令**

启动mysql服务
systemctl start mysqld.service

停止mysql服务
systemctl stop mysqld.service

重启mysql服务
systemctl restart mysqld.service

查看mysql服务当前状态
systemctl status mysqld.service

设置mysql服务开机自启动
systemctl enable mysqld.service

停止mysql服务开机自启动
systemctl disable mysqld.service

### **5.mysql的大小写问题**

A：如果不设置mysql的配置文件，mysql默认是区分大小写的，为了不区分大小写，需要设置如下:

编辑MySQL安装目录下的my.ini文件，默认文件路径是/etc/my.cnf，使用vi或者vim命令在[mysqld]节下 

添加 lower_case_table_names=1(备注：为0时大小写敏感，为1时大小写不敏感)，可以实现MySql按照建表Sql语句的大小写状态来定义表名。 

B：修改默认端口号在[mysqld]下 增加 port=3506 为 端口号，然后重启

### **6.防火墙端口号开放**

如需对外要提供本端口号开放，要做俩个步骤

1.管理平台将此端口号设为出口

2.系统需要将此端口开放，因为centos的防火墙的问题

不建议直接关闭防火墙

操作如下：

查看端口号是否开放：firewall-cmd --query-port=3306/tcp

添加指定需要开放的端口：
firewall-cmd --add-port=3306/tcp --permanent

备注：--permanent表示永久生效，没有此参数重启后失效

重载入添加的端口：

firewall-cmd --reload

查询指定端口是否开启成功：

firewall-cmd --query-port=3306/tcp

移除指定端口：

firewall-cmd --permanent --remove-port=3306/tcp

