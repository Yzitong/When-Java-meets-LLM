# Java基础-并发/多线程

## **线程的基础知识：**

###  1.线程和进程的区别？

线程是一套指令流，进程是正在运行程序的实例，一个进程里可能包含多个线程。

![image-20250814184647823](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250814184647823.png)

### 2.并行和并发有什么区别？

单核CPU下多线程只能串行执行，一般将这种多线程轮流使用CPU的做法称为并发。

多核CPU下多线程是可以并行的。

总结：并发是同一时间应对多件事情的能力，多线程轮流使用一个或多个CPU。

 并行是同一时间动手做多件事情的能力，多个CPU同时执行多个线程。

![image-20250816213936973](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250816213936973.png)

### 3.创建线程的方式有哪些？（高频）

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250816214004881.png" alt="image-20250816214004881" style="zoom: 50%;" />

### →追问：runnable接口和callable接口都能创建线程，它们使用上的区别是什么？

a）Runnable接口的run方法没有返回值，Callable接口的call方法有返回值，是个泛型。

b）Runnable直接start方法运行线程即可，Callable需要和Future、FutureTask配合来获取异步执行的结果。

c）Runnable接口的run方法不能直接抛出异常，Callable接口的call方法允许直接抛出异常。

 

### →追问：在启动线程时run方法和start方法的区别？

start方法：用来启动线程的方法，一个线程只能被调用一次。调用后会自动执行run方法中定义的逻辑代码。

run方法：直接调用时，就是普通的方法，一个线程对象可以调用多次。

 

### 4.线程中包含了哪些状态，状态之间是如何变化的？

包含6个状态（新建——可执行——死亡——阻塞——等待——计时等待）

![image-20250816214320648](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250816214320648.png)

### 5.新建T1、T2、T3三个线程，如何保证它们按顺序执行？

利用join()方法：用于等待某一线程，等其结束后再执行

### 6.notify（）和notifyAll（）有什么区别？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250816221245562.png" alt="image-20250816221245562" style="zoom: 50%;" />

