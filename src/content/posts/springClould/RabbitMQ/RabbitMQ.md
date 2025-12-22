---
title: RabbitMQ
published: 2024-08-31
description: ''
image: '../cover.png'
tags: ["消息队列","微服务"]
category: '消息队列'
draft: false 
lang: ''
---
1.首先，在下载rabbitmq之前，我们要先下载erlang,下载地址为 https://www.erlang.org/downloads，注意erlang与rabbitmq的版本适配，下载完erlang后，配置erlang的环境变量，我们创建一个环境常量ERLANG_HOME,然后将erlang的bin的目录设为值，然后在path里面设置%ERLANG_HOME%\bin。

2.打开cmd,输入erl,检查是否配置成功

3.去rabbitmq官网下载rabbitmq,地址为[Releases · rabbitmq/rabbitmq-server · GitHub](https://github.com/rabbitmq/rabbitmq-server/releases)，如果安装后报错什么fail to find the version of Erlang的时候，就去删除那个之前erlang安装时各文件留下的cookie啊，还有注册编辑表那个erlang，这里是提供一下思路，具体可在网上找

4.下载完成后，进行环境配置，创建一个环境常量`RABBIT_HOME`，然后将sbin那个目录的值赋值给它，然后在path写一个`%RABBIT_HOME%\sbin`。

5.`rabbitmq-plugins enable rabbitmq_shovel rabbitmq_management`用这个命令在sbin中cmd下载中间件。

6.`rabbitmq-plugins.bat enable rabbitmq_management`运行这个，然后才能访问那个监控网站，也就是http://localhost:15672

7.如果显示配置要重新启动才生效，就用

```bash
# 关闭服务
net stop rabbitmq 
# 开启服务
net start rabbitmq 
```

查看rabbitmq的状态的指令：`rabbitmqctl status`

**5672 是RabbitMQ的端口号；15672 是RabbitMQ的Web页面的端口号**



8.关于启动时出现的bug,若出现`Error: unable to perform an operation on node--`这个错误

解决方案
1.首先检查自己rabbitmq版本与erlang版本是否对应
链接: 点击跳转查看版本信息

2.将C:\Users{用户名}.erlang.cookie 复制到 C:\Windows\System32\config\systemprofile 目录。
如果rabbitMQ是启动的就关闭，重启rabbitMQ服务，如果本来就是关的就直接启动

3.在sbin双击rabbitmq-server.bat文件，启动rabbitmq

4.出现
![img.png](img.png)
就表示成功了。