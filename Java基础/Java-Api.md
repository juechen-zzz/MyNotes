# Java常用API

## 1 Array

**asList**

```java
List<Integer> list = Arrays.asList(16, 17, 18, 19, 20);
list.set(1, 99); 		// 更改元素可以
// list.add(21); 		// 添加元素不行。因为此时其底层表示的是数组，因此不能调整尺寸
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

