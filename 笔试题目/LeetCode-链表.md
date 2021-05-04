[TOC]

```java
public class ListNode {
    int val;
    ListNode next;
    ListNode() {}
    ListNode(int val) { this.val = val; }
    ListNode(int val, ListNode next) { this.val = val; this.next = next; }
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

## 删除链表倒数第N个节点

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

## 合并K个升序链表

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

## 奇偶链表（0328）

> *给定一个单链表，把所有的奇数节点和偶数节点分别排在一起。请注意，这里的奇数节点和偶数节点指的是节点编号的奇偶性，而不是节点的值的奇偶性。*
>
> *请尝试使用原地算法完成。你的算法的空间复杂度应为 O(1)，时间复杂度应为 O(nodes)，nodes 为节点总数。*

```java
class Solution {
    public ListNode oddEvenList(ListNode head) {
        if (head == null) {return head;}
        ListNode evenHead = head.next;
        ListNode odd = head, even = evenHead;

        // 本质上依旧是用两个链表进行拼接
        while (even != null && even.next != null) {
            odd.next = even.next;
            odd = odd.next;
            even.next = odd.next;
            even = even.next;
        }

        odd.next = evenHead;
        return head;
    }
}
```

## 两数相加（0002 && 0445）

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

> *输入：(7 -> 2 -> 4 -> 3) + (5 -> 6 -> 4)*
>
> *输出：7 -> 8 -> 0 -> 7*

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        Stack<Integer> stack1 = new Stack();
        Stack<Integer> stack2 = new Stack();
        while (l1 != null) {
            stack1.push(l1.val);
            l1 = l1.next;
        }
        while (l2 != null) {
            stack2.push(l2.val);
            l2 = l2.next;
        }

        int carry = 0;
        ListNode ans = null;
        while (!stack1.isEmpty() || !stack2.isEmpty() || carry != 0) {
            int a = stack1.isEmpty() ? 0 : stack1.pop();
            int b = stack2.isEmpty() ? 0 : stack2.pop();
            int cur = a + b + carry;
            ListNode curNode = new ListNode(cur % 10);
            carry = cur / 10;
            curNode.next = ans;
            ans = curNode;
        }

        return ans;
    }
}
```
