---
title: java代理，动态代理与AOP
published: 2025-12-29
description: ''
image: './cover.png'
tags: ["spring"]
category: 'spring'
draft: false 
lang: ''
---
# 代理模式
代理模式指的是在不改变原有对象的情况下，引入代理对象对原有对象进行访问，进行功能的增强。

**例子**:动态代理，Spring AOP代理，RPC调用;

# 代理实现
## 一、静态代理
**核心特点**:

静态代理是编译期就确定代理类与目标类的关系，需要手动编写代理类。

**实现步骤**:

定义公共接口（规范目标类和代理类的行为）；

实现目标类（真正执行业务逻辑的类）；

实现代理类（持有目标类对象，在接口方法中包装目标方法，添加额外逻辑）。

## 二、动态代理
动态代理是运行期动态生成代理对象（通过反射机制在程序运行时动态创建代理对象）去访问目标对象，对目标对象进行增强。
### 1. JDK动态代理
#### 核心特点
   JDK动态代理是基于接口进行代理的，目标类需要实现接口;

   基于 Java 反射的 java.lang.reflect.Proxy 类和 InvocationHandler 接口实现；
   #### 实现步骤
   定义公共接口（与静态代理一致）；

   实现目标类（与静态代理一致）；

   实现 InvocationHandler 接口（封装代理增强逻辑，持有目标对象）；

   通过 Proxy.newProxyInstance() 方法动态生成代理对象。
#### 代码：
````
// 1. 公共接口（复用静态代理的UserService）
// 2. 目标类（复用静态代理的UserServiceImpl）

// 3. 实现InvocationHandler接口
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class UserServiceJdkProxyHandler implements InvocationHandler {
// 持有目标对象（使用Object类型，提高通用性）
private final Object target;

    public UserServiceJdkProxyHandler(Object target) {
        this.target = target;
    }

    /**
     * 代理逻辑核心方法
     * @param proxy 动态生成的代理对象（一般不使用）
     * @param method 目标类被调用的方法
     * @param args 目标方法的参数
     * @return 目标方法的返回值
     * @throws Throwable 目标方法抛出的异常
     */
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // 前置增强逻辑
        System.out.println("JDK动态代理：方法" + method.getName() + "执行前，校验参数");
        // 反射调用目标类的方法
        Object result = method.invoke(target, args);
        // 后置增强逻辑
        System.out.println("JDK动态代理：方法" + method.getName() + "执行后，记录日志");
        return result;
    }
}

// 测试类
import java.lang.reflect.Proxy;

public class JdkProxyTest {
public static void main(String[] args) {
// 1. 创建目标对象
UserService target = new UserServiceImpl();
// 2. 创建InvocationHandler实例
InvocationHandler handler = new UserServiceJdkProxyHandler(target);
// 3. 动态生成代理对象
UserService proxy = (UserService) Proxy.newProxyInstance(
target.getClass().getClassLoader(), // 目标类的类加载器
target.getClass().getInterfaces(),  // 目标类实现的接口
handler                              // 代理逻辑处理器
);
// 4. 通过代理对象调用方法
proxy.addUser("李四");
}
}
````
   #### 底层原理：
JDK底层原理是代理类和目标类实现同一个接口，通过重写接口+调用目标类接口+增强进行实现
### 2. CGLIB 动态代理
CGlib动态代理是基于子类进行动态代理的，目标类不需要实现接口。
#### 实现步骤：
引入 CGLIB 依赖（Spring 等框架已内置，独立使用需手动引入）；

实现目标类（无需实现接口）；

实现 MethodInterceptor 接口（封装代理增强逻辑）；

通过 Enhancer 类创建动态代理对象。

#### 代码示例
第一步：引入 CGLIB 依赖（Maven）
````
xml
<dependency>
<groupId>cglib</groupId>
<artifactId>cglib</artifactId>
<version>3.3.0</version>
</dependency>
````
第二步：编码实现
````
// 1. 目标类（无需实现接口）
public class OrderService {
public void createOrder(String orderNo) {
System.out.println("目标类：创建订单 " + orderNo);
}
}

// 2. 实现MethodInterceptor接口
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;
import java.lang.reflect.Method;

public class OrderServiceCglibInterceptor implements MethodInterceptor {
/**
* 代理逻辑核心方法
* @param obj 代理对象（子类对象）
* @param method 目标方法
* @param args 目标方法参数
* @param proxy 方法代理对象（用于快速调用父类方法）
* @return 目标方法返回值
* @throws Throwable 目标方法异常
*/
@Override
public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
// 前置增强逻辑
System.out.println("CGLIB动态代理：方法" + method.getName() + "执行前，校验订单编号");
// 调用目标类的方法（两种方式：proxy.invokeSuper 或 method.invoke）
Object result = proxy.invokeSuper(obj, args);
// 后置增强逻辑
System.out.println("CGLIB动态代理：方法" + method.getName() + "执行后，保存订单日志");
return result;
}
}
````
// 3. 测试类
````
import net.sf.cglib.proxy.Enhancer;

public class CglibProxyTest {
public static void main(String[] args) {
// 1. 创建Enhancer对象（CGLIB的核心类，用于生成代理对象）
Enhancer enhancer = new Enhancer();
// 2. 设置父类（目标类）
enhancer.setSuperclass(OrderService.class);
// 3. 设置方法拦截器
enhancer.setCallback(new OrderServiceCglibInterceptor());
// 4. 动态生成代理对象（子类实例）
OrderService proxy = (OrderService) enhancer.create();
// 5. 调用代理方法
proxy.createOrder("ORDER_20251229_001");
}
}
````
# springAOP
#### 定义
springAOP说白了就是在spring运行时动态的使用jdk代理（spring框架默认）或者cglib代理动态（Spring boot2.x之后推荐）的帮我们创建代理类，使用代理类去增强目标类。
#### 例子：
我们想要有一个方法，能在执行前开启事务，在结束后提交事务，执行失败回滚事务，我们就可以使用@Transational注解，该注解的原理就是基于动态代理实现的AOP
#### 代理不生效场景：
上面我们说到了@Transational注解本质上是动态代理，那@transational注解失效的场景就好解释了：

**不走代理而失效的**：

1.方法是static修饰的静态方法。静态方法不属于任何实例，而是类级别的方法。

2.方法或者类被 final修饰。如果是JDK动态代理是基于重写，final修饰方法会失效；如果是cglib动态代理，是基于子类+重写，那使用final修饰类/方法都会失效？

3.方法不是被public修饰的。你一个private的方法人家咋走代理（jdk动态代理和cglib动态代理这两种spring都规定只有public才可以走代理）。

4.这个类没被spring管理（AOP是在bean生命周期初始化后进行代理操作，不被spring管理肯定不会走代理）。

5.类内方法自己调用。自己调用自己类的方法是不会走代理的。

**异常没有抛出**：

rollbackFor使用默认的，默认的只会回滚RuntimeException和Error的异常，那些什么IO异常都不会回滚。

你的异常被自己try catch捕获了。你不抛出来人家咋知道报异常了。

**其他：**

事务传播机制错了，比如你被调用的方法事务机制是REQUIRES_NEW（无论当前方法有无事务，都新建事务，如果当前有则挂起事务），会新起一个事务，两个事务没啥关联，第一个报错了第二个不回滚。

你用的数据库有问题，当你表用不支持事务的引擎时，比如说MyISAM，这时候当然事务不生效。
