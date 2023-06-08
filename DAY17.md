# 算法DAY17 | 110.平衡二叉树 / 257.二叉树的所有路径 / 404.左叶子之和

[TOC]

### 110.平衡二叉树

- 标签：二叉树、递归
- 难度：6.5

**二叉树<u>节点</u>的深度：**指从根节点到该节点的最长简单路径边的条数。

**二叉树<u>节点</u>的高度：**指从该节点到叶子节点的最长简单路径边的条数。

```java
public boolean isBalanced(TreeNode root) {
    return getDepth(root) == -1 ? false : true;
}

// 递归函数：后序遍历，返回结点root的高度
public int getDepth(TreeNode root){
    if(root == null){
        return 0;
    }

    // 求左节点的高度，如果左节点高度为-1，那直接返回-1
    int leftDepth = getDepth(root.left);
    if(leftDepth == -1) return -1;
    // 求右节点的高度，如果右节点高度为-1，那直接返回-1
    int rightDepth = getDepth(root.right);
    if(rightDepth == -1) return -1;

    // 求root结点的高度，如果左右高度差大于1，则返回-1
    if(Math.abs(leftDepth - rightDepth)>1){
        return -1;
        // 否则返回左右较大的高度+1
    }else{
        return Math.max(leftDepth, rightDepth) + 1;
    }
}
```

---

### 257.二叉树的所有路径

- 标签：二叉树、递归、回溯
- 难度：7.0

第一次正式接触回溯。

![17-1](.\img\17-1.png)

回溯可以用于这种找路径的问题。

```java
public List<String> binaryTreePaths(TreeNode root) {
    // 保存所有的路径
    List<String> res = new ArrayList<>();
    
    if(root == null) return res;
    
    // 用于加入当前结点
    List<TreeNode> path = new ArrayList<>();
    
    // 递归函数，不断更新path和res
    traversal(root,path,res);
    return res;
}

public void traversal(TreeNode cur, List<TreeNode> path, List<String> res){
    // 前序遍历，先处理当前结点，加入路径
    path.add(cur);
    
    // 如果当前结点是叶子，把path里面的所有结点拼起来，加入结果集
    if(cur.left == null && cur.right == null){
        StringBuilder sb = new StringBuilder();
        for(int i = 0; i < path.size()-1; i++){
            sb.append(String.valueOf(path.get(i).val)).append("->");
        }
        sb.append(String.valueOf(path.get(path.size()-1).val));
        res.add(sb.toString());
        return;
    }

    // 往左走
    if(cur.left != null){
        traversal(cur.left,path,res);
        // 回溯：走出去一步后，因为还要往右走，所以要在path里删掉左节点
        path.remove(path.size()-1);
    }
    //往右走
    if(cur.right != null){
        traversal(cur.right,path,res);
        // 回溯
        path.remove(path.size()-1);
    }
}
```

---

### 404.左叶子之和

- 标签：二叉树、递归、迭代
- 难度：6.5

一个结点是叶子结点，且是某结点的左结点，那么就管他叫左叶子。

实际的判断条件要通过他的父结点写，即：

```java
t.left != null && t.left.left == null && t.left.right == null
```

#### 方法一：递归（重要）

```java
// 方法一：递归
public int sumOfLeftLeaves(TreeNode root) {
    if (root == null) return 0;
    
    int leftValue = sumOfLeftLeaves(root.left);    // 左
    int rightValue = sumOfLeftLeaves(root.right);  // 右
	
    // 核心：判断左结点是不是左孩子
    if (root.left != null && root.left.left == null && root.left.right == null) { 
        leftValue = root.left.val;
    }
    
    int sum = leftValue + rightValue;  // 中
    return sum;
}
```

#### 方法二：迭代

比较简单，在前序遍历的基础上，简单修改当前结点的处理条件

```java
// 方法二：迭代
public int sumOfLeftLeaves(TreeNode root) {
    int res = 0;
    if(root == null) return res;
    Stack<TreeNode> stack = new Stack<>();
    stack.push(root);
    while(!stack.isEmpty()){
        TreeNode t = stack.pop();
        
        // 简单修改当前结点的处理条件：是左叶子就加入res
        if(t.left != null && t.left.left == null && t.left.right == null){
            res += t.left.val;
        }
        
        if(t.right != null){
            stack.push(t.right);
        }
        if(t.left != null){
            stack.push(t.left);
        }      
    }
    return res;
}
```