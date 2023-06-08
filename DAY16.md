# DAY16 | 104.二叉树的最大深度 / 559.N叉树的最大深度 / 111.二叉树的最小深度 / 222.完全二叉树的结点个数

[TOC]

![16-1](.\img\16-1.png)

### 104.二叉树的最大深度

- 标签：二叉树、递归、迭代
- 难度：5.0

#### 方法一：迭代

迭代法DAY15里讲了，就是套用层序遍历的模板，比较简单。

#### 方法二：递归（重要）

可以后序遍历，以高度代替深度。

```java
public int maxDepth(TreeNode root) {
    if (root == null) {
        return 0;
    }
    return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
}
```

还可以直接求深度。

```java
int maxDepth = 0;

public int maxDepth(TreeNode root) {
    if(root == null) return maxDepth;
    ans(root,0);
    return maxDepth;

}

public void ans(TreeNode t, int depth){
    if(t == null) return;
    depth++; // 得到当前结点t所在深度
    maxDepth = depth > maxDepth ? depth : maxDepth; // 更新当前最大值
    
    ans(t.left,depth);
    ans(t.right,depth);
}
```

---

### 559.N叉树的最大深度

和上一道题几乎一样。

```java
public int maxDepth(Node root) {
    if (root == null) return 0;

    int depth = 0;
    if (root.children != null){
        for (Node child : root.children){
            depth = Math.max(depth, maxDepth(child));
        }
    }

    return depth + 1; //中节点
}
```

---

### 111.二叉树的最小深度

这个题要注意，只有左右孩子都为空，才能计算最小深度。

```java
// 递归函数：返回某一结点所在的高度（从下往上）
public int minDepth(TreeNode root) {
    // 终止条件：结点空，则高度为0
    if(root == null) return 0;

    int leftDepth = minDepth(root.left);
    int rightDepth = minDepth(root.right);

    if(root.left == null) return rightDepth + 1;
    if(root.right == null) return leftDepth + 1;

    return Math.min(leftDepth,rightDepth) + 1;
}
```

---

### 222.完全二叉树的节点个数

模仿求最大深度，很容易写出。

```java
public int countNodes(TreeNode root) {
    if(root == null) return 0;

    int leftNum = countNodes(root.left);
    int rightNum = countNodes(root.right);

    return leftNum + rightNum + 1;
}
```

