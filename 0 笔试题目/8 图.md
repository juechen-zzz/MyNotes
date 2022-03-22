[TOC]

## 成环判定

```java
private static boolean check(int[][] graph) {
    int n = graph.length;
    int[] inDegree = new int[n];

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (graph[j][i] == 1) {
                inDegree[i]++;
            }
        }
    }

    Queue<Integer> queue = new LinkedList<>();
    for (int i = 0; i < n; i++) {
        if (inDegree[i] == 0) {
            queue.offer(i);
        }
    }

    int num = 0;
    while (!queue.isEmpty()) {
        int cur = queue.poll();
        num++;
        for (int i = 0; i < n; i++) {
            if (graph[cur][i] == 1) {
                inDegree[i]--;
                if (inDegree[i] == 0) {queue.offer(i);}
            }
        }
    }

    return num != n;
}
```

## 找到最终的安全状态

> 802
>
> *在有向图中，从某个节点和每个转向处开始出发，沿着图的有向边走。如果到达的节点是终点（即它没有连出的有向边），则停止。*
>
> *如果从起始节点出发，最后必然能走到终点，就认为起始节点是 最终安全 的。更具体地说，对于最终安全的起始节点而言，存在一个自然数 k ，无论选择沿哪条有向边行走 ，走了不到 k 步后必能停止在一个终点上。*
>
> *返回一个由图中所有最终安全的起始节点组成的数组作为答案。答案数组中的元素应当按 升序 排列。*

```java
class Solution {
    public List<Integer> eventualSafeNodes(int[][] graph) {
        int n = graph.length;
        int[] color = new int[n];
        List<Integer> ans = new ArrayList<>();

        for (int i = 0; i < n; i++) {
            if (dfs(graph, color, i)) {
                ans.add(i);
            }
        }

        return ans;
    }

    // 用DFS判断图中的每个节点是否能走到环中。对于每个节点，我们有三种染色的方法：
    	// 0 白色表示该节点还没有被访问过；
    	// 1 灰色表示该节点在栈中（这一轮搜索中被访问过）或者在环中；
    	// 2 黑色表示该节点的所有相连的节点都被访问过，且该节点不在环中。
	// 当我们第一次访问一个节点时，我们把它从白色变成灰色，并继续搜索与它相连的节点。如果在搜索过程中我们遇到一个灰色的节点，那么说明找到了一个环，此时退出搜索，所有的灰色节点保持不变（即从任意一个灰色节点开始，都能走到环中），如果搜索过程中，我们没有遇到灰色的节点，那么在回溯到当前节点时，我们把它从灰色变成黑色，即表示它是一个安全的节点。
    private static boolean dfs(int[][] graph, int[] color, int idx) {
        if (color[idx] > 0) {return color[idx] == 2;}

        color[idx] = 1;
        for (int neigh : graph[idx]) {
            if (color[neigh] == 2) {continue;}
            if (color[neigh] == 1 || !dfs(graph, color, neigh)) {return false;}
        }
        color[idx] = 2;
        return true;
    }
}
```

## 被围绕的区域

> 130
>
> *给定一个二维的矩阵，包含 'X' 和 'O'（字母 O）。*
>
> *找到所有被 'X' 围绕的区域，并将这些区域里所有的 'O' 用 'X' 填充。*

```java
class Solution {
    private static int[][] d = new int[][]{{-1, 0}, {0, 1}, {1, 0}, {0, -1}};

    public void solve(char[][] board) {
        if (board.length == 0 || board[0].length == 0) {return;}

        int n = board.length, m = board[0].length;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if ((i == 0 || i == n - 1 || j == 0 || j == m - 1) && board[i][j] == 'O') {
                    dfs(board, i, j);
                }
            }
        }

        // 被围绕的区间不会存在于边界上，换句话说，任何边界上的 'O' 都不会被填充为 'X'。
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (board[i][j] != '.') {board[i][j] = 'X';}
                else {board[i][j] = 'O';}
            }
        }
    }

    private static void dfs(char[][] board, int x, int y) {
        board[x][y] = '.';
        for (int i = 0; i < 4; i++) {
            int nextX = x + d[i][0];
            int nextY = y + d[i][1];
            if ((nextX >= 0 && nextX < board.length && nextY >= 0 && nextY < board[0].length) && board[nextX][nextY] == 'O') {
                dfs(board, nextX, nextY);
            }
        }
    }
}
```

## 克隆图

> 133
>
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
        for (Node neigh : node.neighbors) {
            cloneNode.neighbors.add(cloneGraph(neigh));
        }

        return cloneNode;
    }
}

// BFS
class Solution {
    public Node cloneGraph(Node node) {
        if (node == null) {return node;}

        Queue<Node> queue = new LinkedList<>();
        queue.offer(node);
        Map<Node, Node> visited = new HashMap<>();
        visited.put(node, new Node(node.val, new ArrayList<Node>()));

        while (!queue.isEmpty()) {
            // 遍历该节点的邻居
            Node cur = queue.poll();
            for (Node neigh : cur.neighbors) {
                if (!visited.containsKey(neigh)) {
                    // 如果没有被访问过，就克隆并存储在哈希表中
                    visited.put(neigh, new Node(neigh.val, new ArrayList<Node>()));
                    // 将邻居节点加入队列中
                    queue.offer(neigh);
                }
                // 更新当前节点的邻居列表
                visited.get(cur).neighbors.add(visited.get(neigh));
            }
        }

        return visited.get(node);
    }
}
```

## 课程表

> 207
>
> *你这个学期必须选修 numCourses 门课程，记为 0 到 numCourses - 1 。**在选修某些课程之前需要先修课程，先修课程按数组 prerequisites 给出*
>
> *例如，先修课程对 [0, 1] 表示：想要学习课程 0 ，你需要先完成课程 1 。*
>
> *请你判断是否可能完成所有课程的学习？*

```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        int[] inDegree = new int[numCourses];
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < numCourses; i++) {graph.add(new ArrayList<>());}

        // 1 初始化入度矩阵和邻接矩阵
        for (int[] tmp : prerequisites) {
            inDegree[tmp[0]]++;
            graph.get(tmp[1]).add(tmp[0]);
        }

        // 2 将入度为0的节点放入队列
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < numCourses; i++) {
            if (inDegree[i] == 0) {
                queue.offer(i);
            }
        }

        // 3 没从队列中取出一个元素，总数就减一，同时更新邻接矩阵
        while (!queue.isEmpty()) {
            int cur = queue.poll();
            numCourses--;
            for (int neigh : graph.get(cur)) {
                if (--inDegree[neigh] == 0) {queue.offer(neigh);}
            }
        }

        return numCourses == 0;
    }
}
```

> 210
>
> *给定课程总量以及它们的先决条件，返回你为了学完所有课程所安排的学习顺序。*

```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        int[] inDegree = new int[numCourses];
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < numCourses; i++) {graph.add(new ArrayList<>());}

        for (int[] tmp : prerequisites) {
            inDegree[tmp[0]]++;
            graph.get(tmp[1]).add(tmp[0]);
        }

        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < numCourses; i++) {
            if (inDegree[i] == 0) {
                queue.offer(i);
            }
        }

        int[] ans = new int[numCourses];
        int idx = 0;
        while (!queue.isEmpty()) {
            int cur = queue.poll();
            numCourses--;
            ans[idx++] = cur;
            for (int neigh : graph.get(cur)) {
                if (--inDegree[neigh] == 0) {
                    queue.offer(neigh);
                }
            }
        }

        return numCourses == 0 ? ans : new int[0];
    }
}
```

> 630
>
> *这里有 n 门不同的在线课程，他们按从 1 到 n 编号。每一门课程有一定的持续上课时间（课程时间）t 以及关闭时间第 d 天。一门课要持续学习 t 天直到第 d 天时要完成，你将会从第 1 天开始。*
>
> *给出 n 个在线课程用 (t, d) 对表示。你的任务是找出最多可以修几门课。*

```java
class Solution {
    public int scheduleCourse(int[][] courses) {
        Arrays.sort(courses, (a, b) -> a[1] - b[1]);
        PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
        int time = 0;

        for (int[] cur : courses) {
            if (time + cur[0] <= cur[1]) {
                pq.offer(cur[0]);
                time += cur[0];
            }
            else if (!pq.isEmpty() && pq.peek() > cur[0]) {
                time += cur[0] - pq.poll();
                pq.offer(cur[0]);
            }
        }

        return pq.size();
    }
}
```

> 1462
>
> 有的课会有直接的先修课程，比如如果想上课程 0 ，你必须先上课程 1 ，那么会以 [1,0] 数对的形式给出先修课程数对。
>
> 给你课程总数 n 和一个直接先修课程数对列表 prerequisite 和一个查询对列表 queries 。
>
> 对于每个查询对 queries[i] ，请判断 queries[i][0] 是否是 queries[i][1] 的先修课程。
>
> 请返回一个布尔值列表，列表中每个元素依次分别对应 queries 每个查询对的判断结果。

```java
class Solution {
    public List<Boolean> checkIfPrerequisite(int numCourses, int[][] prerequisites, int[][] queries) {
        List<Integer>[] graph = new List[numCourses];
        for (int i = 0; i < numCourses; i++) {graph[i] = new ArrayList<>();}

        // 建立邻接表、计算入度
        int[] inDegree = new int[numCourses];
        for (int[] tmp : prerequisites) {
            inDegree[tmp[1]]++;
            graph[tmp[0]].add(tmp[1]);
        }

        Set<Integer>[] visited = new HashSet[numCourses];
        for (int i = 0; i < numCourses; i++) {
            if (inDegree[i] == 0) {     // 只计算入度为0的节点
                dfs(i, graph, visited);
            }
        }

        List<Boolean> ans = new ArrayList<>();
        for (int[] cur : queries) {
            ans.add(visited[cur[0]].contains(cur[1]));
        }

        return ans;
    }

    private static void dfs(int node, List<Integer>[] graph, Set<Integer>[] visited) {
        if (visited[node] != null) {return;}

        visited[node] = new HashSet<>();
        visited[node].add(node);

        // 将子节点的后修课程加入父节点的后修课程
        for (int neigh : graph[node]) {
            dfs(neigh, graph, visited);
            for (int cur : visited[neigh]) {
                visited[node].add(cur);
            }
        }
    }
}
```

> 华为真题：返回课程表指定课程的所有课程号，按序号排序，但是会存在环，存在返回-1

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Scanner;

/**
 * @description: 课程表的改进，输出所有的前置课程名单，按大小序号排序，可能存在环
 * @author: Komorebi
 * @time: 2021/5/12 20:33
 */
public class Huawei0512_2 {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int N = sc.nextInt();
        sc.nextLine();

        int M = sc.nextInt();
        sc.nextLine();

        List<List<Integer>> nums = new ArrayList<>();
        for (int i = 0; i < N; i++) {
            List<Integer> cur = new ArrayList<>();
            String[] tmp = sc.nextLine().split(",");
            if (tmp.length == 1) {
                nums.add(new ArrayList<>());
            }
            else {
                for (int j = 1; j < tmp.length; j++) {
                    int t = Integer.parseInt(tmp[j]);
                    cur.add(t);
                }
                nums.add(new ArrayList<>(cur));
            }
        }

        List<Integer> ans = new ArrayList<>();
        int[] visited = new int[N];
        int[] curVisited = new int[N];
        
        if (!dfs(M, nums, ans, visited, curVisited)) {System.out.println(-1);}
        else {
            ans.remove(ans.size() - 1);
            if (ans.size() == 0) {System.out.println("null");}
            else {
                Collections.sort(ans);
                String ret = "";
                for (int i = 0; i < ans.size() - 1; i++) {ret += (ans.get(i) + ",");}
                ret += ans.get(ans.size() - 1);
                System.out.println(ret);
            }
        }
    }

    private static boolean dfs(int M, List<List<Integer>> nums, List<Integer> ans, int[] visited, int[] curVisited) {
        if (curVisited[M] == 1) {return false;}
        if (visited[M] == 1) {return true;}

        curVisited[M] = 1;

        for (int n : nums.get(M)) {
            if (!dfs(n, nums, ans, visited, curVisited)) {return false;}
        }

        ans.add(M);
        curVisited[M] = 0;
        visited[M] = 1;
        return true;
    }
}
```

## 单词搜索2

> 212
> 给定一个 m x n 二维字符网格 board 和一个单词（字符串）列表 words，找出所有同时在二维网格和字典中出现的单词。
>
> 单词必须按照字母顺序，通过 相邻的单元格 内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母在一个单词中不允许被重复使用。

```java
// DFS
class Solution {
    public List<String> findWords(char[][] board, String[] words) {
        List<String> ans = new ArrayList<>();
        int n = board.length, m = board[0].length;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                dfs(board, words, ans, "", i, j);
            }
        }
        return ans;
    }

    private static void dfs(char[][] board, String[] words, List<String> ans, String s, int x, int y) {
        int n = board.length, m = board[0].length;
        if (x < 0 || x >= n || y < 0 || y >= m) {return;}

        if (containsWord(words, s)) {ans.add(s);}

        dfs(board, words, ans, s + board[x][y], x + 1, y);
        dfs(board, words, ans, s + board[x][y], x - 1, y);
        dfs(board, words, ans, s + board[x][y], x, y + 1);
        dfs(board, words, ans, s + board[x][y], x, y - 1);
    }

    private static boolean containsWord(String[] words, String s) {
        for (String cur : words) {
            if (cur.equals(s)) {
                return true;
            }
        }
        return false;
    }
}
```

## 最小高度树

> 310
>
> 树是一个无向图，其中任何两个顶点只通过一条路径连接。 换句话说，一个任何没有简单环路的连通图都是一棵树。
>
> 给你一棵包含 n 个节点的树，标记为 0 到 n - 1 。给定数字 n 和一个有 n - 1 条无向边的 edges 列表（每一个边都是一对标签），其中 edges[i] = [ai, bi] 表示树中节点 ai 和 bi 之间存在一条无向边。
>
> 可选择树中任何一个节点作为根。当选择节点 x 作为根节点时，设结果树的高度为 h 。在所有可能的树中，具有最小高度的树（即，min(h)）被称为 最小高度树 。
>
> 请你找到所有的 最小高度树 并按 任意顺序 返回它们的根节点标签列表。

```java
class Solution {
    public List<Integer> findMinHeightTrees(int n, int[][] edges) {
        List<Integer> ans = new ArrayList<>();
        if (n == 1) {
            ans.add(0);
            return ans;
        }

        int[] degree = new int[n];
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) {graph.add(new ArrayList<>());}
        for (int[] cur : edges) {
            degree[cur[0]]++;
            degree[cur[1]]++;
            graph.get(cur[0]).add(cur[1]);
            graph.get(cur[1]).add(cur[0]);
        }

        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            if (degree[i] == 1) {
                queue.offer(i);
            }
        }

        while (!queue.isEmpty()) {
            ans = new ArrayList<>();
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                int cur = queue.poll();
                /*
                把当前节点加入结果集，因为我们每次循环都会新建一个list，所以最后保存的就是最后一个状态下的叶子节点，可以想象一下图，每层遍历完，都会把该层（也就是叶子节点层）这一层从队列中移除掉，不就相当于把原来的图给剪掉一圈叶子节点，形成一个缩小的新的图
                */
                ans.add(cur);
                for (int neigh : graph.get(cur)) {
                    degree[neigh]--;
                    if (degree[neigh] == 1) {queue.offer(neigh);}
                }
            }
        }

        return ans;
    }
}
```

## 重新安排行程

> 332
>
> *给定一个机票的字符串二维数组 [from, to]，子数组中的两个成员分别表示飞机出发和降落的机场地点，对该行程进行重新规划排序。所有这些机票都属于一个从 JFK（肯尼迪国际机场）出发的先生，所以该行程必须从 JFK 开始。*

```java
class Solution {
    public List<String> findItinerary(List<List<String>> tickets) {
        Map<String, PriorityQueue<String>> map = new HashMap<>();
        List<String> ans = new ArrayList<>();

        for (List<String> cur : tickets) {
            String src = cur.get(0), dst = cur.get(1);
            if (!map.containsKey(src)) {
                map.put(src, new PriorityQueue<String>());
            }
            map.get(src).offer(dst);
        }

        dfs(map, ans, "JFK");
        Collections.reverse(ans);
        return ans;
    }

    private static void dfs(Map<String, PriorityQueue<String>> map, List<String> ans, String cur) {
        while (map.containsKey(cur) && map.get(cur).size() > 0) {
            String neigh = map.get(cur).poll();
            dfs(map, ans, neigh);
        }
        ans.add(cur);
    }
}
```

## K站中转内最便宜的航班

> 787
>
> *有 n 个城市通过 m 个航班连接。每个航班都从城市 u 开始，以价格 w 抵达 v。*
>
> *现在给定所有的城市和航班，以及出发城市 src 和目的地 dst，你的任务是找到从 src 到 dst 最多经过 k 站中转的最便宜的价格。 如果没有这样的路线，则输出 -1。*

```java
// BFS
import javafx.util.Pair;
class Solution {
    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        List<List<Pair<Integer, Integer>>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) {graph.add(new ArrayList<Pair<Integer, Integer>>());}
        for (int[] cur : flights) {graph.get(cur[0]).add(new Pair(cur[1], cur[2]));}

        int ans = Integer.MAX_VALUE;
        Queue<Pair<Integer, Pair<Integer, Integer>>> queue = new LinkedList<>();
        queue.offer(new Pair(src, new Pair(0, 0)));

        while (!queue.isEmpty() && queue.peek().getValue().getKey() <= k + 1) {
            Pair<Integer, Pair<Integer, Integer>> tmp = queue.peek();
            if (tmp.getKey() == dst) {ans = Math.min(ans, tmp.getValue().getValue());}

            for (Pair<Integer, Integer> m : graph.get(tmp.getKey())) {
                if (m.getValue() + tmp.getValue().getValue() < ans) {
                    queue.offer(new Pair(m.getKey(), new Pair(tmp.getValue().getKey() + 1, m.getValue() + tmp.getValue().getValue())));
                }
            }

            queue.poll();
        }

        return ans == Integer.MAX_VALUE ? -1 : ans;
    }
}

// DFS
class Solution {
    private int[][] graph;
    private boolean[] visited;
    private int ans = Integer.MAX_VALUE;

    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        k = Math.min(k, n - 2);
        this.graph = new int[n][n];
        this.visited = new boolean[n];
        for (int[] cur : flights) {graph[cur[0]][cur[1]] = cur[2];}

        dfs(src, dst, k + 1, 0);

        if (ans == Integer.MAX_VALUE) {return -1;}
        return ans;
    }

    private void dfs(int src, int dst, int k, int cost) {
        if (src == dst) {
            ans = cost;
            return;
        }

        if (k == 0) {return;}

        for (int i = 0; i < graph[src].length; i++) {
            if (graph[src][i] > 0) {
                if (visited[i]) {continue;}
                if (cost + graph[src][i] > ans) {continue;}

                visited[i] = true;
                dfs(i, dst, k - 1, cost + graph[src][i]);
                visited[i] = false;
            }
        }
    }
}

// Dijkstra 算法
public class Solution {
    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int K) {
        // 使用邻接矩阵表示有向图，0 表示不连通
        int[][] graph = new int[n][n];
        for (int[] flight : flights) {
            graph[flight[0]][flight[1]] = flight[2];
        }

        PriorityQueue<int[]> minHeap = new PriorityQueue<>(Comparator.comparingInt(a -> a[1]));
        // 向集合添加一个记录（起点, 费用, 站数限制）的数组，K + 1 表示可以走过站点的个数
        minHeap.offer(new int[]{src, 0, K + 1});

        while (!minHeap.isEmpty()) {
            int[] front = minHeap.poll();
            int v = front[0];
            int price = front[1];
            int k = front[2];

            if (v == dst) {return price;}

            // 如果还可以中转一个站
            if (k > 0) {
                for (int i = 0; i < n; i++) {
                    // 并且存在一条有向边
                    if (graph[v][i] > 0 ) {
                        // 优先队列中存入：有向边指向的顶点 i、从起点 src 到 i 的总路径长度、还有多少站可以中转
                        minHeap.offer(new int[]{i, price + graph[v][i]  , k - 1});
                    }
                }
            }
        }
        return -1;
    }
}
```

## 判断二分图

> 785
>
> *二分图 定义：如果能将一个图的节点集合分割成两个独立的子集 A 和 B ，并使图中的每一条边的两个节点一个来自 A 集合，一个来自 B 集合，就将这个图称为 二分图 。*
>
> *如果图是二分图，返回 true ；否则，返回 false 。*

```java
// BFS
class Solution {
    public boolean isBipartite(int[][] graph) {
        int n = graph.length;
        int[] color = new int[n];
        
        for (int i = 0; i < n; i++) {
            if (color[i] == 0) {
                Queue<Integer> queue = new LinkedList<>();
                queue.offer(i);
                color[i] = 1;
                while (!queue.isEmpty()) {
                    int cur = queue.poll();
                    int cNext = (color[cur] == 1 ? 2 : 1);
                    for (int neigh : graph[cur]) {
                        if (color[neigh] == 0) {
                            queue.offer(neigh);
                            color[neigh] = cNext;
                        }
                        else if (color[neigh] != cNext) {
                            return false;
                        }
                    }
                }
            }
        }

        return true;
    }
}

// DFS
class Solution {
    private int[] color;
    private boolean ans;

    public boolean isBipartite(int[][] graph) {
        int n = graph.length;
        ans = true;
        color = new int[n];
        Arrays.fill(color, 0);
        for (int i = 0; i < n && ans; i++) {
            if (color[i] == 0) {
                dfs(i, 1, graph);
            }
        }
        return ans;
    }

    private void dfs(int node, int c, int[][] graph) {
        color[node] = c;
        int cNext = (c == 1 ? 2 : 1);
        for (int neigh : graph[node]) {
            if (color[neigh] == 0) {
                dfs(neigh, cNext, graph);
            }
            else if (color[neigh] != cNext) {
                ans = false;
                return;
            }
        }
    }
}
```

## 可能的二分法

> 886
>
> *给定一组 N 人（编号为 1, 2, ..., N）， 我们想把每个人分进任意大小的两组。*
>
> *每个人都可能不喜欢其他人，那么他们不应该属于同一组。*
>
> *形式上，如果 dislikes[i] = [a, b]，表示不允许将编号为 a 和 b 的人归入同一组。*
>
> *当可以用这种方法将所有人分进两组时，返回 true；否则返回 false。*

```java
class Solution {
    public boolean possibleBipartition(int N, int[][] dislikes) {
        List<Integer>[] graph = new ArrayList[N + 1];
        for (int i = 1; i <= N; i++) {graph[i] = new ArrayList<>();}

        for (int[] cur : dislikes) {
            graph[cur[0]].add(cur[1]);
            graph[cur[1]].add(cur[0]);
        }

        Map<Integer,Integer> color = new HashMap<>();
        for (int node = 1; node <= N; node++) {
            if (!color.containsKey(node) && !dfs(graph, color, node, 0)) {
                return false;
            }
        }
        return true;
    }

    private static boolean dfs(List<Integer>[] graph, Map<Integer,Integer> color, int node, int c) {
        if (color.containsKey(node)) {return color.get(node) == c;}
        
        color.put(node, c);

        for (int neigh : graph[node]) {
            if (!dfs(graph, color, neigh, c ^ 1)) {return false;}
        }

        return true;
    }
}
```

## 所有可能的路径

> 797
>
> *给一个有 n 个结点的有向无环图，找到所有从 0 到 n-1 的路径并输出（不要求按顺序）*

```java
class Solution {
    public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
        List<List<Integer>> ans = new ArrayList<>();
        if (graph.length == 0) {return ans;}

        dfs(graph, ans, 0, new ArrayList<Integer>());

        return ans;
    }

    private static void dfs(int[][] graph, List<List<Integer>> ans, int cur, List<Integer> path) {
        path.add(cur);
        
        if (cur == graph.length - 1) {
            ans.add(new ArrayList<>(path));
        }
        for (int neigh : graph[cur]) {
            dfs(graph, ans, neigh, path);
        }
        
        path.remove(path.size() - 1);
    }
}
```

## 省份数量

> 547
>
> *有 n 个城市，其中一些彼此相连，另一些没有相连。如果城市 a 与城市 b 直接相连，且城市 b 与城市 c 直接相连，那么城市 a 与城市 c 间接相连。*
>
> *省份 是一组直接或间接相连的城市，组内不含其他没有相连的城市。*
>
> *给你一个 n x n 的矩阵 isConnected ，其中 isConnected[i][j] = 1 表示第 i 个城市和第 j 个城市直接相连，而 isConnected[i][j] = 0 表示二者不直接相连。*
>
> *返回矩阵中 省份 的数量。*

```java
// BFS
class Solution {
    public int findCircleNum(int[][] isConnected) {
        int n = isConnected.length;
        boolean[] visited = new boolean[n];
        int ans = 0;
        Queue<Integer> queue = new LinkedList<>();

        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                queue.offer(i);
                while (!queue.isEmpty()) {
                    int cur = queue.poll();
                    visited[cur] = true;
                    for (int k = 0; k < n; k++) {
                        if (isConnected[cur][k] == 1 && !visited[k]) {
                            queue.offer(k);
                        }
                    }
                }
                ans++;
            }
        }

        return ans;
    }
}

// DFS 
class Solution {
    public int findCircleNum(int[][] isConnected) {
        int n = isConnected.length;
        boolean[] visited  = new boolean[n];
        int ans = 0;

        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                dfs(isConnected, visited, n, i);
                ans++;
            }
        }
        
        return ans;
    }

    private static void dfs(int[][] isConnected, boolean[] visited, int n, int i) {
        for (int j = 0; j < n; j++) {
            if (isConnected[i][j] == 1 && !visited[j]) {
                visited[j] = true;
                dfs(isConnected, visited, n, j);
            }
        }
    }
}
```

## 冗余连接

> 684
>
> *在本问题中, 树指的是一个连通且无环的**无向图**。*
>
> *输入一个图，该图由一个有着N个节点 (节点值不重复1, 2, ..., N) 的树及一条附加的边构成。附加的边的两个顶点包含在1到N中间，这条附加的边不属于树中已存在的边。*
>
> *结果图是一个以边组成的二维数组。每一个边的元素是一对[u, v] ，满足 u < v，表示连接顶点u 和v的无向图的边。*
>
> *返回一条可以删去的边，使得结果图是一个有着N个节点的树。如果有多个答案，则返回二维数组中最后出现的边。答案边 [u, v] 应满足相同的格式 u < v。*

```java
class Solution {
    public int[] findRedundantConnection(int[][] edges) {
        int n = edges.length;
        UnionFind uf = new UnionFind(n + 1);

        for (int i = 0; i < n; i++) {
            int[] cur = edges[i];
            int node1 = cur[0], node2 = cur[1];
            if (uf.find(node1) != uf.find(node2)) {
                uf.union(node1, node2);
            }
            else {
                return cur;
            }
        }

        return new int[0];
    }
}

class UnionFind {
    int[] ancestor;

    public UnionFind(int n) {
        ancestor = new int[n];
        for (int i = 0; i < n; i++) {ancestor[i] = i;}
    }

    public void union(int idx1, int idx2) {
        ancestor[find(idx1)] = find(idx2);
    }

    public int find(int idx) {
        if (ancestor[idx] != idx) {
            ancestor[idx] = find(ancestor[idx]);
        }
        return ancestor[idx];
    }
}
```

> 685
>
> *在本问题中，有根树指满足以下条件的 **有向图**。该树只有一个根节点，所有其他节点都是该根节点的后继。该树除了根节点之外的每一个节点都有且只有一个父节点，而根节点没有父节点。*
>
> *输入一个有向图，该图由一个有着 n 个节点（节点值不重复，从 1 到 n）的树及一条附加的有向边构成。附加的边包含在 1 到 n 中的两个不同顶点间，这条附加的边不属于树中已存在的边。*
>
> *结果图是一个以边组成的二维数组 edges 。 每个元素是一对 [ui, vi]，用以表示 有向 图中连接顶点 ui 和顶点 vi 的边，其中 ui 是 vi 的一个父节点。*
>
> *返回一条能删除的边，使得剩下的图是有 n 个节点的有根树。若有多个答案，返回最后出现在给定二维数组的答案。*

```java
class Solution {
    public int[] findRedundantDirectedConnection(int[][] edges) {
        int n = edges.length;
        UnionFind uf = new UnionFind(n + 1);
        int[] parent = new int[n + 1];
        for (int i = 0; i <= n; i++) {parent[i] = i;}

        int conflict = -1, cycle = -1;
        for (int i = 0; i < n; i++) {
            int[] cur = edges[i];
            int node1 = cur[0], node2 = cur[1];
            if (parent[node2] != node2) {conflict = i;}
            else {
                parent[node2] = node1;
                if (uf.find(node1) == uf.find(node2)) {
                    cycle = i;
                }
                else {
                    uf.union(node1, node2);
                }
            }
        }

        if (conflict < 0) {return edges[cycle];}
        else {
            int[] conflictEdge = edges[conflict];
            if (cycle > 0) {
                return new int[]{parent[conflictEdge[1]], conflictEdge[1]};
            }
            else {
                return conflictEdge;
            }
        }
    }
}

class UnionFind {
    int[] ancestor;

    public UnionFind(int n) {
        ancestor = new int[n];
        for (int i = 0; i < n; ++i) {
            ancestor[i] = i;
        }
    }

    public void union(int index1, int index2) {
        ancestor[find(index1)] = find(index2);
    }

    public int find(int index) {
        if (ancestor[index] != index) {
            ancestor[index] = find(ancestor[index]);
        }
        return ancestor[index];
    }
}
```

## 岛屿数量

> 200
>
> 给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。
>
> 岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。
>
> 此外，你可以假设该网格的四条边均被水包围。

```java
class Solution {
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) {return 0;}

        int n = grid.length, m = grid[0].length;
        int ans = 0;

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == '1') {
                    ans++;
                    dfs(grid, i, j);
                }
            }
        }

        return ans;
    }

    private static void dfs(char[][] grid, int row, int col) {
        int n = grid.length, m = grid[0].length;

        if (row < 0 || row >= n || col < 0 || col >= m || grid[row][col] == '0') {return;}

        grid[row][col] = '0';
        dfs(grid, row - 1, col);
        dfs(grid, row + 1, col);
        dfs(grid, row, col - 1);
        dfs(grid, row, col + 1);
    }
}
```

## 岛屿的最大面积

> 695
>
> *给定一个包含了一些 0 和 1 的非空二维数组 grid 。*
>
> *一个 岛屿 是由一些相邻的 1 (代表土地) 构成的组合，这里的「相邻」要求两个 1 必须在水平或者竖直方向上相邻。你可以假设 grid 的四个边缘都被 0（代表水）包围着。*
>
> *找到给定的二维数组中最大的岛屿面积。(如果没有岛屿，则返回面积为 0 。)*

```java
class Solution {
    public int maxAreaOfIsland(int[][] grid) {
        if (grid.length == 0 || grid[0].length == 0) {return 0;}

        int n = grid.length, m = grid[0].length;
        int ans = 0;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == 1) {
                    int cur = dfs(grid, i, j);
                    ans = Math.max(ans, cur);
                }
            }
        }

        return ans;
    }

    private static int dfs(int[][] grid, int row, int col) {
        int n = grid.length, m = grid[0].length;

        if (row < 0 || row >= n || col < 0 || col >= m || grid[row][col] == 0) {return 0;}

        grid[row][col] = 0;
        return 1 + dfs(grid, row - 1, col) + dfs(grid, row + 1, col) + dfs(grid, row, col - 1) + dfs(grid, row, col + 1);
    }
}
```

## 岛屿的周长

> 463
>
> *给定一个 row x col 的二维网格地图 grid ，其中：grid[i][j] = 1 表示陆地， grid[i][j] = 0 表示水域。*
>
> *网格中的格子 水平和垂直 方向相连（对角线方向不相连）。整个网格被水完全包围，但其中恰好有一个岛屿（或者说，一个或多个表示陆地的格子相连组成的岛屿）。*
>
> *岛屿中没有“湖”（“湖” 指水域在岛屿内部且不和岛屿周围的水相连）。格子是边长为 1 的正方形。网格为长方形，且宽度和高度均不超过 100 。计算这个岛屿的周长。*

```java
class Solution {
    private static int[][] d = new int[][]{{-1, 0}, {0, 1}, {1, 0}, {0, -1}};

    public int islandPerimeter(int[][] grid) {
        int n = grid.length, m = grid[0].length;
        int ans = 0;

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == 1) {
                    int count = 0;
                    for (int k = 0; k < 4; k++) {
                        int nextX = i + d[k][0];
                        int nextY = j + d[k][1];
                        if (nextX < 0 || nextX >= n || nextY < 0 || nextY >= m || grid[nextX][nextY] == 0) {count++;}
                    }
                    ans += count;
                }
            }
        }

        return ans;
    }
}
```

## 太平洋大西洋水流问题

> 417
>
> *给定一个 m x n 的非负整数矩阵来表示一片大陆上各个单元格的高度。“太平洋”处于大陆的左边界和上边界，而“大西洋”处于大陆的右边界和下边界。*
>
> *规定水流只能按照上、下、左、右四个方向流动，且只能从高到低或者在同等高度上流动。*
>
> *请找出那些水流既可以流动到“太平洋”，又能流动到“大西洋”的陆地单元的坐标。*

```java
class Solution {
    private static int[][] dir = new int[][]{{1, 0}, {-1, 0}, {0, 1}, {0, -1}};

    public List<List<Integer>> pacificAtlantic(int[][] heights) {
        List<List<Integer>> ans = new ArrayList<>();
        if (heights.length == 0) {return ans;}

        int n = heights.length, m = heights[0].length;
        int[][] po = new int[n][m], ao = new int[n][m];

        // 从各边界开始逆流进行搜索
        for (int i = 0; i < n; i++) {
            dfs(heights, i, 0, po);
            dfs(heights, i, m - 1, ao);
        }

        for (int j = 0; j < m; j++) {
            dfs(heights, 0, j, po);
            dfs(heights, n - 1, j, ao);
        }

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (po[i][j] == 1 && ao[i][j] == 1) {
                    ans.add(Arrays.asList(i, j));
                }
            }
        }

        return ans;
    }

    private static boolean inArea(int[][] matrix, int x, int y) {
        return x >= 0 && x < matrix.length && y >= 0 && y < matrix[0].length;
    }

    private static void dfs(int[][] matrix, int x, int y, int[][] cur) {
        cur[x][y] = 1;
        for (int[] d : dir) {
            int nextX = x + d[0], nextY = y + d[1];
            //这里的判断条件是：在岛上；递增；在没流过的路上。
            if (!inArea(matrix, nextX, nextY) || matrix[x][y] > matrix[nextX][nextY] || cur[nextX][nextY] == 1) {continue;}
            dfs(matrix, nextX, nextY, cur);
        }
    }
}
```

## 0/1矩阵

> 542
>
> *给定一个由 0 和 1 组成的矩阵，找出每个元素到最近的 0 的距离。*
>
> *两个相邻元素间的距离为 1 。*

```java
class Solution {
    private static int[][] dir = new int[][]{{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

    public int[][] updateMatrix(int[][] mat) {
        int n = mat.length, m = mat[0].length;
        int[][] ans = new int[n][m];
        boolean[][] visited = new boolean[n][m];
        Queue<int[]> queue = new LinkedList<>();

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (mat[i][j] == 0) {
                    queue.offer(new int[]{i, j});
                    visited[i][j] = true;
                }
            }
        }

        while (!queue.isEmpty()) {
            int[] cur = queue.poll();
            int x = cur[0], y = cur[1];
            for (int k = 0; k < 4; k++) {
                int nextX = x + dir[k][0], nextY = y + dir[k][1];
                if (nextX >= 0 && nextX < n && nextY >= 0 && nextY < m && !visited[nextX][nextY]) {
                    ans[nextX][nextY] = ans[x][y] + 1;
                    queue.offer(new int[]{nextX, nextY});
                    visited[nextX][nextY] = true;
                }
            }
        }

        return ans;
    }
}
```

## 网络延迟时间

> 743
>
> *有 n 个网络节点，标记为 1 到 n。*
>
> *给你一个列表 times，表示信号经过 有向 边的传递时间。 times[i] = (ui, vi, wi)，其中 ui 是源节点，vi 是目标节点， wi 是一个信号从源节点传递到目标节点的时间。*
>
> *现在，从某个节点 K 发出一个信号。需要多久才能使所有节点都收到信号？如果不能使所有节点收到信号，返回 -1 。*

```java
class Solution {
    public int networkDelayTime(int[][] times, int n, int k) {
        Map<Integer, Integer> dist = new HashMap<>();
        Map<Integer, List<int[]>> graph = new HashMap<>();
        for (int[] cur : times) {
            if (!graph.containsKey(cur[0])) {
                graph.put(cur[0], new ArrayList<int[]>());
            }
            graph.get(cur[0]).add(new int[]{cur[1], cur[2]});
        }

        for (int node : graph.keySet()) {Collections.sort(graph.get(node), (a, b) -> a[0] - b[0]);}
        for (int i = 1; i <= n; i++) {dist.put(i, Integer.MAX_VALUE);}

        dfs(graph, dist, k, 0);

        int ans = 0;
        for (int num : dist.values()) {
            if (num == Integer.MAX_VALUE) {return -1;}
            ans = Math.max(ans, num);
        }
        return ans;
    }

    private static void dfs(Map<Integer, List<int[]>> graph, Map<Integer, Integer> dist, int node, int time) {
        if (time >= dist.get(node)) {return;}

        dist.put(node, time);
        if (graph.containsKey(node)) {
            for (int[] cur : graph.get(node)) {
                dfs(graph, dist, cur[0], time + cur[1]);
            }
        }
    }
}
```

## 打开转盘锁

> 752
>
> *你有一个带有四个圆形拨轮的转盘锁。每个拨轮都有10个数字： '0', '1', '2', '3', '4', '5', '6', '7', '8', '9' 。每个拨轮可以自由旋转：例如把 '9' 变为  '0'，'0' 变为 '9' 。每次旋转都只能旋转一个拨轮的一位数字。*
>
> *锁的初始数字为 '0000' ，一个代表四个拨轮的数字的字符串。*
>
> *列表 deadends 包含了一组死亡数字，一旦拨轮的数字和列表里的任何一个元素相同，这个锁将会被永久锁定，无法再被旋转。*
>
> *字符串 target 代表可以解锁的数字，你需要给出最小的旋转次数，如果无论如何不能解锁，返回 -1。*

```java
// 1 BFS
class Solution {
    public int openLock(String[] deadends, String target) {
        // 记录需要跳过的（包括限制不能出现的和已经访问过的）
        Set<String> deads = new HashSet<>();
        for (String s : deadends){deads.add(s);}
        Queue<String> queue = new LinkedList<>();
        Set<String> visited = new HashSet<>();

        int step = 0;
        queue.offer("0000");
        visited.add("0000");

        while (!queue.isEmpty()){
            int n = queue.size();
            for (int i = 0; i < n; i++){
                String cur = queue.poll();
                if (deads.contains(cur)){continue;}
                if (cur.equals(target)){return step;}

                for (int j = 0; j < 4; j++){
                    String up = nextOne(cur, j).get(0);
                    if (!visited.contains(up)){
                        queue.offer(up);
                        visited.add(up);
                    }
                    String down = nextOne(cur, j).get(1);
                    if (!visited.contains(down)){
                        queue.offer(down);
                        visited.add(down);
                    }
                }
            }
            step++;
        }
        return -1;
    }

    public List<String> nextOne(String s, int index){
        List<String> res = new LinkedList<>();

        char[] upCh = s.toCharArray();
        if (upCh[index] == '9') {upCh[index] = '0';}
        else {upCh[index] += 1;}
        res.add(new String(upCh));
        
        char[] downCh = s.toCharArray();
        if (downCh[index] == '0') {downCh[index] = '9';}
        else {downCh[index] -= 1;}
        res.add(new String(downCh));

        return res;
    }
}
```

## 不邻接植花

> 1042
>
> *有 n 个花园，按从 1 到 n 标记。另有数组 paths ，其中 paths[i] = [xi, yi] 描述了花园 xi 到花园 yi 的双向路径。在每个花园中，你打算种下四种花之一。*
>
> *另外，所有花园 最多 有 3 条路径可以进入或离开.*
>
> *你需要为每个花园选择一种花，使得通过路径相连的任何两个花园中的花的种类互不相同。*
>
> *以数组形式返回 任一 可行的方案作为答案 answer，其中 answer[i] 为在第 (i+1) 个花园中种植的花的种类。花的种类用  1、2、3、4 表示。保证存在答案。*

```java
class Solution {
    public int[] gardenNoAdj(int n, int[][] paths) {
        /* 这是一道简单题，限制每个节点的度为3，同时提供四种颜色，因此不需要回溯 */
        /* 初始化节点，使用map保存节点与其临界点的关系 */
        Map<Integer, Set<Integer>> graph = new HashMap<>();
        for (int i = 0; i < n; i++) {
            graph.put(i, new HashSet<>());
        }
        /* 初始化路径信息 */
        for (int[] cur : paths) {
            int a = cur[0] - 1;
            int b = cur[1] - 1;
            graph.get(a).add(b);
            graph.get(b).add(a);
        }

        int[] ans = new int[n];
        for (int i = 0; i < n; i++) {
            /* 查看当前节点的所有邻接点的色彩 */
            boolean[] visited = new boolean[5];
            for (int adj : graph.get(i)) {
                visited[ans[adj]] = true;
            }
            /* 为当前节点染色 */
            for (int j = 1; j <= 4; j++) {
                if (!visited[j]) {
                    ans[i] = j;
                }
            }
        }
        return ans;
    }
}
```

## 颜色交替的最短路径

> 1129
>
> *在一个有向图中，节点分别标记为 0, 1, ..., n-1。这个图中的每条边不是红色就是蓝色，且存在自环或平行边。*
>
> *red_edges 中的每一个 [i, j] 对表示从节点 i 到节点 j 的**红色有向边**。类似地，blue_edges 中的每一个 [i, j] 对表示从节点 i 到节点 j 的**蓝色有向边**。*
>
> *返回长度为 n 的数组 answer，其中 answer[X] 是从节点 0 到节点 X 的红色边和蓝色边交替出现的最短路径的长度。如果不存在这样的路径，那么 answer[x] = -1。*

```java
class Solution {
    public int[] shortestAlternatingPaths(int n, int[][] red_edges, int[][] blue_edges) {
		List<List<Integer>> rg = new ArrayList<>();//这是 红色线的集合
		List<List<Integer>> bg = new ArrayList<>();//这是 蓝色线的集合

		for (int i = 0; i < n; i++) {
			//初始化两条线的集合
			rg.add(new ArrayList<>());
			bg.add(new ArrayList<>());
		}

		//根据数组改变 红蓝色线 集合
		for (int[] red : red_edges) {rg.get(red[0]).add(red[1]);}
		for (int[] blue : blue_edges) {bg.get(blue[0]).add(blue[1]);}

		int[][] ans = new int[n][2];
		for (int[] ansColor : ans) {
			//初始化所有距离为MAX
			ansColor[0] = Integer.MAX_VALUE;
			ansColor[1] = Integer.MAX_VALUE;
		}
		//出发点距离设为0
		ans[0][0] = 0;
		ans[0][1] = 0;

		dfs(ans, 0, 0, rg, bg);     //从红色线出发
		dfs(ans, 1, 0, rg, bg);     //从蓝色线出发

		int[] finalAns = new int[n];
		for (int i = 0; i < n; i++) {
			//取最小值  没有的话为-1
			finalAns[i] = Math.min(ans[i][0], ans[i][1]);
			if (finalAns[i] == Integer.MAX_VALUE)
				finalAns[i] = -1;
		}
		return finalAns;
	}

	public void dfs(int[][] ans, int color, int i, List<List<Integer>> rg, List<List<Integer>> bg) {
		List<List<Integer>> g = color == 0 ? rg : bg;		//选择 红蓝色 线
		for (int j : g.get(i)) {
			//遍历该线段 以 i 为出发点的终点
			if (ans[i][color] + 1 < ans[j][1 - color]) {
				//判断 0 -> i -> j 的长度是否 比 已有的 0 -> j 的路径长度长 若是 则更新 
				//!!!这个判断是整个算法的核心 
				//当在也找不到更短的路径时 dfs会停止搜索 否则继续
				ans[j][1 - color] = ans[i][color] + 1;
                dfs(ans, 1 - color, j, rg, bg);
            }
		}
	}
}
```

## 最小高度树

> 310
>
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

## 员工的重要性

> 690
>
> *给定一个保存员工信息的数据结构，它包含了员工 唯一的 id ，重要度 和 直系下属的 id 。*
>
> *比如，员工 1 是员工 2 的领导，员工 2 是员工 3 的领导。他们相应的重要度为 15 , 10 , 5 。那么员工 1 的数据结构是 [1, 15, [2]] ，员工 2的 数据结构是 [2, 10, [3]] ，员工 3 的数据结构是 [3, 5, []] 。注意虽然员工 3 也是员工 1 的一个下属，但是由于 并不是直系 下属，因此没有体现在员工 1 的数据结构中。*
>
> *现在输入一个公司的所有员工信息，以及单个员工 id ，返回这个员工和他所有下属的重要度之和。*

```java
// BFS
class Solution {
    public int getImportance(List<Employee> employees, int id) {
        Map<Integer, Employee> map = new HashMap<>();
        for (Employee e : employees) {map.put(e.id, e);}

        int ans = 0;
        Queue<Integer> queue = new LinkedList<>();
        queue.offer(id);
        while (!queue.isEmpty()) {
            int cur = queue.poll();
            Employee e = map.get(cur);
            ans += e.importance;

            List<Integer> sub = e.subordinates;
            for (int n : sub) {queue.offer(n);}
        }

        return ans;
    }
}

// DFS 
class Solution {
    Map<Integer, Employee> map = new HashMap<>();

    public int getImportance(List<Employee> employees, int id) {
        for (Employee e : employees) {
            map.put(e.id, e);
        }    
        return dfs(id);
    }

    private int dfs(int id) {
        Employee e = map.get(id);
        int ans = e.importance;
        List<Integer> sub = e.subordinates;
        for (int n : sub) {
            ans += dfs(n);
        }
        return ans;
    }
}
```

## 马在棋盘上的概率

> 688
>
> *已知一个 NxN 的国际象棋棋盘，棋盘的行号和列号都是从 0 开始。即最左上角的格子记为 (0, 0)，最右下角的记为 (N-1, N-1)。* 
>
> *现有一个 “马”（也译作 “骑士”）位于 (r, c) ，并打算进行 K 次移动。* 
>
> *如下图所示，国际象棋的 “马” 每一步先沿水平或垂直方向移动 2 个格子，然后向与之相垂直的方向再移动 1 个格子，共有 8 个可选的位置。*
>
> *现在 “马” 每一步都从可选的位置（包括棋盘外部的）中独立随机地选择一个进行移动，直到移动了 K 次或跳到了棋盘外面。*
>
> *求移动结束后，“马” 仍留在棋盘上的概率。*

```java
class Solution {
    public double knightProbability(int n, int k, int row, int col) {
        double[][] dp = new double[n][n];
        int[] dirRow = new int[]{2, 2, 1, 1, -1, -1, -2, -2};
        int[] dirCol = new int[]{1, -1, 2, -2, 2, -2, 1, -1};

        dp[row][col] = 1;
        while (k > 0) {
            double[][] dp2 = new double[n][n];
            for (int i = 0; i < n ; i++) {
                for (int j = 0; j < n; j++) {
                    for (int t = 0; t < 8; t++) {
                        int nextX = i + dirRow[t];
                        int nextY = j + dirCol[t];
                        if (nextX >= 0 && nextX < n && nextY >= 0 && nextY < n) {
                            dp2[nextX][nextY] += dp[i][j] / 8.0;
                        }
                    }
                }
            }
            dp = dp2;
            k--;
        }

        double ans = 0.0;
        for (double[] r : dp) {
            for (double num : r) {
                ans += num;
            }
        }

        return ans;
    }
}
```

## 扫雷游戏

> 529
>
> 给定一个代表游戏板的二维字符矩阵。 'M' 代表一个未挖出的地雷，'E' 代表一个未挖出的空方块，'B' 代表没有相邻（上，下，左，右，和所有4个对角线）地雷的已挖出的空白方块，数字（'1' 到 '8'）表示有多少地雷与这块已挖出的方块相邻，'X' 则表示一个已挖出的地雷。
>
> 现在给出在所有未挖出的方块中（'M'或者'E'）的下一个点击位置（行和列索引），根据以下规则，返回相应位置被点击后对应的面板：
>
> 如果一个地雷（'M'）被挖出，游戏就结束了- 把它改为 'X'。
> 如果一个没有相邻地雷的空方块（'E'）被挖出，修改它为（'B'），并且所有和其相邻的未挖出方块都应该被递归地揭露。
> 如果一个至少与一个地雷相邻的空方块（'E'）被挖出，修改它为数字（'1'到'8'），表示相邻地雷的数量。
> 如果在此次点击中，若无更多方块可被揭露，则返回面板。

```java
class Solution {
    private static int[] dirX = {0, 1, 0, -1, 1, 1, -1, -1};
    private static int[] dirY = {1, 0, -1, 0, 1, -1, 1, -1};

    public char[][] updateBoard(char[][] board, int[] click) {
        int x = click[0], y = click[1];
        if (board[x][y] == 'M') {
            board[x][y] = 'X';
        }
        else {
            dfs(board, x, y);
        }
        return board;
    }

    private static void dfs(char[][] board, int x, int y) {
        int cnt = 0;
        for (int i = 0; i < 8; i++) {
            int nextX = x + dirX[i], nextY = y + dirY[i];
            if (nextX < 0 || nextX >= board.length || nextY < 0 || nextY >= board[0].length) {
                continue;
            }

            if (board[nextX][nextY] == 'M') {cnt++;}
        }

        if (cnt > 0) {board[x][y] = (char)(cnt + '0');}
        else {
            board[x][y] = 'B';
            for (int t = 0; t < 8; t++) {
                int nextX = x + dirX[t], nextY = y + dirY[t];
                if (nextX < 0 || nextX >= board.length || nextY < 0 || nextY >= board[0].length || board[nextX][nextY] != 'E') {
                    continue;
                }

                dfs(board, nextX, nextY);
            }
        }
    }
}
```

