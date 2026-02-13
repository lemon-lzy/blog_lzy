---
title: RAG知识库进阶
published: 2025-09-22
description: ''
image: '../cover.png'
tags: ["AI"]
category: 'AI'
draft: false 
lang: ''
---
# 一、RAG 核心特性
RAG ⁡应用的开发，实际上每个流程都有一些‏值得学习的特性，Spring AI؜ 也为这些流程的技术实现提供了支持﻿，下面让我们按照流程依次进行讲解。
- 文档收集和切割
- 向量转换和存储
- 文档过滤和检索
- 查询增强和关联
## 文档收集和切割 - ETL
文档收集和切割阶段，我们要对自己准备好的知识库文档进行处理，然后保存到向量数据库中。这个过程俗称 ETL（抽取、转换、加载），Spring AI 提供了对 ETL 的支持，参考 [官方文档](https://docs.spring.io/spring-ai/reference/api/etl-pipeline.html)。
### ETL
在 Spr‍ing AI 中，⁡对 Documen‏t 的处理通常遵循؜以下流程：

- **读取文档**：使用 DocumentReader 组件从数据源（如本地文件、网络资源、数据库等）加载文档。
- **转换文档**：根据需求将文档转换为适合后续处理的格式，比如去除冗余信息、分词、词性标注等，可以使用 DocumentTransformer 组件实现。
- **写入文档**：使用 DocumentWriter 将文档以特定格式保存到存储中，比如将文档以嵌入向量的形式写入到向量数据库，或者以键值对字符串的形式保存到 Redis 等 KV 存储中。
