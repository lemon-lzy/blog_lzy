---
title: AI-工具调用
published: 2025-09-23
description: ''
image: '../cover.png'
tags: ["AI"]
category: 'AI'
draft: false 
lang: ''
---
# 一、需求分‍析
我们通过 RAG 技术让 AI 应用具备了根据外部知识库来获取信息并回答的能力，但是直到目前为止，AI 应用还只是个 “知识问答助手”。本节我们可以利用 工具调用 特性，实现更多需求。

## 1）联网搜索

比如智能推荐约会地点，示例用户提问：

- 周末想带女朋友去上海约会，推荐几个适合情侣的小众打卡地？
- 女朋友生气了，有哪些温柔的哄人技巧？
## 2）网页抓取

比如分析恋爱案例，示例用户提问：

最近和对象吵架了，看看 编程导航 的其他情侣是怎么解决矛盾的？
## 3）资源下载

比如恋爱相关的图片 / 音视频下载，示例用户提问：

- 下载一张适合做手机壁纸的星空情侣图片
- 推荐并下载几首适合约会时播放的钢琴曲
## 4）终端操作

- 比如执行代码来生成恋爱报告，示例用户提问：

执行 Python 脚本来生成数据分析报告
## 5）文件操作

比如保存用户恋爱档案，示例用户提问：

- 帮我保存我的恋爱档案为文件
## 6）PDF 生成

比如恋爱计划、情感分析报告 PDF 生成，示例用户提问：

- 生成一份《七夕约会计划》PDF，包含餐厅预订、活动流程和礼物清单
- 分析我和对象近一个月的聊天记录，生成情感报告

# 二、工具调用介绍
## 什么是工具调用？
- 工具调用（Tool Calling）可以理解为让 AI 大模型 借用外部工具 来完成它自己做不到的事情。

- 跟人类一样‍，如果只凭手脚完成⁡不了工作，那么就可‏以利用工具箱来完成؜。

- 工具可以是‍任何东西，比如网页⁡搜索、对外部 AP‏I 的调用、访问外؜部数据、或执行特定﻿的代码等。

- 比如用户提‍问 “帮我查询上海最⁡新的天气”，AI 本‏身并没有这些知识，它؜就可以调用 “查询天﻿气工具”，来完成任务。

- 目前工具调‍用技术发展的已经比较⁡成熟了，几乎所有主流‏的、新出的 AI 大؜模型和 AI 应用开﻿发平台都支持工具调用。

## 工具调用的工作原理
其实，工具调用的工作原理非常简单，**并不是 AI 服务器自己调用这些工具、也不是把工具的代码发送给 AI 服务器让它执行**，它只能提出要求，表示 “我需要执行 XX 工具完成任务”。而真正执行工具的是我们自己的应用程序，执行后再把结果告诉 AI，让它继续工作。

举个例子，‍假如用户提问 “编⁡程导航网站有哪些热‏门文章？”，就需要؜经历下列流程：

- 用户提出问题："编程导航网站有哪些热门文章？"
- 程序将问题传递给大模型
- 大模型分析问题，判断需要使用工具（网页抓取工具）来获取信息
- 大模型输出工具名称和参数（网页抓取工具）
- 程序接收工具调用请求，执行网页抓取操作
- 工具执行抓取并返回文章数据
- 程序将抓取结果传回给大模型
- 大模型分析网页内容，生成关于编程导航热门文章的回答
- 程序将大模型的回答返回给用户

虽然看起来是 AI 在调用工具，但实际上整个过程是 由我们的应用程序控制的。AI 只负责决定什么时候需要用工具，以及需要传递什么参数，真正执行工具的是我们的程序。

你可能会好‍奇，为啥要这么设计⁡呢？这样不是要让程‏序请求 AI 多次؜么？为啥不让 AI﻿ 服务器直接调用工具程序？

有这个想法很正常，但如果让你自己设计一个 AI 大模型服务，你就能理解了。很关键的一点是 安全性，AI 模型永远无法直接接触你的 API 或系统资源，所有操作都必须通过你的程序来执行，这样你可以完全控制 AI 能做什么、不能做什么。

举个例子，你有一个爆破工具‍，用户像 AI 提了需求 ”我要拆这栋房子“，虽然⁡ AI 表示可以用爆破工具，但是需要经过你的同意，‏才能执行爆破。反之，如果把爆破工具植入给 AI，A؜I 觉得自己能炸了，就炸了，不需要再问你的意见。而﻿且这样也给 AI 服务器本身增加了压力。

## 工具调用和功能调用
大家可能看到过 F‍unction Calling（功⁡能调用）这个概念，别担心，其实它和‏ Tool Calling（工具调؜用）完全是同一概念！只是不同平台或﻿每个人习惯的叫法不同而已。

## 工具调用的技术选型
我们先来梳理一下工具调用的流程：

- 工具定义：程序告诉 AI “你可以使用这些工具”，并描述每个工具的功能和所需参数
- 工具选择：AI 在对话中判断需要使用某个工具，并准备好相应的参数
- 返回意图：AI 返回 “我想用 XX 工具，参数是 XXX” 的信息
- 工具执行：我们的程序接收请求，执行相应的工具操作
- 结果返回：程序将工具执行的结果发回给 AI
- 继续对话：AI 根据工具返回的结果，生成最终回答给用户
通过上述流程，我们会发现，‍程序需要和 AI 多次进行交互、还要能够执行对应的⁡工具，怎么实现这些呢？我们当然可以自主开发，不过还‏是更推荐使用 Spring AI、LangChai؜n 等开发框架。此外，有些 AI 大模型服务商也提﻿供了对应的 SDK，都能够简化代码编写。

# 三、Spring AI 工具开发
首先我们通过 [Spring AI 官方](https://docs.spring.io/spring-ai/reference/api/tools.html) 提供的图片来理解 Spring AI 在实现工具调用时都帮我们做了哪些事情？
![img.png](img.png)
- **工具定义与注册**：Spring AI 可以通过简洁的注解自动生成工具定义和 JSON Schema，让 Java 方法轻松转变为 AI 可调用的工具。
- **工具调用请求**：Spring AI 自动处理与 AI 模型的通信并解析工具调用请求，并且支持多个工具链式调用。
- **工具执行**：Spring AI 提供统一的工具管理接口，自动根据 AI 返回的工具调用请求找到对应的工具并解析参数进行调用，让开发者专注于业务逻辑实现。
- **处理工具结果**：Spring AI 内置结果转换和异常处理机制，支持各种复杂 Java 对象作为返回值并优雅处理错误情况。
- **返回结果给模型**：Spring AI 封装响应结果并管理上下文，确保工具执行结果正确传递给模型或直接返回给用户。
- **生成最终响应**：Spring AI 自动整合工具调用结果到对话上下文，支持多轮复杂交互，确保 AI 回复的连贯性和准确性。

![img_1.png](img_1.png)
## 定义工具
**工具定义模式**

**在 Spr‍ing AI 中，定⁡义工具主要有两种模式**‏：基于 Method؜s 方法或者 Fun﻿ctions 函数式编程。

记结论就行了，我们只用学习 基于 Methods 方法 来定义工具，另外一种了解即可。原因是 Methods 方式更容易编写、更容易理解、支持的参数和返回类型更多。

**举个例子来对比这两种定义模式**：

1）**Methods 模式**：通过 **@Tool** 注解定义工具，通过 **tools 方法**绑定工具
````
class WeatherTools {
    @Tool(description = "Get current weather for a location")
    public String getWeather(@ToolParam(description = "The city name") String city) {
        return "Current weather in " + city + ": Sunny, 25°C";
    }
}

// 使用方式
ChatClient.create(chatModel)
    .prompt("What's the weather in Beijing?")
    .tools(new WeatherTools())
    .call();
````
2）Functions 模式：通过 @Bean 注解定义工具，通过 functions 方法绑定工具
````
@Configuration
public class ToolConfig {
    @Bean
    @Description("Get current weather for a location")
    public Function<WeatherRequest, WeatherResponse> weatherFunction() {
        return request -> new WeatherResponse("Weather in " + request.getCity() + ": Sunny, 25°C");
    }
}

// 使用方式
ChatClient.create(chatModel)
    .prompt("What's the weather in Beijing?")
    .functions("weatherFunction")
    .call();
````
显然 Met‍hods 模式的开发量更⁡少（我估计很多同学都没写‏过 Function 函؜数式编程），更推荐这种方﻿式，所以下面重点讲解这种方式。

**定义工具**
Spring AI 提供了两种定义工具的方法 —— **注解式** 和 **编程式**。

1）**注解式**：只需使用 @Tool 注解标记普通 Java 方法，就可以定义工具了，简单直观。

每个工具最好都添加详细清晰的描述，帮助 AI 理解何时应该调用这个工具。对于工具方法的参数，可以使用 @ToolParam 注解提供额外的描述信息和是否必填。
````
class WeatherTools {
    @Tool(description = "获取指定城市的当前天气情况")
    String getWeather(@ToolParam(description = "城市名称") String city) {
        // 获取天气的实现逻辑
        return "北京今天晴朗，气温25°C";
    }
}
````

2）编程式‍：如果想在运行时动⁡态创建工具，可以选‏择编程式来定义工具，؜更灵活。

先定义工具类：
````
class WeatherTools {
    String getWeather(String city) {
        // 获取天气的实现逻辑
        return "北京今天晴朗，气温25°C";
    }
}
````
然后将工具类‍转换为 ToolCall⁡back 工具定义类，之‏后就可以把这个类绑定给 ؜ChatClient，从﻿而让 AI 使用工具了。
````
Method method = ReflectionUtils.findMethod(WeatherTools.class, "getWeather", String.class);
ToolCallback toolCallback = MethodToolCallback.builder()
    .toolDefinition(ToolDefinition.builder(method)
            .description("获取指定城市的当前天气情况")
            .build())
    .toolMethod(method)
    .toolObject(new WeatherTools())
    .build();
````
然后将工具类‍转换为 ToolCall⁡back 工具定义类，之‏后就可以把这个类绑定给 ؜ChatClient，从﻿而让 AI 使用工具了。
````
Method method = ReflectionUtils.findMethod(WeatherTools.class, "getWeather", String.class);
ToolCallback toolCallback = MethodToolCallback.builder()
    .toolDefinition(ToolDefinition.builder(method)
            .description("获取指定城市的当前天气情况")
            .build())
    .toolMethod(method)
    .toolObject(new WeatherTools())
    .build();
````
其实你会发‍现，编程式就是把注⁡解式的那些参数，改‏成通过调用方法来设置؜了而已。

在定义工具时，需要注‍意方法参数和返回值类型的选择。Sprin⁡g AI 支持大多数常见的 Java 类‏型作为参数和返回值，包括基本类型、复杂对象、؜集合等。而且返回值需要是可序列化的，﻿因为它将被发送给 AI 大模型。

以下类型目前不支持作为工具方法的参数或返回类型：
- Optional
- 异步类型（如 CompletableFuture, Future）
- 响应式类型（如 Flow, Mono, Flux）
- 函数式类型（如 Function, Supplier, Consumer）

**使用工具**

定义好工具后‍，Spring AI ⁡提供了多种灵活的方式将‏工具提供给 ChatC؜lient，让 AI ﻿能够在需要时调用这些工具。

1）按需使用：这是最简单的方式，直接在构建 ChatClient 请求时通过 tools() 方法附加工具。这种方式适合只在特定对话中使用某些工具的场景。
````
String response = ChatClient.create(chatModel)
    .prompt("北京今天天气怎么样？")
    .tools(new WeatherTools())  // 在这次对话中提供天气工具
    .call()
    .content();
````
2）全局使用：如‍果某些工具需要在所有对话中都可用⁡，可以在构建 ChatClien‏t 时注册默认工具。这样，这些工؜具将对从同一个 ChatClie﻿nt 发起的所有对话可用。
````
ChatClient chatClient = ChatClient.builder(chatModel)
    .defaultTools(new WeatherTools(), new TimeTools())  // 注册默认工具
    .build();
````
3）更底层的使用方‍式：除了给 ChatClient ⁡绑定工具外，也可以给更底层的 Ch‏atModel 绑定工具（毕竟工具؜调用是 AI 大模型支持的能力），﻿适合需要更精细控制的场景。
````
// 先得到工具对象
ToolCallback[] weatherTools = ToolCallbacks.from(new WeatherTools());
// 绑定工具到对话
ChatOptions chatOptions = ToolCallingChatOptions.builder()
    .toolCallbacks(weatherTools)
    .build();
// 构造 Prompt 时指定对话选项
Prompt prompt = new Prompt("北京今天天气怎么样？", chatOptions);
chatModel.call(prompt);
````
## 工具生态
首先，工具的本质就是一种插件。能不自己写的插件，就尽量不要自己写。我们可以直接在网上找一些优秀的工具实现，比如 [Spring AI Alibaba 官方文档](https://java2ai.com/docs/1.0.0-M6.1/integrations/tools/) 中提到了社区插件。

虽然文档里只提到了屈指可数的插件数，但我们可以顺藤摸瓜，在 GitHub 社区找到官方提供的更多 [工具源码](https://github.com/alibaba/spring-ai-alibaba/tree/main/community/tool-calls)，包含大量有用的工具！比如翻译工具、网页搜索工具、爬虫工具、地图工具等：

# 四、主流工具开发
如果社区中没找到合‍适的工具，我们就要自主开发。需要注⁡意的是，AI 自身能够实现的功能通‏常没必要定义为额外的工具，因为这会؜增加一次额外的交互，我们应该将工具﻿用于 AI 无法直接完成的任务。

下面我们依次来实现需求分析中提到的 6 大工具，开发过程中我们要 格外注意工具描述的定义，因为它会影响 AI 决定是否使用工具。

先在项目根包下新建 tools 包，将所有工具类放在该包下；并且工具的返回值尽量使用 String 类型，让结果的含义更加明确。

## 文件操作
文件操作工具主要提供 2 大功能：保存文件、读取文件。

由于会影响系统资源，所以我们需要将文件统一存放到一个隔离的目录进行存储，在 constant 包下新建文件常量类，约定文件保存目录为项目根目录下的 /tmp 目录中。
````
public interface FileConstant {

    /**
     * 文件保存目录
     */
    String FILE_SAVE_DIR = System.getProperty("user.dir") + "/tmp";
}
````
编写文件操作工具类，通过注解式定义工具，代码如下：
````
public class FileOperationTool {

    private final String FILE_DIR = FileConstant.FILE_SAVE_DIR + "/file";

    @Tool(description = "Read content from a file")
    public String readFile(@ToolParam(description = "Name of the file to read") String fileName) {
        String filePath = FILE_DIR + "/" + fileName;
        try {
            return FileUtil.readUtf8String(filePath);
        } catch (Exception e) {
            return "Error reading file: " + e.getMessage();
        }
    }

    @Tool(description = "Write content to a file")
    public String writeFile(
        @ToolParam(description = "Name of the file to write") String fileName,
        @ToolParam(description = "Content to write to the file") String content) {
        String filePath = FILE_DIR + "/" + fileName;
        try {
            // 创建目录
            FileUtil.mkdir(FILE_DIR);
            FileUtil.writeUtf8String(content, filePath);
            return "File written successfully to: " + filePath;
        } catch (Exception e) {
            return "Error writing to file: " + e.getMessage();
        }
    }
}
````
编写单元测试验证工具功能：
````
@SpringBootTest
public class FileOperationToolTest {

    @Test
    public void testReadFile() {
        FileOperationTool tool = new FileOperationTool();
        String fileName = "编程导航.txt";
        String result = tool.readFile(fileName);
        assertNotNull(result);
    }

    @Test
    public void testWriteFile() {
        FileOperationTool tool = new FileOperationTool();
        String fileName = "编程导航.txt";
        String content = "https://www.codefather.cn 程序员编程学习交流社区";
        String result = tool.writeFile(fileName, content);
        assertNotNull(result);
    }
}
````
## 联网搜索
联网搜索工具的作用是根据关键词搜索网页列表。

我们可以使用专业的网页搜索 API，如 [Search API](https://www.searchapi.io/baidu) 来实现从多个网站搜索内容，这类服务通常按量计费。当然也可以直接使用 Google 或 Bing 的搜索 API（甚至是通过爬虫和网页解析从某个搜索引擎获取内容）。

1）阅读 [Search API 的 官方文档](https://www.searchapi.io/baidu)，重点关注 API 的请求参数和返回结果。从 API 返回的结果中，我们只需要提取关键部分：
````
{
  "organic_results": [
    ...
    {
      "position": 1,
      "title": "编程导航 - 程序员一站式编程学习交流社区,做您编程学习路...",
      "link": "https://codefather.cn/",
      "displayed_link": "codefather.cn/",
      "snippet": "学编程,就来编程导航,程序员免费编程学习交流社区。Java,Python,前端,web网站开发,C语言,C++,Go,后端,SQL,数据库,PHP入门学习、技能提升、求职面试法宝。提升编程效率、优质IT技术文章、海...",
      "snippet_highlighted_words": [
        "编程",
        "编程导航",
        "程序员"
      ],
      "thumbnail": "https://t8.baidu.com/it/u=661528516,2886240705&fm=217&app=126&size=f242,150&n=0&f=JPEG&fmt=auto?s=73B489634AD237E3660C19280200A063&sec=1744477200&t=b5d8762a6f5728d5f2fbc6bcf1774b20"
    },
    ...
  ]
}
````
2）可以把‍接口文档喂给 AI⁡，让它帮我们生成工‏具代码，网页搜索工؜具代码如下：
````
public class WebSearchTool {

    // SearchAPI 的搜索接口地址
    private static final String SEARCH_API_URL = "https://www.searchapi.io/api/v1/search";

    private final String apiKey;

    public WebSearchTool(String apiKey) {
        this.apiKey = apiKey;
    }

    @Tool(description = "Search for information from Baidu Search Engine")
    public String searchWeb(
            @ToolParam(description = "Search query keyword") String query) {
        Map<String, Object> paramMap = new HashMap<>();
        paramMap.put("q", query);
        paramMap.put("api_key", apiKey);
        paramMap.put("engine", "baidu");
        try {
            String response = HttpUtil.get(SEARCH_API_URL, paramMap);
            // 取出返回结果的前 5 条
            JSONObject jsonObject = JSONUtil.parseObj(response);
            // 提取 organic_results 部分
            JSONArray organicResults = jsonObject.getJSONArray("organic_results");
            List<Object> objects = organicResults.subList(0, 5);
            // 拼接搜索结果为字符串
            String result = objects.stream().map(obj -> {
                JSONObject tmpJSONObject = (JSONObject) obj;
                return tmpJSONObject.toString();
            }).collect(Collectors.joining(","));
            return result;
        } catch (Exception e) {
            return "Error searching Baidu: " + e.getMessage();
        }
    }
}
````

````
{
  "organic_results": [
    ...
    {
      "position": 1,
      "title": "编程导航 - 程序员一站式编程学习交流社区,做您编程学习路...",
      "link": "https://codefather.cn/",
      "displayed_link": "codefather.cn/",
      "snippet": "学编程,就来编程导航,程序员免费编程学习交流社区。Java,Python,前端,web网站开发,C语言,C++,Go,后端,SQL,数据库,PHP入门学习、技能提升、求职面试法宝。提升编程效率、优质IT技术文章、海...",
      "snippet_highlighted_words": [
        "编程",
        "编程导航",
        "程序员"
      ],
      "thumbnail": "https://t8.baidu.com/it/u=661528516,2886240705&fm=217&app=126&size=f242,150&n=0&f=JPEG&fmt=auto?s=73B489634AD237E3660C19280200A063&sec=1744477200&t=b5d8762a6f5728d5f2fbc6bcf1774b20"
    },
    ...
  ]
}
````
2）可以把‍接口文档喂给 AI⁡，让它帮我们生成工‏具代码，网页搜索工؜具代码如下：
````
public class WebSearchTool {

    // SearchAPI 的搜索接口地址
    private static final String SEARCH_API_URL = "https://www.searchapi.io/api/v1/search";

    private final String apiKey;

    public WebSearchTool(String apiKey) {
        this.apiKey = apiKey;
    }

    @Tool(description = "Search for information from Baidu Search Engine")
    public String searchWeb(
            @ToolParam(description = "Search query keyword") String query) {
        Map<String, Object> paramMap = new HashMap<>();
        paramMap.put("q", query);
        paramMap.put("api_key", apiKey);
        paramMap.put("engine", "baidu");
        try {
            String response = HttpUtil.get(SEARCH_API_URL, paramMap);
            // 取出返回结果的前 5 条
            JSONObject jsonObject = JSONUtil.parseObj(response);
            // 提取 organic_results 部分
            JSONArray organicResults = jsonObject.getJSONArray("organic_results");
            List<Object> objects = organicResults.subList(0, 5);
            // 拼接搜索结果为字符串
            String result = objects.stream().map(obj -> {
                JSONObject tmpJSONObject = (JSONObject) obj;
                return tmpJSONObject.toString();
            }).collect(Collectors.joining(","));
            return result;
        } catch (Exception e) {
            return "Error searching Baidu: " + e.getMessage();
        }
    }
}
````
3）我们需‍要获取 API K⁡ey 来调用网页搜‏索:
![img_2.png](img_2.png)
4）在配置文件中添加 API Key：
````
# searchApi
search-api:
  api-key: 你的 API Key
````
5）编写单元‍测试代码，读取配置文件⁡中的密钥来创建网页搜索‏工具： 
````
@SpringBootTest
public class WebSearchToolTest {

    @Value("${search-api.api-key}")
    private String searchApiKey;

    @Test
    public void testSearchWeb() {
        WebSearchTool tool = new WebSearchTool(searchApiKey);
        String query = "程序员鱼皮编程导航 codefather.cn";
        String result = tool.searchWeb(query);
        assertNotNull(result);
    }
}
````
## 网页抓取
网页抓取工具的作用是根据网址解析到网页的内容。
1）可以使‍用 jsoup 库⁡实现网页内容抓取和‏解析，首先给项目添؜加依赖：
````
<dependency>
    <groupId>org.jsoup</groupId>
    <artifactId>jsoup</artifactId>
    <version>1.19.1</version>
</dependency>
````
2）编写网‍页抓取工具类:
````
public class WebScrapingTool {

    @Tool(description = "Scrape the content of a web page")
    public String scrapeWebPage(@ToolParam(description = "URL of the web page to scrape") String url) {
        try {
            Document doc = Jsoup.connect(url).get();
            return doc.html();
        } catch (IOException e) {
            return "Error scraping web page: " + e.getMessage();
        }
    }
}
````
3）编写单元测试代码：
````
@SpringBootTest
public class WebScrapingToolTest {

    @Test
    public void testScrapeWebPage() {
        WebScrapingTool tool = new WebScrapingTool();
        String url = "https://www.codefather.cn";
        String result = tool.scrapeWebPage(url);
        assertNotNull(result);
    }
}
````
3）编写单元测试代码：
````
@SpringBootTest
public class WebScrapingToolTest {

    @Test
    public void testScrapeWebPage() {
        WebScrapingTool tool = new WebScrapingTool();
        String url = "https://www.codefather.cn";
        String result = tool.scrapeWebPage(url);
        assertNotNull(result);
    }
}
````
## 终端操作
终端操作工‍具的作用是在终端执⁡行命令，比如执行 ‏python 命令来运؜行脚本。

1）可以通过 ‍Java 的 Proces⁡s API 实现终端命令执‏行，注意 Windows ؜和其他操作系统下的实现略﻿有区别）。工具类代码如下：
````
public class TerminalOperationTool {

    @Tool(description = "Execute a command in the terminal")
    public String executeTerminalCommand(@ToolParam(description = "Command to execute in the terminal") String command) {
        StringBuilder output = new StringBuilder();
        try {
            Process process = Runtime.getRuntime().exec(command);
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(process.getInputStream()))) {
                String line;
                while ((line = reader.readLine()) != null) {
                    output.append(line).append("\n");
                }
            }
            int exitCode = process.waitFor();
            if (exitCode != 0) {
                output.append("Command execution failed with exit code: ").append(exitCode);
            }
        } catch (IOException | InterruptedException e) {
            output.append("Error executing command: ").append(e.getMessage());
        }
        return output.toString();
    }
}
````
如果是 W‍indows 操作⁡系统，要使用下面这‏段代码，否则命令执؜行会报错：
````
public class TerminalOperationTool {

    @Tool(description = "Execute a command in the terminal")
    public String executeTerminalCommand(@ToolParam(description = "Command to execute in the terminal") String command) {
        StringBuilder output = new StringBuilder();
        try {
            ProcessBuilder builder = new ProcessBuilder("cmd.exe", "/c", command);
//            Process process = Runtime.getRuntime().exec(command);
            Process process = builder.start();
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(process.getInputStream()))) {
                String line;
                while ((line = reader.readLine()) != null) {
                    output.append(line).append("\n");
                }
            }
            int exitCode = process.waitFor();
            if (exitCode != 0) {
                output.append("Command execution failed with exit code: ").append(exitCode);
            }
        } catch (IOException | InterruptedException e) {
            output.append("Error executing command: ").append(e.getMessage());
        }
        return output.toString();
    }
}
````
2）编写单元测试代码：
````
@SpringBootTest
public class TerminalOperationToolTest {

    @Test
    public void testExecuteTerminalCommand() {
        TerminalOperationTool tool = new TerminalOperationTool();
        String command = "ls -l";
        String result = tool.executeTerminalCommand(command);
        assertNotNull(result);
    }
}
````
## 资源下载
资源下载工具的作用是通过链接下载文件到本地。

1）使用 Hutool 的 HttpUtil.downloadFile 方法实现资源下载。资源下载工具类的代码如下：
````
public class ResourceDownloadTool {

    @Tool(description = "Download a resource from a given URL")
    public String downloadResource(@ToolParam(description = "URL of the resource to download") String url, @ToolParam(description = "Name of the file to save the downloaded resource") String fileName) {
        String fileDir = FileConstant.FILE_SAVE_DIR + "/download";
        String filePath = fileDir + "/" + fileName;
        try {
            // 创建目录
            FileUtil.mkdir(fileDir);
            // 使用 Hutool 的 downloadFile 方法下载资源
            HttpUtil.downloadFile(url, new File(filePath));
            return "Resource downloaded successfully to: " + filePath;
        } catch (Exception e) {
            return "Error downloading resource: " + e.getMessage();
        }
    }
}
````
2）编写单元测试代码：
````
@SpringBootTest
public class ResourceDownloadToolTest {

    @Test
    public void testDownloadResource() {
        ResourceDownloadTool tool = new ResourceDownloadTool();
        String url = "https://www.codefather.cn/logo.png";
        String fileName = "logo.png";
        String result = tool.downloadResource(url, fileName);
        assertNotNull(result);
    }
}
````
## PDF 生成
PDF 生‍成工具的作用是根据⁡文件名和内容生成 ‏PDF 文档并保存؜。

可以使用 itext 库 实现 PDF 生成。需要注意的是，itext 对中文字体的支持需要额外配置，不同操作系统提供的字体也不同，如果真要做生产级应用，建议自行下载所需字体。

不过对于学‍习来说，不建议在这里⁡浪费太多时间，可以使‏用内置中文字体（不引؜入 font-asi﻿an 字体依赖也可以使用）：
````
// 使用内置中文字体
PdfFont font = PdfFontFactory.createFont("STSongStd-Light", "UniGB-UCS2-H");
document.setFont(font);
````
1）给项目添加依赖：
````
<!-- https://mvnrepository.com/artifact/com.itextpdf/itext-core -->
<dependency>
    <groupId>com.itextpdf</groupId>
    <artifactId>itext-core</artifactId>
    <version>9.1.0</version>
    <type>pom</type>
</dependency>
<!-- https://mvnrepository.com/artifact/com.itextpdf/font-asian -->
<dependency>
    <groupId>com.itextpdf</groupId>
    <artifactId>font-asian</artifactId>
    <version>9.1.0</version>
    <scope>test</scope>
</dependency>
````
2）编写工具类实现代码：
````
public class PDFGenerationTool {

    @Tool(description = "Generate a PDF file with given content")
    public String generatePDF(
            @ToolParam(description = "Name of the file to save the generated PDF") String fileName,
            @ToolParam(description = "Content to be included in the PDF") String content) {
        String fileDir = FileConstant.FILE_SAVE_DIR + "/pdf";
        String filePath = fileDir + "/" + fileName;
        try {
            // 创建目录
            FileUtil.mkdir(fileDir);
            // 创建 PdfWriter 和 PdfDocument 对象
            try (PdfWriter writer = new PdfWriter(filePath);
                 PdfDocument pdf = new PdfDocument(writer);
                 Document document = new Document(pdf)) {
                // 自定义字体（需要人工下载字体文件到特定目录）
//                String fontPath = Paths.get("src/main/resources/static/fonts/simsun.ttf")
//                        .toAbsolutePath().toString();
//                PdfFont font = PdfFontFactory.createFont(fontPath,
//                        PdfFontFactory.EmbeddingStrategy.PREFER_EMBEDDED);
                // 使用内置中文字体
                PdfFont font = PdfFontFactory.createFont("STSongStd-Light", "UniGB-UCS2-H");
                document.setFont(font);
                // 创建段落
                Paragraph paragraph = new Paragraph(content);
                // 添加段落并关闭文档
                document.add(paragraph);
            }
            return "PDF generated successfully to: " + filePath;
        } catch (IOException e) {
            return "Error generating PDF: " + e.getMessage();
        }
    }
}
````
上述代码中，为了实现‍方便，我们是直接保存 PDF 到本地文件系⁡统。此外，你还可以将生成的文件上传‏到对象存储服务，然后返回可访问的 UR؜L 给 AI 去输出；或者将本地文件临﻿时返回给前端，让用户直接访问。

3）编写单元测试代码：
````
@SpringBootTest
public class PDFGenerationToolTest {

    @Test
    public void testGeneratePDF() {
        PDFGenerationTool tool = new PDFGenerationTool();
        String fileName = "编程导航原创项目.pdf";
        String content = "编程导航原创项目 https://www.codefather.cn";
        String result = tool.generatePDF(fileName, content);
        assertNotNull(result);
    }
}
````
## 集中注册
开发好了这么多工具类后，结合我们自己的需求，可以给 AI 一次性提供所有工具，让它自己决定何时调用。所以我们可以创建 工具注册类，方便统一管理和绑定所有工具。

代码如下：
````
@Configuration
public class ToolRegistration {

    @Value("${search-api.api-key}")
    private String searchApiKey;

    @Bean
    public ToolCallback[] allTools() {
        FileOperationTool fileOperationTool = new FileOperationTool();
        WebSearchTool webSearchTool = new WebSearchTool(searchApiKey);
        WebScrapingTool webScrapingTool = new WebScrapingTool();
        ResourceDownloadTool resourceDownloadTool = new ResourceDownloadTool();
        TerminalOperationTool terminalOperationTool = new TerminalOperationTool();
        PDFGenerationTool pdfGenerationTool = new PDFGenerationTool();
        return ToolCallbacks.from(
            fileOperationTool,
            webSearchTool,
            webScrapingTool,
            resourceDownloadTool,
            terminalOperationTool,
            pdfGenerationTool
        );
    }
}
````
##  使用工具
在 Lov‍eApp 类中添加⁡工具调用的代码，通‏过 tools 方؜法绑定所有已注册的﻿工具：
````
@Resource
private ToolCallback[] allTools;

public String doChatWithTools(String message, String chatId) {
    ChatResponse response = chatClient
            .prompt()
            .user(message)
            .advisors(spec -> spec.param(CHAT_MEMORY_CONVERSATION_ID_KEY, chatId)
                    .param(CHAT_MEMORY_RETRIEVE_SIZE_KEY, 10))
            // 开启日志，便于观察效果
            .advisors(new MyLoggerAdvisor())
            .tools(allTools)
            .call()
            .chatResponse();
    String content = response.getResult().getOutput().getText();
    log.info("content: {}", content);
    return content;
}
````


