[TOC]

## 有效的括号（0020）

> *给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。*
>
> *输入: "()[]{}"*
>
> *输出: true*

```java
class Solution {
    public boolean isValid(String s) {
        if (s.length() % 2 != 0) {return false;}
        Map<Character, Character> pairs = new HashMap<Character, Character>() {{
            put(')', '(');
            put(']', '[');
            put('}', '{');
        }};

        Stack<Character> stack = new Stack<>();

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (pairs.containsKey(c)) {
                if (stack.isEmpty() || stack.peek() != pairs.get(c)) {
                    return false;
                }
                stack.pop();
            } else {
                stack.push(c);
            }
        }
        return stack.isEmpty();
    }
}
```

## 柱状图中最大的矩形（0084）

> *给定 n 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。*
>
> *求在该柱状图中，能够勾勒出来的矩形的最大面积。*

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int n = heights.length;
        int[] tmp = new int[n + 2];
        System.arraycopy(heights, 0, tmp, 1, n);

        Stack<Integer> stack = new Stack<>();
        int ans = 0;
        for (int i = 0; i < n + 2; i++) {
            while (!stack.isEmpty() && tmp[i] < tmp[stack.peek()]) {
                int h = tmp[stack.pop()];
                ans = Math.max(ans, (i - stack.peek() - 1) * h);
            }
            stack.push(i);
        }

        return ans;
    }
}
```

## 逆波兰表达式求值（0150）

> *根据 逆波兰表示法，求表达式的值。*
>
> *有效的运算符包括 +, -, \*, / 。每个运算对象可以是整数，也可以是另一个逆波兰表达式。*
>
> *输入: ["2", "1", "+", "3", "\*"]*
>
> *输出: 9*
>
> *解释: 该算式转化为常见的中缀算术表达式为：((2 + 1) \* 3) = 9*

```java
class Solution {
    public int evalRPN(String[] tokens) {
        Stack<Integer> stack = new Stack<>();
        int num1, num2;
        for (String s : tokens) {
            switch(s) {
                case "+":
                    num2 = stack.pop();
                    num1 = stack.pop();
                    stack.push(num1 + num2);
                    break;
                case "-":
                    num2 = stack.pop();
                    num1 = stack.pop();
                    stack.push(num1 - num2);
                    break;
                case "*":
                    num2 = stack.pop();
                    num1 = stack.pop();
                    stack.push(num1 * num2);
                    break;
                case "/":
                    num2 = stack.pop();
                    num1 = stack.pop();
                    stack.push(num1 / num2);
                    break;
                default:
                    stack.push(Integer.valueOf(s));
                    break;
            }
        }
        return stack.pop();
    }
}
```

## 计算器

> *实现一个基本的计算器来计算一个简单的字符串表达式 s 的值。*

```java
// + - * / ()
class Solution {
    public int calculate(String s) {
        Deque<Character> deque = new LinkedList<>();
        for (int i = 0; i < s.length(); i++) {
            deque.offerLast(s.charAt(i));
        }
        return myMethod(deque);
    }

    private static int myMethod(Deque<Character> deque) {
        Stack<Integer> stack = new Stack<>();

        char sign = '+';
        int num = 0;
        while (deque.size() > 0) {
            char c = deque.pollFirst();
            if (Character.isDigit(c)) {
                num = 10 * num + (c - '0');
            }
            if (c == '(') {
                num = myMethod(deque);
            }

            if ((!Character.isDigit(c) && c !=' ') || deque.size() == 0) {
                if (sign == '+') {stack.push(num);}
                else if (sign == '-') {stack.push(-num);}
                else if (sign == '*') {stack.push(stack.pop() * num);}
                else if (sign == '/') {stack.push(stack.pop() / num);}

                num = 0;
                sign = c;
            }

            if (c == ')') {break;}
        }

        int ans = 0;
        for (int n : stack) {
            ans += n;
        }
        return ans;
    }
}
```

## 移掉K位数字（0402）

> *给定一个以字符串表示的非负整数 num，移除这个数中的 k 位数字，使得剩下的数字最小。*

```java
class Solution {
    public String removeKdigits(String num, int k) {
        Deque<Character> deque = new LinkedList<>();
        for (int i = 0; i < num.length(); i++) {
            char c = num.charAt(i);
            while (!deque.isEmpty() && k > 0 && deque.peekLast() > c) {
                deque.pollLast();
                k--;
            }
            deque.offerLast(c);
        }

        for (int i = 0; i < k; i++) {
            deque.pollLast();
        }

        StringBuilder ans = new StringBuilder();
        boolean leadingZero = true;
        while (!deque.isEmpty()) {
            char curNum = deque.pollFirst();
            if (leadingZero && curNum == '0') {continue;}
            leadingZero = false;
            ans.append(curNum);
        }

        return ans.length() == 0 ? "0" : ans.toString();
    }
}
```

## 132模式（0456）

```java
class Solution {
    public boolean find132pattern(int[] nums) {
        int n = nums.length;
        int[] curMin = new int[n];
        curMin[0] = nums[0];

        for (int i = 1; i < n; i++) {curMin[i] = Math.min(curMin[i - 1], nums[i]);}
        Stack<Integer> stack = new Stack<>();

        for (int i = n - 1; i >= 0; i--) {
            while (!stack.isEmpty() && stack.peek() <= curMin[i]) {stack.pop();}
            if (!stack.isEmpty() && nums[i] > stack.peek()) {return true;}
            stack.push(nums[i]);
        }

        return false;
    }
}
```

