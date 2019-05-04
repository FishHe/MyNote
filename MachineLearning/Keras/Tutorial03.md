# Partition and Stitch with Tensor

> **必须强调的是，map_fn函数效率极低，在能够用其它方法代替它时，应当避免使用**

这一节介绍如何利用gather_nd()或者map_fn()函数，对一个已存在Tensor中的局部，进行函数运算。

假设要对一个矩阵中的所有元素进行下面的计算：

$$a_{i,j} =a_{i,j} + a_{i-1,j} \text{ 其中 } i+j = 偶数$$

## Index

在进行张量算法以前，我们需要把要使用的值，重新组成大小相同的张量。首先要获取它们各自对应的下标：



```python
# a矩阵
npa = np.array([[0, 1, 2], [1, 2, 3], [2, 3, 4]])
a = K.constant(npa)
# a(i,j)所对应的下标
indices1 = []
# a(i-1,j-1)对应的下标
indices2 = []
for i in range(3):
    for j in range(3):
        if (i + j) % 2 == 0:
            if i < 1 or i > 1: continue
            if j < 1 or j > 1: continue
            indices1.append([i, j])
            indices2.append([i - 1, j])
```

## Partition

我们可以利用gather_nd()或者dynamic_partition()来获取张量中的某一部分：

```python
# i+j=偶数
a1 = K.tf.gather_nd(a,indices1)
# (i-1,j)
a2 = K.tf.gather_nd(a,indices2)
```

## Do the Function

现在我们来实现函数：

```python
# 求和
a_add = a1 + a2
```

## Recover the Structure

**a_add**已经不再具有二维结构，前面的过程相当于我们通过拆分张量实现了Function的并行化。现在我们要重组张量。我们不赘述网上流传广泛的合并方法，我们介绍dynamic_stitch()函数。

下面，我们将使用**棋盘法**拆分一个二维矩阵，然后将拆分后的张量合并，并恢复原有结构。

```python
# 3x3 的矩阵
npx = np.array([[0, 1, 2], [1, 2, 3], [2, 3, 4]])
# 拆分下标
indices1 = []
indices2 = []
# 合并下标
indices1_inv = []
indices2_inv = []
for i in range(3):
    for j in range(3):
        if (i + j) % 2 == 0:
            indices1.append([i, j])
            indices1_inv.append(3 * i + j)
        else:
            indices2.append([i, j])
            indices2_inv.append(3 * i + j)
x = K.constant(npx)
x1 = K.tf.gather_nd(x,indices1)
x2 = K.tf.gather_nd(x,indices2)

# 合并数组x1,x2
x = K.tf.dynamic_stitch([indices1_inv,indices2_inv],[x1,x2])
# 恢复结构
x = K.reshape(x,shape=(3,3))

sess = K.get_session()
value_float = sess.run(x)
print(value_float)
```
