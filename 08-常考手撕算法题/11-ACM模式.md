# ACM模式

### 两种模式的核心差异

| 对比项       | 力扣核心代码模式                                  | ACM 模式                                                     |
| ------------ | ------------------------------------------------- | ------------------------------------------------------------ |
| **数据结构** | 平台提供 `ListNode` 类，无需关心定义细节          | 必须手动编写 `ListNode` 类（包含 `val` 和 `next`）           |
| **输入处理** | 直接接收 `ListNode head` 作为参数（平台已解析好） | 需自己实现 `arrayToList` 方法，将输入字符串 / 数组转为链表   |
| **输出处理** | 直接返回 `ListNode` 即可，平台自动校验结果        | 需自己实现 `listToString` 方法，将链表转为可读性字符串并打印 |
| **程序入口** | 无需 `main` 方法，平台自动调用核心函数            | 必须编写 `main` 方法，控制从输入到输出的完整流程             |
| **测试方式** | 平台自动用多组测试用例调用 `reverseList` 方法     | 需手动输入测试数据（或写死用例），手动触发反转并查看输出     |

在 ACM 模式中，**栈、数组、字符串这些不需要自己 “建立基础结构”**

```java
// 二叉树节点类：用于表示二叉树的节点结构
class TreeNode {
    TreeNode left, right;
    int val;

    public TreeNode(int val) {
        this.val = val;
    }
}

// 单链表节点类：用于表示单向链表的节点结构
class LinkNode {
    int val;
    LinkNode next;

    public LinkNode(int val) {
        this.val = val;
    }
}

// 双向链表节点类：用于表示双向链表的节点结构（包含key和value）
class DLinkedNode {
    int key;
    int value;
    DLinkedNode prev;
    DLinkedNode next;

    public DLinkedNode() {}

    public DLinkedNode(int _key, int _value) {
        key = _key;
        value = _value;
    }
}

// 导入Scanner类，用于处理控制台输入
import java.util.Scanner;
import java.util.ArrayList;
import java.util.List;
import java.util.Deque;
import java.util.ArrayDeque;
import java.util.Stack;


public class ACMTemplate {
    public static void main1(String[] args) {
        // 程序代码（需根据具体问题补充逻辑）
        
        // Scanner使用示例：处理输入一行数组
        Scanner scanner = new Scanner(System.in);
        //输入字符串
        String[] intStrings = scanner.nextLine().split(" "); // 按空格分割输入的一行内容
        
        List<Integer> array = new ArrayList<>();
        for (String s : intStrings) {
            array.add(Integer.parseInt(s)); // 将每个字符串转为整数并存入列表
        }
        
        scanner.close();
        
        // 注：hasNextInt()存在的问题——会一直等待输入，直到遇到非整数内容（如字母）或手动终止输入（如 Ctrl+D）
    }
    public static void main2(String[] args) {
        // 写死一个测试数组（直接初始化）
        int[] testArray = {1, 2, 3, 4, 5}; // 这就是“写死”的数组
        System.out.println("原数组：" + arrayToString(testArray));
        
        // 调用核心逻辑（反转数组）
        reverseArray(testArray);
        
        System.out.println("反转后：" + arrayToString(testArray));
    }
}
```

