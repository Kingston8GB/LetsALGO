# 算法DAY18 | 513.找树左下角的值 / 112.路径总和 / 106. 从中序与后序遍历序列构造二叉树

[TOC]

### 513.找树左下角的值

- 标签：二叉树、递归、回溯
- 难度：7.0

找树左下角的值 = 在树的**最后一行**找到最左边的值。

不是一直往左遍历，而是要找**深度最大的叶子结点**，确保在最后一行。

迭代法非常简单，找每一层的首节点即可，下面介绍递归回溯法。

```java
// 定义两个全局变量
// 最大深度，每当遍历到叶子结点，就更新一次当前最大深度
int maxDepth = Integer.MIN_VALUE;
// res用于保存最大深度对应的那个节点值
// 由于先遍历左再遍历右，因此res保存的一定是当前深度最左边的值
int res;

public int findBottomLeftValue(TreeNode root) {
    traversal(root,0);
    return res;
}

// 递归传入参数depth，用于实时更新当前递归体对应的深度，所以需要回溯
public void traversal(TreeNode root, int depth){
    // 递归终止条件：当前节点为叶子结点，则需要返回。返回前判断此时深度是否大于maxDepth
    if(root.left == null && root.right == null){
        if(depth > maxDepth){
            maxDepth = depth;
            res = root.val;
        }
        return;
    }

    // 前序遍历
    // 此题没有对中节点的处理，因为我只是想找左下角的值，不会处理中间的其他节点
    // 左
    if(root.left != null){
        // 递归回溯，传入depth+1，递归返回后，depth的值不变
        traversal(root.left, depth + 1);
    }
    // 右
    if(root.right != null){
        // 递归回溯，传入depth+1，递归返回后，depth的值不变
        traversal(root.right, depth + 1);
    }
}
```

---

###  112. 路径总和

- 标签：二叉树、递归

- 难度：7.0

  

《代码随想录》：

- 如果需要搜索整棵二叉树且不用处理递归返回值，递归函数就不要返回值。（113.路径总和ii）
- 如果需要搜索整棵二叉树且需要处理递归返回值，递归函数就需要返回值。 
- 如果要搜索其中一条符合条件的路径，那么递归一定需要返回值，因为遇到符合条件的路径了就要及时返回。（本题）



```java
public boolean hasPathSum(TreeNode root, int targetSum) {
    if(root == null){
        return false;
    }
    targetSum -= root.val;
    // 叶子结点
    if (root.left == null && root.right == null) {
        return targetSum == 0;
    }
    if (root.left != null) {
        boolean left = hasPathSum(root.left, targetSum);
        if (left) {      // 已经找到
            return true;
        }
    }
    if (root.right != null) {
        boolean right = hasPathSum(root.right, targetSum);
        if (right) {     // 已经找到
            return true;
        }
    }
    return false;
}
```

---

### 106. 从中序与后序遍历序列构造二叉树

- 标签：二叉树、递归
- 难度：7.5

因为要返回二叉树的根节点，所以返回值为TreeNode，传入的参数是两个数组。

所以先找到根节点，**递归体的作用就是在两个数组里划分树的左右部分**，然后根节点的左右孩子递归调用，不断划分左右部分，直到左右数组为空。



主要分为以下几步：

1. 如果**后序/中序数组为空，直接返回null**，代表此处已经没有节点可放置；
2. 找到**后序数组中的最后一个节点**，设置为root；
3. 找到root.val在中序中的位置下标，即**分割点**；
4. 构造四个数组，分别是中序遍历序列的左右孩子数组，和后序遍历序列的左右孩子数组；
5. root的左右孩子递归调用递归体，分别传两个数组。

代码如下：

```java
public TreeNode buildTree(int[] inorder, int[] postorder) {
    // 1.如果后序/中序数组为空，直接返回null
    if(inorder.length == 0 || postorder.length == 0) return null;

    // 2.找到后序最右边节点，设置为root
    TreeNode root = new TreeNode(postorder[postorder.length-1]);
    if(postorder.length == 1) return root;


    // 3.找到中序数组里的分割点
    int delimeterIndex;
    for(delimeterIndex = 0; delimeterIndex < inorder.length; delimeterIndex++){
        if(inorder[delimeterIndex] == root.val) break;
    }

    // 4.得到分割后的四个数组
    int[] leftInOrder = new int[delimeterIndex];
    int[] rightInOrder = new int[inorder.length - delimeterIndex - 1];
    int[] leftPostOrder = new int[delimeterIndex];
    int[] rightPostOrder = new int[inorder.length - delimeterIndex - 1];

    for(int i = 0; i < delimeterIndex; i++){
        leftInOrder[i] = inorder[i];
        leftPostOrder[i] = postorder[i];
    }
    for(int i = delimeterIndex + 1; i < inorder.length; i++){
        rightInOrder[i - delimeterIndex - 1] = inorder[i];
        rightPostOrder[i - delimeterIndex - 1] = postorder[i-1];
    }

    // 5.左 右
    root.left = buildTree(leftInOrder,leftPostOrder);
    root.right = buildTree(rightInOrder,rightPostOrder);

    return root;
}
```

这题难度稍大，想明白了也很难实现，不算是二叉树递归的常规模板题，所以需要强行记忆一下。