# python学习笔记



## 1. 中文编码

​	Python中默认的编码格式是 ASCII 格式，在没修改编码格式时无法正确打印汉字，所以在读取中文时会报错。

```python
# -*- coding: UTF-8 -*-
```

​	**注意** ：python3 源码文件默认使用utf-8编码，可以正常指定中文。



## 2. 基础语法

### 2.1. 脚本式编程

#### 2.1.1. 运行脚本

​	通过脚本参数调用解释器开始执行脚本，直到脚本执行完毕。

```python
$ python test.py
```

#### 2.1.2. 标识符

* 在python中，标识符由**字母**、**数字**、**下划线**组成。
* 可以包括**英文**、**数字**、以及**下划线**，但是，<u>**不能由数字开头**</u>。
* 标识符区分大小写。

 -	**单下划线**开头代表不能直接访问的类属性，需通过类提供的接口进行访问。
 -	**双下划线**代表类的私有成员，以双下划线开头和结尾的 **__foo__** 代表 Python 里特殊方法专用的标识。

#### 2.1.3. 引号

* 三引号可以由多行组成，编写多行文本的快捷语法，常用于文档字符串，在文件的特定地点，被当做注释。

#### 2.1.4. 注释

* 单行注释时，以**#**开头。
* 多行注释使用三个单引号(''')或三个双引号(""")。

```python
'''
这是多行注释，使用单引号。
这是多行注释，使用单引号。
这是多行注释，使用单引号。
'''
```

#### 2.1.5.print输出

* print输出默认是换行的，如果不需要换行则在变量末尾加上**，**（逗号）。

```python
#换行输出
print(x)

#不换行输出
print(x,)			#python2
print(x,end=" ")	#python3
```



### 2.2. 数字类型

* **int**：表示为长整型，没有python2中的Long。
* **bool**：布尔型，true。
* **float**：浮点数，如1.23, 3E-2。
* **complex**：复数（1+2j）。



### 2.3. 字符串（String）

* 使用三引号('''或""")可以指定一个多行字符串。

* 反斜杠\可以用来转义，使用r可以让反斜杠不发生转义。如：

  ```python
   r"this is a line with \n"
  ```

   则**\n**会显示，并不是换行。

* Python 中的字符串有两种索引方式，从左往右以 0 开始，从右往左以 -1 开始。

* 字符串的截取的语法格式如下：**变量[头下标:尾下标:步长]**

```python
str='Runoob'
 
print(str)                 # 输出字符串
print(str[0:-1])           # 输出第一个到倒数第二个的所有字符
print(str[0])              # 输出字符串第一个字符
print(str[2:5])            # 输出从第三个开始到第五个的字符
print(str[2:])             # 输出从第三个开始的后的所有字符
print(str * 2)             # 输出字符串两次
print(str + '你好')        # 连接字符串
 
print('hello\nrunoob')      # 使用反斜杠(\)+n转义特殊字符
print(r'hello\nrunoob')     # 在字符串前面添加一个 r，表示原始字符串，不会发生转义
```



### 2.4. 模块导入

* 将整个模块(somemodule)导入，格式为： **import somemodule**
* 从某个模块中导入某个函数,格式为： **from somemodule import somefunction**
* 将某个模块中的全部函数导入，格式为： **from somemodule import \***





## 3. 基本数据类型

### 3.1. 变量声明、赋值

* Python 中的变量不需要声明。每个变量在使用前都必须赋值，变量赋值以后该变量才会被创建。

```python
counter = 100          # 整型变量
miles   = 1000.0       # 浮点型变量
name    = "runoob"     # 字符串
 
print (counter)
print (miles)
print (name)
```

* 多变量赋值

```python
a=b=c=1
a,b,c = 1, 2,"runoob"
```



### 3.2. 标准数据类型

* **不可变数据**：Number , String , Tuple 。
* **可变数据**：List , Dictionary , Set 。

#### 3.2.1. 数值运算

- 数值的除法包含两个运算符：**/** 返回一个浮点数，**//** 返回一个整数。
- 在混合计算时，Python会把整型转换成为浮点数。

#### 3.2.2. Number（数字）

* Python3 支持 **int、float、bool、complex（复数）**。
* 在Python 3里，只有一种整数类型 int，表示为长整型，没有 python2 中的 Long。

```python
>>> a, b, c, d = 20, 5.5, True, 4+3j
>>> print(type(a), type(b), type(c), type(d))
<class 'int'> <class 'float'> <class 'bool'> <class 'complex'>

>>>a = 111
>>> isinstance(a, int)
True
>>>
```

isinstance 和 type 的区别在于：

​	(1)   type()不会认为子类是一种父类类型。

​	(2)   isinstance()会认为子类是一种父类类型。

#### 3.2.3. String（字符串）

* Python中的字符串用单引号 **'** 或双引号 **"** 括起来，同时使用反斜杠 **\** 转义特殊字符。

* 字符串的截取的语法格式如下：（索引值以 **0** 为开始值，**-1** 为从末尾的开始位置。）

  ```python
  变量[头下标:尾下标]
  ```

* 加号 **+** 是字符串的连接符， 星号 ***** 表示复制当前字符串，紧跟的数字为复制的次数。实例如下：

  ```python
  str = 'Runoob'
   
  print (str)          # 输出字符串
  print (str[0:-1])    # 输出第一个到倒数第二个的所有字符
  print (str[0])       # 输出字符串第一个字符
  print (str[2:5])     # 输出从第三个开始到第五个的字符
  print (str[2:])      # 输出从第三个开始的后的所有字符
  print (str * 2)      # 输出字符串两次
  print (str + "TEST") # 连接字符串
  ```

* Python 使用反斜杠(\)转义特殊字符，如果你不想让反斜杠发生转义，可以在字符串前面添加一个 r，表示原始字符串：

  ```python
  >>> print('Ru\noob')
  Ru
  oob
  >>> print(r'Ru\noob')
  Ru\noob
  ```

* **注意，Python 没有单独的字符类型，一个字符就是长度为1的字符串。**

#### 3.2.4. List（列表）

* 列表中元素的类型可以不相同，支持 **数字** 、**字符串**甚至 **列表**。

* 列表截取的语法格式如下：

  ```python
  变量[头下标:尾下标]
  ```

  ![1552740757828](C:\Users\nihaopeng\AppData\Roaming\Typora\typora-user-images\1552740757828.png)

  加号 **+** 是列表连接运算符，星号 ***** 是重复操作。如下实例：

  ```python
  list = [ 'abcd', 786 , 2.23, 'runoob', 70.2 ]
  tinylist = [123, 'runoob']
   
  print (list)            # 输出完整列表
  print (list[0])         # 输出列表第一个元素
  print (list[1:3])       # 从第二个开始输出到第三个元素
  print (list[2:])        # 输出从第三个元素开始的所有元素
  print (tinylist * 2)    # 输出两次列表
  print (list + tinylist) # 连接列表
  ```

* 列表中的元素可以改变：

  ```python
  >>>a = [1, 2, 3, 4, 5, 6]
  >>> a[0] = 9
  >>> a[2:5] = [13, 14, 15]
  >>> a
  [9, 2, 13, 14, 15, 6]
  >>> a[2:5] = []  		 # 将对应的元素值设置为 [] 
  >>> a
  [9, 2, 6]
  ```

* Python 列表截取可以接收第三个参数，参数作用是截取的步长。

  ![1552741738435](C:\Users\nihaopeng\AppData\Roaming\Typora\typora-user-images\1552741738435.png)

#### 3.2.5. Tuple（元组）

* 元组写在小括号 **()** 里，元素之间用逗号隔开。

  ```python
  tuple = ( 'abcd', 786 , 2.23, 'runoob', 70.2  )
  tinytuple = (123, 'runoob')
   
  print (tuple)             # 输出完整元组
  print (tuple[0])          # 输出元组的第一个元素
  print (tuple[1:3])        # 输出从第二个元素开始到第三个元素
  print (tuple[2:])         # 输出从第三个元素开始的所有元素
  print (tinytuple * 2)     # 输出两次元组
  print (tuple + tinytuple) # 连接元组
  ```

* 元组与字符串类似，可以被索引且下标索引从0开始，-1 为从末尾开始的位置。也可以进行截取。

* 虽然tuple的元素不可改变，但它可以包含可变的对象，比如list列表。

* 构造包含 0 个或 1 个元素的元组比较特殊，所以有一些额外的语法规则：

  ```python
  tup1 = ()    # 空元组
  tup2 = (20,) # 一个元素，需要在元素后添加逗号
  ```

#### 3.2.6. Set（集合）

* 可以使用大括号 **{ }** 或者 **set()** 函数创建集合。

  创建一个空集合必须用 **set()** 而不是 **{ }**，因为 **{ }** 是用来创建一个空字典。

  ```
  parame = {value01,value02,...}
  或者
  set(value)
  ```

  ```python
  student = {'Tom', 'Jim', 'Mary', 'Tom', 'Jack', 'Rose'}
   
  print(student)   # 输出集合，重复的元素被自动去掉
   
  # 成员测试
  if 'Rose' in student :
      print('Rose 在集合中')
  else :
      print('Rose 不在集合中')
   
  # set可以进行集合运算
  a = set('abracadabra')
  b = set('alacazam')
   
  print(a)
  print(a - b)     # a 和 b 的差集
  print(a | b)     # a 和 b 的并集
  print(a & b)     # a 和 b 的交集
  print(a ^ b)     # a 和 b 中不同时存在的元素
  ```

#### 3.2.7. Dictionary（字典）

* 字典是一种映射类型，字典用 **{ }** 标识，它是一个无序的 **键(key) : 值(value)** 的集合。

* **键**必须使用**不可变类型**，同一个字典中，键必须是唯一的。

  ```python
  dict = {}
  dict['one'] = "1 - 菜鸟教程"
  dict[2]     = "2 - 菜鸟工具"
   
  tinydict = {'name': 'runoob','code':1, 'site': 'www.runoob.com'}
   
   
  print (dict['one'])       # 输出键为 'one' 的值
  print (dict[2])           # 输出键为 2 的值
  print (tinydict)          # 输出完整的字典
  print (tinydict.keys())   # 输出所有键
  print (tinydict.values()) # 输出所有值
  ```

* 构造函数 dict() 可以直接从键值对序列中构建字典如下：

  ```python
  >>>dict([('Runoob', 1), ('Google', 2), ('Taobao', 3)])
  {'Taobao': 3, 'Runoob': 1, 'Google': 2}
   
  >>> {x: x**2 for x in (2, 4, 6)}
  {2: 4, 4: 16, 6: 36}
   
  >>> dict(Runoob=1, Google=2, Taobao=3)
  {'Runoob': 1, 'Google': 2, 'Taobao': 3}
  ```



### 3.3. 数据类型转换

​	![1552746683944](C:\Users\nihaopeng\AppData\Roaming\Typora\typora-user-images\1552746683944.png)



## 4. 解释器、注释

* Python 的编程模式分为两种：**交互式**，**脚本式**。

* 交互式编程，需要我们打开 cmd 窗口（命令提示符窗口），在窗口中键入`python`,回车，这样就进入了交互式编程。

* 脚本式编程，就是我们先把 python 语句写好，保存在后缀为 .py 的文件里，然后从外部调用这个文件。它也可以使用 cmd 窗口进行调用，**与交互式编程不同的是，不要在cmd窗口内输入python加回车来进入交互模式**

  ```python
  """
  这是多行注释，用三个双引号
  这是多行注释，用三个双引号 
  这是多行注释，用三个双引号
  """
  print("Hello, World!") 
  ```



## 5. 运算符

### 5.1. 算术运算符

```python
a = b ** n      #b的n次方
c = b // n      #整除，向下取整
```

### 5.2. 比较运算符

* 返回的是**布尔值**

![1552813071640](C:\Users\nihaopeng\AppData\Roaming\Typora\typora-user-images\1552813071640.png)

### 5.3. 赋值运算符

![1552813130891](C:\Users\nihaopeng\AppData\Roaming\Typora\typora-user-images\1552813130891.png)

### 5.3. 位运算符

* 将数字看做二进制进行计算，假设a = 60 ,b = 13。

![1552813224106](C:\Users\nihaopeng\AppData\Roaming\Typora\typora-user-images\1552813224106.png)

### 5.4. 逻辑运算符

![1552813442862](C:\Users\nihaopeng\AppData\Roaming\Typora\typora-user-images\1552813442862.png)

### 5.5. 成员运算符

* Python还支持成员运算符，测试实例中包含了一系列的成员，包括**字符串，列表或元组**。

![1552813530922](C:\Users\nihaopeng\AppData\Roaming\Typora\typora-user-images\1552813530922.png)

### 5.6. 身份运算符

* 身份运算符用于比较两个对象的**存储单元**

![1552813582139](C:\Users\nihaopeng\AppData\Roaming\Typora\typora-user-images\1552813582139.png)

### 5.7. 运算符优先级

![1552814481641](C:\Users\nihaopeng\AppData\Roaming\Typora\typora-user-images\1552814481641.png)

