[TOC]

# 0 排序

```java
// 排序自定义(按照二维数组的第一维排序， 第一维相同则按照第二维排序)
Arrays.sort(nums2, (a, b) -> a[0] == b[0] ? a[1] - b[1] : a[0] - b[0]);

// 数组部分排序
Arrays.sort(nums, startIndex, endIndex+1);
Arrays.sort(points, Collections.reverseOrder());

// ArrayList排序 反转
Collections.sort(ans);
Collections.reverse(tmp);

//把其他进制字符串key，转成10进制整数
Integer.parseInt(a, 2)
Integer.parseInt(a, 8)
Integer.parseInt(a, 16)
    
//把10进制keys转成其余进制
Integer.toBinaryString(num)
Integer.toHexString(num);
```

# 1 数组

```java
// 最大最小值
Integer.MAX_VALUE
// 创建
int[] nums = new int[]{1, 2, 3};
// 复制
int[] tmp = new int[nums.length + 2];
System.arraycopy(nums, 0, tmp, 1, nums.length);
// 克隆
int[] nums2 = nums.clone();
// 求和
int sum = Arrays.stream(nums).sum();
```

# 2 String

```java
// 转换成char[]数组
char[] d = s.toCharArray();
String ans = String.valueOf(d);
// 取子串	[start, end)
String s1 = s.substring(start, end);
// 计算一个字符出现的次数
ans.chars().filter(c -> c == ':').count() == 7
// sb
StringBuilder s = new StringBuilder();
s.append(j);
s.deleteCharAt(i);
s.reverse();
String s_end = s.toString();
// 判定是否为数字或字母 
Character.isLetterOrDigit(s.charAt(left))
// 转小写
Character.toLowerCase(s.charAt(left))
// 分割，正则匹配连续的空白字符作为分隔符分割
List<String> wordList = Arrays.asList(s.split("\\s+"));
// 除去开头和末尾的空白字符
s = s.trim();
// 拼接
String.join(" ", wordList);
// 比较两个字符型是否有重复字母的方法（位运算）
int nums1 = 0;
for (int i = 0; i < ransomNote.length(); i++) {
    int index = ransomNote.charAt(i) - 'a';
    nums1 = nums1 | (1 << index);
}

int nums2 = 0;
for (int i = 0; i < magazine.length(); i++) {
    int index = magazine.charAt(i) - 'a';
    nums2 = nums2 | (1 << index);
}

return (nums1 & nums2) != 0;
```

# 3 HashMap

```java
// 遍历 1
for (Map.Entry<Integer, Integer> entry : countMap.entrySet()) {
	int num = entry.getKey(), count = entry.getValue();
}
// 遍历 2
for (int node : graph.keySet()) 
for (int num : dist.values())
// 排序1 key 降序
private static void sortMapByKey(Map<Integer, String> map) {
    Set set = map.keySet();
    Object[] arr = set.toArray();
    Arrays.sort(arr, Collections.reverseOrder());
    for(Object key : arr){
        System.out.println(key  + "--" + map.get(key));
    }
}
// 排序2 value 降序
private static void sortMapByValue1(Map<String, Integer> map) {
    List<Map.Entry<String, Integer>> curList = new ArrayList<>(map.entrySet());
    curList.sort((o1, o2) -> o2.getValue().compareTo(o1.getValue()));

    for (int i = 0; i < curList.size(); i++) {
        System.out.println(curList.get(i).getKey() + "--" + curList.get(i).getValue());
    }
}
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
res2removeLast();
ans.add(Arrays.asList(a, b));
// 判定是否为空
res.isEmpty();
// 大小
res.size();
// 判定有值
res.contains(val);
// LinkedList迭代器
Iterator<Pair> iterator = data[h].iterator();
while (iterator.hasNext()) {
    Pair pair = iterator.next();
    if (pair.getKey() == key) {
        pair.setValue(value);
        return;
    }
}
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
// 取值
List<String> list=new ArrayList<>(set);
// 获取baiset集合数据
for (Iterator iterator = set.iterator(); iterator.hasNext();) {
    String string = (String) iterator.next();
    System.out.println(string);
}
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
PriorityQueue<int[]> pq = new PriorityQueue<>((pair1, pair2) -> pair1[0] != pair2[0] ? pair2[0] - pair1[0] : pair1[1] - pair2[1]);
// 添加删除
pq.add(n);
pq.offer(n);
pq.poll();
pq.peek();
// 判定是否为空
s.isEmpty();
// 大小
s.size();
```



