[TOC]

# 1 哈希表

## 两数之和（0001 && 0167）

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

> *给定一个已按照 升序排列 的整数数组 numbers ，请你从数组中找出两个数满足相加之和等于目标数 target 。*

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0, right = numbers.length - 1;
        while (left < right) {
            int sum = numbers[left] + numbers[right];
            if (sum == target) {
                return new int[]{left + 1, right + 1};
            }
            else if (sum < target) {
                left++;
            }
            else {
                right--;
            }
        }
        return new int[]{-1, -1};
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



# 2 链表

> */***
>
>  ** Definition for singly-linked list.*
>
>  ** public class ListNode {*
>
>  **     int val;*
>
>  **     ListNode next;*
>
>  **     ListNode() {}*
>
>  **     ListNode(int val) { this.val = val; }*
>
>  **     ListNode(int val, ListNode next) { this.val = val; this.next = next; }*
>
>  ** }*
>
>  **/*

## 两数相加（0002）

> *给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。*
>
> *将这两个数相加起来，则会返回一个新的链表来表示它们的和。*
>
> *输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)*
>
> *输出：7 -> 0 -> 8*
>
> *原因：342 + 465 = 807*

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        int carry = 0;
        ListNode head = new ListNode(0);
        ListNode curr = head;

        while (l1 != null || l2 != null) {
            if (l1 != null) {
                carry += l1.val;
                l1 = l1.next;
            }
            if (l2 != null) {
                carry += l2.val;
                l2 = l2.next;
            }
            curr.next = new ListNode(carry % 10);
            carry = carry / 10;
            curr = curr.next;
        }

        if (carry == 1) {
            curr.next = new ListNode(1);
            curr = curr.next;
        }
        return head.next;
    }
}
```

## 删除链表倒数第N个节点（0019）

> *给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。*
>
> *给定一个链表: 1->2->3->4->5, 和 n = 2.*
>
> *当删除了倒数第二个节点后，链表变为 1->2->3->5.*
>
> *给定的 n 保证是有效的。*

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode tmp = new ListNode(-1, head);
        ListNode first = tmp;
        ListNode second = tmp;
        for (int i = 0; i < n; i++) {
            first = first.next;
        }

        while (first.next != null) {
            first = first.next;
            second = second.next;
        }

        second.next = second.next.next;
        return tmp.next;
    }
}
```

## 合并K个升序链表（0021 && 0023）

> *给你一个链表数组，每个链表都已经按升序排列。*
>
> *请你将所有链表合并到一个升序链表中，返回合并后的链表。*
>
> *输入：lists = [[1,4,5],[1,3,4],[2,6]]*
>
> *输出：[1,1,2,3,4,4,5,6]*

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        ListNode ans = null;
        for (int i = 0; i < lists.length; i++) {
            ans = mergeTwoLists(ans, lists[i]);
        }
        return ans;
    }

    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode tmp = new ListNode(-1);
        ListNode cur = tmp;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                cur.next = l1;
                l1 = l1.next;
            } else {
                cur.next = l2;
                l2 = l2.next;
            }
            cur = cur.next;
        }
        if (l1 != null) {cur.next = l1;}
        else if (l2 != null) {cur.next = l2;}
        else {cur.next = null;}
        return tmp.next;
    }
}
```

## 两两交换链表中的节点（0024）

> *给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。*
>
> *你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。*
>
> *输入：head = [1,2,3,4]*
>
> *输出：[2,1,4,3]*

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if (head == null) {return null;}
        ListNode cur = new ListNode(-1, head);
        ListNode p = cur;

        while (p.next != null && p.next.next != null) {
            ListNode n2 = p.next;
            ListNode n1 = n2.next;
            ListNode n3 = n1.next;

            p.next = n1;
            n1.next = n2;
            n2.next = n3;

            p = n2;
        }

        return cur.next;
    }
}
```

## K个一组翻转链表（0025）

> *给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。*
>
> *k 是一个正整数，它的值小于或等于链表的长度。*
>
> *如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。*
>
> *给你这个链表：1->2->3->4->5*
>
> *当 k = 2 时，应当返回: 2->1->4->3->5*
>
> *当 k = 3 时，应当返回: 3->2->1->4->5*

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode ans = new ListNode(-1, head);

        ListNode pre = ans;
        ListNode end = ans;

        while (end != null) {
            for (int i = 0; i < k && end != null; i++) {end = end.next;}
            if (end == null) {break;}
            ListNode start = pre.next;
            ListNode nxt = end.next;

            end.next = null;
            pre.next = myReverse(start);
            start.next = nxt;

            pre = start;
            end = start;
        }

        return ans.next;
    }

    public ListNode myReverse(ListNode head) {
        ListNode pre = null;
        ListNode next = head;
        while (head != null) {
            next = head.next;
            head.next = pre;

            pre = head;
            head = next;
        }
        return pre;
    }
}
```

## 旋转链表（0061）

> *给定一个链表，旋转链表，将链表每个节点向右移动 k 个位置，其中 k 是非负数。*

```java
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if (head == null || head.next == null || k == 0) {return head;}
        ListNode first = head;
        int num = 1;
        while (first.next != null) {
            num++;
            first = first.next;
        }

        ListNode second = new ListNode(-1, head);
        if (k % num == 0) {return head;}
        int n = num - k % num;
        while (n > 0) {
            second = second.next;
            n--;
        }
        ListNode ans = second.next;
        second.next = null;
        first.next = head;
        return ans;
    }
}
```

## 删除链表中的元素

> *给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。*

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode tmp = head;
        while (tmp != null && tmp.next != null) {
            if (tmp.val == tmp.next.val) {
                tmp.next = tmp.next.next;
            }
            else {
                tmp = tmp.next;
            }
        }
        return head;
    }
}
```

> *给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 没有重复出现 的数字。*

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) {return head;}
        ListNode cur = new ListNode(-1);
        cur.next = head;

        ListNode a = cur;
        ListNode b = head.next;
        while (b != null) {
            if (a.next.val != b.val) {
                a = a.next;
                b = b.next;
            }
            else {
                while (b != null && a.next.val == b.val) {b = b.next;}
                a.next = b;
                b = (b == null) ? null : b.next;
            }
        }
        return cur.next;
    }
}
```

> *删除链表中等于给定值 val 的所有节点*

```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        ListNode tmp = new ListNode(-1, head);
        ListNode cur = tmp;
        while (head != null) {
            if (head.val == val) {
                cur.next = head.next;
            }
            else {
                cur = cur.next;
            }
            head = head.next;
        }
        return tmp.next;
    }
}
```



## 分隔链表（0086）

> *给你一个链表和一个特定值 x ，请你对链表进行分隔，使得所有小于 x 的节点都出现在大于或等于 x 的节点之前。*
>
> *你应当保留两个分区中每个节点的初始相对位置。*

```java
class Solution {
    public ListNode partition(ListNode head, int x) {
        if (head == null) {return null;}
        ListNode cur1 = new ListNode(-1);
        ListNode cur2 = new ListNode(-1);
        ListNode p1 = cur1, p2 = cur2;
        while (head != null) {
            if (head.val < x){
                cur1.next = head;
                cur1 = cur1.next;
            } else {
                cur2.next = head;
                cur2 = cur2.next;
            }
            head = head.next;
        }
        // 注意考虑最后的结尾部分要指向null
        cur1.next = p2.next;
        cur2.next = null;
        return p1.next;
    }
}
```

## 反转链表（0092 && 0206）

> *反转一个单链表*

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null) {return null;}
        ListNode pre = null;
        ListNode next = null;

        while (head != null) {
            next = head.next;
            head.next = pre;

            pre = head;
            head = next;
        }
        return pre;
    }
}
```

> *反转从位置 m 到 n 的链表。请使用一趟扫描完成反转。*

```java
class Solution {
    public ListNode reverseBetween(ListNode head, int m, int n) {
        ListNode tmp = new ListNode(-1, head);

        ListNode pre = tmp;
        ListNode cur = head;

        int step = 0;
        while (step < m - 1) {
            pre = pre.next;
            cur = cur.next;
            step++;
        }

        for (int i = 0; i < n - m; i++) {
            ListNode a = cur.next;
            cur.next = cur.next.next;

            a.next = pre.next;
            pre.next = a;
        }
        return tmp.next;
    }
}
```

## 复制带随机指针的链表（0138）

> *给你一个长度为 n 的链表，每个节点包含一个额外增加的随机指针 random ，该指针可以指向链表中的任何节点或空节点。*
>
> *构造这个链表的 深拷贝。 深拷贝应该正好由 n 个 全新 节点组成，其中每个新节点的值都设为其对应的原节点的值。新节点的 next 指针和 random 指针也都应指向复制链表中的新节点，并使原链表和复制链表中的这些指针能够表示相同的链表状态。复制链表中的指针都不应指向原链表中的节点 。*
>
> *例如，如果原链表中有 X 和 Y 两个节点，其中 X.random --> Y 。那么在复制链表中对应的两个节点 x 和 y ，同样有 x.random --> y 。*
>
> *返回复制链表的头节点。*

```java
public class Solution {
    Map<Node, Node> visited = new HashMap<>();

    public Node copyRandomList(Node head) {
        if (head == null) {return null;}

        if (this.visited.containsKey(head)) {return visited.get(head);}

        Node node = new Node(head.val, null, null);
        this.visited.put(head, node);
        
        node.next = this.copyRandomList(head.next);
        node.random = this.copyRandomList(head.random);

        return node;
    }
}
```

## 环形链表（0141 && 0142）

> *链表成环检测*

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) {return false;}
        ListNode fast = head, slow = head;
        while (true) {
            if (fast == null || fast.next == null) {return false;}
            fast = fast.next.next;
            slow = slow.next;
            if (fast == slow) {return true;}
        }
    }
}
```

> *链表成环检测，返回环开始的位置，没有则返回null*

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode fast = head, slow = head;
        while (true) {
            if (fast == null || fast.next == null) {return null;}
            fast = fast.next.next;
            slow = slow.next;
            if (fast == slow) {break;}
        }

        fast = head;
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next;
        }
        return fast;
    }
}
```

## 重排链表（0143）

> *给定一个单链表 L：L0→L1→…→Ln-1→Ln ，*
>
> *将其重新排列后变为： L0→Ln→L1→Ln-1→L2→Ln-2→…*
>
> *你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。*

```java
class Solution {
    public void reorderList(ListNode head) {
        if (head == null) {return;}
        List<ListNode> list = new ArrayList<>();
        while (head != null) {
            list.add(head);
            head = head.next;
        }

        int i = 0, j = list.size() - 1;
        while (i < j) {
            list.get(i).next = list.get(j);
            i++;
            if (i == j) {break;}
            list.get(j).next = list.get(i);
            j--;
        }
        list.get(i).next = null;
    }
}
```

## 链表插入排序（0147）

> *插入排序算法：*
>
> *插入排序是迭代的，每次只移动一个元素，直到所有元素可以形成一个有序的输出列表。*
>
> *每次迭代中，插入排序只从输入数据中移除一个待排序的元素，找到它在序列中适当的位置，并将其插入。*
>
> *重复直到所有输入数据插入完为止。*

```java
class Solution {
    public ListNode insertionSortList(ListNode head) {
        if (head == null || head.next == null) {return head;}
        ListNode tmp = new ListNode(-1, head);
        ListNode last = head, cur = head.next;

        while (cur != null) {
            if (last.val <= cur.val) {
                last = last.next;
            }
            else {
                ListNode prev = tmp;
                while (prev.next.val <= cur.val) {
                    prev = prev.next;
                }
                last.next = cur.next;
                cur.next = prev.next;
                prev.next = cur;
            }
            cur = last.next;
        }

        return tmp.next;
    }
}
```

## 相交链表（0160）

> *编写一个程序，找到两个单链表相交的起始节点。*

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) {return null;}
        ListNode curA = headA;
        ListNode curB = headB;
        while (curA != curB) {
            curA = (curA != null ? curA.next : headB);
            curB = (curB != null ? curB.next : headA);
        }
        return curA;
    }
}
```



# 3 字符串

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
>   *s = "barfoothefoobarman",*
>
>   *words = ["foo","bar"]*
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



# 4 数学问题

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

## 接雨水（0042）

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

## 最大子序和（0053）

> *给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。*

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int n = nums.length;
        if (n == 0) {return 0;}
        int[] dp = new int[n];
        int res = nums[0];

        dp[0] = nums[0];
        for (int i = 1; i < n; i++){
            dp[i] = Math.max(nums[i], nums[i] + dp[i - 1]);
            res = Math.max(res, dp[i]);
        }
        return res;
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

## 不同路径（0062 && 0063）

> *一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。*
>
> *机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。*
>
> *现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？*
>
> *网格中的障碍物和空位置分别用 1 和 0 来表示。*

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if (obstacleGrid[0][0] == 1) {return 0;}
        int m = obstacleGrid.length, n = obstacleGrid[0].length;
        int[][] dp = new int[m][n];
        dp[0][0] = 1;
        for (int i = 1; i < n; i++) {
            if (dp[0][i - 1] == 1 && obstacleGrid[0][i] != 1) {
                dp[0][i] = 1;
            }
        }
        for (int i = 1; i < m; i++) {
            if (dp[i - 1][0] == 1 && obstacleGrid[i][0] != 1) {
                dp[i][0] = 1;
            }
        }

        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (obstacleGrid[i][j] != 1) {
                    dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
                }
            }
        }
        return dp[m - 1][n - 1];
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



# 5 位运算

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



# 6 回溯

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

## 组合总数（0039 && 0040 && 0216）

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
>   *["1","1","1","1","0"],*
>
>   *["1","1","0","1","0"],*
>
>   *["1","1","0","0","0"],*
>
>   *["0","0","0","0","0"]*
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



# 7 栈

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



# 8 二分

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



# 9 树

## 遍历（0094）

> 递归方式（三种）

```java
class Solution {
    List<Integer> ans = new ArrayList<>();

    public List<Integer> inorderTraversal(TreeNode root) {
        helper(root);
        return ans;
    }

    public void helper(TreeNode root) {
        if (root == null) {return;}
        helper(root.left);
        ans.add(root.val);
        helper(root.right);
    }
}
```

> 中序（栈）

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        while (true) {
            while (root != null) {
                stack.push(root);
                root = root.left;
            }
            if (stack.isEmpty()) {return ans;}
            TreeNode node = stack.pop();
            ans.add(node.val);
            root = node.right;
        }
    }
}
```

> 层序遍历

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> ans = new ArrayList<>();
        if (root == null) {return ans;}
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            int n = queue.size();
            List<Integer> tmp = new ArrayList<>();
            while (n > 0) {
                TreeNode node = queue.poll();
                tmp.add(node.val);
                if (node.left != null) {queue.offer(node.left);}
                if (node.right != null) {queue.offer(node.right);}
                n--;
            }
            ans.add(new ArrayList<>(tmp));
        }
        return ans;
    }
}
```

## 不同的二叉搜索树（0095 && 0096）

> *给定一个整数 n，求以 1 ... n 为节点组成的二叉搜索树有多少种？*

```java
class Solution {
    public int numTrees(int n) {
        int[] dp = new int[n + 1];
        dp[0] = 1;
        dp[1] = 1;

        for (int i = 2; i <= n; i++) {
            for (int j = 1; j <= i; j++) {
                dp[i] += dp[j - 1] * dp[i - j];
            }
        }
        return dp[n];
    }
}
```

> *给定一个整数 n，生成所有由 1 ... n 为节点所组成的 二叉搜索树 。*

```java
class Solution {
    public List<TreeNode> generateTrees(int n) {
        if (n == 0) {return new LinkedList<TreeNode>();}
        return generateTrees(1, n);
    }

    public List<TreeNode> generateTrees(int start, int end) {
        List<TreeNode> allTrees = new LinkedList<TreeNode>();
        if (start > end) {
            allTrees.add(null);
            return allTrees;
        }

        // 枚举可行根节点
        for (int i = start; i <= end; i++) {
            // 获得所有可行的左子树集合
            List<TreeNode> leftTrees = generateTrees(start, i - 1);

            // 获得所有可行的右子树集合
            List<TreeNode> rightTrees = generateTrees(i + 1, end);

            // 从左子树集合中选出一棵左子树，从右子树集合中选出一棵右子树，拼接到根节点上
            for (TreeNode left : leftTrees) {
                for (TreeNode right : rightTrees) {
                    TreeNode currTree = new TreeNode(i);
                    currTree.left = left;
                    currTree.right = right;
                    allTrees.add(currTree);
                }
            }
        }
        return allTrees;
    }
}
```

## 验证二叉搜索树（0098）

> *给定一个二叉树，判断其是否是一个有效的二叉搜索树。*

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        List<Integer> ans = inOrder(root);

        for (int i = 0; i < ans.size() - 1; i++) {
            if (ans.get(i) >= ans.get(i + 1)) {
                return false;
            }
        }
        return true;
    }

    public List<Integer> inOrder(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        while (true) {
            while (root != null) {
                stack.push(root);
                root = root.left;
            }
            if (stack.isEmpty()) {return ans;}
            TreeNode node = stack.pop();
            ans.add(node.val);
            root = node.right;
        }
    }
}
```

## 恢复二叉搜索树（0099）

> *给你二叉搜索树的根节点 root ，该树中的两个节点被错误地交换。请在不改变其结构的情况下，恢复这棵树。*
>
> *进阶：使用 O(n) 空间复杂度的解法很容易实现。你能想出一个只使用常数空间的解决方案吗？*

```java
class Solution {
    public void recoverTree(TreeNode root) {
        List<Integer> nums = new ArrayList<Integer>();
        inorder(root, nums);
        int[] swapped = findTwoSwapped(nums);
        recoverTree(root, 2, swapped[0], swapped[1]);
    }

    public void inorder(TreeNode root, List<Integer> nums){
        if (root == null) {return;}
        inorder(root.left, nums);
        nums.add(root.val);
        inorder(root.right, nums);
    }

    public int[] findTwoSwapped(List<Integer> nums){
        int x = -1, y = -1;
        for (int i = 0; i < nums.size() - 1; i++){
            if (nums.get(i) > nums.get(i + 1)){
                y = nums.get(i + 1);
                if (x == -1) {x = nums.get(i);}
                else {break;}
            }
        }
        return new int[]{x, y};
    }

    public void recoverTree(TreeNode root, int count, int x, int y){
        if (root != null){
            if (root.val == x || root.val == y){
                root.val = (root.val == x) ? y : x;
                if (--count == 0){return;}
            }
            recoverTree(root.left, count, x, y);
            recoverTree(root.right, count, x, y);
        }
    }
}
```

## 相同的树（0100）

> *给你两棵二叉树的根节点 p 和 q ，编写一个函数来检验这两棵树是否相同。*
>
> *如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。*

```java
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if ((p == null && q != null) || (p != null && q == null)) {return false;}
        if (p == null && q == null) {return true;}
        return p.val == q.val && isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
    }
}
```

## 对称二叉树（0101）

> *给定一个二叉树，检查它是否是镜像对称的。*

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        queue.offer(root);
        while (!queue.isEmpty()) {
            TreeNode left = queue.poll();
            TreeNode right = queue.poll();
            if (left == null && right == null) {continue;}
            if (left == null || right == null || left.val != right.val) {return false;}
            queue.offer(left.left);
            queue.offer(right.right);
            queue.offer(left.right);
            queue.offer(right.left);
        }
        return true;
    }
}
```

## 二叉树深度（0104 && 0111）

> *给定一个二叉树，找出其最大深度。*

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) {return 0;}
        return 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
    }
}
```

> *给定一个二叉树，找出其最小深度。*

```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) {return 0;}
        int depth = 1;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            int n  = queue.size();
            while (n > 0) {
                TreeNode node = queue.poll();
                if (node.left == null && node.right == null) {return depth;}
                if (node.left != null) {queue.offer(node.left);}
                if (node.right != null) {queue.offer(node.right);}
                n--;
            }
            depth++;
        }
        return depth;
    }
}
```

## 构造二叉树（0105 && 0106 && 0108）

> 从前序和中序遍历构造

```java
class Solution {
    Map<Integer, Integer> map = new HashMap<>();

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return helper(preorder, 0, preorder.length, inorder, 0, inorder.length);
    }

    public TreeNode helper(int[] preorder, int preStart, int preEnd, int[] inorder, int inStart, int inEnd) {
        if (preStart == preEnd) {return null;}

        int rootVal = preorder[preStart];
        TreeNode root = new TreeNode(rootVal);
        int inRootIndex = map.get(rootVal);
        int leftNum = inRootIndex - inStart;

        root.left = helper(preorder, preStart + 1, preStart + 1 + leftNum, inorder, inStart, inRootIndex);
        root.right = helper(preorder, preStart + 1 + leftNum, preEnd, inorder, inRootIndex + 1, inEnd);

        return root;
    }
}
```

> 从中序和后序遍历构造

```java
class Solution {
    Map<Integer, Integer> map = new HashMap<>();

    public TreeNode buildTree(int[] inorder, int[] postorder) {
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return helper(inorder, 0, inorder.length, postorder, 0, postorder.length);
    }

    public TreeNode helper(int[] inorder, int inStart, int inEnd, int[] postorder, int postStart, int postEnd) {
        if (postStart == postEnd) {return null;}

        int rootVal = postorder[postEnd - 1];
        TreeNode root = new TreeNode(rootVal);

        int inRootIndex = map.get(rootVal);
        int leftNum = inRootIndex - inStart;

        root.left = helper(inorder, inStart, inRootIndex, postorder, postStart, postStart + leftNum);
        root.right = helper(inorder, inRootIndex + 1, inEnd, postorder, postStart + leftNum, postEnd - 1);

        return root;
    }
}
```

> 有序数组构造

```java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        return helper(nums, 0, nums.length - 1);
    }

    public TreeNode helper(int[] nums, int left, int right) {
        if (left > right) {return null;}

        int mid = left + (right - left) / 2;

        TreeNode root = new TreeNode(nums[mid]);
        root.left = helper(nums, left, mid - 1);
        root.right = helper(nums, mid + 1, right);
        return root;
    }
}
```

## 平衡二叉树（0110）

> *给定一个二叉树，判断它是否是高度平衡的二叉树。*

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        if (root == null) {return true;}
        return Math.abs(depth(root.left) - depth(root.right)) <= 1 && isBalanced(root.left) && isBalanced(root.right);
    }

    public int depth(TreeNode root) {
        if (root == null) {return 0;}
        return Math.max(depth(root.left), depth(root.right)) + 1;
    }
}
```

## 路径总和（0112 && 0113）

> *给你二叉树的根节点 root 和一个表示目标和的整数 targetSum*
>
> *判断该树中是否存在 根节点到叶子节点 的路径*
>
> *这条路径上所有节点值相加等于目标和 targetSum 。*

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if (root == null) {return false;}
        if (root.left == null && root.right == null) {return targetSum == root.val;}
        return hasPathSum(root.left, targetSum - root.val) || hasPathSum(root.right, targetSum - root.val);
    }
}
```

> *给定一个二叉树和一个目标和，找到所有从根节点到叶子节点路径总和等于给定目标和的路径。*

```java
class Solution {
    List<List<Integer>> ans = new ArrayList<>();
    List<Integer> path = new ArrayList<>();

    public List<List<Integer>> pathSum(TreeNode root, int targetSum) {
        if (root == null) {return ans;}
        dfs(root, targetSum);
        return ans;
    }

    public void dfs(TreeNode root, int targetSum) {
        if (root == null) {return;}
        
        targetSum -= root.val;
        path.add(root.val);

        if (root.left == null && root.right == null && targetSum == 0) {ans.add(new ArrayList<>(path));}

        dfs(root.left, targetSum);
        dfs(root.right, targetSum);

        targetSum += root.val;
        path.remove(path.size() - 1);
    }
}
```

## 二叉树最大路径和（0124）

> *给定一个非空二叉树，返回其最大路径和。*

```java
class Solution {
    int ans = Integer.MIN_VALUE;

    public int maxPathSum(TreeNode root) {
        helper(root);
        return ans;
    }

    public int helper(TreeNode root){
        if (root == null) {return 0;}
        int left = Math.max(0, helper(root.left));
        int right = Math.max(0, helper(root.right));
        ans = Math.max(ans, left + right + root.val);
        return Math.max(left, right) + root.val;
    }
}
```

## 二叉树的所有路径（0257）

> *给定一个二叉树，返回所有从根节点到叶子节点的路径。*

```java
class Solution {
    List<String> ans = new ArrayList<>();

    public List<String> binaryTreePaths(TreeNode root) {
        if (root == null) {return ans;}
        dfs(root, "");
        return ans;
    }

    public void dfs(TreeNode root, String path) {
        if (root == null) {return;}
        
        StringBuilder sb = new StringBuilder(path);
        sb.append(Integer.toString(root.val));
        
        if (root.left == null && root.right == null) {
            ans.add(sb.toString());
        }
        else {
            sb.append("->");
            dfs(root.left, sb.toString());
            dfs(root.right, sb.toString());
        }
    }
}
```

## 二叉树展开为链表（0114）

> *给你二叉树的根结点 root ，请你将它展开为一个单链表：*
>
> *展开后的单链表应该同样使用 TreeNode ，其中 right 子指针指向链表中下一个结点，而左子指针始终为 null 。*
>
> *展开后的单链表应该与二叉树 先序遍历 顺序相同。*

```java
class Solution {
    List<TreeNode> ans = new ArrayList<>();

    public void flatten(TreeNode root) {
        helper(root);
        for (int i = 1; i < ans.size(); i++) {
            TreeNode prev = ans.get(i - 1), curr = ans.get(i);
            prev.left = null;
            prev.right = curr;
        }
    }

    public void helper(TreeNode root) {
        if (root != null) {
            ans.add(root);
            helper(root.left);
            helper(root.right);
        }
    }
}
```

## 求根到叶子节点数字之和（0129）

> *给定一个二叉树，它的每个结点都存放一个 0-9 的数字，每条从根到叶子节点的路径都代表一个数字。*
>
> *计算从根到叶子节点生成的所有数字之和。*

```java
class Solution {
    public int sumNumbers(TreeNode root) {
        return dfs(root, 0);
    }

    public int dfs(TreeNode root, int preSum) {
        if (root == null) {return 0;}

        int sum = preSum * 10 + root.val;

        if (root.left == null && root.right == null) {
            return sum;
        }
        else {
            return dfs(root.left, sum) + dfs(root.right, sum);
        }
    }
}
```

## 完全二叉树的节点个数（0222）

> *给你一棵 完全二叉树 的根节点 root ，求出该树的节点个数。*

```java
class Solution {
    public int countNodes(TreeNode root) {
        if(root == null) {return 0;}
    	int left = countNodes(root.left);
    	int right = countNodes(root.right);
    	return left + right + 1;
    }
}
```

## 翻转二叉树（0226）

> *翻转一棵二叉树。*

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {return null;}

        TreeNode left = invertTree(root.left);
        TreeNode right = invertTree(root.right);

        root.left = right;
        root.right = left;

        return root;
    }
}
```

## 二叉搜索树的最近公共祖先（0235）

> *给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。*

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        List<TreeNode> pathForP = getPath(root, p);
        List<TreeNode> pathForQ = getPath(root, q);
        TreeNode ans = null;
        for (int i = 0; i < pathForP.size() && i < pathForQ.size(); i++) {
            if (pathForP.get(i) == pathForQ.get(i)) {
                ans = pathForP.get(i);
            }
            else {
                break;
            }
        }
        return ans;
    }

    public List<TreeNode> getPath(TreeNode root, TreeNode target) {
        List<TreeNode> path = new ArrayList<>();
        TreeNode node = root;
        while (node != target) {
            path.add(node);
            if (target.val < node.val) {node = node.left;}
            else {node = node.right;}
        }
        path.add(node);
        return path;
    }
}
```

## 二叉树的最近公共祖先（0236）

> *给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。*

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root == null || root == p || root == q) {return root;}
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if(left == null) {return right;}
        if(right == null) {return left;}
        return root;
    }
}
```



# 10 图

## 被围绕的区域（0130）

> *给定一个二维的矩阵，包含 'X' 和 'O'（字母 O）。*
>
> *找到所有被 'X' 围绕的区域，并将这些区域里所有的 'O' 用 'X' 填充。*

```java
class Solution {
    int[][] d = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};

    public void solve(char[][] board) {
        if (board.length == 0 || board[0].length == 0) {return;}

        int m = board.length;
        int n = board[0].length;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if ((i == 0 || i == m - 1 || j == 0 || j == n - 1) && board[i][j] == 'O') {
                    dfs(i, j, board);
                }
            }
        }

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] != '.') {board[i][j] = 'X';}
                else {board[i][j] = 'O';}
            }
        }
    }

    public void dfs(int x, int y, char[][] board) {
        board[x][y] = '.';
        for (int i = 0; i < 4; i++) {
            int nextX = x + d[i][0];
            int nextY = y + d[i][1];
            if ((nextX >= 0 && nextX < board.length && nextY >= 0 && nextY < board[0].length) && board[nextX][nextY] == 'O') {
                dfs(nextX, nextY, board);
            }
        }
    }
}
```

## 克隆图（0133）

> *给你无向 连通 图中一个节点的引用，请你返回该图的 深拷贝（克隆）。*

```java
// DFS
class Solution {
    Map<Node, Node> visited = new HashMap<>();

    public Node cloneGraph(Node node) {
        if (node == null) {return node;}

        // 如果该节点已经被访问过了，则直接从哈希表中取出对应的克隆节点返回
        if (visited.containsKey(node)) {return visited.get(node);}

        // 克隆节点，注意到为了深拷贝我们不会克隆它的邻居的列表
        Node cloneNode = new Node(node.val, new ArrayList<>());
        // 哈希表存储
        visited.put(node, cloneNode);

        // 遍历该节点的邻居并更新克隆节点的邻居列表
        for (Node neighbor: node.neighbors) {
            cloneNode.neighbors.add(cloneGraph(neighbor));
        }
        return cloneNode;
    }
}

// BFS
class Solution {
    public Node cloneGraph(Node node) {
        if (node == null) {return node;}

        Map<Node, Node> visited = new HashMap<>();
        Queue<Node> queue = new LinkedList<>();
        queue.offer(node);
        visited.put(node, new Node(node.val, new ArrayList<Node>()));

        while (!queue.isEmpty()) {
            Node n = queue.poll();
            // 遍历该节点的邻居
            for (Node neighbor: n.neighbors) {
                if (!visited.containsKey(neighbor)) {
                    // 如果没有被访问过，就克隆并存储在哈希表中
                    visited.put(neighbor, new Node(neighbor.val, new ArrayList<Node>()));
                    // 将邻居节点加入队列中
                    queue.offer(neighbor);
                }
                // 更新当前节点的邻居列表
                visited.get(n).neighbors.add(visited.get(neighbor));
            }
        }

        return visited.get(node);
    }
}
```
## 课程表（0207 && 0210）

> *你这个学期必须选修 numCourses 门课程，记为 0 到 numCourses - 1 。*
>
> *在选修某些课程之前需要一些先修课程。 先修课程按数组 prerequisites 给出，*
>
> *其中 prerequisites[i] = [ai, bi] ，表示如果要学习课程 ai 则 必须 先学习课程 bi 。*
>
> 
>
> *例如，先修课程对 [0, 1] 表示：想要学习课程 0 ，你需要先完成课程 1 。*
>
> *请你判断是否可能完成所有课程的学习？如果可以，返回 true ；否则，返回 false 。*

```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        int[] inDegrees = new int[numCourses];
        List<List<Integer>> adjacency = new ArrayList<>();
        for (int i = 0; i < numCourses; i++) {adjacency.add(new ArrayList<>());}

        // 1 初始化入度矩阵和邻接矩阵
        for (int[] tmp : prerequisites) {
            inDegrees[tmp[0]]++;
            adjacency.get(tmp[1]).add(tmp[0]);
        }

        // 2 将入度为0的节点放入队列
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < numCourses; i++) {
            if (inDegrees[i] == 0) {
                queue.offer(i);
            }
        }

        // 3 没从队列中取出一个元素，总数就减一，同时更新邻接矩阵
        while (!queue.isEmpty()) {
            int pre = queue.poll();
            numCourses--;
            for (int cur : adjacency.get(pre)) {
                if (--inDegrees[cur] == 0) {queue.offer(cur);}
            }
        }

        return numCourses == 0;
    }
}
```

> *给定课程总量以及它们的先决条件，返回你为了学完所有课程所安排的学习顺序。*

```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        int[] inDegrees = new int[numCourses];
        List<List<Integer>> adjacency = new ArrayList<>();
        for (int i = 0; i < numCourses; i++) {adjacency.add(new ArrayList<>());}

        for (int[] tmp : prerequisites) {
            inDegrees[tmp[0]]++;
            adjacency.get(tmp[1]).add(tmp[0]);
        }

        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < numCourses; i++) {
            if (inDegrees[i] == 0) {
                queue.offer(i);
            }
        }

        int[] ans = new int[numCourses];
        int index = 0;
        while (!queue.isEmpty()) {
            int pre = queue.poll();
            numCourses--;
            ans[index++] = pre;
            for (int cur : adjacency.get(pre)) {
                if (--inDegrees[cur] == 0) {queue.offer(cur);}
            }
        }

        return numCourses == 0 ? ans : new int[0];
    }
}
```



# 11 数据结构

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