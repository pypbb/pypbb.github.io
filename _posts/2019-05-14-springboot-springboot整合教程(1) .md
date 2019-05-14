---
layout: post
title: springboot整合教程(1)
date: 2019-05-14 12:20:20
categories: springboot
tags: springboot
author: Epoch
---

* content
{:toc}

springboot整合教程(1)

关于springboot项目生成地址：

https://start.spring.io/#

借助该地址可以快速生成一个springboot的简单项目。

如下

![输入图片说明](http://pypbb.oss-cn-beijing.aliyuncs.com/093537_a0f91776_626204.png "屏幕截图.png")

sc delete redis

删除当前的redis服务，且确保在windows 服务中也不可见。这里不删除会导致下面重新注册服务报错。如果忘记删了，执行sc delete redis后再删除也可以

B.在redis配置目录中，找到redis.window.conf配置文件，修改如下内容

找到bind 127.0.0.1去掉加注释#

port 6381  改造为自己下要的端口号，默认6379

requirepass 123456 设置密码，必须设置

maxmemory 1024000000  设置缓存

以上基本上满足需求了，接下来注册服务，找到redis目录，打开cmd，执行如下

redis-server.exe --service-install redis.windows.conf

会发现有如下

![输入图片说明](http://pypbb.oss-cn-beijing.aliyuncs.com/102856_312e1326_626204.png "屏幕截图.png")

说明重新注册成功了，在windows服务中可以找到新注册的redis，并且启动就可以使用了
