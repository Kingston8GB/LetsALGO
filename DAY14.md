# 算法DAY14 | 二叉树理论基础 / 144.二叉树的前序遍历 / 145.二叉树的后序遍历 / 94.二叉树的中序遍历（递归、迭代、统一）

[TOC]



### 二叉树理论基础

#### 1. 二叉树的分类

1. 满二叉树

2. 完全二叉树

3. 二叉搜索树

   ![14-1](.\img\14-1.png)

4. 平衡二叉搜索树（AVL树）

   ![14-2](.\img\14-2.png)

#### 2. 二叉树的存储

1. 二叉链表（链式存储）：用指针把物理结点串在一起

   ![14-3](.\img\14-3.png)

2. 数组（顺序存储）：按层序顺序存储结点，空结点位置要空出来

   ![14-4](.\img\14-4.png)

   如果结点下标为i，它的左孩子（若存在）下标为2i+1，右孩子（若存在）为2i+2。

#### 3. 二叉树的遍历方式

1. 深度优先遍历：先往深走，遇到叶子结点往回走，**用栈（递归）实现**

   ![14-5](.\img\14-5.png)

2. 广度优先遍历：一层一层去遍历，**用队列实现**

#### 4.二叉树的结点定义

```java
public class TreeNode {
    int val;
  	TreeNode left;
  	TreeNode right;
  	
    TreeNode() {}
  	TreeNode(int val) { this.val = val; }
  	TreeNode(int val, TreeNode left, TreeNode right) {
    		this.val = val;
    		this.left = left;
    		this.right = right;
  	}
}
```

---

### 144.二叉树的前序遍历 / 145.二叉树的后序遍历 / 94.二叉树的中序遍历

[题目链接](https://leetcode.cn/problems/binary-tree-preorder-traversal/)

- 标签：二叉树
- 难度：6.0

#### 方法一：递归

1. 递归的三要素（重要）

   - **确定递归函数的参数和返回值**：确定哪些参数是每次递归过程中需要处理的，**用到什么传什么参数**；还要确定每次递归要返回什么值

   - **确定终止条件**：递归在什么条件下返回

   - **确定单层递归的逻辑**：确定每一层递归需要处理的信息

     

2. 以<u>前序遍历</u>为例：

   - **确定递归函数的参数和返回值**：因为可能要输出当前结点的值，或者说要对当前结点进行操作，所以**参数里要包含当前结点**，如果要存到数组里，那么参数还要传入一个数组；

     返回值为void，因为只需要打印输出或者存到数组里，在递归函数里就能做了。

     ```java
     public void preorder(TreeNode root, List<Integer> result)
     ```

     

   - **确定终止条件**：当前遍历的结点为空，就直接return。

     ```java
     if (root == null) {
         return;
     }
     ```

     

   - **确定单层递归的逻辑**：按照左中右的顺序处理结点

     ```java
     result.add(root.val);
     preorder(root.left, result);
     preorder(root.right, result);
     ```

##### 前序遍历代码

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<Integer>();
        preorder(root, result);
        return result;
    }

    public void preorder(TreeNode root, List<Integer> result) {
        if (root == null) {
            return;
        }
        result.add(root.val);
        preorder(root.left, result);
        preorder(root.right, result);
    }
}
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
```

##### 中序遍历代码

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        inorder(root, res);
        return res;
    }

    void inorder(TreeNode root, List<Integer> result) {
        if (root == null) {
            return;
        }
        inorder(root.left, list);
        // 区别在于这一句
        result.add(root.val); 
        inorder(root.right, list);
    }
}
```

##### 后序遍历代码

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        postorder(root, res);
        return res;
    }

    void postorder(TreeNode root, List<Integer> list) {
        if (root == null) {
            return;
        }
        postorder(root.left, list);
        postorder(root.right, list);
        // 区别在于这一句
        list.add(root.val);
    }
}
```

---

#### 方法二：迭代（重要）

```java
// 前序遍历顺序：中-左-右，入栈顺序：中-右-左
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        if (root == null){
            return result;
        }
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);
        while (!stack.isEmpty()){
            TreeNode node = stack.pop();
            result.add(node.val);
            if (node.right != null){
                stack.push(node.right);
            }
            if (node.left != null){
                stack.push(node.left);
            }
        }
        return result;
    }
}

// 中序遍历顺序: 左-中-右 入栈顺序： 左-右
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        if (root == null){
            return result;
        }
        Stack<TreeNode> stack = new Stack<>();
        TreeNode cur = root;
        while (cur != null || !stack.isEmpty()){
           if (cur != null){
               stack.push(cur);
               cur = cur.left;
           }else{
               cur = stack.pop();
               result.add(cur.val);
               cur = cur.right;
           }
        }
        return result;
    }
}

// 后序遍历顺序 左-右-中 入栈顺序：中-左-右 出栈顺序：中-右-左， 最后翻转结果
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        if (root == null){
            return result;
        }
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);
        while (!stack.isEmpty()){
            TreeNode node = stack.pop();
            result.add(node.val);
            if (node.left != null){
                stack.push(node.left);
            }
            if (node.right != null){
                stack.push(node.right);
            }
        }
        Collections.reverse(result);
        return result;
    }
}
```

