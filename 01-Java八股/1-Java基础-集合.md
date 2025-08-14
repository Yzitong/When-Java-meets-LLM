# Java基础-集合

## Java集合框架

![image-20250813203534111](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250813203534111.png)

时间复杂度：就是代码执行次数与n的关系（n为变量）

空间复杂度：算法所占用的额外储存空间与n的关系（n为变量）

一般来说，计算机的空间较多，一般可以牺牲空间去换时间。

数组的删除和插入操作的复杂度都是O(n)，因为是实线性的空间，其插入和删除操作的效率很低。

## ArrayList相关面试题

ArrayList为动态数组，底层是数组（连续线性的空间存储，其所储存的类型需要一致）

### 1. 为什么数组的寻址需要从0开始，而不是从1开始呢？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250813204227997.png" alt="image-20250813204227997" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250813204320745.png" alt="image-20250813204320745" style="zoom: 50%;" />

### 2. ArrayList的构造方法

a)无参构造，默认为空 

b)带初始化容量的构造

c）放入collection集合，将collection对象转化为数组

```java
// a、构造一个空的 ArrayList，初始容量默认为 10（底层数组会自动扩容）
ArrayList<String> list1 = new ArrayList<>();  

// b、构造一个指定初始容量（如 20）的 ArrayList，避免频繁扩容
ArrayList<Integer> list2 = new ArrayList<>(20);  
list2.add(1);  
list2.add(2);  

// c、从 Collection 集合转换（将已有集合转化为 ArrayList）
// 1. 先创建一个 Collection（如另一个 ArrayList）
Collection<String> sourceCollection = new ArrayList<>();  
sourceCollection.add("Java");  
sourceCollection.add("大模型");  

// 2. 通过构造方法将 Collection 转换为 ArrayList
ArrayList<String> list3 = new ArrayList<>(sourceCollection);  

// 也可以直接传入其他 Collection 实现类（如 HashSet）
Collection<Integer> set = new HashSet<>();  
set.add(100);  
set.add(200);  
ArrayList<Integer> list4 = new ArrayList<>(set);  
```



### 3. ArrayList的底层实现原理

![image-20250813205319269](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250813205319269.png)

为什么扩容是1.5倍呢？因为1.5 倍扩容可在源码中通过**位运算**实现，运算效率更高：

```java
int newCapacity = oldCapacity + (oldCapacity >> 1); 
// 等价于 oldCapacity * 1.5
```



### 4. ArrayList list = new ArrayList(10) 这句代码扩容了几次？

这句代码只是声明和实例了一个ArrayList，指定了容量为10，并没有进行扩容。

 

### 5. 数组与List之间的相互转换

数组转换为List，用Arrays.asList()；当数组内容变换时，List集合的内容也会受影响（指向同一地址）

List转换为数组，用list.toArray()；当list内容变换时，数组的内容不会受影响（建立了新的数组，数组拷贝）



### 6. ArrayList 与数组（Array）的核心区别如下：

| **对比维度** | **ArrayList**                                         | **数组（Array）**                                     |
| ------------ | ----------------------------------------------------- | ----------------------------------------------------- |
| 长度特性     | 动态扩容 / 缩容，随元素数量自动调整                   | 一旦创建，长度固定，无法改变                          |
| 类型安全     | 支持泛型（如 `ArrayList<String>`），编译期约束类型    | 无泛型，运行时才可能暴露类型错误                      |
| 存储数据类型 | 仅存对象，基本类型需用包装类（如 `Integer`）          | 可直接存基本类型（如 `int[]`）或对象（如 `String[]`） |
| 操作便捷性   | 提供 `add()`、`remove()` 等丰富 API，自动处理元素移动 | 仅支持下标访问，增删需手动搬移数据，操作繁琐          |
| 初始化要求   | 无需指定大小（无参构造默认初始容量 10）               | 创建时必须指定长度（如 `new int[5]`）                 |

 

### 7. ArrayList和LinkedList的区别是什么？（高频）

a）ArrayList底层是动态数组，LinkedList底层是双向链表

b）ArrayList在已知索引时查找很快为O（1）；二者在增删操作时复杂度都是O（n）

c）内存空间上，ArrayList更节省空间

d）二者本身都是线程不安全的



## HashMap相关面试题

### 1. 二叉搜索树与红黑树

二叉树，二叉树的种类很多，常用的主要是二叉搜索树（BST）和红黑树

**二叉搜索树（BST）：**

定义：满足 左 < 根 < 右

查找、插入、删除的复杂度：O（logn）

在极端情况时，二叉搜索树可能会退化成链表

**红黑树：**

定义：平衡的二叉搜索树，也叫平衡的二叉B树；

红黑树的性质保证了二叉树的平衡（不满足性质时就会发生旋转）

查找、插入、删除的复杂度：O（logn）平衡使得时间复杂度更加稳定

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250814102653346.png" alt="image-20250814102653346" style="zoom: 33%;" />

### 2.散列表（哈希表）

**在HashMap中最重要的一个数据结构就是散列表（哈希表），在散列表中又使用到了红黑树和链表**

**散列表（哈希表）：**

定义：根据键直接访问在内存存储位置值的数据结构，由数组演化而来，利用了数组支持 按照下标进行随机访问数据的特性。

**散列函数（哈希函数）：**

将键（Key）映射为数组下标的函数叫做散列函数，表示为hashValue = hash（key）

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250814103507584.png" alt="image-20250814103507584" style="zoom: 33%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250814103526022.png" alt="image-20250814103526022"  />



### 3.如何解决哈希冲突？

**散列冲突（哈希冲突）：**

实际中，想要找到一个哈希函数能够做到对于不同的key计算得到的散列值都不同几乎是不可能的，不同key的哈希值一样了这就是哈希冲突。

**！拉链法——解决哈希冲突的一个办法（也是HashMap中底层解决哈希冲突的方法）！**

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250814103817801.png" alt="image-20250814103817801" style="zoom: 33%;" />

**拉链法的复杂度分析（重点）：**

a）插入时的复杂度为O（1）

b）查找和删除的平均复杂度也为O（1）；但当哈希冲突比较多，散列表退化为链表时，其时间复杂度就会变成O（n）这时如果将链表替换为其他高效的动态数据结构，比如红黑树，其时间复杂度就是O（logn）

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250814104901611.png" alt="image-20250814104901611" style="zoom:33%;" />



### 4.HashMap的实现原理（高频）

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250814105009820.png" alt="image-20250814105009820" style="zoom: 67%;" />

→追问：HashMap的jdk1.7和jdk1.8有什么区别？1.8之前只有拉链法，1.8后才有红黑树

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250814110706417.png" alt="image-20250814110706417" style="zoom:67%;" />



### 5.HashMap的put方法的具体流程？（高频）

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250814110747794.png" alt="image-20250814110747794" style="zoom: 80%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250814110758520.png" alt="image-20250814110758520" style="zoom: 67%;" />

### 6.讲一讲HashMap的扩容机制

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250814110848029.png" alt="image-20250814110848029" style="zoom: 50%;" />



### 7.hashMap的寻址算法

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250814111054085.png" alt="image-20250814111054085" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250814111109119.png" alt="image-20250814111109119" style="zoom: 50%;" />

//注：1.从此可知为何hashMap数组的长度选择16，为2的次幂。

​		a）计算索引是用&操作效率更高

​		b）扩容时重新计算索引用&操作效率更高



### 8.如何避免hashMap在jdk1.7情况下遇到的多线程死循环问题？

jdk1.7的hashMap没有红黑树，只有数组+链表，在数组进行扩容时，因为链表是头插法，在进行数据迁移的过程中，多线程中有可能导致死循环。

![image-20250814111235420](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250814111235420.png)
