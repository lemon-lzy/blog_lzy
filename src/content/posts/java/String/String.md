---
title: String
published: 2024-07-12
description: '探讨一下java中String类的基本使用'
image: '../cover.png'
tags: ["java"]
category: 'java'
draft: false 
lang: ''
---

# Java String类详解
在Java中，`java.lang.String`是处理字符串的核心类，用于表示不可变的字符序列。它是Java开发中最常用的类之一，具有**不可变性**、**常量池优化**、丰富的字符串操作方法等特性。本文将从String类的定位、核心特性、底层实现、常用方法及使用场景等方面进行详细讲解。

## 一、String类的定位与核心特性
### 1. 类的基础信息
- `String`类位于`java.lang`包下，无需手动导入即可使用。
- 它被`final`修饰，**不能被继承**，确保其方法和属性不会被子类重写或修改。
- 实现了多个核心接口：
  - `Serializable`：支持序列化，可在网络传输或持久化时使用。
  - `Comparable<String>`：支持字符串的自然排序（按Unicode码值比较）。
  - `CharSequence`：表示字符序列，与`StringBuffer`、`StringBuilder`统一接口。

### 2. 核心特性：不可变性（Immutability）
`String`对象一旦创建，其内部的字符序列就无法被修改，这是String类最关键的特性。

#### 底层实现支撑
在Java 9及以后版本，String的底层存储从`char[]`（每个char占2字节）改为`byte[]`（每个byte占1字节）+ 编码标记（`coder`），以节省内存（Latin-1字符占1字节，UTF-16字符占2字节）。核心源码简化如下：
```java
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
    private final byte[] value; // 存储字符的字节数组，final修饰确保不可变
    private final byte coder;   // 编码标记：0表示Latin-1，1表示UTF-16
    // 其他属性和方法...
}
```
- `value`数组被`final`修饰，意味着一旦初始化，其引用无法指向新的数组。
- `value`数组的访问权限为`private`，且String类未提供修改数组元素的方法，进一步保证字符序列不可变。

#### 不可变性的表现
当对字符串进行拼接、替换等操作时，**不会修改原字符串**，而是创建一个新的String对象：
```java
String s = "hello";
s += " world"; // 并非修改原s，而是创建新字符串"hello world"，s指向新对象
```

#### 不可变性的优势
1. **线程安全**：不可变对象在多线程环境下无需同步，可安全共享。
2. **支持常量池**：字符串常量池（String Pool）可缓存不可变的String对象，减少内存占用。
3. **哈希码缓存**：String的`hashCode()`基于字符序列计算，因不可变，哈希码可缓存（`hash`属性存储），提升哈希集合（如`HashMap`）的性能。
4. **安全性**：作为参数传递时（如文件路径、数据库连接串），不可变可避免被意外修改导致的安全问题。

## 二、字符串的创建方式
String类的创建主要有**字面量创建**和**new关键字创建**两种方式，二者在内存分配上有显著差异。

### 1. 字面量创建
通过双引号`""`直接定义字符串，对象会存储在**字符串常量池**（方法区的一部分）中：
```java
String s1 = "java";
String s2 = "java";
System.out.println(s1 == s2); // true，指向常量池中的同一个对象
```
- 原理：JVM会先检查常量池是否存在该字符串，若存在则直接返回引用；若不存在则创建新对象并放入常量池。

### 2. new关键字创建
通过`new String()`创建的对象，**会在堆内存中创建新实例**，同时可能在常量池创建对应的字面量（若不存在）：
```java
String s3 = new String("java");
String s4 = new String("java");
System.out.println(s3 == s4); // false，堆中不同的对象实例
System.out.println(s3.equals(s4)); // true，字符序列相同
```
- 内存分布：`new String("java")`会创建**1个或2个对象**：
  1. 若常量池无"java"，先在常量池创建"java"，再在堆中创建指向该常量的String对象（共2个）。
  2. 若常量池已有"java"，仅在堆中创建新对象（共1个）。

### 3. 手动入池：`intern()`方法
`intern()`方法可将堆中的String对象**手动加入常量池**（若不存在），并返回常量池中的引用：
```java
String s5 = new String("java").intern();
String s6 = "java";
System.out.println(s5 == s6); // true，s5指向常量池对象
```
- 适用场景：当需要大量重复字符串时，使用`intern()`可减少堆内存占用。

## 三、String类的常用方法
String类提供了丰富的方法用于字符串操作，以下按功能分类讲解核心方法。

### 1. 字符/子串获取
| 方法 | 功能 |
|------|------|
| `char charAt(int index)` | 获取指定索引位置的字符（索引从0开始） |
| `String substring(int beginIndex)` | 截取从`beginIndex`到末尾的子串 |
| `String substring(int beginIndex, int endIndex)` | 截取`[beginIndex, endIndex)`区间的子串（左闭右开） |
| `char[] toCharArray()` | 将字符串转换为字符数组 |

**示例**：
```java
String s = "Hello Java";
System.out.println(s.charAt(1)); // e
System.out.println(s.substring(6)); // Java
System.out.println(s.substring(0, 5)); // Hello
char[] chars = s.toCharArray(); // ['H','e','l','l','o',' ','J','a','v','a']
```

### 2. 字符串比较
| 方法 | 功能 |
|------|------|
| `boolean equals(Object obj)` | 比较两个字符串的**字符序列是否完全相同**（区分大小写） |
| `boolean equalsIgnoreCase(String anotherString)` | 比较字符序列是否相同（不区分大小写） |
| `int compareTo(String anotherString)` | 按Unicode码值比较，返回差值（负数：当前串小；0：相等；正数：当前串大） |
| `int compareToIgnoreCase(String str)` | 忽略大小写的Unicode码值比较 |
| `boolean startsWith(String prefix)` | 判断是否以指定前缀开头 |
| `boolean endsWith(String suffix)` | 判断是否以指定后缀结尾 |

**示例**：
```java
String s1 = "Java";
String s2 = "java";
System.out.println(s1.equals(s2)); // false
System.out.println(s1.equalsIgnoreCase(s2)); // true
System.out.println(s1.compareTo(s2)); // -32（'J'的Unicode码比'j'小32）
System.out.println(s1.startsWith("Ja")); // true
System.out.println(s2.endsWith("va")); // true
```

### 3. 字符串查找
| 方法 | 功能 |
|------|------|
| `int indexOf(int ch)` | 查找字符`ch`首次出现的索引，未找到返回-1 |
| `int indexOf(int ch, int fromIndex)` | 从`fromIndex`开始查找字符`ch`首次出现的索引 |
| `int indexOf(String str)` | 查找子串`str`首次出现的索引 |
| `int lastIndexOf(int ch)` | 查找字符`ch`最后一次出现的索引 |
| `int lastIndexOf(String str)` | 查找子串`str`最后一次出现的索引 |

**示例**：
```java
String s = "Hello Java, Hello World";
System.out.println(s.indexOf('o')); // 4
System.out.println(s.indexOf("Hello", 6)); // 13
System.out.println(s.lastIndexOf('l')); // 21
```

### 4. 字符串修改
由于String不可变，修改操作会返回新的String对象：

| 方法 | 功能 |
|------|------|
| `String concat(String str)` | 拼接字符串，等价于`+`运算符 |
| `String replace(char oldChar, char newChar)` | 将所有`oldChar`替换为`newChar` |
| `String replace(CharSequence target, CharSequence replacement)` | 替换所有匹配的子串（非正则方式） |
| `String replaceAll(String regex, String replacement)` | 按正则表达式替换所有匹配的子串 |
| `String replaceFirst(String regex, String replacement)` | 按正则表达式替换第一个匹配的子串 |
| `String toLowerCase()` | 将字符串所有字符转换为小写 |
| `String toUpperCase()` | 将字符串所有字符转换为大写 |
| `String trim()` | 去除首尾的ASCII空白字符（Java 11前仅去除空格、制表符等；Java 11后支持更多空白字符） |
| `String strip()` | Java 11新增，去除首尾所有Unicode空白字符（比`trim`更全面） |
| `String stripLeading()` | Java 11新增，仅去除字符串开头的Unicode空白字符 |
| `String stripTrailing()` | Java 11新增，仅去除字符串尾部的Unicode空白字符 |

**示例**：
```java
String s = "  Hello Java  ";
System.out.println(s.concat(" World")); // "  Hello Java  World"
System.out.println(s.replace('a', 'A')); // "  Hello JAvA  "
System.out.println(s.replace("Java", "Python")); // "  Hello Python  "
System.out.println(s.replaceAll("\\s+", "")); // "HelloJava"
System.out.println(s.replaceFirst("\\s+", "-")); // "-Hello Java  "
System.out.println(s.toLowerCase()); // "  hello java  "
System.out.println(s.toUpperCase()); // "  HELLO JAVA  "
System.out.println(s.trim()); // "Hello Java"
System.out.println("　Hello　".strip()); // "Hello"（去除全角空格）
```

### 5. 字符串分割与拼接
| 方法 | 功能 |
|------|------|
| `String[] split(String regex)` | 按正则表达式分割字符串，返回字符串数组 |
| `static String join(CharSequence delimiter, CharSequence... elements)` | 按指定分隔符拼接多个字符串 |

**示例**：
```java
// 分割
String s = "apple,banana,orange";
String[] fruits = s.split(","); // ["apple", "banana", "orange"]

// 拼接
String joined = String.join("-", "Java", "Spring", "MySQL"); // "Java-Spring-MySQL"
```

### 6. 其他工具方法
| 方法 | 功能 |
|------|------|
| `boolean isEmpty()` | 判断字符串是否为空（长度为0） |
| `int length()` | 返回字符串的字符长度 |
| `static String valueOf(Object obj)` | 将任意类型对象转换为字符串（重载方法支持基本类型） |
| `boolean contains(CharSequence s)` | 判断是否包含指定子串 |

**示例**：
```java
String s = "";
System.out.println(s.isEmpty()); // true
System.out.println("Java".length()); // 4
System.out.println(String.valueOf(123)); // "123"
System.out.println("Hello".contains("ell")); // true
```

## 四、String与StringBuffer、StringBuilder的区别
由于String的不可变性，频繁拼接字符串会创建大量临时对象，效率低下。Java提供了`StringBuffer`和`StringBuilder`来解决此问题，三者的核心区别如下：

| 特性 | String | StringBuffer | StringBuilder |
|------|--------|--------------|---------------|
| 可变性 | 不可变 | 可变 | 可变 |
| 线程安全 | 线程安全（不可变） | 线程安全（方法加`synchronized`） | 非线程安全 |
| 性能 | 拼接操作效率低 | 效率中等（同步开销） | 效率高（无同步） |
| 适用场景 | 字符串常量、少量操作 | 多线程环境下的字符串拼接 | 单线程环境下的字符串拼接 |

**示例**：
```java
// 高效拼接：StringBuilder
StringBuilder sb = new StringBuilder();
sb.append("Hello");
sb.append(" ");
sb.append("Java");
String result = sb.toString(); // "Hello Java"
```

## 五、String的常见面试题与坑点
### 1. `==`与`equals()`的区别
- `==`：比较**引用地址**（是否指向同一对象）。
- `equals()`：比较String的**字符序列**（重写后逻辑）。
```java
String s1 = "java";
String s2 = new String("java");
System.out.println(s1 == s2); // false（地址不同）
System.out.println(s1.equals(s2)); // true（字符相同）
```

### 2. 字符串拼接的底层优化
对于`String s = a + b + c`（a、b、c为字符串字面量），JVM会优化为直接创建`"abc"`，避免中间对象；但如果是变量拼接（如`s = s1 + s2`），底层会创建`StringBuilder`完成拼接，效率低于直接使用`StringBuilder`。

### 3. 空字符串与null的区别
- `""`：是一个有效的String对象，`value`数组长度为0，`length()`返回0。
- `null`：表示引用未指向任何对象，调用`null`的String方法会抛出`NullPointerException`。
```java
String s1 = "";
String s2 = null;
System.out.println(s1.isEmpty()); // true
System.out.println(s2.isEmpty()); // 抛出NullPointerException
```

## 六、总结
String类是Java中处理字符序列的基础类，其**不可变性**是设计的核心，带来了线程安全、常量池优化等优势，但也导致频繁修改时效率低下（需借助`StringBuffer`/`StringBuilder`）。掌握String的创建方式、内存分布、常用方法及与可变字符串类的区别，是Java开发的基础要求。在实际开发中，应根据场景选择合适的字符串类：常量字符串用String，单线程拼接用StringBuilder，多线程拼接用StringBuffer。