---
layout: post
title: windows上安装redis服务
date: 2018-11-12 13:20:20
categories: redis
tags: redis
author: Epoch
---

* content
{:toc}

windows环境安装案例

下载Redis-x64-3.2.100.msi安装，下一步，直到成功完成。

下载地址：https://github.com/MicrosoftArchive/redis/releases

A.打开windows服务，停止redis,打开cmd,在命令执行

如下

![输入图片说明](https://images.gitee.com/uploads/images/2018/1009/093537_a0f91776_626204.png "屏幕截图.png")

sc delete redis

删除当前的redis服务，且确保在windows 服务中也不可见。这里不删除会导致下面重新注册服务报错。如果忘记删了，执行sc delete redis后再删除也可以

B.在redis配置目录中，找到redis.window.conf配置文件，修改如下内容

找到bind 127.0.0.1去掉加注释#

port 6381  改造为自己下要的端口号，默认6379

requirepass 123456 设置密码，必须设置

# maxmemory <bytes>

maxmemory 1024000000  设置缓存

以上基本上满足需求了，接下来注册服务，找到redis目录，打开cmd，执行如下

redis-server.exe --service-install redis.windows.conf

会发现有如下

![输入图片说明](https://images.gitee.com/uploads/images/2018/0921/102856_312e1326_626204.png "屏幕截图.png")

说明重新注册成功了，在windows服务中可以找到新注册的redis，并且启动就可以使用了
