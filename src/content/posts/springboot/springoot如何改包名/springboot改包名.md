---
title: springboot模版项目如何改包名
published: 2024-10-14
description: ''
image: './cover.png'
tags: ["springboot"]
category: 'springboot'
draft: false 
lang: ''
---
1.在对应文件夹重命名

2.使用ctrl+shift+R进行全局替换，将com.xx.xx改为com.xxx.xxx

3.如果项目名有中括号，就用file->projectStructe重构名字

4.检查其他依赖到路径的地方进行修改，比如mapper的xml文件，yml文件,pom文件等