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

print(next(myiter))
print(next(myiter))
print(next(myiter))
print(next(myiter))
print(next(myiter))
```

### 1.2 StopIteration

* StopIteration 异常用于标识迭代的完成，防止出现无限循环的情况，在 __next__() 方法中我们可以设置在完成指定循环次数后触发 StopIteration 异常来结束迭代。

  ```python
  class MyNumbers:
    def __iter__(self):
      self.a = 1
      return self
   
    def __next__(self):
      if self.a <= 20:
        x = self.a
        self.a += 1
        return x
      else:
        raise StopIteration
   
  myclass = MyNumbers()
  myiter = iter(myclass)
   
  for x in myiter:
    print(x)
  ```

### 1.3 生成器

* 使用了**yield**的函数被称为生成器。
* 生成器是**一个返回迭代器的函数**，只能用于迭代操作，更简单点理解生成器就是一个迭代器。
* 在调用生成器运行的过程中，每次遇到 yield 时函数会暂停并保存当前所有的运行信息，**返回 yield 的值**, 并在下一次执行 next() 方法时从当前位置继续运行。

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

* 一个函数 f，f 返回一个 list，这个 list 是动态计算出来的（不管是数学上的计算还是逻辑上的读取格式化），并且这个 list 会很大（无论是固定很大还是随着输入参数的增大而增大），这个时候，我们希望每次调用这个函数并使用迭代器进行循环的时候一个一个的得到每个 list 元素而不是直接得到一个完整的 list 来节省内存，这个时候 yield 就很有用。



## 2. 函数

* 函数代码以**def**关键词开头，后接函数标识符名称和圆括号**（）**。
* 任何传入参数和自变量必须放在圆括号中间，圆括号之间可以用于定义参数。
* 函数的第一行语句可以选择性的使用文档字符串-用于存放函数说明。
* 函数内容以冒号开始，并且缩进。
* **return [表达式]** 结束函数，选择性的返回一个值给调用方，不带表达式的**return** 相当于返回**None**。

```python
# 计算面积

def area(width,height):
    return width * height

def print_wecome(name):
    print('Welcome',name)
    
print_welcome('My name')
w = 4
h = 5
print("width = " ,w,"height = ",h,"area = ",area(w.h))
```

### 2.1 参数传递

* 在python 中，类型属于对象，变量时没有类型的。

```python
a = [1,2,3]

a = "name"
```

​	**[1,2,3]** 是 List 类型，**"name"** 是 String 类型，而变量 a 是没有类型，她仅仅是一个对象的引用（一个指针），可以是指向 List 类型对象，也可以是指向 String 类型对象。

#### 2.1.1 可更改（mutable）和不可更改（immutable）对象

​	在python 中，**strings**,**tuples**和**numbers**是不可更改对象，**list**,**dict**是可更改的。

* **不可变类型：**变量赋值 **a=5** 后再赋值 **a=10**，这里实际是新生成一个 int 值对象 10，再让 a 指向它，而 5 被丢弃，不是改变a的值，相当于新生成了a。

* **可变类型：**变量赋值 **la=[1,2,3,4]** 后再赋值 **la[2]=5** 则是将 list la 的第三个元素值更改，本身la没有动，只是其内部的一部分值被修改了。

* 函数的参数传递：

  * **不可变类型：**类似 c++ 的值传递，如 整数、字符串、元组。如fun（a），传递的只是a的值，没有影响a对象本身。比如在 fun（a）内部修改 a 的值，只是修改另一个复制的对象，不会影响 a 本身。

  * **可变类型：**类似 c++ 的引用传递，如 列表，字典。如 fun（la），则是将 la 真正的传过去，修改后fun外部的la也会受影响。

    ```python
    # 不可变函数说明
    def ChangeInt( a ):
        a = 10
     
    b = 2
    ChangeInt(b)
    print( b ) # 结果是 2
    
    # 可写函数说明
    def changeme( mylist ):
       "修改传入的列表"
       mylist.append([1,2,3,4])
       print ("函数内取值: ", mylist)
       return
     
    # 调用changeme函数
    mylist = [10,20,30]
    changeme( mylist )
    print ("函数外取值: ", mylist)
    ```

#### 2.1.2 参数（必须参数，关键字参数，默认参数，不定长参数）

* **必需参数**须以正确的顺序传入函数。调用时的数量必须和声明时的一样。

* **关键字参数**和函数调用关系紧密，函数调用使用关键字参数来确定传入的参数值。使用关键字参数**允许**函数调用时参数的顺序与声明时不一致，因为 Python 解释器能够用参数名匹配参数值。

  ```python
  #可写函数说明
  def printinfo( name, age ):
     "打印任何传入的字符串"
     print ("名字: ", name)
     print ("年龄: ", age)
     return
   
  #调用printinfo函数
  printinfo( age=50, name="runoob" )
  ```

* 调用函数时，如果没有传递参数，则会使用默认参数。以下实例中如果没有传入 age 参数，则使用默认值：

* 默认参数必须放在最后面

  ```python
  #可写函数说明
  def printinfo( name, age = 35 ):
     "打印任何传入的字符串"
     print ("名字: ", name)
     print ("年龄: ", age)
     return
   
  #调用printinfo函数
  printinfo( age=50, name="runoob" )
  print ("------------------------")
  printinfo( name="runoob" )
  ```

* ```python
  # 可写函数说明
  def printinfo( arg1, *vartuple ):
     "打印任何传入的参数"
     print ("输出: ")
     print (arg1)
     print (vartuple)
   
  # 调用printinfo 函数
  printinfo( 70, 60, 50 )		#输出(60, 50)
  
  # 可写函数说明
  def printinfo( arg1, **vardict ):
     "打印任何传入的参数"
     print ("输出: ")
     print (arg1)
     print (vardict)
   
  # 调用printinfo 函数
  printinfo(1, a=2,b=3)		#输出{'a': 2, 'b': 3}
  ```

* 声明函数时，参数中星号 ***** 可以单独出现，如果单独出现星号 ***** 后的参数必须用关键字传入。

  ```python
  def f(a,b,*,c):
      return a+b+c
  
  >>> f(1,2,3)   # 报错
  >>> f(1,2,c=3) # 正常
  6
  ```

#### 2.1.3 匿名函数

**python**使用**lambda**来创建匿名函数，即不使用def这种形式定义函数

* **lambda**主体是一个**表达式**，而不是**代码块**。仅仅能做lambda表达式中封装有限的逻辑。
* lambda函数拥有自己的命名空间，且不能访问自己参数列表之外灬全局命名空间里的参数。
* 虽然lambda函数看起来只能写一行，却不等同于C或C++的内联函数，后者的目的是调用小函数时不占用栈内存从而增加运行效率。

##### 语法：

```python
# 可写函数说明
sum = lambda arg1, arg2: arg1 + arg2
 
# 调用sum函数
print ("相加后的值为 : ", sum( 10, 20 ))
print ("相加后的值为 : ", sum( 20, 20 ))
```

#### 2.1.4 Return语句

* **return [表达式]** 语句用于退出函数，选择性地向调用方返回一个表达式。不带参数值的return语句返回None。

### 2.2 变量作用域

- L （Local） 局部作用域
- E （Enclosing） 闭包函数外的函数中
- G （Global） 全局作用域
- B （Built-in） 内置作用域（内置函数所在模块的范围）

以 L –> E –> G –>B 的规则查找，即：在局部找不到，便会去局部外的局部找（例如闭包），再找不到就会去全局找，再者去内置中找。

```python
g_count = 0  # 全局作用域
def outer():
    o_count = 1  # 闭包函数外的函数中
    def inner():
        i_count = 2  # 局部作用域
```

​	Python 中只有模块（module），类（class）以及函数（def、lambda）才会引入新的作用域，其它的代码块（如 if/elif/else/、try/except、for/while等）是不会引入新的作用域的，也就是说这些语句内定义的变量，外部也可以访问

#### 2.2.1 global 和 nonlocal关键字

```python
num = 1
def fun1():
    global num  # 需要使用 global 关键字声明
    print(num) 
    num = 123
    print(num)
fun1()
print(num)		#输出123
```

如果要修改嵌套作用域（enclosing 作用域，外层非全局作用域）中的变量则需要 nonlocal 关键字了

```python
def outer():
    num = 10
    def inner():
        nonlocal num   # nonlocal关键字声明
        num = 100
        print(num)
    inner()
    print(num)
    
>>>outer()
```

