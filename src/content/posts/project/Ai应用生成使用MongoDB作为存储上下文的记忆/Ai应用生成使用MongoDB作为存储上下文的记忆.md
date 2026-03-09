---
title: Ai应用生成使用MongoDB作为存储上下文的记忆
published: 2026-03-09
description: ''
image: '../cover.png'
tags: ["project"]
category: 'project'
draft: false 
lang: ''
---

# Ai应用生成使用MongoDB作为存储上下文的记忆
1 首先引入依赖
````
        <!-- MongoDB 依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-mongodb</artifactId>
        </dependency>

````
2 定义一个储蓄类，用于完成上线文的存储（MongoChatMemoryStore）

主要作用是实现ChatMemoryStore接口，重写以下三个方法，完成上下文的存储。
为了存储方便，我们可以定义一个ChatMessages的实体类用于映射MongoDB的属性。
````
@Data
@AllArgsConstructor
@NoArgsConstructor
@Document("chat_history")
public class ChatMessages {
    //唯一标识，映射到 MongoDB 文档的 _id 字段
    @Id
    private ObjectId messageId;

    private String content; //存储当前聊天记录列表的json字符串
    
    // 当然还可以加字段，不过目前够用
}
````
![img.png](img.png)
````
@Component
@Slf4j
public class MongoChatMemoryStore implements ChatMemoryStore {
    private final MongoTemplate mongoTemplate;
    
    public MongoChatMemoryStore(MongoTemplate mongoTemplate) {
        this.mongoTemplate = mongoTemplate;
    }

    @Override
    public List<ChatMessage> getMessages(Object memoryId) {
        try {
            Query query = new Query(Criteria.where("memoryId").is(memoryId.toString()));
            ChatMessages chatMessages = mongoTemplate.findOne(query, ChatMessages.class);
            return chatMessages != null ? 
                ChatMessageDeserializer.messagesFromJson(chatMessages.getContent()) : 
                new ArrayList<>();
        } catch (Exception e) {
            log.error("Error retrieving messages for memoryId: {}", memoryId, e);
            return new ArrayList<>();
        }
    }

    @Override
    public void updateMessages(Object memoryId, List<ChatMessage> messages) {
        try {
            Query query = new Query(Criteria.where("memoryId").is(memoryId.toString()));
            Update update = new Update()
                .set("content", ChatMessageSerializer.messagesToJson(messages))
                .set("updatedAt", new Date());
            
            mongoTemplate.upsert(query, update, ChatMessages.class);
        } catch (Exception e) {
            log.error("Error updating messages for memoryId: {}", memoryId, e);
        }
    }

    @Override
    public void deleteMessages(Object memoryId) {
        try {
            Query query = new Query(Criteria.where("memoryId").is(memoryId.toString()));
            mongoTemplate.remove(query, ChatMessages.class);
        } catch (Exception e) {
            log.error("Error deleting messages for memoryId: {}", memoryId, e);
        }
    }
}

````
3 修改AI相关类（AiCodeGeneratorServiceFactory.java）
````

 @Resource
 private MongoChatMemoryStore mongoChatMemoryStore;
 
 @Bean
    public AiCodeGeneratorService aiCodeGeneratorService() {


        return AiServices.builder(AiCodeGeneratorService.class)
                .chatModel(chatModel)
                .streamingChatModel(openAiStreamingChatModel)
                // 根据 id 构建独立的对话记忆
                .chatMemoryProvider(memoryId -> MessageWindowChatMemory
                        .builder()
                        .id(memoryId)
                        .chatMemoryStore(mongoChatMemoryStore)
                        .maxMessages(20)
                        .build())
                .build();
    }

    @Bean
    public AiCodeGeneratorService aiToolsCodeGeneratorService() {

        return AiServices.builder(AiCodeGeneratorService.class)
                .chatModel(chatModel)
 
                .streamingChatModel(openAiStreamingChatModel)

                // 根据 id 构建独立的对话记忆
                .chatMemoryProvider(memoryId -> MessageWindowChatMemory
                        .builder()
                        .id(memoryId)
                        .chatMemoryStore(mongoChatMemoryStore)
                        .maxMessages(20)
                        .build())
                .tools(new FileWriteTool())
                .build();
    }
````
4 最后在配置文件在加入MongoDB的配置
````
spring:
  data:
    mongodb:
      uri: mongodb://admin:*****ad@192.168.56.10:27017/chat_history_db?authSource=admin
````
5 总结
在之前写代码中经常使用Redis作为缓存，对于存储持久化信息老感觉不怎么对胃口。故此在对比了Es\Mongo\Pgsql后选择MongoDb。