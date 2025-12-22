---
title: swagger自动生成前端请求接口
published: 2024-10-22
description: ''
image: './cover.png'
tags: ["springboot"]
category: 'springboot'
draft: false 
lang: ''
---
# 后端swagger/前端自动生成接口

1. 在后端，我们使用knife4j（集Swagger）和openApi为一体的增强解决方案，我们先引入对应依赖，然后再配置对应配置类（具体看自己的项目和网上搜）
2. 访问localhost:xx/前缀/doc.html
3. 搜eslint，勾选automatic eslint configuration和搜prettier勾选on reformat code action（按ctrl加alt加L会自动帮我们美化代码）这些都是为了前端代码规范。
4. 在config中的confiig.ts中找到openapi,有两个大括号，把其中一个删掉，只剩下有schemapath的那一块，在swagger文档中将文档路径的doc.html替换成swaggee文档中主页的分组url然后访问，能看见一堆json的代码，将他们全部复制然后粘贴在schemapath中，修改projectname为自己想要的，最后运行package.json中openapi就可以生成对应接口代码了。