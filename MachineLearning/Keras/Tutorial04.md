# Advanced Function in Tensor

上一节，介绍了如何在Tensor上进行拆分和连接，这一节，介绍如何遍历Tensor中的每一个元素，并对其进行操作。

# Map in Tensor

若要对Tensor中对每一个变量，都执行一个相同的函数，使用tf.map_fn来进行。

```py
map_fn(fn, elems, dtype=None, parallel_iterations=10, back_prop=True, swap_memory=False, infer_shape=True, name=None)
```

这里，只介绍三个参数：

* fn：映射函数。
* elems：被fn调用的Tensor，其中的每一个元素都会被fn进行映射。
* dtype：elems的数据类型和fn的输出类型不一致时，此参数必须指定。

如下面代码：

```py
y = tf.map_fn(fn,x)
```

等价于公式:

$$y=f_n(x)$$

# Iteration in Tensor

将二维数组x看做国际象棋的棋盘，将其分为两类，一类是红色变量，一类是黑色变量。迭代二阶张量x中的每一个变量，使红色变量赋值为0，黑色变量不变。

首先，定义x：

```py
npx = np.array([[0, 1, 2，3], [1, 2, 3，4], [2, 3, 4，5], [3, 4, 5，6]])
x = K.variable(npx)
```

映射函数fn有一个固定输入i，它代表x中的每一个元素，fn需要一个Tensor作为返回值；fn可由lambda表达式定义；为了实现映射，我们需要一个自定义的映射方法fun：

```py
def fun(i,m,W,H):
    # 获取行标（第0维度的下标）
    p = i // W
    # 获取列标（第1维度的下标）
    q = i % W
    def f1(): return 0.0
    def f2(): return m[p,q]
    # K.tf.cond is the condition clause of TensorFlow
    return K.tf.cond(K.tf.equal((p+q)%2,0),f1,f2)
```

注意这里的判断，必须使用TensorFlow中的张量判断函数，因为在fun中，还没有得到具体的值，不能使用python中的条件语句。这种定义，是在计算图中定义了条件判断语句。

然后用map_fn，将操作fun应用到张量的每个元素上。注意，这里要迭代张量中的所有变量。

```py
x = K.map_fn(lambda i: fun(i, x, 4, 4), K.tf.range(0, 4 * 4, dtype="int32"), dtype="float32")
```
