# Tensorflow笔记

## 常量创建方法

```python
a = tf.constant('hello', dtype=tf.string)
b = tf.constant(1.0, dtype=float32)
```



## 变量创建方法

```python
a = tf.Variable(2, tf.int32)
```



## 常用数据类型

| 数据类型  |      说明      |   数据类型    |    说明    |
| :-------: | :------------: | :-----------: | :--------: |
|  tf.int   |    8位整数     |   tf.string   |   字符串   |
| tf.int16  |    16位整数    |    tf.bool    |   布尔型   |
| tf.int32  |    32位整数    | tf.complex64  |  64位复数  |
| tf.int64  |    64位整数    | tf.complex128 | 128位复数  |
| tf.uint8  | 8位无符号整数  |  tf.float16   | 16位浮点数 |
| tf.uint16 | 16位无符号整数 |  tf.float32   | 32位浮点数 |



## 常用函数

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



## 矩阵

### 创建矩阵

```python
tf.constant([1,2,3], shape=[2,3])
# 生成一个两行三列的矩阵，第二行的数值由输入的数值进行补全
```

### 创建随机矩阵

```python
tf.random_normal(shape, mean=0.0, stddev=1.0, dtype=tf.float32, seed=None, name=None)
# 正态分布随机数，均值为mean，标准差为stddev

tf.truncated_normal(shape, mean=0.0, stddev=1.0, dtype=tf.float32, seed=None, name=None)
# 截断正态分布随机数，均值mean，标准差stddev，范围在【mean - 2 * stddev, mean + 2 * stddev】

tf.random_uniform(shape, minval=0, maxval=None, dtype=tf.float32, seed=None, name=None)
# 均匀分布随机数，范围在【minval, maxval】
```

### 查看维数

```python
tf.shape(Tensor)
```

* 若shape=[-1]，表示将tensor展开成一个list
* 若shape=[a,b,c,…]，常规语法（abc均大于0）
* 若shape=[a,-1,c,…]，表示tf会根据tensor的原尺寸自动计算b的值

### 矩阵函数

| 操作                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| tf.diag(diagonal, name=None)                                 | 返回一个给定对角值的对角tensor<br /># ‘diagonal’ is [1, 2, 3, 4]<br />tf.diag(diagonal)==><br />[[1, 0, 0, 0]<br />[0, 2, 0, 0]<br />[0, 0, 3, 0]<br />[0, 0, 0, 4]]<br /> |
| tf.diag_part(input, name=None)                               | 功能与上面相反                                               |
| tf.trace(x, name=None)                                       | 求一个2维tensor足迹，即对角值diagonal之和                    |
| tf.transpose(a, perm=None, name=‘transpose’)                 | 调换tensor的维度顺序<br />按照列表perm的维度排列调换tensor顺序<br />如定义，则perm为(n-1…0)<br /># ‘x’ is [[1,2,3],[4,5,6]]<br />tf.transpose(x) ==> [[1 4], [2 5], [3 6]]<br />#Equivalently<br />tf.transpose(x, perm=[1,0]) ==> [[1 4], [2 5], [3 6]] |
| tf.matmul(a, b, <br />transpose_a=False, transpose_b=False, <br />a_is_sparse=False, b_is_sparse=False, name=None) | 矩阵相乘                                                     |
| tf.matrix_determinant(input, name=None)                      | 返回方阵的行列式                                             |
| tf.matrix_inverse(input, adjoint=None, name=None)            | 求方阵的逆矩阵，adjoint=True时，<br />计算输入共轭矩阵的逆矩阵 |
| tf.cholesky(input, name=None)                                | 对输入方阵cholesky分解，<br />即把一个对称正定的矩阵表示成一个下三角<br />矩阵**L**和其转置的乘积的分解A=LL^T |
| tf.matrix_solve(matrix, rhs, adjoint=None, name=None)        | 求解，matrix为方阵，shape=[M,M]<br />rhs的shape为[M, K]<br />output为[M, K] |

