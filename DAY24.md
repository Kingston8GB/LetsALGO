# 算法DAY24 | 回溯算法理论基础 / 77.组合

[TOC]

### 回溯算法理论基础

回溯算法本质上是暴力递归搜索，因此性能不高。虽然可以添加一些剪枝操作进行优化，但改变不了本质。

**回溯可以用来解决如下问题：**

- 组合：从N个数中找到k个符合要求的数
- 切割：一个字符串能有几种符合条件的切割方式
- 子集：N个数中能有几种符合条件的子集
- 排列：N个数全排列，能有几种符合条件的排列方式
- 棋盘：N皇后、数独

**很重要的思想：**

- 集合的大小 → 树的宽度（用for循环横向遍历）
- 递归的深度 → 树的深度（用递归纵向遍历

**回溯法模板：**

```java
void backtracking(){
	if(终止条件){
        存放结果;
        return;
    }
    
    for(选择本层集合中元素){
        处理节点;
        backtracking();
        回溯，撤销处理;
    }   
}
```



### 77.组合

- 标签：递归、回溯
- 难度：7.0

面对N个数选择k个数的组合的问题时，暴力的解法需要开k个for循环，每层循环分别取一个数。

这样不一定不行，但是在k变化时代码都写不出来。

递归就是嵌套调用，如果递归函数里有for循环，递归k层就可以实现开k个循环的效果。

```java
List<List<Integer>> res = new ArrayList<>();
List<Integer> path = new ArrayList<>();

public List<List<Integer>> combine(int n, int k) {
    backtracking(n,k,1);
    return res;
}

public void backtracking(int n, int k, int startIndex){
    // 递归终止条件，当纵向遍历达到一定深度（用path数组大小衡量），就把path加入结果集并返回
    if(path.size() == k){
        // 这个地方一定要重新new ArrayList，不然res里的元素指向同一个path
        res.add(new ArrayList<>(path));
        return;
    }

    // 横向遍历集合，但是不是从0开始，而是从startIndex，因为path不能重复
    // 剪枝优化：i最多只用遍历到，即使把集合里i后面所有元素都加入path，size也达不到k了
    for(int i = startIndex; i <= (n - (k-path.size()) + 1); i++){
        path.add(i);
        // 纵向遍历，往path里加元素，把下次加入开始下标设置为i+1
        backtracking(n,k,i+1);
        path.remove(path.size() - 1);
    }
}
```

可以看出，横向遍历指的是从集合里选取1,2,3,4,...，纵向遍历指的是往path里添加。

注意，在横向遍历的时候，做了一个**剪枝优化**，当把剩下所有元素加入path也不够长，就不需要横向遍历下去了。

从树形结构上可以清晰地看出，“剪枝”这个说法很形象！