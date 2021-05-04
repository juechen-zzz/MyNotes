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

## 有效的括号字符串（0678）

> *给定一个只包含三种字符的字符串：（ ，） 和 \*，写一个函数来检验这个字符串是否为有效字符串。有效字符串具有如下规则：*
>
> *任何左括号 ( 必须有相应的右括号 )。*
>
> *任何右括号 ) 必须有相应的左括号 ( 。*
>
> *左括号 ( 必须在对应的右括号之前 )。*
>
> ** 可以被视为单个右括号 ) ，或单个左括号 ( ，或一个空字符串。*
>
> *一个空字符串也被视为有效字符串。*

```java
// solution 1
class Solution {
    public boolean checkValidString(String s) {
        int left = 0, right = 0, n = s.length();
        for (int i = 0; i < n; i++) {
            left += (s.charAt(i) == ')') ? -1 : 1;
            right += (s.charAt(n - i - 1) == '(') ? -1 : 1;
            if (left < 0 || right < 0) {
                return false;
            }
        }
        return true;
    }
}

// solution 2 stack
class Solution {
    public boolean checkValidString(String s) {
        int n = s.length();
        Stack<Integer> left = new Stack<>();
        Stack<Integer> star = new Stack<>();

        for (int i = 0; i < n; i++) {
            char c = s.charAt(i);
            if (c == '(') {left.push(i);}
            else if (c == '*') {star.push(i);}
            else {
                if (!left.isEmpty()) {left.pop();}
                else if (!star.isEmpty()) {star.pop();}
                else {return false;}
            }
        }

        if (left.size() > star.size()) {return false;}
        else {
            while (!left.isEmpty() && !star.isEmpty()) {
                if (left.peek() > star.peek()) {return false;}
                star.pop();
                left.pop();
            }
            return true;
        }
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

## 下一个更大元素

> 给你两个 没有重复元素 的数组 nums1 和 nums2 ，其中nums1 是 nums2 的子集。
>
> 请你找出 nums1 中每个元素在 nums2 中的下一个比其大的值。
>
> nums1 中数字 x 的下一个更大元素是指 x 在 nums2 中对应位置的右边的第一个比 x 大的元素。如果不存在，对应位置输出 -1 。

```java
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        int len1 = nums1.length;
        int len2 = nums2.length;

        Stack<Integer> stack = new Stack<>();
        Map<Integer, Integer> map = new HashMap<>();
        // 先处理 nums2，把对应关系存入哈希表
        for (int i = 0; i < len2; i++) {
            while (!stack.isEmpty() && stack.peek() < nums2[i]) {
                map.put(stack.pop(), nums2[i]);
            }
            stack.push(nums2[i]);
        }

        // 遍历 nums1 得到结果集
        int[] ans = new int[len1];
        for (int i = 0; i < len1; i++) {
            ans[i] = map.getOrDefault(nums1[i], -1);
        }
        return ans;
    }
}
```

> 给定一个循环数组（最后一个元素的下一个元素是数组的第一个元素），输出每个元素的下一个更大元素。数字 x 的下一个更大的元素是按数组遍历顺序，这个数字之后的第一个比它更大的数，这意味着你应该循环地搜索它的下一个更大的数。如果不存在，则输出 -1。

```java
class Solution {
    public int[] nextGreaterElements(int[] nums) {
        int n = nums.length;
        int[] ans = new int[n];
        Arrays.fill(ans, -1);

        Stack<Integer> stack = new Stack<>();
        for (int i = 0; i < n * 2 - 1; i++) {
            while (!stack.isEmpty() && nums[stack.peek()] < nums[i % n]) {
                ans[stack.pop()] = nums[i % n];
            }
            stack.push(i % n);
        }

        return ans;
    }
}
```

> *给你一个正整数 n ，请你找出符合条件的最小整数，其由重新排列 n 中存在的每位数字组成，并且其值大于 n 。如果不存在这样的正整数，则返回 -1 。*
>
> *注意 ，返回的整数应当是一个 32 位整数 ，如果存在满足题意的答案，但不是 32 位整数 ，同样返回 -1 。*

```java
class Solution {
    public int nextGreaterElement(int n) {
        char[] nums = ("" + n).toCharArray();
        int left = nums.length - 2;
        while (left >= 0 && nums[left] >= nums[left + 1]) {left--;}
        if (left < 0) {return -1;}

        int right = nums.length - 1;
        while (right >= 0 && nums[left] >= nums[right]) {right--;}

        swap(nums, left, right);
        reverse(nums, left + 1);

        try {
            return Integer.parseInt(new String(nums));
        } catch (Exception e) {
            return -1;
        }
    }

    private void swap(char[] nums, int left, int right) {
        char tmp = nums[left];
        nums[left] = nums[right];
        nums[right] = tmp;
    }

    private void reverse(char[] nums, int start) {
        int left = start, right = nums.length - 1;
        while (left < right) {
            swap(nums, left, right);
            left++;
            right--;
        }
    }
}
```

## 最短无序连续子数组（0581）

> *给你一个整数数组 nums ，你需要找出一个 连续子数组 ，如果对这个子数组进行升序排序，那么整个数组都会变为升序排序。*
>
> *请你找出符合题意的 最短 子数组，并输出它的长度。*

```java
// sort
class Solution {
    public int findUnsortedSubarray(int[] nums) {
        int[] nums2 = nums.clone();
        Arrays.sort(nums2);
        int left = nums.length, right = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != nums2[i]) {
                left = Math.min(left, i);
                right = Math.max(right, i);
            }
        }
        return (right - left >= 0 ? right - left + 1 : 0);
    }
}

// stack
class Solution {
    public int findUnsortedSubarray(int[] nums) {
        Stack<Integer> stack = new Stack<>();

        int left = nums.length, right = 0;
        for (int i = 0; i < nums.length; i++) {
            while (!stack.isEmpty() && nums[stack.peek()] > nums[i]) {
                left = Math.min(left, stack.pop());
            }
            stack.push(i);
        }
        stack.clear();

        for (int i = nums.length - 1; i >= 0; i--) {
            while (!stack.isEmpty() && nums[stack.peek()] < nums[i]) {
                right = Math.max(right, stack.pop());
            }
            stack.push(i);
        }

        return right - left > 0 ? right - left + 1 : 0;
    }
}
```

## 函数的独占时间（0636）

> *给出一个非抢占单线程CPU的 n 个函数运行日志，找到函数的独占时间。*
>
> *每个函数都有一个唯一的 Id，从 0 到 n-1，函数可能会递归调用或者被其他函数调用。*
>
> *日志是具有以下格式的字符串：function_id：start_or_end：timestamp。例如："0:start:0" 表示函数 0 从 0 时刻开始运行。"0:end:0" 表示函数 0 在 0 时刻结束。*
>
> *函数的独占时间定义是在该方法中花费的时间，调用其他函数花费的时间不算该函数的独占时间。你需要根据函数的 Id 有序地返回每个函数的独占时间。*

```java
class Solution {
    public int[] exclusiveTime(int n, List<String> logs) {
        Stack<Integer> stack = new Stack<>();
        int[] ans = new int[n];
        String[] s = logs.get(0).split(":");

        stack.push(Integer.parseInt(s[0]));
        int pre = Integer.parseInt(s[2]);
        for (int i = 1; i < logs.size(); i++) {
            s = logs.get(i).split(":");
            if (s[1].equals("start")) {
                if (!stack.isEmpty()) {
                    ans[stack.peek()] += Integer.parseInt(s[2]) - pre;
                }
                stack.push(Integer.parseInt(s[0]));
                pre = Integer.parseInt(s[2]);
            }
            else {
                ans[stack.peek()] += Integer.parseInt(s[2]) - pre + 1;
                stack.pop();
                pre = Integer.parseInt(s[2]) + 1;
            }
        }

        return ans;
    }
}
```

## 棒球比赛（0682）

> *你现在是一场采用特殊赛制棒球比赛的记录员。这场比赛由若干回合组成，过去几回合的得分可能会影响以后几回合的得分。*
>
> *比赛开始时，记录是空白的。你会得到一个记录操作的字符串列表 ops，其中 ops[i] 是你需要记录的第 i 项操作，ops 遵循下述规则：*
>
> *整数 x - 表示本回合新获得分数 x*
>
> *"+" - 表示本回合新获得的得分是前两次得分的总和。题目数据保证记录此操作时前面总是存在两个有效的分数。*
>
> *"D" - 表示本回合新获得的得分是前一次得分的两倍。题目数据保证记录此操作时前面总是存在一个有效的分数。*
>
> *"C" - 表示前一次得分无效，将其从记录中移除。题目数据保证记录此操作时前面总是存在一个有效的分数。*
>
> *请你返回记录中所有得分的总和。*

```java
class Solution {
    public int calPoints(String[] ops) {
        Stack<Integer> stack = new Stack<>();

        for(String op : ops) {
            if (op.equals("+")) {
                int top = stack.pop();
                int newtop = top + stack.peek();
                stack.push(top);
                stack.push(newtop);
            } else if (op.equals("C")) {
                stack.pop();
            } else if (op.equals("D")) {
                stack.push(2 * stack.peek());
            } else {
                stack.push(Integer.valueOf(op));
            }
        }

        int ans = 0;
        for(int score : stack) {ans += score;}
        return ans;
    }
}
```

