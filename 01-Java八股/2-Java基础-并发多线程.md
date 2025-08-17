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

```java
public class OrderedThreads {
    public static void main(String[] args) {
        // 创建三个线程，分别执行不同任务
        Thread t1 = new Thread(() -> {
            System.out.println("T1 开始执行");
            try {
                // 模拟耗时操作
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("T1 执行完毕\n");
        }, "T1");

        Thread t2 = new Thread(() -> {
            System.out.println("T2 开始执行");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("T2 执行完毕\n");
        }, "T2");

        Thread t3 = new Thread(() -> {
            System.out.println("T3 开始执行");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("T3 执行完毕\n");
        }, "T3");

        try {
            // 启动T1并等待其完成
            t1.start();
            t1.join();  // 主线程等待T1执行完毕

            // T1完成后启动T2并等待其完成
            t2.start();
            t2.join();  // 主线程等待T2执行完毕

            // T2完成后启动T3并等待其完成
            t3.start();
            t3.join();  // 主线程等待T3执行完毕
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("所有线程执行结束");
    }
}
    
```



### 6.notify（）和notifyAll（）有什么区别？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250816221245562.png" alt="image-20250816221245562" style="zoom: 50%;" />

### 7.在JAVA中wait和sleep方法的不同？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817120507864.png" alt="image-20250817120507864" style="zoom:67%;" />

**核心区别：对 “锁” 的处理不同**

- `wait()`：**会释放当前对象的锁**。
  调用`wait()`的前提是线程已获取该对象的锁（即必须在`synchronized`同步块 / 同步方法中使用），调用后线程会释放锁并进入等待状态，其他线程可以争夺该对象的锁。
- `sleep()`：**不会释放任何锁**。
  线程调用`sleep()`后，只是暂时停止执行（进入阻塞状态），但仍持有已获取的所有锁，其他线程无法获取这些锁。

| 区别     | `wait()`                   | `sleep()`                               |
| -------- | -------------------------- | --------------------------------------- |
| 所属类   | `Object`（实例方法）       | `Thread`（静态方法）                    |
| 锁处理   | 释放对象锁                 | 不释放任何锁                            |
| 唤醒方式 | 需`notify()`/`notifyAll()` | 超时后自动唤醒or被`interrupt()`方法打断 |
| 使用前提 | 必须在`synchronized`中     | 无特殊前提                              |
| 主要用途 | 线程间通信                 | 暂停指定时间                            |

### 8.如何停止一个正在运行的线程? 

一个正在运行的线程正常是需要run方法运行结束后自然的结束线程，如果想要停止，可以用flag变量标志或其他方法标志一下去退出run方法。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817121454480.png" alt="image-20250817121454480" style="zoom: 50%;" />

## **线程中的并发安全：**

### 1.synchronized关键字的底层原理

a） synchronized存在的意义：synchronized（对象锁）采用互斥的方式让同一时刻至多只有一个线程能持有对象锁，其他线程再想获取这个对象锁就会阻塞住，没办法执行锁内的代码。（避免超卖超买的情况）

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817121622609.png" alt="image-20250817121622609" style="zoom:50%;" />

b）synchronized的**底层**就是利用了Monitor（jvm级别的对象），让需要的对象锁关联monitor。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817122125566.png" alt="image-20250817122125566" style="zoom:67%;" />

### →追问：monitor实现的锁属于重量级锁，你了解过锁升级吗？

monitor是jvm级别的对象，也就是内核态的东西，在用monitor的过程中就会涉及用户态（JAVA代码）和内核态的切换，成本较高，性能较差，也就是重量级锁。

Jdk1.6后引入了偏向锁和轻量级锁（可用于没有多线程代码竞争的情况）

一旦锁发生了竞争，就会升级为重量级锁。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817122409304.png" alt="image-20250817122409304" style="zoom: 33%;" />



### 2.请你谈一谈JMM (java内存模型) Java memory model

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817122649812.png" alt="image-20250817122649812" style="zoom: 50%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817122710604.png" alt="image-20250817122710604" style="zoom:50%;" />

### 3.CAS 乐观锁你知道吗?

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817171901818.png" alt="image-20250817171901818" style="zoom:67%;" />

**原子性（Atomicity）** ：**一个操作或一系列操作要么 “全部执行完成”，要么 “完全不执行”**，中间不会被任何其他操作打断，也不会出现 “执行一半” 的中间状态。

CAS 操作包含三个核心参数：

- **内存地址 V**：需要操作的变量在内存中的地址；
- **预期值 A**：线程认为变量当前应该有的值（操作前的旧值）；
- **新值 B**：如果变量当前值等于预期值 A，线程希望将其更新为 B。

操作逻辑是：
线程读取内存地址 V 中的值，与预期值 A 比较。如果相等，说明没有其他线程修改过该值，就将 V 中的值更新为 B；如果不相等，说明该值已被其他线程修改，当前线程不做任何操作（或重试）。整个过程是**原子性**的（由 CPU 硬件指令直接支持，如 x86 的 `cmpxchg` 指令），无需加锁。



### →追问：乐观锁与悲观锁的区别？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817172505001.png" alt="image-20250817172505001" style="zoom: 50%;" />

### 4.请谈谈对volatile关键字的理解。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817172527853.png" alt="image-20250817172527853" style="zoom: 50%;" />

### 5.什么是AQS？

全称是Abstract Queued Synchronizer 即抽象队列同步器。它是构建锁或者其他同步组件的基础框架。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817173232994.png" alt="image-20250817173232994" style="zoom:33%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817173247734.png" alt="image-20250817173247734" style="zoom: 80%;" />

### 6.ReentrantLock（可重入锁）的实现原理

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817183051973.png" alt="image-20250817183051973" style="zoom: 50%;" />

“可重入” 指线程可以多次获取同一把锁，且不会因重复获取而死锁。其核心通过 AQS 的`state`变量和 “持有线程” 标记实现：

ReentrantLock 和 synchronized 不一样，需要手动释放锁，所以使用 ReentrantLock的时候一定要手动释放锁，并且**加锁次数和释放次数要一样**

1. **状态变量（state）的含义**：`state`表示锁的 “重入次数”：
   - `state = 0`：锁未被任何线程持有；
   - `state > 0`：锁被某线程持有，`state`的值就是该线程的重入次数。
2. **获取锁（lock ()）的逻辑**：
   当线程尝试获取锁时：
   - 若`state = 0`（锁未被持有）：当前线程通过 CAS 将`state`设为 1，并记录 “持有锁的线程” 为自己（AQS 的`exclusiveOwnerThread`变量），成功获取锁。
   - 若`state > 0`（锁已被持有）：检查 “持有锁的线程” 是否为当前线程（通过`exclusiveOwnerThread`判断）。若是，则将`state`加 1（重入次数 + 1），成功获取锁；若不是，则当前线程进入等待队列阻塞。
3. **释放锁（unlock ()）的逻辑**：
   当线程释放锁时：
   - 将`state`减 1（重入次数 - 1）；
   - 若`state`减为 0：表示线程完全释放锁，将 “持有锁的线程”（`exclusiveOwnerThread`）置为`null`，并唤醒等待队列中的线程竞争锁；
   - 若`state > 0`：表示线程仍持有锁（还有未释放的重入次数），不唤醒其他线程。



### 7.公平锁与非公平锁的实现区别

`ReentrantLock`支持两种获取锁的模式，核心区别在于 **“是否按线程等待顺序分配锁”**：

**1. 非公平锁（默认，性能更优）**

线程获取锁时，不遵守 “先到先得”，可能直接插队获取锁：

- 逻辑：线程尝试获取锁时，先直接通过 CAS 抢占（无论等待队列中是否有线程）。若抢占失败，再进入等待队列排队。
- 优点：减少线程切换开销（若刚释放锁的线程再次获取锁，可直接抢占，无需唤醒队列线程）。
- 缺点：可能导致某些线程长期饥饿（一直抢不到锁）。

**2. 公平锁**

线程获取锁时，严格按 “等待队列的顺序” 分配（先到先得）：

- 逻辑：线程尝试获取锁时，先检查等待队列中是否有更早等待的线程。若有，则当前线程直接进入队列排队；若没有，再尝试 CAS 获取锁。
- 优点：保证线程获取锁的公平性，避免饥饿。
- 缺点：频繁的线程切换可能导致性能下降。



### 8.死锁产生的条件是什么？

当一个线程需要同时拥有多个锁的时候，就可能出现死锁。

→如何进行死锁的判断？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817184213778.png" alt="image-20250817184213778" style="zoom: 80%;" />

### 9.聊一下ConcurrentHashMap.(高频)

是线性安全的HashMap。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817184341005.png" alt="image-20250817184341005" style="zoom:80%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817184356084.png" alt="image-20250817184356084" style="zoom:80%;" />

### 10.Java程序中怎么保证多线程的执行安全？（导致并发程序出现问题的根本原因是什么？）

维持并发程序执行安全的三大特性：原子性、内存可见性、有序性。

并发程序出现问题的根本原因就是这些特性被破坏。

我们避免的方法：

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817184506013.png" alt="image-20250817184506013" style="zoom: 67%;" />

## **线程池：**

### 1.说一下线程池的核心参数？（线程池的底层原理？）（高频）

核心参数：

a）核心线程数

b）最大线程数，最大线程 = 核心线程 + 救急线程

c）救急时间

d）阻塞队列

e）线程工厂：用于给线程命名

f）拒绝策略：4种拒绝策略（常考）

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817185238293.png" alt="image-20250817185238293" style="zoom: 67%;" />

### 2.线程池中有哪些常见阻塞队列？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817185455480.png" alt="image-20250817185455480" style="zoom: 50%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817185604351.png" alt="image-20250817185604351" style="zoom: 50%;" />

### 3.如何确定核心线程数？

n为CPU的核数，IO密集型一个线程占据CPU的时间短，所以可以多设计几个线程。

CPU密集型，一个线程占用的时间长，要减少线程间切换。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817185805947.png" alt="image-20250817185805947" style="zoom: 50%;" />

### 4.线程池的种类有哪些？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817185847786.png" alt="image-20250817185847786" style="zoom:50%;" />

### 5.为什么不建议用Executors创建线程池？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817185910230.png" alt="image-20250817185910230" style="zoom:50%;" />

ThreadPoolExecutor的创建方法，需要7个参数，不会造成OOM（内存溢出）；而Executors下的创建线程返回的对象可能会造成OOM

```java
import java.util.concurrent.*;

public class ThreadPoolExample {
    public static void main(String[] args) {
        // 1. 定义线程池的核心参数
        int corePoolSize = 2;         // 核心线程数
        int maximumPoolSize = 4;      // 最大线程数
        long keepAliveTime = 60;      // 非核心线程空闲存活时间
        TimeUnit unit = TimeUnit.SECONDS; // 时间单位
        BlockingQueue<Runnable> workQueue = new LinkedBlockingQueue<>(2); // 任务队列
        ThreadFactory threadFactory = Executors.defaultThreadFactory();   // 线程工厂
        RejectedExecutionHandler handler = new ThreadPoolExecutor.AbortPolicy(); // 拒绝策略

        // 2. 创建线程池
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            corePoolSize,
            maximumPoolSize,
            keepAliveTime,
            unit,
            workQueue,
            threadFactory,
            handler
        );

        // 3. 向线程池提交任务
        for (int i = 0; i < 8; i++) {
            final int taskId = i;
            executor.submit(() -> {
                try {
                    // 模拟任务执行时间
                    Thread.sleep(1000);
                    System.out.println("任务 " + taskId + " 执行完毕，执行线程：" + Thread.currentThread().getName());
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
            System.out.println("提交任务 " + taskId);
        }

        // 4. 关闭线程池
        executor.shutdown();
        try {
            // 等待所有任务完成或超时
            if (!executor.awaitTermination(5, TimeUnit.SECONDS)) {
                // 超时后强制关闭
                executor.shutdownNow();
            }
        } catch (InterruptedException e) {
            executor.shutdownNow();
        }
        
        System.out.println("所有任务处理完毕，线程池已关闭");
    }
}
```

## **多线程的使用场景：**

### 1.你们的项目哪里用到了多线程？（线程池的使用场景CountDownLatch、Future）（高频）

a）使用场景一：

项目中我们需要把数据库中的数据一次性的同步到es索引库中，当时数据量过多（1000万以上）一次性读取数据会导致OOM内存溢出，所以利用线程池的方法导入，即利用CountDownLatch来控制，避免一次性加载过多，防止内存溢出。

 

CountDownLatch：倒计时锁，等待其他多个线程完成倒计时（某件事）之后才能执行。await方法用于等待计数归零，countDown方法用来让计数减一。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817190407338.png" alt="image-20250817190407338" style="zoom: 33%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817190422685.png" alt="image-20250817190422685" style="zoom: 67%;" />

### 2.谈谈你对ThreadLocal的理解。（重点）

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817190515098.png" alt="image-20250817190515098" style="zoom:50%;" />

### **→追问：ThreadLocal是否存在内存泄漏问题？存在，会OOM**

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250817190538037.png" alt="image-20250817190538037" style="zoom:50%;" />

