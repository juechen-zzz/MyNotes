# Tensorflow2.0

## 精度转换

```python
a = tf.constant(np.pi, dtype=tf.float16)
b = tf.cast(a, tf.double)
```

* tf.double = tf.float64
* 常用的精度类型有 tf.int16, tf.int32, tf.int64, tf.float16, tf.float32, tf.float64



## 创建张量

```python
# 全0和全1
a = tf.ones([2, 2])
b = tf.zeros([2. 2])
# 创建和一个张量形状一样的张量
c = tf.zeros_like(a)
d = tf.ones_like(a)
# 自定义数值
e = tf.fill([2, 2], 99)
# 创建形状为 shape，均值为 mean，标准差为 stddev的标准正态分布
f = tf.random.normal(shape, mean=0.0, stddev=1.0) 
# 创 [𝑚𝑖𝑛𝑣𝑎𝑙, 𝑚𝑎𝑥𝑣𝑎𝑙]区间的均匀分布的张量
g = tf.random.uniform(shape, minval=0, maxval=None, dtype=tf.float32)
```



## 创建序列

```Python
# 创建[0, 𝑙𝑖𝑚𝑖𝑡)之间，步长为 delta 的 列，不包含 limit 本身
# tf.range(start, limit, delta=1)可以创建[𝑠𝑡𝑎𝑟𝑡, 𝑙𝑖𝑚𝑖𝑡)，步长为 delta 的序列，不包含 limit 本身
tf.range(limit, delta=1)
```

