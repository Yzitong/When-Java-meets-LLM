# JVM（Java虚拟机）

## 基础知识：

### 1.JVM的位置

JVM 并非独立的 “安装程序”，而是依托于 JRE/JDK 的 “运行环境组件”。

JVM（Java 虚拟机）是 Java 程序的 “执行引擎”，其运行依赖于操作系统和 JRE（Java 运行时环境），层级关系如下：
**操作系统（OS）→ JRE/JDK → JVM → Java 字节码（.class 文件）→ Java 程序运行**

- 它是 “介于 JRE 和 Java 程序之间” 的虚拟环境：JRE 提供了 JVM 运行所需的核心类库（如 rt.jar）和配置，JVM 则负责将字节码翻译成操作系统能识别的机器码并执行。

### 2.JVM的体系结构

- **jvm调优：99%都是在方法区和堆，大部分时间调堆。** JNI（java native interface）本地方法接口。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250820171259449.png" alt="image-20250820171259449" style="zoom:67%;" />

![image-20250820171755255](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250820171755255.png)

### 3.native关键字

- **凡是带了native关键字的，说明 java的作用范围达不到，去调用底层C语言的库！**
- **依赖 JNI：Java Native Interface（Java本地方法接口）**，融合不同的编程语言为 Java 所用！ 最初：C、C++。
- 凡是带了native关键字的方法就会进入本地方法栈；
- 本地接口的作用是融合不同的编程语言为Java所用，它的初衷是融合C/C++程序，Java在诞生的时候是C/C++横行的时候，想要立足，必须有调用C、C++的程序，于是就在内存中专门开辟了一块区域处理标记为native的代码，它的具体做法是 在 Native Method Stack 中登记native方法，在 ( ExecutionEngine ) 执行引擎执行的时候加载Native Libraies。
- 目前该方法使用的越来越少了，除非是与硬件有关的应用，比如通过Java程序驱动打印机或者Java系统管理生产设备，在企业级应用中已经比较少见。因为现在的异构领域间通信很发达，比如可以使用Socket通信，也可以使用Web Service等等，不多做介绍！

## 类加载器：

- 作用：加载Class文件——如果new Car();（具体实例在堆里，引用变量名放栈里） 。
- 先来看看一个类加载到 JVM 的一个基本结构：

![image-20250820174912561](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250820174912561.png)

- 类是模板，对象是具体的，通过new来实例化对象。car1，car2，car3，名字在栈里面，真正的实例，具体的数据在堆里面，栈只是引用地址。
- 类加载器一级一级向上有多层类加载器：

1. 启动类（根）加载器（三层）Bootstrap ClassLoader
2. 扩展类加载器（二层）Extention ClassLoader
3. 应用程序加载器（一层） Application ClassLoader
4. 自定义类加载器  User ClassLoader

### 1.双亲委派机制

- 概念：当某个类加载器需要加载某个.class文件时，它首先把这个任务委托给他的上级类加载器，递归这个操作，如果上级的类加载器没有加载，自己才会去加载这个类。

1.类加载器收到类加载的请求    

2.将这个请求向上委托给父类加载器去完成，一直向上委托APP-->EXC-->BOOT，直到启动类加载    

3.启动加载器检查是否能够加载当前这个类，能加载就结束，使用当前的加载器，否则，抛出异常，适知子加载器进行加载    

4.重复步骤3

- 例子：当一个Hello.class这样的文件要被加载时。不考虑我们自定义类加载器，首先会在AppClassLoader中检查是否加载过，如果有那就无需再加载了。如果没有，那么会拿到父加载器，然后调用父加载器的loadClass方法。父类中同理也会先检查自己是否已经加载过，如果没有再往上。注意这个类似递归的过程，直到到达Bootstrap classLoader之前，都是在检查是否加载过，并不会选择自己去加载。直到BootstrapClassLoader，已经没有父加载器了，这时候开始考虑自己是否能加载了，如果自己无法加载，会下沉到子加载器去加载，一直到最底层，如果没有任何加载器能加载，就会抛出ClassNotFoundException。

![image-20250820180524268](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250820180524268.png)

作用：

1. 防止重复加载同一个.class。通过委托去向上面问一问，加载过了，就不用再加载一遍。保证数据安全。
2. 保证核心.class不能被篡改。通过委托方式，不会去篡改核心.class，即使篡改也不会去加载，即使加载也不会是同一个.class对象了。不同的加载器加载同一个.class也不是同一个Class对象。这样保证了Class执行安全。