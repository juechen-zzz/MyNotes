[TOC]

## 电话号码的字母组合（0017）

> *给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。*
>
> *给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。*
>
> *输入："23"*
>
> *输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].*

```java
class Solution {
    public String[] map = {"abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
    public StringBuilder sb = new StringBuilder();
    public List<String> ans = new ArrayList<>();

    public List<String> letterCombinations(String digits) {
        if (digits.length() == 0) {return ans;}
        backtrack(digits, 0);
        return ans;
    }

    public void backtrack(String digits, int index) {
        if (sb.length() == digits.length()) {
            ans.add(sb.toString());
            return;
        }

        String val = map[digits.charAt(index) - '2'];

        for (char ch : val.toCharArray()) {
            sb.append(ch);
            backtrack(digits, index + 1);
            sb.deleteCharAt(sb.length() - 1);
        }
    }
}
```

## 括号生成（0022）

> *数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 有效的 括号组合。*
>
> *输入：n = 3*
>
> *输出：[*
>
> ​       *"((()))",*
>
> ​       *"(()())",*
>
> ​       *"(())()",*
>
> ​       *"()(())",*
>
> ​       *"()()()"*
>
> ​     *]*

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> ans = new ArrayList<>();
        backtrack(ans, new StringBuilder(), 0, 0, n);
        return ans;
    }

    public void backtrack(List<String> ans, StringBuilder sb, int start, int end, int n) {
        if (sb.length() == 2 * n) {
            ans.add(sb.toString());
            return;
        }

        if (start < n) {
            sb.append('(');
            backtrack(ans, sb, start + 1, end, n);
            sb.deleteCharAt(sb.length() - 1);
        }

        if (end < start) {
            sb.append(')');
            backtrack(ans, sb, start, end + 1, n);
            sb.deleteCharAt(sb.length() - 1);
        }
    }
}
```

## 组合总数

> *给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。*
>
> *candidates 中的数字可以无限制重复被选取。*

```java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        Arrays.sort(candidates);
        List<List<Integer>> ans = new ArrayList<>();
        List<Integer> track = new ArrayList<>();
        backtrack(candidates, target, ans, track, 0, 0);
        return ans;
    }

    public void backtrack(int[] candidates, int target, List<List<Integer>> ans, List<Integer> track, int sum, int begin) {
        if (sum == target) {
            ans.add(new ArrayList(track));
            return;
        }
        for (int i = begin; i < candidates.length; i++) {
            if (i > begin && candidates[i] == candidates[i - 1]) {continue;}
            int curSum = candidates[i] + sum;
            if (curSum <= target) {
                track.add(candidates[i]);
                backtrack(candidates, target, ans, track, curSum, i);
                track.remove(track.size() - 1);
            }
            else {
                break;
            }
        }
    }
}
```

> *给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。*
>
> *candidates 中的每个数字在每个组合中只能使用一次。*

```java
class Solution {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates);
        List<List<Integer>> ans = new ArrayList<>();
        List<Integer> track = new ArrayList<>();
        backtrack(candidates, target, ans, track, 0, 0);
        return ans;
    }

    public void backtrack(int[] candidates, int target, List<List<Integer>> ans, List<Integer> track, int sum, int begin) {
        if (sum == target) {
            ans.add(new ArrayList(track));
            return;
        }

        for (int i = begin; i < candidates.length; i++) {
            if (i > begin && candidates[i] == candidates[i - 1]) {continue;}
            int curSum = candidates[i] + sum;
            if (curSum <= target) {
                track.add(candidates[i]);
                backtrack(candidates, target, ans, track, curSum, i + 1);
                track.remove(track.size() - 1);
            }
            else {
                break;
            }
        }
    }
}
```

> *找出所有相加之和为 n 的 k 个数的组合。组合中只允许含有 1 - 9 的正整数，并且每种组合中不存在重复的数字。*

```java
class Solution {
    List<List<Integer>> ans = new ArrayList<>();
    List<Integer> tmp = new ArrayList<>();

    public List<List<Integer>> combinationSum3(int k, int n) {
        dfs(k, n, 1, 9);
        return ans;
    }

    public void dfs(int k, int n, int start, int end) {
        if (tmp.size() + (end - start + 1) < k || tmp.size() > k) {return;}

        if (tmp.size() == k) {
            int sum = 0;
            for (int num : tmp) {sum += num;}
            if (sum == n) {
                ans.add(new ArrayList<>(tmp));
                return;
            }
        }
        
        dfs(k, n, start + 1, end);

        tmp.add(start);
        dfs(k, n, start + 1, end);
        tmp.remove(tmp.size() - 1);
    }
}
```

> *给你一个由 不同 整数组成的数组 nums ，和一个目标整数 target 。请你从 nums 中找出并返回总和为 target 的元素组合的个数。*

```java
class Solution {
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target + 1];
        dp[0] = 1;
        for (int i = 1; i <= target; i++) {
            for (int x : nums) {
                if (i >= x) {
                    dp[i] += dp[i - x];
                }
            }
        }
        return dp[target];
    }
}
```

> *给你一个由 不同 整数组成的数组 nums ，和一个目标整数 target 。请你从 nums 中找出并返回总和为 target 的元素组合的个数。*

```java
class Solution {
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target + 1];
        dp[0] = 1;
        for (int i = 1; i <= target; i++) {
            for (int x : nums) {
                if (i >= x) {
                    dp[i] += dp[i - x];
                }
            }
        }
        return dp[target];
    }
}
```

## 组合（0077）

> *给定两个整数 n 和 k，返回 1 ... n 中所有可能的 k 个数的组合。*

```java
class Solution {
    List<List<Integer>> ans = new ArrayList<>();
    List<Integer> tmp = new ArrayList<>();

    public List<List<Integer>> combine(int n, int k) {
        dfs(n, k, 1);
        return ans;
    }

    public void dfs(int n, int k, int cur) {
        if (tmp.size() + (n - cur + 1) < k) {return;}

        if (tmp.size() == k) {
            ans.add(new ArrayList<>(tmp));
            return;
        }

        tmp.add(cur);
        dfs(n, k, cur + 1);
        tmp.remove(tmp.size() - 1);

        dfs(n, k, cur + 1);
    }
}
```

## 子集（0078 && 0090）

> *给你一个整数数组 nums ，数组中的元素 互不相同 。返回该数组所有可能的子集（幂集）。*

```java
class Solution {
    List<List<Integer>> ans = new ArrayList<>();
    List<Integer> tmp = new ArrayList<>();

    public List<List<Integer>> subsets(int[] nums) {
        backtrack(nums, 0);
        return ans;
    }

    public void backtrack(int[] nums, int idx) {
        ans.add(new ArrayList<>(tmp));
        for (int i = idx; i < nums.length; i++) {
            tmp.add(nums[i]);
            backtrack(nums, i + 1);
            tmp.remove(tmp.size() - 1);
        }
    }
}
```

> *给定一个可能包含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。*
>
> *说明：解集不能包含重复的子集。*

```java
class Solution {
    List<List<Integer>> ans = new ArrayList<>();
    List<Integer> tmp = new ArrayList<>();

    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        backtrack(nums, 0);
        return ans;
    }

    public void backtrack(int[] nums, int idx) {
        Collections.sort(tmp);
        if (!ans.contains(tmp)) {ans.add(new ArrayList<>(tmp));}
        for (int i = idx; i < nums.length; i++) {
            tmp.add(nums[i]);
            backtrack(nums, i + 1);
            tmp.remove(tmp.size() - 1);
        }
    }
}
```

## 全排列（0046 && 0047）

> *给定一个 没有重复 数字的序列，返回其所有可能的全排列。*

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> ans = new ArrayList<>();
        List<Integer> track = new ArrayList<>();
        Arrays.sort(nums);
        backtrack(nums, ans, track);
        return ans;
    }

    public void backtrack(int[] nums, List<List<Integer>> ans, List<Integer> track) {
        if (track.size() == nums.length) {
            ans.add(new ArrayList(track));
            return;
        }

        for (int i = 0; i < nums.length; i++) {
            if (track.contains(nums[i])) {continue;}
            track.add(nums[i]);
            backtrack(nums, ans, track);
            track.remove(track.size() - 1);
        }
    }
}
```

> *给定一个可包含重复数字的序列 nums ，按任意顺序 返回所有不重复的全排列。*

```java
class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> ans = new ArrayList<>();
        List<Integer> track = new ArrayList<>();
        boolean[] visited = new boolean[nums.length];
        Arrays.sort(nums);
        backtrack(nums, ans, track, visited);
        return ans;
    }

    public void backtrack(int[] nums, List<List<Integer>> ans, List<Integer> track, boolean[] visited) {
        if (track.size() == nums.length) {
            ans.add(new ArrayList(track));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            if (visited[i] || (i > 0 && nums[i] == nums[i - 1] && !visited[i - 1])) {
                continue;
            }
            track.add(nums[i]);
            visited[i] = true;
            backtrack(nums, ans, track, visited);
            visited[i] = false;
            track.remove(track.size() - 1);
        }
    }
}
```

## 排列序列（0060）

> *给出集合 [1,2,3,...,n]，其所有元素共有 n! 种排列。*
>
> *按大小顺序列出所有排列情况，并一一标记，当 n = 3 时, 所有排列如下：*
>
> *给定 n 和 k，返回第 k 个排列。*

```java
class Solution {
    public String getPermutation(int n, int k) {
        k--;

        int [] factorial = new int[n];
        factorial[0] = 1;
        for (int i = 1; i < n; i++) {factorial[i] = factorial[i - 1] * i;}

        List<Integer> nums = new ArrayList<>();
        for (int i = 1; i <= n; i++) {nums.add(i);}

        StringBuilder ans = new StringBuilder();
        for (int i = n - 1; i >= 0; i--) {
            int index = k / factorial[i];
            ans.append(nums.remove(index));
            k -= index * factorial[i];
        }

        return ans.toString();
    }
}
```

## N皇后（0051 && 0052）

> *n 皇后问题研究的是如何将 n 个皇后放置在 n×n 的棋盘上，并且使皇后彼此之间不能相互攻击。*

```java
class Solution {
    List<List<String>> res = new ArrayList<>();					// int res = 0;

    public List<List<String>> solveNQueens(int n) {
        char[][] board = new char[n][n];
        for (char[] i : board){
            Arrays.fill(i, '.');                
        }
        backtrack(board, 0);
        return res;
    }

    // 路径：board 中小于 row 的那些行都已经成功放置了皇后
    // 选择列表：第 row 行的所有列都是放置皇后的选择
    // 结束条件：row 超过 board 的最后一行
    void backtrack(char[][] board, int row){
        if (row == board.length) {
            res.add(array2list(board));							// res++;
            return;
        }

        for (int col = 0; col < board.length; col++){
            if (!check(board, row, col)){
                continue;
            }
            board[row][col] = 'Q';
            backtrack(board, row + 1);
            board[row][col] = '.';
        }
    }

    List<String> array2list(char[][] board){
        List<String> res = new ArrayList<>();
        for (char[] i : board){
            StringBuilder s = new StringBuilder();
            for (char j : i){
                s.append(j);
            }
            res.add(s.toString());
        }
        return res;
    }

    boolean check(char[][] board, int row, int col){
        int n = board.length;
        // 检查列是否有皇后互相冲突
        for (int i = 0; i < n; i++) {
            if (board[i][col] == 'Q')
                return false;
        }
        // 检查右上方是否有皇后互相冲突
        for (int i = row - 1, j = col + 1; i >= 0 && j < n; i--, j++) {
            if (board[i][j] == 'Q')
                return false;
        }
        // 检查左上方是否有皇后互相冲突
        for (int i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--) {
            if (board[i][j] == 'Q')
                return false;
        }
        return true;
    }
}
```

## 单词搜索（0079）

> *给定一个二维网格和一个单词，找出该单词是否存在于网格中。*

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                if (board[i][j] == word.charAt(0)) {
                    if (dfs(board, word, 0, i, j)) {return true;}
                }
            }
        }
        return false;
    }

    public boolean dfs(char[][] board, String word, int idx, int row, int col) {
        if (row < 0 || row > board.length - 1 || col < 0 || col > board[0].length - 1) {return false;}
        if (board[row][col] == word.charAt(idx)) {
            board[row][col] = '0';
            idx++;
            if (idx == word.length() || dfs(board, word, idx, row + 1, col) || dfs(board, word, idx, row, col + 1) || dfs(board, word, idx, row - 1, col) || dfs(board, word, idx, row, col - 1)) {
                return true;
            }
            idx--;
            board[row][col] = word.charAt(idx);
        }
        return false;
    }
}
```

## 岛屿数量（0200）

> *给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。*
>
> *岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。*
>
> *此外，你可以假设该网格的四条边均被水包围。*
>
> *输入：grid = [*
>
> *["1","1","1","1","0"],*
>
> *["1","1","0","1","0"],*
>
> *["1","1","0","0","0"],*
>
> *["0","0","0","0","0"]*
>
> *]*
>
> *输出：1*

```java
class Solution {
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) {return 0;}

        int m = grid.length;
        int n = grid[0].length;
        int ans = 0;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    ans++;
                    dfs(grid, i, j);
                }
            }
        }

        return ans;
    }

    public void dfs(char[][] grid, int row, int col) {
        int m = grid.length;
        int n = grid[0].length;

        if (row < 0 || row >= m || col < 0 || col >= n || grid[row][col] == '0') {return;}

        grid[row][col] = '0';
        dfs(grid, row - 1, col);
        dfs(grid, row + 1, col);
        dfs(grid, row, col - 1);
        dfs(grid, row, col + 1);
    }
}

```

## 累加数（0306）

> *累加数是一个字符串，组成它的数字可以形成累加序列。*
>
> *一个有效的累加序列必须至少包含 3 个数。除了最开始的两个数以外，字符串中的其他数都等于它之前两个数相加的和。*
>
> *给定一个只包含数字 '0'-'9' 的字符串，编写一个算法来判断给定输入是否是累加数。*

```java
class Solution {
    public boolean isAdditiveNumber(String num) {
        if (num.length() < 3) {return false;}
        return dfs(num, num.length(), 0, 0, 0, 0);
    }

    /**
     * @param num    原始字符串
     * @param len    原始字符串长度
     * @param idx    当前处理下标
     * @param sum    前面的两个数字之和
     * @param pre    前一个数字
     * @param k      当前是处理的第几个数字
     */
    private boolean dfs(String num, int len, int idx, long sum, long pre, int k) {
        if (idx == len) {return k > 2;}

        for (int i = idx; i < len; i++) {
            long cur = getCurValue(num, idx, i);
            if (cur < 0 || (k >= 2 && cur != sum)) {continue;}
            if (dfs(num, len, i + 1, pre + cur, cur, k + 1)) {return true;}
        }
        return false;
    }

    private long getCurValue(String num, int left, int right) {
        if (left < right && num.charAt(left) == '0') {return -1;}
        long ans = 0;
        while (left <= right) {
            ans = ans * 10 + (num.charAt(left++) - '0');
        }
        return ans;
    }
}
```

## 矩阵中的最长递增路径（0329）

```java
class Solution {
    public int longestIncreasingPath(int[][] matrix) {
        if (matrix.length == 0) {return 0;}
        int[][] visited = new int[matrix.length][matrix[0].length];
        int ans = 0;

        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[0].length; j++) {
                if (visited[i][j] == 0) {
                    ans = Math.max(ans, dfs(matrix, visited, i, j));
                }
            }
        }

        return ans;
    }

    private int dfs(int[][] matrix, int[][] visited, int row, int col) {
        if (row < 0 || row >= matrix.length || col < 0 || col >= matrix[0].length) {return 0;}
        if (visited[row][col] > 0) {return visited[row][col];}

        int ans = 0;
        if (row - 1 >= 0 && matrix[row - 1][col] > matrix[row][col]) {
            ans = Math.max(ans, dfs(matrix, visited, row - 1, col));
        }
        if (row + 1 < matrix.length && matrix[row + 1][col] > matrix[row][col]) {
            ans = Math.max(ans, dfs(matrix, visited, row + 1, col));
        }
        if (col - 1 >= 0 && matrix[row][col - 1] > matrix[row][col]) {
            ans = Math.max(ans, dfs(matrix, visited, row, col - 1));
        }
        if (col + 1 < matrix[0].length && matrix[row][col + 1] > matrix[row][col]) {
            ans = Math.max(ans, dfs(matrix, visited, row, col + 1));
        }

        visited[row][col] = ans + 1;
        return ans + 1;
    }
}
```

## 字典树排数（0386）

> *给定一个整数 n, 返回从 1 到 n 的字典顺序。*
>
> *例如，*
>
> *给定 n =1 3，返回 [1,10,11,12,13,2,3,4,5,6,7,8,9] 。*

```java
class Solution {
    public List<Integer> lexicalOrder(int n) {
        List<Integer> ans = new ArrayList<>();
        for (int i = 1; i < 10; i++){
            dfs(n, i, ans);
        }
        return ans;
    }

    private void dfs(int n, int i, List<Integer> list){
        if(i > n) {return;}
        list.add(i);
        for(int j = 0; j <= 9; j++){
            dfs(n, i * 10 + j, list);
        }
    }
}
```

## 二进制手表（0401）

> *二进制手表顶部有 4 个 LED 代表 小时（0-11），底部的 6 个 LED 代表 分钟（0-59）。*
>
> *每个 LED 代表一个 0 或 1，最低位在右侧。*

```java
class Solution {
    List<String> ans = new ArrayList<>();
    int[] dic = new int[]{1, 2, 4, 8, 1, 2, 4, 8, 16, 32};

    public List<String> readBinaryWatch(int num) {
        dfs(num, 0, 0, 0);
        return ans;
    }

    private void dfs(int count, int hour, int minute, int index) {
        if (count == 0) {
            ans.add(hour + ":" + (minute > 9 ? minute : "0" + minute));
        }
        for (int i = index; i < 10; i++) {
            if (i < 4 && hour + dic[i] < 12) {
                dfs(count - 1, hour + dic[i], minute, i + 1);
            }
            if (i >= 4 && minute + dic[i] < 60) {
                dfs(count - 1, hour, minute + dic[i], i + 1);
            }
        }
    }
}
```

## 太平洋大西洋水流问题（0417）

> *给定一个 m x n 的非负整数矩阵来表示一片大陆上各个单元格的高度。“太平洋”处于大陆的左边界和上边界，而“大西洋”处于大陆的右边界和下边界。*
>
> *规定水流只能按照上、下、左、右四个方向流动，且只能从高到低或者在同等高度上流动。*
>
> *请找出那些水流既可以流动到“太平洋”，又能流动到“大西洋”的陆地单元的坐标。*

```java
class Solution {
    private int m, n;
    private int[][] dir = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
    private boolean inArea(int x, int y) {return 0 <= x && x < m && 0 <= y && y < n;}

    public List<List<Integer>> pacificAtlantic(int[][] heights) {
        List<List<Integer>> ans = new ArrayList<>();
        if (heights.length == 0) {return ans;}

        m = heights.length;
        n = heights[0].length;
        int[][] po = new int[m][n], ao = new int[m][n];

        for (int i = 0; i < m; i++) {
            dfs(heights, i, 0, po);
            dfs(heights, i, n - 1, ao);
        }
        for (int i = 0; i < n; i++) {
            dfs(heights, 0, i, po);
            dfs(heights, m - 1, i, ao);
        }

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (po[i][j] == 1 && ao[i][j] == 1){
                    ans.add(Arrays.asList(i, j));
                }
            }
        }
        return ans;
    }

    private void dfs(int[][] matrix, int x, int y, int[][] tmp){
        tmp[x][y] = 1;
        for (int[] d : dir) {
            int newx = x + d[0];
            int newy = y + d[1];
            if (!inArea(newx, newy) || matrix[x][y] > matrix[newx][newy] || tmp[newx][newy] == 1){
                continue;
            }
            dfs(matrix, newx, newy, tmp);
        }
    }
}
```

## 最小基因变化（0433）

> *一条基因序列由一个带有8个字符的字符串表示，其中每个字符都属于 "A", "C", "G", "T"中的任意一个。*
>
> *假设我们要调查一个基因序列的变化。一次基因变化意味着这个基因序列中的一个字符发生了变化。*
>
> *例如，基因序列由"AACCGGTT" 变化至 "AACCGGTA" 即发生了一次基因变化。*
>
> *与此同时，每一次基因变化的结果，都需要是一个合法的基因串，即该结果属于一个基因库。*
>
> *现在给定3个参数 — start, end, bank，分别代表起始基因序列，目标基因序列及基因库，请找出能够使起始基因序列变化为目标基因序列所需的最少变化次数。如果无法实现目标变化，请返回 -1。*

```java
class Solution {
    public int minMutation(String start, String end, String[] bank) {
        Set<String> set = new HashSet<>(Arrays.asList(bank));
        if (!set.contains(end)) {return -1;}
        char[] dir = {'A', 'C', 'G', 'T'};

        Queue<String> queue = new LinkedList<>();
        queue.offer(start);
        set.remove(start);
        int step = 0;

        while (queue.size() > 0) {
            step++;
            int count = queue.size();
            for (int k = 0; k < count; k++) {
                char[] tmp = queue.poll().toCharArray();
                int n = tmp.length;
                for (int i = 0; i < n; i++) {
                    char oldChar = tmp[i];
                    for (int j = 0; j < 4; j++) {
                        tmp[i] = dir[j];
                        String newGenetic = new String(tmp);
                        if (end.equals(newGenetic)) {
                            return step;
                        }
                        else if (set.contains(newGenetic)) {
                            set.remove(newGenetic);
                            queue.offer(newGenetic);
                        }
                    }
                    tmp[i] = oldChar;
                }
            }
        }

        return -1;
    }
}
```

## 火柴拼正方形（0473）

> 请找出一种能使用所有火柴拼成一个正方形的方法。不能折断火柴，可以把火柴连接起来，并且每根火柴都要用到。
>
> 输入为小女孩拥有火柴的数目，每根火柴用其长度表示。输出即为是否能用所有的火柴拼成正方形。
>

```java
class Solution {
    public boolean makesquare(int[] nums) {
        int sum = 0;
        for (int n : nums) {sum += n;}
        if (sum == 0 || sum % 4 != 0) {return false;}
        return myMethod(nums, sum >> 2, new int[4], 0);
    }

    private static boolean myMethod(int[] nums, int target, int[] edges, int index) {
        if (index == nums.length) {
            if (edges[0] == edges[1] && edges[1] == edges[2] && edges[2] == edges[3]) {return true;}
            return false;
        }

        for (int i = 0; i < 4; i++) {
            if (edges[i] + nums[index] > target) {continue;}

            edges[i] += nums[index];
            if (myMethod(nums, target, edges, index + 1)) {return true;}
            edges[i] -= nums[index];
        }

        return false;
    }
}
```

## 递增子序列（0491）

> *给定一个整型数组, 你的任务是找到所有该数组的递增子序列，递增子序列的长度至少是 2 。*

```java
class Solution {
    List<List<Integer>> ans = new ArrayList<>();
    List<Integer> tmp = new ArrayList<>();

    public List<List<Integer>> findSubsequences(int[] nums) {
        dfs(nums, 0, Integer.MIN_VALUE);
        return ans;
    }

    private void dfs(int[] nums, int cur, int end) {
        if (cur == nums.length) {
            if (tmp.size() >= 2) {
                ans.add(new ArrayList<>(tmp));
            }
            return;
        }

        // 使序列合法的办法非常简单，即给「选择」做一个限定条件，只有当前的元素大于等于上一个选择的元素的时候才能选择这个元素，这样枚举出来的所有元素都是合法的
        if (nums[cur] >= end) {
            tmp.add(nums[cur]);
            dfs(nums, cur + 1, nums[cur]);
            tmp.remove(tmp.size() - 1);
        }

        // 那如何保证没有重复呢？我们需要给「不选择」做一个限定条件，只有当当前的元素不等于上一个选择的元素的时候，才考虑不选择当前元素，直接递归后面的元素。
        if (nums[cur] != end) {
            dfs(nums, cur + 1, end);
        }
    }
}
```

