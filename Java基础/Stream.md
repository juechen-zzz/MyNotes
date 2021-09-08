# Stream

> JDK1.8的新特性，结合Lambda表达式可以提高开发效率
>
> 1. 用于对集合**迭代**的增强处理。
> 2. 可以对集合数组进行更高效的聚合操作（分组、过滤、排序、追加）等。
> 3. 解决了传统开发过程中JDK对集合API不足的问题。

**示例**

```java
// 返回红色苹果的集合 List<Apple>
return appleStore.stream().filter(a -> "red".equals(a.getColor())).collect(Collectors.toList());

// 获取所有红色苹果的总重量
appleStore.stream()
	.filter(w -> "red".equals(w.getColor()))
    .mapToInt(Apple::getWeight).sum();
```

