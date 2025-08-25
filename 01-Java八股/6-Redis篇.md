# Redis-面试题

Redis：缓存中间件

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825152336429.png" alt="image-20250825152336429" style="zoom: 67%;" />

## 本地缓存、Redis、MySQL数据库对比

| **对比维度**       | **本地缓存（如 Caffeine）**                                  | **Redis（分布式缓存）**                                      | **MySQL（关系型数据库）**                                    |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **核心定位**       | 进程内的 “一级缓存”，加速单实例内的超高频访问                | 分布式环境的 “二级缓存”，解决跨实例数据共享与中高频访问加速  | 数据的 “最终存储源”，保证持久化、一致性和复杂查询支持        |
| **存储介质**       | 应用进程的内存（与应用同进程）                               | 独立服务器的内存（主要）+ 磁盘（持久化备份）                 | 磁盘（主要）+ 内存（缓存加速，如 InnoDB Buffer Pool）        |
| **访问速度**       | 极快（微秒级，甚至纳秒级），无网络开销                       | 快（毫秒级），依赖网络通信（TCP/IP）                         | 较慢（毫秒到秒级），依赖磁盘 IO 和复杂计算                   |
| **数据一致性**     | 弱一致性（仅单实例有效，多实例间无法同步）                   | 最终一致性（通过持久化和同步机制保证，支持部分强一致性）     | 强一致性（支持 ACID 事务，多版本并发控制）                   |
| **数据持久化**     | 无（进程重启后数据丢失）                                     | 支持（RDB 快照 / AOF 日志，可恢复数据）                      | 强支持（数据持久化到磁盘，事务日志保证崩溃恢复）             |
| **分布式支持**     | 不支持（仅当前应用实例可见，多实例数据独立）                 | 支持（跨进程、跨服务器、跨机房共享数据）                     | 支持（通过主从、集群实现分布式部署，核心是数据存储）         |
| **数据规模**       | 受限（依赖应用进程内存，通常较小，MB 到 GB 级）              | 较大（独立内存空间，可扩容至 GB 到 TB 级）                   | 极大（依赖磁盘容量，可支持 TB 到 PB 级）                     |
| **数据结构支持**   | 简单（键值对为主，部分支持过期、淘汰策略）                   | 丰富（字符串、哈希、列表、集合、Sorted Set 等）              | 结构化（表、行、列，支持关联关系）                           |
| **过期与淘汰策略** | 支持（如 LRU、LFU 等，Caffeine 的 W-TinyLFU 策略最优）       | 支持（多种淘汰策略，如 volatile-lru、allkeys-lru 等）        | 不支持（数据需手动删除或通过 SQL 条件筛选）                  |
| **典型适用场景**   | 1. 超高频访问的静态数据（如系统配置、字典表） 2. 单实例内的临时计算结果 3. 本地私有数据（如验证码） | 1. 分布式共享数据（如用户会话、购物车） 2. 热点数据缓存（如商品详情） 3. 计数器、限流、排行榜等功能 | 1. 核心业务数据存储（如订单、用户信息） 2. 复杂关联查询（如多表 join） 3. 事务性操作（如支付、库存扣减） |
| **主要优势**       | 延迟极低，无网络开销，减轻下游存储压力                       | 分布式共享能力，性能均衡，支持丰富功能                       | 数据持久化可靠，支持事务和复杂查询，容量无上限               |
| **主要劣势**       | 不支持分布式共享，内存受限，多实例数据不一致                 | 依赖网络，性能略低于本地缓存，内存成本较高                   | 磁盘 IO 慢，高并发下易成为瓶颈                               |

## Redis使用场景

我看你的项目中都用到了redis，你在最近的项目中哪些场景使用了redis呢？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825153543428.png" alt="image-20250825153543428" style="zoom:67%;" />

→关于缓存：

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825155557244.png" alt="image-20250825155557244" style="zoom: 50%;" />

### 1.如果发生了缓存穿透，该如何解决？

**缓存穿透：**查询一个不存在的数据，MySQL查询不到数据也不会直接写入缓存，就会导致每次请求都查询数据库，使数据库的压力过大，而造成宕机。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825155900697.png" alt="image-20250825155900697" style="zoom:67%;" />

解决方案：

a）缓存空数据

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825155927818.png" alt="image-20250825155927818" style="zoom:67%;" />

b）布隆过滤器

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825160304938.png" alt="image-20250825160304938" style="zoom:67%;" />

### 2.缓存击穿的定义与解决方案？

**缓存击穿：（某一个热点key过期）**给某一个key设置了过期时间，当key过期时，在过期的某个时间点，恰好对这个key有大量并发的请求，这些并发的请求可能会瞬间把数据库压垮。

解决方案：

a）互斥锁：强一致性，但性能差

b）逻辑过期：高可用，性能优，不能保证数据的绝对一致性。

![image-20250825160539720](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825160539720.png)

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825160550293.png" alt="image-20250825160550293" style="zoom: 50%;" />

### 3.缓存雪崩的含义？怎么解决？

**缓存雪崩：**指在同一时间下大量的key同时失效或者redis服务宕机，导致大量请求到达数据库，带来巨大压力。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825164031468.png" alt="image-20250825164031468" style="zoom: 50%;" />

解决方案：给不同key的过期时间添加随机值

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825164119658.png" alt="image-20250825164119658" style="zoom: 33%;" />

### 4.MySQL的数据如何与redis进行同步呢？（双写一致性）

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825165215716.png" alt="image-20250825165215716" style="zoom: 33%;" />

**什么是双写一致性？**即缓存和数据库的数据要保持一致。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825165252659.png" alt="image-20250825165252659" style="zoom: 50%;" />

**为什么要延迟双删呢？**

a）双删的原因：无论先删除缓存or先修改数据库，都会造成脏数据的可能。

b）延迟的原因：数据库一般为主从数据库，延时一段时间让主从数据库进行同步。

c）延时的时间不好控制，延迟双删也有脏数据的风险。

 

#### A. 当一致性要求高时（如抢券库存）：Redisson 读写锁方案

 抢券库存场景的核心痛点是**并发更新剧烈**（大量用户同时抢券），且**不允许超卖或库存不一致**（例如库存为 100，最终被抢数量不能超过 100）。此时需要通过**分布式锁**严格控制并发，Redisson 的读写锁（`RReadWriteLock`）是典型解决方案。

**方案原理**

Redisson 读写锁是分布式环境下的可重入锁，遵循 “**读共享、写排他**” 原则：

- 读锁（`RLock`）：多个线程可同时获取，适用于查询操作；
- 写锁（`RLock`）：仅允许一个线程获取，获取时会阻塞所有读锁和其他写锁，适用于更新操作。

通过读写锁可确保 “更新 MySQL 和 Redis” 的操作是原子性的，避免并发场景下的 “脏写” 或 “脏读”。

实现步骤（以 “扣减优惠券库存” 为例）

1. **定义共享锁标识**：用业务唯一键作为锁标识（如`coupon:stock:{couponId}`）。
2. **查询库存时加读锁**：确保查询过程中不会被写操作干扰，获取到的库存是 “当前最新且未被修改” 的。
3. **扣减库存时加写锁**：确保同一时间只有一个线程执行 “扣减 MySQL 库存 + 更新 Redis 库存” 的操作，避免并发冲突。



#### B. 当允许一定延时（如文章热点数据）：MQ 异步通知方案

文章热点数据（如标题、阅读量）的特点是**读多写少**，且更新后允许短暂不一致（例如阅读量延迟 1-2 秒同步到 Redis）。此时无需阻塞主流程，可通过 MQ（如 RabbitMQ、Kafka）异步同步数据，兼顾性能与最终一致性。

**方案原理**

核心是 “**MySQL 更新后，通过 MQ 发送消息通知 Redis 异步更新**”，流程上解耦 “数据更新” 与 “缓存同步”，避免主流程因缓存操作阻塞。最终通过消息消费保证 Redis 与 MySQL 一致（延时通常在毫秒到秒级）。

**实现步骤（以 “文章阅读量 + 1” 为例）**

1. **更新 MySQL 数据**：先确保 MySQL 数据更新成功（如阅读量 + 1）。
2. **发送 MQ 消息**：向 MQ 发送一条 “数据更新” 消息（包含文章 ID、更新字段等）。
3. **消费消息同步 Redis**：MQ 消费者接收到消息后，从 MySQL 读取最新数据，更新到 Redis。





### 5.redis作为缓存，数据的持久化是怎么做的呢？

在redis中提供了**两种数据持久化**的方式：1.**RDB** 2. **AOF** 

**（RDB恢复的更快，是二进制文件；AOF恢复的慢，但数据完整度更高）**

为redis持久化，redis宕机后可恢复。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825165857256.png" alt="image-20250825165857256" style="zoom: 33%;" />

### 6.redis中数据的过期策略？redis的key过期后，会立即删除吗？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825171044847.png" alt="image-20250825171044847" style="zoom: 50%;" />

redis中有两种数据过期策略：但一般是**两种策略（惰性删除** **+** **定期删除）配合使用！！！**

**a）惰性删除**

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825171146446.png" alt="image-20250825171146446" style="zoom: 33%;" />

**b）定期删除**

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825171315379.png" alt="image-20250825171315379" style="zoom: 33%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825171331629.png" alt="image-20250825171331629" style="zoom:33%;" />

### 7.redis中数据的淘汰策略？假如缓存过多，内存是有限的，内存被占满了怎么办？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825171942368.png" alt="image-20250825171942368" style="zoom: 33%;" />

LRU算法：对于内存中使用次数最少、存放时间最长的进行删除



### 8.redis中的分布式锁：

synchronized互斥锁具有的问题：只能在本地使用，不能用于服务器集群情况下→ 解决方案：分布式锁

redis分布式锁需要设置超时时间：如果获取锁成功后出现服务器宕机情况，此时锁就没有办法释放，其他业务无法使用

通常情况下，分布式锁的使用场景：集群情况下的定时任务、抢单、幂等性场景。

#### **a）synchronized互斥锁与分布式锁的区别？**

互斥锁：synchronized（适用于单体架构，只有一个服务器）

但很多情况下，服务器是集群部署的。synchronized只能解决某服务器内的互斥，无法解决多个服务器之间的冲突与互斥。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825172346005.png" alt="image-20250825172346005" style="zoom:67%;" />

所以需要引入分布式锁。

![image-20250825172410480](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825172410480.png)

#### **b）redission实现的分布式锁——setnx锁+lua脚本**

![image-20250825172526913](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825172526913.png)

setnx锁在使用时，不好控制锁的有效时间，手动给锁续期的方案也比较麻烦，所以redission锁内部在其基础上进行了改进，可以自动的给锁续期。

**redission锁执行流程：**

![image-20250825172651580](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825172651580.png)

在一个线程获取锁的时候，另一个线程加锁不成功，先不断进行尝试加锁，可以设定这个循环的次数，一旦达到这个次数，就不再尝试加锁，直接加锁失败。

但一般情况下一个线程任务都是很快结束的。

**！重点！：**1.redission中有看门狗，为锁续期。一个线程获取锁成功以后，WatchDog会给持有锁的线程续期，默认是每隔10秒续期一次。

​                   2.没有获得锁的线程会不断尝试加锁等待

​                   3.加锁、设置过期时间等操作都是基于lua脚本完成，保证执行的完整性。

**→追问：redission实现的分布式锁是否可以实现重入呢？**

可重入。同一个线程可以多次获取同一个锁

![image-20250825173204545](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825173204545.png)

**→追问：如果我的项目采用了redis集群，redission分布式锁会出现什么问题？**

可能会出现**主从一致性**的问题，这时候可以考虑使用**RedLock红锁**。（性能低，如果需要保证强一致性，可以用zookeeper）



#### c）主从切换导致的锁丢失问题——红锁RedLock/zookeeper

**一、先理解主从架构下的分布式锁问题**

在 Redis 主从集群中，使用普通分布式锁（如 Redisson 的单节点锁）会存在 **“主从切换导致的锁丢失”** 问题，流程如下：

1. 客户端 A 在主节点上成功获取锁（写入锁信息）；
2. 主节点还未将锁信息同步到从节点时，主节点宕机；
3. 哨兵或集群自动将从节点升级为新主节点（新主节点中没有客户端 A 的锁信息）；
4. 客户端 B 在新主节点上成功获取到相同的锁，导致 “锁冲突”（两个客户端同时持有同一把锁）。

这就是主从一致性问题的核心：**锁信息在主从同步前丢失，导致新主节点无法识别已有锁**。

**二、RedLock（红锁）是什么？**

RedLock 是一种基于**多个独立 Redis 实例**（非主从关系）的分布式锁方案，核心思想是：
**“只有在超过半数的独立 Redis 节点上成功获取锁，且总耗时不超过锁的有效时间，才认为锁获取成功”**。

它要求部署**5 个独立的 Redis 节点**（推荐奇数个，如 5 个），这些节点之间没有主从复制关系，彼此完全独立（避免单点故障或同步问题）。



### 9.Redis是单线程的，但是为什么还那么快？

![image-20250825173438379](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825173438379.png)

![image-20250825173731830](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825173731830.png)

**→追问：能解释一下I/O多路复用模型吗？I/O多路复用模型是怎么实现高效的网络请求的？**

I/O 多路复用模型是解决**高并发网络 I/O 问题**的核心技术（如 Web 服务器、Redis 等中间件都依赖它），其核心思想是：**让一个进程 / 线程可以同时监听多个 I/O 事件（如网络连接的读写），当某个 I/O 事件就绪时（如数据到达），再通知应用程序处理**。这种模型避免了传统阻塞 I/O 的低效问题，能高效处理数万甚至数十万并发连接。

## Redis集群

### Redis集群有哪些方案？（三种：主从复制、哨兵模式、分片集群 ）

1.**主从复制**：

单节点Redis的并发能力是有上限的，要进一步提高Redis的并发能力，就需要搭建主从集群，实现**读写分离**。

- **架构**：由 1 个主节点（Master）和多个从节点（Slave）组成。
  - 主节点：负责处理所有写操作（SET、DEL 等），并将数据同步给从节点；
  - 从节点：仅处理读操作（GET 等），数据完全从主节点复制而来，不接受写操作。
- **数据同步机制**：
  - 首次连接（全量复制）：从节点启动时，会向主节点发送 SYNC 命令，主节点生成 RDB 快照并发送给从节点，从节点加载快照后，主节点再将期间的增量命令（写操作）同步给从节点，保证数据一致。
  - 增量复制：后续主节点的写操作会通过 “复制缓冲区” 实时同步给从节点（基于偏移量，避免重复传输）。

![image-20250825173903763](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825173903763.png)

| **对比维度**     | **主从复制**                     | **哨兵模式**                                  | **分片集群（Redis Cluster）**                  |
| ---------------- | -------------------------------- | --------------------------------------------- | ---------------------------------------------- |
| **核心目标**     | 数据备份 + 读负载均衡            | 主从基础上实现自动故障转移（高可用）          | 数据分片 + 分布式高可用（大规模扩展）          |
| **节点角色**     | 1 主多从（从节点只读）           | 1 主多从 + 多个哨兵（哨兵负责监控和故障转移） | 多主多从（每个主节点分管部分数据，从节点备份） |
| **写操作支持**   | 仅主节点处理（单主瓶颈）         | 仅主节点处理（单主瓶颈）                      | 多个主节点并行处理（无单主瓶颈）               |
| **数据存储范围** | 所有节点存储全量数据             | 所有节点存储全量数据                          | 数据分片存储（每个主节点存储部分数据）         |
| **自动故障转移** | 不支持（需手动切换）             | 支持（哨兵自动选举新主）                      | 支持（集群内置逻辑，从节点自动升级）           |
| **最大数据量**   | 受单节点内存限制（GB 级）        | 受单节点内存限制（GB 级）                     | 支持大规模扩展（TB 级，取决于主节点数量）      |
| **适用场景**     | 小规模应用、读多写少、需简单备份 | 中小规模应用、需高可用（避免人工干预）        | 大规模应用、高并发、数据量大（如电商、社交）   |
| **复杂度**       | 低（部署简单）                   | 中（需配置哨兵集群）                          | 高（需管理槽分配、节点扩容等）                 |

## Redis中的数据类型（重点）

### **1.Redis中的五种数据类型：String、list、Hash、set、zset**

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825174435550.png" alt="image-20250825174435550" style="zoom:67%;" />

### 2.Zset的底层实现原理

zset（排序+去重）的底层原理是压缩列表+跳表

![image-20250825174528844](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825174528844.png)

压缩表的本质就是列表，只是在找首尾元素的时候更为容易，但是找其他元素的时候，仍需要遍历。

什么时候用压缩表，什么时候用跳表？

1.有序集合保存的元素数量小于128个

2.有序集合保存的所有元素的长度小于64字节

 

跳表：增加了多级索引，通过多级索引位置的转跳，实现了快速查找元素。

其增删改查的复杂度都是O（logn）



### **3.Zset为什么使用跳表而不是二叉树or红黑树？**

1.跳表更适合范围查找，跳表效率比红黑树高。

2.跳表实现比红黑树简单。

 

### **4.为什么redis使用跳表而mysql使用B+树？**

redis是内存，mysql是外存，要尽量减少磁盘io，b+tree四层，可以存储256亿行数据，但是只需要进行四次IO就可以查到。跳表就不止四次，从磁盘获取数据可比从内存获取数据难多了，所以需要尽可能的减少磁盘IO。而跳跃表要比b+tree实现简单，redis是基于内存操作，没有磁盘IO，所以选择更加容易实现的跳跃表。

 

### **5.如何利用zset解决排行榜问题**

zset （排序+去重）是 Redis 常用数据类型之一，它适用于排行榜类型的业务场景，比如 QQ 音乐排行榜、用户贡献榜等。在音乐排行榜单中，我们可以将歌曲的点击次数作为 score 值，把歌曲的名字作为 value 值，通过对 score 排序就可以得出歌曲“热度榜单”。

**获取top10的悬赏排行，redis场景题**

 排行榜意味着一个用户一个信息---去重；去重+有序：zset

**Zset中可以存储不重复的元素集合，并为每个元素关联一个浮点数分数（score)，Redis会根据这个分数自动对集合中的元素进行排序。**

具体方法：zadd user:ranking(集合名称) 100（积分作为分数） user1（用户id作为元素）

Zrevrange user:ranking 0 2 withscores(从大到小取出三个）

想要实现积分相同时按最后更新时间升序：score = 积分 + （1 - 时间戳/10的13次方）（把时间戳作为小数传入了score中） 当降序时即直接加时间戳/10的13次方即可。

代码实现：

