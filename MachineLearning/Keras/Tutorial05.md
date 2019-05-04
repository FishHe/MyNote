# From Indices to Item

如果已知元素下标，要如何从一个Tensor中获取这些元素，并组成新的Tensor呢？将可行的方案归纳如下。

## tf.gather_nd

```py
for i in range(10):
  for j in range(10):
    indices.append([i,j])
x = tf.gather_nd(x,indices)
```

这种做法的最大弊端，在于要迭代产生Indices，使用python的for语句，耗时严重。

## tf.map_fn

```py
M,N = x.shape
xtype = x.dtype
x = tf.reshape(x,[-1])
x = tf.map_fn(lambda i:x[i],tf.range(0,M*N,dtype=tf.int32),dtype = xtype)
```

map_fn效率极低。
