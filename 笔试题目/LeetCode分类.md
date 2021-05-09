[TOC]

# 1 哈希表

## 两数之和（0001）

> *给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。*
>
> *给定 nums = [2, 7, 11, 15], target = 9*
>
> *因为 nums[0] + nums[1] = 2 + 7 = 9*
>
> *所以返回 [0, 1]*

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++){
            int n = target - nums[i];
            if (map.containsKey(n)){
                return new int[]{map.get(n), i};
            }
            else {
                map.put(nums[i], i);
            }
        }
        return new int[0];
    }
}
```

## 有效的数独（0036）

> *判断一个 9x9 的数独是否有效。只需要根据以下规则，验证已经填入的数字是否有效即可。*
>
> *数字 1-9 在每一行只能出现一次。*
>
> *数字 1-9 在每一列只能出现一次。*
>
> *数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。*

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        HashMap<Integer, Integer>[] row = new HashMap[9];
        HashMap<Integer, Integer>[] col = new HashMap[9];
        HashMap<Integer, Integer>[] box = new HashMap[9];
        for (int i = 0; i < 9; i++) {
            row[i] = new HashMap<Integer, Integer>();
            col[i] = new HashMap<Integer, Integer>();
            box[i] = new HashMap<Integer, Integer>();
        }

        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                char num = board[i][j];
                if (num != '.') {
                    int n = (int)num;
                    int boxIndex = (i / 3) * 3 + j / 3;

                    row[i].put(n, row[i].getOrDefault(n, 0) + 1);
                    col[j].put(n, col[j].getOrDefault(n, 0) + 1);
                    box[boxIndex].put(n, box[boxIndex].getOrDefault(n, 0) + 1);

                    if (row[i].get(n) > 1 || col[j].get(n) > 1 || box[boxIndex].get(n) > 1) {return false;}
                }
            }
        }

        return true;
    }
}
```

## 缺失的第一个正数（0041）

> *给你一个未排序的整数数组 nums ，请你找出其中没有出现的最小的正整数。*
>
> *进阶：你可以实现时间复杂度为 O(n) 并且只使用常数级别额外空间的解决方案吗？*

```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        int n = nums.length;
        for (int i = 0; i < n; ++i) {
            if (nums[i] <= 0) {
                nums[i] = n + 1;
            }
        }
        for (int i = 0; i < n; ++i) {
            int num = Math.abs(nums[i]);
            if (num <= n) {
                nums[num - 1] = -Math.abs(nums[num - 1]);
            }
        }
        for (int i = 0; i < n; ++i) {
            if (nums[i] > 0) {
                return i + 1;
            }
        }
        return n + 1;
    }
}
```

## 单词规律（0290）

> *给定一种规律 pattern 和一个字符串 str ，判断 str 是否遵循相同的规律。*
>
> *这里的 遵循 指完全匹配，例如， pattern 里的每个字母和字符串 str 中的每个非空单词之间存在着双向连接的对应规律。*

```java
class Solution {
    public boolean wordPattern(String pattern, String s) {
        Map<Character, String> map = new HashMap<>();
        String[] strs = s.split(" ");
        int n = strs.length;
        if (n != pattern.length()) {return false;}

        for (int i = 0; i < n; i++) {
            char c = pattern.charAt(i);
            if (!map.containsKey(c)) {
                if (!map.containsValue(strs[i])) {
                    map.put(c, strs[i]);
                }
                else {
                    return false;
                }
            }
            else if (!map.get(c).equals(strs[i])) {
                return false;
            }
        }

        return true;
    }
}
```

## 根据字符出现频率排序（0451）

> *给定一个字符串，请将字符串里的字符按照出现的频率降序排列。*
>
> *示例 1:*
>
> *输入:*
>
> *"tree"*
>
> *输出:*
>
> *"eert"*

```java
// 桶排序
class Solution {
    public String frequencySort(String s) {
        if (s.isEmpty() || s.length() == 1) {return s;}

        Map<Character, Integer> count = new HashMap<>();
        for (char c : s.toCharArray()) {
            count.put(c, count.getOrDefault(c, 0) + 1);
        }

        // 构造一个桶的集合（即一系列桶），桶的个数为 s 的长度 +1，因为 buckets[0] 没有意义
        // 目的是将出现频率为 i 的字符放到第 i 个桶里（即 buckets[i]）
        List<Character>[] buckets = new List[s.length() + 1];

        for (char key : count.keySet()) {
            // 某个字符在 HashMap 中的 Value 是几就会被放到第几个桶里
            int value = count.get(key);
            if (buckets[value] == null) {
                // 如果某个桶还未放入过字符（即未初始化），则初始化其为一个数组
                buckets[value] = new ArrayList<Character>();
            }
            buckets[value].add(key); // 然后将字符放到桶中
        }

        StringBuilder ans = new StringBuilder();
        for (int i = buckets.length - 1; i > 0; --i) {
            // 遍历每个桶
            if (buckets[i] != null) {
                // 如果桶里有字符
                for (char j : buckets[i]) {
                    // 遍历桶里的每个字符
                    for (int k = i; k > 0; --k) {
                        // 字符出现了几次就向 ans 中添加几次该字符
                        ans.append(j);
                    }
                }
            }
        }

        return ans.toString();
    }
}

// HashMap排序
class Solution {
    public String frequencySort(String s) {
        if (s.isEmpty() || s.length() == 1) {return s;}

        Map<Character, Integer> count = new HashMap<>();
        for (char c : s.toCharArray()) {
            count.put(c, count.getOrDefault(c, 0) + 1);
        }

        List<Map.Entry<Character, Integer>> curList = new ArrayList<>(count.entrySet());
        curList.sort((o1, o2) -> o2.getValue().compareTo(o1.getValue()));

        StringBuilder ans = new StringBuilder();
        for (int i = 0; i < curList.size(); i++) {
            for (int j = 0; j < curList.get(i).getValue(); j++) {
                ans.append(curList.get(i).getKey());
            }
        }

        return ans.toString();
    }
}
```

## 连续的子数组和（0523）

> 给定一个包含 非负数 的数组和一个目标 整数 k ，编写一个函数来判断该数组是否含有连续的子数组，其大小至少为 2，且总和为 k 的倍数，即总和为 n * k ，其中 n 也是一个整数。

```java
class Solution {
    public boolean checkSubarraySum(int[] nums, int k) {
        int n = nums.length;
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, -1);

        int sum = 0;
        for (int i = 0; i < n; i++) {
            sum += nums[i];
            if (k != 0) {sum = sum % k;}

            if (map.containsKey(sum)) {
                if (i - map.get(sum) > 1) {
                    return true;
                }
            }
            else {
                map.put(sum, i);
            }
        }

        return false;
    }
}
```

## 连续数组（0525）

> 给定一个二进制数组, 找到含有相同数量的 0 和 1 的最长连续子数组（的长度）。

```java
class Solution {
    public int findMaxLength(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, -1);
        int ans = 0, count = 0;
        for (int i = 0; i < nums.length; i++) {
            count += (nums[i] == 1 ? 1 : -1);
            if (map.containsKey(count)) {
                ans = Math.max(ans, i - map.get(count));
            }
            else {
                map.put(count, i);
            }
        }
        return ans;
    }
}
```

## 和为K的子数组（0560）

> *给定一个整数数组和一个整数 k，你需要找到该数组中和为 k 的连续的子数组的个数。*

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int ans = 0;
        int pre = 0;
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, 1);

        for (int i = 0; i < nums.length; i++) {
            pre += nums[i];
            if (map.containsKey(pre - k)) {
                ans += map.get(pre - k);
            }
            map.put(pre, map.getOrDefault(pre, 0) + 1);
        }

        return ans;
    }
}
```

## 两个列表的最小索引总和（0599）

> *假设Andy和Doris想在晚餐时选择一家餐厅，并且他们都有一个表示最喜爱餐厅的列表，每个餐厅的名字用字符串表示。*
>
> *你需要帮助他们用最少的索引和找出他们共同喜爱的餐厅。 如果答案不止一个，则输出所有答案并且不考虑顺序。 你可以假设总是存在一个答案。*

```java
class Solution {
    public String[] findRestaurant(String[] list1, String[] list2) {
        Map<Integer, List<String>> map = new HashMap<>();
        for (int i = 0; i < list1.length; i++) {
            for (int j = 0; j < list2.length; j++) {
                if (list1[i].equals(list2[j])) {
                    if (!map.containsKey(i + j)) {
                        map.put(i + j, new ArrayList<String>());
                    }
                    map.get(i + j).add(list1[i]);
                }
            }
        }

        int minIndexSum = Integer.MAX_VALUE;
        for (int k : map.keySet()) {
            minIndexSum = Math.min(minIndexSum, k);
        }
        String[] ans = new String[map.get(minIndexSum).size()];
        return map.get(minIndexSum).toArray(ans);
    }
}
```

## 任务调度器（0621）

> *给你一个用字符数组 tasks 表示的 CPU 需要执行的任务列表。其中每个字母表示一种不同种类的任务。任务可以以任意顺序执行，并且每个任务都可以在 1 个单位时间内执行完。在任何一个单位时间，CPU 可以完成一个任务，或者处于待命状态。*
>
> *然而，两个 相同种类 的任务之间必须有长度为整数 n 的冷却时间，因此至少有连续 n 个单位时间内 CPU 在执行不同的任务，或者在待命状态。*
>
> *你需要计算完成所有任务所需要的 最短时间 。*

```java
class Solution {
    public int leastInterval(char[] tasks, int n) {
        Map<Character, Integer> map = new HashMap<>();
        int maxTimes = 0;
        for (char c : tasks) {
            map.put(c, map.getOrDefault(c, 0) + 1);
            maxTimes = Math.max(maxTimes, map.get(c));
        }

        int maxCount = 0;
        for (char c : map.keySet()) {
            if (map.get(c) == maxTimes) {
                maxCount++;
            }
        }

        return Math.max((maxTimes - 1) * (n + 1) + maxCount, tasks.length);
    }
}
```

## 分割数组为连续子序列（0659）

> *给你一个按升序排序的整数数组 num（可能包含重复数字），请你将它们分割成一个或多个长度至少为 3 的子序列，其中每个子序列都由连续整数组成。*
>
> *如果可以完成上述分割，则返回 true ；否则，返回 false 。*

```java
class Solution {
    public boolean isPossible(int[] nums) {
        Map<Integer, PriorityQueue<Integer>> map = new HashMap<>();
        for (int x : nums) {
            if (!map.containsKey(x)) {map.put(x, new PriorityQueue<Integer>());}

            if (map.containsKey(x - 1)) {
                int preLength = map.get(x - 1).poll();
                if (map.get(x - 1).isEmpty()) {map.remove(x - 1);}
                map.get(x).offer(preLength + 1);
            }
            else {
                map.get(x).offer(1);
            }
        }

        for (int k : map.keySet()) {
            PriorityQueue<Integer> queue = map.get(k);
            if (queue.peek() < 3) {return false;}
        }

        return true;
    }
}
```



# 2 位运算

## 颠倒二进制位（0190）

> *颠倒给定的 32 位无符号整数的二进制位。*
>
> *输入: 00000010100101000001111010011100*
>
> *输出: 00111001011110000010100101000000*

```java
public class Solution {
    // you need treat n as an unsigned value
    public int reverseBits(int n) {
        int ans = 0;
        for (int i = 0; i < 32; i++) {
            ans = ans << 1;
            ans = (n & 1) | ans;
            n = n >> 1;
        }
        return ans;
    }
}
```

## 位1的个数（0191）

> *编写一个函数，输入是一个无符号整数（以二进制串的形式），返回其二进制表达式中数字位数为 '1' 的个数（也被称为汉明重量）。*

```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int count = 0;
        for (int i = 0; i < 32; i++) {
            count += (n & 1);
            n = n >> 1;
        }
        return count;
    }
}
```

## 数字范围按位与（0201）

> *给定范围 [m, n]，其中 0 <= m <= n <= 2147483647，返回此范围内所有数字的按位与（包含 m, n 两端点）。*

```java
class Solution {
    public int rangeBitwiseAnd(int m, int n) {
        while (m < n) {
            // 抹去最右边的 1
            n = n & (n - 1);
        }
        return n;
    }
}
```

## 数组中两个数的最大异或值

> *给定一个非空数组，数组中元素为 a0, a1, a2, … , an-1，其中 0 ≤ ai < 231 。*
>
> *找到 ai 和aj 最大的异或 (XOR) 运算结果，其中0 ≤ i, j < n 。*

```java
class Solution {
    public int findMaximumXOR(int[] nums) {
        int maxNum = nums[0];
        for (int num : nums) {maxNum = Math.max(maxNum, num);}
        int n = (Integer.toBinaryString(maxNum)).length();

        int ans = 0, curXor;
        Set<Integer> prefixes = new HashSet<>();
        for (int i = n - 1; i >= 0; i--) {
            ans = ans << 1;
            curXor = ans | 1;
            prefixes.clear();

            for (int num : nums) {
                prefixes.add(num >> i);
            }
            for (int p : prefixes) {
                if (prefixes.contains(curXor ^ p)) {
                    ans = curXor;
                    break;
                }
            }
        }

        return ans;
    }
}
```





# 3 数学问题

## 特殊数字

> 快乐数：*对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和。*

```java
class Solution {
    public boolean isHappy(int n) {
        Set<Integer> set = new HashSet<>();
        while (!set.contains(n) && n != 1) {
            set.add(n);
            n = getNextNum(n);
        }
        return n == 1;
    }

    public int getNextNum(int n) {
        int ans = 0;
        while (n > 0) {
            int d = n % 10;
            ans += d * d;
            n /= 10;
        }
        return ans;
    }
}
```

> 质数：*统计所有小于非负整数 n 的质数的数量*

```java
class Solution {
    public int countPrimes(int n) {
        int ans = 0;
        for (int i = 2; i < n; i++) {
            ans += isPrime(i) ? 1 : 0;
        }
        return ans;
    }

    public boolean isPrime(int n) {
        for (int i = 2; i * i <= n; i++) {
            if (n % i == 0) {return false;}
        }
        return true;
    }
}
```

> 丑数：*丑数就是只包含质因数 2, 3, 5 的正整数。*

```java
class Solution {
    public boolean isUgly(int num) {
        while (num > 1 && (num % 2 == 0 || num % 3 == 0 || num % 5 == 0)) {
            if (num % 2 == 0) {num /= 2;}
            if (num % 3 == 0) {num /= 3;}
            if (num % 5 == 0) {num /= 5;}
        }
        return num == 1;
    }
}
```

> *编写一个程序，找出第 n 个丑数。*

```java
class Solution {
    public int nthUglyNumber(int n) {
        int[] dp = new int[n];
        dp[0] = 1;
        int index2 = 0, index3 = 0, index5 = 0;
        for (int i = 1; i < n; i++) {
            int minValue = Math.min(Math.min(dp[index2] * 2, dp[index3] * 3), dp[index5] * 5);
            dp[i] = minValue;
            if (dp[index2] * 2 == minValue) {index2++;}
            if (dp[index3] * 3 == minValue) {index3++;}
            if (dp[index5] * 5 == minValue) {index5++;}
        }
        return dp[n - 1];
    }
}
```

> *编写一段程序来查找第 n 个超级丑数。*
>
> *超级丑数是指其所有质因数都是长度为 k 的质数列表 primes 中的正整数。*

```java
class Solution {
    public class SuperUglyNumber {
        public int nthSuperUglyNumber(int n, int[] primes) {
            int len = primes.length;
            int dp[] = new int[n];
            dp[0] = 1;
            /*梳理一下思路，dp[i]保存的是第i个超级丑数*/
            /*index[i]表示的是primes里面的第i个数下一个将要相乘的数在dp中的位置，
            反过来想，对于每个primes来说，我们都需要和dp中已经算出来的结果相乘算，
            然后取最小的那个作为新的dp元素
            索引index实际上表示是这个素数已经和dp中的哪个位置结合了，下一个位置的坐标是多少 */
            int index[] = new int[len];
            /*可能存在重复的丑数，所以呢，不要在for循环里面加break，把所有的情况都+1*/
            for (int i = 1; i < n; i++) {
                int min = Integer.MAX_VALUE;
                /*遍历对比一下值，找出最小的，*/
                for (int j = 0; j < len; j++) {
                    if (min > primes[j] * dp[index[j]]) {
                        min = primes[j] * dp[index[j]];//这个地方就是当前质因数和他要结合的dp
                    }
                }
                dp[i] = min;
                /*那个素数要乘以dp的坐标index要加1，向后推一个位
                * 如果存在重复的值，也就是说不同的质因数相乘，得出来相同的结果了，
                * 我们就把这几个位置都+1，这样就可以避免出现重复的值了。
                * 你想想，假如你找到了对应的素数的index，把它加1之后就break掉，那么后面的数也可以算出这个结果，
                * 下次循环的时候，势必会把这个重复的数当成下一个dp，因为这个数肯定要比下一个丑数小
                * 所以我们在for循环中不要加break；*/
                for (int j = 0; j < len; j++) {
                    if (min == primes[j] * dp[index[j]]) {
                        index[j]++;
                    }
                }

            }
            return dp[n - 1];
        }
    }
}
```


> 完全平方数：
>
> *给定正整数 n，找到若干个完全平方数（比如 1, 4, 9, 16, ...）使得它们的和等于 n。你需要让组成和的完全平方数的个数最少。*

```java
class Solution {
    public int numSquares(int n) {
        int[] dp = new int[n + 1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;

        int maxIndex = (int)Math.sqrt(n) + 1;
        int[] squareNums = new int[maxIndex];
        for (int i = 1; i < maxIndex; i++) {squareNums[i] = i * i;}

        for (int i = 1; i <= n; i++) {
            for (int j = 1; j < maxIndex; j++) {
                if (i < squareNums[j]) {break;}
                dp[i] = Math.min(dp[i], dp[i - squareNums[j]] + 1);
            }
        }

        return dp[n];
    }
}
```

> 平方数之和：*给定一个非负整数 c ，你要判断是否存在两个整数 a 和 b，使得 a2 + b2 = c 。**给定一个非负整数 c ，你要判断是否存在两个整数 a 和 b，使得 a2 + b2 = c 。*
>
> *一个非负整数 c 能够表示为两个整数的平方和，当且仅当 c 的所有形如 4k+3 的质因子的幂次均为偶数。*

```java
public class Solution {
    public boolean judgeSquareSum(int c) {
        for (int i = 2; i * i <= c; i++) {
            int count = 0;
            if (c % i == 0) {
                while (c % i == 0) {
                    count++;
                    c /= i;
                }
                if (i % 4 == 3 && count % 2 != 0)
                    return false;
            }
        }
        return c % 4 != 3;
    }
}
```


> 完美数：
>
> *对于一个 正整数，如果它和除了它自身以外的所有 正因子 之和相等，我们称它为 「完美数」。*
>
> *给定一个 整数 n， 如果是完美数，返回 true，否则返回 false*

```java
class Solution {
    public boolean checkPerfectNumber(int num) {
        if (num <= 0) {return false;}
        int sum = 0;

        for (int i = 1; i * i <= num; i++) {
            if (num % i == 0) {
                sum += i;
                if (i * i != num) {
                    sum += num / i;
                }
            }
        }

        return sum == 2 * num;
    }
}
```

## 买卖股票

> *只允许完成一笔交易（即买入和卖出一支股票一次），设计一个算法来计算你所能获取的最大利润*

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices.length < 2) {return 0;}
        int minPrice = prices[0];
        int ans = 0;
        for (int i = 0; i < prices.length; i++){
            minPrice = Math.min(minPrice, prices[i]);
            ans = Math.max(ans, prices[i] - minPrice);
        }
        return ans;
    }
}
```

> *可以尽可能地完成更多的交易（多次买卖一支股票）*

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices.length < 2) {return 0;}
        int ans = 0;
        for (int i = 1; i < prices.length; i++){
            if (prices[i] > prices[i - 1]){
                ans = ans + prices[i] - prices[i - 1];
            }
        }
        return ans;
    }
}
```

> *最多可以完成 两笔 交易*

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices.length < 2) {return 0;}
        int ans = 0;
        int buy1 = Integer.MAX_VALUE, buy2 = Integer.MAX_VALUE;
        int pro1 = Integer.MIN_VALUE, pro2 = Integer.MIN_VALUE;

        for (int i = 0; i < prices.length; i++){
            buy1 = Math.min(buy1, prices[i]);
            pro1 = Math.max(pro1, prices[i] - buy1);
            buy2 = Math.min(buy2, prices[i] - pro1);
            pro2 = Math.max(pro2, prices[i] - buy2);
        }
        return pro2;
    }
}
```

> *设计一个算法来计算你所能获取的最大利润。你最多可以完成 k 笔交易。*

```java
class Solution {
    public int maxProfit(int k, int[] prices) {
        int n = prices.length;
        if(n < 2) return 0;

        // 如果k超过了最大可买卖次数，那就将k置为最大买卖次数
        // 最大买卖次数就是天数的一半，如果当前卖出又买入，是没有意义的
        k = Math.min(k, n / 2);

        int[] buy = new int[k + 1];
        int[] pro = new int[k + 1];
        Arrays.fill(buy, Integer.MAX_VALUE);
        Arrays.fill(pro, Integer.MIN_VALUE);
        buy[0] = 0;
        pro[0] = 0;

        for (int i = 0; i < n; i++){
            for (int j = 1; j <= k; j++){
                buy[j] = Math.min(buy[j], prices[i] - pro[j - 1]);
                pro[j] = Math.max(pro[j], prices[i] - buy[j]);
            }
        }

        return pro[k];
    }
}
```

> *设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:*
>
> *你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。*
>
> *卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。*

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        if (n == 0) {return 0;}

        // dp[i][0]: 手上持有股票的最大收益
        // dp[i][1]: 手上不持有股票，并且处于冷冻期中的累计最大收益
        // dp[i][2]: 手上不持有股票，并且不在冷冻期中的累计最大收益
        int[][] dp = new int[n][3];
        dp[0][0] = -prices[0];
        for (int i = 1; i < n; i++) {
            dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][2] - prices[i]);
            dp[i][1] = dp[i - 1][0] + prices[i];
            dp[i][2] = Math.max(dp[i - 1][1], dp[i - 1][2]);
        }

        return Math.max(dp[n - 1][1], dp[n - 1][2]);
    }
}
```

> *给定一个整数数组 prices，其中第 i 个元素代表了第 i 天的股票价格 ；非负整数 fee 代表了交易股票的手续费用。*
>
> *你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。*

```java
class Solution {
    public int maxProfit(int[] prices, int fee) {
        int n = prices.length;
        int[][] dp = new int[n][2];

        dp[0][0] = 0;
        dp[0][1] = -prices[0];

        for (int i = 1; i < n; i++) {
            dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i] - fee);
            dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
        }
        return dp[n - 1][0];
    }
}
```

## 打家劫舍

> *你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。*
>
> *给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。*

```java
class Solution {
    public int rob(int[] nums) {
        if (nums.length == 0) {return 0;}
        int n = nums.length;
        if (n == 1) {return nums[0];}

        int[] dp = new int[n];
        dp[0] = nums[0];
        dp[1] = Math.max(nums[0], nums[1]);
        for (int i = 2; i < n; i++) {
            dp[i] = Math.max(dp[i - 1], dp[i - 2] + nums[i]);
        }
        return dp[n - 1];
    }
}
```

> 环形数组，不能偷连续的两家

```java
class Solution {
    public int rob(int[] nums) {
        if(nums.length == 0) return 0;
        if(nums.length == 1) return nums[0];
        return Math.max(myRob(Arrays.copyOfRange(nums, 0, nums.length - 1)), 
                        myRob(Arrays.copyOfRange(nums, 1, nums.length)));
    }

    private int myRob(int[] nums) {
        int n = nums.length;
        if (n == 0) {return 0;}
        else if (n == 1) {return nums[0];}
        else if (n == 2) {return nums[0] > nums[1] ? nums[0] : nums[1];}
        int[] dp = new int[n];
        dp[0] = nums[0];
        dp[1] = nums[1];
        for (int i = 2; i < n; i++) {
            int tmp = dp[0];
            for (int j = 1; j < i - 1; j++) {
                if (dp[j] > tmp) {tmp = dp[j];}
            }
            dp[i] = Math.max(dp[i - 1], tmp + nums[i]);
        }
        return dp[n - 1];
    }
}
```

> *在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。*
>
> *计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。*
>
> *示例 1:
>
> *输入: [3,2,3,null,3,null,1]*
>
> *输出: 7* 
>
> *解释: 小偷一晚能够盗取的最高金额 = 3 + 3 + 1 = 7.*

```java
class Solution {
    Map<TreeNode, Integer> f = new HashMap<>();
    Map<TreeNode, Integer> g = new HashMap<>();

    public int rob(TreeNode root) {
        dfs(root);
        return Math.max(f.getOrDefault(root, 0), g.getOrDefault(root, 0));
    }

    private void dfs(TreeNode node) {
        if (node == null) {return;}

        dfs(node.left);
        dfs(node.right);
        f.put(node, node.val + g.getOrDefault(node.left, 0) + g.getOrDefault(node.right, 0));
        g.put(node, Math.max(f.getOrDefault(node.left, 0), g.getOrDefault(node.left, 0)) 
            + Math.max(f.getOrDefault(node.right, 0), g.getOrDefault(node.right, 0)));
    }
}
```

> *给你一个整数数组 nums ，你可以对它进行一些操作。*
>
> *每次操作中，选择任意一个 nums[i] ，删除它并获得 nums[i] 的点数。之后，你必须删除每个等于 nums[i] - 1 或 nums[i] + 1 的元素。*
>
> *开始你拥有 0 个点数。返回你能通过这些操作获得的最大点数。*

```java
// 类似于打家劫舍，先统计出相同数字一共有多少点数，然后就等价于打家劫舍里不能偷相邻两家
class Solution {
    public int deleteAndEarn(int[] nums) {
        int maxVal = 0;
        for (int n : nums) {maxVal = Math.max(maxVal, n);}

        int[] sum = new int[maxVal + 1];
        for (int n : nums) {sum[n] += n;}

        int len = sum.length;
        int first = sum[0], second = Math.max(sum[0], sum[1]);
        for (int i = 2; i < len; i++) {
            int tmp = second;
            second = Math.max(first + sum[i], second);
            first = tmp;
        }
        return second;
    }
}
```

## 接雨水

> *给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。*

```java
class Solution {
    public int trap(int[] height) {
        int ans = 0;
        int leftMax = 0, rightMax = 0;
        for (int i = 0; i < height.length; i++) {
            leftMax = Math.max(leftMax, height[i]);
            rightMax = Math.max(rightMax, height[height.length - 1 - i]);
            ans += (leftMax + rightMax - height[i]);
        }
        return ans - leftMax * height.length;
    }
}

// 单独算每个坑，求出坑的最小值
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        if (n == 0) {return 0;}

        int[] leftMax = new int[n];
        leftMax[0] = height[0];
        for (int i = 1; i < n; ++i) {
            leftMax[i] = Math.max(leftMax[i - 1], height[i]);
        }

        int[] rightMax = new int[n];
        rightMax[n - 1] = height[n - 1];
        for (int i = n - 2; i >= 0; --i) {
            rightMax[i] = Math.max(rightMax[i + 1], height[i]);
        }

        int ans = 0;
        int min = Integer.MAX_VALUE;
        // 单独算每个坑，求出坑的最小值
        for (int i = 0; i < n; ++i) {
            if (height[i] < leftMax[i] && height[i] > rightMax[i]) {
                ans += Math.min(leftMax[i], rightMax[i]) - height[i];
            }
            else {
                if (ans != 0) {
                    min = Math.min(min, ans);
                    ans = 0;
                }
            }
        }
        return min == Integer.MAX_VALUE ? 0 : min;
    }
}
```

> *给你一个 m x n 的矩阵，其中的值均为非负整数，代表二维高度图每个单元的高度，请计算图中形状最多能接多少体积的雨水。*

```java
class Solution {
    public int trapRainWater(int[][] heightMap) {
        if (heightMap.length == 0) {return 0;}
        int m = heightMap.length, n = heightMap[0].length;
        boolean[][] visited = new boolean[m][n];
        PriorityQueue<int[]> pq = new PriorityQueue<>((o1, o2) -> o1[2] - o2[2]);

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (i == 0 || j == 0 || i == m - 1 || j == n - 1) {
                    pq.offer(new int[]{i, j, heightMap[i][j]});
                    visited[i][j] = true;
                }
            }
        }

        int ans = 0;
        int[] dirs = {-1, 0, 1, 0, -1};
        while (!pq.isEmpty()) {
            int[] curPoint = pq.poll();
            for (int i = 0; i < 4; i++) {
                int nextX = curPoint[0] + dirs[i];
                int nextY = curPoint[1] + dirs[i + 1];
                if (nextX >= 0 && nextX < m && nextY >= 0 && nextY < n && !visited[nextX][nextY]) {
                    if (curPoint[2] > heightMap[nextX][nextY]) {
                        ans += curPoint[2] - heightMap[nextX][nextY];
                    }
                    pq.offer(new int[]{nextX, nextY, Math.max(heightMap[nextX][nextY], curPoint[2])});
                    visited[nextX][nextY] = true;
                }
            }
        }

        return ans;
    }
}
```

## N数之和

> *给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组。*
>
> *注意：答案中不可以包含重复的三元组。*
>
> *给定数组 nums = [-1, 0, 1, 2, -1, -4]，*
>
> *满足要求的三元组集合为：*
>
> *[*
>
>   *[-1, 0, 1],*
>
>   *[-1, -1, 2]*
>
> *]*

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> ans = new ArrayList<>();
        if (nums.length < 3) {return ans;}
        for (int i = 0; i < nums.length - 2; i++){
            if (nums[i] > 0){break;}
            if (i > 0 && nums[i] == nums[i - 1]){continue;}

            int left = i + 1, right = nums.length - 1;
            while (left < right){
                int total = nums[i] + nums[left] + nums[right];
                if (total < 0) {left++;}
                else if (total > 0) {right--;}
                else {
                    List<Integer> list = new ArrayList<>();
                    list.add(nums[i]);
                    list.add(nums[left]);
                    list.add(nums[right]);
                    ans.add(list);

                    while (left < right && nums[left] == nums[left + 1]) {left++;}
                    while (left < right && nums[right] == nums[right - 1]) {right--;}
                    left++;
                    right--;
                }
            }
        }
        return ans;
    }
}
```

> *给定一个包括 n 个整数的数组 nums 和 一个目标值 target。找出 nums 中的三个整数，使得它们的和与 target 最接近。返回这三个数的和。假定每组输入只存在唯一答案。*
>
> *输入：nums = [-1,2,1,-4], target = 1*
>
> *输出：2*
>
> *解释：与 target 最接近的和是 2 (-1 + 2 + 1 = 2)* 

```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int ans = nums[0] + nums[1] + nums[2];

        for (int i = 0; i < nums.length - 2; i++){
            if (i > 0 && nums[i] == nums[i - 1]) {continue;}
            int left = i + 1, right = nums.length - 1;
            while (left < right) {
                int total = nums[i] + nums[left] + nums[right];
                if (Math.abs(total - target) < Math.abs(ans - target)){ans = total;}
                if (total - target < 0) {
                    while (left < right && nums[left] == nums[left + 1]) {left++;}
                    left++;
                }
                else if (total - target > 0) {
                    while (left < right && nums[right] == nums[right - 1]) {right--;}
                    right--;
                }
                else {break;}
            }
        }
        return ans;
    }
}
```

> *给定一个包含 n 个整数的数组 nums 和一个目标值 target，判断 nums 中是否存在四个元素 a，b，c 和 d ，使得 a + b + c + d 的值与 target 相等？找出所有满足条件且不重复的四元组。*
>
> *给定数组 nums = [1, 0, -1, 0, -2, 2]，和 target = 0。*
>
> *满足要求的四元组集合为：*
>
> *[*
>
>   *[-1,  0, 0, 1],*
>
>   *[-2, -1, 1, 2],*
>
>   *[-2,  0, 0, 2]*
>
> *]*

```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        Arrays.sort(nums);
        List<List<Integer>> ans = new ArrayList<>();
        if (nums.length < 4) {return ans;}
        for (int i = 0; i < nums.length - 3; i++) {
            // 此处不能判断nums[i]和target的大小，然后break，因为可能target是负数
            if (i > 0 && nums[i] == nums[i - 1]) {continue;}
            for (int j = i + 1; j < nums.length - 2; j++) {
                if (j > i + 1 && nums[j] == nums[j - 1]) {continue;}
                int left = j + 1, right = nums.length - 1;
                while (left < right) {
                    int total = nums[i] + nums[j] + nums[left] + nums[right];
                    if (total < target) {left++;}
                    else if (total > target) {right--;}
                    else {
                        List<Integer> tmp = new ArrayList<>();
                        tmp.add(nums[i]);
                        tmp.add(nums[j]);
                        tmp.add(nums[left]);
                        tmp.add(nums[right]);
                        ans.add(tmp);

                        while (left < right && nums[left] == nums[left + 1]) {left++;}
                        while (left < right && nums[right] == nums[right - 1]) {right--;}
                        left++;
                        right--;
                    }
                }
            }
        }
        return ans;
    }
}
```

## 删除排序数组中的重复项

> *给定一个排序数组，你需要在 原地 删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。*
>
> *不要使用额外的数组空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。*
>
> *给定数组 nums = [1,1,2],* 
>
> *函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。* 
>
> *你不需要考虑数组中超出新长度后面的元素。*

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if (nums.length <= 1) {return nums.length;}
        int index = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[index] != nums[i]) {
                index++;
                nums[index] = nums[i];
            }
        }
        return index + 1;
    }
}
```

> *给定一个增序排列数组 nums ，你需要在 原地 删除重复出现的元素，使得每个元素最多出现两次，返回移除后数组的新长度。*

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if (nums.length == 1) {return 1;}
        Arrays.sort(nums);
        int count = 1;
        int index = 1;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] == nums[i - 1]) {
                count++;
            }
            else {
                count = 1;
            }
            if (count <= 2) {
                nums[index] = nums[i];
                index++;
            }
        }
        return index;
    }
}
```

## 跳跃游戏

> *给定一个非负整数数组，你最初位于数组的第一个位置。*
>
> *数组中的每个元素代表你在该位置可以跳跃的最大长度。*
>
> *判断你是否能够到达最后一个位置。*

```java
class Solution {
    public boolean canJump(int[] nums) {
        int n = nums.length, rightMost = 0;
        for (int i = 0; i < nums.length; i++) {
            if (i <= rightMost) {
                rightMost = Math.max(rightMost, i + nums[i]);
                if (rightMost >= n - 1) {return true;}
            }
        }
        return false;
    }
}
```

> *给定一个非负整数数组，你最初位于数组的第一个位置。*
>
> *数组中的每个元素代表你在该位置可以跳跃的最大长度。*
>
> *你的目标是使用最少的跳跃次数到达数组的最后一个位置。*

```java
class Solution {
    public int jump(int[] nums) {
        int end = 0;
        int maxPosition = 0; 
        int steps = 0;
        for(int i = 0; i < nums.length - 1; i++){
            //找能跳的最远的
            maxPosition = Math.max(maxPosition, nums[i] + i); 
            if(i == end){ //遇到边界，就更新边界，并且步数加一
                end = maxPosition;
                steps++;
            }
        }
        return steps;
    }
}
```


## 螺旋矩阵

> *给定一个包含 m x n 个元素的矩阵（m 行, n 列），请按照顺时针螺旋顺序，返回矩阵中的所有元素。*

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> order = new ArrayList<Integer>();
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return order;
        }
        int rows = matrix.length, columns = matrix[0].length;
        int left = 0, right = columns - 1, top = 0, bottom = rows - 1;
        while (left <= right && top <= bottom) {
            for (int i = left; i <= right; i++) {
                order.add(matrix[top][i]);
            }
            top++;
            for (int j = top; j <= bottom; j++) {
                order.add(matrix[j][right]);
            }
            right--;
            if (left <= right && top <= bottom) {
                for (int i = right; i >= left; i--) {
                    order.add(matrix[bottom][i]);
                }
                bottom--;
                for (int j = bottom; j >= top; j--) {
                    order.add(matrix[j][left]);
                }
                left++;
            }
        }
        return order;
    }
}
```

> *给定一个正整数 n，生成一个包含 1 到 n2 所有元素，且元素按顺时针顺序螺旋排列的正方形矩阵。*

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int left = 0, right = n - 1;
        int top = 0, bottom = n - 1;
        int[][] ans = new int[n][n];
        int num = 1, target = n * n;

        while (num <= target) {
            for (int i = left; i <= right; i++) {ans[top][i] = num++;}
            top++;
            for (int i = top; i <= bottom; i++) {ans[i][right] = num++;}
            right--;
            for (int i = right; i >= left; i--) {ans[bottom][i] = num++;}
            bottom--;
            for (int i = bottom; i >= top; i--) {ans[i][left] = num++;}
            left++;
        }
        
        return ans;
    }
}
```

## 两个数组的交集（0350）

> *给定两个数组，编写一个函数来计算它们的交集。*

```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        if (nums1.length > nums2.length) {return intersect(nums2, nums1);}
        Map<Integer, Integer> map = new HashMap<Integer, Integer>();
        for (int num : nums1) {map.put(num, map.getOrDefault(num, 0) + 1);}
        
        int[] ans = new int[nums1.length];
        int index = 0;
        for (int num : nums2) {
            int count = map.getOrDefault(num, 0);
            if (count > 0) {
                ans[index++] = num;
                count--;
                if (count > 0) {
                    map.put(num, count);
                } else {
                    map.remove(num);
                }
            }
        }
        return Arrays.copyOfRange(ans, 0, index);
    }
}
```

## 区间问题

> 合并区间（0056）
>
> *给出一个区间的集合，请合并所有重叠的区间。*

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        if (intervals.length == 0) {return new int[0][2];}
        Arrays.sort(intervals, new Comparator<int[]>(){
            public int compare(int[] a, int[] b) {
                return a[0] == b[0] ? a[1] - b[1] : a[0] - b[0];
            }
        });

        List<int[]> ans = new ArrayList<>();
        for (int i = 0; i < intervals.length; i++) {
            int L = intervals[i][0], R = intervals[i][1];
            if (ans.size() == 0 || ans.get(ans.size() - 1)[1] < L) {
                ans.add(new int[]{L, R});
            }
            else {
                ans.get(ans.size() - 1)[1] = Math.max(ans.get(ans.size() - 1)[1], R);
            }
        }
        return ans.toArray(new int[ans.size()][]);
    }
}
```

> 插入区间（0057）
>
> *给出一个无重叠的 ，按照区间起始端点排序的区间列表。*
>
> *在列表中插入一个新的区间，你需要确保列表中的区间仍然有序且不重叠（如果有必要的话，可以合并区间）。*

```java
class Solution {
    // 1 先复制一份，加入新的数组，再合并
    public int[][] insert(int[][] intervals, int[] newInterval) {
        int[][] intervalsAdd = new int[intervals.length + 1][2];
        for (int i = 0; i < intervals.length; i++) {
            intervalsAdd[i] = intervals[i];
        }
        intervalsAdd[intervals.length] = newInterval;
        return merge(intervalsAdd);
    }
    // 2 直接比较
    public int[][] insert(int[][] intervals, int[] newInterval) {
        int left = newInterval[0], right = newInterval[1];
        boolean placed = false;
        List<int[]> ans = new ArrayList<>();
        for (int[] tmp : intervals) {
            if (tmp[0] > right) {
                if (!placed) {
                    ans.add(new int[]{left, right});
                    placed = true;
                }
                ans.add(tmp);
            }
            else if (tmp[1] < left) {
                ans.add(tmp);
            }
            else {
                left = Math.min(left, tmp[0]);
                right = Math.max(right, tmp[1]);
            }
        }

        if (!placed) {ans.add(new int[]{left, right});}
        return ans.toArray(new int[ans.size()][]);
    }
}
```

> 汇总区间（0228）
>
> *给定一个无重复元素的有序整数数组 nums 。*
>
> *返回 恰好覆盖数组中所有数字 的 最小有序 区间范围列表。也就是说，nums 的每个元素都恰好被某个区间范围所覆盖，并且不存在属于某个范围但不属于 nums 的数字 x 。*
>
> *输入：nums = [0,1,2,4,5,7]*
>
> *输出：["0->2","4->5","7"]*

```java
class Solution {
    public List<String> summaryRanges(int[] nums) {
        List<String> ans = new ArrayList<>();
        int index = 0, n = nums.length;
        while (index < n) {
            int start = index;
            index++;
            while (index < n && nums[index] == nums[index - 1] + 1) {index++;}
            int end = index - 1;
            StringBuilder sb = new StringBuilder(Integer.toString(nums[start]));
            if (start < end) {
                sb.append("->");
                sb.append(Integer.toString(nums[end]));
            }
            ans.add(sb.toString());
        }
        return ans;
    }
}
```

> 无重叠区间（0435）
>
> *给定一个区间的集合，找到需要移除区间的最小数量，使剩余区间互不重叠。*

```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        if (intervals.length == 0) {return 0;}

        Arrays.sort(intervals, new Comparator<int[]>() {
            public int compare(int[] A, int[] B) {
                return A[0] - B[0];
            }
        });

        int n = intervals.length;
        // dp[i]: 以区间i为最后一个区间，可以选出的区间数量的最大值
        int[] dp = new int[n];
        Arrays.fill(dp, 1);
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (intervals[j][1] <= intervals[i][0]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
        }

        return n - dp[n - 1];
    }
}
```

## 区间和的个数（0327）

> *给定一个整数数组 nums 。区间和 S(i, j) 表示在 nums 中，位置从 i 到 j 的元素之和，包含 i 和 j (i ≤ j)。*
>
> *请你以下标 i （0 <= i <= nums.length ）为起点，元素个数逐次递增，计算子数组内的元素和。*
>
> *当元素和落在范围 [lower, upper] （包含 lower 和 upper）之内时，记录子数组当前最末元素下标 j ，记作 有效 区间和 S(i, j) 。*
>
> *求数组中，值位于范围 [lower, upper] （包含 lower 和 upper）之内的 有效 区间和的个数。*
>
> *输入：nums = [-2,5,-1], lower = -2, upper = 2,*
>
> *输出：3* 
>
> *解释：*
>
> *下标 i = 0 时，子数组 [-2]、[-2,5]、[-2,5,-1]，对应元素和分别为 -2、3、2 ；其中 -2 和 2 落在范围 [lower = -2, upper = 2] 之间，因此记录有效区间和 S(0,0)，S(0,2) 。*
>
> *下标 i = 1 时，子数组 [5]、[5,-1] ，元素和 5、4 ；没有满足题意的有效区间和。*
>
> *下标 i = 2 时，子数组 [-1] ，元素和 -1 ；记录有效区间和 S(2,2) 。*
>
> *故，共有 3 个有效区间和。*

```java
class Solution {
    public int countRangeSum(int[] nums, int lower, int upper) {
        long s = 0;
        long[] sum = new long[nums.length + 1];
        for (int i = 0; i < nums.length; ++i) {
            s += nums[i];
            sum[i + 1] = s;
        }
        return countRangeSumRecursive(sum, lower, upper, 0, sum.length - 1);
    }

    public int countRangeSumRecursive(long[] sum, int lower, int upper, int left, int right) {
        if (left == right) {
            return 0;
        } else {
            int mid = (left + right) / 2;
            int n1 = countRangeSumRecursive(sum, lower, upper, left, mid);
            int n2 = countRangeSumRecursive(sum, lower, upper, mid + 1, right);
            int ret = n1 + n2;

            // 首先统计下标对的数量
            int i = left;
            int l = mid + 1;
            int r = mid + 1;
            while (i <= mid) {
                while (l <= right && sum[l] - sum[i] < lower) {
                    l++;
                }
                while (r <= right && sum[r] - sum[i] <= upper) {
                    r++;
                }
                ret += r - l;
                i++;
            }

            // 随后合并两个排序数组
            int[] sorted = new int[right - left + 1];
            int p1 = left, p2 = mid + 1;
            int p = 0;
            while (p1 <= mid || p2 <= right) {
                if (p1 > mid) {
                    sorted[p++] = (int) sum[p2++];
                } else if (p2 > right) {
                    sorted[p++] = (int) sum[p1++];
                } else {
                    if (sum[p1] < sum[p2]) {
                        sorted[p++] = (int) sum[p1++];
                    } else {
                        sorted[p++] = (int) sum[p2++];
                    }
                }
            }
            for (int j = 0; j < sorted.length; j++) {
                sum[left + j] = sorted[j];
            }
            return ret;
        }
    }
}
```

## 寻找右区间（0436）

> *给你一个区间数组 intervals ，其中 intervals[i] = [starti, endi] ，且每个 starti 都 不同 。*
>
> *区间 i 的 右侧区间 可以记作区间 j ，并满足 startj >= endi ，且 startj 最小化 。*
>
> *返回一个由每个区间 i 的 右侧区间 的最小起始位置组成的数组。如果某个区间 i 不存在对应的 右侧区间 ，则下标 i 处的值设为 -1 。*

```java
class Solution {
    public int[] findRightInterval(int[][] intervals) {
        int n = intervals.length;
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < n; i++) {map.put(intervals[i][0], i);}

        Arrays.sort(intervals, (a, b) -> a[0] - b[0]);

        int[] ans = new int[n];
        for (int i = 0; i < n; i++) {
            int left = i, right = n - 1;
            while (left < right) {
                int mid = left + (right - left) / 2;
                if (intervals[mid][0] >= intervals[i][1]) {
                    right = mid;
                }
                else {
                    left = mid + 1;
                }
            }

            if (intervals[i][1] > intervals[right][0] || i == n - 1) {
                ans[map.get(intervals[i][0])] = -1;
            }
            else {
                ans[map.get(intervals[i][0])] = map.get(intervals[right][0]);
            }
        }

        return ans;
    }
}
```

## 格雷码（0089）

> *格雷编码是一个二进制数字系统，在该系统中，两个连续的数值仅有一个位数的差异。*
>
> *给定一个代表编码总位数的非负整数 n，打印其格雷编码序列。即使有多个不同答案，你也只需要返回其中一种。*

```java
class Solution {
    public List<Integer> grayCode(int n) {
        List<Integer> ans = new ArrayList<>();
        ans.add(0);
        int tmp = 1;

        for (int i = 0; i < n; i++) {
            for (int j = ans.size() - 1; j >= 0; j--) {
                ans.add(tmp + ans.get(j));
            }
            tmp *= 2;
        }

        return ans;
    }
}
```

## 存在重复元素

> *给定一个整数数组，判断是否存在重复元素。*

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        Set<Integer> set = new HashSet<Integer>();
        for (int x : nums) {
            if (!set.add(x)) {
                return true;
            }
        }
        return false;
    }
}
```

> *给定一个整数数组和一个整数 k，判断数组中是否存在两个不同的索引 i 和 j，使得 nums [i] = nums [j]，并且 i 和 j 的差的 绝对值 至多为 k。*

```java
class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        Set<Integer> set = new HashSet<>();
        for (int i = 0; i < nums.length; ++i) {
            if (set.contains(nums[i])) {return true;}
            set.add(nums[i]);
            if (set.size() > k) {
                set.remove(nums[i - k]);
            }
        }
        return false;
    }
}
```

> *在整数数组 nums 中，是否存在两个下标 i 和 j，使得 nums [i] 和 nums [j] 的差的绝对值小于等于 t ，且满足 i 和 j 的差的绝对值也小于等于 ķ 。*
>
> *如果存在则返回 true，不存在返回 false。*

```java
class Solution {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
        TreeSet<Long> set = new TreeSet<>();
        int n = nums.length;
        for (int i = 0; i < n; i++) {
            if (i > k) {set.remove((long)nums[i - k - 1]);}
            // 返回在这个集合中大于或者等于给定元素的最小元素
            Long low = set.ceiling((long) nums[i] - t);
            if (low != null && low <= (long)nums[i] + t) {return true;}
            set.add((long) nums[i]);
        }
        return false;
    }
}
```

## H指数

> *编写一个方法，计算出研究者的 h 指数。*

```java
class Solution {
    public int hIndex(int[] citations) {
        if (citations.length == 0) {return 0;}
        Arrays.sort(citations);
        int cite = 1;
        int ans = 0;
        for (int i = citations.length - 1; i >= 0; i--) {
            if (citations[i] >= cite) {
                cite++;
                ans++;
            }
            else {
                return ans;
            }
        }
        return ans;
    }
}
```

## 前K个高频元素

> 前K个高频元素
>
> *给定一个非空的整数数组，返回其中出现频率前 k 高的元素。*

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> countMap = new HashMap<>();
        for (int n : nums) {
            countMap.put(n, countMap.getOrDefault(n, 0) + 1);
        }

        PriorityQueue<int[]> pq = new PriorityQueue<>(new Comparator<int[]>() {
            public int compare(int[] a, int[] b) {
                return a[1] - b[1];
            }
        });

        for (Map.Entry<Integer, Integer> entry : countMap.entrySet()) {
            int num = entry.getKey(), count = entry.getValue();
            if (pq.size() == k) {
                if (pq.peek()[1] < count) {
                    pq.poll();
                    pq.offer(new int[]{num, count});
                }
            } else {
                pq.offer(new int[]{num, count});
            }
        }

        int[] ans = new int[pq.size()];
        int index = pq.size() - 1;
        while (!pq.isEmpty()) {
            ans[index--] = pq.poll()[0];
        }
        return ans;
    }
}
```

> 前K个高频单词
>
> *给一非空的单词列表，返回前 k 个出现次数最多的单词。*
>
> *返回的答案应该按单词出现频率由高到低排序。如果不同的单词有相同出现频率，按字母顺序排序。*

```java
class Solution {
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> map = new HashMap<>();
        for (String s : words) {
            map.put(s, map.getOrDefault(s, 0) + 1);
        }

        List<Map.Entry<String, Integer>> curList = new ArrayList<>(map.entrySet());
        curList.sort((o1, o2) -> o1.getValue() == o2.getValue() ? o1.getKey().compareTo(o2.getKey()) : o2.getValue() - o1.getValue());

        List<String> ans = new ArrayList<>();
        for (int i = 0; i < k; i++) {
            ans.add(curList.get(i).getKey());
        }

        return ans;
    }
}
```

## 查找和最小的K对数字（0373）

> *给定两个以升序排列的整形数组 nums1 和 nums2, 以及一个整数 k。*
>
> *定义一对值 (u,v)，其中第一个元素来自 nums1，第二个元素来自 nums2。*
>
> *找到和最小的 k 对数字 (u1,v1), (u2,v2) ... (uk,vk)。*

```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> ans = new ArrayList<>();
        int m = nums1.length, n = nums2.length;
        if (m == 0 || n == 0) {return ans;}

        int[] tmp = new int[m];
        while (ans.size() < k) {
            int cur = 0;
            for (int i = 0; i < m; i++) {
                if (tmp[i] == n) {continue;}
                if (tmp[cur] == n || nums1[cur] + nums2[tmp[cur]] > nums1[i] + nums2[tmp[i]]) {
                    cur = i;
                }
            }

            if (tmp[cur] == n) {break;}
            ans.add(Arrays.asList(nums1[cur], nums2[tmp[cur]]));
            tmp[cur]++;
        }

        return ans;
    }
}
```

## 最长连续序列（0128）

> *给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。*

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int n : nums) {set.add(n);}

        int ans = 0;

        for (int n : nums) {
            if (!set.contains(n - 1)) {
                int currentNum = n;
                int tmp = 1;

                while (set.contains(currentNum + 1)) {
                    currentNum++;
                    tmp++;
                }

                ans = Math.max(ans, tmp);
            }
        }

        return ans;
    }
}
```

## 长度最小的子数组（0209）

> 给定一个含有 n 个正整数的数组和一个正整数 target 。
>
> 找出该数组中满足其和 ≥ target 的长度最小的 连续子数组 [numsl, numsl+1, ..., numsr-1, numsr] ，并返回其长度。如果不存在符合条件的子数组，返回 0 。

```java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        int left = 0, right = 0;
        int sum = 0;
        int min  = Integer.MAX_VALUE;

        while (right < nums.length) {
            sum += nums[right++];
            while (sum >= s) {
                min = Math.min(min, right - left);
                sum -= nums[left++];
            }
        }
        
        return min == Integer.MAX_VALUE ? 0 : min;
    }
}
```

## 三个无重叠子数组的最大和（0689）

> *给定数组 nums 由正整数组成，找到三个互不重叠的子数组的最大和。*
>
> *每个子数组的长度为k，我们要使这3\*k个项的和最大化。*
>
> *返回每个区间起始索引的列表（索引从 0 开始）。如果有多个结果，返回字典序最小的一个。*

```java
class Solution {
    public int[] maxSumOfThreeSubarrays(int[] nums, int k) {
        int n = nums.length;
        int[] cache = new int[n - k + 1];
        int[] left = new int[n - k + 1];
        int[] right = new int[n - k + 1];

        int sum = 0;
        for (int i = 0; i < k; i++) {sum += nums[i];}
        cache[0] = sum;
        
        for (int i = 1; i < cache.length; i++) {
            int v = cache[i] = sum = sum + nums[i + k - 1] - nums[i - 1];
            if (v > cache[left[i - 1]]) {
                left[i] = i;
            }
            else {
                left[i] = left[i - 1];
            }
        }
        right[n - k] = n - k;

        for (int i = n - k - 1; i >= 0; i--) {
            if (cache[i] >= cache[right[i + 1]]) {
                right[i] = i;
            }
            else {
                right[i] = right[i + 1];
            }
        }

        int interval = k << 1;
        int[] ans = new int[]{0, k, interval};
        int max = Integer.MIN_VALUE;
        int maxM = cache.length - k;

        for (int m = k; m < maxM; m++) {
            int v = cache[left[m - k]] + cache[m] + cache[right[m + k]];
            if (v > max) {
                max = v;
                ans[0] = left[m - k];
                ans[1] = m;
                ans[2] = right[m + k];
            }
        }

        return ans;
    }
}
```

## 加油站（0134）

> *在一条环路上有 N 个加油站，其中第 i 个加油站有汽油 gas[i] 升。*
>
> *你有一辆油箱容量无限的的汽车，从第 i 个加油站开往第 i+1 个加油站需要消耗汽油 cost[i] 升。你从其中的一个加油站出发，开始时油箱为空。*
>
> *如果你可以绕环路行驶一周，则返回出发时加油站的编号，否则返回 -1。*

```java
class Solution {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int n = gas.length;
        int ans = 0;
        int curSpace = Integer.MAX_VALUE;
        int curIndex = 0;

        for (int i = 0; i < n; i++) {
            ans += gas[i] - cost[i];
            if (ans < curSpace) {
                curSpace = ans;
                curIndex = i;
            }
        }

        return ans < 0 ? -1 : (curIndex + 1) % n;
    }
}
```

## 分发糖果（0135）

> *老师想给孩子们分发糖果，有 N 个孩子站成了一条直线，老师会根据每个孩子的表现，预先给他们评分。*
>
> *你需要按照以下要求，帮助老师给这些孩子分发糖果：*
>
> *每个孩子至少分配到 1 个糖果。*
>
> *评分更高的孩子必须比他两侧的邻位孩子获得更多的糖果。*
>
> *那么这样下来，老师至少需要准备多少颗糖果呢？*

```java
class Solution {
    public int candy(int[] ratings) {
        int n = ratings.length;
        int[] tmp = new int[n];
        for (int i = 0; i < n; i++) {
            if (i > 0 && ratings[i - 1] < ratings[i]) {
                tmp[i] = tmp[i - 1] + 1;
            }
            else {
                tmp[i] = 1;
            }
        }

        int ans = 0, num = 0;
        for (int i = n - 1; i >= 0; i--) {
            if (i < n - 1 && ratings[i] > ratings[i + 1]) {
                num++;
            }
            else {
                num = 1;
            }
            ans += Math.max(tmp[i], num);
        }

        return ans;
    }
}
```

## 戳气球（0312）

> *有 n 个气球，编号为0 到 n - 1，每个气球上都标有一个数字，这些数字存在数组 nums 中。*
>
> *现在要求你戳破所有的气球。戳破第 i 个气球，你可以获得 nums[i - 1] \* nums[i] \* nums[i + 1] 枚硬币。* 
>
> *这里的 i - 1 和 i + 1 代表和 i 相邻的两个气球的序号。*
>
> *如果 i - 1或 i + 1 超出了数组的边界，那么就当它是一个数字为 1 的气球。*
>
> *求所能获得硬币的最大数量。*

```java
class Solution {
    public int maxCoins(int[] nums) {
        int n = nums.length;
        // rec[i][j] 表示[i,j]区间内的最大硬币数
        int[][] rec = new int[n + 2][n + 2];

        int[] val = new int[n + 2];
        val[0] = val[n + 1] = 1;
        for (int i = 1; i <= n; i++) {val[i] = nums[i - 1];}
        
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i + 2; j <= n + 1; j++) {
                for (int k = i + 1; k < j; k++) {
                    int sum = val[i] * val[k] * val[j];
                    sum += (rec[i][k] + rec[k][j]);
                    rec[i][j] = Math.max(rec[i][j], sum);
                }
            }
        }
        return rec[0][n + 1];
    }
}
```

## 只出现一次的数字

> *给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。*

```java
class Solution {
    public int singleNumber(int[] nums) {
        int ans = 0;
        for (int num : nums) {
            ans ^= num;
        }
        return ans;
    }
}
```

> *给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现了三次。找出那个只出现了一次的元素。*

```java
class Solution {
    public int singleNumber(int[] nums) {
        Set<Long> set = new HashSet<>();
        long sumSet = 0, sumArray = 0;
        for(int n : nums) {
            sumArray += n;
            set.add((long)n);
        }
        for(Long s : set) {sumSet += s;}
        return (int)((3 * sumSet - sumArray) / 2);
    }
}
```

> 给定一个整数数组 `nums`，其中恰好有两个元素只出现一次，其余所有元素均出现两次。 找出只出现一次的那两个元素。你可以按 **任意顺序** 返回答案。

```java
class Solution {
    public int[] singleNumber(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int n : nums) {
            if (set.contains(n)) {
                set.remove(n);
            }
            else {
                set.add(n);
            }
        }
        int[] ans = new int[set.size()];
        List<Integer> list = new ArrayList<>(set);
        for (int i = 0; i < list.size(); i++) {
            ans[i] = list.get(i);
        } 
        return ans;
    }
}
```

> *给定一个只包含整数的有序数组，每个元素都会出现两次，唯有一个数只会出现一次，找出这个数。*

```java
class Solution {
    public int singleNonDuplicate(int[] nums) {
        int ans = 0;
        for (int num : nums) {
            ans ^= num;
        }
        return ans;
    }
}

class Solution {
    public int singleNonDuplicate(int[] nums) {
        int left = 0;
        int right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == nums[mid ^ 1]) {
                left = mid + 1;
            }
            else {
                right = mid;
            }
        }
        return nums[left];
    }
}
```

## 数组中重复的数据（0442）

> *给定一个整数数组 a，其中1 ≤ a[i] ≤ n （n为数组长度）, 其中有些元素出现两次而其他元素出现一次。*
>
> *找到所有出现两次的元素。*
>
> *你可以不用到任何额外空间并在O(n)时间复杂度内解决这个问题吗？*

```java
class Solution {
    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> ans = new ArrayList<>();
        if (nums.length < 2) {return ans;}

        for (int i = 0; i < nums.length; i++) {
            while (nums[nums[i] - 1] != nums[i]) {
                swap(nums, i, nums[i] - 1);
            }
        }

        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != i + 1) {
                ans.add(nums[i]);
            }
        }

        return ans;
    }

    private void swap(int[] nums, int idx1, int idx2) {
        int tmp = nums[idx1];
        nums[idx1] = nums[idx2];
        nums[idx2] = tmp;
    }
}
```

## 找到所有数组中消失的数字（0448）

> *给定一个范围在 1 ≤ a[i] ≤ n ( n = 数组大小 ) 的 整型数组，数组中的元素一些出现了两次，另一些只出现一次。*
>
> *找到所有在 [1, n] 范围之间没有出现在数组中的数字。*

```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        int n = nums.length;
        for (int num : nums) {
            int x = (num - 1) % n;
            nums[x] += n;
        }
        
        List<Integer> ans = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (nums[i] <= n) {
                ans.add(i + 1);
            }
        }
        return ans;
    }
}
```

## 阶乘后的零（0172）

> *给定一个整数 n，返回 n! 结果尾数中零的数量。*

```java
class Solution {
    public int trailingZeroes(int n) {
        int count = 0;
        for (int i = 5; i <= n; i += 5) {
            int currentFactor = i;
            while (currentFactor % 5 == 0) {
                count++;
                currentFactor /= 5;
            }
        }
        return count;
    }
}
```

## 数字1的个数（0233）

> *给定一个整数 n，计算所有小于等于 n 的非负整数中数字 1 出现的个数。*

```java
class Solution {
    public int countDigitOne(int n) {
        int ans = 0;
        int base = 1;
        int k = 1;
        if (n <= 0) {return 0;}

        while (n / base != 0) {
            int cur = (n / base) % 10;
            int high = n / (base * 10);
            int low = n - n / base * base;
            if (cur > k) {
                ans += (high + 1) * base;
            }
            if (cur == k) {
                ans += high * base + low + 1;
            }
            if (cur < k) {
                ans += high * base;
            }
            base *= 10;
        }

        return ans;
    }
}
```

## 除自身以外数组的乘积（0238）

> *给你一个长度为 n 的整数数组 nums，其中 n > 1，返回输出数组 output ，其中 output[i] 等于 nums 中除 nums[i] 之外其余各元素的乘积。*

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;

        int[] left = new int[n];
        int[] right = new int[n];
        int[] ans = new int[n];

        left[0] = 1;
        for (int i = 1; i < n; i++) {left[i] = nums[i - 1] * left[i - 1];}

        right[n - 1] = 1;
        for (int i = n - 2; i >= 0; i--) {right[i] = nums[i + 1] * right[i + 1];}

        for (int i = 0; i < n; i++) {
            ans[i] = left[i] * right[i];
        }

        return ans;
    }
}
```

## 滑动窗口最大值（0239）

> *给你一个整数数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。*
>
> *返回滑动窗口中的最大值。*

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        PriorityQueue<int[]> pq = new PriorityQueue<>(new Comparator<int[]>() {
            public int compare(int[] A, int[] B) {
                return A[0] != B[0] ? B[0] - A[0] : B[1] - A[1];
            }
        });

        for (int i = 0; i < k; i++) {pq.offer(new int[]{nums[i], i});}

        int[] ans = new int[n - k + 1];
        ans[0] = pq.peek()[0];

        for (int i = k; i < n; i++) {
            pq.offer(new int[]{nums[i], i});
            while (pq.peek()[1] <= i - k) {pq.poll();}
            ans[i - k + 1] = pq.peek()[0];
        }

        return ans;
    }
}
```

## 拼接最大数（0321）

> *给定长度分别为 m 和 n 的两个数组，其元素由 0-9 构成，表示两个自然数各位上的数字。*
>
> *现在从这两个数组中选出 k (k <= m + n) 个数字拼接成一个新的数，要求从同一个数组中取出的数字保持其在原数组中的相对顺序。*

```java
class Solution {
    /**
     * 最大数来源于 nums1长度为s的子序列 和num2长度为k - s的子序列
     * 反证法可得 最大数来源于 nums1长度为s的最大子序列 和num2长度为k - s的最大子序列
     * 按最大值合并两个子序列， 即为结果
     * 时间复杂度 ： O(k * max(n, k) )
     */
    public int[] maxNumber(int[] nums1, int[] nums2, int k) {
        int[] ans = new int[k];
        if (nums1.length + nums2.length < k) {return ans;}
        for(int s = Math.max(k - nums2.length, 0); s <= Math.min(nums1.length , k); s++){
            List<Integer> l1 = getKLargestNumber(nums1, s);   //O(n1)
            List<Integer> l2 = getKLargestNumber(nums2, k - s);  //O(n2)
            int[] merge = merge(l1, l2);    //O(k)
            if(isBigger(merge, ans)) {ans = merge;}  //O(k)
        }
        return ans;
    }

    //获取长度为k的最大子序列
    public List<Integer> getKLargestNumber(int[] nums, int k){
        int n = nums.length;
        int popCnt = n - k;
        List<Integer> res = new ArrayList<>();
        if(k == 0)  {return res;}
        for(int i = 0; i < n; i++){
            while(!res.isEmpty() && res.get(res.size() - 1) < nums[i] && popCnt > 0){
                res.remove(res.size() - 1);
                popCnt--;
            }
            if(res.size() < k) {res.add(nums[i]);}
            else {popCnt--;}  //注意， 这里容易漏了， 如果没有添加到数组， pop--
        }
        return res;
    }

    //合并两个有序子序列， 成为一个最大值
    public int[] merge(List<Integer> l1, List<Integer> l2){
        int[] res = new int[l1.size() + l2.size()];
        int l = 0, r = 0, idx = 0;
        StringBuilder sb1 = new StringBuilder();
        StringBuilder sb2 = new StringBuilder();
        for(int i = l; i < l1.size(); i++) {sb1.append(l1.get(i));}
        for(int i = r; i < l2.size(); i++) {sb2.append(l2.get(i));}
        String a = sb1.toString();
        String b = sb2.toString();
        while (l < l1.size() || r < l2.size()){
            if(l >= l1.size()) {res[idx++] = l2.get(r++);}
            else if(r >= l2.size()) {res[idx++] = l1.get(l++);}
            else if(a.substring(l, l1.size()).compareTo(b.substring(r, l2.size())) >= 0){
                res[idx++] = l1.get(l++);
            }else{
                res[idx++] = l2.get(r++);
            }
        }
        return res;
    }

    //前面的数是否大于后面的数
    public boolean isBigger(int[] list, int[] ans){
        if(ans.length == 0) {return true;}
        for(int i = 0; i < list.length; i++){
            if (list[i] > ans[i]) {return true;}
            else if (list[i] < ans[i]) {return false;}
        }
        return false;
    }
}
```

## 猜数字（0299）

> *你写出一个秘密数字，并请朋友猜这个数字是多少。*
>
> *朋友每猜测一次，你就会给他一个提示，告诉他的猜测数字中有多少位属于数字和确切位置都猜对了（称为“Bulls”, 公牛），有多少位属于数字猜对了但是位置不对（称为“Cows”, 奶牛）。*
>
> *朋友根据提示继续猜，直到猜出秘密数字。*
>
> *请写出一个根据秘密数字和朋友的猜测数返回提示的函数，返回字符串的格式为 xAyB ，x 和 y 都是数字，A 表示公牛，用 B 表示奶牛。*

```java
class Solution {
    public String getHint(String secret, String guess) {
        // secretArray 和 guessArray 分别记录 两个字符串中非公牛的各个数字的数量
        int[] secretArray = new int[10];
        int[] guessArray = new int[10];
        // 公牛
        int A = 0;
        for (int i = 0; i < secret.length(); i++) {
            // 如果同位的数字相等则，公牛++
            if (secret.charAt(i) == guess.charAt(i)) {
                A++;
            } else {
                secretArray[secret.charAt(i) - '0']++;
                guessArray[guess.charAt(i) - '0']++;
            }
        }
        // 奶牛
        int B = 0;
        for (int i = 0; i < 10; i++) {
            // 不同位上的相同数字的数量
            B += Math.min(secretArray[i], guessArray[i]);
        }
        StringBuilder stringBuilder = new StringBuilder();
        return stringBuilder.append(A).append('A').append(B).append('B').toString();
    }
}
```

## 按要求补齐数组（0330）

> *给定一个已排序的正整数数组 nums，和一个正整数 n 。*
>
> *从 [1, n] 区间内选取任意个数字补充到 nums 中，使得 [1, n] 区间内的任何数字都可以用 nums 中某几个数字的和来表示。*
>
> *请输出满足上述要求的最少需要补充的数字个数。*

```java
class Solution {
    public int minPatches(int[] nums, int n) {
        int ans = 0;
        long x = 1;
        int index = 0;

        while (x <= n) {
            if (index < nums.length && nums[index] <= x) {
                x += nums[index];
                index++;
            }
            else {
                x *= 2;
                ans ++;
            }
        }

        return ans;
    }
}
```

## 水壶问题（0365）

> *有两个容量分别为 x升 和 y升 的水壶以及无限多的水。请判断能否通过使用这两个水壶，从而可以得到恰好 z升 的水？*
>
> *如果可以，最后请用以上水壶中的一或两个来盛放取得的 z升 水。*

```java
class Solution {
    public boolean canMeasureWater(int x, int y, int z) {
        if (x + y < z) {return false;}
        if (x == 0 || y == 0) {return z == 0 || x + y == z;}
        return z % gcd(x, y) == 0;
    }

    private int gcd(int x, int y) {
        int tmp = x % y;
        while (tmp != 0) {
            x = y; 
            y = tmp;
            tmp = x % y;
        }
        return y;
    }
}
```

## 摆动序列（0376）

> *如果连续数字之间的差严格地在正数和负数之间交替，则数字序列称为摆动序列。第一个差（如果存在的话）可能是正数或负数。少于两个元素的序列也是摆动序列。*
>
> *例如， [1,7,4,9,2,5] 是一个摆动序列，因为差值 (6,-3,5,-7,3) 是正负交替出现的。相反, [1,4,7,2,5] 和 [1,7,4,5,5] 不是摆动序列，第一个序列是因为它的前两个差值都是正数，第二个序列是因为它的最后一个差值为零。*
>
> *给定一个整数序列，返回作为摆动序列的最长子序列的长度。 通过从原始序列中删除一些（也可以不删除）元素来获得子序列，剩下的元素保持其原始顺序。*

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
                down[i] = Math.max(down[i - 1], up[i - 1] + 1);
                up[i] = up[i - 1];
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

## 文件的最长绝对路径（0388）

> *在文本格式中，如下所示(⟶表示制表符)：*
>
> *dir*
>
> *⟶ subdir1*
>
> *⟶ ⟶ file1.ext*
>
> *⟶ ⟶ subsubdir1*
>
> *⟶ subdir2*
>
> *⟶ ⟶ subsubdir2*
>
> *⟶ ⟶ ⟶ file2.ext*

```java
class Solution {
    public int lengthLongestPath(String input) {
        if (input.length() == 0) {return 0;}
        int ans = 0;
        int[] sum = new int[input.length() + 1];    //从1开始，第0层就是0

        for (String s : input.split("\n")) {
            System.out.println(s);
            int level = s.lastIndexOf('\t') + 2;    // 计算当前在第几层（从第一层开始，没有\t就是第一层）
            int len = s.length() - (level - 1);     // 计算当前这一行的长度
            if (s.contains(".")) {
                ans = Math.max(ans, sum[level - 1] + len);
            } else {
                sum[level] = sum[level - 1] + len + 1;  //是目录，要+1，目录有个/的
            }
        }
        return ans;
    }
}
```

## 消除游戏（0390）

> *给定一个从1 到 n 排序的整数列表。*
>
> *首先，从左到右，从第一个数字开始，每隔一个数字进行删除，直到列表的末尾。*
>
> *第二步，在剩下的数字中，从右到左，从倒数第一个数字开始，每隔一个数字进行删除，直到列表开头。*
>
> *我们不断重复这两步，从左到右和从右到左交替进行，直到只剩下一个数字。*
>
> *返回长度为 n 的列表中，最后剩下的数字。*

```java
class Solution {
    public int lastRemaining(int n) {
        if (n == 0) {return 0;}
        
        int first = 1;
        int factor = 1;
        boolean isLeft = true;
        boolean isEven = ((n & 1) == 0);

        while (n != 1) {
            if (isLeft || !isEven) {
                first += factor;
            }
            n = n >> 1;
            factor = factor << 1;
            isLeft = !isLeft;
            isEven = ((n & 1) == 0);
        }

        return first;
    }
}
```

## 完美矩形（0391）

> *我们有 N 个与坐标轴对齐的矩形, 其中 N > 0, 判断它们是否能精确地覆盖一个矩形区域。*

```java
class Solution {
    public boolean isRectangleCover(int[][] rectangles) {
        // 完美矩形的左下角和右上角坐标
        int X1 = Integer.MAX_VALUE, Y1 = Integer.MAX_VALUE;
        int X2 = Integer.MIN_VALUE, Y2 = Integer.MIN_VALUE;

        // 小矩形面积之和
        int areas = 0;
        // 记录所有顶点的出现情况
        Set<String> set = new HashSet<>();
        for (int[] tmp : rectangles) {
            int x1 = tmp[0], y1 = tmp[1], x2 = tmp[2], y2 = tmp[3];

            X1 = Math.min(X1, x1);
            Y1 = Math.min(Y1, y1);
            X2 = Math.max(X2, x2);
            Y2 = Math.max(Y2, y2);
            areas += (x2 - x1) * (y2 - y1);

            String[] curPoints = {x1 + " " + y1, x2 + " " + y2, x1 + " " + y2, x2 + " " + y1};
            for (String s : curPoints) {
                if (set.contains(s)) {set.remove(s);}
                else {set.add(s);}
            }
        }

        // 面积是否相等
        if (areas != (X2 - X1) * (Y2 - Y1)) {return false;}

        // 顶点情况是否满足
        if (set.size() != 4 || !set.contains(X1 + " " + Y1) || !set.contains(X2 + " " + Y2)
            || !set.contains(X1 + " " + Y2) || !set.contains(X2 + " " + Y1)) {
                return false;
            }
        return true;
    }
}
```

## 青蛙过河（0403）

> *一只青蛙想要过河。 假定河流被等分为若干个单元格，并且在每一个单元格内都有可能放有一块石子（也有可能没有）。 青蛙可以跳上石子，但是不可以跳入水中。*
>
> *给你石子的位置列表 stones（用单元格序号 升序 表示）， 请判定青蛙能否成功过河（即能否在最后一步跳至最后一块石子上）。*
>
> *开始时， 青蛙默认已站在第一块石子上，并可以假定它第一步只能跳跃一个单位（即只能从单元格 1 跳至单元格 2 ）。*
>
> *如果青蛙上一步跳跃了 k 个单位，那么它接下来的跳跃距离只能选择为 k - 1、k 或 k + 1 个单位。 另请注意，青蛙只能向前方（终点的方向）跳跃。*

```java
class Solution {
    public boolean canCross(int[] stones) {
        Map<Integer, Set<Integer>> map = new HashMap<>();
        int n = stones.length;
        for (int i = 0; i < n; i++) {
            map.put(stones[i], new HashSet<Integer>());
        }
        map.get(0).add(0);

        for (int i = 0; i < n; i++) {
            for (int k : map.get(stones[i])) {
                for (int step = k - 1; step <= k + 1; step++) {
                    if (step > 0 && map.containsKey(stones[i] + step)) {
                        map.get(stones[i] + step).add(step);
                    }
                }
            }
        }

        return map.get(stones[n - 1]).size() > 0;
    }
}
```

## 根据身高重建队列（0406）

> *假设有打乱顺序的一群人站成一个队列，数组 people 表示队列中一些人的属性（不一定按顺序）。每个 people[i] = [hi, ki] 表示第 i 个人的身高为 hi ，前面 正好 有 ki 个身高大于或等于 hi 的人。*
>
> *请你重新构造并返回输入数组 people 所表示的队列。返回的队列应该格式化为数组 queue ，其中 queue[j] = [hj, kj] 是队列中第 j 个人的属性（queue[0] 是排在队列前面的人）。*

```java
class Solution {
    public int[][] reconstructQueue(int[][] people) {
        Arrays.sort(people, new Comparator<int[]>(){
            public int compare(int[] A, int[] B) {
                return A[0] == B[0] ? B[1] - A[1] : A[0] - B[0];
            } 
        });

        int n = people.length;
        int[][] ans = new int[n][];
        for (int[] cur : people) {
            int space = cur[1] + 1;
            for (int i = 0; i < n; i++) {
                if (ans[i] == null) {
                    space--;
                    if (space == 0) {
                        ans[i] = cur;
                        break;
                    }
                }
            }
        }

        return ans;
    }
}
```

## 最小基因变化（0433）

> 一条基因序列由一个带有8个字符的字符串表示，其中每个字符都属于 "A", "C", "G", "T"中的任意一个。
>
> 假设我们要调查一个基因序列的变化。一次基因变化意味着这个基因序列中的一个字符发生了变化。
>
> 例如，基因序列由"AACCGGTT" 变化至 "AACCGGTA" 即发生了一次基因变化。
>
> 与此同时，每一次基因变化的结果，都需要是一个合法的基因串，即该结果属于一个基因库。
>
> 现在给定3个参数 — start, end, bank，分别代表起始基因序列，目标基因序列及基因库，请找出能够使起始基因序列变化为目标基因序列所需的最少变化次数。如果无法实现目标变化，请返回 -1。

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
                        if (end.equals(newGenetic)) {return step;}
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

## 环形数组是否存在循环（0457）

> 存在一个不含 0 的 环形 数组 nums ，每个 nums[i] 都表示位于下标 i 的角色应该向前或向后移动的下标个数：
>
> 如果 nums[i] 是正数，向前 移动 nums[i] 步
> 如果 nums[i] 是负数，向后 移动 nums[i] 步
> 因为数组是 环形 的，所以可以假设从最后一个元素向前移动一步会到达第一个元素，而第一个元素向后移动一步会到达最后一个元素。

```java
class Solution {
    public boolean circularArrayLoop(int[] nums) {
        int n = nums.length;
        if (n == 1 || n == 0) {return false;}
        if (n == 2 && nums[0] * nums[1] <= 0) {return false;}

        int[] nums2 = new int[n];
        for (int i = 0; i < n; i++) {nums2[i] = nums[i];}

        for (int i = 0; i < n; i++) {
            if (nums2[i] == 0) {continue;}
            else {
                int curNum = nums[i], curIndex = i;
                nums2[i] = 0;
                int circleLen = 0;
                while (true) {
                    int nextIndex = curIndex + curNum;
                    if (nextIndex >= 0) {nextIndex %= n;}
                    else {nextIndex = n - ((0 - nextIndex) % n);}

                    int nextNum = nums[nextIndex];
                    if (nextIndex == curIndex || nextNum * curNum <= 0) {break;}

                    if (circleLen > n) {return true;}

                    circleLen++;
                    nums2[nextIndex] = 0;
                    curNum = nextNum;
                    curIndex = nextIndex;
                }
            }
        }

        return false;
    }
}
```

## 供暖器（0475）

> 冬季已经来临。 你的任务是设计一个有固定加热半径的供暖器向所有房屋供暖。
>
> 在加热器的加热半径范围内的每个房屋都可以获得供暖。
>
> 现在，给出位于一条水平线上的房屋 houses 和供暖器 heaters 的位置，请你找出并返回可以覆盖所有房屋的最小加热半径。

```java
class Solution {
    // 双指针
    public int findRadius(int[] houses, int[] heaters) {
        // 1. 排序
        Arrays.sort(houses);
        Arrays.sort(heaters);

        // 2. 双指针计算最大半径
        // 下面的代码最难懂的地方是 Math.abs(heaters[i] - house) >= Math.abs(heaters[i + 1] - house)
        // 我们举一个例子来说明这行代码的意思：
        // houses：1，2，3，4
        // heaters：1，4
        // 对于 house 1，heater 1 比 heater 4 更接近 house1，所以不将 i 移动到 i + 1
        // 对于 house 2，heater 1 比 heater 4 更接近 house2，所以不将 i 移动到 i + 1
        // 对于 house 3，heater 4 比 heater 1 更接近 house3，所以将 i 移动到 i + 1
        // 对于 house 4，依次类推
        int ans = 0, idx = 0;
        for (int h : houses) {
            while (idx < heaters.length - 1 && Math.abs(heaters[idx] - h) >= Math.abs(heaters[idx + 1] - h)) {
                idx++;
            }
            ans = Math.max(ans, Math.abs(heaters[idx] - h));
        }

        return ans;
    }
}
```

## 翻转对（0493）

> 给定一个数组 nums ，如果 i < j 且 nums[i] > 2*nums[j] 我们就将 (i, j) 称作一个重要翻转对。
>
> 你需要返回给定数组中的重要翻转对的数量。
>
> 示例 1:
>
> 输入: [1,3,2,3,1]
> 输出: 2
> 示例 2:
>
> 输入: [2,4,3,5,1]
> 输出: 3

```java
class Solution {
    public int reversePairs(int[] nums) {
        if (nums.length == 0) {return 0;}
        return myMethod(nums, 0, nums.length - 1);
    }

    private int myMethod(int[] nums, int left, int right) {
        if (left == right) {return 0;}
        else {
            int mid = left + (right - left) / 2;
            int countLeft = myMethod(nums, left, mid);
            int countRight = myMethod(nums, mid + 1, right);
            int ans = countLeft + countRight;

            int i = left, j = mid + 1;
            while (i <= mid) {
                while (j <= right && (long)nums[i] > 2 * (long)nums[j]) {j++;}
                ans += j - mid - 1;
                i++;
            }

            int[] sorted = new int[right - left + 1];
            int p1 = left, p2 = mid + 1;
            int p = 0;
            while (p1 <= mid || p2 <= right) {
                if (p1 > mid) {
                    sorted[p++] = nums[p2++];
                }
                else if (p2 > right) {
                    sorted[p++] = nums[p1++];
                }
                else {
                    if (nums[p1] < nums[p2]) {
                        sorted[p++] = nums[p1++];
                    }
                    else {
                        sorted[p++] = nums[p2++];
                    }
                }
            }

            for (int k = 0; k < sorted.length; k++) {
                nums[left + k] = sorted[k];
            }
            return ans;
        }
    }
}
```

## 对角线遍历（0498）

> 给定一个含有 M x N 个元素的矩阵（M 行，N 列），请以对角线遍历的顺序返回这个矩阵中的所有元素，对角线遍历如下图所示。

```java
class Solution {
    public int[] findDiagonalOrder(int[][] matrix) {
        if (matrix == null || matrix.length == 0) {return new int[0];}

        int m = matrix.length, n = matrix[0].length;
        int[] ans = new int[m * n];

        int idx = 0;
        List<Integer> tmp = new ArrayList<>();

        for (int i = 0; i < m + n - 1; i++) {
            tmp.clear();

            int row = i < n ? 0 : i - n + 1;
            int col = i < n ? i : n - 1;

            while (row < m && col > -1) {
                tmp.add(matrix[row][col]);
                row++;
                col--;
            }

            if (i % 2 == 0) {
                Collections.reverse(tmp);
            }

            for (int j = 0; j < tmp.size(); j++) {
                ans[idx++] = tmp.get(j);
            }
        }

        return ans;
    }
}
```

## 求解方程（0640）

> *求解一个给定的方程，将x以字符串"x=#value"的形式返回。该方程仅包含'+'，' - '操作，变量 x 和其对应系数。*
>
> *如果方程没有解，请返回“No solution”。*
>
> *如果方程有无限解，则返回“Infinite solutions”。*
>
> *如果方程中只有一个解，要保证返回值 x 是一个整数。*

```java
class Solution {
    public String solveEquation(String equation) {
        String ans = null;
        try {
            String[] split = equation.split("=");
            String left = split[0], right = split[1];
            int[] leftAns = splitVar(left);
            int[] rightAns = splitVar(right);
            
            int xNum = leftAns[0] - rightAns[0];
            int constNum = rightAns[1] - leftAns[1];
            if (xNum != 0) {return "x=" + constNum / xNum;}
            if (constNum == 0) {return "Infinite solutions";}

            return "No solution";
        } catch (Exception e) {ans = "No solution";}
        return ans;
    }

    private static int[] splitVar(String s) {
        String[] split = s.replace("-", "+-").split("\\+");
        int sumX = 0;
        int sumS = 0;
        for (int i = 0; i < split.length; i++) {
            if (split[i].equals("x")) {
                sumX++;
            } else if (split[i].equals("-x")) {
                sumX--;
            } else if (split[i].contains("x")) {
                sumX += Integer.valueOf(split[i].substring(0, split[i].length() - 1));
            } else if (!split[i].equals("")) {
                sumS += Integer.valueOf(split[i]);
            } else {
                continue;
            }
        }
        return new int[]{sumX, sumS};
    }
}
```

## Dota2 参议院（0649）

> *Dota2 的世界里有两个阵营：Radiant(天辉)和 Dire(夜魇)*
>
> *Dota2 参议院由来自两派的参议员组成。现在参议院希望对一个 Dota2 游戏里的改变作出决定。他们以一个基于轮为过程的投票进行。在每一轮中，每一位参议员都可以行使两项权利中的一项：*
>
> *禁止一名参议员的权利：*
>
> *参议员可以让另一位参议员在这一轮和随后的几轮中丧失所有的权利。*
>
> *宣布胜利：*  *如果参议员发现有权利投票的参议员都是同一个阵营的，他可以宣布胜利并决定在游戏中的有关变化。*

```java
class Solution {
    public String predictPartyVictory(String senate) {
        int n = senate.length();
        Queue<Integer> radiant = new LinkedList<>();
        Queue<Integer> dire = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            if (senate.charAt(i) == 'R') {
                radiant.offer(i);
            }
            else {
                dire.offer(i);
            }
        }

        while (!radiant.isEmpty() && !dire.isEmpty()) {
            int radiantIndex = radiant.poll(), direIndex = dire.poll();
            if (radiantIndex < direIndex) {
                radiant.offer(radiantIndex + n);
            }
            else {
                dire.offer(direIndex + n);
            }
        }

        return !radiant.isEmpty() ? "Radiant" : "Dire";
    }
}
```

## 只有两个键的键盘（0650）

> *最初在一个记事本上只有一个字符 'A'。你每次可以对这个记事本进行两种操作：*
>
> *Copy All (复制全部) : 你可以复制这个记事本中的所有字符(部分的复制是不允许的)。*
>
> *Paste (粘贴) : 你可以粘贴你上一次复制的字符。*
>
> *给定一个数字 n 。你需要使用最少的操作次数，在记事本中打印出恰好 n 个 'A'。输出能够打印出 n 个 'A' 的最少操作次数。*

```java
class Solution {
    public int minSteps(int n) {
        int ans = 0, d = 2;
        while (n > 1) {
            while (n % d == 0) {
                ans += d;
                n /= d;
            }
            d++;
        }
        return ans;
    }
}
```

## 非递减数列（0665）

> *给你一个长度为 n 的整数数组，请你判断在 最多 改变 1 个元素的情况下，该数组能否变成一个非递减数列。*
>
> *我们是这样定义一个非递减数列的： 对于数组中任意的 i (0 <= i <= n-2)，总满足 nums[i] <= nums[i + 1]。*

```java
class Solution {
    public boolean checkPossibility(int[] nums) {
        int n = nums.length;
        int count = 0;
        for (int i = 0; i < n - 1; i++) {
            int x = nums[i], y = nums[i + 1];
            if (x > y) {
                count++;
                if (count > 1) {return false;}
                if (i > 0 && y < nums[i - 1]) {nums[i + 1] = x;}
            }
        }
        return true;
    }
}
```

## 最大交换（0670）

> *给定一个非负整数，你至多可以交换一次数字中的任意两位。返回你能得到的最大值。*

```java
class Solution {
    public int maximumSwap(int num) {
        String s = String.valueOf(num);
        int n = s.length();
        char[] charArray = s.toCharArray();

        int[] last = new int[10];
        for (int i = 0; i < n; i++) {
            last[charArray[i] - '0'] = i;
        }

        for (int i = 0; i < n - 1; i++) {
            for (int d = 9; d > charArray[i] - '0'; d--) {
                if (last[d] > i) {
                    swap(charArray, i, last[d]);
                    return Integer.parseInt(new String(charArray));
                }
            }
        }
        
        return num;
    }

    private static void swap(char[] charArray, int index1, int index2) {
        char tmp = charArray[index1];
        charArray[index1] = charArray[index2];
        charArray[index2] = tmp;
    }
}
```

## 24点游戏（0679）

> *你有 4 张写有 1 到 9 数字的牌。你需要判断是否能通过 \*，/，+，-，(，) 的运算得到 24。*

```java
class Solution {
    private static final int TARGET = 24;
    private static final double EPSILON = 1e-6;

    public boolean judgePoint24(int[] cards) {
        List<Double> ans = new ArrayList<>();
        for (int n : cards) {ans.add((double) n);}
        return myMethod(ans);
    }

    private static boolean myMethod(List<Double> list) {
        if (list.size() == 0) {return false;}
        if (list.size() == 1) {return Math.abs(list.get(0) - TARGET) < EPSILON;}

        int size = list.size();
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) {
                if (i != j) {
                    List<Double> list2 = new ArrayList<Double>();
                    for (int k = 0; k < size; k++) {
                        if (k != i && k != j) {
                            list2.add(list.get(k));
                        }
                    }
                    for (int k = 0; k < 4; k++) {
                        if (k < 2 && i > j) {
                            continue;
                        }
                        if (k == 0) {
                            list2.add(list.get(i) + list.get(j));
                        } else if (k == 1) {
                            list2.add(list.get(i) * list.get(j));
                        } else if (k == 2) {
                            list2.add(list.get(i) - list.get(j));
                        } else if (k == 3) {
                            if (Math.abs(list.get(j)) < EPSILON) {
                                continue;
                            } else {
                                list2.add(list.get(i) / list.get(j));
                            }
                        }
                        if (myMethod(list2)) {return true;}
                        list2.remove(list2.size() - 1);
                    }
                }
            }
        }
        return false;
    }
}
```

## 验证IP地址

> 编写一个函数来验证输入的字符串是否是有效的 IPv4 或 IPv6 地址。
>
> 如果是有效的 IPv4 地址，返回 "IPv4" ；
> 如果是有效的 IPv6 地址，返回 "IPv6" ；
> 如果不是上述类型的 IP 地址，返回 "Neither" 。
> IPv4 地址由十进制数和点来表示，每个地址包含 4 个十进制数，其范围为 0 - 255， 用(".")分割。比如，172.16.254.1；
>
> 同时，IPv4 地址内的数不会以 0 开头。比如，地址 172.16.254.01 是不合法的。
>
> IPv6 地址由 8 组 16 进制的数字来表示，每组表示 16 比特。这些组数字通过 (":")分割。比如,  2001:0db8:85a3:0000:0000:8a2e:0370:7334 是一个有效的地址。而且，我们可以加入一些以 0 开头的数字，字母可以使用大写，也可以是小写。所以， 2001:db8:85a3:0:0:8A2E:0370:7334 也是一个有效的 IPv6 address地址 (即，忽略 0 开头，忽略大小写)。

```java
class Solution {
    public String validIPAddress(String IP) {
        if (IP.chars().filter(c -> c == '.').count() == 3) {
            return validateIPv4(IP);
        }
        else if (IP.chars().filter(c -> c == ':').count() == 7) {
            return validateIPv6(IP);
        }
        return "Neither";
    }

    private static String validateIPv4(String IP) {
        String[] nums = IP.split("\\.", -1);
        for (String s : nums) {
            if (s.length() == 0 || s.length() > 3 || (s.charAt(0) == '0' && s.length() != 1)) {return "Neither";}
            for (char c : s.toCharArray()) {
                if (!Character.isDigit(c)) {return "Neither";}
            }
            if (Integer.parseInt(s) > 255) {return "Neither";}
        }
        return "IPv4";
    }

    private static String validateIPv6(String IP) {
        String[] nums = IP.split(":", -1);
        String hexDigits = "0123456789abcdefABCDEF";
        for (String s : nums) {
            if (s.length() == 0 || s.length() > 4) {return "Neither";}
            for (char c : s.toCharArray()) {
                if (hexDigits.indexOf(c) == -1) {return "Neither";}
            }
        }
        return "IPv6";
    }
}
```

## 掉落的方块（0699）

> *在无限长的数轴（即 x 轴）上，我们根据给定的顺序放置对应的正方形方块。*
>
> *第 i 个掉落的方块（positions[i] = (left, side_length)）是正方形，其中 left 表示该方块最左边的点位置(positions[i][0])，side_length 表示该方块的边长(positions[i][1])。*
>
> *每个方块的底部边缘平行于数轴（即 x 轴），并且从一个比目前所有的落地方块更高的高度掉落而下。在上一个方块结束掉落，并保持静止后，才开始掉落新方块。*
>
> *方块的底边具有非常大的粘性，并将保持固定在它们所接触的任何长度表面上（无论是数轴还是其他方块）。邻接掉落的边不会过早地粘合在一起，因为只有底边才具有粘性。*
>
> *返回一个堆叠高度列表 ans 。每一个堆叠高度 ans[i] 表示在通过 positions[0], positions[1], ..., positions[i] 表示的方块掉落结束后，目前所有已经落稳的方块堆叠的最高高度。*

```java
class Solution {
    public List<Integer> fallingSquares(int[][] positions) {
        int n = positions.length;
        int[] heights = new int[n];
        for (int i = 0; i < n; i++) {
            int left = positions[i][0];
            int size = positions[i][1];
            int right = left + size;
            heights[i] += size;

            for (int j = i + 1; j < n; j++) {
                int left2 = positions[j][0];
                int size2 = positions[j][1];
                int right2 = left2 + size2;
                if (left2 < right && left < right2) {
                    heights[j] = Math.max(heights[j], heights[i]);
                }
            }
        }

        List<Integer> ans = new ArrayList<>();
        int cur = -1;
        for (int x : heights) {
            cur = Math.max(cur, x);
            ans.add(cur);
        }
        return ans;
    }
}
```



