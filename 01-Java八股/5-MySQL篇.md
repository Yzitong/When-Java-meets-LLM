# MySQL-面试题

![image-20250824214723818](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250824214723818.png)

## MySQL优化

### 1.在MySQL中，如何定位慢查询？

一般慢查询的表象：页面加载过慢、接口响应时间过长（超过2s）

定位慢查询的方式有两种：1.采用开源的工具 2.用mysql自带的慢日志

开源的工具常用的有：调试工具Arthas、运维工具Skywalking

自带的慢日志，使用时需要配置如下两个参数。先开启慢日志的使用，还有定义慢日志的时间。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250824214813668.png" alt="image-20250824214813668" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250824214826215.png" alt="image-20250824214826215" style="zoom:67%;" />

### →追问：那这个SQL语句执行的很慢，你将如何分析/优化呢？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250824220531669.png" alt="image-20250824220531669" style="zoom: 67%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250824220550336.png" alt="image-20250824220550336" style="zoom: 67%;" />

### 2.什么是索引？如何使用？

索引（index）是帮助MySQL高效获取数据的有序数据结构。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250824221149602.png" alt="image-20250824221149602" style="zoom: 80%;" />

### →追问：索引的底层结构你了解吗？（B树与B+树）

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250824221314010.png" alt="image-20250824221314010" style="zoom:80%;" />

其底层数据结构是**B+树**。

**B树**是矮胖树，每一层可以存储多个key，其指针可以指向下一层某一区间的数。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250824221436560.png" alt="image-20250824221436560" style="zoom: 67%;" />

MySQL自带的搜索引擎就是InnoDB，就是用B+树实现的。

**B+树**所有非叶子结点只存储指针，而叶子结点才会存储数据。B+树更适合区间查询

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250824221716826.png" alt="image-20250824221716826" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250824221733445.png" alt="image-20250824221733445"  />

### 3.什么是聚簇索引（聚集索引），什么是非聚簇索引（二级索引）？

**聚簇索引（聚集索引）：**数据与索引放到一块，B+树的叶子节点保存了**整行数据**，一般索引是是主键or唯一索引。

**非聚簇索引（二级索引）：**数据与索引分开储存，B+树的叶子节点保存对应的**主键**，可以有多个。（一般普通索引都是）

聚簇索引的叶子节点直接包含完整的`(id, name, age, address,...)`数据行

二级索引的叶子节点存储`(name, id)`（索引字段 ，主键）

![image-20250824222347806](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250824222347806.png)

### →追问：什么是回表查询？

通过二级索引找到对应的主键值，到聚集索引中查找整行数据，这个过程就是回表。

![image-20250824222420734](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250824222420734.png)

### 4.什么叫覆盖索引？

即一次索引可以找出全部的所需信息，一旦需要回表的，就不是覆盖索引。

```sql
-- 查询字段仅为索引字段和主键（被二级索引覆盖）
SELECT id, name FROM user WHERE name = '李四';
```

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825104220262.png" alt="image-20250825104220262" style="zoom:67%;" />

### 5.MySQL超大分页怎么处理？

*问题阐述*：如下图，在 MySQL 中，当分页查询的`offset`非常大时（例如`LIMIT 1000000, 10`），查询性能会急剧下降，这就是 “超大分页” 问题。其核心原因是：MySQL 执行`LIMIT offset, rows`时，需要先扫描到`offset`位置的记录，再取`rows`条数据，当`offset`很大时，会扫描大量无关数据，导致 IO 和 CPU 消耗过高。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825105158820.png" alt="image-20250825105158820" style="zoom:67%;" />

*解决方案：*

**1. 基于主键 / 唯一索引的 “书签分页”（推荐）**

如果表有自增主键（如`id`）或唯一索引，可通过 “记录上一页的最后一个标识”（类似书签）来定位下一页，避免使用大`offset`。

**原理**：利用索引直接定位到 “上一页末尾”，再向后取数据，无需扫描前面的所有记录。

**示例**：
假设有一张`orders`表，`id`为自增主键，要查询第 100001-100010 条记录：

```sql
-- 传统低效写法（offset过大）
SELECT * FROM orders LIMIT 100000, 10; -- 需扫描100010条记录

-- 优化写法（基于主键书签）
-- 假设上一页最后一条记录的id是100000
SELECT * FROM orders WHERE id > 100000 LIMIT 10; -- 直接从id=100001开始取，仅扫描10条
```

**2. 覆盖索引+子查询。**

使排序中无回表，再关联索引去查询，提升效率。

```sql
-- 传统低效写法（需扫描大量数据并回表）
SELECT * FROM orders WHERE status=1 LIMIT 100000, 10;

-- 优化写法（索引覆盖+延迟关联）
-- 1. 先通过索引获取符合条件的主键（仅扫描索引，效率高）
-- 2. 再用主键关联原表取完整数据
SELECT o.* 
FROM orders o
INNER JOIN (
  SELECT id 
  FROM orders 
  WHERE status=1 
  LIMIT 100000, 10  -- 这里的LIMIT仅扫描索引，速度更快
) AS t ON o.id = t.id;
```

**覆盖索引+子查询所具有的问题：**使用了覆盖索引+子查询的方式批量查询数据库，但是覆盖索引也是全量查询，如果我的数据会快速累积，分批处理的过程中总数据量从5w变为6w，怎么办呢？

当前代码使用覆盖索引先获取所有ID，然后分批查询详细信息，这种方式在数据快速增长的情况下确实存在问题。如果在处理过程中数据从5万增长到6万，可能会导致：

- 内存压力增大（一次性加载所有ID到内存）
- 数据不一致（处理过程中新增的数据可能被遗漏）
- 查询效率降低（随着数据量增长，全表扫描代价越来越高）

**优化方案：基于ID范围的分页查询**

使用 where id > last_id limit batch_size 的方式进行分页查询，这种方式有以下优点：

- 不需要一次性获取所有ID
- 可以处理数据增长的情况
- 利用主键索引高效查询
- 避免传统OFFSET分页在大数据量时的性能问题

### 6.索引创建原则有哪些？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825111409464.png" alt="image-20250825111409464" style="zoom:67%;" />

尽量使用联合索引，是为了更多的使用覆盖索引，避免回表。

### 7.什么情况下索引会失效？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825112324978.png" alt="image-20250825112324978" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825112336326.png" alt="image-20250825112336326" style="zoom:67%;" />

### 8.谈一谈你对sql的优化经验

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825112445403.png" alt="image-20250825112445403" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825112540073.png" alt="image-20250825112540073" style="zoom:67%;" />

## **事务相关**

### 1.什么是事务？事务的特性？

事务是一组操作的集合，它是不可分割的工作单位，事务会把所有的操作作为一个整体向系统提交或撤销操作请求，这些操作要么同时成功，要么同时失败。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825112833436.png" alt="image-20250825112833436" style="zoom:67%;" />

### 2.并发事务可能会产生什么问题？如何解决这些问题？MySQL的默认隔离级别是？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825113302665.png" alt="image-20250825113302665" style="zoom:67%;" />

**解决方案：对事务进行隔离。**

**Mysql中有四种隔离级别：**

如下表，关于不同的隔离级别下是否还存在三个问题。当事务的隔离级别 越高，数据越安全，但是性能越低。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825113422353.png" alt="image-20250825113422353" style="zoom:67%;" />

### 3.undo log和redo log的区别？（关于MySQL的日志文件）

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825113547494.png" alt="image-20250825113547494" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825113749511.png" alt="image-20250825113749511" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825113809256.png" alt="image-20250825113809256" style="zoom:67%;" />

### 4.事务的隔离性是如何保证的？

保证隔离性的两种方式：

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825120044515.png" alt="image-20250825120044515" style="zoom:67%;" />

### →追问：请你解释一下MVCC。

用到的概念：

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825120124479.png" alt="image-20250825120124479" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825120133661.png" alt="image-20250825120133661" style="zoom:67%;" />

## **主从同步原理**

在项目上线后，通常会进行主从数据库的构建，如下图所示，读写分离，主从一致。

### 如何让主从数据库同步的呢？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825120746077.png" alt="image-20250825120746077" style="zoom:67%;" />

![image-20250825120806831](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825120806831.png)

## **分库分表**

### 你们项目中用过分库分表吗？怎么用的？

使用分库分表的前提：单表数据量达1000w或20G以后。达到IO瓶颈和CPU瓶颈。

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250825121045473.png" alt="image-20250825121045473" style="zoom:67%;" />