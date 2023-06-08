# 算法DAY20 | 654.最大二叉树 / 617.合并二叉树 /  700.二叉搜索树中的搜索

[TOC]

### 654.最大二叉树

- 标签：二叉树、递归
- 难度：7.0

相比【106. 从中序与后序遍历序列构造二叉树】简单一点，但异曲同工。

本质上都是二叉树的构造，这类问题要采用前序遍历，即先添加中间节点，才能添加他的左右节点。左右节点需要递归添加。

可以采用原题函数作为递归体，需要每次传入一个数组，这个数组里的最大值就是当前的中间节点值。返回值就是当前的根节点（中间节点），也就是上层函数的root.left或者root.right。

思路如下：

1. **递归退出条件：**如果数组空了，就没有元素可处理了，也就是已经到null节点了，直接返回null；如果数组只剩1个元素，则直接返回以这个元素为值的节点（中间节点）；
2. **前序遍历-处理中间节点：**找当前数组内最大值（这里可以提取出一个方法），记录下最大值和对应的下标，作为分数组的依据；把数组内这个下标左边的值保存到一个新数组里，再把下标右边的值保存到另一个新数组里；
3. **前序遍历-处理左右节点：**左节点调用递归函数，递归函数的返回值就是根据左边数组确定的根节点；右边同理；
4. **返回根节点**。



我的第一版代码：

```java
public TreeNode constructMaximumBinaryTree(int[] nums) {
    // 1.递归退出条件
    if(nums.length == 0) return null;
    if(nums.length == 1) return new TreeNode(nums[0]);
    
    // 2.前序遍历-处理中间节点
    int maxNum = Integer.MIN_VALUE;
    int maxNumIndex = -1;
    for(int i = 0; i < nums.length; i++){
        if(nums[i] > maxNum){
            maxNum = nums[i];
            maxNumIndex = i;
        }
    }
    TreeNode root = new TreeNode(maxNum);
    // 根据分割点maxNumIndex，构建左右两个新数组
    int[] leftNums = new int[maxNumIndex];
    int[] rightNums = new int[nums.length - maxNumIndex - 1];
    for(int i = 0; i < maxNumIndex; i++){
        leftNums[i] = nums[i];
    }
    for(int i = 0; i < nums.length - maxNumIndex - 1; i++){
        rightNums[i] = nums[i + maxNumIndex + 1];
    }

    // 3.前序遍历-处理左右节点
    root.left = constructMaximumBinaryTree(leftNums);
    root.right = constructMaximumBinaryTree(rightNums);

    // 4.返回根节点
    return root;
}
```

但这个代码可以优化，不用每次都构建新数组，这样很多余。

我们只需要构建新的递归函数，**每次递归把要操作的子数组的初始下标和终止下标传进去**，在原数组上操作就行了。

**递归退出条件就不再是数组为空，而是初始和终止两个下标相遇了。**



优化后的代码（来自代码随想录）：

```java
public TreeNode constructMaximumBinaryTree(int[] nums) {
    return constructMaximumBinaryTree1(nums, 0, nums.length);
}

// 新的递归函数，加上了子数组的初始下标和终止下标，就可以不创建新数组而在原数组上操作了
public TreeNode constructMaximumBinaryTree1(int[] nums, int leftIndex, int rightIndex) {
    // 1.递归退出条件：两个下标相遇
    if (rightIndex - leftIndex < 1) {// 没有元素了
        return null;
    }
    if (rightIndex - leftIndex == 1) {// 只有一个元素
        return new TreeNode(nums[leftIndex]);
    }
    
    // 2.前序遍历-处理中间节点（和上一个代码一样）
    int maxIndex = leftIndex;// 最大值所在位置
    int maxVal = nums[maxIndex];// 最大值
    for (int i = leftIndex + 1; i < rightIndex; i++) {
        if (nums[i] > maxVal){
            maxVal = nums[i];
            maxIndex = i;
        }
    }
    TreeNode root = new TreeNode(maxVal);
    
    // 不需要创建真的新数组了
    
    // 3.前序遍历-处理左右节点：传入原始数组+两个下标，而不用划分新数组
    root.left = constructMaximumBinaryTree1(nums, leftIndex, maxIndex);
    root.right = constructMaximumBinaryTree1(nums, maxIndex + 1, rightIndex);
    
    // 4.返回根节点
    return root;
}
```

此题在力扣上的通过率极高……

---

###  617.合并二叉树

- 标签：二叉树、递归
- 难度：6.5

此题可以视为二叉树的构造问题，但是需要同时操作两棵二叉树。

迭代法比较重要，是用队列模拟两棵树，和<a href="https://leetcode.cn/problems/symmetric-tree/">101.对称二叉树</a>异曲同工。

```java
// 递归法（前序遍历）
public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
    // 递归退出条件：如果t1或者t2为空，那么就返回给另一个
    if(root1 == null) return root2;
    if(root2 == null) return root1;

    // 中：以t1为基础，修改重叠节点的值
    root1.val += root2.val;

    // 左、右
    root1.left = mergeTrees(root1.left,root2.left);
    root1.right = mergeTrees(root1.right,root2.right);

    return root1;
}

// 迭代法
public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
    if(root1 == null) return root2;
    if(root2 == null) return root1;

    // TreeNode root = new TreeNode(root1.val + root2.val);
    Deque<TreeNode> deque = new LinkedList<>();
    deque.addFirst(root1);
    deque.addLast(root2);
    while(!deque.isEmpty()){
        // 同时遍历，处理两棵树位置对应的元素
        TreeNode t1 = deque.pollFirst();
        TreeNode t2 = deque.pollLast();

        // 此时t1、t2一定非空，不然放不进队列
        t1.val += t2.val;

        // 如果t1和t2的右节点都非空，那么就把右节点放进队列
        if(t1.right != null && t2.right != null){
            deque.addFirst(t1.right);
            deque.addLast(t2.right);
        }
        // 如果t1和t2的左节点都非空，那么就把右节点放进队列
        if(t1.left != null && t2.left != null){
            deque.addFirst(t1.left);
            deque.addLast(t2.left);
        }

        // 如果t1的左节点为空，但t2左节点不空，那就把t2的左节点赋给t1
        if(t1.left == null && t2.left != null){
            t1.left = t2.left;
        }
        // 如果t1的右节点为空，但t2右节点不空，那就把t2的右节点赋给t1
        if(t1.right == null && t2.right != null){
            t1.right = t2.right;
        }
    }

    // 这样就能保证在root1的基础上重叠了root2
    return root1;
}
```

---

###  700.二叉搜索树中的搜索

- 标签：二叉搜索树
- 难度：6.5

充分利用好二叉搜索树的特性，尤其是迭代法。

代码如下：

```java
// 递归法
public TreeNode searchBST(TreeNode root, int val) {
    if(root == null || root.val == val) return root;
    TreeNode res = null;
    if(root.val > val) res = searchBST(root.left,val);
    if(root.val < val) res = searchBST(root.right,val);
    return res;
}

// 迭代法
public TreeNode searchBST(TreeNode root, int val) {
    if(root == null) return null;
    TreeNode p = root; // 有点多余
    while(p != null && p.val != val){
        if(p.val > val) p = p.left;
        else if(p.val < val) p = p.right;
    }
    return p;
}
```

注意：递归是有返回值的，别忘了接收，因为找到符合条件的节点就需要返回；如果单纯对那个节点进行处理则不需要返回值。

---

### 98.验证二叉搜索树

- 标签：二叉搜索树
- 难度：6.5

思路就一句话：验证中序遍历序列是否递增有序。

遍历整棵树，要有返回值。

迭代法和递归法都需要掌握。

```java
// 保存上一个结点，用于比较值
TreeNode pre = null;
// 递归法：中序遍历，默认返回true，除非遇到节点值小于等于上一个节点的情况
public boolean isValidBST(TreeNode root) {
    if(root == null) return true;
    // 左
    boolean left = isValidBST(root.left);

    // 中
    if(pre != null && pre.val >= root.val) return false;
    pre = root;

    // 右
    boolean right = isValidBST(root.right);

    return left && right;
}

// 迭代法：中序遍历
public boolean isValidBST(TreeNode root) {
    if(root == null) return true;
    Stack<TreeNode> stack = new Stack<>();
    while(root != null || !stack.isEmpty()){
        while(root != null){
            stack.push(root);
            root = root.left;
        }
        TreeNode temp = stack.pop();
        if(pre != null && pre.val >= temp.val){
            return false;
        }
        pre = temp;
        root = temp.right;
    }
    return true;
}
```

