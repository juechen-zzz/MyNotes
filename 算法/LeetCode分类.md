[TOC]

# 1 哈希表

## 两数之和

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

## 有效的数独

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



# 2 数学问题

## 寻找两个正序数组的中位数（0004）

> *给定两个大小为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。请你找出并返回这两个正序数组的中位数。*
>
> *输入：nums1 = [1,3], nums2 = [2]*
>
> *输出：2.00000*
>
> *解释：合并数组 = [1,2,3] ，中位数 2*

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int m = nums1.length;
        int n = nums2.length;
        int len = m + n;

        int left = -1, right = -1;
        int aStart = 0, bStart = 0;

        for (int i = 0; i <= len / 2; i++){
            left = right;
            // 如果 aStart 还没有到最后并且此时 nums1 位置的数字小于 nums1 位置的数组，那么就可以后移了。
            // 如果 B 数组此刻已经没有数字了，继续取数字 B[ bStart ]，则会越界，所以判断下 bStart 是否大于数组长度了，这样 || 后面的不会执行
            if (aStart < m && (bStart >= n || nums1[aStart] < nums2[bStart])){
                right = nums1[aStart++];
            } else {
                right = nums2[bStart++];
            }
        }

        if ((len & 1) == 0) {return (left + right) / 2.0;}
        else {return right;}
    }
}
```

## Z字形变化（0006）

> *将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。*
>
> *比如输入字符串为 "LEETCODEISHIRING" 行数为 3 时，排列如下：*
>
> *L   C   I   R*
>
> *E T O E S I I G*
>
> *E   D   H   N*
>
> *输入: s = "LEETCODEISHIRING", numRows = 3*
>
> *输出: "LCIRETOESIIGEDHN"*

```java
class Solution {
    public String convert(String s, int numRows) {
        if (numRows == 1) {return s;}
        List<StringBuilder> rows = new ArrayList<>();
        for (int i = 0; i < Math.min(s.length(), numRows); i++){
            rows.add(new StringBuilder());
        }

        int curRow = 0;
        boolean goingDown = false;
        for (char c : s.toCharArray()) {
            rows.get(curRow).append(c);
            if (curRow == 0 || curRow == numRows - 1) {goingDown = !goingDown;}
            curRow += goingDown ? 1 : -1;
        }

        StringBuilder res = new StringBuilder();
        for (StringBuilder row : rows) {res.append(row);}
        return res.toString();
    }
}
```

## 整数反转（0007）

> *给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。*
>
> *输入: 123*
>
> *输出: 321*

```java
class Solution {
    public int reverse(int x) {
        int ans = 0;
        while (x != 0) {
            int pop = x % 10;
            x /= 10;
            if (ans > Integer.MAX_VALUE / 10 || ans < Integer.MIN_VALUE / 10) {return 0;}
            ans = ans * 10 + pop;
        }
        return ans;
    }
}
```

## 盛最多水的容器（0011）

> *给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0) 。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。*
>
> *输入：[1,8,6,2,5,4,8,3,7]*
>
> *输出：49* 
>
> *解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。*

```java
class Solution {
    public int maxArea(int[] height) {
        if (height.length < 2) {return 0;}
        int ans = Integer.MIN_VALUE;
        int left = 0, right = height.length - 1;
        while (left < right){
            int curSize = (right - left) * Math.min(height[left], height[right]);
            ans = Math.max(ans, curSize);
            if (height[left] < height[right]){left++;}
            else {right--;}
        }
        return ans;
    }
}
```

##  整数转罗马（0012 && 0013）

> *罗马数字包含以下七种字符: I， V， X， L，C，D 和 M。*
>
> *字符          数值*
>
> *I             1*
>
> *V             5*
>
> *X             10*
>
> *L             50*
>
> *C             100*
>
> *D             500*
>
> *M             1000*

```java
class Solution {
    int[] values = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};    
    String[] symbols = {"M","CM","D","CD","C","XC","L","XL","X","IX","V","IV","I"};

    public String intToRoman(int num) {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < values.length && num >= 0; i++) {
            while (values[i] <= num) {
                num -= values[i];
                sb.append(symbols[i]);
            }
        }
        return sb.toString();
    }
}
```

```java
class Solution {
    public int romanToInt(String s) {
        Map<String, Integer> map = new HashMap<>();
        map.put("I", 1);
        map.put("IV", 4);
        map.put("V", 5);
        map.put("IX", 9);
        map.put("X", 10);
        map.put("XL", 40);
        map.put("L", 50);
        map.put("XC", 90);
        map.put("C", 100);
        map.put("CD", 400);
        map.put("D", 500);
        map.put("CM", 900);
        map.put("M", 1000);

        int ans = 0;
        for (int i = 0; i < s.length();){
            if (i + 1 < s.length() && map.containsKey(s.substring(i, i+2))) {
                ans += map.get(s.substring(i, i+2));
                i += 2;
            }
            else {
                ans += map.get(s.substring(i, i+1));
                i++;
            }
        }
        return ans;
    }
}
```

## N数之和（0015 && 0016 && 0018）

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

## 删除排序数组中的重复项（0026 && 0080）

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

## 移除元素（0027）

> *给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。*
>
> *不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并 原地 修改输入数组。*
>
> *元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。*
>
> *给定 nums = [3,2,2,3], val = 3,*
>
> *函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。*

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            if (nums[left] == val) {
                int tmp = nums[right];
                nums[right] = nums[left];
                nums[left] = tmp;
                right--;
            }
            else {
                left++;
            }
        }
        return right + 1;
    }
}
```

## 下一个排列（0031）

> *实现获取 下一个排列 的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。*
>
> *如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。*
>
> *必须 原地 修改，只允许使用额外常数空间。*
>
> *输入：nums = [1,2,3]*
>
> *输出：[1,3,2]*

```java
class Solution {
    public void nextPermutation(int[] nums) {
        int index = nums.length - 1;
        while (index >= 1) {
            if (nums[index - 1] >= nums[index]) {
                index--;
            }
            else {break;}
        }

        if (index > 0) {
            int left = index - 1;
            int right = nums.length - 1;
            while (nums[left] >= nums[right]) {
                right--;
            }
            int tmp = nums[left];
            nums[left] = nums[right];
            nums[right] = tmp;
            Arrays.sort(nums, index, nums.length);
        }
        else {
            Arrays.sort(nums);
        }
        return;
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

## 跳跃游戏（0045 && 0055）

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


## 螺旋矩阵（0054 && 0059）

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

## 合并区间（0056）

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

## 插入区间（0057）

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

## 汇总区间（0228）

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

## 无重叠区间（0435）

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

## 加一（0066）

> *给定一个由 整数 组成的 非空 数组所表示的非负整数，在该数的基础上加一。*
>
> *最高位数字存放在数组的首位， 数组中每个元素只存储单个数字。*
>
> *你可以假设除了整数 0 之外，这个整数不会以零开头。*

```java
class Solution {
    public int[] plusOne(int[] digits) {
        if (digits.length == 0) {return digits;}
        int idx = digits.length - 1;
        while (idx >= 0) {
            if (digits[idx] != 9) {
                digits[idx]++;
                break;
            }
            else {
                digits[idx] = 0;
            }
            idx--;
        }
        // 考虑9999这种情况
        if (idx == -1) {
            int[] ans = new int[digits.length + 1];
            ans[0] = 1;
            for (int i = 1; i <= digits.length; i++) {ans[i] = digits[i - 1];}
            return ans;
        }
        else {
            return digits;
        }
    }
}
```

## 颜色分类（0075）

> *给定一个包含红色、白色和蓝色，一共 n 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。*

```java
class Solution {
    public void sortColors(int[] nums) {
        int n = nums.length;
        int idx = 0;
        for (int i = 0; i < n; i++) {
            if (nums[i] == 0) {
                int tmp = nums[i];
                nums[i] = nums[idx];
                nums[idx] = tmp;
                idx++;
            }
        }
        for (int i = idx; i < n; i++) {
            if (nums[i] == 1) {
                int tmp = nums[i];
                nums[i] = nums[idx];
                nums[idx] = tmp;
                idx++;
            }
        }
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

## 杨辉三角（0118）

> *给定一个非负整数 numRows，生成杨辉三角的前 numRows 行。*

```java
class Solution {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> ans = new ArrayList<>();
        for (int i = 0; i < numRows; i++) {
            List<Integer> tmp = new ArrayList<>();
            for (int j = 0; j <= i; j++) {
                if (j == 0 || j == i) {
                    tmp.add(1);
                }
                else {
                    tmp.add(ans.get(i - 1).get(j - 1) + ans.get(i - 1).get(j));
                }
            }
            ans.add(new ArrayList<>(tmp));
        }
        return ans;
    }
}
```

## 前K个高频元素（0347）

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

## 打家劫舍

> *你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。*
>
> *给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。*

```java
class Solution {
    public int rob(int[] nums) {
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
        int[] diff = new int[n];
        for (int i = 0; i < n; i++) {diff[i] = gas[i] - cost[i];}

        int sum = 0;
        for (int num : diff) {sum += num;}
        if (sum < 0) {return -1;}

        int d = 0, ans = 0;
        for (int i = 0; i < n; i++) {
            d += diff[i];
            if (d < 0) {
                ans = (i + 1) % n;
                d = 0;
            }
        }
        return ans;
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

## 只出现过一次的数字（0136 && 0137）

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

## 寻找峰值（0162）

> *峰值元素是指其值大于左右相邻值的元素。*
>
> *给你一个输入数组 nums，找到峰值元素并返回其索引。数组可能包含多个峰值，在这种情况下，返回 任何一个峰值 所在位置即可。*
>
> *你可以假设 nums[-1] = nums[n] = -∞ 。*

```java
public class Solution {
    public int findPeakElement(int[] nums) {
        for (int i = 0; i < nums.length - 1; i++) {
            if (nums[i] > nums[i + 1]) {return i;}
        }
        return nums.length - 1;
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

## H指数（0264）

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
>

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

> *统计所有小于非负整数 n 的质数的数量*

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
>

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

# 3 DP 

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

## 零钱兑换（0322）

> *给定不同面额的硬币 coins 和一个总金额 amount。*
>
> *编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。*
>
> *可以认为每种硬币的数量是无限的。*

```java
public class Solution {
    public int coinChange(int[] coins, int amount) {
        int max = amount + 1;
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, max);
        
        dp[0] = 0;
        for (int i = 1; i <= amount; i++) {
            for (int j = 0; j < coins.length; j++) {
                if (coins[j] <= i) {
                    dp[i] = Math.min(dp[i], 1 + dp[i - coins[j]]);
                }
            }
        }
        return dp[amount] > amount ? -1 : dp[amount];
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
>

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



# 4 位运算

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



# 5 栈

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



# 6 二分

## 搜索旋转排序数组（0033 && 0081）

> *升序排列的整数数组 nums 在预先未知的某个点上进行了旋转（例如， [0,1,2,4,5,6,7] 经旋转后可能变为 [4,5,6,7,0,1,2] ）。*
>
> *请你在数组中搜索 target ，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。*
>
> *输入：nums = [4,5,6,7,0,1,2], target = 0*
>
> *输出：4*

```java
class Solution {
    public int search(int[] nums, int target) {
        int n = nums.length;
        if (n == 0) {return -1;}
        if (n == 1) {return nums[0] == target ? 0 : -1;}
        int left = 0, right = n - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {return mid;}
            if (nums[left] <= nums[mid]) {
                if (nums[left] <= target && nums[mid] > target) {
                    right = mid - 1;
                }
                else {
                    left = mid + 1;
                }
            }
            else {
                if (nums[mid] < target && nums[right] >= target) {
                    left = mid + 1;
                } 
                else {
                    right = mid - 1;
                }
            }
        }
        return -1;
    }
}
```

> *假设按照升序排序的数组在预先未知的某个点上进行了旋转。*
>
> *( 例如，数组 [0,0,1,2,2,5,6] 可能变为 [2,5,6,0,0,1,2] )。*
>
> *编写一个函数来判断给定的目标值是否存在于数组中。若存在返回 true，否则返回 false。*

```java
class Solution {
    public boolean search(int[] nums, int target) {
        int n = nums.length;
        if (n == 0) {return false;}
        if (n == 1) {return nums[0] == target;}
        int left = 0, right = n - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {return true;}
            if (nums[left] == nums[mid]) {
                left++;
                continue;
            }
            if (nums[left] <= nums[mid]) {
                if (nums[left] <= target && target < nums[mid]) {
                    right = mid - 1;
                }
                else {
                    left = mid + 1;
                }
            }
            else {
                if (nums[mid] < target && target <= nums[right]) {
                    left = mid + 1;
                }
                else {
                    right = mid - 1;
                }
            }
        }

        return false;
    }
}
```

## 在排序数组中查找元素的第一个和最后一个位置（0034）

> *给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。*
>
> *如果数组中不存在目标值 target，返回 [-1, -1]。*
>
> *输入：nums = [5,7,7,8,8,10], target = 8*
>
> *输出：[3,4]*

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int leftIndex = binarySearch(nums, target, true);
        int rightIndex = binarySearch(nums, target, false);
        if (leftIndex <= rightIndex && rightIndex < nums.length && nums[leftIndex] == target && nums[rightIndex] == target) {
            return new int[]{leftIndex, rightIndex};
        }
        return new int[]{-1, -1};
    }

    public int binarySearch(int[] nums, int target, boolean lower) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = (left + right) / 2;
            if (nums[mid] > target || (lower && nums[mid] >= target)) {
                right = mid - 1;
            }
            else {
                left = mid + 1;
            }
        }
        return lower ? left : right;
    }
}
```

## 寻找旋转排序数组中的最小值（0153）

> *假设按照升序排序的数组在预先未知的某个点上进行了旋转。例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] 。*
>
> *请找出其中最小的元素。*

```java
class Solution {
    public int findMin(int[] nums) {
        int left = 0;
        int right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < nums[right]) {
                right = mid;
            } else if (nums[mid] > nums[right]) {
                left = mid + 1;
            } else {
                right -= 1;
            }
        }
        return nums[left];
    }
}
```

## x的平方根（0069）

> *实现 int sqrt(int x) 函数。*

```java
class Solution {
    public int mySqrt(int x) {
        int left = 0, right = x;
        int ans = -1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if ((long)mid * mid <= x) {
                ans = mid;
                left = mid + 1;
            }
            else {
                right = mid - 1;
            }
        }
        return ans;
    }
}
```

## 第一个错误的版本（0278）

> *假设你有 n 个版本 [1, 2, ..., n]，你想找出导致之后所有版本出错的第一个错误的版本。*

```java
public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int left = 1, right = n;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (isBadVersion(mid)) {
                right = mid;
            }
            else {
                left = mid + 1;
            }
        }
        return left;
    }
}
```

## 寻找重复数（0287）

> *给定一个包含 n + 1 个整数的数组 nums ，其数字都在 1 到 n 之间（包括 1 和 n），可知至少存在一个重复的整数。*
>
> *假设 nums 只有 一个重复的整数 ，找出 这个重复的数 。*

```java
class Solution {
    public int findDuplicate(int[] nums) {
        int n = nums.length;
        int left = 1, right = n - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            int count = 0;
            for (int i = 0; i < n; i++) {
                if (nums[i] <= mid) {
                    count++;
                }
            }

            // 等价于找右边界
            if (count <= mid) {
                left = mid + 1;
            }
            else {
                right = mid - 1;
            }
        }
        return right + 1;
    }
}
```

## 有序数组中第K小的元素（0378）

> *给你一个 n x n 矩阵 matrix ，其中每行和每列元素均按升序排序，找到矩阵中第 k 小的元素。*

```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int left = matrix[0][0];
        int right = matrix[n - 1][n - 1];
        while (left < right) {
            int mid = left + ((right - left) >> 1);
            if (check(matrix, mid, k, n)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }

    public boolean check(int[][] matrix, int mid, int k, int n) {
        int i = n - 1;
        int j = 0;
        int num = 0;
        while (i >= 0 && j < n) {
            if (matrix[i][j] <= mid) {
                num += i + 1;
                j++;
            } else {
                i--;
            }
        }
        return num >= k;
    }
}
```

## 反转字符串中的元音字母（0345）

> *编写一个函数，以字符串作为输入，反转该字符串中的元音字母。*

```java
class Solution {
    private final static HashSet<Character> vowels = new HashSet<>(Arrays.asList('a', 'e', 'i', 'o', 'u', 'A', 'E', 'I', 'O', 'U'));

    public String reverseVowels(String s) {
        if (s.length() == 0) {return s;}
        int left = 0, right = s.length() - 1;
        char[] c = new char[s.length()];

        while (left <= right) {
            char c1 = s.charAt(left);
            char c2 = s.charAt(right);
            if (!vowels.contains(c1)) {
                c[left++] = c1;
            }
            else if (!vowels.contains(c2)) {
                c[right--] = c2;
            }
            else {
                c[left++] = c2;
                c[right--] = c1;
            }
        }

        return String.valueOf(c);
    }
}
```

## 有序数组中第K小的元素（0378）

> *给你一个 n x n 矩阵 matrix ，其中每行和每列元素均按升序排序，找到矩阵中第 k 小的元素。*

```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int left = matrix[0][0];
        int right = matrix[n - 1][n - 1];
        while (left < right) {
            int mid = left + ((right - left) >> 1);
            if (check(matrix, mid, k, n)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }

    private boolean check(int[][] matrix, int mid, int k, int n) {
        int i = n - 1, j = 0;
        int count = 0;
        while (i >= 0 && j < n) {
            if (matrix[i][j] <= mid) {
                count += (i + 1);
                j++;
            }
            else {
                i--;
            }
        }
        return count >= k;
    }
}
```

# 7 数据结构

## LRU缓存机制（0146）

> *运用你所掌握的数据结构，设计和实现一个 LRU (最近最少使用) 缓存机制 。*
>
> *实现 LRUCache 类：*
>
> *LRUCache(int capacity) 以正整数作为容量 capacity 初始化 LRU 缓存*
>
> *int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。*
>
> *void put(int key, int value) 如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字-值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。*
>
> 
>
> *示例：*
>
> *输入*
>
> *["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]*
>
> *[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]*
>
> *输出*
>
> *[null, null, null, 1, null, -1, null, -1, 3, 4]*

```java
class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int CACHE_SIZE;

    /**
     * 传递进来最多能缓存多少数据
     *
     * @param cacheSize 缓存大小
     */
    public LRUCache(int cacheSize) {
        // true 表示让 linkedHashMap 按照访问顺序来进行排序，最近访问的放在头部，最老访问的放在尾部。
        super((int) Math.ceil(cacheSize / 0.75) + 1, 0.75f, true);
        CACHE_SIZE = cacheSize;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        // 当 map中的数据量大于指定的缓存个数的时候，就自动删除最老的数据。
        return size() > CACHE_SIZE;
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
public class LRUCache {
    class DLinkedNode {
        int key;
        int value;
        DLinkedNode prev;
        DLinkedNode next;
        public DLinkedNode() {}
        public DLinkedNode(int _key, int _value) {key = _key; value = _value;}
    }

    private Map<Integer, DLinkedNode> cache = new HashMap<Integer, DLinkedNode>();
    private int size;
    private int capacity;
    private DLinkedNode head, tail;

    public LRUCache(int capacity) {
        this.size = 0;
        this.capacity = capacity;
        // 使用伪头部和伪尾部节点
        head = new DLinkedNode();
        tail = new DLinkedNode();
        head.next = tail;
        tail.prev = head;
    }

    public int get(int key) {
        DLinkedNode node = cache.get(key);
        if (node == null) {
            return -1;
        }
        // 如果 key 存在，先通过哈希表定位，再移到头部
        moveToHead(node);
        return node.value;
    }

    public void put(int key, int value) {
        DLinkedNode node = cache.get(key);
        if (node == null) {
            // 如果 key 不存在，创建一个新的节点
            DLinkedNode newNode = new DLinkedNode(key, value);
            // 添加进哈希表
            cache.put(key, newNode);
            // 添加至双向链表的头部
            addToHead(newNode);
            ++size;
            if (size > capacity) {
                // 如果超出容量，删除双向链表的尾部节点
                DLinkedNode tail = removeTail();
                // 删除哈希表中对应的项
                cache.remove(tail.key);
                --size;
            }
        }
        else {
            // 如果 key 存在，先通过哈希表定位，再修改 value，并移到头部
            node.value = value;
            moveToHead(node);
        }
    }

    private void addToHead(DLinkedNode node) {
        node.prev = head;
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
    }

    private void removeNode(DLinkedNode node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    private void moveToHead(DLinkedNode node) {
        removeNode(node);
        addToHead(node);
    }

    private DLinkedNode removeTail() {
        DLinkedNode res = tail.prev;
        removeNode(res);
        return res;
    }
}
```

```java
public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    
private final int CACHE_SIZE;

    // 这里就是传递进来最多能缓存多少数据
    public LRUCache(int cacheSize) {
        super((int) Math.ceil(cacheSize / 0.75) + 1, 0.75f, true); // 这块就是设置一个hashmap的初始大小，同时最后一个true指的是让linkedhashmap按照访问顺序来进行排序，最近访问的放在头，最老访问的就在尾
        CACHE_SIZE = cacheSize;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry eldest) {
        return size() > CACHE_SIZE; // 这个意思就是说当map中的数据量大于指定的缓存个数的时候，就自动删除最老的数据
    }

}
```



## 最小栈（0155）

> *设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。*
>
> *push(x) —— 将元素 x 推入栈中。*
>
> *pop() —— 删除栈顶的元素。*
>
> *top() —— 获取栈顶元素。*
>
> *getMin() —— 检索栈中的最小元素。*
>
>  
>
> *示例:*
>
> *输入：*
>
> *["MinStack","push","push","push","getMin","pop","top","getMin"]*
>
> *[[],[-2],[0],[-3],[],[],[],[]]*
>
> *输出：*
>
> *[null,null,null,null,-3,null,0,-2]*

```java
class MinStack {
    Deque<Integer> stack;

    /** initialize your data structure here. */
    public MinStack() {
        stack = new LinkedList<>();
    }
    
    public void push(int x) {
        stack.offerLast(x);
    }
    
    public void pop() {
        stack.pollLast();
    }
    
    public int top() {
        return stack.peekLast();
    }
    
    public int getMin() {
        int minValue = Integer.MAX_VALUE;
        for (int n : stack) {
            if (n < minValue) {
                minValue = n;
            }
        }
        return minValue;
    }
}

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack obj = new MinStack();
 * obj.push(x);
 * obj.pop();
 * int param_3 = obj.top();
 * int param_4 = obj.getMin();
 */
```

> *实现一个二叉搜索树迭代器。你将使用二叉搜索树的根节点初始化迭代器。*

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class BSTIterator {
    List<Integer> list;
    int index;

    public BSTIterator(TreeNode root) {
        list = new ArrayList<>();
        this.index = 0;
        this._inorder(root);
    }

    public void _inorder(TreeNode root) {
        if (root == null) {return;}
        this._inorder(root.left);
        list.add(root.val);
        this._inorder(root.right);
    }
    
    public int next() {
        return list.get(index++);
    }
    
    public boolean hasNext() {
        return index < list.size();
    }
}

/**
 * Your BSTIterator object will be instantiated and called as such:
 * BSTIterator obj = new BSTIterator(root);
 * int param_1 = obj.next();
 * boolean param_2 = obj.hasNext();
 */
```

## 前缀树（0208）

> *实现一个 Trie (前缀树)，包含 insert, search, 和 startsWith 这三个操作*

```java
class Trie {

    class TireNode {
        private boolean isEnd;
        TireNode[] next;

        public TireNode() {
            isEnd = false;
            next = new TireNode[26];
        }
    }

    private TireNode root;

    public Trie() {
        root = new TireNode();
    }

    public void insert(String word) {
        TireNode node = root;
        for (char c : word.toCharArray()) {
            if (node.next[c - 'a'] == null) {
                node.next[c - 'a'] = new TireNode();
            }
            node = node.next[c - 'a'];
        }
        node.isEnd = true;
    }

    public boolean search(String word) {
        TireNode node = root;
        for (char c : word.toCharArray()) {
            node = node.next[c - 'a'];
            if (node == null) {
                return false;
            }
        }
        return node.isEnd;
    }

    public boolean startsWith(String prefix) {
        TireNode node = root;
        for (char c : prefix.toCharArray()) {
            node = node.next[c - 'a'];
            if (node == null) {
                return false;
            }
        }
        return true;
    }
}
/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * boolean param_2 = obj.search(word);
 * boolean param_3 = obj.startsWith(prefix);
 */
```

## 队列实现栈（0225）

> *请你仅使用两个队列实现一个后入先出（LIFO）的栈，并支持普通队列的全部四种操作（push、top、pop 和 empty）。*

```java
class MyStack {
    Queue<Integer> queue1;
    Queue<Integer> queue2;

    /** Initialize your data structure here. */
    public MyStack() {
        queue1 = new LinkedList<>();
        queue2 = new LinkedList<>();
    }
    
    /** Push element x onto stack. */
    public void push(int x) {
        queue2.offer(x);
        while (!queue1.isEmpty()) {
            queue2.offer(queue1.poll());
        }
        Queue<Integer> temp = queue1;
        queue1 = queue2;
        queue2 = temp;
    }
    
    /** Removes the element on top of the stack and returns that element. */
    public int pop() {
        return queue1.poll();
    }
    
    /** Get the top element. */
    public int top() {
        return queue1.peek();
    }
    
    /** Returns whether the stack is empty. */
    public boolean empty() {
        return queue1.isEmpty();
    }
}

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack obj = new MyStack();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.top();
 * boolean param_4 = obj.empty();
 */
```

## 栈实现队列（0232）

```java
import java.util.Stack;

public class MyQueue {

    private Stack<Integer> stackPush;
    private Stack<Integer> stackPop;

    /**
     * Initialize your data structure here.
     */
    public MyQueue() {
        stackPush = new Stack<>();
        stackPop = new Stack<>();
    }

    /**
     * Push element x to the back of queue.
     */
    public void push(int x) {
        stackPush.push(x);
    }

    /**
     * 辅助方法：一次性将 stackPush 里的所有元素倒入 stackPop
     * 注意：1、该操作只在 stackPop 里为空的时候才操作，否则会破坏出队入队的顺序
     * 2、在 peek 和 pop 操作之前调用该方法
     */
    private void shift() {
        if (stackPop.isEmpty()) {
            while (!stackPush.isEmpty()) {
                stackPop.push(stackPush.pop());
            }
        }
    }

    /**
     * Removes the element from in front of queue and returns that element.
     */
    public int pop() {
        shift();
        if (!stackPop.isEmpty()) {
            return stackPop.pop();
        }
        throw new RuntimeException("队列里没有元素");
    }

    /**
     * Get the front element.
     */
    public int peek() {
        shift();
        if (!stackPop.isEmpty()) {
            return stackPop.peek();
        }
        throw new RuntimeException("队列里没有元素");
    }

    /**
     * Returns whether the queue is empty.
     */
    public boolean empty() {
        return stackPush.isEmpty() && stackPop.isEmpty();
    }
}

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = new MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * boolean param_4 = obj.empty();
 */

```



# 8 排序算法

> 快排：从排序数组中任选一个数据（一般默认最后一个）为分区点，遍历数据，比分区点小的放左边，比分区点大的放右边；再利用分治思想，直到区间缩小到1，就说明数据有序。

```java
package quickSort;
 
public class QuickSort {
	private static int count;
	/**
	 * 测试
	 * @param args
	 */
	public static void main(String[] args) {
		int[] num = {3,45,78,64,52,11,64,55,99,11,18};
		System.out.println(arrayToString(num,"未排序"));
		QuickSort(num,0,num.length-1);
		System.out.println(arrayToString(num,"排序"));
		System.out.println("数组个数："+num.length);
		System.out.println("循环次数："+count);
		
	}
	/**
	 * 快速排序
	 * @param num	排序的数组
	 * @param left	数组的前针
	 * @param right 数组后针
	 */
	private static void QuickSort(int[] num, int left, int right) {
		//如果left等于right，即数组只有一个元素，直接返回
		if(left>=right) {
			return;
		}
		//设置最左边的元素为基准值
		int key=num[left];
		//数组中比key小的放在左边，比key大的放在右边，key值下标为i
		int i=left;
		int j=right;
		while(i<j){
			//j向左移，直到遇到比key小的值
			while(num[j]>=key && i<j){
				j--;
			}
			//i向右移，直到遇到比key大的值
			while(num[i]<=key && i<j){
				i++;
			}
			//i和j指向的元素交换
			if(i<j){
				int temp=num[i];
				num[i]=num[j];
				num[j]=temp;
			}
		}
		num[left]=num[i];
		num[i]=key;
		count++;
		QuickSort(num,left,i-1);
		QuickSort(num,i+1,right);
	}
	/**
	 * 将一个int类型数组转化为字符串
	 * @param arr
	 * @param flag
	 * @return
	 */
	private static String arrayToString(int[] arr,String flag) {
		String str = "数组为("+flag+")：";
		for(int a : arr) {
			str += a + "\t";
		}
		return str;
	}
}
```

> 归并排序：若要排序一个数组，先把数组从中间分为前后两部分，然后对两部分分别排序，再合并在一起。

```java
public class MergeSort {
    public static void main(String[] args) {
        int a[] = { 51, 46, 20, 18, 65, 97, 82, 30, 77, 50 };
        mergeSort(a, 0, a.length - 1);
        System.out.println("排序结果：" + Arrays.toString(a));
    }

    private static void mergeSort(int[] a, int low, int high) {
        int mid = (low + high) / 2;
        if (low < high) {
            // 左边
            mergeSort(a, low, mid);
            // 右边
            mergeSort(a, mid + 1, high);
            // 左右归并
            merge(a, low, mid, high);
            System.out.println(Arrays.toString(a));
        }
    }

    private static void merge(int[] a, int low, int mid, int high) {
        int[] temp = new int[high - low + 1];
        int i = low;// 左指针
        int j = mid + 1;// 右指针
        int k = 0;
        // 把较小的数先移到新数组中
        while (i <= mid && j <= high) {
            if (a[i] < a[j]) {
                temp[k++] = a[i++];
            } else {
                temp[k++] = a[j++];
            }
        }
        // 把左边剩余的数移入数组
        while (i <= mid) {
            temp[k++] = a[i++];
        }
        // 把右边边剩余的数移入数组
        while (j <= high) {
            temp[k++] = a[j++];
        }
        // 把新数组中的数覆盖nums数组
        for (int k2 = 0; k2 < temp.length; k2++) {
            a[k2 + low] = temp[k2];
        }
    }
}
```

