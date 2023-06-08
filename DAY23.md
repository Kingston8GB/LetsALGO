# 算法DAY23 | 669. 修剪二叉搜索树 /  108.将有序数组转换为二叉搜索树 /  538.把二叉搜索树转换为累加树

[TOC]

### 669. 修剪二叉搜索树

- 标签：二叉搜索树
- 难度：7.5

```java
// 题解待完善
public TreeNode trimBST(TreeNode root, int low, int high) {
    if (root == null) {
        return null;
    }
    if (root.val < low) {
        return trimBST(root.right, low, high);
    }
    if (root.val > high) {
        return trimBST(root.left, low, high);
    }
    // root在[low,high]范围内
    root.left = trimBST(root.left, low, high);
    root.right = trimBST(root.right, low, high);
    return root;
}
```



---

### 108.将有序数组转换为二叉搜索树

- 标签：二叉搜索树、二分法
- 难度：7.0

二叉搜索树的构造，要用前序遍历，先new中间节点，在把递归结果分别赋给root.left和root.right。

注意递归终止条件是左指针和右指针相遇。

注意边界条件，推荐用左闭右闭区间。

```java
public TreeNode sortedArrayToBST(int[] nums) {
    TreeNode root = traversal(nums, 0, nums.length - 1);
    return root;
}

// 左闭右闭区间[left, right]
private TreeNode traversal(int[] nums, int left, int right) {
    if (left > right) return null;

    int mid = left + ((right - left) >> 1);
    // 中
    TreeNode root = new TreeNode(nums[mid]);
    // 左
    root.left = traversal(nums, left, mid - 1);
    // 右
    root.right = traversal(nums, mid + 1, right);
    
    // 返回构造好的二叉搜索树
    return root;
}
```

---

###  538.把二叉搜索树转换为累加树

- 标签：二叉搜索树
- 难度：6.5

根据题图，这道题就是按照反中序的序列，不断累加节点的值，将当前累计结果赋给当前节点值。

所以是一道模板题，在中间节点的处理上，一共做两件事：

1. 用一个全局sum变量，累计节点的val；
2. 把当前节点值修改为当前的sum。

```java
int sum;
public TreeNode convertBST(TreeNode root) {
    sum = 0;
    convertBST1(root);
    return root;
}

// 按右中左顺序遍历，累加即可
public void convertBST1(TreeNode root) {
    if (root == null) {
        return;
    }
    convertBST1(root.right);
    sum += root.val;
    root.val = sum;
    convertBST1(root.left);
}
```

迭代法也是用模板。

