# 算法DAY1 | 数组理论基础 / 704.二分查找 / 27.移除元素

### 数组理论基础

数组是再熟悉不过的数据结构了。关于数组的算法题主要考查代码能力。

数组在计算机内存中一般顺序存储，根据一个下标索引获取到对应数据。**内存地址顺序增长，下标索引也顺序增长。**



![1-1](F:\2022\代码随想录\img\1-1.png)

数组的增删改查需要移动元素，时间复杂度O(n)。

二维数组需要强调的是，**在Java中，每一行的头结点地址是不确定的**，由JVM随机分配；C++中，二维数组每一行的存储仍然连续。

![1-2](F:\2022\代码随想录\img\1-2.png)



### 704.二分查找

[题目链接](https://leetcode.cn/problems/binary-search/)

- 标签：数组、二分法
- 难度：4.0

> 给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。
>
>
> 示例 1:
>
> ```java
> 输入: nums = [-1,0,3,5,9,12], target = 9
> 输出: 4
> 解释: 9 出现在 nums 中并且下标为 4
> ```
>
>
> 示例 2:
>
> ```java
> 输入: nums = [-1,0,3,5,9,12], target = 2
> 输出: -1
> 解释: 2 不存在 nums 中因此返回 -1
> ```
>
>
> 提示：
>
> 你可以假设 nums 中的所有元素是不重复的。
> n 将在 [1, 10000]之间。
> nums 的每个元素都将在 [-9999, 9999]之间。



二分查找（binary search）是一种有序数组的查找方法，可以将时间复杂度从O(n)降到O(logn)。**使用普通二分法的前提是：有序数组且无重复元素**。

二分查找最恶心的地方在于区间的边界条件不好写，比如是＜还是≤，有时候会弄混。区间的定义是不变量，需要在while寻找中每一次边界的处理都要坚持根据区间的定义来操作，这就是**循环不变量**规则。

二分法整体很简单，就不贴图了，看代码脑补。



#### 方法一：迭代（左闭右闭）

```java
public int search(int[] nums, int target) {
    // 健壮性，很重要
    if (target < nums[0] || target > nums[nums.length - 1]) {
        return -1;
    }

    int left = 0;
    // 因为[left,right]是左闭右闭区间，这里右端点取最后一个元素
    int right = nums.length - 1;
    int mid;

    // 因为是左闭右闭区间，所以left==right是有意义的
    while(left <= right){
        mid = left + ((right - left) >> 1);
        if(nums[mid] < target){
            left = mid + 1;
        }else if(nums[mid] > target){
            right = mid - 1;
        }else{
            return mid;
        }
    }
    return -1;
}
```

#### 方法二：迭代（左闭右开）

单独给左闭右开记作一种方法，主要区别有三点，以作强调。

```java
// 解法二：迭代（左闭右开）
public int search(int[] nums, int target) {
    // 健壮性，很重要
    if (target < nums[0] || target > nums[nums.length - 1]) {
        return -1;
    }

    int left = 0;
    // 因为[left,right)是左闭右开区间，这里右端点取最后一个元素+1
    int right = nums.length;
    int mid;

    // 那么这里left==right就没意义了，就可以退出循环了
    while(left < right){
        mid = left + ((right - left) >> 1);
        if(nums[mid] < target){
            left = mid + 1;
        }else if(nums[mid] > target){
            // 令right为新的右端点，注意左闭右开这里是mid不是mid-1
            right = mid;
        }else{
            return mid;
        }
    }
    return -1;
}
```

#### 方法三：递归

仅供思路参考。

```java
// 解法三：递归（左闭右闭）
public int search(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    return searchBinary(nums, left, right, target);
}

public int searchBinary(int[] nums, int left, int right, int target){
    int res = -1;
    int mid = (left + right) / 2;
    
    // 退出条件
    if(nums[mid] == target) return mid;
    
    if(left <= right){
        if(nums[mid] > target){
            res = searchBinary(nums, left, right - 1, target);
        }else if(nums[mid] < target){
            res = searchBinary(nums, left + 1, right, target);
        }
    }
    return res;
}
```



### 27.移除元素

[题目链接](https://leetcode.cn/problems/remove-element/)

- 标签：数组、双指针
- 难度：5.0

> 给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。
>
> 不要使用额外的数组空间，你必须仅使用 **O(1) 额外空间**并 原地 修改输入数组。
>
> 元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。
>
> 示例：
>
> ```java
> 输入：nums = [3,2,2,3], val = 3
> 输出：2, nums = [2,2]
> 解释：函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。你不需要考虑数组中超出新长度后面的元素。例如，函数返回的新长度为 2 ，而 nums = [2,2,3,3] 或 nums = [2,2,0,0]，也会被视作正确答案。
>     
> 输入：nums = [0,1,2,2,3,0,4,2], val = 2
> 输出：5, nums = [0,1,4,0,3]
> 解释：函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。注意这五个元素可为任意顺序。你不需要考虑数组中超出新长度后面的元素。
> ```



数组的移除比链表更难，因为需要移动后面的元素；而本题要求空间复杂度为O(1)，则不可以开辟新的数组用于存储数据。

本题采用快慢指针，本质上是遍历两遍：**快指针跑得快，在前面找要留下的元素，跳过值为val的元素；慢指针跑得慢，快指针每找到一个元素，他就在后面按顺序填坑**。

记住并理解下面的代码思想，只有七八行，非常巧妙。

```java
public int removeElement(int[] nums, int val) {
    // 初始化慢指针
    int slowIndex = 0;
    // 移动快指针
    for(int fastIndex = 0; fastIndex < nums.length; fastIndex++){
        // 如果快指针当前遍历到的是  值为val的元素，快指针会跳过，慢指针不动
        // 如果快指针当前遍历到的不是值为val的元素，慢指针会把这个元素在后面按顺序填坑
        if(val != nums[fastIndex]){
            nums[slowIndex++] = nums[fastIndex];
        }
    }
    // 慢指针最后一轮++后，值刚好是数组长度，直接return
    return slowIndex;
}
```