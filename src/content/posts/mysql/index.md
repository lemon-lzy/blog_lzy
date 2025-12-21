---
title: MySQL基础笔记
published: 2025-03-21
description: '编写了MySQL的一个基本概念及其用法'
image: "./cover.png"
tags: ["mysql"]
category: 'mysql'
draft: false 
lang: ''
---

# MySQL 基础笔记

## 数据库相关概念

| 名称               | 全称                                                                 | 简称                      |
| ------------------ | -------------------------------------------------------------------- | ------------------------- |
| 数据库             | 存储数据的仓库，数据是有组织的进行存储                               | DataBase（DB）            |
| 数据库管理系统     | 操纵和管理数据库的大型软件                                           | DataBase Management System（DBMS） |
| SQL                | 操作关系型数据库的编程语言，定义了一套操作关系型数据库的统一标准       | Structured Query Language（SQL） |

## 关系型数据库

**概念**：建立在关系模型基础上，由多张相互连接的二维表组成的数据库。

简单说，基于二维表存储数据的数据库就称为关系型数据库，不是基于二维表存储数据的数据库，就是非关系型数据库。

**特点**：
1. 使用表存储数据，格式统一，便于维护
2. 使用SQL语言操作，标准统一，使用方便。

## SQL 通用语法

1. SQL语句可以单行或者多行书写，以分号结尾
2. SQL语句可以使用空格/缩进来增强语句的可读性。
3. MySQL数据库的SQL语句不区分大小写，关键字建议使用大写。
4. 注释：
    - 单行注释：`-- 注释内容` 或 `# 注释内容`（MySQL特有)
    - 多行注释：`/* 注释内容*/`

| 分类    | 全称                           | 说明                                               |
| ------- | ------------------------------ | -------------------------------------------------- |
| **DDL** | **Data Definition Language**   | 数据定义语言，用来定义数据库对象(数据库，表，字段） |
| **DML** | **Data Manipulation Language** | 数据操作语言，用来对数据库表中的数据进行增删改      |
| DQL     | **Data Query Language**        | 数据查询语言，用来查询数据库中表的记录              |
| DCL     | **Data Control Language**      | 数据控制语言，用来创建数据库用户、控制数据库的权限  |

## SQL

### DDL

#### 数据库操作

> 查询

查询所有数据库
```sql
show databases;
```

查询当前数据库
```sql
select database();
```

> 创建

```sql
create database [if not exists] 数据库名 [default charset 字符集] [collate 排序规则];
```

**说明**：在创建数据库时，指定字符集不直接指定utf8 ，而是指定utf8mb4 因为utf8确定字符的大小只能是3个字节，但是在数据库中有时候有4个字节的字符。

> 删除

```sql
drop database [if exists] 数据库名;
```

> 使用

```sql
use 数据库名;
```

> 安装完Mysql自带的数据库

| Databases          |
| ------------------ |
| information_schema |
| mysql              |
| performance_schema |
| sys                |

#### 表操作-查询

> 查询当前数据库所有的表

```sql
show tables;
```

> 表结构查询

```sql
desc 表名;
```

> 查询指定表的建表语句

```sql
show create table 表名;
```

#### 表操作-创建

```sql
CREATE TABLE 表名(
字段1 字段1类型 [ COMMENT 字段1注释 ],
字段2 字段2类型 [COMMENT 字段2注释 ],
字段3 字段3类型 [COMMENT 字段3注释 ],
......
字段n 字段n类型 [COMMENT 字段n注释 ]
) [ COMMENT 表注释 ] ;
```

**注意**：[...] 内为可选参数，最后一个字段后面没有逗号。

#### 表操作-数据类型

##### 数值类型

| 类型         | 大小     | 有符号(SIGNED)范围                                    | 无符号(UNSIGNED)范围                                       | 描述               |
| ------------ | -------- | ----------------------------------------------------- | ---------------------------------------------------------- | ------------------ |
| TINYINT      | 1  byte  | (-128，127)                                           | (0，255)                                                   | 小整数值           |
| SMALLINT     | 2  bytes | (-32768，32767)                                       | (0，65535)                                                 | 大整数值           |
| MEDIUMINT    | 3  bytes | (-8388608，8388607)                                   | (0，16777215)                                              | 大整数值           |
| INT或INTEGER | 4  bytes | (-2147483648，2147483647)                             | (0，4294967295)                                            | 大整数值           |
| BIGINT       | 8  bytes | (-2^63，2^63-1)                                       | (0，2^64-1)                                                | 极大整数值         |
| FLOAT        | 4  bytes | (-3.402823466 E+38，3.402823466351 E+38)              | 0 和 (1.175494351  E-38，3.402823466 E+38)                 | 单精度浮点数值     |
| DOUBLE       | 8  bytes | (-1.7976931348623157 E+308，1.7976931348623157 E+308) | 0 和  (2.2250738585072014 E-308，1.7976931348623157 E+308) | 双精度浮点数值     |
| DECIMAL      |          | 依赖于M(精度)和D(标度)的值                            | 依赖于M(精度)和D(标度)的值                                 | 小数值(精确定点数) |

**说明**：在定义double时候 比如`score double(4 , 1)` 括号中的第一位表示总共能有几位数，第二位代表能有几位小数。

##### 字符类型

| 类型       | 大小                  | 描述                         |
| ---------- | --------------------- | ---------------------------- |
| CHAR       | 0-255 bytes           | 定长字符串                   |
| VARCHAR    | 0-65535 bytes         | 变长字符串                   |
| TINYBLOB   | 0-255 bytes           | 不超过255个字符的二进制数据  |
| TINYTEXT   | 0-255 bytes           | 短文本字符串                 |
| BLOB       | 0-65 535 bytes        | 二进制形式的长文本数据       |
| TEXT       | 0-65 535 bytes        | 长文本数据                   |
| MEDIUMBLOB | 0-16 777 215 bytes    | 二进制形式的中等长度文本数据 |
| MEDIUMTEXT | 0-16 777 215 bytes    | 中等长度文本数据             |
| LONGBLOB   | 0-4 294 967 295 bytes | 二进制形式的极大文本数据     |
| LONGTEXT   | 0-4 294 967 295 bytes | 极大文本数据                 |

| char(10) （性能好） | 用户名 username varchar(50) |
| ------------------- | --------------------------- |
| varchar(10)（性能较差） | 性别 gender char(1)        |

##### 日期类型

| 类型      | 大小 | 范围                                       | 格式                | 描述                     |
| --------- | ---- | ------------------------------------------ | ------------------- | ------------------------ |
| DATE      | 3    | 1000-01-01 至  9999-12-31                  | YYYY-MM-DD          | 日期值                   |
| TIME      | 3    | -838:59:59 至  838:59:59                   | HH:MM:SS            | 时间值或持续时间         |
| YEAR      | 1    | 1901 至 2155                               | YYYY                | 年份值                   |
| DATETIME  | 8    | 1000-01-01 00:00:00 至 9999-12-31 23:59:59 | YYYY-MM-DD HH:MM:SS | 混合日期和时间值         |
| TIMESTAMP | 4    | 1970-01-01 00:00:01 至 2038-01-19 03:14:07 | YYYY-MM-DD HH:MM:SS | 混合日期和时间值，时间戳 |

#### 表操作-修改

> 添加字段

```sql
alter table 表名 add 字段名 类型（长度） [comment 注释][约束];
```

**关于MySQL列名区分大小写问题**：
在mysql中如果用引号使用在列名是会报错的因为，mysql会把引号中的内容当作字符串处理，而不是列名，如果想要有大小写那么可以用反引号括起来，但是在使用的时候mysql依然是不识别大小写的，意味着这就只是一个标识。

示例：
```sql
CREATE TABLE MyTable (
  `ColName` INT,
   colname VARCHAR(20)
);

SELECT `ColName` FROM MyTable;
```

> 修改字段

- 修改数据类型
```sql
alter table 表名 modify 字段名 新数据类型（长度）
```

- 修改字段名和字段类型
```sql
alter table 表名 change 旧字段名 新字段名 类型（长度） [comment 注释] [约束]
```

- 删除字段
```sql
alter table 表名 drop 字段名;
```

- 修改表名
```sql
alter table 表名 rename to 新表名；
```

#### 表操作-删除

- 删除表
```sql
drop table [if exists] 表名；
```

- 删除指定表，并重新创建表
```sql
truncate table 表名；
```

**注意**：删除表时，表中的全部数据也会被删除。

### DML

DML英文全称是Data Manipulation Language(数据操作语言)，用来对数据库中表的数据记录进行增、删、改操作。
- 添加数据（INSERT）
- 修改数据（UPDATE）
- 删除数据（DELETE）

#### 添加数据

1. 给指定字段添加数据
```sql
INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...);
```

2. 给全部字段添加数据
```sql
INSERT INTO 表名 VALUES (值1, 值2, ...);
```

3. 批量添加数据
```sql
INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...) ;
INSERT INTO 表名 VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...) ;
```

**注意事项**：
- 插入数据时，指定的字段顺序需要与值的顺序是一一对应的。
- 字符串和日期型数据应该包含在引号中。
- 插入的数据大小，应该在字段的规定范围内。

#### 修改数据

```sql
UPDATE 表名 SET 字段名1 = 值1 , 字段名2 = 值2 , .... [ WHERE 条件 ] ;
```

**注意事项**：
修改语句的条件可以有，也可以没有，如果没有条件，则会修改整张表的所有数据。

#### 删除数据

```SQL
DELETE FROM 表名 [ WHERE 条件 ] ;
```

**注意事项**：
- DELETE 语句的条件可以有，也可以没有，如果没有条件，则会删除整张表的所有数据。
- DELETE 语句不能删除某一个字段的值(可以使用UPDATE，将该字段值置为NULL即可)。
- 当进行删除全部数据操作时，datagrip会提示我们，询问是否确认删除，我们直接点击Execute即可。

### DQL

DQL英文全称是Data Query Language(数据查询语言)，数据查询语言，用来查询数据库中表的记录。
查询关键字: SELECT

在一个正常的业务系统中，查询操作的频次是要远高于增删改的，当我们去访问企业官网、电商网站，在这些网站中我们所看到的数据，实际都是需要从数据库中查询并展示的。而且在查询的过程中，可能还会涉及到条件、排序、分页等操作。

#### 案例前置数据

```sql
drop table if exists employee;
create table emp(
id int comment '编号',
workno varchar(10) comment '工号',
name varchar(10) comment '姓名',
gender char(1) comment '性别',
age tinyint unsigned comment '年龄',
idcard char(18) comment '身份证号',
workaddress varchar(50) comment '工作地址',
entrydate date comment '入职时间'
)comment '员工表';
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (1, '00001', '柳岩666', '女', 20, '123456789012345678', '北京', '2000-01-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (2, '00002', '张无忌', '男', 18, '123456789012345670', '北京', '2005-09-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (3, '00003', '韦一笑', '男', 38, '123456789712345670', '上海', '2005-08-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (4, '00004', '赵敏', '女', 18, '123456757123845670', '北京', '2009-12-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (5, '00005', '小昭', '女', 16, '123456769012345678', '上海', '2007-07-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (6, '00006', '杨逍', '男', 28, '12345678931234567X', '北京', '2006-01-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (7, '00007', '范瑶', '男', 40, '123456789212345670', '北京', '2005-05-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (8, '00008', '黛绮丝', '女', 38, '123456157123645670', '天津', '2015-05-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (9, '00009', '范凉凉', '女', 45, '123156789012345678', '北京', '2010-04-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (10, '00010', '陈友谅', '男', 53, '123456789012345670', '上海', '2011-01-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (11, '00011', '张士诚', '男', 55, '123567897123465670', '江苏', '2015-05-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (12, '00012', '常遇春', '男', 32, '123446757152345670', '北京', '2004-02-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (13, '00013', '张三丰', '男', 88, '123656789012345678', '江苏', '2020-11-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (14, '00014', '灭绝', '女', 65, '123456719012345670', '西安', '2019-05-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (15, '00015', '胡青牛', '男', 70, '12345674971234567X', '西安', '2018-04-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
VALUES (16, '00016', '周芷若', '女', 18, null, '北京', '2012-06-01');
```

##### 基本语法

```sql
SELECT
字段列表
FROM
表名列表
WHERE
条件列表
GROUP BY
分组字段列表
HAVING
分组后条件列表
ORDER BY
排序字段列表
LIMIT
分页参数
```

##### 基础查询

- 查询多个字段
```sql
SELECT 字段1, 字段2, 字段3 ... FROM 表名 ; 
SELECT * FROM 表名 ;
```
**注意** : `*` 号代表查询所有字段，在实际开发中尽量少用（不直观、影响效率）。

- 字段设置别名
```sql
SELECT 字段1 [ AS 别名1 ] , 字段2 [ AS 别名2 ] ... FROM 表名;
SELECT 字段1 [ 别名1 ] , 字段2 [ 别名2 ] ... FROM 表名;
```

- 去除重复记录
```sql
SELECT DISTINCT 字段列表 FROM 表名;
```

**案例**：
A. 查询指定字段 name, workno, age并返回
```sql
select name,workno,age from emp; 
```

B. 查询返回所有字段
```sql
select id ,workno,name,gender,age,idcard,workaddress,entrydate from emp; 
select * from emp; 
```

C. 查询所有员工的工作地址,起别名
```sql 
select workaddress as '工作地址' from emp;
-- as可以省略
select workaddress '工作地址' from emp;
```

D. 查询公司员工的上班地址有哪些(不要重复)
```sql
select distinct workaddress '工作地址' from emp;
```

##### 条件查询

```sql
SELECT 字段列表 FROM 表名 WHERE 条件列表 ;
```

| 比较运算符          | 功能                                     |
| ------------------- | ---------------------------------------- |
| >                   | 大于                                     |
| >=                  | 大于等于                                 |
| <                   | 小于                                     |
| <=                  | 小于等于                                 |
| =                   | 等于                                     |
| <>  或 !=           | 不等于                                   |
| between ... and ... | 在某个范围之内(含最小、最大值)           |
| in(...)             | 在in之后的列表中的值，多选               |
| like 占位符         | 模糊匹配(_匹配单个字符, %匹配任意个字符) |
| is null             | 是null                                   |

**注意**：between后面是小的，and 后面是大的。

常用逻辑运算符如下：

| 逻辑运算符 | 功能                        |
| ---------- | --------------------------- |
| AND 或 &&  | 并且 (多个条件同时成立)     |
| OR 或 \|\| | 或者 (多个条件任意一个成立) |
| NOT 或 !   | 非 , 不是                   |

**案例**：
A. 查询年龄等于 88 的员工
```sql
select * from emp where age = 88;
```

B. 查询年龄小于 20 的员工信息
```sql
select * from emp where age < 20;
```

C. 查询年龄小于等于 20 的员工信息
```sql
select * from emp where age <= 20; 
```

D. 查询没有身份证号的员工信息
```sql
select * from emp where idcard is null;
```

E. 查询有身份证号的员工信息
```sql
select * from emp where idcard is not null; 
```

F. 查询年龄不等于 88 的员工信息
```sql
select * from emp where age != 88;
select * from emp where age <> 88;
```

G. 查询年龄在15岁(包含) 到 20岁(包含)之间的员工信息
```sql
select * from emp where age >= 15 && age <= 20;
select * from emp where age >= 15 and age <= 20;
select * from emp where age between 15 and 20;
```

H. 查询性别为 女 且年龄小于 25岁的员工信息
```sql
select * from emp where gender = '女' and age < 25;
```

I. 查询年龄等于18 或 20 或 40 的员工信息
```sql
select * from emp where age = 18 or age = 20 or age =40;
select * from emp where age in(18,20,40);
```

J. 查询姓名为两个字的员工信息
```sql
select * from emp where name like '__'; 
```

K. 查询身份证号最后一位是X的员工信息
```sql
select * from emp where idcard like '%X';
select * from emp where idcard like '_________________X';
```

##### 聚合函数

**介绍**：将一列数据作为一个整体，进行纵向计算。

常见的聚合函数：

| 函数  | 功能     |
| ----- | -------- |
| count | 统计数量 |
| max   | 最大值   |
| min   | 最小值   |
| avg   | 平均值   |
| sum   | 求和     |

**语法**：
```sql
SELECT 聚合函数(字段列表) FROM 表名 ;
```

**注意** : NULL值是不参与所有聚合函数运算的。

**案例**：
A. 统计该企业员工数量
```sql
select count(*) from emp; -- 统计的是总记录数
select count(idcard) from emp; -- 统计的是idcard字段不为null的记录数
```
**说明**：对于count聚合函数，统计符合条件的总记录数，还可以通过 `count(数字/字符串)` 的形式进行统计，比如：`select count(1) from emp`。

B. 统计该企业员工的平均年龄
```sql
select avg(age) from emp; 
```

C. 统计该企业员工的最大年龄
```sql
select max(age) from emp;
```

D. 统计该企业员工的最小年龄
```sql
select min(age) from emp;
```

E. 统计西安地区员工的年龄之和
```sql
select sum(age) from emp where workaddress = '西安';
```

##### 分组查询

```sql
SELECT 字段列表 FROM 表名 [ WHERE 条件 ] GROUP BY 分组字段名 [ HAVING 分组后过滤条件 ]
```

**where与having区别**：
- 执行时机不同：where是分组之前进行过滤，不满足where条件，不参与分组；而having是分组之后对结果进行过滤。
- 判断条件不同：where不能对聚合函数进行判断，而having可以。

**注意事项**：
- 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义。
- 执行顺序: where > 聚合函数 > having 。
- 支持多字段分组, 具体语法为 : `group by columnA,columnB`。

**案例**：
A. 根据性别分组 , 统计男性员工 和 女性员工的数量
```sql
select gender, count(*) from emp group by gender ;
```

B. 根据性别分组 , 统计男性员工 和 女性员工的平均年龄
```sql
select gender, avg(age) from emp group by gender ; 
```

C. 查询年龄小于45的员工 , 并根据工作地址分组 , 获取员工数量大于等于3的工作地址
```sql
select workaddress, count(*) address_count from emp where age < 45 group by workaddress having address_count >= 3;
```

D. 统计各个工作地址上班的男性及女性员工的数量
```sql
select workaddress, gender, count(*) '数量' from emp group by gender , workaddress;
```

##### 排序查询

```sql
SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1 , 字段2 排序方式2 ; 
```

**排序方式**：
- ASC : 升序(默认值)
- DESC: 降序

**注意事项**：
- 如果是升序, 可以不指定排序方式ASC ;
- 如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序 ;

**案例**：
A. 根据年龄对公司的员工进行升序排序
```sql
select * from emp order by age asc;
select * from emp order by age;
```

B. 根据入职时间, 对员工进行降序排序
```sql
select * from emp order by entrydate desc;
```

C. 根据年龄对公司的员工进行升序排序 , 年龄相同 , 再按照入职时间进行降序排序
```sql
select * from emp order by age asc , entrydate desc; 
```

##### 分页查询

分页操作在业务系统开发时，也是非常常见的一个功能，我们在网站中看到的各种各样的分页条，后台都需要借助于数据库的分页操作。

```sql
SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数 ;
```

**注意事项**：
- 起始索引从0开始，起始索引 = （查询页码 - 1）* 每页显示记录数。
- 分页查询是数据库的方言，不同的数据库有不同的实现，MySQL中是LIMIT。
- 如果查询的是第一页数据，起始索引可以省略，直接简写为 `limit 10`。

**案例**：
A. 查询第1页员工数据, 每页展示10条记录
```sql
select * from emp limit 0,10;
select * from emp limit 10;
```

B. 查询第2页员工数据, 每页展示10条记录
```sql
select * from emp limit 10,10; 
```

##### 执行顺序

![执行顺序](http://xwhking.object.kuken.tech/picgo/image-20230604220900409.png)

### DCL

DCL英文全称是Data Control Language(数据控制语言)，用来管理数据库用户、控制数据库的访问权限。

#### 管理用户

- 查询用户
```sql
select * from mysql.user;
```
**说明**：其中 Host代表当前用户访问的主机, 如果为localhost, 仅代表只能够在当前本机访问，是不可以远程访问的。User代表的是访问该数据库的用户名。在MySQL中需要通过Host和User来唯一标识一个用户。

- 创建用户
```sql
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
```

- 修改用户密码
```sql
ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码' ;
```

- 删除用户
```sql
DROP USER '用户名'@'主机名' ;
```

**注意事项**：
- 在MySQL中需要通过用户名@主机名的方式，来唯一标识一个用户。
- 主机名可以使用 % 通配。
- 这类SQL开发人员操作的比较少，主要是DBA（ Database Administrator 数据库管理员）使用。

**案例**：
A. 创建用户itcast, 只能够在当前主机localhost访问, 密码123456;
```sql
create user 'itcast'@'localhost' identified by '123456';
```

B. 创建用户heima, 可以在任意主机访问该数据库, 密码123456
```sql
create user 'heima'@'%' identified by '123456';
```

C. 修改用户heima的访问密码为1234;
```sql
alter user 'heima'@'%' identified with mysql_native_password by '1234';
```

D. 删除 itcast@localhost 用户
```sql
drop user 'itcast'@'localhost';
```

#### 权限控制

MySQL中定义了很多种权限，但是常用的就以下几种：

| 权限                | 说明                 |
| ------------------- | -------------------- |
| ALL、ALL PRIVILEGES | 所有权限             |
| SELECT              | 查询数据             |
| INSERT              | 插入数据             |
| UPDATE              | 修改数据             |
| DELETE              | 删除数据             |
| ALTER               | 修改表               |
| DROP                | 删除数据库、表、视图 |
| CREATE              | 创建数据库、表       |

**查询权限**：
```sql
SHOW GRANTS FOR '用户名'@'主机名' ;
```

**授予权限**：
```sql
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名'
```

**撤销权限**：
```sql
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';
```

**注意事项**：
- 多个权限之间，使用逗号分隔
- 授权时， 数据库名和表名可以使用 * 进行通配，代表所有。

**案例**：
A. 查询 'heima'@'%' 用户的权限
```sql
show grants for 'heima'@'%';
```

B. 授予 'heima'@'%' 用户itcast数据库所有表的所有操作权限
```sql
grant all on itcast.* to 'heima'@'%';
```

C. 撤销 'heima'@'%' 用户的itcast数据库的所有权
```sql
revoke all on itcast.* from 'heima'@'%';
```

## 函数

函数 是指一段可以直接被另一段程序调用的程序或代码。 也就意味着，这一段程序或代码在MySQL中已经给我们提供了，我们要做的就是在合适的业务场景调用对应的函数完成对应的业务需求即可。

MySQL中的函数主要分为以下四类： 字符串函数、数值函数、日期函数、流程函数。

### 字符串函数

MySQL中内置了很多字符串函数，常用的几个如下：

| 函数                     | 功能                                                       |
| ------------------------ | ---------------------------------------------------------- |
| CONCAT(S1,S2,...Sn)      | 字符串拼接，将S1，S2，... Sn拼接成一个字符串               |
| LOWER(str)               | 将字符串str全部转为小写                                    |
| UPPER(str)               | 将字符串str全部转为大写                                    |
| LPAD(str,n,pad)          | 左填充，用字符串pad对str的左边进行填充，达到n个字符串长度  |
| RPAD(str,n,pad)          | 右填充，用字符串pad对str的右边进行填充，达到n个字符串长度  |
| TRIM(str)                | 去掉字符串头部和尾部的空格                                 |
| SUBSTRING(str,start,len) | 返回从字符串str从start位置起的len个长度的字符串            |

**案例**：
A. concat : 字符串拼接
```sql
select concat('Hello' , ' MySQL');
```

B. lower : 全部转小写
```sql
select lower('Hello');
```

C. upper : 全部转大写
```sql
select upper('Hello');
```

D. lpad : 左填充
```sql
select lpad('01', 5, '-');
```

E. rpad : 右填充
```sql
select rpad('01', 5, '-');
```

F. trim : 去除空格
```sql
select trim(' Hello MySQL ');
```

G. substring : 截取子字符串
```sql
select substring('Hello MySQL',1,5);
```

**具体案例**：
由于业务需求变更，企业员工的工号，统一为5位数，目前不足5位数的全部在前面补0。比如： 1号员工的工号应该为00001。
```sql
update emp set workno = lpad(workno, 5, '0');
```

### 数值函数

| 函数       | 功能                               |
| ---------- | ---------------------------------- |
| CEIL(x)    | 向上取整                           |
| FLOOR(x)   | 向下取整                           |
| MOD(x,y)   | 返回x/y的模                        |
| RAND()     | 返回0~1内的随机数                  |
| ROUND(x,y) | 求参数x的四舍五入的值，保留y位小数 |

**案例**：
A. ceil：向上取整
```sql
select ceil(1.1);
```

B. floor：向下取整
```sql
select floor(1.9);
```

C. mod：取模
```sql
select mod(7,4);
```

D. rand：获取随机数
```sql
select rand();
```

E. round：四舍五入
```sql
select round(2.344,2);
```

**具体案例**：
通过数据库的函数，生成一个六位数的随机验证码。
```sql
select lpad(round(rand()*1000000 , 0), 6, '0');
```

### 日期函数

| 函数                               | 功能                                               |
| ---------------------------------- | -------------------------------------------------- |
| CURDATE()                          | 返回当前日期                                       |
| CURTIME()                          | 返回当前时间                                       |
| NOW()                              | 返回当前日期和时间                                 |
| YEAR(date)                         | 获取指定date的年份                                 |
| MONTH(date)                        | 获取指定date的月份                                 |
| DAY(date)                          | 获取指定date的日期                                 |
| DATE_ADD(date, INTERVAL expr type) | 返回一个日期/时间值加上一个时间间隔expr后的时间值   |
| DATEDIFF(date1,date2)              | 返回起始时间date1 和 结束时间date2之间的天数       |

**案例**：
A. curdate：当前日期
```sql
select curdate();
```

B. curtime：当前时间
```sql
select curtime();
```

C. now：当前日期和时间
```sql
select now();
```

D. YEAR , MONTH , DAY：当前年、月、日
```sql
select YEAR(now());
select MONTH(now());
select DAY(now());
```

E. date_add：增加指定的时间间隔
```sql
select date_add(now(), INTERVAL 70 YEAR );
```

F. datediff：获取两个日期相差的天数
```sql
select datediff('2021-10-01', '2021-12-01');
```

**具体案例**：
查询所有员工的入职天数，并根据入职天数倒序排序。
```sql
select name, datediff(curdate(), entrydate) as 'entrydays' from emp order by entrydays desc;
```

### 流程函数

流程函数也是很常用的一类函数，可以在SQL语句中实现条件筛选，从而提高语句的效率。

| 函数                                                         | 功能                                                       |
| ------------------------------------------------------------ | ---------------------------------------------------------- |
| IF(value , t , f)                                            | 如果value为true，则返回t，否则返回 f                       |
| IFNULL(value1 , value2)                                      | 如果value1不为空，返回value1，否则 返回value2              |
| CASE WHEN [ val1 ] THEN [res1] ... ELSE [ default ] END      | 如果val1为true，返回res1，... 否则返回default默认值       |
| CASE [ expr ] WHEN [ val1 ] THEN [res1] ... ELSE [ default ] END | 如果expr的值等于val1，返回 res1，... 否则返回default默认值 |

**演示**：
A. if
```sql
select if(false, 'Ok', 'Error');
```

B. ifnull
```sql
select ifnull('Ok','Default');
select ifnull('','Default');
select ifnull(null,'Default');
```

C. case when then else end
需求: 查询emp表的员工姓名和工作地址 (北京/上海 ----> 一线城市 , 其他 ----> 二线城市)
```sql
select
name,
( case workaddress when '北京' then '一线城市' when '上海' then '一线城市' else '二线城市' end ) as '工作地址'
from emp;
```

**案例**：
创建成绩表并插入数据：
```sql
create table score(
id int comment 'ID',
name varchar(20) comment '姓名',
math int comment '数学',
english int comment '英语',
chinese int comment '语文'
) comment '学员成绩表';
insert into score(id, name, math, english, chinese) VALUES (1, 'Tom', 67, 88, 95), (2, 'Rose' , 23, 66, 90),(3, 'Jack', 56, 98, 76);
```

查询成绩等级：
```sql
select
id,
name,
(case when math >= 85 then '优秀' when math >=60 then '及格' else '不及格' end ) '数学',
(case when english >= 85 then '优秀' when english >=60 then '及格' else '不及格' end ) '英语',
(case when chinese >= 85 then '优秀' when chinese >=60 then '及格' else '不及格' end ) '语文'
from score;
```

## 约束

### 概述

**概念**：约束是作用于表中字段上的规则，用于限制存储在表中的数据。

**目的**：保证数据库中数据的正确、有效性和完整性。

**分类**：

| 约束                      | 描述                                                      | 关键字      |
| ------------------------- | --------------------------------------------------------- | ----------- |
| 非空约束                  | 限制该字段的数据不能为null                                | NOT NULL    |
| 唯一约束                  | 保证该字段的所有数据都是唯一、不重复的                    | UNIQUE      |
| 主键约束                  | 主键是一行数据的唯一标识，要求非空且唯一                  | PRIMARY KEY |
| 默认约束                  | 保存数据时，如果未指定该字段的值，则采用默认值            | DEFAULT     |
| 检查约束(8.0.16版本 之后) | 保证字段值满足某一个条件                                  | CHECK       |
| 外键约束                  | 用来让两张表的数据之间建立连接，保证数据的一致性和完整性   | FOREIGN KEY |

**注意**：约束是作用于表中字段上的，可以在创建表/修改表的时候添加约束。

### 约束演示

**需求**：

| 字段名 | 字段含义    | 字段类型    | 约束条件                   | 约束关键字                  |
| ------ | ----------- | ----------- | -------------------------- | --------------------------- |
| id     | ID唯一标识  | int         | 主键，并且自动增长         | PRIMARY KEY, AUTO_INCREMENT |
| name   | 姓名        | varchar(10) | 不为空，并且唯一           | NOT NULL , UNIQUE           |
| age    | 年龄        | int         | 大于0，并且小于等于120     | CHECK                       |
| status | 状态        | char(1)     | 如果没有指定该值，默认为1  | DEFAULT                     |
| gender | 性别        | char(1)     | 无                         |                             |

**建表语句**：
```sql
CREATE TABLE tb_user(
    id int AUTO_INCREMENT PRIMARY KEY COMMENT 'ID唯一标识',
    name varchar(10) NOT NULL UNIQUE COMMENT '姓名' ,
    age int check (age > 0 && age <= 120) COMMENT '年龄' ,
    status char(1) default '1' COMMENT '状态',
    gender char(1) COMMENT '性别'
);
```

**验证是否生效**：
```sql
insert into tb_user(name,age,status,gender) values ('Tom1',19,'1','男'),('Tom2',25,'0','男');
insert into tb_user(name,age,status,gender) values ('Tom3',19,'1','男');
insert into tb_user(name,age,status,gender) values (null,19,'1','男');
insert into tb_user(name,age,status,gender) values ('Tom3',19,'1','男');
insert into tb_user(name,age,status,gender) values ('Tom4',80,'1','男');
insert into tb_user(name,age,status,gender) values ('Tom5',-1,'1','男');
insert into tb_user(name,age,status,gender) values ('Tom5',121,'1','男');
insert into tb_user(name,age,gender) values ('Tom5',120,'男');
```

### 外键约束

**外键**：用来让两张表的数据之间建立连接，从而保证数据的一致性和完整性。

**示例**：
左侧的emp表是员工表，里面存储员工的基本信息，包含员工的ID、姓名、年龄、职位、薪资、入职日期、上级主管ID、部门ID，在员工的信息中存储的是部门的ID dept_id，而这个部门的ID是关联的部门表dept的主键id，那emp表的dept_id就是外键,关联的是另一张表的主键。

**注意**：目前上述两张表，只是在逻辑上存在这样一层关系；在数据库层面，并未建立外键关联，所以是无法保证数据的一致性和完整性的。

**数据准备**：
```sql
create table dept(
    id int auto_increment comment 'ID' primary key,
    name varchar(50) not null comment '部门名称'
)comment '部门表';
INSERT INTO dept (id, name) VALUES (1, '研发部'), (2, '市场部'),(3, '财务部'), (4, '销售部'), (5, '总经办');
create table emp(
    id int auto_increment comment 'ID' primary key,
    name varchar(50) not null comment '姓名',
    age int comment '年龄',
    job varchar(20) comment '职位',
    salary int comment '薪资',
    entrydate date comment '入职时间',
    managerid int comment '直属领导ID',
    dept_id int comment '部门ID'
)comment '员工表';
INSERT INTO emp (id, name, age, job,salary, entrydate, managerid, dept_id)
VALUES
(1, '金庸', 66, '总裁',20000, '2000-01-01', null,5),(2, '张无忌', 20, '项目经理',12500, '2005-12-05', 1,1),
(3, '杨逍', 33, '开发', 8400,'2000-11-03', 2,1),(4, '韦一笑', 48, '开发',11000, '2002-02-05', 2,1),
(5, '常遇春', 43, '开发',10500, '2004-09-07', 3,1),(6, '小昭', 19, '程序员鼓励师',6600, '2004-10-12', 2,1);
```

#### 语法

- 添加外键
```sql
CREATE TABLE 表名(
字段名 数据类型,
...
[CONSTRAINT] [外键名称] FOREIGN KEY (外键字段名) REFERENCES 主表 (主表列名)
);

-- 或者
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名) REFERENCES 主表 (主表列名) ;
```

为emp的dept_id字段添加外键约束，关联dept表的主键id：
```sql
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id);
```

- 删除外键
```sql
ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;
```

删除emp表的外键fk_emp_dept_id：
```sql
alter table emp drop foreign key fk_emp_dept_id;
```

#### 删除/更新行为

| 行为        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| NO ACTION   | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 RESTRICT 一致) 默认行为 |
| RESTRICT    | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 NO ACTION 一致) 默认行为 |
| CASCADE     | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除/更新外键在子表中的记录。 |
| SET NULL    | 当在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（这就要求该外键允许取null）。 |
| SET DEFAULT | 父表有变更时，子表将外键列设置成一个默认的值 (Innodb不支持)  |

**语法**：
```sql
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段) REFERENCES 主表名 (主表字段名) ON UPDATE CASCADE ON DELETE CASCADE;
```

**演示**：
- CASCADE
```sql
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id) on update cascade on delete cascade ;
```

- SET NULL
```sql
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id) on update set null on delete set null ;
```

## 多表查询

### 多表关系

项目开发中，在进行数据库表结构设计时，会根据业务需求及业务模块之间的关系，分析并设计表结构，由于业务之间相互关联，所以各个表结构之间也存在着各种联系，基本上分为三种：
- 一对多(多对一)
- 多对多
- 一对一

#### 一对多

- **案例**：部门 与 员工的关系
- **关系**：一个部门对应多个员工，一个员工对应一个部门
- **实现**：在多的一方建立外键，指向一的一方的主键

![一对多](http://xwhking.object.kuken.tech/picgo/image-20230605192317752.png)

#### 多对多

- **案例**：学生 与 课程的关系
- **关系**：一个学生可以选修多门课程，一门课程也可以供多个学生选择
- **实现**：建立第三张中间表，中间表至少包含两个外键，分别关联两方主键

![多对多](http://xwhking.object.kuken.tech/picgo/image-20230605192335764.png)

**对应SQL**：
```sql
create table student(
id int auto_increment primary key comment '主键ID',
name varchar(10) comment '姓名',
no varchar(10) comment '学号'
) comment '学生表';
insert into student values (null, '黛绮丝', '2000100101'),(null, '谢逊', '2000100102'),(null, '殷天正', '2000100103'),(null, '韦一笑', '2000100104');
create table course(
id int auto_increment primary key comment '主键ID',
name varchar(10) comment '课程名称'
) comment '课程表';
insert into course values (null, 'Java'), (null, 'PHP'), (null , 'MySQL') , (null, 'Hadoop');
create table student_course(
    id int auto_increment comment '主键' primary key,
    studentid int not null comment '学生ID',
    courseid int not null comment '课程ID',
    constraint fk_courseid foreign key (courseid) references course (id),
    constraint fk_studentid foreign key (studentid) references student (id)
)comment '学生课程中间表';
insert into student_course values (null,1,1),(null,1,2),(null,1,3),(null,2,2),(null,2,3),(null,3,4);
```

#### 一对一

- **案例**：用户 与 用户详情的关系
- **关系**：一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率
- **实现**：在任意一方加入外键，关联另外一方的主键，并且设置外键为唯一的(UNIQUE)

![一对一](http://xwhking.object.kuken.tech/picgo/image-20230605192449921.png)

**对应SQL脚本**：
```sql
create table tb_user(
id int auto_increment primary key comment '主键ID',
name varchar(10) comment '姓名',
age int comment '年龄',
gender char(1) comment '1: 男 , 2: 女',
phone char(11) comment '手机号'
) comment '用户基本信息表';
create table tb_user_edu(
id int auto_increment primary key comment '主键ID',
degree varchar(20) comment '学历',
major varchar(50) comment '专业',
primaryschool varchar(50) comment '小学',
middleschool varchar(50) comment '中学',
university varchar(50) comment '大学',
userid int unique comment '用户ID',
constraint fk_userid foreign key (userid) references tb_user(id)
) comment '用户教育信息表';
insert into tb_user(id, name, age, gender, phone) values
(null,'黄渤',45,'1','18800001111'),
(null,'冰冰',35,'2','18800002222'),
(null,'码云',55,'1','18800008888'),
(null,'李彦宏',50,'1','18800009999');
insert into tb_user_edu(id, degree, major, primaryschool, middleschool, university, userid) values
(null,'本科','舞蹈','静安区第一小学','静安区第一中学','北京舞蹈学院',1),
(null,'硕士','表演','朝阳区第一小学','朝阳区第一中学','北京电影学院',2),
(null,'本科','英语','杭州市第一小学','杭州市第一中学','杭州师范大学',3),
(null,'本科','应用数学','阳泉第一小学','阳泉区第一中学','清华大学',4);
```

### 多表查询概述

#### 数据准备

删除之前 emp, dept表的测试数据，执行如下脚本，创建emp表与dept表并插入测试数据：
```sql
-- 创建dept表，并插入数据
create table dept(
id int auto_increment comment 'ID' primary key,
name varchar(50) not null comment '部门名称'
)comment '部门表';
INSERT INTO dept (id, name) VALUES (1, '研发部'), (2, '市场部'),(3, '财务部'), (4, '销售部'), (5, '总经办'), (6, '人事部');
-- 创建emp表，并插入数据
create table emp(
id int auto_increment comment 'ID' primary key,
name varchar(50) not null comment '姓名',
age int comment '年龄',
job varchar(20) comment '职位',
salary int comment '薪资',
entrydate date comment '入职时间',
managerid int comment '直属领导ID',
dept_id int comment '部门ID'
)comment '员工表';
-- 添加外键
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id);
INSERT INTO emp (id, name, age, job,salary, entrydate, managerid, dept_id)
VALUES
(1, '金庸', 66, '总裁',20000, '2000-01-01', null,5),
(2, '张无忌', 20, '项目经理',12500, '2005-12-05', 1,1),
(3, '杨逍', 33, '开发', 8400,'2000-11-03', 2,1),
(4, '韦一笑', 48, '开发',11000, '2002-02-05', 2,1),
(5, '常遇春', 43, '开发',10500, '2004-09-07', 3,1),
(6, '小昭', 19, '程序员鼓励师',6600, '2004-10-12', 2,1),
(7, '灭绝', 60, '财务总监',8500, '2002-09-12', 1,3),
(8, '周芷若', 19, '会计',48000, '2006-06-02', 7,3),
(9, '丁敏君', 23, '出纳',5250, '2009-05-13', 7,3),
(10, '赵敏', 20, '市场部总监',12500, '2004-10-12', 1,2),
(11, '鹿杖客', 56, '职员',3750, '2006-10-03', 10,2),
(12, '鹤笔翁', 19, '职员',3750, '2007-05-09', 10,2),
(13, '方东白', 19, '职员',5500, '2009-02-12', 10,2),
(14, '张三丰', 88, '销售总监',14000, '2004-10-12', 1,4),
(15, '俞莲舟', 38, '销售',4600, '2004-10-12', 14,4),
(16, '宋远桥', 40, '销售',4600, '2004-10-12', 14,4),
(17, '陈友谅', 42, null,2000, '2011-10-12', 1,null);
```

dept表共6条记录，emp表共17条记录。

#### 概述

多表查询就是指从多张表中查询数据。

原来查询单表数据，执行的SQL形式为：`select * from emp;`

那么我们要执行多表查询，就只需要使用逗号分隔多张表即可，如：`select * from emp , dept ;`。

此时,我们看到查询结果中包含了大量的结果集，总共102条记录，而这其实就是员工表emp所有的记录 (17) 与 部门表dept所有记录(6) 的所有组合情况，这种现象称之为笛卡尔积。

**在多表查询中，我们是需要消除无效的笛卡尔积的，只保留两张表关联部分的数据。在SQL语句中，如何来去除无效的笛卡尔积呢？ 我们可以给多表查询加上连接查询的条件即可。**

#### 分类

- 连接查询
    - 内连接：相当于查询A、B交集部分数据
    - 外连接：
        - 左外连接：查询左表所有数据，以及两张表交集部分数据
        - 右外连接：查询右表所有数据，以及两张表交集部分数据
    - 自连接：当前表与自身的连接查询，自连接必须使用表别名
- 子查询

### 内连接

![内连接](http://xwhking.object.kuken.tech/picgo/image-20230605193153734.png)

内连接查询的是两张表交集部分的数据。

内连接的语法分为两种: 隐式内连接、显式内连接。

- 隐式内连接
```sql
SELECT 字段列表 FROM 表1 , 表2 WHERE 条件 ... ;
```

- 显式内连接
```sql
SELECT 字段列表 FROM 表1 [ INNER ] JOIN 表2 ON 连接条件 ... ;
```

**案例**：
A. 查询每一个员工的姓名 , 及关联的部门的名称 (隐式内连接实现)
```sql
select emp.name , dept.name from emp , dept where emp.dept_id = dept.id ;
-- 为每一张表起别名,简化SQL编写
select e.name,d.name from emp e , dept d where e.dept_id = d.id;
```

B. 查询每一个员工的姓名 , 及关联的部门的名称 (显式内连接实现)
```sql
select e.name, d.name from emp e inner join dept d on e.dept_id = d.id;
-- 为每一张表起别名,简化SQL编写
select e.name, d.name from emp e join dept d on e.dept_id = d.id;
```

**注意**：一旦为表起了别名，就不能再使用表名来指定对应的字段了，此时只能够使用别名来指定字段。

### 外连接

![外连接](http://xwhking.object.kuken.tech/picgo/image-20230605193511404.png)

外连接分为两种，分别是：左外连接 和 右外连接。

- 左外连接
```sql
SELECT 字段列表 FROM 表1 LEFT [ OUTER ] JOIN 表2 ON 条件 ... ;
```
左外连接相当于查询表1(左表)的所有数据，当然也包含表1和表2交集部分的数据。

- 右外连接
```sql
SELECT 字段列表 FROM 表1 RIGHT [ OUTER ] JOIN 表2 ON 条件 ... ;
```
右外连接相当于查询表2(右表)的所有数据，当然也包含表1和表2交集部分的数据。

**案例**：
A. 查询emp表的所有数据, 和对应的部门信息
```sql
select e.*, d.name from emp e left outer join dept d on e.dept_id = d.id;
select e.*, d.name from emp e left join dept d on e.dept_id = d.id;
```

B. 查询dept表的所有数据, 和对应的员工信息(右外连接)
```sql
select d.*, e.* from emp e right outer join dept d on e.dept_id = d.id;
select d.*, e.* from dept d left outer join emp e on e.dept_id = d.id;
```

**注意事项**： 左外连接和右外连接是可以相互替换的，只需要调整在连接查询时SQL中，表结构的先后顺序就可以了。而我们在日常开发使用时，更偏向于左外连接。

### 自连接

#### 自连接查询

自连接查询，顾名思义，就是自己连接自己，也就是把一张表连接查询多次。

```sql
SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件 ... ;
```

而对于自连接查询，可以是内连接查询，也可以是外连接查询。

**案例**：
A. 查询员工 及其 所属领导的名字
```sql
select a.name , b.name from emp a , emp b where a.managerid = b.id;
```

B. 查询所有员工 emp 及其领导的名字 emp , 如果员工没有领导, 也需要查询出来
```sql
select a.name '员工', b.name '领导' from emp a left join emp b on a.managerid = b.id;
```

**注意事项**：在自连接查询中，必须要为表起别名，要不然我们不清楚所指定的条件、返回的字段，到底是哪一张表的字段。

### 联合查询

对于union查询，就是把多次查询的结果合并起来，形成一个新的查询结果集。

```sql
SELECT 字段列表 FROM 表A ...
UNION [ ALL ]
SELECT 字段列表 FROM 表B ....;
```

- 对于联合查询的多张表的列数必须保持一致，字段类型也需要保持一致。
- union all 会将全部的数据直接合并在一起，union 会对合并之后的数据去重。

**案例**：
A. 将薪资低于 5000 的员工 , 和 年龄大于 50 岁的员工全部查询出来.
```sql
select * from emp where salary < 5000
union all
select * from emp where age > 50;

-- 去重查询
select * from emp where salary < 5000
union
select * from emp where age > 50;
```

**注意**： 如果多条查询语句查询出来的结果，字段数量不一致，在进行union/union all联合查询时，将会报错。

### 子查询

**概念**：SQL语句中嵌套SELECT语句，称为嵌套查询，又称子查询。

```sql
SELECT * FROM t1 WHERE column1 = ( SELECT column1 FROM t2 );
```

子查询外部的语句可以是INSERT / UPDATE / DELETE / SELECT 的任何一个。

**分类**：

根据子查询结果不同，分为：
A. 标量子查询（子查询结果为单个值）
B. 列子查询(子查询结果为一列)
C. 行子查询(子查询结果为一行)
D. 表子查询(子查询结果为多行多列)

根据子查询位置，分为：
A. WHERE之后
B. FROM之后
C. SELECT之后

#### 标量子查询

子查询返回的结果是单个值（数字、字符串、日期等），最简单的形式，这种子查询称为标量子查询。

常用的操作符：`= <> > >= < <=`

**案例**：
A. 查询 "销售部" 的所有员工信息
```sql
select * from emp where dept_id = (select id from dept where name = '销售部');
```

B. 查询在 "方东白" 入职之后的员工信息
```sql
select * from emp where entrydate > (select entrydate from emp where name = '方东白');
```

#### 列子查询

子查询返回的结果是一列（可以是多行），这种子查询称为列子查询。

常用的操作符：IN 、NOT IN 、 ANY 、SOME 、 ALL

| 操作符 | 描述                                   |
| ------ | -------------------------------------- |
| IN     | 在指定集合范围之内，多选一             |
| NOT IN | 不在指定的集合范围之内                 |
| ANY    | 子查询返回列表中，有任意一个满足即可   |
| SOME   | 与ANY等同，使用SOME的地方都可以使用ANY |
| ALL    | 子查询返回列表的所有值都必须满足       |

**案例**：
A. 查询 "销售部" 和 "市场部" 的所有员工信息
```sql
select * from emp where dept_id in (select id from dept where name = '销售部' or name = '市场部');
```

B. 查询比 财务部 所有人工资都高的员工信息
```sql
select * from emp where salary > all ( select salary from emp where dept_id = (select id from dept where name = '财务部') );
```

C. 查询比研发部其中任意一人工资高的员工信息
```sql
select * from emp where salary > any ( select salary from emp where dept_id = (select id from dept where name = '研发部') );
```

#### 行子查询

子查询返回的结果是一行（可以是多列），这种子查询称为行子查询。

常用的操作符：`= 、<> 、IN 、NOT IN`

**案例**：
A. 查询与 "张无忌" 的薪资及直属领导相同的员工信息 ;
```sql
select * from emp where (salary,managerid) = (select salary, managerid from emp where name = '张无忌');
```

#### 表子查询

子查询返回的结果是多行多列，这种子查询称为表子查询。

常用的操作符：IN

**案例**：
A. 查询与 "鹿杖客" , "宋远桥" 的职位和薪资相同的员工信息
```sql
select * from emp where (job,salary) in ( select job, salary from emp where name = '鹿杖客' or name = '宋远桥' );
```

B. 查询入职日期是 "2006-01-01" 之后的员工信息 , 及其部门信息
```sql
select e.*, d.* from (select * from emp where entrydate > '2006-01-01') e left join dept d on e.dept_id = d.id ;
```

### 多表查询案例

**数据环境准备**：
```sql
create table salgrade(
grade int,
losal int,
hisal int
) comment '薪资等级表';
insert into salgrade values (1,0,3000);
insert into salgrade values (2,3001,5000);
insert into salgrade values (3,5001,8000);
insert into salgrade values (4,8001,10000);
insert into salgrade values (5,10001,15000);
insert into salgrade values (6,15001,20000);
insert into salgrade values (7,20001,25000);
insert into salgrade values (8,25001,30000);
```

1). 查询员工的姓名、年龄、职位、部门信息 （隐式内连接）
```sql
select e.name , e.age , e.job , d.name from emp e , dept d where e.dept_id = d.id;
```

2). 查询年龄小于30岁的员工的姓名、年龄、职位、部门信息（显式内连接）
```sql
select e.name , e.age , e.job , d.name from emp e inner join dept d on e.dept_id = d.id where e.age < 30;
```

3). 查询拥有员工的部门ID、部门名称
```sql
select distinct d.id , d.name from emp e , dept d where e.dept_id = d.id;
```

4). 查询所有年龄大于40岁的员工, 及其归属的部门名称; 如果员工没有分配部门, 也需要展示出来(外连接)
```sql
select e.*, d.name from emp e left join dept d on e.dept_id = d.id where e.age > 40 ;
```

5). 查询所有员工的工资等级
```sql
-- 方式一
select e.* , s.grade , s.losal, s.hisal from emp e , salgrade s where e.salary >= s.losal and e.salary <= s.hisal;
-- 方式二
select e.* , s.grade , s.losal, s.hisal from emp e , salgrade s where e.salary between s.losal and s.hisal;
```

6). 查询 "研发部" 所有员工的信息及 工资等级
```sql
select e.* , s.grade from emp e , dept d , salgrade s where e.dept_id = d.id and ( e.salary between s.losal and s.hisal ) and d