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

###  2.什么是单例模式？你还了解哪些Java的设计模式？

单例 代理

### **3.什么是AOP？你的项目中有没有使用到AOP？**

AOP是面向切面的编程，可以理解为公用的代码。通过拦截某个注释（使用了该注释的就要运行这段公共代码）获取被增强的方法对象。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826143217658.png" alt="image-20250826143217658" style="zoom: 67%;" />

![image-20250826143250710](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826143250710.png)

![image-20250826143327296](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826143327296.png)

### **4.Spring中事务失效的场景有哪些？**

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826143945864.png" alt="image-20250826143945864" style="zoom:50%;" />

![image-20250826144003754](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826144003754.png)

![image-20250826144021492](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826144021492.png)

![image-20250826144039568](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826144039568.png)

### **5.Spring的bean的生命周期。（考）**

![image-20250826144852328](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826144852328.png)

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826144918693.png" alt="image-20250826144918693" style="zoom: 50%;" />

### **6.Spring中的循环引用你了解吗？什么是循环引用/依赖？**

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145015702.png" alt="image-20250826145015702" style="zoom: 33%;" />

**产生循环引用/依赖，会出现死循环**

![image-20250826145108751](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145108751.png)

### 7.如何解决Spring中的循环依赖问题？

![image-20250826145239923](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145239923.png)

很明显，之前出现死循环的情况就是，在只有一级缓存的情况，如果我们引入二级缓存，与一级缓存搭配使用的话，流程如下：

![image-20250826145307948](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145307948.png)

一级缓存+二级缓存可以解决一般bean对象的循环依赖问题，当bean对象是**代理对象**时，就会导致最后的一级缓存池（单例池）内是代理对象，而不是对象本身，就没有办法解决。此时需要借助三级缓存，流程如下：

![image-20250826145340216](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145340216.png)

三级缓存池是对象工厂（普通对象+代理对象），是用于创建对象的，之后从中获得了对象的ObjectFactory对象后，就会把创建好的对象放到二级缓存池，最后是走完全部生命周期的才会放入一级缓存池。

![image-20250826145406571](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145406571.png)

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145427244.png" alt="image-20250826145427244" style="zoom: 50%;" />

### **8.SpringMVC的执行流程？（考）**

SpringMVC 是 Spring 框架的一部分，专门用于构建 Web 应用程序。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145608975.png" alt="image-20250826145608975" style="zoom:67%;" />

![image-20250826145627298](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145627298.png)

![image-20250826145641803](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250826145641803.png)

### **9.springboot的自动配置原理（考）**

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