---
title: object
published: 2024-07-11
description: '探讨一下java的Object类'
image: '../cover.png'
tags: ["java"]
category: 'java'
draft: false 
lang: ''
---

# Object类的定位与常用方法详解
在Java中，`java.lang.Object`类是所有类的根类，**所有Java类都直接或间接继承自Object类**，这是Java面向对象体系的基础特性。无论是自定义类、Java内置类（如`String`、`List`），还是数组，都隐式继承了Object类的属性和方法。本文将从Object类的定位、核心特性出发，详细讲解其常用方法的功能、使用场景与重写规范。

## 一、Object类的定位
### 1. 类层级的根节点
Java的继承体系是单继承结构，所有类最终都会追溯到Object类。即使定义类时没有显式声明`extends Object`，编译器也会自动为其添加该继承关系。例如：
```java
// 显式继承Object（可省略）
public class Person extends Object {}
// 隐式继承Object，与上面等价
public class Student {}
```
数组也属于Object的子类，例如`String[]`、`int[]`都可以赋值给Object类型变量：
```java
Object arr = new String[5];
Object intArr = new int[10];
```

### 2. 提供通用行为的基础
Object类定义了一组所有对象都应具备的通用方法，这些方法为Java对象提供了基础行为，如对象的比较、哈希计算、字符串表示、垃圾回收回调等。开发者可以根据业务需求重写这些方法，定制对象的行为。

### 3. 支持多态的核心载体
由于所有类都继承自Object，Object类型的引用可以指向任意类型的对象，这是Java多态的重要体现。例如，集合类`ArrayList<Object>`可以存储任意类型的对象，方法参数使用Object类型可接收任意参数。

## 二、Object类的常用方法
Object类提供了11个方法，其中常用的核心方法包括：`toString()`、`equals(Object obj)`、`hashCode()`、`getClass()`、`clone()`、`finalize()`（已过时）、`wait()`/`notify()`/`notifyAll()`（线程相关）。以下按使用频率和重要性逐一讲解。

### 1. `toString()`方法
#### 功能定义
返回对象的字符串表示形式，默认实现返回**类名@哈希码的十六进制表示**，格式为：`getClass().getName() + "@" + Integer.toHexString(hashCode())`。

#### 默认实现的问题
默认的`toString()`返回值可读性差，无法直观反映对象的属性信息。例如：
```java
class Person {
    private String name;
    private int age;
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

public class Test {
    public static void main(String[] args) {
        Person p = new Person("张三", 20);
        System.out.println(p.toString()); // 输出：Person@1b6d3586
    }
}
```

#### 重写规范
通常需要重写`toString()`，返回包含对象关键属性的字符串，便于调试和日志输出。重写示例：
```java
class Person {
    private String name;
    private int age;
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // 重写toString()
    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + "}";
    }
}

public class Test {
    public static void main(String[] args) {
        Person p = new Person("张三", 20);
        System.out.println(p); // 直接打印对象会自动调用toString()，输出：Person{name='张三', age=20}
    }
}
```

#### 注意点
- 重写时应保证字符串包含对象的核心属性，避免敏感信息泄露。
- `System.out.println(obj)`、日志框架输出对象时，会自动调用`toString()`。

### 2. `equals(Object obj)`方法
#### 功能定义
用于判断当前对象与传入的对象是否“相等”。默认实现基于**引用相等**（即两个对象是否指向内存中的同一地址），源码如下：
```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

#### 默认实现的局限
默认的`equals()`仅判断引用是否相同，无法满足业务上“属性相等即对象相等”的需求。例如，两个`Person`对象的`name`和`age`都相同，但引用不同，默认`equals()`会返回`false`。

#### 重写规则（必须遵循）
根据Java官方规范，重写`equals()`需满足以下5个特性：
1. **自反性**：`x.equals(x)`必须返回`true`。
2. **对称性**：若`x.equals(y)`为`true`，则`y.equals(x)`也必须为`true`。
3. **传递性**：若`x.equals(y)`和`y.equals(z)`为`true`，则`x.equals(z)`也必须为`true`。
4. **一致性**：只要对象的属性未变，多次调用`equals()`结果应一致。
5. **非空性**：`x.equals(null)`必须返回`false`。

#### 重写示例
以`Person`类为例，重写`equals()`判断`name`和`age`是否相等：
```java
class Person {
    private String name;
    private int age;
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public boolean equals(Object obj) {
        // 1. 引用相同，直接返回true
        if (this == obj) return true;
        // 2. 传入对象为null或类型不同，返回false
        if (obj == null || getClass() != obj.getClass()) return false;
        // 3. 强制类型转换并比较属性
        Person person = (Person) obj;
        return age == person.age && Objects.equals(name, person.name);
    }
}
```
**说明**：使用`Objects.equals(name, person.name)`而非直接`name.equals(person.name)`，可避免`name`为`null`时抛出`NullPointerException`。

#### 与`==`的区别
| 符号 | 作用对象 | 比较规则 |
|------|----------|----------|
| `==` | 基本类型：比较值；引用类型：比较引用地址 | 直接比较内存或值 |
| `equals()` | 仅引用类型 | 默认比较引用，重写后可比较属性 |

### 3. `hashCode()`方法
#### 功能定义
返回对象的哈希码值（int类型），用于支持哈希表（如`HashMap`、`HashSet`）的高效操作。哈希码是对象的唯一标识（理论上可能冲突），默认实现基于对象的内存地址计算。

#### 核心契约（与`equals()`强关联）
重写`equals()`时**必须重写`hashCode()`**，否则会违反以下契约：
1. 若两个对象通过`equals()`判断相等，则它们的`hashCode()`必须返回相同的值。
2. 若两个对象的`hashCode()`相同，`equals()`不一定返回`true`（哈希冲突）。

#### 违反契约的后果
若仅重写`equals()`而不重写`hashCode()`，当对象存入哈希集合（如`HashSet`）时，会出现“相等的对象却被视为不同元素”的问题。例如：
```java
Person p1 = new Person("张三", 20);
Person p2 = new Person("张三", 20);
Set<Person> set = new HashSet<>();
set.add(p1);
set.add(p2);
// 若未重写hashCode()，set中会包含两个元素（p1和p2的hashCode不同）
System.out.println(set.size()); // 输出2，不符合预期
```

#### 重写示例
结合`Person`类，重写`hashCode()`基于`name`和`age`计算哈希值：
```java
class Person {
    private String name;
    private int age;
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public boolean equals(Object obj) {
        // 省略equals重写代码
    }

    @Override
    public int hashCode() {
        // 使用Objects.hash()生成哈希码，自动处理null值
        return Objects.hash(name, age);
    }
}
```
**说明**：`Objects.hash()`会将传入的属性组合计算哈希码，比手动计算更简洁且不易出错。

### 4. `getClass()`方法
#### 功能定义
返回对象的运行时类（`Class`对象），该方法是`final`的，无法被重写。源码如下：
```java
public final Class<?> getClass() {}
```

#### 常用场景
1. **判断对象的实际类型**：通过`getClass()`可获取对象的运行时类型，区别于编译时类型。
2. **反射操作**：基于`Class`对象实现反射（如创建对象、调用方法、访问属性）。

#### 使用示例
```java
class Animal {}
class Dog extends Animal {}

public class Test {
    public static void main(String[] args) {
        Animal animal = new Dog();
        // 编译时类型：Animal，运行时类型：Dog
        System.out.println(animal.getClass().getName()); // 输出：Dog
        System.out.println(animal instanceof Animal); // true
        System.out.println(animal.getClass() == Animal.class); // false
        System.out.println(animal.getClass() == Dog.class); // true
    }
}
```

### 5. `clone()`方法
#### 功能定义
创建并返回当前对象的副本，实现对象的**浅拷贝**（默认）。该方法是`protected`的，需重写为`public`才能被外部调用，且实现类需实现`Cloneable`接口（标记接口，无方法），否则会抛出`CloneNotSupportedException`。

#### 浅拷贝与深拷贝
1. **浅拷贝**：仅复制对象的基本类型属性，引用类型属性只复制属性的引用值,因此原对象和拷贝对象的引用类型属性指向同一块内存。
2. **深拷贝**：不仅复制基本类型属性，还会复制引用类型属性的对象，原对象和副本完全独立。

#### 使用示例
**浅拷贝实现**：
```java
class Person implements Cloneable {
    private String name;
    private int age;
    private Address address; // 引用类型属性

    public Person(String name, int age, Address address) {
        this.name = name;
        this.age = age;
        this.address = address;
    }

    // 重写clone()为public
    @Override
    public Person clone() throws CloneNotSupportedException {
        return (Person) super.clone();
    }
}

class Address {
    private String city;
    public Address(String city) {
        this.city = city;
    }
}

public class Test {
    public static void main(String[] args) throws CloneNotSupportedException {
        Address addr = new Address("北京");
        Person p1 = new Person("张三", 20, addr);
        Person p2 = p1.clone();
        
        // 浅拷贝：p1和p2的address引用指向同一对象
        System.out.println(p1.getAddress() == p2.getAddress()); // true
        p2.getAddress().setCity("上海");
        System.out.println(p1.getAddress().getCity()); // 输出：上海
    }
}
```
**深拷贝实现**（重写`clone()`时复制引用类型属性）：
```java
@Override
public Person clone() throws CloneNotSupportedException {
    Person clone = (Person) super.clone();
    // 手动复制引用类型属性，实现深拷贝
    clone.address = address.clone(); // 需让Address也实现Cloneable并重写clone()
    return clone;
}
```

#### 注意点
- `clone()`方法并非构造方法，不会调用构造函数。
- 对于不可变对象（如`String`），浅拷贝已足够；对于可变引用类型，需实现深拷贝避免数据共享。

### 6. `finalize()`方法（已过时）
#### 功能定义
该方法在对象被垃圾回收器回收前被调用，用于释放资源（如文件句柄、网络连接）。Java 9中标记为过时，Java 18中正式移除，推荐使用`try-with-resources`或手动关闭资源。

#### 缺陷
- 执行时机不确定：垃圾回收的触发时机由JVM决定，无法保证`finalize()`何时执行。
- 可能导致内存泄漏：若`finalize()`中出现异常，对象仍可能无法被回收。

### 7. 线程相关方法：`wait()`/`notify()`/`notifyAll()`
这三个方法是`final`的，用于实现线程间的通信，基于对象的**监视器锁（Monitor）** 工作，必须在同步代码块/方法中调用，否则会抛出`IllegalMonitorStateException`。

#### 方法功能
1. **`wait()`**：让当前线程释放对象锁并进入等待状态，直到其他线程调用`notify()`/`notifyAll()`唤醒，或等待超时（重载方法`wait(long timeout)`）。
2. **`notify()`**：唤醒在此对象监视器上等待的**单个随机线程**。
3. **`notifyAll()`**：唤醒在此对象监视器上等待的**所有线程**。

#### 使用示例
```java
class Message {
    private String content;
    private boolean isEmpty = true;

    public synchronized void put(String content) {
        while (!isEmpty) {
            try {
                wait(); // 队列非空，生产者等待
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
        this.content = content;
        isEmpty = false;
        notifyAll(); // 唤醒消费者
    }

    public synchronized String take() {
        while (isEmpty) {
            try {
                wait(); // 队列为空，消费者等待
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
        isEmpty = true;
        notifyAll(); // 唤醒生产者
        return content;
    }
}
```

## 三、总结
Object类作为Java继承体系的根，为所有对象提供了通用的基础行为，其核心方法的设计体现了Java面向对象的核心思想：
1. `toString()`：定制对象的字符串表示，提升调试可读性。
2. `equals()` & `hashCode()`：实现对象的业务相等判断，支撑哈希集合的正常工作。
3. `getClass()`：获取对象运行时类型，是反射的基础。
4. `clone()`：实现对象拷贝，需区分浅拷贝与深拷贝。
5. `wait()`/`notify()`/`notifyAll()`：实现线程间通信，是并发编程的基础。

重写Object类的方法时，必须遵循官方规范（如`equals()`的五大特性、`hashCode()`与`equals()`的契约），否则会导致程序出现难以排查的逻辑错误或性能问题。