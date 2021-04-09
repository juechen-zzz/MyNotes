[TOC]

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

## 单词搜索2（0212）

> 给定一个 m x n 二维字符网格 board 和一个单词（字符串）列表 words，找出所有同时在二维网格和字典中出现的单词。
>
> 单词必须按照字母顺序，通过 相邻的单元格 内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母在一个单词中不允许被重复使用。
>

```java
class Solution {
    List<String> ans = new ArrayList<>();

    public List<String> findWords(char[][] board, String[] words) {
        int m = board.length, n = board[0].length;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                dfs(board, words, "", i, j);
            }
        }
        return ans;
    }

    public void dfs(char[][] board, String[] words, String s, int x, int y) {
        int m = board.length, n = board[0].length;
        if (x < 0 || x >= m || y < 0 || y >= n) {return;}

        if (containsWord(words, s)) {ans.add(s);}

        dfs(board, words, s + board[x][y], x + 1, y);
        dfs(board, words, s + board[x][y], x - 1, y);
        dfs(board, words, s + board[x][y], x, y + 1);
        dfs(board, words, s + board[x][y], x, y - 1);
    }

    public boolean containsWord(String[] words, String s) {
        for (String tmp : words) {
            if (tmp.equals(s)) {
                return true;
            }
        }
        return false;
    }
}
```

## 最小高度树（0310）

> 树是一个无向图，其中任何两个顶点只通过一条路径连接。 换句话说，一个任何没有简单环路的连通图都是一棵树。
>
> 给你一棵包含 n 个节点的树，标记为 0 到 n - 1 。给定数字 n 和一个有 n - 1 条无向边的 edges 列表（每一个边都是一对标签），其中 edges[i] = [ai, bi] 表示树中节点 ai 和 bi 之间存在一条无向边。
>
> 可选择树中任何一个节点作为根。当选择节点 x 作为根节点时，设结果树的高度为 h 。在所有可能的树中，具有最小高度的树（即，min(h)）被称为 最小高度树 。
>
> 请你找到所有的 最小高度树 并按 任意顺序 返回它们的根节点标签列表。
>

```java
class Solution {
    public List<Integer> findMinHeightTrees(int n, int[][] edges) {
        List<Integer> ans = new ArrayList<>();

        int[] degree = new int[n];
        List<List<Integer>> adjency = new ArrayList<>();
        for (int i = 0; i < n; i++) {adjency.add(new ArrayList<>());}
        for (int[] edge : edges) {
            degree[edge[0]]++;
            degree[edge[1]]++;
            adjency.get(edge[0]).add(edge[1]);
            adjency.get(edge[1]).add(edge[0]);
        }

        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            if (degree[i] == 1) {
                queue.offer(i);
            }
        }

        int residue = n;
        if (residue == 1) {ans.add(0);}

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

        while (!queue.isEmpty()) {
            ans.add(queue.poll());
        }
        return ans;
    }
}
```

## 重新安排行程（0332）

> *给定一个机票的字符串二维数组 [from, to]，子数组中的两个成员分别表示飞机出发和降落的机场地点，对该行程进行重新规划排序。所有这些机票都属于一个从 JFK（肯尼迪国际机场）出发的先生，所以该行程必须从 JFK 开始。*

```java
class Solution {
    Map<String, PriorityQueue<String>> map = new HashMap<>();
    List<String> ans = new LinkedList<>();

    public List<String> findItinerary(List<List<String>> tickets) {
        for (List<String> ticket : tickets) {
            String src = ticket.get(0), dst = ticket.get(1);
            if (!map.containsKey(src)) {
                map.put(src, new PriorityQueue<String>());
            }
            map.get(src).offer(dst);
        }

        dfs("JFK");
        Collections.reverse(ans);
        return ans;
    }

    public void dfs(String cur) {
        while (map.containsKey(cur) && map.get(cur).size() > 0) {
            String tmp = map.get(cur).poll();
            dfs(tmp);
        }
        ans.add(cur);
    }
}
```

## 判断二分图（0785）

> *二分图 定义：如果能将一个图的节点集合分割成两个独立的子集 A 和 B ，并使图中的每一条边的两个节点一个来自 A 集合，一个来自 B 集合，就将这个图称为 二分图 。*
>
> *如果图是二分图，返回 true ；否则，返回 false 。*

```java
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

// BFS
class Solution {
    private int[] color;

    public boolean isBipartite(int[][] graph) {
        int n = graph.length;
        color = new int[n];
        Arrays.fill(color, 0);
        for (int i = 0; i < n; i++) {
            if (color[i] == 0) {
                Queue<Integer> queue = new LinkedList<>();
                queue.offer(i);
                color[i] = 1;
                while (!queue.isEmpty()) {
                    int node = queue.poll();
                    int cNext = (color[node] == 1 ? 2 : 1);
                    for (int neigh : graph[node]) {
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
```

## 所有可能的路径（0797）

> *给一个有 n 个结点的有向无环图，找到所有从 0 到 n-1 的路径并输出（不要求按顺序）*

```java
class Solution {
    List<List<Integer>> ans = new ArrayList<>();

    public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
        if (graph.length == 0) {return ans;}
        dfs(graph, 0, new ArrayList<Integer>());
        return ans;
    }

    private void dfs(int[][] graph, int cur, List<Integer> path) {
        path.add(cur);
        if (cur == graph.length - 1) {
            ans.add(new ArrayList<>(path));
        }
        for (int neigh : graph[cur]) {
            dfs(graph, neigh, path);
        }
        path.remove(path.size() - 1);
    }
}
```

## 省份数量（0547）

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
        int num = isConnected.length;
        boolean[] visited = new boolean[num];
        int ans = 0;
        Queue<Integer> queue = new LinkedList<>();
        
        for (int i = 0; i < num; i++){
            // 首先判定这个点没访问过
            if (!visited[i]){
                queue.offer(i);
                while (!queue.isEmpty()){
                    int j = queue.poll();
                    visited[j] = true;
                    for (int k = 0; k < num; k++){
                        if (isConnected[j][k] == 1 && !visited[k]){
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
        int provinces = isConnected.length;
        boolean[] visited = new boolean[provinces];
        int circles = 0;
        for (int i = 0; i < provinces; i++) {
            if (!visited[i]) {
                dfs(isConnected, visited, provinces, i);
                circles++;
            }
        }
        return circles;
    }

    public void dfs(int[][] isConnected, boolean[] visited, int provinces, int i) {
        for (int j = 0; j < provinces; j++) {
            if (isConnected[i][j] == 1 && !visited[j]) {
                visited[j] = true;
                dfs(isConnected, visited, provinces, j);
            }
        }
    }
}
```

## 找到最终的安全状态（0802）

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
            if (dfs(i, color, graph)) {
                ans.add(i);
            }
        }

        return ans;
    }

    private boolean dfs(int node, int[] color, int[][] graph) {
        if (color[node] > 0) {return color[node] == 2;}

        color[node] = 1;
        for (int neigh : graph[node]) {
            if (color[neigh] == 2) {continue;}
            if (color[neigh] == 1 || !dfs(neigh, color, graph)) {return false;} 
        }

        color[node] = 2;
        return true;
    }
}
```

## 冗余连接（0684 && 0685）

> *在本问题中, 树指的是一个连通且无环的无向图。*
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
            int[] edge = edges[i];
            int node1 = edge[0], node2 = edge[1];
            if (uf.find(node1) != uf.find(node2)) {
                uf.union(node1, node2);
            }
            else {
                return edge;
            }
        }
        return new int[0];
    }
}

class UnionFind {
    int[] ancestor;

    public UnionFind(int n) {
        ancestor = new int[n];
        for (int i = 0; i < n; i++) {
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

> *在本问题中，有根树指满足以下条件的 有向 图。该树只有一个根节点，所有其他节点都是该根节点的后继。该树除了根节点之外的每一个节点都有且只有一个父节点，而根节点没有父节点。*
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
        for (int i = 1; i <= n; i++) {parent[i] = i;}

        int conflict = -1, cycle = -1;
        for (int i = 0; i < n; i++) {
            int[] edge = edges[i];
            int node1 = edge[0], node2 = edge[1];
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

## 岛屿数量（0200）

> 给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。
>
> 岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。
>
> 此外，你可以假设该网格的四条边均被水包围。
>

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

## 岛屿的最大面积（0695）

> *给定一个包含了一些 0 和 1 的非空二维数组 grid 。*
>
> *一个 岛屿 是由一些相邻的 1 (代表土地) 构成的组合，这里的「相邻」要求两个 1 必须在水平或者竖直方向上相邻。你可以假设 grid 的四个边缘都被 0（代表水）包围着。*
>
> *找到给定的二维数组中最大的岛屿面积。(如果没有岛屿，则返回面积为 0 。)*

```java
class Solution {
    public int maxAreaOfIsland(int[][] grid) {
        if (grid == null || grid.length == 0) {return 0;}

        int m = grid.length;
        int n = grid[0].length;
        int ans = 0;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    int tmp = dfs(grid, i, j);
                    ans = Math.max(ans, tmp);
                }
            }
        }

        return ans;
    }

    public int dfs(int[][] grid, int row, int col) {
        int m = grid.length;
        int n = grid[0].length;

        if (row < 0 || row >= m || col < 0 || col >= n || grid[row][col] == 0) {return 0;}

        grid[row][col] = 0;
        return 1 + dfs(grid, row - 1, col) + dfs(grid, row + 1, col) 
                 + dfs(grid, row, col + 1) + dfs(grid, row, col - 1);
    }
}
```

## 岛屿的周长（0463）

> *给定一个 row x col 的二维网格地图 grid ，其中：grid[i][j] = 1 表示陆地， grid[i][j] = 0 表示水域。*
>
> *网格中的格子 水平和垂直 方向相连（对角线方向不相连）。整个网格被水完全包围，但其中恰好有一个岛屿（或者说，一个或多个表示陆地的格子相连组成的岛屿）。*
>
> *岛屿中没有“湖”（“湖” 指水域在岛屿内部且不和岛屿周围的水相连）。格子是边长为 1 的正方形。网格为长方形，且宽度和高度均不超过 100 。计算这个岛屿的周长。*

```java
class Solution {
    private int[] dir = {1, 0, -1, 0, 1};

    public int islandPerimeter(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int ans = 0;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    int count = 0;
                    for (int k = 0; k < 4; k++) {
                        int nextX = i + dir[k];
                        int nextY = j + dir[k + 1];
                        if (nextX < 0 || nextX >= m || nextY < 0 || nextY >= n || grid[nextX][nextY] == 0) {count++;}
                    }
                    ans += count;
                }
            }
        }
        return ans;
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

## 