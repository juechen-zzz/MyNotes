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

> *给定一个非空字符串 s，最多删除一个字符。判断是否能成为回文字符串。*

```java
class Solution {
    public boolean validPalindrome(String s) {
        int low = 0, high = s.length() - 1;
        while (low < high) {
            char c1 = s.charAt(low), c2 = s.charAt(high);
            if (c1 == c2) {
                low++;
                high--;
            } else {
                return validPalindrome(s, low, high - 1) || validPalindrome(s, low + 1, high);
            }
        }
        return true;
    }

    public boolean validPalindrome(String s, int low, int high) {
        for (int i = low, j = high; i < j; i++, j--) {
            char c1 = s.charAt(i), c2 = s.charAt(j);
            if (c1 != c2) {return false;}
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

## 回文子串（0647）

> *给定一个字符串，你的任务是计算这个字符串中有多少个回文子串。*
>
> *具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被视作不同的子串。*

```java
class Solution {
    public int countSubstrings(String s) {
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        int ans = 0;

        for (int j = 0; j < n; j++) {
            for (int i = 0; i <= j; i++) {
                //如果i和j指向的字符不一样，那么dp[i][j]就不能构成回文字符串
                if (s.charAt(i) != s.charAt(j)) {continue;}
                dp[i][j] = (j - i <= 2 || dp[i + 1][j - 1]);
                //如果从i到j能构成回文串，count就加1
                if (dp[i][j]) {ans++;}
            }
        }

        return ans;
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

## 删除无效的括号（0301）

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

## 复原IP地址（0093）

> 给定一个只包含数字的字符串，用以表示一个 IP 地址，返回所有可能从 s 获得的 有效 IP 地址 。你可以按任何顺序返回答案。
>
> 有效 IP 地址 正好由四个整数（每个整数位于 0 到 255 之间组成，且不能含有前导 0），整数之间用 '.' 分隔。
>
> 例如："0.1.2.201" 和 "192.168.1.1" 是 有效 IP 地址，但是 "0.011.255.245"、"192.168.1.312" 和 "192.168@1.1" 是 无效 IP 地址。
>
>  
>
> 示例 1：
>
> 输入：s = "25525511135"
> 输出：["255.255.11.135","255.255.111.35"]
> 示例 2：
>
> 输入：s = "0000"
> 输出：["0.0.0.0"]

```java
class Solution {
    static final int SEG_COUNT = 4;
    List<String> ans = new ArrayList<String>();
    int[] segments = new int[SEG_COUNT];

    public List<String> restoreIpAddresses(String s) {
        segments = new int[SEG_COUNT];
        dfs(s, 0, 0);
        return ans;
    }

    public void dfs(String s, int segId, int segStart) {
        // 如果找到了 4 段 IP 地址并且遍历完了字符串，那么就是一种答案
        if (segId == SEG_COUNT) {
            if (segStart == s.length()) {
                StringBuffer ipAddr = new StringBuffer();
                for (int i = 0; i < SEG_COUNT; ++i) {
                    ipAddr.append(segments[i]);
                    if (i != SEG_COUNT - 1) {
                        ipAddr.append('.');
                    }
                }
                ans.add(ipAddr.toString());
            }
            return;
        }

        // 如果还没有找到 4 段 IP 地址就已经遍历完了字符串，那么提前回溯
        if (segStart == s.length()) {
            return;
        }

        // 由于不能有前导零，如果当前数字为 0，那么这一段 IP 地址只能为 0
        if (s.charAt(segStart) == '0') {
            segments[segId] = 0;
            dfs(s, segId + 1, segStart + 1);
        }

        // 一般情况，枚举每一种可能性并递归
        int addr = 0;
        for (int segEnd = segStart; segEnd < s.length(); ++segEnd) {
            addr = addr * 10 + (s.charAt(segEnd) - '0');
            if (addr > 0 && addr <= 0xFF) {
                segments[segId] = addr;
                dfs(s, segId + 1, segEnd + 1);
            } else {
                break;
            }
        }
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

## 反转字符串（0541）

> *给定一个字符串 s 和一个整数 k，你需要对从字符串开头算起的每隔 2k 个字符的前 k 个字符进行反转。*
>
> *如果剩余字符少于 k 个，则将剩余字符全部反转。*
>
> *如果剩余字符小于 2k 但大于或等于 k 个，则反转前 k 个字符，其余字符保持原样。*

```java
class Solution {
    public String reverseStr(String s, int k) {
        char[] ans = s.toCharArray();
        for (int start = 0; start < ans.length; start += 2 * k) {
            int left = start, right = Math.min(start + k - 1, ans.length - 1);
            while (left < right) {
                char tmp = ans[left];
                ans[left++] = ans[right];
                ans[right--] = tmp;
            }
        } 
        return new String(ans);
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

## 字符串解码（0394）

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

## 移掉K位数字（0402）

> 给定一个以字符串表示的非负整数 *num*，移除这个数中的 *k* 位数字，使得剩下的数字最小。

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

        for (int i = 0; i < k; i++) {deque.pollLast();}

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

## 重复的子字符串（0459）

> *给定一个非空的字符串，判断它是否可以由它的一个子串重复多次构成。给定的字符串只含有小写英文字母，并且长度不超过10000。*

```java
// 字符串匹配
class Solution {
    public boolean repeatedSubstringPattern(String s) {
        return (s + s).indexOf(s, 1) != s.length();
    }
}

// KMP
class Solution {
    public boolean repeatedSubstringPattern(String s) {
        return kmp(s + s, s);
    }

    private boolean kmp(String query, String pattern) {
        int m = query.length(), n = pattern.length();
        int[] fail = new int[n];
        Arrays.fill(fail, -1);

        for (int i = 1; i < n; i++) {
            int j = fail[i - 1];
            while (j != -1 && pattern.charAt(j + 1) != pattern.charAt(i)) {
                j = fail[j];
            }
            if (pattern.charAt(j + 1) == pattern.charAt(i)) {
                fail[i] = j + 1;
            }
        }

        int match = -1;
        for (int i = 1; i < m - 1; i++) {
            while (match != -1 && pattern.charAt(match + 1) != query.charAt(i)) {
                match = fail[match];
            }
            if (pattern.charAt(match + 1) == query.charAt(i)) {
                match++;
                if (match == n - 1) {return true;}
            }
        }

        return false;
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

## 环绕字符串中唯一的子字符串（0467）

> 把字符串 s 看作是“abcdefghijklmnopqrstuvwxyz”的无限环绕字符串，所以 s 看起来是这样的："...zabcdefghijklmnopqrstuvwxyzabcdefghijklmnopqrstuvwxyzabcd....". 
>
> 现在我们有了另一个字符串 p 。你需要的是找出 s 中有多少个唯一的 p 的非空子串，尤其是当你的输入是字符串 p ，你需要输出字符串 s 中 p 的不同的非空子串的数目。 
>
> 注意: p 仅由小写的英文字母组成，p 的大小可能超过 10000。

```java
class Solution {
    public int findSubstringInWraproundString(String p) {
        int[] dp = new int[26];
        char[] array = p.toCharArray();

        int count = 0;
        for (int i = 0; i < array.length; i++) {
            if (i > 0 && (array[i] - array[i - 1] - 1) % 26 == 0) {count++;}
            else {count = 1;}

            dp[array[i] - 'a'] = Math.max(dp[array[i] - 'a'], count);
        }

        int ans = 0;
        for (int n : dp) {ans += n;}
        return ans;
    }
}
```

## 连接词（0472）

> 给定一个 不含重复 单词的字符串数组 words ，编写一个程序，返回 words 中的所有 连接词 。
>
> 连接词 的定义为：一个字符串完全是由至少两个给定数组中的单词组成的。

```java
class Solution {
    public List<String> findAllConcatenatedWordsInADict(String[] words) {
        Set<String> set = new HashSet<>(Arrays.asList(words));
        List<String> ans = new ArrayList<>();
        for (String s : words) {
            if (myMethod(s, set, 0)) {
                ans.add(s);
            }
        }
        return ans;
    }

    private boolean myMethod(String s, Set<String> set, int index) {
        if (set.contains(s) && index != 0) {return true;}
        for (int i = 1; i < s.length(); i++) {
            String tmp = s.substring(0, i);
            if (set.contains(tmp)) {
                if (myMethod(s.substring(i), set, index + 1)) {
                    return true;
                }
            }
        }
        return false;
    }
}
```

## 最长特殊序列

> 给你两个字符串，请你从这两个字符串中找出最长的特殊序列。
>
> 「最长特殊序列」定义如下：该序列为某字符串独有的最长子序列（即不能是其他字符串的子序列）。
>
> 子序列 可以通过删去字符串中的某些字符实现，但不能改变剩余字符的相对顺序。空序列为所有字符串的子序列，任何字符串为其自身的子序列。
>
> 输入为两个字符串，输出最长特殊序列的长度。如果不存在，则返回 -1。

```java
class Solution {
    public int findLUSlength(String a, String b) {
        if (a.equals(b)) {return -1;}
        return Math.max(a.length(), b.length());
    }
}
```

> 给定字符串列表，你需要从它们中找出最长的特殊序列。最长特殊序列定义如下：该序列为某字符串独有的最长子序列（即不能是其他字符串的子序列）。
>
> 子序列可以通过删去字符串中的某些字符实现，但不能改变剩余字符的相对顺序。空序列为所有字符串的子序列，任何字符串为其自身的子序列。
>
> 输入将是一个字符串列表，输出是最长特殊序列的长度。如果最长特殊序列不存在，返回 -1 。

```java
class Solution {
    public int findLUSlength(String[] strs) {
        Arrays.sort(strs, (s1, s2) -> s2.length() - s1.length());

        for (int i = 0; i < strs.length; i++) {
            boolean flag = true;
            for (int j = 0; j < strs.length; j++) {
                if (i == j) {continue;}
                System.out.println(strs[i] + " " + strs[j]);
                if (isSubsequence(strs[i], strs[j])) {
                    flag = false;
                    break;
                }
            }
            if (flag) {return strs[i].length();}
        }

        return -1;
    }

    private static boolean isSubsequence(String x, String y) {
        int idx = 0;
        for (int i = 0; i < y.length() && idx < x.length(); i++) {
            if (x.charAt(idx) == y.charAt(i)) {
                idx++;
            }
        }
        return idx == x.length();
    }
}
```

## 通过删除字母匹配字典里最长单词（0524）

> 给定一个字符串和一个字符串字典，找到字典里面最长的字符串，该字符串可以通过删除给定字符串的某些字符来得到。如果答案不止一个，返回长度最长且字典顺序最小的字符串。如果答案不存在，则返回空字符串。

```java
class Solution {
    public String findLongestWord(String s, List<String> dictionary) {
        String ans = "";
        for (String cur : dictionary) {
            if (isSubsequence(cur, s)) {
                if (cur.length() > ans.length() || (cur.length() == ans.length() && cur.compareTo(ans) < 0)) {
                    ans = cur;
                }
            }
        }
        return ans;
    }

    private static boolean isSubsequence(String x, String y) {
        int idx = 0;
        for (int i = 0; i < y.length() && idx < x.length(); i++) {
            if (x.charAt(idx) == y.charAt(i)) {
                idx++;
            }
        }
        return idx == x.length();
    }
}
```

## 字符串的排列（0567）

> *给定两个字符串 s1 和 s2，写一个函数来判断 s2 是否包含 s1 的排列。*
>
> *换句话说，第一个字符串的排列之一是第二个字符串的子串。*

```java
class Solution {
    public boolean checkInclusion(String s1, String s2) {
        Map<Character, Integer> need = new HashMap<>();
        Map<Character, Integer> window = new HashMap<>();
        for (int i = 0; i < s1.length(); i++) {
            char c = s1.charAt(i);
            need.put(c, need.getOrDefault(c, 0) + 1);
        }

        int left = 0, right = 0;
        int valid = 0;

        while (right < s2.length()) {
            char c = s2.charAt(right);
            right++;
            if (need.containsKey(c)) {
                window.put(c, window.getOrDefault(c, 0) + 1);
                if (window.get(c) - need.get(c) == 0) {valid++;}
            }

            while (right - left >= s1.length()) {
                if (valid == need.size()) {return true;}
                char d = s2.charAt(left);
                left++;
                if (need.containsKey(d)) {
                    if (window.get(d) - need.get(d) == 0) {valid--;}
                    window.put(d, window.getOrDefault(d, 0) - 1);
                }
            }
        }

        return false;
    }
}
```

## 删除字符串中的所有相邻重复项（1047）

> *给出由小写字母组成的字符串 S，重复项删除操作会选择两个相邻且相同的字母，并删除它们。*
>
> *在 S 上反复执行重复项删除操作，直到无法继续删除。*
>
> *在完成所有重复项删除操作后返回最终的字符串。答案保证唯一。*

```java
// 递归
class Solution {
    public String removeDuplicates(String S) {
        int n = S.length();
        for (int i = 1; i < n; i++){
            if (S.charAt(i - 1) == S.charAt(i)){
                return removeDuplicates(S.substring(0, i - 1) + removeDuplicates(S.substring(i + 1)));
            }
        }
        return S;
    }
}

// 栈
class Solution {
    public String removeDuplicates(String S) { 
        StringBuilder sb = new StringBuilder();
        int sbLength = 0;
        for (char c : S.toCharArray()) {
            if (sbLength != 0 && c == sb.charAt(sbLength - 1)) {
                sb.deleteCharAt(sbLength - 1);
                sbLength--;
            }
            else {
                sb.append(c);
                sbLength++;
            }
        }
        return sb.toString();
    }
}
```

