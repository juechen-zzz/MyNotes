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

> 前序+中序（栈）

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        if (root == null) {return ans;}

        Stack<TreeNode> stack = new Stack<>();
        TreeNode node = root;
        while (!stack.isEmpty() || node != null) {
            while (node != null) {
                ans.add(node.val);
                stack.push(node);
                node = node.left;
            }
            node = stack.pop();
            node = node.right;
        }
        return ans;
    }
}
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

## 最小高度树（0310）

> *树是一个无向图，其中任何两个顶点只通过一条路径连接。 换句话说，一个任何没有简单环路的连通图都是一棵树。*
>
> *给你一棵包含 n 个节点的树，标记为 0 到 n - 1 。给定数字 n 和一个有 n - 1 条无向边的 edges 列表（每一个边都是一对标签），其中 edges[i] = [ai, bi] 表示树中节点 ai 和 bi 之间存在一条无向边。*
>
> *可选择树中任何一个节点作为根。当选择节点 x 作为根节点时，设结果树的高度为 h 。在所有可能的树中，具有最小高度的树（即，min(h)）被称为 最小高度树 。*
>
> *请你找到所有的 最小高度树 并按 任意顺序 返回它们的根节点标签列表。*

```java
class Solution {
    public List<Integer> findMinHeightTrees(int n, int[][] edges) {
        List<Integer> ans = new ArrayList<>();

        // 构建邻接表和各个节点的度
        int[] degree = new int[n];
        List<List<Integer>> adjency = new ArrayList<>();
        for (int i = 0; i < n; i++) {adjency.add(new ArrayList<>());}
        for (int[] edge : edges) {
            degree[edge[0]]++;
            degree[edge[1]]++;
            adjency.get(edge[0]).add(edge[1]);
            adjency.get(edge[1]).add(edge[0]);
        }

        // 一度节点(叶子节点)入队
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            if (degree[i] == 1) {
                queue.offer(i);
            }
        }

        // 单独处理只有一个节点的情况
        int residue = n;
        if (residue == 1) {ans.add(0);}

        // 剩余的节点不是一个或两个,将叶子节点(度为1)一层一层剥离,最后暴露出来的就是根
        // 最后的根节点只可能是一个或者两个
        while (residue != 1 && residue != 2) {
            int size = queue.size();
            residue -= size;
            for (int i = 0; i < size; i++) {
                int cur = queue.poll();
                for (int node : adjency.get(cur)) {
                    if (degree[node] > 0) {
                        degree[node]--;
                    }
                    if (degree[node] == 1) {
                        queue.offer(node);
                    }
                }
            }
        }

        // 队列中最后剩下的就是根
        while (!queue.isEmpty()) {
            ans.add(queue.poll());
        }
        return ans;
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

## 删除二叉搜索树的节点（0450）

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

