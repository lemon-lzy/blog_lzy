---
title: AI应用开发
published: 2025-09-20
description: ''
image: '../cover.png'
tags: ["AI"]
category: 'AI'
draft: false 
lang: ''
---

# 一、AI 应用方案设计
## 1、系统提示词设计
前面提到，‍系统提示词相当于 ⁡AI 应用的 “灵‏魂”，直接决定了 ؜AI 的行为模式、﻿专业性和交互风格。

对于 AI‍ 对话应用，最简单⁡的做法是直接写一段‏系统预设，定义 “؜你是谁？能做什么？﻿”，比如：

````
你是一位恋爱大师，为用户提供情感咨询服务
````
这种简单提示虽然‍可以工作，但效果往往不够理想。

因此我们要‍优化系统预设，可以⁡借助 AI 进行‏优化。示例 Prom؜pt：

````
我正在开发【恋爱大师】AI 对话应用，请你帮我编写设置给 AI 大模型的系统预设 Prompt 指令。要求让 AI 作为恋爱专家，模拟真实恋爱咨询场景、多给用户一些引导性问题，不断深入了解用户，从而提供给用户更全面的建议，解决用户的情感问题。
````

AI 提供的优化后系统提示词：

````
扮演深耕恋爱心理领域的专家。开场向用户表明身份，告知用户可倾诉恋爱难题。围绕单身、恋爱、已婚三种状态提问：单身状态询问社交圈拓展及追求心仪对象的困扰；恋爱状态询问沟通、习惯差异引发的矛盾；已婚状态询问家庭责任与亲属关系处理的问题。引导用户详述事情经过、对方反应及自身想法，以便给出专属解决方案。
````

## 2、多轮对话实现
要实现具有 “记忆力” 的 AI 应用，让 AI 能够记住用户之前的对话内容并保持上下文连贯性，我们可以使用Spring AI 框架的 **对话记忆能力**。

如何使用对话记忆能力呢？参考 Spring AI 的官方文档，了解到 Spring AI 提供了 [ChatClient API](https://docs.spring.io/spring-ai/reference/api/chatclient.html) 来和 AI 大模型交互。



### ChatClient 特性
之前我们是直接使用 Spring Boot 注入的 [ChatModel](https://docs.spring.io/spring-ai/reference/api/chatmodel.html) 来调用大模型完成对话，而通过我们自己构造的 [ChatClient](https://docs.spring.io/spring-ai/reference/api/chatclient.html)，可实现功能更丰富、更灵活的 AI 对话客户端，也更推荐通过这种方式调用 AI。

通过示例代码，‍能够感受到 ChatMode⁡l 和 ChatClient 的‏区别。ChatClien؜t 支持更复杂灵活的链式调用（Fluent API）：
````
// 基础用法(ChatModel)
ChatResponse response = chatModel.call(new Prompt("你好"));

// 高级用法(ChatClient)
ChatClient chatClient = ChatClient.builder(chatModel)
    .defaultSystem("你是恋爱顾问")
    .build();
    
String response = chatClient.prompt().user("你好").call().content();
````
ChatC‍lient 支持多⁡种响应格式，比如返‏回 ChatRes؜ponse 对象、﻿返回实体对象、流式返回：
````
// ChatClient支持多种响应格式
// 1. 返回 ChatResponse 对象（包含元数据如 token 使用量）
ChatResponse chatResponse = chatClient.prompt()
    .user("Tell me a joke")
    .call()
    .chatResponse();

// 2. 返回实体对象（自动将 AI 输出映射为 Java 对象）
// 2.1 返回单个实体
record ActorFilms(String actor, List<String> movies) {}
ActorFilms actorFilms = chatClient.prompt()
    .user("Generate the filmography for a random actor.")
    .call()
    .entity(ActorFilms.class);

// 2.2 返回泛型集合
List<ActorFilms> multipleActors = chatClient.prompt()
    .user("Generate filmography for Tom Hanks and Bill Murray.")
    .call()
    .entity(new ParameterizedTypeReference<List<ActorFilms>>() {});

// 3. 流式返回（适用于打字机效果）
Flux<String> streamResponse = chatClient.prompt()
    .user("Tell me a story")
    .stream()
    .content();

// 也可以流式返回ChatResponse
Flux<ChatResponse> streamWithMetadata = chatClient.prompt()
    .user("Tell me a story")
    .stream()
    .chatResponse();
````
可以给 C‍hatClient ⁡设置默认参数，比如系‏统提示词，还可以在对؜话时动态更改系统提示﻿词的变量，类似模板的概念：
````
// 定义默认系统提示词
ChatClient chatClient = ChatClient.builder(chatModel)
        .defaultSystem("You are a friendly chat bot that answers question in the voice of a {voice}")
        .build();

// 对话时动态更改系统提示词的变量
chatClient.prompt()
        .system(sp -> sp.param("voice", voice))
        .user(message)
        .call()
        .content());
````
### Advisors
Spring AI 使用 Advisors（顾问）机制来增强 AI 的能力，可以理解为一系列可插拔的拦截器，在调用 AI 前和调用 AI 后可以执行一些额外的操作，比如：

- **前置增强**：调用 AI 前改写一下 Prompt 提示词、检查一下提示词是否安全
- **后置增强**：调用 AI 后记录一下日志、处理一下返回的结果

用法很简单，我们可‍以直接为 ChatClient 指定⁡**默认拦截器(顾问)**，比如**对话记忆拦截器 Me‏ssageChatMemoryAdv؜isor** 可以帮助我们实现多轮对话能﻿力，省去了自己维护对话列表的麻烦。
````
var chatClient = ChatClient.builder(chatModel)
    .defaultAdvisors(
        new MessageChatMemoryAdvisor(chatMemory), // 对话记忆 advisor
        new QuestionAnswerAdvisor(vectorStore)    // RAG 检索增强 advisor
    )
    .build();

String response = this.chatClient.prompt()
    // 对话时动态设定拦截器参数，比如指定对话记忆的 id 和长度
    .advisors(advisor -> advisor.param("chat_memory_conversation_id", "678")
            .param("chat_memory_response_size", 100))
    .user(userText)
    .call()
	.content();
````
#### Chat Memory Advisor
前面我们提到‍了，想要实现对话记忆功能⁡，可以使用 Spring‏ AI 的 **ChatMe؜moryAdvisor**，﻿它主要有几种内置的实现方式：

- **MessageChatMemoryAdvisor**：从记忆中检索历史对话，并将其作为消息集合添加到提示词中
- **PromptChatMemoryAdvisor**：从记忆中检索历史对话，并将其添加到提示词的系统文本中
- **VectorStoreChatMemoryAdvisor**：可以用向量数据库来存储检索历史对话

## 多轮对话 AI 应用开发
1）首先初始化 ChatC‍lient 对象。使用 Spring 的构造器注入方⁡式来注入阿里大模型 dashscopeChatMod‏el 对象，并使用该对象来初始化 ChatCli؜ent。初始化时指定默认的系统 Prompt 和基于内存﻿的对话记忆 Advisor。代码如下：
````
@Component
@Slf4j
public class LoveApp {

    private final ChatClient chatClient;

    private static final String SYSTEM_PROMPT = "扮演深耕恋爱心理领域的专家。开场向用户表明身份，告知用户可倾诉恋爱难题。" +
            "围绕单身、恋爱、已婚三种状态提问：单身状态询问社交圈拓展及追求心仪对象的困扰；" +
            "恋爱状态询问沟通、习惯差异引发的矛盾；已婚状态询问家庭责任与亲属关系处理的问题。" +
            "引导用户详述事情经过、对方反应及自身想法，以便给出专属解决方案。";

    public LoveApp(ChatModel dashscopeChatModel) {
        // 初始化基于内存的对话记忆
        ChatMemory chatMemory = new InMemoryChatMemory();
        chatClient = ChatClient.builder(dashscopeChatModel)
                .defaultSystem(SYSTEM_PROMPT)
                .defaultAdvisors(
                        new MessageChatMemoryAdvisor(chatMemory)
                )
                .build();
    }
}
````
2）编写对话方法‍。调用 chatClie⁡nt 对象，传入用户 Pr‏ompt，并且给 advi؜sor 指定对话 id 和对话﻿记忆大小。代码如下：
````
public String doChat(String message, String chatId) {
    ChatResponse response = chatClient
            .prompt()
            .user(message)
            .advisors(spec -> spec.param(CHAT_MEMORY_CONVERSATION_ID_KEY, chatId)
                    .param(CHAT_MEMORY_RETRIEVE_SIZE_KEY, 10))
            .call()
            .chatResponse();
    String content = response.getResult().getOutput().getText();
    log.info("content: {}", content);
    return content;
}
````
