---
title: docker的使用
published: 2024-10-22
description: ''
image: '../cover.png'
tags: ["docker"]
category: 'dokcer'
draft: false 
lang: ''
---
# docker的使用

## 1.docker的安装与部署

#### docker的安装：

详见linx系统和idea远程操控笔记

#### 部署mysql:
![img.png](img.png)


```
docker run -d --name mysql -p 3306:3306 -e TZ=Asia/Shanghai -e MYSQL_ROOT_PASSWORD=293928 mysql
```
![img_1.png](img_1.png)
因为docker镜像将所需的环境，配置，系统函数库等都进行了打包，所以docker的运行并不会受操作系统影响，可以跨操作系统运行

docker运行的时候会创建一个叫容器的隔离环境，这使得一台操作系统可以运行很多个docker服务器，而且各个运行的服务器不会因为环境，版本所冲突
![img_2.png](img_2.png)
比如我们拉取mysql的docker镜像后可以运行两个不同端口的mysql服务，而且他们两是互不冲突的
![img_3.png](img_3.png)
#### 命令解读：
![img_4.png](img_4.png)
这里解释一下-p:这个是将我们访问的宿主机端口(192.168.150.101)时将映射访问到对应mysql容器的mysql

## 2.docker基础
![img_5.png](img_5.png)
我们其实可以通过；

docker --help查看docker的命令，还有docker XX --help来查看docker命令的具体参数什么的

![img_6.png](img_6.png)
我们在查看docker容器的时候（即docker ps）的时候，会输出很多的信息，如果我们不想输出这么多的信息，我们可以通过上面这种方式来选择要输出的内容

--format后面跟着一串字符串，table后面.id是容器的id，.imag是镜像名，.ports是对应宿主机端口，.status是容器状态，.names是容器名称

但这么输入的话命令很长，所以我们可以通过给命令输出的办法：
![img_7.png](img_7.png)
在root目录的.bashrc容器中，可以定义一些命令的别名，这样我们就不用每次都输入这么长的命令，我们可以通过vi上面图片的命令进入文件编辑，注意vi要按i键才能编辑，然后esc退出编辑模式。（因为~就可以代表root,所以上面就是数入波浪线）。

然后我们还要用:

```
source ~/.bashrc
```

来使文件配置生效



#### **数据卷挂载**

**需求背景：**

![img_8.png](img_8.png)
![img_9.png](img_9.png)
我们要通过nginx容器来修改内部的index.html文件，但却发现nginx的容器里面没有vi命令，这是因为容器只提供了最基本的命令

#### 那我们该怎么修改容器内容呢？

![img_10.png](img_10.png)
我们可以通过宿主机搞一个目录（数据卷）与容器内目录进行映射，这样的话我们容器内文件的增删改查抖音可以在数据卷中进行，容器内目录会自动进行映射。
![img_11.png](img_11.png)


```
docker volume ls                     查看docker所以的数据卷
docker volume inspect 数据卷名        查看数据卷的详细信息
```
![img_12.png](img_12.png)
我们可以通过mobaX_term_personal 点击第四个图标，然后勾上follow terminal folder，最回自动跳转到data目录，我们可以在这里修改数据卷的信息，然后数据卷信息会自动映射到nginx的html

![img_13.png](img_13.png)
#### 本地数据挂载
![img_14.png](img_14.png)
**自定义镜像**
![img_15.png](img_15.png)
![img_16.png](img_16.png)
镜像不是一个打包成一个文件，而且有好几个文件，即好几个层

由于镜像制作有些依赖的库函数都是公共的，所以我们可以把这些文件作为一个基础镜像，这样这样每次制作镜像的时候就可以使用这些公共镜像，而不是每次都要一个一个去挑
![img_17.png](img_17.png)
![img_18.png](img_18.png)
![img_19.png](img_19.png)
**操作：**
![img_20.png](img_20.png)
先使用mobax将要使用的dockerfile,jar包拉到root目录（如果不是root目录，可以使用..回归到root目录）

![img_21.png](img_21.png)
![img_22.png](img_22.png)
这里要注意dockerfile中的镜像我们这边是没有的，我们需要下载，这边黑马已经提供了tar包，把tar包拉取到根目录，然后执行

```
docker load -i tar包名
```

将tar包解压成镜像。

![img_23.png](img_23.png)
然后我们进入dockerfile的文件目录，创建镜像，因为dockerfile就在该目录，而且dockerfile文件的名字也叫dockerfile符合默认名字，所以我们只需要起名和写一个点。
![img_24.png](img_24.png)
构建完镜像我们来运行容器，-d指的是在后台运行，--name起容器名字，-p指定端口映射，这里要指定8080，因为dockerfile的配置就是8080，最后指定跑哪个镜像就行

**总结**
![img_25.png](img_25.png)
#### docker网络
![img_26.png](img_26.png)
![img_27.png](img_27.png)
查看容器的具体信息，我们可以看到一个是.3,一个是.4然后前面其他ip都一样，是属于同一网段的，所以两容器是可以ping通的，那不是说容器是相互隔离的吗，他们是怎么联系的？

![img_28.png](img_28.png)
docker会创建一个虚拟网卡并分配ip十六字节后的变化（因为255是8个字节，也就是前面两段是不能变的，后面的会按顺序递增）
![img_29.png](img_29.png)
我们可以测试一下，我们先用：

```
docker exec -it dd bash
-it 表示 与容器进行交互式启动 -d 表示可后台运行容器 （守护式运行） 

exec表示进入容器
```

进入dd容器的控制台看看

但其实这种容器相互访问的方式是不好的，因为容器的ip不是固定的，一旦有其他的容器启动占用了你的ip，那容器的ip将会改变，这样你之前设置的ip就访问不到对应的ip地址了，那我们该怎么解决呢？
![img_30.png](img_30.png)
我们可以创建一个自定义网络，使网络内的容器能通过容器名相互访问


## 3.项目部署

#### 后端部署：

项目需求：
![img_31.png](img_31.png)
注意点：
![img_32.png](img_32.png)
注意：由于本地部署的ip和以后上线的ip是不一样的，所以我们这里要将datasource的url用${}占位符进行动态配置，将ip地址配置为开发环境对应yml文件中的**hm.db.host**属性

同时，后端还写了构建镜像需要的dockerfile文件：

![img_33.png](img_33.png)
我们将项目父工程进行打包，然后jar包和dockerfile文件一起扔进虚拟机的root目录下（mobax连接虚拟机，拉取文件什么都很方便），

![img_34.png](img_34.png)
执行构建镜像命令，可以看到镜像已经被我们构建完成了
![img_35.png](img_35.png)
运行容器，可以看到项目也就被部署成功了，访问项目的url，可以发现项目部署成功。



**前端部署：**

需求：
![img_36.png](img_36.png)
![img_37.png](img_37.png)
![img_38.png](img_38.png)
其中conf配置的是nginx端口代理到哪个目录下和后端接口的代理配置。

![img_39.png](img_39.png)
我们将对应的前端html文件拉取到linx中root文件目录下，然后先写容器启动语法：我们前端有多少个就挂载多少个端口，然后将前端文件挂载到docker的`/usr/share/nginx/html`,将nginx配置文件挂载到容器的`/etc/nginx/nginx.conf`中。同时配置将该容器加入自定义网络，使得其能与自定义网络中的其他容器进行交互。

这里要注意容器之间的访问都是用容器名访问的。



#### dockercompose
![img_40.png](img_40.png)
![img_41.png](img_41.png)
docoerkcompose是通过yml对容器的描述实现多个容器快速部署，其中：

image表示容器的镜像；port表示端口的映射；environmnet表示环境配置；volume表示数据卷的挂载；networks表示自定义网络。
![img_42.png](img_42.png)
章鱼哥不仅可以快速启动多个容器，还可以帮我们部署后端，创建自定义网络
![img_43.png](img_43.png)
![img_44.png](img_44.png)
我们只要在root文件目录下把compose.yml文件拉下来，然后我们再运行：

```
docker compose up -d
```

即可自动部署全部的容器，后端和自定义网络，实现一键部署

这里的-d在后台运行的意思是程序不会在前台打印一堆日志啥的，而是后台自己跑。


