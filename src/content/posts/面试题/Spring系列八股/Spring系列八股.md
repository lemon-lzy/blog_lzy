---
title: Spring系列八股
published: 2025-12-01
description: ''
image: '../cover.png'
tags: ["八股"]
category: '八股'
draft: false 
lang: ''
---

# SpringIOC
## 定义：
IoC指的是将对象的创建和管理交给spring容器，而不是自己new。

我们只需要在xml配置bean，spring容器在初始化时就会读取这些配置，然后通过反射生成对应的bean实例。当需要某些对象时，spring就会从容器中拿到对应的bean对象并通过依赖注入赋值给我们。
##  好处：
1. 实现对象之间的解耦
2. 可扩展性强，将对象的创建和管理交给IOC容器管理，可以在不修改业务的情况下增加额外功能，比如AOP，日志，事务。

## DI
依赖注入指的是由容器负责依赖对象的注入（比如a依赖b,将被依赖对象b注入到目标对象a），是实现IOC的一个核心的机制，他有四种依赖注入方式:
- 构造器注入
- setter方法注入
- 还有字段注入（比如AutoWired）

# AOP
### 定义：
AOP指的面向切面编程，是在不改变业务的情况下，动态的将一些功能增强到业务当中，比如日志打印。
### 实现aop有五大核心：
- 切面（功能模块，比如日志切面）
- 通知（执行时机，比如执行方法后）
- 连接点（可能被增强的节点）
- 切入点（真正被增强的节点）
- 织入（增强的过程）。
### 底层实现：
- AOP是动态织入，运行的时候通过动态代理实现（JDK代理或者CGLib代理）
- Spring默认使用JDK动态代理
- springBoot2版本使用CGLib代理。
#### 两者的区别:
- JDK动态代理是基于接口进行代理，代理类必须实现一个或多个接口
- CGLib动态代理是基于子类进行代理，代理类无需实现接口

# 通知类型
- **前置通知**:方法执行前通知
- **后置通知**:方法执行成功后通知
- **后置异常通知**:方法执行异常后通知
- **后置最终通知**:方法执行成功与否都进行通知
- **环绕通知**:方法执行前后进行通知

# Spring框架中都用到了哪些设计模式？
- **工厂模式**：BeanFactory就是简单工厂模式的体现，用来创建对象的实例；
- **单例模式**：Bean默认为单例模式。
- **代理模式**：Spring的AOP功能用到了JDK的动态代理和CGLIB字节码生成技术；
- **模板方法**：用来解决代码重复的问题。比如.RestTemplate,RabbitTemplate, JpaTemplate。
- **适配器模式**: spring MVC 的 HandlerAdapter
- **观察者模式**: Spring Event事件驱动

# bean的定义：
任何被Spring容器管理的对象都叫作bean

# beanFatory
BeanFactory是bean工厂，由IOC底层容器实现，用来创建和管理bean

# Bean的注入方式有哪些
- **构造函数注入**：通过类的构造函数来注入依赖项。
- **Setter注入**：通过类的Setter方法来注入依赖项。
- **Field(字段）注入**：直接在类的字段上使用注解（如@Autowired或@Resource)来注入依赖

# bean的作用域有多少种？
bean的作用域有6种:
1. **singleton**:单例,ioc容器只有一个
2. **protype**:原型，ioc容器有多个
3. **application**:每个web应用创建一个
4. **session**:每次会话创建一个
5. **request**:每次请求创建一个，请求完销毁
6. **websocket**:每次长连接创建一个

# bean的生命周期 
![img.png](img.png)
1. 先实例化bean
2. 对bean进行属性注入
3. 检查bean有没有实现aware接口，有将对应的资源给bean,比如bean的名字，工厂。
4. 调用所有BeanPostProcess的PostProceessBeforeInitialization方法进行前置处理。
5. 初始化bean
6. 调用所有BeanPostProcess的PostProceessAfterInitialization方法进行后置处理。比如AOP.
7. 销毁bean

# bean注入方式？
## 1. XML 配置方式
````
-<bean id="..." class="..."> 
````
- 传统方式，现在很少写全量 XML，但仍需知道。

## 2. @Component 系列注解（扫描方式）

- 注解：@Component 、@Service 、@Controller 、@Repository
- 依赖：必须在 @ComponentScan 扫描包下
- 特点：被动被扫描，适合自己项目里的类

## 3. @Configuration + @Bean（手动注册）
````
@Configuration
public class Config {
@Bean
public UserService userService() {
return new UserService();
}
}
````
- 配置类要被扫描到
- 适合：第三方类、需要复杂构造的 Bean

## 4. @Import 方式（主动导入）

- 写在 @Configuration 配置类上
- 配置类必须被扫描，但被导入的类可以不在扫描包内

## 一句话整体区分（好记）

- XML：老方式
- @Component：靠扫描，被动注册
- @Bean：在配置类里手动 new
- @Import：主动把类“拉”进容器，不受包扫描限制

# 自动装配方式？
Spring装配指的是SpringIoc容器根据bean的类型或者名称自动注入依赖的bean
- 根据bean类型进行装配
- 根据bean名称进行装配

# 循环依赖
## 一、循环依赖定义
指两个或者多个bean相互引用，形成闭环。
## 二、Spring解决方案
Spring采用三级缓存解决循环依赖问题，核心思想：用半成品Bean的地址完成依赖赋值，无需等待对方完整初始化后再赋值。

1. 一级缓存：存放完全初始化完成的Bean。
2. 二级缓存：存放已实例化但未完成初始化的Bean（仅持有Bean地址）。
3. 三级缓存：存放Bean的工厂方法，用于延迟获取早期的bean对象

## 三、三级缓存执行流程

循环依赖完整流程（A ↔ B）

- 实例化Bean A：调用构造方法实例化A，实例化完成后将Bean A的工厂 ObjectFactory 加入三级缓存。 
- ObjectFactory 作用：延迟初始化，后续可获取Bean A的早期对象。 
- 依赖注入Bean A：对A进行依赖注入，发现A依赖B。检查一、二级缓存，未找到Bean B。 
- 实例化Bean B：创建B，先实例化，再将B的工厂加入三级缓存。 
- 依赖注入Bean B：检查一、二级缓存，未找到A，调用三级缓存中A的工厂，根据是否需要代理获取早期的bean代理对象(就是刚才实例化的那个，加了层代理而已)或者原始对象(就是刚才实例化的那个)，完成B的依赖注入。 
- Bean B初始化完成：B初始化后，将其加入一级缓存。 
- 继续初始化Bean A：从一级缓存获取已初始化完成的B，完成A的依赖注入，将A放入一级缓存。

## 四、使用二级缓存可以吗？
### 实际方案：使用三级缓存(Factory)

1. 实例化 Bean A
2. 放入三级缓存（工厂 ObjectFactory）
3. 判断是否有循环依赖：
- 否（大多数情况）：工厂不被调用 → 初始化后创建代理（标准生命周期）
- 是（A依赖B，B依赖A）：工厂被触发 → 提前创建代理（特殊处理）
### 总结

三级缓存就是为了在解决循环依赖和维持标准生命周期之间做一个平衡。它让我们可以只在真正发生循环依赖的时候，才去提前创建代理对象；否则，就按兵不动，维持原样。
## 五、三级缓存解决循环依赖的条件

1. 涉及的Bean均为单例对象。
2. 字母顺序在前的Bean（如先创建的A）必须通过set方法注入属性（而非构造方法注入）。

## 六、三级缓存失效场景

1. 构造器注入循环依赖：A的构造方法依赖B，B的构造方法依赖A，对象无法实例化，三级缓存失效。
2. bean是原型（prototype）作用域：原型Bean每次获取都会创建新实例，A依赖新B、B依赖新A，形成无限循环，三级缓存失效。

## 七、失效场景解决方法

可通过@Lazy注解实现懒加载，解决上述循环依赖失效问题。

# SpringMVC说一下？
Spring MVC 是 Spring的基础上 对 MVC 的实现，在传统 MVC 基础上做了扩展：

- 将 Model 层拆分为业务模型 Service 和数据模型 Repository；
- 将 Controller 层拆分为前端控制器 DispatcherServlet 和后端控制器 Controller；
- 相比之前 Servlet+JSP 每个url都要写一个servlet的模式，它和 Servlet 解耦，简化了 Web 开发，不用再写大量 Servlet。

# SpringMVC和Spring的关系

Spring 是基础，Spring MVC 是构建在 Spring 之上的 Web 层框架，依赖 Spring 的 IOC、DI、AOP 等能力来处理 Web 请求。

# Spring的好处？
1. 第一个好处就是将对象的创建，管理和依赖注入交给IOC容器，而不需要我们自己new。
2. 第二个好处就是有AOP,可以在不改变业务逻辑的情况下动态增强功能。

# pring事务管理方式
- 编程式事务：通过 TransactionTemplate 或者TransactionManager 手动管理事务
- 声明式事务：@Transactiona, 通过aop实现的

# Spring事务隔离级别？
Spring的隔离级别有:

读未提交，读已提交，可重复读（默认），串行化。

如果数据库和spring隔离级别不一样以spring为主

# Spring事务传播行为？
### 作用：
事务传播行为指的是:事务方法被另一个方法调用的时候，事务要怎么传播
## 种类
### 事务的解决方案（A套B）:
- 融入事务:B直接用A的事务。缺点是B错A会回滚
- 挂起事务:A执行事务B挂起，B执行事务A挂起，两者事务单独执行。
- 嵌套事务:B错A不回滚,A错B回滚（大影响小，小不会影响大）

## 事务传播机制（7种）
### 融入三种:
调用方法有则事务融入，没有则创建新事务，不开启事务和抛出异常

### 挂起两种
新建事务，或者不开启事务，如果调用方法有事务则挂起调用方法事务

### 嵌套一种:
如果调用方法有事务就嵌套事务（即子事务回滚时父事务不影响，父事务回滚时子事务跟着回滚），没有则新建事务。

### 永无事务:
不开启事务，如果调用方法有事务则抛出异常

# Spring事务什么时候失效？
### 修饰符相关（3种）
- 1.事务方法是static修饰的静态方法（属于类）
- 2.事务方法被final修饰（无法继承，也就无法代理）
- 3.事务方法不是public修饰（非public也无法代理）

### 类自身（2种）
- 4.事务方法的类不是spring容器管理（代理在bean初始化后）
- 5.事务方法是在类内调用（类内调用的方法，this不会走动态代理）

## 异常（2种）
- 6.事务方法抛出的异常不在范围内（比如@Transational注解默认error和runtimexception回滚）如果是**IOException**就会失效
- 7.事务方法抛出的异常被try catch捕获

## 设定错误
- 8.事务传播方式设定错误（比如父事务挂起，开启新事务）
- 9.数据库不支持事务

# @Qualifier注解作用
@Qualifier 就是用来指定要注入的 Bean 名称，从而解决同一类型多个 Bean 实例时的注入冲突问题。

搭配：
````
- @Autowired + @Qualifier("bean名字")
````
就可以精准指定注入哪一个 Bean。

# @Bean和@Component的区别？

- **@Bean：**配合@Configuration注解标注在方法上，将方法返回值注入Spring容器。
- **@Component：**标注在类上，被Spring扫描后将类实例注入容器。

# #@Component系列注解的区别？
- Component系列注解指的是：@Component,@Controller,@Service,@Repository
- 本质没有任何区别，都是为了更好的区分业务

# 单例Bean是否有并发安全问题？
单例bean有并发安全问题，只要bean里面有可修改的实例变量，在多线程同时修改的话就会出现并发安全问题。

### 解决:
- 可以用ThreadLocal来存变量，让变量变成线程私有。
- 可以通过加锁来保证同一时间只有一个线程能够访问变量。

# @Primary注解的作用？
@primary注解标注在bean实例上，意义是在多实例bean冲突时，优先用标注@primary注解的bean

# @value注解的作用？
@Value可以给标注字段赋值，可以直接写一个默认值或者从配置文件读取值给他

# @RequestBody和@responseBody的作用？
![img_1.png](img_1.png)

# @PathVariable的作用？
@PathVariable是用来读取路径的参数

# @valid和@validated的区别？
- valid不支持分组校验
- validated支持(将字段校验分不同的组，不同情况下可以使用不同组进行校验)
### @valid
````
import javax.validation.Valid;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;

// 嵌套对象
class Address {
    @NotBlank(message = "地址不能为空")
    private String detail;
}

// 主实体
class User {
    @NotBlank(message = "用户名不能为空")
    private String username;

    // @Valid 支持嵌套校验（@Validated 本身做不到）
    @Valid
    @NotNull(message = "地址不能为空")
    private Address address;
}

// Controller
@RestController
@RequestMapping("/valid")
public class ValidController {

    @PostMapping("/add")
    public String add(@Valid @RequestBody User user, BindingResult result) {
        if (result.hasErrors()) {
            return result.getFieldError().getDefaultMessage();
        }
        return "校验成功";
    }
}
````
### @validated
````
import org.springframework.validation.annotation.Validated;
import javax.validation.constraints.NotBlank;

// 分组定义
class Group {
    public interface Add {}
    public interface Update {}
}

// 实体
class User {
    @NotBlank(message = "ID不能为空", groups = Group.Update.class)
    private String id;

    @NotBlank(message = "用户名不能为空", groups = Group.Add.class)
    private String username;
}

// Controller
@RestController
@RequestMapping("/validated")
public class ValidatedController {

    // 新增分组校验
    @PostMapping("/add")
    public String add(@Validated(Group.Add.class) @RequestBody User user) {
        return "新增校验成功";
    }

    // 更新分组校验
    @PostMapping("/update")
    {
        return "更新校验成功";
    }
}
````

# @Schedule注解的作用
schedule注解用来做定时任务，可以配合cron表达式(秒，分，时，日，月，周，年)，得在启动类上面加一个EnableSchedule

# @condition注解作用
@condition注解用来根据条件决定是否加载bean

# @Async的原理是什么？
- @Async 实现方法异步调用，底层基于 AOP 动态代理 + 线程池。
- Spring 会为标了 @Async 的类/方法生成代理对象，调用方法时拦截，把方法包装成任务提交到线程池，主线程直接返回，不阻塞等待。

### 默认线程池是什么

- Spring Boot 2.x 以后，@Async 默认用 SimpleAsyncTaskExecutor
- 它不是真正的池，来一个任务就开一个新线程，无上限复用。

### 默认线程池的弊端

- 无限创建线程：高并发下会创建大量线程，导致 OOM 或服务器资源耗尽
- 无线程复用：每次都 new Thread，频繁创建销毁开销大
- 无拒绝策略：任务堆积没有保护机制

### 所以生产上要怎么做

必须重写自定义线程池：

- 配置核心线程数、最大线程数、队列容量、空闲时间、拒绝策略
- 实现 AsyncConfigurer 或直接定义 ThreadPoolTaskExecutor 交给 Spring 管理

# @Async失效场景：
其实本质上是AOP失效，有以下几种场景:

### 修饰符相关（3种）
1. 方法不是public修饰（非public也无法代理）
2. 方法是static修饰的静态方法（属于类）
3. 方法被final修饰（无法继承，也就无法代理）

### 类自身（2种）
4. 方法的类不是spring容器管理（代理在bean初始化后）
5. 方法是在类内调用（类内调用的方法，this不会走动态代理）

### 其他（2种）
6. 异步方法返回类型错误
7. 启动类没有用@EnableAsync注解

# RestFul风格说一下？
RESTful 是一种 API 设计风格，核心是用 URL 表示资源，用 HTTP 方法表示对资源的操作，而不是用接口名描述行为。

1. URL 代表资源（如 /user 、/order/101）
2. HTTP 方法代表动作：
- GET：查询
- POST：新增
- PUT：全量修改
- DELETE：删除

响应用状态码表示结果，接口更统一、语义更清晰。

比如 GET /user/{id} 表示获取指定id的用户

# 什么是SpringBoot?
SpringBoot是简化spring应用开发的框架。采用约定大于配置的思想，将Spring的经常需要的配置自动给我们完成，如果需要更改只需要在配置文件中进行修改就行。（比如约定默认端口号server.port=8080，如果不想这个端口直接在配置文件中修改。）

### 它主要体现在：
- SpringBoot Starter起步依赖，帮我们导入spring开发常用jar包，通过parent帮我们管理版本，避免版本冲突。
- SpringBoot的自动装配，可以减少spring复杂的xml配置（mysql为例）
- SpringBoot内置的单独的Tomcat容器，不需要我们手动去将应用部署到Web容器中

# SpringBoot核心特点;
- **start起步依赖**:帮我们导入spring开发常用jar包
- **自动装配**:通过@enableAutoConfiguration注解扫描Meta/spring.fatories文件，读取所有的自动配置类，通过注解条件决定加载配置类
- **内嵌tomact服务器**:不用将应用打成war包部署到外部web容器
- **外部化配置**:可以通过yml文件来修改配置监控和管理

# SpringBoot如何实现自动装配的？
- 通过@EnableAutoConfiguration注解实现自动配置。
- 该注解会通过@import注解导入一个AutoConfigurationImportSelector类
- 该类会通过springFactoriesLoader去扫描的所有的META-INF/spring.factories&nbsp文件
- 找到所有的自动配置类，然后将满足条件注解的自动配置类加载到spring容器中。后续我们就可以直接注入自动装配的bean.

# Spring Boot 中 application.properties 和 application.yml 的区别是什么？
- 书写格式不同。 
- 优先级不同：properties比yaml优先加载

# 如何在 Spring Boot 中定义和读取自定义配置？
- 可以使用@Value注解
- @ConfigurationProperties注解

# Spring Boot 配置文件加载优先级你知道吗？
- 命令行最优先
- jar包外比jar包内优先
- p带profile比不带profile优先
- roperties比yml优先

# Spring Boot 打成的 jar 和普通的 jar 有什么区别 ?
springboot打的jar包的比普通多了个内嵌服务器，不需要部署在外部web容器就可以直接运行

# 怎么理解SpringBoot的start？
start可以看成一组依赖集合，引入一个start就等于引入了这一组依赖
### 如何自定义start?
- 首先先创建一个项目，写一个自动配置类（@Configuation）
- 然后再写一个属性类（用于别人导入start后可以动态修改配置,@ConfiguationProperties注解声明）
- 最后在Meta-Info文件下的spring.factories文件下写入自动配置类的路径.
- 最后install打包到本地仓库，别的项目就可以通过pom文件进行导入了

# Spring Boot 如何处理跨域请求（CORS）？
## 跨域定义:
跨域指的是浏览器在访问后端时，会检查协议，域名 端口一不一致，如果有有不一致的说明发生了跨域
## 解决办法:
### 局部跨域:
使用@CrossOrigin注解
### 全局跨域:
使用corsFilter过滤器
