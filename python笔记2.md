# python笔记2

## 1. 迭代器和生成器

* 迭代器是一直可以记住遍历的位置的对象。
* 从集合的第一个元素开始访问，直到结束，**只能往前不能后退**。
* 两个基本方法：**iter()** 和 **next()**。

```python
list = [1,2,3,4]
a = iter(list)
>>> print (next(it))   # 输出迭代器的下一个元素
1
>>> print (next(it))
2

for x in a :
	print(x , end = " ")

```

### 1.1 创建迭代器

* 把一个类作为一个迭代器使用需要在类中实现两个方法 __iter__() 与 __next__() 。
* __iter__() 方法返回一个特殊的迭代器对象， 这个迭代器对象实现了 __next__() 方法并通过 StopIteration 异常标识迭代的完成。
* __next__() 方法会返回下一个迭代器对象。

```python
class MyNumbers:
  def __iter__(self):
    self.a = 1
    return self
 
  def __next__(self):
    x = self.a
    self.a += 1
    return x
 
myclass = MyNumbers()
myiter = iter(myclass)
```

### 1.2 StopIteration

* StopIteration 异常用于标识迭代的完成，防止出现无限循环的情况，在 __next__() 方法中我们可以设置在完成指定循环次数后触发 StopIteration 异常来结束迭代。

### 1.3 生成器

* 使用了**yield**的函数被称为生成器。
* 生成器是一个返回迭代器的函数，只能用于迭代操作，更简单点理解生成器就是一个迭代器。
* 在调用生成器运行的过程中，每次遇到 yield 时函数会暂停并保存当前所有的运行信息，返回 yield 的值, 并在下一次执行 next() 方法时从当前位置继续运行。

```python
import sys
 
def fibonacci(n): 			 # 生成器函数 - 斐波那契
    a, b, counter = 0, 1, 0
    while True:
        if (counter > n): 
            return
        yield a
        a, b = b, a + b
        counter += 1
f = fibonacci(10) 			# f 是一个迭代器，由生成器返回生成
 
while True:
    try:
        print (next(f), end=" ")
    except StopIteration:
        sys.exit()
```

