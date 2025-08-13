# Java基础-集合

## Java集合框架

![image-20250813203534111](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250813203534111.png)

时间复杂度：就是代码执行次数与n的关系（n为变量）

空间复杂度：算法所占用的额外储存空间与n的关系（n为变量）

一般来说，计算机的空间较多，一般可以牺牲空间去换时间。

数组的删除和插入操作的复杂度都是O(n)，因为是实线性的空间，其插入和删除操作的效率很低。

## ArrayList相关面试题

ArrayList为动态数组，底层是数组（连续线性的空间存储，其所储存的类型需要一致）

### 1.为什么数组的寻址需要从0开始，而不是从1开始呢？

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250813204227997.png" alt="image-20250813204227997" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250813204320745.png" alt="image-20250813204320745" style="zoom: 50%;" />

### 2.ArrayList的构造方法

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



### 3.ArrayList的底层实现原理

![image-20250813205319269](https://raw.githubusercontent.com/Yzitong/When-Java-meets-LLM/main/images/image-20250813205319269.png)

### 4.ArrayList list = new ArrayList(10) 这句代码扩容了几次？

这句代码只是声明和实例了一个ArrayList，指定了容量为10，并没有进行扩容。

 

### 5.数组与List之间的相互转换

数组转换为List，用Arrays.asList()；当数组内容变换时，List集合的内容也会受影响（指向同一地址）

List转换为数组，用list.toArray()；当list内容变换时，数组的内容不会受影响（建立了新的数组，数组拷贝）

 

### 6.ArrayList和LinkedList的区别是什么？（高频）

a）ArrayList底层是动态数组，LinkedList底层是双向链表

b）ArrayList在已知索引时查找很快为O（1）；二者在增删操作时复杂度都是O（n）

c）内存空间上，ArrayList更节省空间

d）二者本身都是线程不安全的



## HashMap相关面试题

二叉树，二叉树的种类很多，常用的主要是二叉搜索树（BST）和红黑树

**二叉搜索树（BST）：**

定义：满足 左 < 根 < 右

查找、插入、删除的复杂度：O（logn）

在极端情况时，二叉搜索树可能会退化成链表

**红黑树：**

定义：平衡的二叉搜索树，也叫平衡的二叉B树；

红黑树的性质保证了二叉树的平衡（不满足性质时就会发生旋转）

查找、插入、删除的复杂度：O（logn）平衡使得时间复杂度更加稳定