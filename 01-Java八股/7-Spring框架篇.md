# Spring框架篇-面试题

**SSM（Spring+SpringMVC+MyBatis）**

![image-20250826142421533](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826142421533.png)

## **Spring框架常见的注解**

### **1.spring常见的注解有哪些？（bean的实例化、依赖注入）**

![image-20250826144500398](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826144500398.png)

### **2.springboot常见的注解有哪些？（spring的自动化配置）**

![image-20250826144621761](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826144621761.png)

### **3.springMVC常见的注解有哪些？（web框架）**

![image-20250826144709502](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826144709502.png)

### **Q：列举几个spring框架中常用注释**（考）

A:**@RequestMapping** **：**指定请求的 URL、请求方法（如 GET、POST 等）、请求参数、请求头等信息。该注解既可以用在类级别，也可以用在方法级别。

**@GetMapping、@PostMapping**……是@RequestMapping下的特定的请求方式。

举例：

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826142033765.png" alt="image-20250826142033765" style="zoom: 50%;" />

**@RequestBody：**会自动将前端请求的json中的数据转换为 Java 对象。

**@ResponseBody：**会自动将Java对象转换为前端的json数据。

**@Component** **：**注解通知Spring被此注解的类需要被纳入到Spring Bean容器中并进行管理。

**@Controller：**是 @Component 注解的一个特定形式，被该注解标注的类会被 Spring 自动扫描并配置，用于处理 HTTP 请求。通常与 @RequestMapping 注解一起使用。

**@Service：**是 @Component 注解的一个特定形式，用于标记一个类为业务逻辑层的服务类。

**@Autowired：**Spring 会自动在容器中查找匹配的 Bean 并注入

**@SpringBootApplication：**是一个组合注解，包含了 @SpringBootConfiguration、@EnableAutoConfiguration 和 @ComponentScan 三个注解。它是 Spring Boot 应用的核心注解，用于启动 Spring Boot 应用，开启自动配置和组件扫描功能。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826142324786.png" alt="image-20250826142324786" style="zoom:50%;" />

## **Spring相关**

### 1.**Spring框架中的单例bean是线程安全的吗？不是**

一般来说，默认Spring中的bean就是单例的，想要是多例需要自己加注释调整。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826142610491.png" alt="image-20250826142610491" style="zoom: 33%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826142633907.png" alt="image-20250826142633907" style="zoom:50%;" />

###  2.什么是单例模式？你还了解哪些Java的设计模式？（考）

- **传统单例**：指一个类在**整个 JVM 中只有一个实例**，由类自身控制实例创建（私有构造 + 静态方法）。
- **Spring 单例**：指一个 Bean 在**Spring 容器中只有一个实例**，容器负责管理这个实例的生命周期（创建、依赖注入、销毁等）。
  注意：Spring 单例是 “容器级” 的，不同容器可以有同一个类的不同实例；且 Spring 默认对 Bean 采用单例模式（`scope="singleton"`）。

**实际开发中使用单例模式的场景**

1. **工具类**：如日志工具类（`Logger`）、日期处理工具类（`DateUtils`），无需多实例，全局共享一个即可。
2. **配置管理类**：如读取数据库配置的`DBConfig`，全局只需要一份配置信息，避免多实例导致的配置不一致。
3. **线程池 / 连接池**：如`ThreadPoolExecutor`实例，全局唯一的池可以统一管理线程资源，避免资源浪费。
4. **Spring 中的 Service/DAO 层 Bean**：默认单例，因为这些组件无状态（不存储用户私有数据），复用实例可减少内存开销。

**其他的Java设计模式**

1. **工厂模式（Factory Pattern）**

   - **核心逻辑**：不用 `new` 直接创建对象，而是交给「工厂类」统一生产，屏蔽创建细节。

   - **极简例子**：做一个 “水果工厂”，要苹果就给苹果，要香蕉就给香蕉，不用自己手动 `new Apple()` 或 `new Banana()`。

   - **面试说场景**：比如项目里的 “支付渠道创建”—— 要微信支付就调用工厂拿微信支付实例，要支付宝就拿支付宝实例，不用在业务代码里写一堆 `new WeChatPay()`/`new Alipay()`，改起来也方便（加新支付方式只改工厂，不改业务）。

2. **观察者模式（Observer Pattern）**

   - **核心逻辑**：“一个变，一群跟着变”—— 被观察者（比如订单）状态变了，所有观察它的对象（比如短信通知、物流系统）都会自动收到消息。

   - **极简例子**：订单支付成功后，自动发短信 + 通知物流。

   - **面试说场景**：除了订单，还有比如 “配置文件更新”—— 配置中心（被观察者）改了配置，所有依赖配置的服务（观察者）自动刷新配置，不用重启服务。

3. **策略模式（Strategy Pattern）**

   - **核心逻辑**：“选方案”—— 不同策略（比如不同支付方式）单独写，业务代码里按需切换，不用写一堆 `if-else`。

   - **极简例子**：用户选 “微信支付” 就走微信逻辑，选 “支付宝” 就走支付宝逻辑。

   - **面试说场景**：比如 “导出文件”—— 选 “导出 Excel” 就用 Excel 策略，选 “导出 PDF” 就用 PDF 策略；或者 “排序”—— 数据少用冒泡排序，数据多用快速排序，业务层直接切换策略即可。



### 3.单例模式实现的2种方式？饿汉式 vs 饱汉式（考）

- 饿汉式 “急”，类加载就创建，线程安全但可能占资源；
- 饱汉式 “懒”，用时才创建，需处理线程安全（双重检查锁是标准写法）；
- 面试写代码时，饿汉式直接写静态实例，饱汉式一定要加双重检查锁和 volatile（体现细节把控）。

| 对比维度   | 饿汉式                             | 饱汉式（DCL 实现）              |
| ---------- | ---------------------------------- | ------------------------------- |
| 初始化时机 | 类加载时（静态变量初始化）         | 首次调用`getInstance()`时       |
| 线程安全   | 天然安全（类加载机制保证）         | 需要手动处理（如 DCL+volatile） |
| 资源消耗   | 可能浪费（实例提前创建，即使不用） | 更节省（按需创建）              |
| 实现复杂度 | 简单（无锁）                       | 较复杂（需处理线程安全）        |

**1. 饿汉式（立即加载）**

```java
public class SingletonHungry {
    // 类加载时就初始化实例（“饿”：迫不及待创建）
    private static final SingletonHungry instance = new SingletonHungry();
    
    // 私有构造，禁止外部实例化
    private SingletonHungry() {}
    
    // 直接返回实例
    public static SingletonHungry getInstance() {
        return instance;
    }
}
```

**2. 饱汉式（延迟加载，懒汉式）**

```java
public class SingletonLazy {
    // 延迟初始化，初始为null（“饱”：需要时才创建）
    private static volatile SingletonLazy instance; // volatile防止指令重排
    
    private SingletonLazy() {}
    
    // 双重检查锁（DCL）保证线程安全
    public static SingletonLazy getInstance() {
        if (instance == null) { // 第一次检查：避免频繁加锁
            synchronized (SingletonLazy.class) {
                if (instance == null) { // 第二次检查：防止多线程同时进入时重复创建
                    instance = new SingletonLazy();
                }
            }
        }
        return instance;
    }
}
```

### 4.什么是代理模式？静态代理VS动态代理

1. **静态代理**
   - 代理类在**编译期就已确定**（如上面的`Agent`类）。
   - 优点：简单直观，容易理解。
   - 缺点：一个真实类对应一个代理类，代码冗余（如果有 10 个明星，就要写 10 个经纪人）。
2. **动态代理**
   - 代理类在**运行时动态生成**（无需手动编写代理类）。
   - 常见实现：
     - JDK 动态代理：基于接口（要求真实类必须实现接口）。
     - CGLIB 动态代理：基于继承（可以代理没有实现接口的类）。
   - 优点：无需手动写代理类，减少代码冗余，适合批量代理。
   - 场景：
     - Spring AOP 的 “动态代理” 模式：对标注了`@Transactional`（事务）、`@Log`（日志）等注解的方法，Spring 会通过 JDK 动态代理或 CGLIB 动态代理生成代理对象，自动添加事务控制、日志记录等增强逻辑；
     - MyBatis 的 Mapper 接口代理：Mapper 接口没有实现类，MyBatis 通过动态代理在运行时生成代理对象，将接口方法映射为 SQL 执行逻辑。

### 5.Spring框架的2个核心优势：AOP与IOC（考）

IoC容器和AOP模块。通过IoC容器管理POJO对象以及他们之间的耦合关系；通过AOP以动态非侵入的方式增强服务。

 **IOC (Inversion of Control) - 控制反转 / DI (Dependency Injection) - 依赖注入**

- **核心思想：** 颠覆传统的对象创建和管理方式。传统编程中，对象自己负责创建或查找它所依赖的其他对象（即“控制权”在对象自身）。IOC则将这个控制权**反转**给了外部容器（Spring容器）。
- **实现方式：** Spring主要通过**依赖注入**来实现IOC。这意味着：
  - 对象的**依赖关系**（即它需要使用的其他对象）不是由对象内部创建，而是由Spring容器在**创建对象时**，**注入**给它（通过构造器、Setter方法或字段）。
  - 对象只需要声明它需要什么（通过接口、构造器参数、Setter方法或注解如`@Autowired`），而不关心依赖的具体实现从哪里来、如何创建。
- **核心优势：**
  - **解耦：** 这是**最根本的优势**。组件（类）不再直接依赖于具体实现，而是依赖于接口或抽象。这使得组件更容易替换、复用和独立测试。更改一个组件的实现不会强制修改依赖它的其他组件（只要接口不变）。

**AOP相关如下↓**

### **6.什么是AOP？你的项目中有没有使用到AOP？**（考）

AOP是面向切面的编程，可以理解为公用的代码。通过拦截某个注释（使用了该注释的就要运行这段公共代码）获取被增强的方法对象。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826143217658.png" alt="image-20250826143217658" style="zoom: 67%;" />

![image-20250826143250710](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826143250710.png)

![image-20250826143327296](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826143327296.png)

### **7.Spring中事务失效的场景有哪些？**

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826143945864.png" alt="image-20250826143945864" style="zoom:50%;" />

![image-20250826144003754](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826144003754.png)

![image-20250826144021492](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826144021492.png)

![image-20250826144039568](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826144039568.png)

### **8.Spring的bean的生命周期。（考）**

![image-20250826144852328](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826144852328.png)

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826144918693.png" alt="image-20250826144918693" style="zoom: 50%;" />

### **9.Spring中的循环引用你了解吗？什么是循环引用/依赖？**

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145015702.png" alt="image-20250826145015702" style="zoom: 33%;" />

**产生循环引用/依赖，会出现死循环**

![image-20250826145108751](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145108751.png)

### 10.如何解决Spring中的循环依赖问题？

![image-20250826145239923](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145239923.png)

很明显，之前出现死循环的情况就是，在只有一级缓存的情况，如果我们引入二级缓存，与一级缓存搭配使用的话，流程如下：

![image-20250826145307948](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145307948.png)

一级缓存+二级缓存可以解决一般bean对象的循环依赖问题，当bean对象是**代理对象**时，就会导致最后的一级缓存池（单例池）内是代理对象，而不是对象本身，就没有办法解决。此时需要借助三级缓存，流程如下：

![image-20250826145340216](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145340216.png)

三级缓存池是对象工厂（普通对象+代理对象），是用于创建对象的，之后从中获得了对象的ObjectFactory对象后，就会把创建好的对象放到二级缓存池，最后是走完全部生命周期的才会放入一级缓存池。

![image-20250826145406571](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145406571.png)

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145427244.png" alt="image-20250826145427244" style="zoom: 50%;" />

### **11.SpringMVC的执行流程？（考）**

SpringMVC 是 Spring 框架的一部分，专门用于构建 Web 应用程序。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145608975.png" alt="image-20250826145608975" style="zoom:67%;" />

![image-20250826145627298](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145627298.png)

![image-20250826145641803](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145641803.png)

### **12.springboot的自动配置原理（考）**

![image-20250826145808154](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145808154.png)

![image-20250826145826472](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145826472.png)

![image-20250826145841441](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145841441.png)

## MyBatis相关

### **1.MyBatis的执行流程（考）**

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145937784.png" alt="image-20250826145937784" style="zoom:50%;" />

![image-20250826145959909](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145959909.png)

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826150106955.png" alt="image-20250826150106955" style="zoom: 50%;" />

将SQL内的相关参数转换为Java对象的参数

### **2.MyBatis是否支持延迟加载？**

支持，但是默认没有开启

**什么是延迟加载？**

当用户表中有一个成员变量是订单表，那默认的情况下，查询用户信息是就会将订单信息也查出来。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826150241384.png" alt="image-20250826150241384" style="zoom:50%;" />

**延迟加载的底层原理。**

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826150309809.png" alt="image-20250826150309809" style="zoom:67%;" />

### **3.MyBatis的一级、二级缓存用过吗？**

一级缓存是同一个会话sqlsession内，信息共享，相同的信息同一个会话就不用查两遍了。

二级缓存是不同的会话sqlsession也可以信息共享，但默认是关闭的，开启后相同的信息不同的会话也不需要查两遍了

![image-20250826150523963](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826150523963.png)