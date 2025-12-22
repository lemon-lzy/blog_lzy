---
title: Mybatis的使用
published: 2024-10-07
description: ''
image: './cover.png'
tags: ["springboot"]
category: 'springboot'
draft: false 
lang: ''
---
# Mybatis的使用

### 1.引言：传统JDBC的困境

数据库操作的痛点：在传统的JDBC编程中，开发者必须手写大量的重复代码来处理数据库连接、SQL执行、结果集映射和资源释放，这些重复性工作不仅增加了出错的几率，还大大降低了开发效率。
问题举例：例如，在每次查询数据库时，我们需要：
打开数据库连接。
执行SQL语句。
处理查询结果，映射成Java对象。
关闭数据库连接。

这一系列操作让开发者的工作更加复杂，尤其是在查询结构变化较多的项目中。代码示例：

```
Connection conn = null;
PreparedStatement stmt = null;
ResultSet rs = null;
try {
    conn = DriverManager.getConnection(DB_URL, USER, PASS);
    String sql = "SELECT id, name, age FROM users WHERE id=?";
    stmt = conn.prepareStatement(sql);
    stmt.setInt(1, userId);
    rs = stmt.executeQuery();
    if(rs.next()) {
        User user = new User();
        user.setId(rs.getInt("id"));
        user.setName(rs.getString("name"));
        user.setAge(rs.getInt("age"));
   }
} finally {
    // 关闭资源
}
```

这种代码繁琐冗长，难以维护，尤其当你需要做更多复杂的查询时，代码会变得非常臃肿。**那有没有一种方式能够简化这种操作？**——这时，MyBatis应运而生

### 2.MyBatis：简化数据库操作的框架

什么是MyBatis？
MyBatis是一个轻量级的Java持久层框架，它的设计目标是简化数据库操作，同时提供高度的灵活性。与Hibernate等ORM框架不同，MyBatis允许开发者直接编写SQL语句，但仍能享受到ORM的部分便利性，如自动结果映射等。

MyBatis如何解决传统JDBC的问题？

SQL与代码分离：MyBatis通过XML文件或注解将SQL语句与Java代码分离，减少了冗余代码。
自动映射：MyBatis可以将SQL查询结果直接映射到Java对象中，避免手动逐行处理ResultSet。
支持动态SQL：开发者可以根据查询条件动态生成SQL语句，减少硬编码。

**实际代码对比**：同样的数据库查询操作，在MyBatis中的写法：

```
<select id="getUserById" parameterType="int" resultType="User">
  SELECT id, name, age FROM users WHERE id = #{id}
</select>
```

Java代码：

```
User user = userMapper.getUserById(userId);
```



### 3.mybatis的基础配置与环境搭建

##### **项目结构**

- 创建一个基于Maven的Spring Boot项目，MyBatis可以通过其官方的Spring Boot Starter轻松与Spring Boot整合。

- 项目结构示例：

  ├── src
  │   ├── main
  │   │   ├── java
  │   │   │   └── com.example
  │   │   │       └── MyBatisApp.java
  │   │   ├── resources
  │   │   │   ├── application.yml
  │   │   │   └── mapper
  │   │   │       └── UserMapper.xml
  └── pom.xml

##### **Maven依赖管理**

- 首先，我们需要在`pom.xml`文件中添加相关的依赖，以便Spring Boot项目支持MyBatis。
- **依赖配置**

```
<dependencies>
  <!-- Spring Boot基础依赖 -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
  </dependency>

  <!-- MyBatis与Spring Boot集成 -->
  <dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.4</version>
  </dependency>

  <!-- MySQL数据库驱动 -->
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
  </dependency>

  <!-- 测试依赖 -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
  </dependency>
</dependencies>
```

##### **配置数据库连接信息**

- MyBatis通过[配置文件](https://so.csdn.net/so/search?q=配置文件&spm=1001.2101.3001.7020)来管理与数据库的连接。我们可以使用Spring Boot的`application.yml`文件进行配置。
- **`application.yml`配置**：

```
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: password
    driver-class-name: com.mysql.cj.jdbc.Driver

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.example.entity
```

**配置解释：**

spring.datasource: 配置数据库连接的URL、用户名和密码。
mybatis.mapper-locations: 指定Mapper XML文件的存放路径。
mybatis.type-aliases-package: 设置MyBatis实体类的包路径，方便后续自动映射

**编写MyBatis配置文件**：
MyBatis的核心配置文件是mybatis-config.xml，但当你使用Spring Boot与MyBatis整合时，很多全局配置可以通过Spring Boot的配置文件直接管理，简化了传统的mybatis-config.xml文件。因此，在此整合过程中，我们可以省去这个文件，只需要通过application.yml文件进行配置。

##### **创建实体类与Mapper接口**

- **实体类**：在项目中创建一个`User`实体类，用来表示数据库中的`users`表。

```
package com.example.entity;

public class User {
    private int id;
    private String name;
    private int age;

    // Getters and Setters

}
```

**Mapper接口**：在`com.example.mapper`包下创建一个接口`UserMapper`，定义[数据库操作](https://so.csdn.net/so/search?q=数据库操作&spm=1001.2101.3001.7020)方法。

```
package com.example.mapper;

import com.example.entity.User;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;

@Mapper
public interface UserMapper {
    @Select("SELECT * FROM users WHERE id = #{id}")
    User getUserById(int id);
}


```

**编写Mapper XML文件**：
虽然可以通过注解编写SQL，但对于复杂查询，我们推荐使用XML文件进行管理。接下来展示如何编写一个XML文件与Mapper接口配合使用。

**创建`UserMapper.xml`**：在`resources/mapper`目录下创建`UserMapper.xml`，并定义SQL查询。

```
<mapper namespace="com.example.mapper.UserMapper">
<select id="getUserById" resultType="com.example.entity.User">
SELECT id, name, age FROM users WHERE id = #{id}</select>
</mapper>
```

- **解释**：
    - `namespace`：对应`UserMapper`接口。
    - `resultType`：定义SQL查询的返回类型，指定为`User`实体类。
    - `#{id}`：通过占位符将参数传递给SQL语句，MyBatis自动处理参数绑定。
    -

### 3.**MyBatis深入操作——实现CRUD与动态SQL**

####  **实现CRUD操作**:

###### 3.1 创建用户（Create）

- **Mapper接口定义**：在`UserMapper`中定义插入用户的操作。

```
@Insert("INSERT INTO users(name, age) VALUES(#{name}, #{age})")
void insertUser(User user);
```

#####   **Mapper XML定义**：你也可以选择在XML中管理SQL。

```
<insert id="insertUser" parameterType="com.example.entity.User">
INSERT INTO users(name, age) VALUES(#{name}, #{age})
</insert>
```

**解释**：

- `#{name}` 和 `#{age}` 是MyBatis的占位符，自动将`User`对象的属性绑定到SQL语句中



###### 3.2 查询用户（Read）

- **Mapper接口定义**：我们已经在前一篇文章中定义了一个简单的[查询操作](https://so.csdn.net/so/search?q=查询操作&spm=1001.2101.3001.7020)。我们可以添加更多查询方法，例如按名称查询用户。

```
@Select("SELECT * FROM users WHERE name = #{name}")
List<User> getUsersByName(String name);
```

**Mapper XML定义**：在XML中定义相应的SQL。

```
<select id="getUsersByName" resultType="com.example.entity.User">
SELECT * FROM users WHERE name = #{name}
</select>
```

- **解释**：
    - 这里的`resultType`指定了返回的对象类型为`User`。
    - 使用`List<User>`返回多个结果，MyBatis会自动将查询结果集映射为`User`对象列表。



###### 3.3 更新用户（Update）

- **Mapper接口定义**：定义一个更新用户信息的方法。

```
@Update("UPDATE users SET name = #{name}, age = #{age} WHERE id = #{id}")
void updateUser(User user);
```

**Mapper XML定义**：

```
<update id="updateUser" parameterType="com.example.entity.User">
UPDATE users SET name = #{name}, age = #{age} WHERE id = #{id}
</update>
```

- **解释**：

    - `UPDATE`语句用于修改数据库中的数据，`#{id}`用于定位要修改的记录，`#{name}` 和 `#{age}`用于更新字段值。



###### 3.4 删除用户（Delete）

- **Mapper接口定义**：定义删除操作的方法

```
@Delete("DELETE FROM users WHERE id = #{id}")
void deleteUser(int id);
```

**Mapper XML定义**：

```
<delete id="deleteUser" parameterType="int">
DELETE FROM users WHERE id = #{id}
</delete>
```

- **解释**：
    - `DELETE`语句用于删除记录，`#{id}`占位符会替换为传入的用户ID。



### 4. **动态SQL的使用**

MyBatis支持动态SQL，通过`<if>`、`<choose>`、`<where>`等标签，可以根据条件动态构建SQL语句。这在复杂查询中非常有用，例如多条件查询时，可以根据用户输入动态生成查询条件。

###### 4.1 示例：条件查询

- 需求：查询用户时，允许根据姓名或年龄进行查询，用户可以选择提供一个或多个查询条件。

- **Mapper接口定义**：

  List<User> findUsersByConditions(Map<String, Object> conditions);

  **Mapper XML定义**：

  ```
  <select id="findUsersByConditions" parameterType="map" resultType="com.example.entity.User">
  SELECT * FROM users <where>
  <if test="name != null and name != ''"> AND name = #{name} </if>
  <if test="age != null"> AND age = #{age}</if>
  </where>
  </select>
  ```

    - **解释**：
        - `<where>` 标签：MyBatis会自动处理`WHERE`子句的前导条件，确保语法正确。
        - `<if>` 标签：根据传入的条件（`name` 和 `age`）动态生成查询条件。如果没有传入这些参数，对应的条件将被忽略。

###### 4.2 示例：动态更新

- 需求：只更新用户传入的字段，而不更新未提供的字段。

- **Mapper接口定义**

  ```
  void updateUserSelective(User user);
  ```

- **Mapper XML定义**：

  ```
  <update id="updateUserSelective" parameterType="com.example.entity.User">
    UPDATE users
    <set>
      <if test="name != null">name = #{name},</if>
      <if test="age != null">age = #{age},</if>
    </set>
    WHERE id = #{id}
  </update>
  ```

- **解释**：

    - `<set>` 标签：MyBatis会自动处理SQL中的逗号问题，确保不会在最后一个字段后生成多余的逗号。
    - 动态更新只会修改传入的字段，不会影响未提供的字段。



### 4.**MyBatis分页与缓存机制详解**

##### 1. **引言：为何需要分页与缓存？**

- **分页**：当数据库中的数据量非常大时，如果我们一次性读取所有数据，不仅会造成性能问题，还会导致内存溢出。因此，分页查询成为了非常必要的手段，通过每次只读取一部分数据，来减轻数据库压力。
- **缓存**：在实际项目中，某些数据可能会被频繁查询，而数据本身在短时间内不会发生变化。通过缓存机制，我们可以减少对数据库的重复访问，从而提升查询速度。

##### 2. **MyBatis中的分页实现**

**MyBatis本身并不直接提供分页功能**，但可以通过两种方式来实现分页查询：

- **方式一：手动分页**：通过SQL的`LIMIT`语句手动控制分页。
- **方式二：集成分页插件**：使用开源的分页插件，比如`PageHelper`，它能自动处理分页逻辑，方便快捷。

###### 2.1 手动分页实现

在MyBatis中，可以通过SQL的`LIMIT`和`OFFSET`子句手动实现分页查询。`LIMIT`用于限制返回的记录数量，`OFFSET`用于指定起始的行数。

- **Mapper接口定义**：

  ```
  List<User> getUsersByPage(@Param("offset") int offset, @Param("limit") int limit);
  ```

- **Mapper XML文件**：

  ```
  <select id="getUsersByPage" resultType="com.example.entity.User">
    SELECT * FROM users
    LIMIT #{limit} OFFSET #{offset}
  </select>
  ```

- **SQL解析**：

    - `LIMIT #{limit}`：限制返回的记录数。
    - `OFFSET #{offset}`：指定从哪一行开始读取数据。

- **使用示例**：查询第2页的数据，每页10条记录。

  ```
  List<User> users = userMapper.getUsersByPage(10, 10);
  ```

  **这种方式虽然简单，但需要开发者手动计算`offset`和`limit`，并且在项目中每次分页查询都要手动传递这些参数**。



###### 2.2 使用分页插件（PageHelper）

**PageHelper** 是一个功能强大的[MyBatis分页插件](https://so.csdn.net/so/search?q=MyBatis分页插件&spm=1001.2101.3001.7020)，可以自动处理分页逻辑，大大简化分页查询的工作量。

**步骤1：添加PageHelper依赖**

  ```
  <dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.4.6</version>
  </dependency>
  ```

**步骤2：配置PageHelper**
PageHelper通过配置Spring Boot的`application.yml`文件进行初始化，配置如下：

  ```
  pagehelper:
    helper-dialect: mysql  # 数据库类型
    reasonable: true       # 合理化分页，自动纠正错误的页码
  ```

**步骤3：使用PageHelper进行分页**
在使用时，我们只需要在查询前调用`PageHelper.startPage()`方法，即可实现分页。

**使用示例**：

  ```
  @Autowired
  private UserMapper userMapper;
  
  public List<User> getUsersByPage(int pageNum, int pageSize) {
      PageHelper.startPage(pageNum, pageSize);
      return userMapper.getAllUsers();
  }
  ```

**解释**：

- `PageHelper.startPage(pageNum, pageSize)`：指定当前页码和每页显示的记录数。
- `getAllUsers()`：执行Mapper中的查询方法，PageHelper会自动对查询结果进行分页处理。

**分页结果处理**：使用PageHelper后，返回结果会被包装成`Page`对象，包含分页信息，如总记录数、总页数等。

  ```
  PageInfo<User> pageInfo = new PageInfo<>(users);
  System.out.println("总页数: " + pageInfo.getPages());
  System.out.println("总记录数: " + pageInfo.getTotal());
  ```

3. **MyBatis缓存机制**

MyBatis支持两级缓存：

- **一级缓存（本地缓存）**：默认开启，每个`SqlSession`级别的缓存，缓存生命周期仅在`SqlSession`内部。每次数据库操作都使用同一个`SqlSession`时，可以避免重复查询。
- **二级缓存**：针对`Mapper`级别的缓存，跨`SqlSession`有效，可以持久化缓存数据。

​           3.1 一级缓存

​          **一级缓存默认开启**，无须额外配置。它存储在当前`SqlSession`中，如果在同一个`SqlSession`中执行相同的查询，MyBatis会 从缓存中获取数据，而不会重新访问数据库。

​          **示例**：

```
SqlSession session = sqlSessionFactory.openSession();
User user1 = session.selectOne("com.example.mapper.UserMapper.getUserById", 1);
User user2 = session.selectOne("com.example.mapper.UserMapper.getUserById", 1);
System.out.println(user1 == user2);  // true, 表示从缓存中获取数据
```

**注意事项**：当执行`INSERT`、`UPDATE`或`DELETE`等写操作时，MyBatis会自动清空一级缓存，确保缓存中的数据是最新的。

###### 3.2 二级缓存

**二级缓存需要手动开启**，它是Mapper级别的缓存，能够跨`SqlSession`使用，可以将查询结果缓存在内存中，甚至持久化到磁盘。

**步骤1：开启二级缓存**
在`mybatis-config.xml`中开启全局二级缓存支持：

```
<settings>
  <setting name="cacheEnabled" value="true"/>
</settings>
```

**步骤2：为Mapper开启二级缓存**
在`UserMapper.xml`文件中，通过`<cache>`标签开启二级缓存：

```
<mapper namespace="com.example.mapper.UserMapper">
  <cache/>
  <select id="getUserById" resultType="com.example.entity.User">
    SELECT * FROM users WHERE id = #{id}
  </select>
</mapper>
```

**解释**：

- `cacheEnabled="true"`：全局开启二级缓存功能。
- `<cache>`：为当前Mapper启用二级缓存。

**二级缓存的数据保存机制**：默认情况下，二级缓存使用的是内存缓存，但也可以通过配置持久化到本地文件系统或者Redis等缓存服务。

###### 3.3 二级缓存示例

**示例**：在两个`SqlSession`之间执行相同的查询。

```
SqlSession session1 = sqlSessionFactory.openSession();
User user1 = session1.selectOne("com.example.mapper.UserMapper.getUserById", 1);
session1.close();  // 关闭SqlSession，数据会被放入二级缓存

SqlSession session2 = sqlSessionFactory.openSession();
User user2 = session2.selectOne("com.example.mapper.UserMapper.getUserById", 1);
session2.close();

System.out.println(user1 == user2);  // true, 数据从二级缓存中获取
```

###### 3.4 缓存的注意事项

- **适用场景**：二级缓存适用于数据较少变化、频繁读取的场景，比如字典数据或用户信息。在数据频繁更新的场景，启用缓存可能会导致数据不一致的问题。
- **缓存清除机制**：当执行`INSERT`、`UPDATE`或`DELETE`操作时，MyBatis会自动清除二级缓存中相关的缓存数据，确保数据的一致性。

##### 4. **综合示例：分页与缓存的结合使用**

在一个实际的用户管理系统中，我们可以同时使用分页查询和二级缓存来优化性能。例如，通过PageHelper实现分页查询，并利用二级缓存减少数据库的查询压力。

**示例**：结合分页和缓存的用户查询服务。

```
@Autowired
private UserMapper userMapper;

public List<User> getUsersWithPagination(int pageNum, int pageSize) {
    PageHelper.startPage(pageNum, pageSize);
    List<User> users = userMapper.getAllUsers();
    return users;
}
```

在这个示例中，分页查询的结果可以在二级缓存中保存，后续的分页查询可以从缓存中获取，减少对数据库的访问。

##### 5. **总结与展望**

- **总结**：通过本篇文章，我们详细了解了如何在MyBatis中实现分页
