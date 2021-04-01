[TOC]

## 无重复字符的最长子串（0003）

> *给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。*
>
> *输入: s = "abcabcbb"*
>
> *输出: 3* 
>
> *解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。*

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        HashMap<Character, Integer> window = new HashMap<>();

        int left = 0, right = 0;
        int ans = 0;

        while (right < s.length()){
            char c = s.charAt(right);
            right++;
            window.put(c, window.getOrDefault(c, 0) + 1);
            while (window.get(c) > 1){
                char d = s.charAt(left);
                left++;
                window.put(d, window.getOrDefault(d, 0) - 1);
            }
            ans = Math.max(ans, right - left);
        }
        return ans;
    }
}
```

## 最长回文子串（0005）      

> *给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。*
>
> *输入: "babad"*
>
> *输出: "bab"*
>
> *注意: "aba" 也是一个有效答案。*

```java
// DP
class Solution {
    public String longestPalindrome(String s) {
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        String ans = "";
        for (int i = n - 1; i >= 0; i--){
            for (int j = i; j < n; j++){
                if (i == j) {dp[i][j] = true;}
                else if (j - i == 1) {dp[i][j] = (s.charAt(i) == s.charAt(j));}
                else {
                    dp[i][j] = ((s.charAt(i) == s.charAt(j)) && dp[i + 1][j - 1]);
                }

                if (dp[i][j] && (j - i + 1 >= ans.length())){
                    ans = s.substring(i, j + 1);
                }
            }
        }
        return ans;
    }
}

// 中心扩散
class Solution {
    public String longestPalindrome(String s) {
        String ans = "";
        int idx = 0;
        while (idx < s.length()) {
            int left = idx, right = idx;
            while (left - 1 >= 0 && s.charAt(left) == s.charAt(left - 1)){left--;}
            while (right + 1 < s.length() && s.charAt(right) == s.charAt(right + 1)) {right++;}
            
            idx = right + 1;
            while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)){
                left--;
                right++;
            }

            if (right - left - 1 > ans.length()) {
                ans = s.substring(left + 1, right);
            }
        }
        return ans;
    }
}
```

## 最长回文串（0409）

> *给定一个包含大写字母和小写字母的字符串，找到通过这些字母构造成的最长的回文串。*
>
> *在构造过程中，请注意区分大小写。比如 "Aa" 不能当做一个回文字符串。*

```java
class Solution {
    public int longestPalindrome(String s) {
        if (s.length() == 0) {return 0;}
        Map<Character, Integer> count = new HashMap<>();
        for (int i = 0; i < s.length(); i++) {
            count.put(s.charAt(i), count.getOrDefault(s.charAt(i), 0) + 1);
        }

        int ans = 0;
        boolean single = true;
        for (char c : count.keySet()) {
            if (single && ((count.get(c) & 1) == 1)) {
                ans += count.get(c);
                single = false;
            }
            else {
                ans += (((count.get(c) & 1) == 0) ? count.get(c) : count.get(c) - 1);
            }
        }
        return ans;
    }
}
```

## 最长回文子序列（0516）

> *给定一个字符串 s ，找到其中最长的回文子序列，并返回该序列的长度。可以假设 s 的最大长度为 1000 。*

```java
class Solution {
    public int longestPalindromeSubseq(String s) {
        int n = s.length();
        int[][] dp = new int[n][n];
        for (int i = 0; i < n; i++) {
            dp[i][i] = 1;
        }

        for (int i = n - 2; i >= 0; i--){
            for (int j = i + 1; j < n; j++){
                if (s.charAt(i) == s.charAt(j)){
                    dp[i][j] = dp[i + 1][j - 1] + 2;
                }
                else {
                    dp[i][j] = Math.max(dp[i + 1][j], dp[i][j - 1]);
                }
            }
        }

        return dp[0][n - 1];
    }
}
```

## 验证回文串（0125）

> *给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。*

```java
class Solution {
    public boolean isPalindrome(String s) {
        int left = 0, right = s.length() - 1;
        while (left < right) {
            if (!Character.isLetterOrDigit(s.charAt(left))) {left++;}
            else if (!Character.isLetterOrDigit(s.charAt(right))) {right--;}
            else {
                if (Character.toLowerCase(s.charAt(left)) != Character.toLowerCase(s.charAt(right))) {return false;}
                left++;
                right--;
            }
        }
        return true;
    }
}
```

## 分割回文串（0131 && 0132）

> *给定一个字符串 s，将 s 分割成一些子串，使每个子串都是回文串。*
>
> *返回 s 所有可能的分割方案。*

```java
class Solution {
    List<List<String>> ans = new ArrayList<>();

    public List<List<String>> partition(String s) {
        int len = s.length();
        if (len == 0) {return ans;}
        Deque<String> path  = new LinkedList<>();

        backtrack(s, 0, len, path);
        return ans;
    }

    public void backtrack(String s, int start, int len, Deque<String> path) {
        if (start == len) {
            ans.add(new ArrayList<>(path));
            return;
        }

        for (int i = start; i < len; i++) {
            if (!checkpalindrome(s, start, i)) {continue;}
            path.offerLast(s.substring(start, i + 1));
            backtrack(s, i + 1, len, path);
            path.removeLast();
        }
    }

    public boolean checkpalindrome(String s, int left, int right) {
        while (left < right) {
            if (s.charAt(left) != s.charAt(right)) {return false;}
            left++;
            right--;
        }
        return true;
    }
}
```

> *给定一个字符串 s，将 s 分割成一些子串，使每个子串都是回文串。*
>
> *返回符合要求的最少分割次数。*

```java
class Solution {
    public int minCut(String s) {
        int len = s.length();
        if (len < 2) {return 0;}

        int[] dp = new int[len];
        for (int i = 0; i < len; i++) {dp[i] = i;}

        for (int i = 1; i < len; i++) {
            if (checkPalindrome(s, 0, i)) {
                dp[i] = 0;
                continue;
            }

            for (int j = 0; j < i; j++) {
                if (checkPalindrome(s, j + 1, i)) {
                    dp[i] = Math.min(dp[i], dp[j] + 1);
                }
            }
        }
        return dp[len - 1];
    }

    public boolean checkPalindrome(String s, int left, int right) {
        while (left < right) {
            if (s.charAt(left) != s.charAt(right)) {return false;}
            left++;
            right--;
        }
        return true;
    }
}
```

## 字符串转换整数（0008）

> *实现一个 atoi 函数，使其能将字符串转换成整数。*
>
> *首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。接下来的转化规则如下：*
>
> *输入: "   -42"*
>
> *输出: -42*

```java
public class Solution {
    public int myAtoi(String str) {
        int len = str.length();
        char[] charArray = str.toCharArray();

        // 1、去除前导空格
        int index = 0;
        while (index < len && charArray[index] == ' ') {index++;}

        // 2、如果已经遍历完成（针对极端用例 "      "）
        if (index == len) {return 0;}

        // 3、如果出现符号字符，仅第 1 个有效，并记录正负
        int sign = 1;
        char firstChar = charArray[index];
        if (firstChar == '+') {
            index++;
        } else if (firstChar == '-') {
            index++;
            sign = -1;
        }

        // 4、将后续出现的数字字符进行转换
        int res = 0;
        while (index < len) {
            char currChar = charArray[index];
            // 4.1 先判断不合法的情况
            if (currChar > '9' || currChar < '0') {break;}

            // 题目中说：环境只能存储 32 位大小的有符号整数，因此，需要提前判断乘以 10 以后是否越界
            if (res > Integer.MAX_VALUE / 10 || (res == Integer.MAX_VALUE / 10 && (currChar - '0') > Integer.MAX_VALUE % 10)) {
                return Integer.MAX_VALUE;
            }
            if (res < Integer.MIN_VALUE / 10 || (res == Integer.MIN_VALUE / 10 && (currChar - '0') > -(Integer.MIN_VALUE % 10))) {
                return Integer.MIN_VALUE;
            }

            // 4.2 合法的情况下，才考虑转换，每一步都把符号位乘进去
            res = res * 10 + sign * (currChar - '0');
            index++;
        }
        return res;
    }
}
```

## 正则表达式匹配（0010）

> *给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '\*' 的正则表达式匹配。*
>
> *'.' 匹配任意单个字符*
>
> *'\*' 匹配零个或多个前面的那一个元素*
>
> *输入：s = "ab" p = ".\*"*
>
> *输出：true*
>
> *解释：".\*" 表示可匹配零个或多个（'\*'）任意字符（'.'）。*

```java
class Solution {
    public boolean isMatch(String s, String p) {
        //初始化dp[0][0]=true,dp[0][1]和dp[1][0]~dp[s.length][0]默认值为false所以不需要显式初始化
        //填写第一行dp[0][2]~dp[0][p.length]
        int m = s.length(), n = p.length();
        boolean[][] dp = new boolean[m + 1][n + 1];
        dp[0][0] = true;
        for (int k = 2; k <= p.length(); k++){
            dp[0][k] = (p.charAt(k - 1) == '*' && dp[0][k-2]);
        }

        for (int i = 0; i < m; i++){
            for (int j = 0; j < n; j++){
                if (p.charAt(j) == '*'){
                    // 1.只匹配0次，s不变[i+1], p移除两个元素[j+1-2]。
                    // 2.比较s的i元素和p的j-1(因为此时j元素为*)元素,相等则移除首元素[i+1-1],p不变。
                    dp[i + 1][j + 1] = (dp[i + 1][j - 1] || (dp[i][j + 1] && headMatched(s, p, i, j - 1)));
                }else {
                    dp[i + 1][j + 1] =  (dp[i][j] && headMatched(s, p, i, j));
                }
            }
        }
        return dp[m][n];
    }
    //判断s第i个字符和p第j个字符是否匹配
    public boolean headMatched(String s,String p,int i,int j){
        return (s.charAt(i) == p.charAt(j) || p.charAt(j) == '.');
    }
}
```

## 最长公共前缀（0014）

> *编写一个函数来查找字符串数组中的最长公共前缀。*
>
> *如果不存在公共前缀，返回空字符串 ""。*
>
> *输入: ["flower","flow","flight"]*
>
> *输出: "fl"*

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs.length == 0) {return "";}
        else if (strs.length == 1) {return strs[0];}
        else {
            int minLength = strs[0].length();
            for (int i = 1; i < strs.length; i++){
                if (strs[i].length() < minLength) {
                    minLength = strs[i].length();
                }
            }
            
            int index = 0;
            while (index < minLength){
                char ch = strs[0].charAt(index);
                for (int i = 0; i < strs.length; i++){
                    if (strs[i].charAt(index) != ch) {
                        return strs[0].substring(0, index);
                    }
                }
                index++;
            }
            return strs[0].substring(0, index);
        }
    }
}
```

## 串联所有单词的子串（0030）

> *给定一个字符串 s 和一些长度相同的单词 words。找出 s 中恰好可以由 words 中所有单词串联形成的子串的起始位置。*
>
> *注意子串要与 words 中的单词完全匹配，中间不能有其他字符，但不需要考虑 words 中单词串联的顺序。*
>
> *输入：*
>
> *s = "barfoothefoobarman",*
>
> *words = ["foo","bar"]*
>
> *输出：[0,9]*
>
> *解释：*
>
> *从索引 0 和 9 开始的子串分别是 "barfoo" 和 "foobar" 。*
>
> *输出的顺序不重要, [9,0] 也是有效答案。*

```java
// 滑动窗口
class Solution {
    public List<Integer> findSubstring(String s, String[] words) {
        List<Integer> ans = new ArrayList<>();
        if (s.length() == 0 || words.length == 0) {return ans;}

        int wordLength = words[0].length(), wordNum = words.length;
        int targetLength = wordLength * wordNum;
        HashMap<String, Integer> map = new HashMap<>();
        for (String word : words) {
            map.put(word, map.getOrDefault(word, 0) + 1);
        }

        for (int i = 0; i < s.length() - targetLength + 1; i++) {
            String tmp = s.substring(i, i + targetLength);
            HashMap<String, Integer> tempMap = new HashMap<>();
            for (int j = 0; j < targetLength; j += wordLength) {
                String tempWord = tmp.substring(j, j+ wordLength);
                tempMap.put(tempWord, tempMap.getOrDefault(tempWord, 0) + 1);
            }
            if (tempMap.equals(map)) {ans.add(i);}
        }

        return ans;
    }
}
```

## 最长有效括号（0032）

> *给你一个只包含 '(' 和 ')' 的字符串，找出最长有效（格式正确且连续）括号子串的长度。*
>
> *输入：s = "(()"*
>
> *输出：2*
>
> *解释：最长有效括号子串是 "()"*

```java
class Solution {
    public int longestValidParentheses(String s) {
        int[] dp = new int[s.length() + 1];
        dp[0] = 0;
        for (int i = 1; i < s.length(); i++) {
            if (s.charAt(i) == ')' && i - dp[i - 1] - 1 >= 0 && s.charAt(i - dp[i - 1] - 1) == '(') {
                dp[i] = dp[i - 1] + 2;
                if (i - dp[i - 1] - 2 >= 0) {
                    dp[i] += dp[i - dp[i - 1] - 2];
                }
            }
        }
        return getMax(dp);
    }

    public int getMax(int[] nums) {
        int ans = Integer.MIN_VALUE;
        for (int n : nums) {
            if (n > ans) {
                ans = n;
            }
        }
        return ans;
    }
}
```

## 删除无效的括号（0300）

> *删除最小数量的无效括号，使得输入的字符串有效，返回所有可能的结果。*
>
> *说明: 输入可能包含了除 ( 和 ) 以外的字符。*

```java
class Solution {
    public List<String> removeInvalidParentheses(String s) {
        Set<String> set = new HashSet<>();
        List<String> ans = new ArrayList<>();
        set.add(s);
        while (true) {
            for (String str : set) {
                if (isRegular(str))
                    ans.add(str);
            }
            if (ans.size() > 0) return ans;
            Set<String> nextSet = new HashSet<>();
            for (String str : set) {
                for (int i = 0; i < str.length(); i ++) {
                    if (str.charAt(i) == '(' || str.charAt(i) == ')')
                        nextSet.add(str.substring(0, i) + str.substring(i + 1, str.length()));
                }
            }
            set = nextSet;
        }
    }
    
    public boolean isRegular(String s) {
        char[] ss = s.toCharArray();
        int count = 0;
        for (char c : ss) {
            if (c == '(') count ++;
            else if (c == ')') count --;
            if (count < 0) return false;
        }
        return count == 0;
    }
}
```

## 通配符匹配（0044）

> *给定一个字符串 (s) 和一个字符模式 (p) ，实现一个支持 '?' 和 '\*' 的通配符匹配。*
>
> *'?' 可以匹配任何单个字符。*
>
> *'\*' 可以匹配任意字符串（包括空字符串）。*
>
> *两个字符串完全匹配才算匹配成功。*

```java
class Solution {
    public boolean isMatch(String s, String p) {
        int m = s.length();
        int n = p.length();
        boolean[][] dp = new boolean[m + 1][n + 1];
        dp[0][0] = true;
        for (int i = 1; i <= n; i++) {
            if (p.charAt(i - 1) == '*') {
                dp[0][i] = true;
            } 
            else {
                break;
            }
        }

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (p.charAt(j - 1) == '*') {
                    dp[i][j] = dp[i - 1][j] || dp[i][j - 1];
                }
                else if (p.charAt(j - 1) == '?' || s.charAt(i - 1) == p.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                }
            }
        }

        return dp[m][n];
    }
}
```

## 字母异位词分组（0049）

> *给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。*

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        for (String s : strs) {
            char[] c = s.toCharArray();
            Arrays.sort(c);
            String key = new String(c);
            List<String> list = map.getOrDefault(key, new ArrayList<String>());
            list.add(s);
            map.put(key, list);
        }
        return new ArrayList<List<String>>(map.values());
    }
}
```

## 最后一个单词的长度（0058）

> *给定一个仅包含大小写字母和空格 ' ' 的字符串 s，返回其最后一个单词的长度。*
>
> *如果字符串从左向右滚动显示，那么最后一个单词就是最后出现的单词。*

```java
class Solution {
    public int lengthOfLastWord(String s) {
        int count = 0;
        for (int i = s.length() - 1; i >= 0; i--) {
            if (s.charAt(i) == ' ' && count == 0) {
                continue;
            }
            else if (s.charAt(i) != ' ') {
                count++;
            }
            else {
                break;
            }
        }
        return count;
    }
}
```

## 二进制求和（0067）

> *给两个二进制字符串，返回它们的和（用二进制表示）。*
>
> *输入为 非空 字符串且只包含数字 1 和 0。*

```java
class Solution {
    public String addBinary(String a, String b) {
        StringBuilder ans = new StringBuilder();
        int ca = 0;
        for(int i = a.length() - 1, j = b.length() - 1; i >= 0 || j >= 0; i--, j--) {
            int sum = ca;
            sum += (i >= 0 ? a.charAt(i) - '0' : 0);
            sum += (j >= 0 ? b.charAt(j) - '0' : 0);
            ans.append(sum % 2);
            ca = sum / 2;
        }
        ans.append(ca == 1 ? ca : "");
        return ans.reverse().toString();
    }
}
```

## 简化路径（0071）

> *以 Unix 风格给出一个文件的绝对路径，你需要简化它。或者换句话说，将其转换为规范路径。*

```java
class Solution {
    public String simplifyPath(String path) {
        if (path == null || path.length() == 0) {return "/";}
        int n = path.length();
        
        List<String> names = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (path.charAt(i) != '/'){
                int j = i;
                while (j < n && path.charAt(j) != '/') {j++;}
                names.add(path.substring(i, j));
                i = j;
            }
        }

        List<String> ans = new ArrayList<>();
        for (int i = 0; i < names.size(); i++) {
            if (names.get(i).equals("..")) {
                if (ans.size() > 0) {
                    ans.remove(ans.size() - 1);
                }
            }
            else if (!names.get(i).equals(".")) {
                ans.add(names.get(i));
            }
        }
        return "/" + String.join("/", ans);
    }
}
```

## 编辑距离（0072）

> *给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数 。*
>
> 对一个单词进行如下三种操作：*
>
> *插入一个字符*
>
> *删除一个字符*
>
> *替换一个字符*

```java
class Solution {
    public int minDistance(String word1, String word2) {
        int m = word1.length(), n = word2.length();
        int[][] dp = new int[m + 1][n + 1];
        for (int i = 1; i <= m; i++) {dp[i][0] = i;}
        for (int j = 1; j <= n; j++) {dp[0][j] = j;}

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                }
                else {
                    dp[i][j] = Math.min(Math.min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1;
                }
            }
        }

        return dp[m][n];
    }
}
```

## 最小覆盖子串（0076）

> *给你一个字符串 s 、一个字符串 t 。返回 s 中涵盖 t 所有字符的最小子串。如果 s 中不存在涵盖 t 所有字符的子串，则返回空字符串 "" 。*
>
> *注意：如果 s 中存在这样的子串，我们保证它是唯一的答案。*

```java
class Solution {
    public String minWindow(String s, String t) {
        HashMap<Character, Integer> need = new HashMap<>();
        HashMap<Character, Integer> window = new HashMap<>();
        for (int i = 0; i < t.length(); i++) {
            char c = t.charAt(i);
            need.put(c, need.getOrDefault(c, 0) + 1);
        }

        int left = 0, right = 0;
        int valid = 0;
        // 记录最小覆盖子串的起始索引及长度
        int start = 0, len = s.length() + 1;
        while (right < s.length()){
            char c = s.charAt(right);
            right++;
            if (need.containsKey(c)){
                window.put(c, window.getOrDefault(c, 0) + 1);
                if (window.get(c) - need.get(c) == 0){valid++;}
            }

            while (valid == need.size()){
                if (right - left < len){
                    start = left;
                    len = right - left;
                }
                char d = s.charAt(left);
                left++;
                if (need.containsKey(d)){
                    if (window.get(d) - need.get(d) == 0){valid--;}
                    window.put(d, window.getOrDefault(d, 0) - 1);
                }
            }
        }

        return len == s.length() + 1 ? "" : s.substring(start, start + len);
    }
}
```

## 解码方法（0091）

> *'A' -> 1*
>
> *'B' -> 2*
>
> *...*
>
> *'Z' -> 26*
>
> *要 解码 已编码的消息，所有数字必须基于上述映射的方法，反向映射回字母（可能有多种方法）。例如，"111" 可以将 "1" 中的每个 "1" 映射为 "A" ，从而得到 "AAA" ，或者可以将 "11" 和 "1"（分别为 "K" 和 "A" ）映射为 "KA" 。注意，"06" 不能映射为 "F" ，因为 "6" 和 "06" 不同。*
>
> *给你一个只含数字的 非空 字符串 num ，请计算并返回 解码 方法的 总数 。*

```java
class Solution {
    public int numDecodings(String s) {
        int n = s.length();
        if (n == 0) {return 0;}
        int[] dp = new int[n + 1];
        dp[0] = 1;
        dp[1] = s.charAt(0) == '0' ? 0 : 1;

        for (int i = 1; i < n; i++) {
            if ((s.charAt(i - 1) == '1') || (s.charAt(i - 1) == '2' && s.charAt(i) < '7')) {
                if (s.charAt(i) == '0') {dp[i + 1] = dp[i - 1];}
                else {dp[i + 1] = dp[i] + dp[i - 1];}
            }
            else if (s.charAt(i) == '0') {
                return 0;
            }
            else {
                dp[i + 1] = dp[i];
            }
        }

        return dp[n];
    }
}
```

## 不同的子序列（0115）

> 给定一个字符串 `s` 和一个字符串 `t` ，计算在 `s` 的子序列中 `t` 出现的个数。

```java
class Solution {
    public int numDistinct(String s, String t) {
        int m = s.length(), n = t.length();
        if (m < n) {
            return 0;
        }
        int[][] dp = new int[m + 1][n + 1];
        for (int i = 0; i <= m; i++) {
            dp[i][n] = 1;
        }
        for (int i = m - 1; i >= 0; i--) {
            char sChar = s.charAt(i);
            for (int j = n - 1; j >= 0; j--) {
                char tChar = t.charAt(j);
                if (sChar == tChar) {
                    dp[i][j] = dp[i + 1][j + 1] + dp[i + 1][j];
                } else {
                    dp[i][j] = dp[i + 1][j];
                }
            }
        }
        return dp[0][0];
    }
}
```

## 单词拆分（0139 && 0140）

> *给定一个非空字符串 s 和一个包含非空单词的列表 wordDict，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词。*

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        int n = s.length();
        if (n == 0) {return true;}

        boolean[] dp = new boolean[n + 1];
        dp[0] = true;

        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                if (dp[j] && wordDict.contains(s.substring(j, i))) {
                    dp[i] = true;
                }
            }
        }

        return dp[n];
    }
}
```

> *给定一个非空字符串 s 和一个包含非空单词列表的字典 wordDict，在字符串中增加空格来构建一个句子，使得句子中所有的单词都在词典中。返回所有这些可能的句子。*

```java
class Solution {
    public List<String> wordBreak(String s, List<String> wordDict) {
        // 为了快速判断一个单词是否在单词集合中，需要将它们加入哈希表
        int n = s.length();

        // 第 1 步：动态规划得到是否可以划分
        boolean[] dp = new boolean[n + 1];
        dp[0] = true;
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                if (dp[j] && wordDict.contains(s.substring(j, i))) {
                    dp[i] = true;
                }
            }
        }

        // 第 2 步：回溯算法搜索所有符合条件的解
        List<String> ans = new ArrayList<>();
        if (dp[n]) {
            Deque<String> deque = new LinkedList<>();
            dfs(s, n, wordDict, dp, deque, ans);
            return ans;
        }

        return ans;
    }

    public void dfs(String s, int len, List<String> wordDict, boolean[] dp, Deque<String> deque, List<String> ans) {
        if (len == 0) {
            ans.add(String.join(" ", deque));
            return;
        }

        for (int i = len - 1; i >= 0; i--) {
            String tmp = s.substring(i, len);
            if (dp[i] && wordDict.contains(tmp)) {
                deque.offerFirst(tmp);
                dfs(s, i, wordDict, dp, deque, ans);
                deque.pollFirst();
            }
        }
    }
}
```

## 翻转字符串中的单词（0151）

> *给定一个字符串，逐个翻转字符串中的每个单词。*
>
> *示例 1：*
>
> *输入："the sky is blue"*
>
> *输出："blue is sky the"*

```java
class Solution {
    public String reverseWords(String s) {
        // 除去开头和末尾的空白字符
        s = s.trim();
        // 正则匹配连续的空白字符作为分隔符分割
        List<String> wordList = Arrays.asList(s.split("\\s+"));
        Collections.reverse(wordList);
        return String.join(" ", wordList);
    }
}
```

## 比较版本号（0165）

> *给你两个版本号 version1 和 version2 ，请你比较它们。*

```java
class Solution {
    public int compareVersion(String version1, String version2) {
        String[] nums1 = version1.split("\\.");
        String[] nums2 = version2.split("\\.");
        int n1 = nums1.length, n2 = nums2.length;

        int i1, i2;
        for (int i = 0; i < Math.max(n1, n2); i++) {
            i1 = i < n1 ? Integer.parseInt(nums1[i]) : 0;
            i2 = i < n2 ? Integer.parseInt(nums2[i]) : 0;
            if (i1 != i2) {return i1 > i2 ? 1 : -1;}
        }

        return 0;
    }
}
```

## 最大数（0179）

> *给定一组非负整数 nums，重新排列它们每个数字的顺序（每个数字不可拆分）使之组成一个最大的整数。*

```java
class Solution {
    public String largestNumber(int[] nums) {
        String[] strs = new String[nums.length];
        for (int i = 0; i < nums.length; i++) {
            strs[i] = String.valueOf(nums[i]);
        }
        Arrays.sort(strs, (o1, o2) -> (o2 + o1).compareTo(o1 + o2));

        StringBuilder ans = new StringBuilder();
        for (String s : strs) {
            if (ans.toString().equals("0")) {ans.delete(0, 1);}
            ans.append(s);
        }

        return ans.toString();
    }
}
```

## 最大单词长度乘积（0318）

> *给定一个字符串数组 words，找到 length(word[i]) \* length(word[j]) 的最大值，并且这两个单词不含有公共字母。*
>
> *可以认为每个单词只包含小写字母。如果不存在这样的两个单词，返回 0。*

```java
class Solution {
    public int maxProduct(String[] words) {
        int[] wordToNum = new int[words.length];
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            int num = 0;
            for (int j = 0; j < word.length(); j++) {
                int index = word.charAt(j) - 'a';
                num = num | (1 << index);
            }
            wordToNum[i] = num;
        }

        int ans = 0;
        for (int i = 0; i < words.length - 1; i++) {
            for (int j = i + 1; j < words.length; j++) {
                if ((wordToNum[i] & wordToNum[j]) != 0) {continue;}
                ans = Math.max(ans, words[i].length() * words[j].length());
            }
        }

        return ans;
    }
}
```

## 同构字符串（0205）

> *给定两个字符串 s 和 t，判断它们是否是同构的。*
>
> *如果 s 中的字符可以被替换得到 t ，那么这两个字符串是同构的。*
>
> *所有出现的字符都必须用另一个字符替换，同时保留字符的顺序。两个字符不能映射到同一个字符上，但字符可以映射自己本身。*

```java
class Solution {
    public boolean isIsomorphic(String s, String t) {
        Map<Character, Character> s2t = new HashMap<>();
        Map<Character, Character> t2s = new HashMap<>();
        int len = s.length();
        for (int i = 0; i < len; ++i) {
            char x = s.charAt(i), y = t.charAt(i);
            if ((s2t.containsKey(x) && s2t.get(x) != y) || (t2s.containsKey(y) && t2s.get(y) != x)) {
                return false;
            }
            s2t.put(x, y);
            t2s.put(y, x);
        }
        return true;
    }
}
```

## 去除重复字母（0316）

> *给你一个字符串 s ，请你去除字符串中重复的字母，使得每个字母只出现一次。*
>
> *需保证 返回结果的字典序最小（要求不能打乱其他字符的相对位置）。*

```java
class Solution {
    public String removeDuplicateLetters(String s) {
        Deque<Character> deque = new LinkedList<>();
        int[] count = new int[26];
        boolean[] exists = new boolean[26];

        for (char c : s.toCharArray()) {
            count[c - 'a']++;
        }

        for (int i = 0; i < s.length(); i++) {
            char tmp = s.charAt(i);
            if (!exists[tmp - 'a']) {
                while (!deque.isEmpty() && deque.peekLast() > tmp && count[deque.peekLast() - 'a'] > 0) {
                    exists[deque.peekLast() - 'a'] = false;
                    deque.removeLast();
                }
                exists[tmp - 'a'] = true;
                deque.offerLast(tmp);
            }
            count[tmp - 'a']--;
        }

        StringBuilder ans = new StringBuilder();
        for (char c : deque) {
            ans.append(c);
        }

        return ans.toString();
    }
}
```

## 字符串编码（0394）

> *给定一个经过编码的字符串，返回它解码后的字符串。*
>
> *编码规则为: k[encoded_string]，表示其中方括号内部的 encoded_string 正好重复 k 次。注意 k 保证为正整数。*

```java
class Solution {
    public String decodeString(String s) {
        StringBuilder ans = new StringBuilder();
        Stack<Integer> count = new Stack<>();
        Stack<StringBuilder> set = new Stack<>();

        int num = 0;
        for (char c : s.toCharArray()) {
            if (Character.isDigit(c)) {
                num = num * 10 + c - '0';
            }
            else if (c == '[') {
                count.add(num);
                set.add(ans);
                num = 0;
                ans = new StringBuilder();
            }
            else if (Character.isAlphabetic(c)) {
                ans.append(c);
            }
            else {
                StringBuilder tmp = set.pop();
                int curCount = count.pop();
                for (int i = 0; i < curCount; i++) {tmp.append(ans);}
                ans = tmp;
            }
        }
        
        return ans.toString();
    }
}
```

## 压缩字符串（0443）

> *输入：*
>
> *["a","a","b","b","c","c","c"]*
>
> *输出：*
>
> *返回 6 ，输入数组的前 6 个字符应该是：["a","2","b","2","c","3"]*

```java
class Solution {
    public int compress(char[] chars) {
        // anchor:字符    write：写的位置    read：读的位置
        int anchor = 0, write = 0;
        for (int read = 0; read < chars.length; read++) {
            if (read + 1 == chars.length || chars[read + 1] != chars[read]) {
                chars[write++] = chars[anchor];
                if (read > anchor) {
                    for (char c : ("" + (read - anchor + 1)).toCharArray()) {       // 写count
                        chars[write++] = c;
                    }
                }
                anchor = read + 1;
            }
        }
        return write;
    }
}
```

## 至少有K个重复字符的最长子串（0395）

> *给你一个字符串 s 和一个整数 k ，请你找出 s 中的最长子串， 要求该子串中的每一字符出现次数都不少于 k 。返回这一子串的长度。*

```java
class Solution {
    public int longestSubstring(String s, int k) {
        int ans = 0;
        int n = s.length();

        // 枚举最长子串中的字符种类
        // total:窗口内的字符种类数目
        // less:窗口中还有几种字符未达到k次
        for (int i = 1; i <= 26; i++) {
            int left = 0, right = 0;
            int[] count = new int[26];
            int total = 0, less = 0;

            while (right < n) {
                count[s.charAt(right) - 'a']++;
                if (count[s.charAt(right) - 'a'] == 1) {
                    total++;
                    less++;
                }
                if (count[s.charAt(right) - 'a'] == k) {
                    less--;
                }

                while (total > i) {
                    count[s.charAt(left) - 'a']--;
                    if (count[s.charAt(left) - 'a'] == k - 1) {
                        less++;
                    }
                    if (count[s.charAt(left) - 'a'] == 0) {
                        total--;
                        less--;
                    }
                    left++;
                }
                if (less == 0) {ans = Math.max(ans, right - left + 1);}
                right++;
            }
        }
        return ans;
    }
}
```

## 替换后的最长重复字符（0424）

> *给你一个仅由大写英文字母组成的字符串，你可以将任意位置上的字符替换成另外的字符，总共可最多替换 k 次。在执行上述操作后，找到包含重复字母的最长子串的长度。*
>
> *注意：字符串长度 和 k 不会超过 104。*

```java
class Solution {
    public int characterReplacement(String s, int k) {
        int n = s.length();
        if (n < 2) {return n;}

        char[] charArray = s.toCharArray();
        int left = 0, right = 0;
        int ans = 0, maxCount = 0;
        int[] count = new int[26];

        while (right < n) {
            count[charArray[right] - 'A']++;
            maxCount = Math.max(maxCount, count[charArray[right] - 'A']);
            right++;

            if (right - left > maxCount + k) {
                count[charArray[left] - 'A']--;
                left++;
            }
            
            ans = Math.max(ans, right - left);
        }

        return ans;
    }
}
```

## 找到字符串中所有字母异位词（0438）

> *给定一个字符串 s 和一个非空字符串 p，找到 s 中所有是 p 的字母异位词的子串，返回这些子串的起始索引。*
>
> *字符串只包含小写英文字母，并且字符串 s 和 p 的长度都不超过 20100。*

```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        HashMap<Character, Integer> need = new HashMap<>();
        HashMap<Character, Integer> window = new HashMap<>();
        for (int i = 0; i < p.length(); i++){
            char c = p.charAt(i);
            need.put(c, need.getOrDefault(c, 0) + 1);
        }

        int left = 0, right = 0;
        int valid = 0;
        List<Integer> ans = new ArrayList<Integer>();

        while (right < s.length()){
            char c = s.charAt(right);
            right++;
            if (need.containsKey(c)){
                window.put(c, window.getOrDefault(c, 0) + 1);
                if (window.get(c) - need.get(c) == 0) {valid++;}
            }

            while (right - left >= p.length()){
                if (valid - need.size() == 0) {ans.add(left);}
                char d = s.charAt(left);
                left++;
                if (need.containsKey(d)){
                    if (window.get(d) - need.get(d) == 0) {valid--;}
                    window.put(d, window.getOrDefault(d, 0) - 1);
                }
            }
        }
        
        return ans;
    }
}
```

