[TOC]

# Java常用API

## 1 Array

### 1.1 asList

```java
List<Integer> list = Arrays.asList(16, 17, 18, 19, 20);
list.set(1, 99); 		// 更改元素可以
// list.add(21); 		// 添加元素不行。因为此时其底层表示的是数组，因此不能调整尺寸
```

### 1.2 binarySearch

```java
int[] nums = new int[]{1, 2, 3, 4, 5, 6, 7, 8};
System.out.println(Arrays.binarySearch(nums, 4));   // 3
```

### 1.3 equals

* 比较的是值

```java
int[] nums = new int[]{1, 2, 3, 4, 5, 6, 7, 8};
int[] nums2 = new int[]{1, 2, 3, 4, 5, 6, 7, 8};
System.out.println(Arrays.equals(nums, nums2));		// true
```

### 1.4 sort

* Arrays.sort(Object[] array, int from, int to)

* 对数组元素指定范围进行排序（排序范围是从元素下标为from,到下标为to-1的元素进行排序）

```java
// 排序自定义(按照二维数组的第一维排序， 第一维相同则按照第二维排序)
Arrays.sort(nums2, (a, b) -> a[0] == b[0] ? a[1] - b[1] : a[0] - b[0]);

// 数组部分排序
Arrays.sort(nums, startIndex, endIndex+1);

// 数组反向排序
Arrays.sort(points, Collections.reverseOrder());

// 字符串排序，默认先大写再小写，以下为忽略大小写排序
Arrays.sort(strArray, String.CASE_INSENSITIVE_ORDER);
```

### 1.5 fill

* Arrays.fill(Object[] array,Object object)

* 填充，取头不取尾

### 1.6 toString

```java
int[] nums = new int[]{1, 2, 3, 4, 5, 6, 7, 8};
System.out.println(Arrays.toString(nums));
// [1, 2, 3, 4, 5, 6, 7, 8]
```

### 1.7 deepToString

```java 
int[][] nums2 = new int[][]{{1, 2, 3}, {4, 5, 6}};
System.out.println(Arrays.deepToString(nums2));
// [[1, 2, 3], [4, 5, 6]]
```

### 1.8 copyOf

* 截取固定长度

```java
int[] nums = new int[]{1, 2, 3, 4, 5, 6, 7, 8};
int[] nums1 = Arrays.copyOf(nums, 4);
System.out.println(Arrays.toString(nums1));
// [1, 2, 3, 4]
```

### 1.9 copyOfRange

* 按范围截取，取头不取尾

```java
int[] nums2 = Arrays.copyOfRange(nums, 1, 4);
System.out.println(Arrays.toString(nums2));
// [2, 3, 4]
```

### 1.10 setAll

* 利用提供的公式，对每个元素进行运算

```java
int[] nums = new int[]{1, 2, 3, 4, 5, 6, 7, 8};
Arrays.setAll(nums, x -> x * x);
System.out.println(Arrays.toString(nums));
```



## 2 List

**toArray**

```java
public static void main(String[] args) {  
    List<String> list = new ArrayList<String>();  
    list.add("1");  
    list.add("2");  
    // toArray必须有参数，可以替换为new String[list.size()]
    String[] res = (String[])list.toArray(new String[0]); 
} 
```



## 3 Map

## 4 String

## 5 Queue

## 6 Deque

## 7 Set

## 8 PriorityQueue
