# 算法DAY11| 20.有效的括号 / 1047.删除字符串中的所有相邻重复项 / 150.逆波兰表达式求值

[TOC]

### 20.有效的括号

[题目链接](https://leetcode.cn/problems/valid-parentheses/)

- 标签：栈
- 难度：4.5

栈最基本的应用。**遇到左括号就进栈，遇到右括号时，以下情况直接返回false**：

1. **栈空**，就不可能有左括号和他匹配；
2. pop出来的**左括号和右括号不匹配**，比如pop出来了“(”，但是右括号是“]”。

此外，字符串遍历完了之后，**如果栈中还有元素，也要return false**，别忘了。

```java
public boolean isValid(String s) {
    Stack<Character> stack  = new Stack<>();
    for(int i = 0; i < s.length(); i++){
        char c = s.charAt(i);
        if(c == '(' || c == '[' || c == '{'){
            stack.push(c);
        }else{
            if(stack.isEmpty()) return false;
            char top = stack.pop();
            if(top == '(' && c != ')') return false;
            if(top == '[' && c != ']') return false;
            if(top == '{' && c != '}') return false;
        }
    }
    return stack.isEmpty();
}
```

还有一种思路，就是**入栈的时候不入左括号，而是入它对应的右括号**，这样可以把检查是否匹配变成检查右括号和左括号是否相等。

---

### 1047.删除字符串中的所有相邻重复项

[题目链接](https://leetcode.cn/problems/remove-all-adjacent-duplicates-in-string/)

- 标签：栈
- 难度：4.5

用栈实现祖玛的功能。

借用了一下StringBuilder类的reverse()方法。

```java
public String removeDuplicates(String s) {
    Stack<Character> stack = new Stack<>();
    for(int i = 0; i < s.length(); i++){
        if(stack.isEmpty() || s.charAt(i)!=stack.peek()){
            stack.push(s.charAt(i));
        }else{
            stack.pop();
        }
    }
    StringBuilder sb = new StringBuilder();
    while(!stack.isEmpty()){
        sb.append(stack.pop());
    }
    sb.reverse();
    return sb.toString();
}
```

---

### 150.逆波兰表达式求值

[题目链接](https://leetcode.cn/problems/evaluate-reverse-polish-notation/)

- 标签：栈
- 难度：4.5

后缀表达式求值，用栈实现，遇到数就进栈，遇到运算符就出栈两个数做运算，再把运算结果压入栈中。

![11-1](.\img\11-1.gif)

栈元素的类型可以是String，也可以是Integer，用好String.valueOf()和Integer.parseInt()这两个方法做转换。

```java
public int evalRPN(String[] tokens) {
    Stack<String> stack = new Stack<>();
    for(String token : tokens){
        int num1 = 0;
        int num2 = 0;
        // 如果是运算符，就出栈两个数
        // ps：这个地方最好是把字面值常量放在.equals的前面，防止NPE
        if(token.equals("+") || token.equals("-") || token.equals("*") || token.equals("/")){
            num2 = Integer.parseInt(stack.pop());
            num1 = Integer.parseInt(stack.pop());
        }
        // 如果是加法符号，就把这两个数求和的结果压入栈中，下同
        if(token.equals("+")){
            stack.push(String.valueOf(num1 + num2));
        }else if(token.equals("-")){
            stack.push(String.valueOf(num1 - num2));
        }else if(token.equals("*")){
            stack.push(String.valueOf(num1 * num2));
        }else if(token.equals("/")){
            stack.push(String.valueOf(num1 / num2));
        // 如果是数，就直接进栈
        }else{
            stack.push(token);
        }
    }
    // 返回栈中唯一剩的那个元素
    return Integer.parseInt(stack.peek());
}
```

这道题细节上写法有很多，思路都是一样的。