# 算法DAY13 | 239.滑动窗口最大值 / 347.前K个高频元素 / 栈与队列总结

[TOC]

### 239.滑动窗口最大值

[题目链接](https://leetcode.cn/problems/sliding-window-maximum/)

- 标签：队列
- 难度：6.5

这道题用暴力解法虽然复杂度也不算高，只有O(kn)，但是会超时。

```java
// 以下解法TLE
public int[] maxSlidingWindow(int[] nums, int k) {
    int left = 0;
    int right = k - 1;
    List<Integer> res = new ArrayList<>();

    while(right < nums.length){
        int max = Integer.MIN_VALUE;
        for(int i = left; i <= right; i++){
            if(nums[i] > max){
                max = nums[i];
            }
        }
        left++;
        right++;
        res.add(max);
    }
    // 流处理，ArrayList转数组
    return res.stream().mapToInt(Integer::intValue).toArray();
}
```



正确的做法是针对<u>每个窗口</u>维护一个**单调队列**。在本题中，单调队列由窗口内元素按照降序排列而来，**后一个元素如果比前一个大，就舍弃前一个元素**。

![13-1](.\img\13-1.gif)

我们需要手动实现一个单调队列，**保证每个窗口的单调队列更新后，队头元素都是这个窗口里最大的元素**。可以选择基于LinkedList的实现，因为LinkedList两边都能增删；当然选择ArrayDeque也可以。

我们手动写单调队列的add和poll方法，目的是在窗口移动的时候能够把新元素add进单调队列，离开窗口的那个元素poll出去。

**poll方法**的逻辑是，如果离开窗口的元素不是单调队列里最大的元素，那就不用从单调队列里把它出队。

**add方法**的逻辑是，新元素从单调队列的末尾开始往前遍历，如果末尾的元素比新元素小，就出队，直到末尾的元素大于新元素，这时把新元素加进去。

这样，窗口移动后的单调队列就更新好了。过程中要把每个窗口内的单调队列的队头元素加入结果集。

```java
// 定义一个单调队列类
class MyQueue {
    // 基于LinkedList实现
    Deque<Integer> deque = new LinkedList<>();
    
    //poll操作：比较当前要弹出的数值是否等于队列出口的数值，如果相等则弹出
    void poll(int val) {
        if (!deque.isEmpty() && val == deque.peek()) {
            deque.poll();
        }
    }
    // push操作：添加元素时，如果要添加的元素大于入口处的元素，就将入口元素弹出
    void add(int val) {
        while (!deque.isEmpty() && val > deque.getLast()) {
            deque.removeLast();
        }
        deque.add(val);
    }
    // peek操作：队列队顶元素始终为最大值
    int peek() {
        return deque.peek();
    }
}

class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        // 别忘了考虑特殊情况
        if (nums.length == 1) {
            return nums;
        }
        int len = nums.length - k + 1;
        // 存放结果元素的数组
        int[] res = new int[len];
        int num = 0;
        // 我的队列
        MyQueue myQueue = new MyQueue();
        // 先将前k的元素放入队列
        for (int i = 0; i < k; i++) {
            myQueue.add(nums[i]);
        }
        res[num++] = myQueue.peek();
        for (int i = k; i < nums.length; i++) {
            // 离开的元素poll
            myQueue.poll(nums[i - k]);
            // 新加入滑动窗口的元素add
            myQueue.add(nums[i]);
            // 记录当前窗口的最大值（也就是单调队列队头元素）
            res[num++] = myQueue.peek();
        }
        return res;
    }
}
```

---



### 347.前K个高频元素

[题目链接]()

- 标签：队列、堆、优先队列
- 难度：6.0

这个题一开始思路很好想，先拿map把每个数出现的频次记录下来。然后我们的需求是根据value排序，取对应的前K个key，这就**需要我们把键值对完整存在某个数据结构里**。这个数据结构还要有排序的功能，**每add进去一个键值对，就能自动把value大的键值对排在前面**，最后输出前K个键值对里面的键就行了。

这个数据结构就可以用**优先队列**实现，底层原理是**大根堆**。

Java集合类中有优先队列的实现——**PriorityQueue，需要传入比较器**，如果不传，就默认是小根堆。题解代码如下：

```java
public int[] topKFrequent(int[] nums, int k) {
    Map<Integer,Integer> map = new HashMap<>();
    for(int elem : nums){
        map.put(elem, map.getOrDefault(elem,0) + 1);
    }

    // 基于大顶堆的优先队列实现，要给构造器传入一个比较器，可以用lambda表达式写
    PriorityQueue<int[]> queue  = new PriorityQueue<>((a,b)->(b[1]-a[1]));

    // map的键值对集合
    Set<Map.Entry<Integer,Integer>> entrySet = map.entrySet();
    // 遍历集合，把key、value构成的数组加进优先队列，优先队列就会按照value对数组排序
    for(Map.Entry<Integer,Integer> entry : entrySet){
        Integer key = entry.getKey();
        Integer value = entry.getValue();
        queue.add(new int[]{key,value});
    }

    // 找排在前k位的数组，输出数组的第一个元素，即key
    int[] res = new int[k];
    for(int i = 0; i < k; i++){
        res[i] = queue.poll()[0];
    }
    return res;
}
```

优先队列的泛型那里，其实**可以直接传入Map.Entry类型**，代码更简洁，也更本质，完全没有必要再把键值对类型改成数组。

```java
Map<Integer,Integer> map = new HashMap<>();
    for(int elem : nums){
        map.put(elem, map.getOrDefault(elem,0) + 1);
    }

    // 基于大顶堆的优先队列实现，要给构造器传入一个比较器，可以用lambda表达式写
	// 这次我们直接传Map.Entry类型，比较器也要稍微改一下
    PriorityQueue<Map.Entry<Integer,Integer>> queue  = new PriorityQueue<>((a,b)->(b.getValue()-a.getValue()));

    // map的键值对集合
    Set<Map.Entry<Integer,Integer>> entrySet = map.entrySet();
    // 遍历集合，直接把键值对加入优先队列，优先队列就会按照value对键值对排序
    for(Map.Entry<Integer,Integer> entry : entrySet){
        queue.add(entry);
    }

    // 找排在前k位的数组，输出键值对的key
    int[] res = new int[k];
    for(int i = 0; i < k; i++){
        res[i] = queue.poll().getKey();
    }
    return res;
```

---

### 栈与队列总结

[点此见卡哥总结。](https://programmercarl.com/%E6%A0%88%E4%B8%8E%E9%98%9F%E5%88%97%E6%80%BB%E7%BB%93.html#%E6%A0%88%E5%9C%A8%E7%B3%BB%E7%BB%9F%E4%B8%AD%E7%9A%84%E5%BA%94%E7%94%A8)

卡哥的总结很棒，尤其是我们做过的几个栈和队列的经典应用，值得反复回顾！

这部分很考查Java集合类的熟练程度，要注意复习。