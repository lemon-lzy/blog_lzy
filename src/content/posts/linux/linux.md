---
title: linux
published: 2026-02-28
description: ''
image: './cover.png'
tags: ["linux"]
category: 'linux'
draft: false 
lang: ''
---

# 目录和文件操作相关
````
ls：查询当前目录下，都包含那些文件
ll：查询当前目录下，都包含那些文件（含详细信息）
pwd：查看当前目录是什么
cd：切换当前目录: cd 路径
touch：创建文件: touch 文件名
cat：读取文件: cat 文件名
echo：写入文件: echo 内容 > 文件名
mkdir：创建目录: mkdir 目录名
rm：删除文件或文件夹 -r表示递归：rm -r 文件夹名字
cp：复制文件：cp 要复制的文件 复制到哪里
mv：移动文件: mv 要移动的文件 移动到哪里
less：查看文件内容，使用↑或者↓就可以进行翻页：less 文件名
head：查看文件的开头 -n 表示行数：head -n 行数 文件名
tail：查看文件的末尾 -n 表示行数：tail -n 行数 文件名
vi[vim]：文本编辑: vi 文件名，输入i进入编辑模式，esc回到普通模式，输入:wq保存文本。
搜索关键字：输入英文的?或/，后面跟上要搜索的关键词，然后按住n健查找下一个，按N健查找上一个。
find：查找文件：find test/ -name "abc*"
````

# 网络相关：
````
wget：单线程URL请求下载：wget url
axel: 多线程URL请求下载：shell axel -n 线程数 url
rz：上传文件
sz：下载文件：sz 文件路径名
scp：跨服务器传输文件或文件夹 -r 表示递归：scp aaa.zip root@192.168.200.130:/usr/local/
````

# 项目相关
````
free：查看内存空间 -m 多少M，-g 多少G：free -g
df：查看磁盘空间：df -h /
du -sh：查看文件或者文件夹占用的空间：du -sh 文件或者文件路径名
top：查看CPU使用情况
ps：查看进程：ps -ef|grep java
lsof：查看端口占用：lsof -i:端口号
telnet：端口是否开启：telnet 192.168.200.130 8080
kill：杀掉进程：kill -9 1011
````

# 其他：
````
chmod：为某个目录添加执行权限：chmod a+x -R 目录名
sudo: 获取管理员权限 -i 不失效：sudo -i 然后输入当前管理员用户的密码 
sudo password root：修改root的密码
su：切换管理员身份 su命令之后，输入root的密码即可
gref: 匹配（配合 -i忽略大小写，-n 显示多少行）
````

# 项目平时使用排查：

### 实时跟踪日志，同时过滤包含「error」的行（快速找报错）
````
tail -f app.log | grep "error"
````

### 查看进程的内存/cpu等资源
````
top -p PID进程号
````

### 文件权限修改：
````
chmod 755 命令的含义是：
1）7: 文件所有者（user）的权限，表示读、写和执行 (rwx) 权限。
2）5: 文件所属组（group）的权限，表示读和执行 (r-x) 权限。
3）5: 其他用户（others）的权限，表示读和执行 (r-x) 权限。

换句话说，通过 chmod 755 filename，我们为文件设置了以下权限：
1）文件所有者：可读，可写，可执行。
2）文件所在组：可读，可执行，但不能修改。
3）其他用户：可读，可执行，但不能修改。

1）权限的数值表示及其计算方法：

权限 rwx 三种状态中，每种状态都对应一个数值：r=4，w=2，x=1。当某种身份（如user）拥有某些权限时，这些数值需要累加，例如：rwx=7 (4+2+1)，r-x=5 (4+1)。
````

### 查看java进程
````
jps 只查看java进程

ps -ef | grep java  过滤出包含「java」关键词的进程行。
````


