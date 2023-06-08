# 算法DAY27 | 39. 组合总和 / 40.组合总和II / 131.分割回文串

[TOC]

### 39. 组合总和

- 标签：递归、回溯
- 难度：7.5

上上题：从N个数中找K个数，有多少种组合？

上题：从9个数中找K个和为n的数，有多少种组合？

本题：从N个数中找不限个和为n的数，有多少种组合？

而且本题允许数重复，需要改动递归调用处，不需要再传入i+1，而是传入i。



什么时候用startIndex，什么时候不用？

- 像上题一样，从不同的集合里取数，就不需要startIndex
- 其他题都是从相同集合中取组合，需要startIndex

```java
List<List<Integer>> res = new ArrayList<>();
List<Integer> path = new ArrayList<>();

public List<List<Integer>> combinationSum(int[] candidates, int target) {
    backTracking(candidates,target,0,0);
    return res;
}

public void backTracking(int[] candidates, int target,int startIndex, int sum){
    if(sum > target) return;
    if(sum == target){
        res.add(new ArrayList<>(path));
        return;
    }
    for(int i = startIndex; i < candidates.length; i++){
        sum += candidates[i];
        path.add(candidates[i]);
        backTracking(candidates,target,i,sum);
        sum -= candidates[i];
        path.remove(path.size()-1);
    }
}
```



### 40.组合总和II

- 标签：递归、回溯
- 难度：8.0

本题涉及去重。

```java
// 注释待完善

LinkedList<Integer> path = new LinkedList<>();
List<List<Integer>> ans = new ArrayList<>();
boolean[] used;
int sum = 0;

public List<List<Integer>> combinationSum2(int[] candidates, int target) {
    used = new boolean[candidates.length];
    // 加标志数组，用来辅助判断同层节点是否已经遍历
    Arrays.fill(used, false);
    // 为了将重复的数字都放到一起，所以先进行排序
    Arrays.sort(candidates);
    backTracking(candidates, target, 0);
    return ans;
}

private void backTracking(int[] candidates, int target, int startIndex) {
    if (sum == target) {
        ans.add(new ArrayList(path));
    }
    for (int i = startIndex; i < candidates.length; i++) {
        if (sum + candidates[i] > target) {
            break;
        }
        // 出现重复节点，同层的第一个节点已经被访问过，所以直接跳过
        if (i > 0 && candidates[i] == candidates[i - 1] && !used[i - 1]) {
            continue;
        }
        used[i] = true;
        sum += candidates[i];
        path.add(candidates[i]);
        // 每个节点仅能选择一次，所以从下一位开始
        backTracking(candidates, target, i + 1);
        used[i] = false;
        sum -= candidates[i];
        path.removeLast();
    }
}
```

### 131.分割回文串

- 标签：递归、回溯
- 难度：8.0

分割问题也可采用回溯法。

```java
// 注释待完善

List<List<String>> lists = new ArrayList<>();
Deque<String> deque = new LinkedList<>();

public List<List<String>> partition(String s) {
    backTracking(s, 0);
    return lists;
}

private void backTracking(String s, int startIndex) {
    //如果起始位置大于s的大小，说明找到了一组分割方案
    if (startIndex >= s.length()) {
        lists.add(new ArrayList(deque));
        return;
    }
    for (int i = startIndex; i < s.length(); i++) {
        //如果是回文子串，则记录
        if (isPalindrome(s, startIndex, i)) {
            String str = s.substring(startIndex, i + 1);
            deque.addLast(str);
        } else {
            continue;
        }
        //起始位置后移，保证不重复
        backTracking(s, i + 1);
        deque.removeLast();
    }
}
//判断是否是回文串
private boolean isPalindrome(String s, int startIndex, int end) {
    for (int i = startIndex, j = end; i < j; i++, j--) {
        if (s.charAt(i) != s.charAt(j)) {
            return false;
        }
    }
    return true;
}
```