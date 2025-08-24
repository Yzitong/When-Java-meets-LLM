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