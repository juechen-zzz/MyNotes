[TOC]

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
```

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

> 栈

```java
	/**
     * 前序遍历非递归方式实现
     * 非递归实现思路：二叉树遍历的递归实现很简单，也很容易理解，在进行非递归实现时，需要用到栈这种数据结构（为什么是栈，不是别的数据结构）。
     * 因为递归实现的过程就是程序自己在处理圧栈和弹栈，改用非递归实现时，用栈模拟系统的圧栈与弹栈，就可以了。
     */
    public List<Integer> preorder(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        TreeNode cur = root;
        while (cur != null || !stack.isEmpty()) {
            //对于前序遍历，需要一直往二叉树的左子树上走，直道左子树走完。在走左子树的过程中需要输出遇到节点的值
            while (cur != null) {
                ans.add(cur.val);
                stack.push(cur);
                cur = cur.left;
            }
            //左边的节点都走完了，需要改变节点方向
            if (!stack.isEmpty()) {
                cur = stack.pop();
                cur = cur.right;
            }
        }
        return ans;
    }

	/**
     * 中序遍历的非递归实现,与上述前序遍历类似，只有稍许不同，注意
     */
    public List<Integer> inorder(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        TreeNode cur = root;
        while (cur != null || !stack.isEmpty()) {
            //一直向左，但是先不打印经历的节点的值
            while (cur != null) {
                stack.push(cur);
                cur = cur.left;
            }
            //到达最左边，打印并改变方向
            if (!stack.isEmpty()) {
                cur = stack.pop();
                ans.add(cur.val);
                cur = cur.right;
            }
 
        }
        return ans;
 
    }

/**
     * 后序遍历的非递归实现
     * 技巧：妙用前序遍历的非递归可以实现后序遍历的非递归实现，这里需要注意几点改变：后序时，先遍历右，再遍历左，最后将得到的结果反向就好了
     */
    public List<Integer> postorder(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        TreeNode cur = root;
        while (cur != null || !stack.isEmpty()) {
            while (cur != null) {
                ans.add(cur.val);
                stack.push(cur);
                cur = cur.right;
            }
            if (!stack.isEmpty()) {
                cur = stack.pop();
                cur = cur.left;
            }
        }
        Collections.reverse(ans);
        return ans;
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

## 构造二叉树

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

## 二叉树的直径（0543）

> *给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过也可能不穿过根结点。*

```java
class Solution {
    int ans;

    public int diameterOfBinaryTree(TreeNode root) {
        ans = 1;
        helper(root);
        return ans - 1;
    }

    private int helper(TreeNode node) {
        if (node == null) {return 0;}

        int left = helper(node.left);
        int right = helper(node.right);
        
        ans = Math.max(ans, left + right + 1);
        return Math.max(left, right) + 1;
    }
}
```

## 二叉树的坡度（0563）

> *给定一个二叉树，计算 整个树 的坡度 。*
>
> *一个树的 节点的坡度 定义即为，该节点左子树的节点之和和右子树节点之和的 差的绝对值 。如果没有左子树的话，左子树的节点之和为 0 ；没有右子树的话也是一样。空结点的坡度是 0 。*
>
> *整个树 的坡度就是其所有节点的坡度之和。*

```java
class Solution {
    int ans = 0;

    public int findTilt(TreeNode root) {
        traverse(root);
        return ans;
    }

    public int traverse(TreeNode root) {
        if (root == null) {return 0;}
        int left = traverse(root.left);
        int right = traverse(root.right);
        ans += Math.abs(left - right);
        return left + right + root.val;
    }
}}
```

## 二叉树的最大宽度（0662）

> *给定一个二叉树，编写一个函数来获取这个树的最大宽度。树的宽度是所有层中的最大宽度。这个二叉树与满二叉树（full binary tree）结构相同，但一些节点为空。*
>
> *每一层的宽度被定义为两个端点（该层最左和最右的非空节点，两端点间的null节点也计入长度）之间的长度。*

```java
class Solution {
    public int widthOfBinaryTree(TreeNode root) {
        if (root == null) {return 0;}
        Queue<TreeNode> queue = new LinkedList<>();
        LinkedList<Integer> list = new LinkedList<>();
        queue.offer(root);
        list.add(1);

        int ans = 1;
        while (!queue.isEmpty()) {
            int count = queue.size();
            for (int i = 0; i < count; i++) {
                TreeNode cur = queue.poll();
                int curIndex = list.removeFirst();
                if (cur.left != null) {
                    queue.offer(cur.left);
                    list.add(curIndex * 2);
                }
                if (cur.right != null) {
                    queue.offer(cur.right);
                    list.add(curIndex * 2 + 1);
                }
            }

            if (list.size() >= 2) {
                ans = Math.max(ans, list.getLast() - list.getFirst() + 1);
            }
        }

        return ans;
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

## 二叉树中第二小的节点（0671）

> *给定一个非空特殊的二叉树，每个节点都是正数，并且每个节点的子节点数量只能为 2 或 0。如果一个节点有两个子节点的话，那么该节点的值等于两个子节点中较小的一个。*
>
> *更正式地说，root.val = min(root.left.val, root.right.val) 总成立。*
>
> *给出这样的一个二叉树，你需要输出所有节点中的第二小的值。如果第二小的值不存在的话，输出 -1 。*

```java
class Solution {
    public int findSecondMinimumValue(TreeNode root) {
        if (root == null || (root.left == null && root.right == null)) {return -1;}     //没有最小节点

        //找出候选数，默认就是子节点值，如果子节点值和root值相同，递归，在子树中寻找候选数
        int left = root.left.val;
        int right = root.right.val;

        if (root.left.val == root.val) {left = findSecondMinimumValue(root.left);}
        if (root.right.val == root.val) {right = findSecondMinimumValue(root.right);}

        //如果左右候选数都正常，返回较小值就可
        if (left != -1 && right != -1) {return Math.min(left, right);}

        //如果候选数有-1，说明整个子树中没有可供候选的数
        if (left != -1){
            //左子树正常，答案就是左边的候选数
            return left;
        } 
        else {
            //右子树正常，返回答案
            //或者右子树也没有候选数，返回-1，即right
            return right;
        }
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

## 二叉搜索树查找、插入和删除

> 二叉查找树的搜索

```java
class Solution {
    public TreeNode searchBST(TreeNode root, int val) {
        if (root == null || root.val == val) {return root;}

        return root.val > val ? searchBST(root.left, val) : searchBST(root.right, val);
    }
}
```

> 二叉查找树的插入

```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) {return new TreeNode(val);}

        TreeNode pos = root;
        while (pos != null) {
            if (val < pos.val) {
                if (pos.left == null) {
                    pos.left = new TreeNode(val);
                    break;
                }
                else {
                    pos = pos.left;
                }
            }
            else {
                if (pos.right == null) {
                    pos.right = new TreeNode(val);
                    break;
                }
                else {
                    pos = pos.right;
                }
            }
        }

        return root;
    }
}
```

> 删除二叉搜索树的节点
>
> *给定一个二叉搜索树的根节点 root 和一个值 key，删除二叉搜索树中的 key 对应的节点，并保证二叉搜索树的性质不变。返回二叉搜索树（有可能被更新）的根节点的引用。*

```java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if (root == null) {return null;}

        if (key > root.val) {root.right = deleteNode(root.right, key);}
        else if (key < root.val) {root.left = deleteNode(root.left, key);}
        else {
            if (root.left == null && root.right == null) {root = null;}
            else if (root.right != null) {
                root.val = successor(root);
                root.right = deleteNode(root.right, root.val);
            }
            else {
                root.val = predecessor(root);
                root.left = deleteNode(root.left, root.val);
            }
        }

        return root;
    }

    // 代表的是中序遍历序列的下一个节点
    public int successor(TreeNode root) {
        root = root.right;
        while (root.left != null) {root = root.left;}
        return root.val;
    }

    // 代表的是中序遍历序列的上一个节点
    public int predecessor(TreeNode root) {
        root = root.left;
        while (root.right != null) {root = root.right;}
        return root.val;
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

## 验证二叉树的前序序列化（0331）

> *序列化二叉树的一种方法是使用前序遍历。当我们遇到一个非空节点时，我们可以记录下这个节点的值。如果它是一个空节点，我们可以使用一个标记值记录，例如 #。*

```java
class Solution {
    public boolean isValidSerialization(String preorder) {
        int n = preorder.length();
        int i = 0;
        Stack<Integer> stack = new Stack<>();
        stack.push(1);

        while (i < n) {
            if (stack.isEmpty()) {return false;}
            if (preorder.charAt(i) == ',') {i++;}
            else if (preorder.charAt(i) == '#') {
                int top = stack.pop() - 1;
                if (top > 0) {stack.push(top);}
                i++;
            }
            else {
                while (i < n && preorder.charAt(i) != ',') {i++;}
                int top = stack.pop() - 1;
                if (top > 0) {stack.push(top);}
                stack.push(2);
            }
        }

        return stack.isEmpty();
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

## 修建二叉搜索树（0669）

> *给你二叉搜索树的根节点 root ，同时给定最小边界low 和最大边界 high。通过修剪二叉搜索树，使得所有节点的值在[low, high]中。*
>
> *修剪树不应该改变保留在树中的元素的相对结构（即，如果没有被移除，原有的父代子代关系都应当保留）。 可以证明，存在唯一的答案。*
>
> *所以结果应当返回修剪好的二叉搜索树的新的根节点。注意，根节点可能会根据给定的边界发生改变。*

```java
class Solution {
    public TreeNode trimBST(TreeNode root, int low, int high) {
        if (root == null) {return root;}
        if (root.val > high) {return trimBST(root.left, low, high);}
        if (root.val < low) {return trimBST(root.right, low, high);}

        root.left = trimBST(root.left, low, high);
        root.right = trimBST(root.right, low, high);

        return root;
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

## 二叉搜索树的范围和（0938）

> *给定二叉搜索树的根结点 root，返回值位于范围 [low, high] 之间的所有结点的值的和。*

```java
class Solution {
    public int rangeSumBST(TreeNode root, int low, int high) {
        if (root == null) {return 0;}

        if (root.val > high) {return rangeSumBST(root.left, low, high);}
        if (root.val < low) {return rangeSumBST(root.right, low, high);}

        return root.val + rangeSumBST(root.left, low, high) + rangeSumBST(root.right, low, high);
    }
}
```

## 把二叉搜索树转换成累加树（0538）

> 给出二叉 搜索 树的根节点，该树的节点值各不相同，请你将其转换为累加树（Greater Sum Tree），使每个节点 node 的新值等于原树中大于或等于 node.val 的值之和。
>
> 提醒一下，二叉搜索树满足下列约束条件：
>
> 节点的左子树仅包含键 小于 节点键的节点。
> 节点的右子树仅包含键 大于 节点键的节点。
> 左右子树也必须是二叉搜索树。

```java
class Solution {
    int sum = 0;

    public TreeNode convertBST(TreeNode root) {
        if (root != null) {
            convertBST(root.right);
            sum += root.val;
            root.val = sum;
            convertBST(root.left);
        }
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

## 最大二叉树（0654）

> 给定一个不含重复元素的整数数组 nums 。一个以此数组直接递归构建的 最大二叉树 定义如下：
>
> 二叉树的根是数组 nums 中的最大元素。
> 左子树是通过数组中 最大值左边部分 递归构造出的最大二叉树。
> 右子树是通过数组中 最大值右边部分 递归构造出的最大二叉树。
> 返回有给定数组 nums 构建的 最大二叉树 。

```java
class Solution {
    public TreeNode constructMaximumBinaryTree(int[] nums) {
        return myMethod(nums, 0, nums.length);
    }

    private static TreeNode myMethod(int[] nums, int left, int right) {
        if (left == right) {return null;}

        int maxIndex = max(nums, left, right);
        TreeNode root = new TreeNode(nums[maxIndex]);
        root.left = myMethod(nums, left, maxIndex);
        root.right = myMethod(nums, maxIndex + 1, right);
        
        return root;
    }

    private static int max(int[] nums, int left, int right) {
        int maxIndex = left;
        for (int i = left; i < right; i++) {
            if (nums[maxIndex] < nums[i]) {
                maxIndex = i;
            }
        }
        return maxIndex;
    }
}
```

## 合并二叉树（0617）

> *给定两个二叉树，想象当你将它们中的一个覆盖到另一个上时，两个二叉树的一些节点便会重叠。*
>
> *你需要将他们合并为一个新的二叉树。合并的规则是如果两个节点重叠，那么将他们的值相加作为节点合并后的新值，否则不为 NULL 的节点将直接作为新二叉树的节点。*

```java
class Solution {
    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
        if (root1 == null) {return root2;}
        if (root2 == null) {return root1;}

        TreeNode ans = new TreeNode(root1.val + root2.val);
        ans.left = mergeTrees(root1.left, root2.left);
        ans.right = mergeTrees(root1.right, root2.right);
        return ans;
    }
}
```

## 在二叉树中增加一行（0623）

> *给定一个二叉树，根节点为第1层，深度为 1。在其第 d 层追加一行值为 v 的节点。*
>
> *添加规则：给定一个深度值 d （正整数），针对深度为 d-1 层的每一非空节点 N，为 N 创建两个值为 v 的左子树和右子树。*
>
> *将 N 原先的左子树，连接为新节点 v 的左子树；将 N 原先的右子树，连接为新节点 v 的右子树。*
>
> *如果 d 的值为 1，深度 d - 1 不存在，则创建一个新的根节点 v，原先的整棵树将作为 v 的左子树。*

```java
class Solution {
    public TreeNode addOneRow(TreeNode root, int val, int depth) {
        return myMethod(root, val, depth, true);
    }

    private static TreeNode myMethod(TreeNode root, int val, int depth, boolean left) {
        if (depth == 1 && left) {
            TreeNode ans = new TreeNode(val);
            ans.left = root;
            return ans;
        }

        if (depth == 1 && !left) {
            TreeNode tmp = new TreeNode(val);
            tmp.right = root;
            return tmp;
        }

        if (root != null) {
            root.left = myMethod(root.left, val, depth - 1, true);
            root.right = myMethod(root.right, val, depth - 1, false);
        }

        return root;
    }
}
```

## 寻找重复的子树（0652）

> *给定一棵二叉树，返回所有重复的子树。对于同一类的重复子树，你只需要返回其中任意一棵的根结点即可。*
>
> *两棵树重复是指它们具有相同的结构以及相同的结点值。*

```java
class Solution {
    Map<String, Integer> count;
    List<TreeNode> ans;

    public List<TreeNode> findDuplicateSubtrees(TreeNode root) {
        count = new HashMap<>();
        ans = new ArrayList<>();
        myMethod(root);
        return ans;        
    }

    private String myMethod(TreeNode node) {
        if (node == null) {return "#";}
        String serial = node.val + "," + myMethod(node.left) + "," + myMethod(node.right);
        count.put(serial, count.getOrDefault(serial, 0) + 1);
        if (count.get(serial) == 2) {ans.add(node);}
        return serial;
    }
}
```

## 输出二叉树（0655）

> *给你一个按升序排序的整数数组 num（可能包含重复数字），请你将它们分割成一个或多个长度至少为 3 的子序列，其中每个子序列都由连续整数组成。*
>
> *如果可以完成上述分割，则返回 true ；否则，返回 false 。*

```java
class Solution {
    public List<List<String>> printTree(TreeNode root) {
        int h = getHeight(root);
        int w = (1 << h) - 1;

        List<List<String>> ans = new ArrayList<>();
        for (int i = 0; i < h; i++) {
            List<String> cur = new ArrayList<>();
            for (int j = 0; j < w; j++) {
                cur.add("");
            }
            ans.add(cur);
        }

        fill(root, ans, 0, 0, w - 1);
        return ans;
    }

    private static int getHeight(TreeNode root) {
        if (root == null) {return 0;}
        return 1 + Math.max(getHeight(root.left), getHeight(root.right));
    }

    private static void fill(TreeNode node, List<List<String>> ans, int h, int left, int right) {
        if (node == null) {return;}
        int mid = left + (right - left) / 2;
        String s = ans.get(h).get(mid);
        ans.get(h).set(mid, Integer.toString(node.val));

        fill(node.left, ans, h + 1, left, mid - 1);
        fill(node.right, ans, h + 1, mid + 1, right);
    }
}
```

## N叉树

> 最大深度

```java
class Solution {
    public int maxDepth(Node root) {
        Queue<Pair<Node, Integer>> stack = new LinkedList<>();
        if (root != null) {stack.add(new Pair(root, 1));}

        int depth = 0;
        while (!stack.isEmpty()) {
            Pair<Node, Integer> cur = stack.poll();
            root = cur.getKey();
            int curDepth = cur.getValue();
            if (root != null) {
                depth = Math.max(depth, curDepth);
                for (Node c : root.children) {
                    stack.add(new Pair(c, curDepth + 1));
                }
            }
        }

        return depth;
    }
}
```

> 前序

```java
class Solution {
    public List<Integer> preorder(Node root) {
        Stack<Node> stack = new Stack<>();
        List<Integer> ans = new ArrayList<>();
        if (root == null) {return ans;}

        stack.add(root);
        while (!stack.isEmpty()) {
            Node node = stack.pop();
            ans.add(node.val);
            Collections.reverse(node.children);
            for (Node adj : node.children) {
                stack.add(adj);
            }
        }
        return ans;
    }
}
```

> 后序

```java
class Solution {
    public List<Integer> postorder(Node root) {
        Stack<Node> stack = new Stack<>();
        LinkedList<Integer> ans = new LinkedList<>();
        if (root == null) {return ans;}

        stack.add(root);
        while (!stack.isEmpty()) {
            Node node = stack.pop();
            ans.addFirst(node.val);
            for (Node adj : node.children) {
                if (adj != null) {
                    stack.add(adj);    
                } 
            }
        }
        return ans;
    }
}
```

## 两数之和（0653）

> *给定一个二叉搜索树和一个目标结果，如果 BST 中存在两个元素且它们的和等于给定的目标结果，则返回 true。*

```java
class Solution {
    public boolean findTarget(TreeNode root, int k) {
        Set<Integer> set = new HashSet<>();
        return myMethod(root, k, set);
    }

    private static boolean myMethod(TreeNode root, int k, Set<Integer> set) {
        if (root == null) {return false;}
        if (set.contains(k - root.val)) {return true;}
        set.add(root.val);
        return myMethod(root.left, k, set) || myMethod(root.right, k, set);
    }
}
```

## 路径总和（0112 && 0113 && 0437）

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

> *给定一个二叉树，它的每个结点都存放着一个整数值。*
>
> *找出路径和等于给定数值的路径总数。*
>
> *路径不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。*

```java
class Solution {
    public int pathSum(TreeNode root, int sum) {
        Map<Integer, Integer> prefixSumCount = new HashMap<>();
        prefixSumCount.put(0, 1);
        return myMethod(root, prefixSumCount, sum, 0);
    }

    private int myMethod(TreeNode root, Map<Integer, Integer> prefixSumCount, int target, int curSum) {
        if (root == null) {return 0;}

        int ans = 0;
        curSum += root.val;

        //---核心代码
        // 看看root到当前节点这条路上是否存在节点前缀和加target为currSum的路径
        // 当前节点->root节点反推，有且仅有一条路径，如果此前有和为currSum-target,而当前的和又为currSum,两者的差就肯定为target了
        // currSum-target相当于找路径的起点，起点的sum+target=currSum，当前点到起点的距离就是target
        ans += prefixSumCount.getOrDefault(curSum - target, 0);
        prefixSumCount.put(curSum, prefixSumCount.getOrDefault(curSum, 0) + 1);

        ans += myMethod(root.left, prefixSumCount, target, curSum);
        ans += myMethod(root.right, prefixSumCount, target, curSum);

        prefixSumCount.put(curSum, prefixSumCount.get(curSum) - 1);
        return ans;
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

## 另一个树的子树（0572）

> *给定两个非空二叉树 s 和 t，检验 s 中是否包含和 t 具有相同结构和节点值的子树。s 的一个子树包括 s 的一个节点和这个节点的所有子孙。s 也可以看做它自身的一棵子树。*

```java
class Solution {
    public boolean isSubtree(TreeNode s, TreeNode t) {
        if (t == null) {return true;}
        if (s == null) {return false;}
        return isSubtree(s.left, t) || isSubtree(s.right, t) || isSameTree(s, t);
    }

    private static boolean isSameTree(TreeNode s, TreeNode t) {
        if (s == null && t == null) {return true;}
        if (s == null || t == null) {return false;}
        if (s.val != t.val) {return false;}
        return isSameTree(s.left, t.left) && isSameTree(s.right, t.right);
    }
}
```

## 根据二叉树创建字符串（0606）

> *你需要采用前序遍历的方式，将一个二叉树转换成一个由括号和整数组成的字符串。*
>
> *空节点则用一对空括号 "()" 表示。而且你需要省略所有不影响字符串与原始二叉树之间的一对一映射关系的空括号对。*

```java
class Solution {
    public String tree2str(TreeNode t) {
        if (t == null) {return "";}
        if (t.left == null && t.right == null) {return t.val + "";}
        if (t.right == null) {return t.val + "(" + tree2str(t.left) + ")";}
        return t.val + "(" + tree2str(t.left) + ")(" + tree2str(t.right) + ")"; 
    }
}
```

## 左叶子之和（0404）

> 计算给定二叉树的所有左叶子之和。

```java
class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        if (root == null) {return 0;}
        return dfs(root);
    }

    private int dfs(TreeNode root) {
        int ans = 0;
        if (root.left != null) {
            ans += (isLeaf(root.left) ? root.left.val : dfs(root.left));
        }
        if (root.right != null && !isLeaf(root.right)) {
            ans += dfs(root.right);
        }
        return ans;
    }

    private boolean isLeaf(TreeNode root) {
        return (root.left == null && root.right == null);
    }
}
```

## 出现次数最多的子树元素和（0508）

> *给你一个二叉树的根结点，请你找出出现次数最多的子树元素和。一个结点的「子树元素和」定义为以该结点为根的二叉树上所有结点的元素之和（包括结点本身）。*
>
> *你需要返回出现次数最多的子树元素和。如果有多个元素出现的次数相同，返回所有出现次数最多的子树元素和（不限顺序）。*

```java
class Solution {
    Map<Integer, Integer> map = new HashMap<>();
    int maxCount = 0;

    public int[] findFrequentTreeSum(TreeNode root) {
        if (root == null) {return new int[0];}

        findSum(root);
        
        List<Integer> tmpList = new ArrayList<>();
        for (int i : map.keySet()) {
            if (map.get(i) == maxCount) {
                tmpList.add(i);
            }
        }

        int[] ans = new int[tmpList.size()];
        for (int i = 0; i < ans.length; i++) {
            ans[i] = tmpList.get(i);
        }

        return ans;
    }

    private int findSum(TreeNode root) {
        if (root == null) {return 0;}

        int leftSum = findSum(root.left);
        int rightSum = findSum(root.right);
        int sum = root.val + leftSum + rightSum;

        map.put(sum, map.getOrDefault(sum, 0) + 1);
        maxCount = Math.max(maxCount, map.get(sum));

        return sum;
    }
}
```

## 最长同值路径（0687）

> *给定一个二叉树，找到最长的路径，这个路径中的每个节点具有相同值。 这条路径可以经过也可以不经过根节点。*
>
> *注意：两个节点之间的路径长度由它们之间的边数表示。*

```java
class Solution {
    int ans = 0;

    public int longestUnivaluePath(TreeNode root) {
        myMethod(root);
        return ans;
    }

    public int myMethod(TreeNode node) {
        if (node == null) {return 0;}
        int left = myMethod(node.left);
        int right = myMethod(node.right);

        int arrowLeft = 0, arrowRight = 0;
        if (node.left != null && node.left.val == node.val) {arrowLeft += left + 1;}
        if (node.right != null && node.right.val == node.val) {arrowRight += right + 1;}
        
        ans = Math.max(ans, arrowLeft + arrowRight);
        return Math.max(arrowLeft, arrowRight);
    }
}
```

