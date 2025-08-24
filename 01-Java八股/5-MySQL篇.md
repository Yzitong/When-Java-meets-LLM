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

![image-20250824222347806](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250824222347806.png)

### →追问：什么是回表查询？

通过二级索引找到对应的主键值，到聚集索引中查找整行数据，这个过程就是回表。

![image-20250824222420734](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250824222420734.png)