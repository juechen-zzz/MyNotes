# 0 排序

```java
// 排序自定义(按照二维数组的第一维排序， 第一维相同则按照第二维排序)
Arrays.sort(intervals, new Comparator<int[]>(){
            public int compare(int[] a, int[] b) {
                return a[0] == b[0] ? a[1] - b[1] : a[0] - b[0];
            }
        });

// 数组部分排序
Arrays.sort(nums, startIndex, endIndex+1);
// ArrayList排序
Collections.sort(ans);
// 二进制
Integer.parseInt(a, 2)
```

# 1 数组

```java
// 最大最小值
Integer.MAX_VALUE
// 创建
int[] nums = new int[n];
int[] nums = new int[]{1, 2, 3};
```

# 2 字符串

```java
// 取单个字符
char c = s.charAt(index);
// 判定是否相同
s1.equals(s2);
// 转换成char[]数组
char[] d = s.toCharArray();
// 取子串	[start, end)
String s1 = s.substring(start, end);
// sb
StringBuilder s = new StringBuilder();
s.append(j);
s.reverse();
String s_end = s.toString();
```

# 3 HashMap

```java
// 创建
HashMap<Integer, Integer> dic = new HashMap<>();
// 输入输出
dic.put(key, val);
dic.get(key);
// 若要修改，则必须覆盖
dic.put(key, window.getOrDefault(key, 0) + 1);
```

# 4 ArrayList + LinkedList

```java
// 创建
List<List<Integer>> res1 = new ArrayList<>();
// 添加删除
res1.add(val);
res1.remove(index);
res2.addFirst(val);	// 在前面加
res2.add(val);		// 在后面加
res2.removeFirst();
res2.removeLast();
// 判定是否为空
res.isEmpty();
// 大小
res.size();
// 判定有值
res.contains(val);
```

# 5 Queue

```java
// 创建
Queue<TreeNode> queue = new LinkedList<>();
// 添加删除
queue.offer(root);
TreeNode node = queue.poll();
// 返回队头元素
TreeNode node = queue.peek();
// 判定是否为空
res.isEmpty();
// 大小
res.size();
```

# 6 Deque

```java
// 创建
Deque<Character> deque = new LinkedList<>();
// 添加删除
deque.offerFirst(n);
deque.offerLast(n);
deque.pollFirst();
deque.pollLast();
// 返回元素
deque.peekFirst()
deque.peekLast()
// 判定是否为空
res.isEmpty();
// 大小
res.size();
```

# 7 HashSet

```java
// 创建
Set<String> se = new HashSet<>();
// 添加删除
se.add(n);
se.remove(n);
// 包含
se.contains(n);
```

# 8 Stack

```java
// 创建
Stack<Integer> s = new Stack<>();
// 添加删除
s.push(n);
s.pop(n);
s.peek();
// 判定是否为空
s.isEmpty();
// 大小
s.size();
```

# 9 PriorityQueue

* 最小堆（优先级队列）

```java
// 创建
PriorityQueue<Integer> pq = new PriorityQueue<>();						// 最小堆
PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);		// 最大堆
PriorityQueue<int[]> pq = new PriorityQueue<>(new Comparator<int[]>() {
    public int compare(int[] pair1, int[] pair2) {
        return pair1[0] != pair2[0] ? pair2[0] - pair1[0] : pair1[1] - pair2[1];
    }
});
// 添加删除
pq.offer(n);
pq.poll();
pq.peek();
// 判定是否为空
s.isEmpty();
// 大小
s.size();
```

# 10 List

```java
List<Pair>[] edges = new List[stringNum];
for (int i = 0; i < stringNum; i++){edges[i] = new ArrayList<Pair>();}
```

