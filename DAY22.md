# 算法DAY22 | 530.二叉搜索树的最小绝对差 / 450.删除二叉搜索树中的节点

[TOC]

### 701.二叉搜索树中的插入操作

- 标签：二叉搜索树
- 难度：6.5

这道题需要用到二叉搜索树的特性，不需要遍历整棵二叉树。

因为不要求调整树的结构，因此只需要找到合适的位置，插入即可，比较简单。

```java
// 递归法
public TreeNode insertIntoBST(TreeNode root, int val) {
    // 递归出口，如果找到了空节点，就找到了插入位置，直接创建新节点，赋值val
    if(root == null){
        return new TreeNode(val);
    }
    // 没有中间节点处理
    // 向左遍历
    if(root.val > val){
        root.left = insertIntoBST(root.left,val);
    }
    // 向右遍历
    else{
        root.right = insertIntoBST(root.right,val);
    }

    return root;
}

// 迭代法
public TreeNode insertIntoBST(TreeNode root, int val) {
    // 如果树为空，直接返回以val为唯一节点的新树
    if(root == null){
        return new TreeNode(val);
    }
    // 定义遍历指针
    TreeNode p = root;

    // 为给左右孩子赋值，需要保留当前节点的父节点，否则无法连成二叉树
    TreeNode pre = null;

    // 二叉搜索树的查找
    while(p != null){
        // 记得再往左/右走之前，先保存当前节点
        pre = p;
        if(p.val > val){
            p = p.left;
        }else if(p.val < val){
            p = p.right;
        }
    }

    // 往左/右插入val
    if(pre.val > val){
        pre.left = new TreeNode(val);
    }else{
        pre.right = new TreeNode(val);
    }

    // 返回根节点
    return root;
}
```



---

### 450.删除二叉搜索树中的节点

- 标签：二叉搜索树
- 难度：7.5

本题涉及到树结构的修改，故难度较大。

>- **第一种情况**：没找到删除的节点，遍历到空节点直接返回了
>- 找到删除的节点
>  - **第二种情况**：左右孩子都为空（叶子节点），直接删除节点， 返回NULL为根节点
>  - **第三种情况**：删除节点的左孩子为空，右孩子不为空，删除节点，右孩子补位，返回右孩子为根节点
>  - **第四种情况**：删除节点的右孩子为空，左孩子不为空，删除节点，左孩子补位，返回左孩子为根节点
>  - **第五种情况**：左右孩子节点都不为空，则将删除节点的左子树头结点（左孩子）放到删除节点的右子树的最左面节点的左孩子上，返回删除节点右孩子为新的根节点。

```java
public TreeNode deleteNode(TreeNode root, int key) {
    if(root == null){
        return null;
    }
    if(root.val == key){
        if(root.left == null && root.right == null){
            return null;
        }else if(root.left != null && root.right == null){
            return root.left;
        }else if(root.left == null && root.left != null){
            return root.right;
        }else{
            TreeNode cur = root.right;
            while(cur.left != null){
                cur = cur.left;
            }
            cur.left = root.left;
            return root.right;
        }
    }
    if(root.val > key){
        root.left = deleteNode(root.left,key);
    }else{
        root.right = deleteNode(root.right,key);
    }
    return root;
}
```