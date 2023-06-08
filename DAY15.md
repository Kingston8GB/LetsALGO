# DAY15 | 层序遍历 / 226.翻转二叉树 / 101.对称二叉树

[TOC]

### 层序遍历

![15-1](.\img\15-1.gif)

#### 102.二叉树的层序遍历

用队列实现。

```java
List<List<Integer>> res = new ArrayList<List<Integer>>();
    
public List<List<Integer>> levelOrder(TreeNode root) {
    if(root == null) return res;
    Queue<TreeNode> q = new LinkedList<>();
    q.offer(root);

    while(!q.isEmpty()){
        int size = q.size();
        List<Integer> innerRes = new ArrayList<>();
        while(size > 0){
            TreeNode t = q.poll();
            innerRes.add(t.val);
            size--;
            if(t.left!=null){
                q.offer(t.left);
            }
            if(t.right!=null){
                q.offer(t.right);
            }
        }
        res.add(innerRes);

    }
    return res;
}
```

#### 107.二叉树的层序遍历II

在上道题的基础上反转res数组。

```java
public List<List<Integer>> levelOrderBottom(TreeNode root) {
    List<List<Integer>> res = new ArrayList<List<Integer>>();


    if(root == null) return res;
    Queue<TreeNode> q = new LinkedList<>();
    q.offer(root);

    while(!q.isEmpty()){
        int size = q.size();
        List<Integer> innerRes = new ArrayList<>();
        while(size > 0){
            TreeNode t = q.poll();
            innerRes.add(t.val);
            size--;
            if(t.left!=null){
                q.offer(t.left);
            }
            if(t.right!=null){
                q.offer(t.right);
            }
        }
        res.add(innerRes);

    }
    Collections.reverse(res);
    return res;
}
```

#### 199.二叉树的右视图

用size的值判断是不是每一层最后一个结点。

```java
public List<Integer> rightSideView(TreeNode root) {
    Queue<TreeNode> q = new LinkedList<>();
    List<Integer> res = new ArrayList<>();
    if(root == null){
        return res;
    }
    q.offer(root);
    while(!q.isEmpty()){
        int size = q.size();
        while(size > 0){
            TreeNode t = q.poll();
            
            // 核心部分
            if(--size == 0){
                res.add(t.val);
            }
            
            if(t.left!=null){
                q.offer(t.left);
            }
            if(t.right != null){
                q.offer(t.right);
            }
        }
    }
    return res;
}
```

#### 637.二叉树的层平均值

每层取平均值。

```java
public List<Double> averageOfLevels(TreeNode root) {
    List<Double> res = new ArrayList<>();
    if(root == null){
        return res;
    }
    Queue<TreeNode> q = new LinkedList<>();
    q.offer(root);
    while(!q.isEmpty()){
        Double sum = 0.0;
        int size = q.size();
        for(int i = 0; i < size; i++){
            TreeNode t = q.poll();

            // 核心部分
            sum += 1.0 * t.val;

            if(t.left != null){
                q.offer(t.left);
            }
            if(t.right != null){
                q.offer(t.right);
            }
        }
        res.add(sum / size);
    }
    return res;
}
```

#### 429.N叉树的层序遍历

和二叉树本质上没什么区别。

```java
public List<List<Integer>> levelOrder(Node root) {
    List<List<Integer>> res = new ArrayList<List<Integer>>();
    if(root == null){
        return res;
    }
    Queue<Node> q = new LinkedList<>();
    q.offer(root);
    while(!q.isEmpty()){
        int size = q.size();
        List<Integer> innerRes = new ArrayList<>();
        while(size-- > 0){
            Node n = q.poll();
            innerRes.add(n.val);
            List<Node> children = n.children;
            
            // 最好加上这句
            if (children == null || children.size() == 0) {
                    continue;
                }
            
            for(Node child : children){
                if(child!=null){
                    q.offer(child);
                }
            }
        }
        res.add(innerRes);
    }
    return res;
}
```

#### 515.在每个树行中找最大值

```java
public List<Integer> largestValues(TreeNode root) {
    if(root == null){
        return Collections.emptyList();
    }
    List<Integer> result = new ArrayList();
    Queue<TreeNode> queue = new LinkedList();
    queue.offer(root);
    while(!queue.isEmpty()){
        int max = Integer.MIN_VALUE;
        for(int i = queue.size(); i > 0; i--){
            TreeNode node = queue.poll();
            max = Math.max(max, node.val);
            if(node.left != null) queue.offer(node.left);
            if(node.right != null) queue.offer(node.right);
        }
        result.add(max);
    } 
    return result;
}
```

#### 116.填充每个节点的下一个右侧节点指针

```java
public Node connect(Node root) {
    if(root == null) return root;
    Queue<Node> que = new LinkedList<>();
    que.offer(root);
    while(!que.isEmpty()){
        int size = que.size();
        Node p = null;
        Node q = null;
        if(size > 0){
            p = que.poll();
        }
        while(size > 0){
            if(size > 1){
                q = que.poll();
            }else{
                q = null;
            }
            size--;
            p.next = q;

            if(p.left!=null){
                que.offer(p.left);
            }
            if(p.right!=null){
                que.offer(p.right);
            }

            p = q;
        }

    }
    return root;
}
```

#### 117.填充每个节点的下一个右侧节点指针

和上道题代码一样。



#### 104.二叉树的最大深度

##### 方法一：迭代（层序遍历）

```java
public int maxDepth(TreeNode root) {
    if(root == null) return 0;
    int depth = 0;
    Queue<TreeNode> que = new LinkedList<>();
    que.offer(root);
    while(!que.isEmpty()){
        depth++;
        int size = que.size();
        while(size-- > 0){
            TreeNode t = que.poll();
            if(t.left!=null){
                que.offer(t.left);
            }
            if(t.right!=null){
                que.offer(t.right);
            }
        }
    }
    return depth;
}
```

##### 方法二：递归

DAY16再讲。

```java
// 方法二：递归
public int maxDepth(TreeNode root) {
    if(root == null) return 0;
    return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
}
```

#### 111.二叉树的最小深度

##### 方法一：迭代（层序遍历）

```java
public int minDepth(TreeNode root) {
    if(root == null) return 0;
    int depth = 0;
    Queue<TreeNode> que = new LinkedList<>();
    que.offer(root);
    
    // 只要有结点没孩子，就到此为止计算深度
    boolean hasChild = true;
    
    while(!que.isEmpty() && hasChild){
        hasChild = true;
        depth++;
        int size = que.size();
        while(size-- > 0){
            TreeNode t = que.poll();
            
            // 核心部分
            if(t.left!=null){
                que.offer(t.left);
            }
            if(t.right!=null){
                que.offer(t.right);
            }
            if(t.left==null && t.right==null){
                hasChild = false;
                break;
            }
            
        }
    }
    return depth;
}
```

##### 方法二：递归

DAY16再讲。



---

### 226.翻转二叉树

- 标签：二叉树、递归、迭代
- 难度：5.5

只是在二叉树的前序遍历代码上做了点处理。

```java
// 方法一：递归
public TreeNode invertTree(TreeNode root) {
    if(root == null){
        return null;
    }
    
    //处理如下
    swapChildren(root);
    
    invertTree(root.left);
    invertTree(root.right);
    return root;
}

// 方法二：迭代
public TreeNode invertTree(TreeNode root){
    if(root == null) return null;
    Stack<TreeNode> stack = new Stack<>();
    stack.push(root);
    while(!stack.isEmpty()){
        TreeNode t = stack.pop();
        
        //处理如下
        swapChildren(t);
        
        if(t.right!=null){
            stack.push(t.right);
        }
        if(t.left!=null){
            stack.push(t.left);
        }
    }
    return root;
}

public void swapChildren(TreeNode node){
    TreeNode temp = node.left;
    node.left = node.right;
    node.right = temp;
}
```

---

### 101.对称二叉树

- 标签：二叉树、递归、迭代
- 难度：6.0

#### 方法一：递归

递归做法，就是先排除左右有一个空，或左右值不相等的情况，再用后序遍历进行比较。

```java
public boolean isSymmetric(TreeNode root) {
    return compare(root.left, root.right);
}

private boolean compare(TreeNode left, TreeNode right) {

    if (left == null && right != null) {
        return false;
    }
    if (left != null && right == null) {
        return false;
    }

    if (left == null && right == null) {
        return true;
    }
    if (left.val != right.val) {
        return false;
    }
    // 比较外侧
    boolean compareOutside = compare(left.left, right.right);
    // 比较内侧
    boolean compareInside = compare(left.right, right.left);
    return compareOutside && compareInside;
}
```

#### 方法二：迭代

迭代使用双端队列，同时在两端插入左右元素，进行比较。

```java
/**
 * 迭代法
 * 使用双端队列，相当于两个栈
 */
public boolean isSymmetric2(TreeNode root) {
    Deque<TreeNode> deque = new LinkedList<>();
    deque.offerFirst(root.left);
    deque.offerLast(root.right);
    while (!deque.isEmpty()) {
        TreeNode leftNode = deque.pollFirst();
        TreeNode rightNode = deque.pollLast();
        if (leftNode == null && rightNode == null) {
            continue;
        }
        //            if (leftNode == null && rightNode != null) {
        //                return false;
        //            }
        //            if (leftNode != null && rightNode == null) {
        //                return false;
        //            }
        //            if (leftNode.val != rightNode.val) {
        //                return false;
        //            }
        // 以上三个判断条件合并
        if (leftNode == null || rightNode == null || leftNode.val != rightNode.val) {
            return false;
        }
        deque.offerFirst(leftNode.left);
        deque.offerFirst(leftNode.right);
        deque.offerLast(rightNode.right);
        deque.offerLast(rightNode.left);
    }
    return true;
}
```

