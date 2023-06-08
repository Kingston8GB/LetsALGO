# 算法DAY21 | 530.二叉搜索树的最小绝对差 / 501.二叉搜索树中的众数 / 236. 二叉树的最近公共祖先 / 235. 二叉搜索树的最近公共祖先

[TOC]

### 530.二叉搜索树的最小绝对差

- 标签：二叉搜索树
- 难度：6.5

二叉搜索树离不开中序遍历的有序性，因此只需要在中序遍历序列里找到两两之间的最小差值。

递归和迭代两个方法都需要掌握，是一道模板题。

```java
// 递归法
public int getMinimumDifference(TreeNode root) {

    if(root == null) return 0;
    traversal(root);
    return minDiff;
}
public void traversal(TreeNode root){
    if(root == null) return;

    traversal(root.left);

    // if(pre == null) minDiff = root.val;
    if(pre != null && root.val - pre.val < minDiff) minDiff = root.val - pre.val;
    pre = root;

    traversal(root.right);
}

// 迭代法
public int getMinimumDifference(TreeNode root){
    if(root == null) return 0;
    Stack<TreeNode> stack = new Stack<>();
    TreeNode cur = root;
    while(cur!=null || !stack.isEmpty()){
        if(cur!=null){
            stack.push(cur);
            cur = cur.left;

        }else{
            cur = stack.pop();
            if(pre != null && cur.val - pre.val < minDiff){
                minDiff = cur.val - pre.val;
            }
            pre = cur;
            cur = cur.right;
        }

    }
    return minDiff;
}
```



---

###  501.二叉搜索树中的众数

- 标签：二叉搜索树
- 难度：7.0

和上一道题比较相似，也是通过二叉搜索树的中序遍历序列递增有序性质。

题目要求不能有辅助空间，因此不能用哈希表统计中序序列中每个元素的出现频次；取而代之，可以采用双指针，用pre指针保存前面节点的值，从而确定当前元素出现了几次，具体流程如下：

对当前节点的操作分为两部分，**第一部分是更新count变量：**

1. 如果pre为空（中序遍历序列的第一个节点），或当前节点与pre结点值不同，则count置为1；
2. 此外，当前节点与pre值相同，则count++；

**第二部分是根据count的值，更新结果集res：**

1. 如果count>maxCount，则更新maxCount，清空res并加入当前节点；
2. 如果count<=maxCount，直接在res中加入当前节点

最后别忘了更新pre。

```java
List<Integer> res = new ArrayList<>();
// int mode;
int count = 1;
int maxCount = 1;
TreeNode pre = null;
public int[] findMode(TreeNode root) {
    // 递归法
    if(root == null) return null;
    traversal(root);
    return res.stream().mapToInt(k -> k).toArray();
}

public void traversal(TreeNode root){
    if(root == null) return;
    
    traversal(root.left);
    int rootValue = root.val;
    // 计数
    if (pre == null || rootValue != pre.val) {
        count = 1;
    } else {
        count++;
    }
    // 更新结果以及maxCount
    if (count > maxCount) {
        res.clear();
        res.add(rootValue);
        maxCount = count;
    } else if (count == maxCount) {
        res.add(rootValue);
    }
    pre = root;
    traversal(root.right);
}
```

---

### 236. 二叉树的最近公共祖先

- 标签：二叉树、递归
- 难度：7.5

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if(root == null || root == p || root == q){
        return root;
    }

    // 后序遍历
    TreeNode left = lowestCommonAncestor(root.left,p,q);
    TreeNode right = lowestCommonAncestor(root.right,p,q);

    if(left != null && right != null){
        return root;
    }else if(left == null && right != null){
        return right;
    }else if(left != null && right == null){
        return left;
    }else{
        return null;
    }
}
```

---

### 235. 二叉搜索树的最近公共祖先

- 标签：二叉搜索树
- 难度：6.5

对二叉搜索树进行前序遍历：

- 如果p和q的值都**大于**当前节点的值，那就往**右**孩子走
- 如果p和q的值都**小于**当前节点的值，那就往**左**孩子走
- 如果p和q一个在左，一个在右，则当前节点**一定**是最近公共祖先

例如图中值为5的节点，一定是最近公共祖先，不用继续往下层找了。



所以这道题的递归和迭代可以不使用常规模板，就根据上面的步骤写。

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    // 递归法
    // 如果p和q的值都大于当前节点的值，那就往右孩子走
    if(root.val < p.val && root.val < q.val){
        return lowestCommonAncestor(root.right, p, q);
    }
    // 如果p和q的值都小于当前节点的值，那就往左孩子走
    else if(root.val > p.val && root.val > q.val){
        return lowestCommonAncestor(root.left, p, q);
    }
    // 如果p和q一个在左，一个在右，则当前节点一定是最近公共祖先
    else{
        return root;
    }

    // 迭代法就更简单了
    while(true){
        if(root.val < p.val && root.val < q.val){
            root = root.right;
        }else if(root.val > p.val && root.val > q.val){
            root = root.left;
        }else{
            return root;
        }
    }
}
```

