---
title: AI生成应用-LangChain4j的使用
published: 2026-01-10
description: ''
image: './cover.png'
tags: ["项目"]
category: '项目'
draft: false 
lang: ''
---
# 一、需求分析
需求 —— 让AI 根据用户的描述，自动生成完整的网؜页应用。

AI 能够生成 原生网页代码，并将代码文件保存到本地。

原生是指代‍码中不需要引入第三方框架（比如 Vue 和 React），简؜简单单、干干净净，更容易运行，实现起来更简单。

我们可以采用 2 种原生生成模式来满足不同的使用场景：

- **原生 HTML 模式**：将所有代码（HTML、CSS、JS）打包在一个 HTML 文件中，适合快速原型和简单应用
- **原生多文件模式**：按照标准的前端项目结构，分别生成 index.html、style.css 和 script.js 文件
# 二、方案设‍计
## 整个 AI 应用生成的核心流程؜：

**用户输入描述 → AI 大模型生成 → 提取生成内容 → 写入本地文件**

### 技术细节：

- 如何实现和 AI 的对话？
- 如何设计有效的提示词？
- 如何确保 AI 输出的格式符合我们的要求？
- 如何处理生成的代码并保存到合适的位置？

## 编写系统提示词
提示词的质量直接决定了 AI 生成结果的好坏,我们可以参考网上的 Prompt [编写指南](https://help.aliyun.com/zh/model-studio/use-cases/prompt-engineering-guide) 。

有个小技巧‍是直接让 AI 帮我们根据需求来生成提示词，比如我向 ؜AI 提问：
````
我正在做一个 AI 零代码应用生成平台，根据用户的一段描述即可生成一个完整网站。生成的网站使用 HTML + CSS + JS 实现，帮我编写一个专业的 Prompt。
````
经过多轮调试和优化，我最终确定了两套系统提示词模板：

**1）生成单个 HTML 文件模式的提示词**：
````
你是一位资深的 Web 前端开发专家，精通 HTML、CSS 和原生 JavaScript。你擅长构建响应式、美观且代码整洁的单页面网站。

你的任务是根据用户提供的网站描述，生成一个完整、独立的单页面网站。你需要一步步思考，并最终将所有代码整合到一个 HTML 文件中。

约束:
1. 技术栈: 只能使用 HTML、CSS 和原生 JavaScript。
2. 禁止外部依赖: 绝对不允许使用任何外部 CSS 框架、JS 库或字体库。所有功能必须用原生代码实现。
3. 独立文件: 必须将所有的 CSS 代码都内联在 `<head>` 标签的 `<style>` 标签内，并将所有的 JavaScript 代码都放在 `</body>` 标签之前的 `<script>` 标签内。最终只输出一个 `.html` 文件，不包含任何外部文件引用。
4. 响应式设计: 网站必须是响应式的，能够在桌面和移动设备上良好显示。请优先使用 Flexbox 或 Grid 进行布局。
5. 内容填充: 如果用户描述中缺少具体文本或图片，请使用有意义的占位符。例如，文本可以使用 Lorem Ipsum，图片可以使用 https://picsum.photos 的服务 (例如 `<img src="https://picsum.photos/800/600" alt="Placeholder Image">`)。
6. 代码质量: 代码必须结构清晰、有适当的注释，易于阅读和维护。
7. 交互性: 如果用户描述了交互功能 (如 Tab 切换、图片轮播、表单提交提示等)，请使用原生 JavaScript 来实现。
8. 安全性: 不要包含任何服务器端代码或逻辑。所有功能都是纯客户端的。
9. 输出格式: 你的最终输出必须包含 HTML 代码块，可以在代码块之外添加解释、标题或总结性文字。格式如下：

```html
... HTML 代码 ...
````
上面的**Lorem ipsum**是印刷排版行业使用的虚拟文本，主要用于测试文章或文字在不同字型、版型下的视觉效果。在我们的场景中，当用户描述比较简略时，AI 可以用这类占位符内容来完善页面结构。

**2）生成多文件模式的提示词**：
````
你是一位资深的‍ Web 前端开发专家，你精⁡通编写结构化的 HTML、清‏晰的 CSS 和高效的原生 ؜JavaScript，遵循代﻿码分离和模块化的最佳实践。

你的任务是根据用户提供的网站描述，创建构成一个完整单页网站所需的三个核心文件：HTML, CSS, 和 JavaScript。你需要在最终输出时，将这三部分代码分别放入三个独立的 Markdown 代码块中，并明确标注文件名。

约束：
1. 技术栈: 只能使用 HTML、CSS 和原生 JavaScript。
2. 文件分离:
- index.html: 只包含网页的结构和内容。它必须在 `<head>` 中通过 `<link>` 标签引用 `style.css`，并且在 `</body>` 结束标签之前通过 `<script>` 标签引用 `script.js`。
- style.css: 包含网站所有的样式规则。
- script.js: 包含网站所有的交互逻辑。
3. 禁止外部依赖: 绝对不允许使用任何外部 CSS 框架、JS 库或字体库。所有功能必须用原生代码实现。
4. 响应式设计: 网站必须是响应式的，能够在桌面和移动设备上良好显示。请在 CSS 中使用 Flexbox 或 Grid 进行布局。
5. 内容填充: 如果用户描述中缺少具体文本或图片，请使用有意义的占位符。例如，文本可以使用 Lorem Ipsum，图片可以使用 https://picsum.photos 的服务 (例如 `<img src="https://picsum.photos/800/600" alt="Placeholder Image">`)。
6. 代码质量: 代码必须结构清晰、有适当的注释，易于阅读和维护。
7. 输出格式: 每个代码块前要注明文件名。可以在代码块之外添加解释、标题或总结性文字。格式如下：

```html
... HTML 代码 ...


```css
... CSS 代码 ...


```javascript
... JavaScript 代码 ...
````
## 大模型技术选型
大模型的能力决定了 AI 生成结果的上限。在选择大模型时，需要重点关注：效果、成本、生成速度、开发成本、性能、生态。

像我平时用‍的比较多的是**阿里的通义系列大模型**，因为阿里的生态做的不؜错，能很轻松地在 Java 中接入。

所以在选择具体的大模型服务时，我会优先使用 **阿里云百炼平台** 进行效果测试，发现它的输出结构是能够满足需求的。
![img.png](img.png)
但是经过对‍比测试，我发现 DeepSeek R1 在代码生成任务上和性价比方面表现؜更出色，下面这个是我用编写好的提示词生成的网站：
![img_1.png](img_1.png)
除了生成效果，我们还需要考虑开发成本和功能完整性。查看 DeepSeek 的官方文档 后，我发现它与 OpenAI 的 API 完全兼容，这意味着我们可以直接使用现有的 OpenAI 客户端库来接入 DeepSeek。
## AI 开发框架选型
AI 开发框架的作用是**简化项目中开发 AI 应用的过程**。实际企业开发中，可不是调用一下 AI 的 API 接口这么简单，还有像 RAG 检索增强生成、Tools 工具调用、MCP 模型上下文协议等典型场景，这些功能如果都自己开发，那成本可就太高了。

### 实际开发中应该如何选择 AI 开发框架呢？

目前主流的 Java AI 开发框‍架当属 Spring AI 和 LangChain4j:
- Spring AI 目前支持的能力更多，还有国内 Spring AI Alibaba 的巨头加؜持，生态更好，遇到问题更容易解决；
- LangChain4j 的优势在于可以独立于 Spring 项目使用，更自由灵活一些。

不过这类框‍架重点学习一个就好了，很多概念和用法是相通的,我这里选择的是 LangChain4j。

# 三、LangChain4j入门
LangChain4j 是目前主流的 Java AI 开发框架。؜我看重它的 3 大优势：

- **声明式编程模式**：集成简单，通过**简单的注解和接口定义**，就能实现复杂的 AI 交互逻辑，这大大降低了开发门槛
- **丰富的模型支持**：不仅支持 OpenAI，还**兼容国内外主流**的大模型服务
- **容易集成**：它和 **Spring Boot 的集成**做得非常好，能快速整合到已有项目中
 ## LangChain4j的重要特性如下：
- AI 对话 - ChatModel
- 多模态 - Multimodality
- 系统提示词 - SystemMessage
- AI 服务 - AI Service
- Spring Boot 项目整合
- 会话记忆 - ChatMemory
- 结构化输出
- 检索增强生成 - RAG
- 工具调用 - Tools
- 模型上下文协议 - MCP
- 护轨 - Guardrail
- 日志和可观测性

[官方文档](https://docs.langchain4j.dev/intro/)
# 四、实现 AI 应用生成
确定了技术方案后，我们开始实现。

## 1、大模型接入
首先需要获取 DeepSeek 的 API Key，访问 [官方文档](https://api-docs.deepseek.com/zh-cn/)：
![img_2.png](img_2.png)
接下来引入必要的依赖。参考 [LangChain4j 的官方文档](https://docs.langchain4j.dev/integrations/language-models/open-ai/#spring-boot)，我们需要添加 OpenAI 大模型的依赖：
![img_3.png](img_3.png)
在 pom.xml 中添加依赖：
````
<dependency>
    <groupId>dev.langchain4j</groupId>
    <artifactId>langchain4j</artifactId>
    <version>1.1.0</version>
</dependency>
<dependency>
    <groupId>dev.langchain4j</groupId>
    <artifactId>langchain4j-open-ai-spring-boot-starter</artifactId>
    <version>1.1.0-beta7</version>
</dependency>
````
为了保护敏感信息，我们需要在 **.gitignore** 中添加本地配置文件 application-local.yml，忽略该文件的提交，之后就可以放心地将敏感配置都写在这个文件里了。
````
### CUSTOM ###
application-local.yml
````
创建本地配置文件 application-local.yml，填写 Chat Model 配置。此外，为了调试方便，我们开启了详细的日志记录。这里参考了 LangChain4j 的日志配置文档：
````
# AI
langchain4j:
open-ai:
chat-model:
base-url: https://api.deepseek.com
api-key: <Your API Key>
model-name: deepseek-chat
log-requests: true
log-responses: true
````
在主配置文件中激活本地环境：
````
spring:
profiles:
active: local
````
## 2、开发 AI 服务
按照 LangChain4j 推荐的 AI Service 开发模式，在 ai 包下创建服务接口：
````
public interface AiCodeGeneratorService {

    String generateCode(String userMessage);
}
````
考虑到系统‍提示词通常比较长，将它们单独维护在资源文件中。准备了两؜种生成模式对应的系统提示词文件：
- codegen-html-system-prompt.txt：原生 HTML 模式
- codegen-multi-file-system-prompt.txt：原生三件套模式

![img_4.png](img_4.png)
  在服务接口中‍添加 2 个生成代码的方法，分别对应 2 种生成模式。使用 Lan؜gChain4j 的注解来指定系统提示词：
![img_5.png](img_5.png)
  创建工厂类来初始化 AI 服务：
![img_6.png](img_6.png)
最后，编写单元测试来验证功能：
![img_7.png](img_7.png)


