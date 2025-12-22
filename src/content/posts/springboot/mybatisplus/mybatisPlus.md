---
title: mybatisPlus
published: 2024-10-08
description: ''
image: './cover.png'
tags: ["springboot"]
category: 'springboot'
draft: false 
lang: ''
---
## Mybatisplus的介绍和使用

## 1.mybatisPlus快速入门

### 1.mybatisplus的使用

1.引入依赖
![img.png](img.png)
2.写mapper接口，继承basemapper，这样mapper就有了增删改查这些方法
![img_1.png](img_1.png)
![img_2.png](img_2.png)
### 2.常用注解

mybatisplus是通过以下规则知道我们的mapper是要增删改查那张表的
![img_3.png](img_3.png)
如果user表名不一样，可以通过@tableName注解去指定对应的表，mybatispuls识别的类中一般默认主键字段名字叫id，如果你的表的主键名字不叫id，请用@TableId注解指定表的主键，该注解还可以设置表的主键的变化
![img_4.png](img_4.png)
如果类的字段满足以下条件，也要指定类对应的表字段
![img_5.png](img_5.png)
### 3.常见配置
![img_6.png](img_6.png)
type-aliases-package为配置类型别名扫描包，如果配置了这个以后写类型路径就不用写这么麻烦的全路径了，而是配置路径后面的路径即可

mapper-locations：指的是扫描的xml包的位置，默认扫描resource/mapper路径下的xml文件，如果配置成上面这个，则默认为resource/mapper/xx/下的xml文件，我们一般使用默认的就行

map-underscore-to-camel-case:表示是否开启驼峰转下划线（domain=>数据库）去查找数据库对应的表，如果不开启就是默认完全相同，一般项目我都不选择开

cache-enabled表示是否开启二级缓存，就是在不同sqlsession执行相同的mapper的查询方法要不要写入内存进行缓存

id-type表示id的生成方法，注意我们的@TableId（Idype=）注解和该注解的功能是一样的，且注解的优先级高于该全局配置

uopdate-strategy：表示update方法是否只更新非空的，null的字段不改变，我们一般都开



## 2.mybatispuls的核心功能

### 1.条件构造器

![img_7.png](img_7.png)
这些方法的传参是一个Wrapper条件构造器，我们可以通过写条件构造器来实现复杂的查询
![img_8.png](img_8.png)
Wrapper条件构造器定义了一个抽象的API，其核心是一个抽象类 `AbstractWrapper `，其子类 `QueryWrapper `和 `LambdaQueryWrapper `等提供了对SQL条件的不同表达方式。这些构造器允许动态地构建出各种复杂的查询条件，如相等、不等、大于、小于、模糊匹配、IN查询等。此外，它们还支持条件组合，例如 `and `、 `or `以及括号分组等。我们主要用的就是QueryWrapper和LambdaQueryWrapper(

查询的)还有updateWrapper和LambdaUpdateWrapper(更新的)

#####  Wrapper之间的对比分析

在选择使用 `QueryWrapper `还是 `LambdaQueryWrapper `时，我们需要根据实际的使用场景和需求来决定。以下是两者之间的对比分析：

- **代码可读性** ： `LambdaQueryWrapper `由于使用了Lambda表达式，代码可读性更高，更适合复杂的查询条件。
- **便捷性** ： `LambdaQueryWrapper `的链式调用语法更加直观，减少了代码的冗余。
- **安全保护** ： `LambdaQueryWrapper `通过编译器对字段名的检查，提供了更高的安全保障。
- **性能开销** ：在大多数情况下，性能开销基本可以忽略不计，但在极端情况下， `QueryWrapper `可能会略胜一筹。

```
// LambdaQueryWrapper示例
LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<>();
lqw.eq(User::getName, "张三").gt(User::getAge, 30);
 
// QueryWrapper示例
QueryWrapper<User> qw = new QueryWrapper<>();
qw.eq("name", "张三").gt("age", 30);
```

####  更新条件的构造

更新操作同样可以利用Wrapper条件构造器来完成。这里我们使用LambdaUpdateWrapper来举例：

```java
LambdaUpdateWrapper<User> updateWrapper = new LambdaUpdateWrapper<>();
updateWrapper.set(User::getAge, 18); // 设置年龄为18岁
updateWrapper.eq(User::getName, "张三"); // 如果姓名为"张三"
userMapper.update(null, updateWrapper);
```

####  多条件链式组合

通过链式调用，我们可以轻松地将多个条件组合起来，形成更为复杂的查询语句：
![img_9.png](img_9.png)
这些是构造的条件，可以根据字母去猜，比如gt是greater than(大于),lt是less than（小于）

### 2.自定义sql
![img_10.png](img_10.png)
**例子:**
![img_11.png](img_11.png)
因为有些公司是不允许在业务代码层面写类似：blance=blance-20这种原生sql语句的，所以我们可以通过自定义sql和wrapper构造where条件一起自定义sql,自定义sql和mybatis一样在对应的mybatispuls中写
![img_12.png](img_12.png)
### 3.Iservice接口

mybatispuls提供了一个Iservice接口，只要继承该类就可以有一堆操作serivce对应entity的增删改查，但这些接口都需要实现，因为有一个ISeriviceImp实现了Iservice接口的全部方法,我们只要接口继承Iservice,接口实现类继承IserviceImp类
![img_13.png](img_13.png)
![img_14.png](img_14.png)
**批处理**
![img_15.png](img_15.png)
![img_16.png](img_16.png)
如果是十万次for循环一次一次添加一条，每次添加一条都是一次网络请求，而网络请求是非常耗时的，所以这种方法是最慢的

如果是每add一千个user然后批处理提交一次的，这种网络请求从十万次变成了一千次，大大减少了网络请求的次数，但是因为他批处理提交是将list的一千条数据编译成一千条sql语句去执行的，所以还是会慢一些

最后一种方法是在yml文件数据库的配置后面拼上&然后上图那个，这种方法是让批处理在编译的时候编译成一条sql语句，所以这种方法的耗时是最少的

## 3.mybatispuls扩展功能

### 1.代码生成

在idea中安装插件mybatisX，然后在数据库对应表中右键点code generator,即可生成表对应的generate文件，里面有表对应的实体类，继承了basemapper的对应对象的XXmapper，继承了iService的对应XXservice接口和继承了IserviceImp的对应的XXServiceImp实体类和在resource/mapper目录下生菜对应的xml文件。然后我们复制粘贴到我们原来对应包即可。

### 2.DB静态工具
![img_17.png](img_17.png)
mybatispuls个我们提供了一个静态工具DB,能够实现和Iservice接口一样的业务增删改查
### 3.逻辑删除
![img_18.png](img_18.png)
![img_19.png](img_19.png)


```
@TableLogic
```

我们也可以通过给表对应的类加一个注解来说明该字段是逻辑删除

```
@TableField(exist = false)
private static final long serialVersionUID = 1L;
```

我们给类对象加序列化的时候也要记住写一个注解表明该字段并不是数据库对应的字段

### 4.枚举处理器
![img_20.png](img_20.png)
遇到这种值为枚举的字段，我们可以设置该类的值为一个枚举值，枚举是可以进行==判断的，这样使用枚举我们就可以不会忘了哪个数字是对应的哪个状态什么的。我们也可以通过枚举类的get方法来获取枚举对应的值或者信息

**但这样会有个问题：**
![img_21.png](img_21.png)
我们数据库表的字段和类的字段的类型是不同的，那怎么实现int<==>枚举类型呢，这里mybatispuls给我们提供了一个处理器：**MybatisEnumTypeHandler**，那怎么用呢？

**第一步：**
![img_22.png](img_22.png)
第一，我们要用@EnumValue指名我们的枚举类的哪个字段和数据库字段进行映射了

**第二步：**
![img_23.png](img_23.png)
还有就是我们枚举类返回的时候默认是返回枚举字段“XX”,由于spring返回给前端的数据底层是通过springmvc控制的，所以我们可以使用一个注解@JsonValue还指定该枚举类返回给前端的数据是什么。
![img_24.png](img_24.png)
![img_25.png](img_25.png)
### 5.json处理器

如果我们数据库有一个json存储的数据，怎么实现对象与json数组的自动转化呢？
![img_26.png](img_26.png)
**这里我们还是两步：**
![img_27.png](img_27.png)
**第一步：**给对象要映射成json的字段加一个`typeHeader=jacksonTypeHeader.class`

**第二步：**给表对应类加一个@TbaleName(autoResultMap=true)开启自动映射

## 4.插件功能
![img_28.png](img_28.png)
mybatispuls插件实现了很多的插件，不过我们这里主要用的是分页插件**PaginationInnerterceptor**
![img_29.png](img_29.png)
首先，我们要创建一个配置类
![img_30.png](img_30.png)
然后我们就可以使用page来进行分页了
![img_31.png](img_31.png)
**案例：**
![img_32.png](img_32.png)
![img_33.png](img_33.png)
上述代码在**创建page条件对象**和将**返回的page结果转化为dto**这两段代码与业务没什么太大关系，而且具有一定的通用性，所以我们可以其实现为一个通用方法
![img_34.png](img_34.png)
具体实现就不多说了，挺简单的。 