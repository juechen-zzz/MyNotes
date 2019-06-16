# Tensorflow笔记

* 常量创建方法

```python
a = tf.constant('hello', dtype=tf.string)
b = tf.constant(1.0, dtype=float32)
```

* 变量创建方法

```python
a = tf.Variable(2, tf.int32)
```

* 常用数据类型

| 数据类型  |      说明      |   数据类型    |    说明    |
| :-------: | :------------: | :-----------: | :--------: |
|  tf.int   |    8位整数     |   tf.string   |   字符串   |
| tf.int16  |    16位整数    |    tf.bool    |   布尔型   |
| tf.int32  |    32位整数    | tf.complex64  |  64位复数  |
| tf.int64  |    64位整数    | tf.complex128 | 128位复数  |
| tf.uint8  | 8位无符号整数  |  tf.float16   | 16位浮点数 |
| tf.uint16 | 16位无符号整数 |  tf.float32   | 32位浮点数 |

* 常用函数

|            操作             |            描述            |
| :-------------------------: | :------------------------: |
|   tf.add(x, y, name=None)   |            求和            |
|   tf.sub(x, y, name=None)   |            减法            |
|   tf.mul(x, y, name=None)   |            乘法            |
|   tf.div(x, y, name=None)   |            除法            |
|   tf.mod(x, y, name=None)   |            取模            |
|    tf.abs(x, name=None)     |           绝对值           |
|    tf.neg(x, name=None)     |            取负            |
|    tf.sign(x, name=None)    | 返回符号y = sign(x),-1,0,1 |
|    tf.inv(x, name=None)     |        取反(二进制)        |
|   tf.square(x, name=None)   |            平方            |
|    tf.sqrt(x, name=None)    |            根号            |
|    tf.exp(x, name=None)     |          e的次方           |
|    tf.log(x, name=None)     |          计算log           |
| tf.maximum(x, y, name=None) |   返回最大值(x>y ? x:y)    |
| tf.minimum(x, y, name=None) |   返回最小值(x<y ? x:y)    |

