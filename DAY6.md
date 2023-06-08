# 算法DAY6 | 哈希表理论基础 / 242.有效的字母异位词 / 349.两个数组的交集 / 202.快乐数 / 1.两数之和

[TOC]



### 哈希表理论基础

![6-1](.\img\6-1.png)

**一般哈希表都是用来快速判断一个元素是否出现集合里。**

哈希法也是**牺牲了空间换取了时间**，因为我们要使用额外的数组，set或者是map来存放数据，才能实现快速的查找。



### 242.有效的字母异位词

[题目链接](https://leetcode.cn/problems/valid-anagram/)

- 标签：哈希表
- 难度：4.0

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        int[] record = new int[26];

        for(int i = 0; i < s.length(); i++){
            record[s.charAt(i) - 'a']++;
        }

        for(int j = 0; j < t.length(); j++){
            record[t.charAt(j) - 'a']--;
        }

        for(int k = 0; k < record.length; k++){
            if(record[k] != 0){
                return false;
            }
        }
        return true;
    }
}
```

没啥可说的，很简单。



### 349.两个数组的交集

[题目链接](https://leetcode.cn/problems/intersection-of-two-arrays/)

- 标签：哈希表
- 难度：5.0

上道题的题解没用集合类，所以这道题我一开始也没用，然后就缝缝补补地写出了如下代码。

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        int[] count = new int[1001];
        for(int elem1 : nums1){
            count[elem1]++;
        }
        
        int cnt = 0;
        int[] temp = new int[1001];
        for(int elem2 : nums2){
            if(count[elem2]>0){
                temp[elem2] = 1;
                cnt++;
                count[elem2] = -1;
            }
        }
        int[] res = new int[cnt];
        int p = 0;
        
        for(int i = 0; i < temp.length; i++){
            if(temp[i]>0){
                res[p++] = i;
            }
        }
        return res;
    }
}
```

以下是用了集合类的题解

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        if (nums1 == null || nums1.length == 0 || nums2 == null || nums2.length == 0) {
            return new int[0];
        }
        Set<Integer> set1 = new HashSet<>();
        Set<Integer> resSet = new HashSet<>();
        //遍历数组1
        for (int i : nums1) {
            set1.add(i);
        }
        //遍历数组2的过程中判断哈希表中是否存在该元素
        for (int i : nums2) {
            if (set1.contains(i)) {
                resSet.add(i);
            }
        }
        //将结果几何转为数组，用的是stream流处理
        return resSet.stream().mapToInt(x -> x).toArray();
    }
}
```



### 202.快乐数

[题目链接](https://leetcode.cn/problems/happy-number/)

- 标签：哈希表
- 难度：5.0

抓住题干说的”无限循环“，这就是退出循环的条件。

```java
class Solution {
    public boolean isHappy(int n) {
        Set<Integer> set = new HashSet();
        while(n!=1 && !set.contains(n)){
            set.add(n);
            n = getNextNumber(n);
        }
        return n==1;
    }

    public Integer getNextNumber(Integer n){
        int sum = 0;
        while(n!=0){
            sum += (n % 10) * (n % 10);
            n /= 10;
        }
        return sum;
    }
}
```



### 1.两数之和

[题目链接](https://leetcode.cn/problems/two-sum/)

- 标签：哈希表
- 难度：5.5

力扣第一题，是不能用暴力解法的。

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer,Integer> map = new HashMap();

        int[] res = new int[2];
        if(nums == null || nums.length == 0){
            return res;
        }

        for(int i = 0; i<nums.length; i++){
            if(map.containsKey(target - nums[i])){
                res[0] = i;
                res[1] = map.get(target-nums[i]);
                return res;
            }
            map.put(nums[i],i);
        }
        return res;
    }
}
```

