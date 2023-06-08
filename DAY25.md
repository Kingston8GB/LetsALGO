# 算法DAY25 | 216.组合总和III /  17.电话号码的字母组合

[TOC]

### 216.组合总和III

- 标签：递归、回溯
- 难度：7.5

上题：从N个数中找K个数，有多少种组合？

本题：从9个数中找K个和为n的数，有多少种组合？

思路还是一样的，横向遍历指的是从1遍历到9，纵向遍历是指往path数组里加元素，直到总和达到n，保存这个path。所以要有个变量，保存path数组里的总和，与path数组一起同递归同回溯。

剪枝在上一题的基础上，还可以在sum已经大于n的时候，直接return。

```java
List<List<Integer>> res = new ArrayList<>();
List<Integer> path = new ArrayList<>();

public List<List<Integer>> combinationSum3(int k, int n) {
    backtracking(n,k,1,0);
    return res; 
}

public void backtracking(int targetSum, int k, int startIndex, int sum){
    if(path.size() == k){
        if(sum == targetSum) res.add(new ArrayList<>(path));
        return;
    }
    if(sum > targetSum){
        return;
    }
    for(int i = startIndex; i <= (9 - (k - path.size()) + 1); i++){
        sum += i;
        path.add(i);

        backtracking(targetSum,k,i+1,sum);
        sum -= i;
        path.remove(path.size() - 1);
    }
}
```



### 17.电话号码的字母组合

- 标签：递归、回溯
- 难度：8.0

同样是求组合题，思路还是一样的，但本题要关注几个细节：

- 用什么保存数字和字母的映射关系
- 本题横向遍历是在遍历什么？纵向遍历又在遍历什么？
- 输入0和1怎么办？

```java
List<String> res = new ArrayList<>();
StringBuilder sb = new StringBuilder();

public List<String> letterCombinations(String digits) {
    if(digits == null || digits.length() == 0){
        return res;
    }
    
    // 数字到对应字母的映射表
    String[] numStrings = {"", "","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"};
    backtracking(numStrings,digits,0);
    return res;
}

// 回溯函数传入映射表、电话号码和当前遍历位置
public void backtracking(String[] numStrings, String digits, int index){
    // 递归终止条件
    if(index == digits.length()){
        res.add(sb.toString());
        return;
    }

    // 找到数字对应的字母
    String curNumString = numStrings[digits.charAt(index) - '0'];
    // 横向遍历这些字母
    for(int i = 0; i < curNumString.length(); i++){
        // 加到path里
        sb.append(curNumString.charAt(i));
        // 递归
        backtracking(numStrings,digits,index+1);
        // 回溯
        sb.deleteCharAt(sb.length() - 1);
    }
}
```
