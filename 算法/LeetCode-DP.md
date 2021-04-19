[TOC]

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

## 最长递增子序列（0300）

> *给你一个整数数组 nums ，找到其中最长严格递增子序列的长度。*
>
> *子序列是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，[3,6,2,7] 是数组 [0,3,1,6,2,2,7] 的子序列。*

```java
// DP
class Solution {
    public int lengthOfLIS(int[] nums) {
        int n = nums.length;
        int[] dp = new int[n];
        Arrays.fill(dp, 1);

        for (int i = 0; i < n; i++){
            for (int j = 0; j < i; j++){
                if (nums[i] > nums[j]){
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
        }

        int ans = 0;
        for (int i = 0; i < n; i++){
            ans = Math.max(ans, dp[i]);
        }
        return ans;
    }
}

// 二分
class Solution {
    public int lengthOfLIS(int[] nums) {
        int[] top = new int[nums.length];
        
        int piles = 0;
        for (int i = 0; i < nums.length; i++){
            int poker = nums[i];

            int left = 0, right = piles;
            while (left < right){
                int mid = (left + right) / 2;
                if (top[mid] > poker){
                    right = mid;
                } else if (top[mid] < poker){
                    left = mid + 1;
                }else {
                    right = mid;
                }
            }

            if (left == piles) {piles++;}
            top[left] = poker;
        }
        return piles;
    }
}
```

## 最长公共子序列（1143）

> *给定两个字符串 text1 和 text2，返回这两个字符串的最长公共子序列的长度。*
>
> *一个字符串的 子序列 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。*
>
> *例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。两个字符串的「公共子序列」是这两个字符串所共同拥有的子序列。*
>
> *若这两个字符串没有公共子序列，则返回 0。*

```java
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        int m = text1.length(), n = text2.length();
        int[][] dp = new int[m + 1][n + 1];
        for (int i = 0; i <= m; i++){
            for (int j = 0; j <= n; j++){
                if (i == 0 || j == 0) {
                    dp[i][j] = 0;
                    continue;
                }
                // 若当前对比的字符相同，则直接dp[i-1][j-1]+1
                if (text1.charAt(i - 1) == text2.charAt(j - 1)){
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                }
                // 若不相同，则max(dp[i - 1][j], dp[i][j - 1])
                else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[m][n];
    }
}
```

## 两个字符串的删除操作（0583）

> *给定两个单词 word1 和 word2，找到使得 word1 和 word2 相同所需的最小步数，每步可以删除任意一个字符串中的一个字符。*

```java
// 最长公共子序列（超时）
class Solution {
    public int minDistance(String word1, String word2) {
        return word1.length() + word2.length() - 2 * lcs(word1, word2, word1.length(), word2.length());
    }

    private static int lcs(String s1, String s2, int m, int n) {
        if (m == 0 || n == 0) {return 0;}
        if (s1.charAt(m - 1) == s2.charAt(n - 1)) {
            return 1 + lcs(s1, s2, m - 1, n - 1);
        }
        else {
            return Math.max(lcs(s1, s2, m, n - 1), lcs(s1, s2, m - 1, n));
        }
    }
}

// dp
class Solution {
    public int minDistance(String word1, String word2) {
        int[][] dp = new int[word1.length() + 1][word2.length() + 1];

        for (int i = 0; i <= word1.length(); i++) {
            for (int j = 0; j <= word2.length(); j++) {
                if (i == 0 || j == 0) {
                    dp[i][j] = i + j;
                }
                else if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                }
                else {
                    dp[i][j] = 1 + Math.min(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }

        return dp[word1.length()][word2.length()];
    }
}
```

## 最长回文子序列（0516）

> 给定一个字符串 `s` ，找到其中最长的回文子序列，并返回该序列的长度。可以假设 `s` 的最大长度为 `1000` 。

```java
class Solution {
    public int longestPalindromeSubseq(String s) {
        int n = s.length();
        int[][] dp = new int[n][n];
        for (int i = 0; i < n; i++) {dp[i][i] = 1;}

        for (int i = n - 2; i >= 0; i--) {
            for (int j = i + 1; j < n; j++) {
                if (s.charAt(i) == s.charAt(j)) {
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

## 最小路径和（0064）

> 给定一个包含非负整数的 `*m* x *n*` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。
>
> **说明：**每次只能向下或者向右移动一步。

```java
class Solution {
    public int minPathSum(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] dp = new int[m][n];
        dp[0][0] = grid[0][0];
        for (int i = 0; i < m; i++) {
            if (i == 0) {continue;}
            else {dp[i][0] = dp[i - 1][0] + grid[i][0];}
        }
        for (int j = 0; j < n; j++) {
            if (j == 0) {continue;}
            else {dp[0][j] = dp[0][j - 1] + grid[0][j];}
        }

        if (m == 1 || n == 1) {return dp[m - 1][n - 1];}
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = Math.min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j];
            }
        }
        return dp[m - 1][n - 1];
    }
}
```

## 三角形最小路径和（0120）

> *给定一个三角形 triangle ，找出自顶向下的最小路径和。*

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int n = triangle.size();
        int[][] dp = new int[n][n];
        dp[0][0] = triangle.get(0).get(0);
        for (int i = 1; i < n; i++) {
            for (int j = 0; j <= i; j++) {
                if (j == 0) {
                    dp[i][j] = triangle.get(i).get(j) + dp[i - 1][j];
                }
                else if (j == i) {
                    dp[i][j] = triangle.get(i).get(j) + dp[i - 1][j - 1];
                }
                else {
                    dp[i][j] = triangle.get(i).get(j) + Math.min(dp[i - 1][j - 1], dp[i - 1][j]);
                }
            }
        }

        int ans = Integer.MAX_VALUE;
        for (int i = 0; i < n; i++) {
            if (dp[n - 1][i] < ans) {
                ans = dp[n - 1][i];
            }
        }

        return ans;
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

## 计算各个位数不同的数字个数（0357）

> *给定一个非负整数 n，计算各位数字都不同的数字 x 的个数，其中 0 ≤ x < 10n 。*

```java
class Solution {
    public int countNumbersWithUniqueDigits(int n) {
        //个位为0，
        if(n == 0) {return 1;}
        int ans = 0;
        int[] dp = new int[n];
        dp[0] = 9;
        //分别计算 i位数 不包含0时对应的数量
        for (int i = 1; i < n; i++) {
            dp[i] = dp[i - 1] * (9 - i);
        }
        //最终结果加上不包含0的情况
        for (int i = 0; i < n ; i++) {
            ans += dp[i];
        }
        //最终结果加上包含0的情况
        for (int i = 2; i <= n; i++) {
            ans += dp[i - 2] * (i - 1);
        }
        //加上0这种特殊情况
        return ans + 1;
    }
}
```

## 乘积最大子数组（0152）

> *给你一个整数数组 nums ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。*

```java
class Solution {
    public int maxProduct(int[] nums) {
        int n = nums.length;
        int[] maxDp = new int[n];
        int[] minDp = new int[n];
        maxDp[0] = nums[0];
        minDp[0] = nums[0];

        int ans = nums[0];
        for (int i = 1; i < n; i++) {
            maxDp[i] = Math.max(nums[i], Math.max(nums[i] * maxDp[i - 1], nums[i] * minDp[i - 1]));
            minDp[i] = Math.min(nums[i], Math.min(nums[i] * maxDp[i - 1], nums[i] * minDp[i - 1]));
            ans = Math.max(ans, maxDp[i]);
        }

        return ans;
    }
}
```

## 地下城游戏（0174）

> *编写一个函数来计算确保骑士能够拯救到公主所需的最低初始健康点数。*

```java
class Solution {
    public int calculateMinimumHP(int[][] dungeon) {
        int m = dungeon.length, n = dungeon[0].length;
        int[][] dp = new int[m + 1][n + 1];
        for (int i = 0; i <= m; ++i) {Arrays.fill(dp[i], Integer.MAX_VALUE);}
        dp[m][n - 1] = dp[m - 1][n] = 1;

        // needMin + globalDun[i][j] = Math.min(dp[i + 1][j], dp[i][j + 1])
        for (int i = m - 1; i >= 0; i--) {
            for (int j = n - 1; j >= 0; j--) {
                int minn = Math.min(dp[i + 1][j], dp[i][j + 1]);
                dp[i][j] = Math.max(minn - dungeon[i][j], 1);
            }
        }

        return dp[0][0];
    }
}
```

## 最大正方形（0221）

> *在一个由 '0' 和 '1' 组成的二维矩阵内，找到只包含 '1' 的最大正方形，并返回其面积。*

```java
class Solution {
    public int maximalSquare(char[][] matrix) {
        int ans = 0;
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {return 0;}

        int m = matrix.length, n = matrix[0].length;
        int[][] dp = new int[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == '1') {
                    if (i == 0 || j == 0) {dp[i][j] = 1;}
                    else {
                        dp[i][j] = Math.min(Math.min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1;
                    }
                    ans = Math.max(ans, dp[i][j]);
                }
            }
        }

        return ans * ans;
    }
}
```

## 二维区域检索求和（0304）

> 给定一个二维矩阵，计算其子矩形范围内元素的总和，该子矩阵的左上角为 `(row1, col1)` ，右下角为 `(row2, col2)` 。

```java
class NumMatrix {
    int[][] dp;

    public NumMatrix(int[][] matrix) {
        int m = matrix.length;
        if (m > 0) {
            int n = matrix[0].length;
            dp = new int[m + 1][n + 1];
            dp[1][1] = matrix[0][0];
            for (int i = 1; i < m; i++) {dp[i + 1][1] = dp[i][1] + matrix[i][0];}
            for (int j = 1; j < n; j++) {dp[1][j + 1] = dp[1][j] + matrix[0][j];}
            for (int i = 1; i < m; i++) {
                for (int j = 1; j < n; j++) {
                    dp[i + 1][j + 1] = matrix[i][j] + dp[i][j + 1] + dp[i + 1][j] - dp[i][j];
                }
            }
        }
    }
    
    public int sumRegion(int row1, int col1, int row2, int col2) {
        return dp[row2 + 1][col2 + 1] + dp[row1][col1] - dp[row2 + 1][col1] - dp[row1][col2 + 1];
    }
}

/**
 * Your NumMatrix object will be instantiated and called as such:
 * NumMatrix obj = new NumMatrix(matrix);
 * int param_1 = obj.sumRegion(row1,col1,row2,col2);
 */
```

## 零钱兑换

> *给定不同面额的硬币 coins 和一个总金额 amount。*
>
> *编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。*
>
> *可以认为每种硬币的数量是无限的。*

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, amount + 1);
        dp[0] = 0;

        for (int coin : coins) {
            for (int i = coin; i <= amount; i++) {
                dp[i] = Math.min(dp[i], dp[i - coin] + 1);
            }
        }

        return dp[amount] > amount ? -1 : dp[amount];
    }
}
```

> *给定不同面额的硬币和一个总金额。写出函数来计算可以凑成总金额的硬币组合数。假设每一种面额的硬币有无限个。* 

```java
class Solution {
    public int change(int amount, int[] coins) {
        int[] dp = new int[amount + 1];
        dp[0] = 1;
        for (int coin : coins) {
            for (int i = coin; i <= amount; i++) {
                dp[i] += dp[i - coin];
            }
        }

        return dp[amount];
    }
}
```

## 俄罗斯套娃（0354）

> *给定一些标记了宽度和高度的信封，宽度和高度以整数对形式 (w, h) 出现。当另一个信封的宽度和高度都比这个信封大的时候，这个信封就可以放进另一个信封里，如同俄罗斯套娃一样。*
>
> *请计算最多能有多少个信封能组成一组“俄罗斯套娃”信封（即可以把一个信封放到另一个信封里面）。*

```java
class Solution {
    public int maxEnvelopes(int[][] envelopes) {
        int n = envelopes.length;
        Arrays.sort(envelopes, new Comparator<int[]>(){
            public int compare(int[] a, int[] b){
                return a[0] == b[0] ? b[1] - a[1] : a[0] - b[0];
            }
        });

        int[] height = new int[n];
        for (int i = 0;i < n; i++){
            height[i] = envelopes[i][1];
        }
        return lengthOfLIS(height);
    }

    public int lengthOfLIS(int[] height){
        int n = height.length;
        int[] dp = new int[n];
        Arrays.fill(dp, 1);

        for (int i = 0; i < n; i++){
            for (int j = 0; j < i; j++){
                if (height[i] > height[j]){
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
        }

        int ans = 0;
        for (int i = 0; i < n; i++){
            ans = Math.max(ans, dp[i]);
        }
        return ans;
    }
} 
```

## 矩形区域不超过 K 的最大数值和（0363）

> *给定一个非空二维矩阵 matrix 和一个整数 k，找到这个矩阵内部不大于 k 的最大矩形和。*

```java
class Solution {
    public int maxSumSubmatrix(int[][] matrix, int k) {
        int m = matrix.length, n = matrix[0].length;
        int ans = Integer.MIN_VALUE;
        
        for (int i1 = 1; i1 <= m; i1++) {
            for (int j1 = 1; j1 <= n; j1++) {
                int[][] dp = new int[m + 1][n + 1];
                dp[i1][j1] = matrix[i1 - 1][j1 - 1];
                
                for (int i2 = i1; i2 <= m; i2++) {
                    for (int j2 = j1; j2 <= n; j2++) {
                        dp[i2][j2] = dp[i2 - 1][j2] + dp[i2][j2 - 1] - dp[i2 - 1][j2 - 1] + matrix[i2 - 1][j2 - 1];
                        if (dp[i2][j2] <= k && dp[i2][j2] > ans) {
                            ans = dp[i2][j2];
                        }
                    }
                }
            }
        }
        return ans;
    }
}
```

## 最大整除子集（0368）

> *给出一个由无重复的正整数组成的集合，找出其中最大的整除子集，子集中任意一对 (Si，Sj) 都要满足：Si % Sj = 0 或 Sj % Si = 0。*
>
> *如果有多个目标子集，返回其中任何一个均可。*

```java
class Solution {
    public List<Integer> largestDivisibleSubset(int[] nums) {
        int n = nums.length;
        if (n == 0) {return new ArrayList<>();}
        Arrays.sort(nums);

        List[] dp = new List[n];
        dp[0] = new ArrayList<>();
        dp[0].add(nums[0]);

        List<Integer> ans = dp[0];
        for (int i = 1; i < n; i++) {
            dp[i] = new ArrayList<>();
            dp[i].add(nums[i]);

            for (int j = 0; j < i; j++) {
                if (nums[i] % nums[j] == 0 && dp[i].size() < dp[j].size() + 1) {
                    dp[i] = new ArrayList(dp[j]);
                    dp[i].add(nums[i]);
                }
            }

            if (ans.size() < dp[i].size()) {ans = dp[i];}
        }

        return ans;
    }
}
```

## 猜数字大小（0375）

> *我们正在玩一个猜数游戏，游戏规则如下：*
>
> *我从 1 到 n 之间选择一个数字，你来猜我选了哪个数字。*
>
> *每次你猜错了，我都会告诉你，我选的数字比你的大了或者小了。*
>
> *然而，当你猜了数字 x 并且猜错了的时候，你需要支付金额为 x 的现金。直到你猜到我选的数字，你才算赢得了这个游戏。*

```java
class Solution {
    public int getMoneyAmount(int n) {
        if (n == 1) {return 0;}
        int[][] dp = new int[n + 1][n + 1];

        for (int i = 1; i < n + 1; i++) {
            for (int j = i - 1; j > 0; j--) {
                int min = Integer.MAX_VALUE, left = 0, right = 0;
                for (int k = i; k > j - 1; k--) {
                    left = (k == j || j == k - 1) ? -1 : dp[k - 1][j];
                    right = (k == i || i == k + 1) ? -1 : dp[i][k + 1];
                    min = (left == -1 && right == -1) ? k : Math.min(min, k + Math.max(left, right));
                }
                dp[i][j] = min;
            }
        }
        return dp[n][1];
    }
}
```

## 摆动序列（0376）

> 如果连续数字之间的差严格地在正数和负数之间交替，则数字序列称为摆动序列。第一个差（如果存在的话）可能是正数或负数。少于两个元素的序列也是摆动序列。
>
> 例如， [1,7,4,9,2,5] 是一个摆动序列，因为差值 (6,-3,5,-7,3) 是正负交替出现的。相反, [1,4,7,2,5] 和 [1,7,4,5,5] 不是摆动序列，第一个序列是因为它的前两个差值都是正数，第二个序列是因为它的最后一个差值为零。
>
> 给定一个整数序列，返回作为摆动序列的最长子序列的长度。 通过从原始序列中删除一些（也可以不删除）元素来获得子序列，剩下的元素保持其原始顺序。
>

```java
class Solution {
    public int wiggleMaxLength(int[] nums) {
        int n = nums.length;
        if (n < 2) {return n;}

        int[] up = new int[n];
        int[] down = new int[n];
        up[0] = down[0] = 1;
        for (int i = 1; i < n; i++) {
            if (nums[i] > nums[i - 1]) {
                up[i] = Math.max(up[i - 1], down[i - 1] + 1);
                down[i] = down[i - 1];
            }
            else if (nums[i] < nums[i - 1]) {
                up[i] = up[i - 1];
                down[i] = Math.max(down[i - 1], up[i - 1] + 1);
            }
            else {
                up[i] = up[i - 1];
                down[i] = down[i - 1];
            }
        }

        return Math.max(up[n - 1], down[n - 1]);
    }
}
```

## 分割数组的最大值（0410）

> *给定一个非负整数数组 nums 和一个整数 m ，你需要将这个数组分成 m 个非空的连续子数组。*
>
> *设计一个算法使得这 m 个子数组各自和的最大值最小。*

```java
class Solution {
    public int splitArray(int[] nums, int m) {
        int n = nums.length;
        int[][] dp = new int[n + 1][m + 1];
        for (int i = 0; i <= n; i++) {Arrays.fill(dp[i], Integer.MAX_VALUE);}

        int[] sum = new int[n + 1];
        for (int i = 0; i < n; i++) {sum[i + 1] = sum[i] + nums[i];}

        dp[0][0] = 0;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= Math.min(i, m); j++) {
                for (int k = 0; k < i; k++) {
                    dp[i][j] = Math.min(dp[i][j], Math.max(dp[k][j - 1], sum[i] - sum[k]));
                }
            }
        }

        return dp[n][m];
    }
}
```

## 分割等和子集（0416）

> *给定一个只包含正整数的非空数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。*

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int n = nums.length;
        if (n < 2) {return false;}

        int sum = 0, maxNum = 0;
        for (int num : nums) {
            sum += num;
            maxNum = Math.max(maxNum, num);
        }
        if ((sum & 1) != 0) {return false;}
        int target = sum / 2;
        if (maxNum > target) {return false;}

        // dp[i][j]表示从数组的[0,i]下标范围内选取若干个正整数，是否存在一种方案的和为j
        boolean[][] dp = new boolean[n][target + 1];
        for (int i = 0; i < n; i++) {dp[i][0] = true;}
        dp[0][nums[0]] = true;

        for (int i = 1; i < n; i++) {
            int curNum = nums[i];
            for (int j = 1; j <= target; j++) {
                if (j >= curNum) {
                    dp[i][j] = dp[i - 1][j] || dp[i - 1][j - curNum];
                }
                else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }

        return dp[n - 1][target];
    }
}
```

## 一和零（0474）

> 给你一个二进制字符串数组 strs 和两个整数 m 和 n 。
>
> 请你找出并返回 strs 的最大子集的大小，该子集中 最多 有 m 个 0 和 n 个 1 。
>
> 如果 x 的所有元素也是 y 的元素，集合 x 是集合 y 的 子集 。

```java
class Solution {
    public int findMaxForm(String[] strs, int m, int n) {
        int[][] dp = new int[m + 1][n + 1];
        for (String s: strs) {
            int[] count = countString(s);
            for (int zeroes = m; zeroes >= count[0]; zeroes--) {
                for (int ones = n; ones >= count[1]; ones--) {
                    dp[zeroes][ones] = Math.max(dp[zeroes][ones], 1 + dp[zeroes - count[0]][ones - count[1]]);
                }
            }
        }
        return dp[m][n];
    }

    private static int[] countString(String s) {
        int[] dir = new int[2];
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '0') {dir[0]++;}
            else {dir[1]++;}
        }
        return dir;
    }
}
```

## 我能赢吗（0464）

> 在 "100 game" 这个游戏中，两名玩家轮流选择从 1 到 10 的任意整数，累计整数和，先使得累计整数和达到或超过 100 的玩家，即为胜者。
>
> 如果我们将游戏规则改为 “玩家不能重复使用整数” 呢？
>
> 例如，两个玩家可以轮流从公共整数池中抽取从 1 到 15 的整数（不放回），直到累计整数和 >= 100。
>

```java
class Solution {
    public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
        if ((1 + maxChoosableInteger) * maxChoosableInteger / 2 < desiredTotal) {return false;}
        return myMethod(maxChoosableInteger, desiredTotal, new Boolean[1 << maxChoosableInteger], 0);
    }

    private boolean myMethod(int maxChoosableInteger, int desiredTotal, Boolean[] dp, int state) {
        if (dp[state] != null) {return dp[state];}

        for (int i = 1; i <= maxChoosableInteger; i++) {
            int cur = 1 << (i - 1);
            if ((cur & state) != 0) {continue;}

            if (desiredTotal - i <= 0 || myMethod(maxChoosableInteger, desiredTotal - i, dp, state | cur) == false){
                return dp[state] = true;
            }
        }

        return dp[state] = false;
    }
}
```

## 环绕字符串中唯一的子字符串（0467）

> 把字符串 s 看作是“abcdefghijklmnopqrstuvwxyz”的无限环绕字符串，所以 s 看起来是这样的："...zabcdefghijklmnopqrstuvwxyzabcdefghijklmnopqrstuvwxyzabcd....". 
>
> 现在我们有了另一个字符串 p 。你需要的是找出 s 中有多少个唯一的 p 的非空子串，尤其是当你的输入是字符串 p ，你需要输出字符串 s 中 p 的不同的非空子串的数目。 
>

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

## 预测赢家（0486）

> *给定一个表示分数的非负整数数组。 玩家 1 从数组任意一端拿取一个分数，随后玩家 2 继续从剩余数组任意一端拿取分数，然后玩家 1 拿，…… 。每次一个玩家只能拿取一个分数，分数被拿取之后不再可取。直到没有剩余分数可取时游戏结束。最终获得分数总和最多的玩家获胜。*
>
> *给定一个表示分数的数组，预测玩家1是否会成为赢家。你可以假设每个玩家的玩法都会使他的分数最大化。*

```java
// 回溯
class Solution {
    public boolean PredictTheWinner(int[] nums) {
        return myMethod(nums, 0, nums.length - 1, 1) >= 0;
    }

    private int myMethod(int[] nums, int start, int end, int turn) {
        if (start == end) {
            return nums[start] * turn;
        }
        int scoreStart = nums[start] * turn + myMethod(nums, start + 1, end, -turn);
        int scoreEnd = nums[end] * turn + myMethod(nums, start, end - 1, -turn);
        
        if(turn == 1) {return Math.max(scoreStart, scoreEnd);}
        else {return Math.min(scoreStart, scoreEnd);}
    }
}

// DP : dp[i][j]表示当数组剩下的部分为下标i到j时，当前玩家与另一个玩家的分数之差的最大值
class Solution {
    public boolean PredictTheWinner(int[] nums) {
        int n = nums.length;
        int[][] dp = new int[n][n];
        for (int i = 0; i < n; i++) {dp[i][i] = nums[i];}

        for (int i = n - 2; i >= 0; i--) {
            for (int j = i + 1; j < n; j++) {
                dp[i][j] = Math.max(nums[i] - dp[i + 1][j], nums[j] - dp[i][j - 1]);
            }
        }

        return dp[0][n - 1] >= 0;
    }
}
```

## 目标和（0494）

> 给定一个非负整数数组，a1, a2, ..., an, 和一个目标数，S。现在你有两个符号 + 和 -。对于数组中的任意一个整数，你都可以从 + 或 -中选择一个符号添加在前面。
>
> 返回可以使最终数组和为目标数 S 的所有添加符号的方法数。
>

```java
class Solution {
    public int findTargetSumWays(int[] nums, int S) {
        int[][] dp = new int[nums.length][2001];
        dp[0][nums[0] + 1000] = 1;
        dp[0][-nums[0] + 1000] += 1;

        for (int i = 1; i < nums.length; i++) {
            for (int sum = -1000; sum <= 1000; sum++) {
                if (dp[i - 1][sum + 1000] > 0) {
                    dp[i][sum + nums[i] + 1000] += dp[i - 1][sum + 1000];
                    dp[i][sum - nums[i] + 1000] += dp[i - 1][sum + 1000];
                }
            }
        }

        return S > 1000 ? 0 : dp[nums.length - 1][S + 1000];
    }
}
```

## 移除盒子（0546）

> *给出一些不同颜色的盒子，盒子的颜色由数字表示，即不同的数字表示不同的颜色。*
>
> *你将经过若干轮操作去去掉盒子，直到所有的盒子都去掉为止。每一轮你可以移除具有相同颜色的连续 k 个盒子（k >= 1），这样一轮之后你将得到 k \* k 个积分。*
>
> *当你将所有盒子都去掉之后，求你能获得的最大积分和。*

```java
class Solution {
    int[][][] dp;

    public int removeBoxes(int[] boxes) {
        int n = boxes.length;
        dp = new int[n][n][n];
        return calculate(boxes, 0, n - 1, 0);
    }

    private int calculate(int[] boxes, int left, int right, int k) {
        if (left > right) {return 0;}

        if (dp[left][right][k] == 0) {
            dp[left][right][k] = calculate(boxes, left, right - 1, 0) + (k + 1) * (k + 1);
            for (int i = left; i < right; i++) {
                if (boxes[i] == boxes[right]) {
                    dp[left][right][k] = Math.max(dp[left][right][k], calculate(boxes, left, i, k + 1) + calculate(boxes, i + 1, right - 1, 0));
                }
            }
        }

        return dp[left][right][k];
    }
}
```

## 学生出勤记录

> *给定一个字符串来代表一个学生的出勤记录，这个记录仅包含以下三个字符：*
>
> *'A' : Absent，缺勤*
>
> *'L' : Late，迟到*
>
> *'P' : Present，到场*
>
> *如果一个学生的出勤记录中不超过一个'A'(缺勤)并且不超过两个连续的'L'(迟到),那么这个学生会被奖赏。*
>
> *你需要根据这个学生的出勤记录判断他是否会被奖赏。*

```java
class Solution {
    public boolean checkRecord(String s) {
        int countA = 0;
        int preL = 0;
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == 'A') {
                countA++;
                if (countA > 1) {return false;}
            }
            else if (s.charAt(i) == 'L') {
                if (preL == i - 1 && (i > 1 && s.charAt(i - 2) == 'L')) {
                    return false;
                }
                preL = i;
            }
        }
        return true;
    }
}
```

> *给定一个正整数 n，返回长度为 n 的所有可被视为可奖励的出勤记录的数量。 答案可能非常大，你只需返回结果mod 109 + 7的值。*
>
> *学生出勤记录是只包含以下三个字符的字符串：*
>
> *'A' : Absent，缺勤*
>
> *'L' : Late，迟到*
>
> *'P' : Present，到场*
>
> *如果记录不包含多于一个'A'（缺勤）或超过两个连续的'L'（迟到），则该记录被视为可奖励的。*

```java
class Solution {
    private static final long MOD = 1000000007;

    public int checkRecord(int n) {
        long[] dp = new long[n <= 5 ? 6 : n + 1];
        dp[0] = 1;
        dp[1] = 2;
        dp[2] = 4;
        dp[3] = 7;
        
        for (int i = 4; i <= n; i++) {
            dp[i] = ((2 * dp[i - 1]) % MOD + (MOD - dp[i - 4])) % MOD;
        }

        long sum = dp[n];
        for (int i = 1; i <= n; i++) {
            sum += (dp[i - 1] * dp[n - i]) % MOD;
        }

        return (int)(sum % MOD);
    }
}
```

## 最长湍流子数组（0978）

> *当 A 的子数组 A[i], A[i+1], ..., A[j] 满足下列条件时，我们称其为湍流子数组：*
>
> *若 i <= k < j，当 k 为奇数时， A[k] > A[k+1]，且当 k 为偶数时，A[k] < A[k+1]；*
>
> *或 若 i <= k < j，当 k 为偶数时，A[k] > A[k+1] ，且当 k 为奇数时， A[k] < A[k+1]。*
>
> *也就是说，如果比较符号在子数组中的每个相邻元素对之间翻转，则该子数组是湍流子数组。*
>
> *返回 A 的最大湍流子数组的长度。*

```java
class Solution {
    public int maxTurbulenceSize(int[] arr) {
        if (arr.length == 1) {return 1;}
        int n = arr.length;
        int[][] dp = new int[n][2];
        dp[0][0] = dp[0][1] = 1;

        for (int i = 1; i < n; i++) {
            dp[i][0] = dp[i][1] = 1;
            if (arr[i - 1] > arr[i]) {
                dp[i][0] = dp[i - 1][1] + 1;
            }
            else if (arr[i - 1] < arr[i]) {
                dp[i][1] = dp[i - 1][0] + 1;
            }
        }

        int ans = 1;
        for (int i = 0; i < n; i++) {
            ans = Math.max(ans, Math.max(dp[i][0], dp[i][1]));
        }
        return ans;
    }
}
```

## 给N*3网格图涂色（1411）

> *你有一个 n x 3 的网格图 grid ，你需要用 红，黄，绿 三种颜色之一给每一个格子上色，且确保相邻格子颜色不同*
>
> *（也就是有相同水平边或者垂直边的格子颜色不同）。*
>
> *给你网格图的行数 n 。*
>
> *请你返回给 grid 涂色的方案数。由于答案可能会非常大，请你返回答案对 10^9 + 7 取余的结果。*

```java
class Solution {
    public int numOfWays(int n) {
        long dp2 = 6, dp3 = 6, mod = 1000000007;
        for(int i = 1; i < n; i++) {
            long temp2 = dp2;
            long temp3 = dp3;
            // 若当前使用 2 种颜色组成网格
            // 如果上一个网格是 2 种颜色，则当前网格有 3 种可能
            // 如果上一个网格是 3 种颜色，则当前网格有 2 种可能
            dp2 = (temp2 * 3 % mod + temp3 * 2 % mod) % mod;
            // 若当前使用 3 种颜色组成网格
            // 如果上一个网格是 2 种颜色，则当前网格有 2 种可能
            // 如果上一个网格是 3 种颜色，则当前网格有 2 种可能
            dp3 = (temp2 * 2 % mod + temp3 * 2 % mod) % mod;
        }
        return (int)((dp2 + dp3) % mod);
    }
}
```

