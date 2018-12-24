# Keras Backend

Keras是一个深度学习库的高层封装，在研究时，经常涉及一些底层操作，为了在使用Keras的同时，进行底层操作，需要引入**keras.backend**。

下面介绍如何在Keras流程中，使用argmax来提取张量中最大元素的下标。

# Lambda

Lambda表达式是用来声明**匿名函数**的语法糖。但是为了方便描述，我们暂时给它一个变量：

```Python
add = lambda x, y : x + y
z = add(1,2)
```

按照c语言的行为来理解，上面的Lambda表达式相当于返回了一个函数指针。其中，x和y是参数，冒号后面的是返回值。然后通过在指针后添加括号和参数来调用这个函数。

# Lambda Layer

在只有计算，没有参数的层中，我们可以简单调用**Lambda Layer**来实现。下面定义最大化参数层，它返回给定维度上具有最大值的下标。**Lambda Layer**是执行自定义函数、与TensorFlow建立连接的标准层。

如给出2维张量：
$$\begin{bmatrix}2&1&0\\
3&2&1\\
4&3&2\end{bmatrix}$$

对axis = 1求argmax，就是在每列(axis = 1)中，找出最大元素，返回除列以外的下标（行标）；返回值为：[2,2,2]。

对axis = 2求argmax，就是在每行(axis = 2)中，找出最大元素，返回除行以外的下标（列标）；返回值为：[0,0,0]。

下面是如何定义和使用这个层：

```Python
npx = np.array([[2, 3, 4],[1, 2, 3],[0, 1, 2]])
npy = np.array([[2, 3, 4],[1, 2, 3],[0, 1, 2]])

inputx = keras.Input(shape=(None,None))
inputy = keras.Input(shape=(None,None))

outputz = keras.layers.add([inputx, inputy])

# axis = 1 is the inner dim
# axis = 2 is the outer dim
# the numpy array's index goes from outer to inner arr[outer]...[inner]
outputz = keras.layers.Lambda(lambda x : keras.backend.argmax(x,1))(outputz)

model = keras.Model(inputs=[inputx, inputy], outputs=[outputz])

x = model.predict([[npx], [npy]])

print(x)
```

在Lambda层中，可以引入额外的参数，用arguments传递：

```Python
outputz = keras.layers.Lambda(lambda x,test1: keras.backend.argmax(x, 0+test1),arguments={"test1":1})(outputz)
```

## From TensorFlow to Keras Tensor

对任何keras模型使用Backend内的函数后，都会将其变为Tensor，例如：

```Python
model = keras.Model(inputs=[inputx, inputy], outputs=[outputz])

x = model([inputx, inputy])
# t is a tensor now
t = K.argmax(x,axis=1)
```

如何将tensor重新定义到模型内的？t此时成为TensorFlow的张量，为了将它重新封装成**Keras Tensor**，我们要借助Lambda层：

x是Lambda层的输入，t是其输出，我们可以这样定义：

```Python
model = keras.layers.Lambda(lambda _ : t)(x)
```

_表示Lambda表达式不需要调用参数，但t的内部其实已经包含了x。

下面是完整的代码：

```Python
npx = np.array([[0, 1, 2],[1, 2, 3],[2, 3, 4]])
npy = np.array([[0, 1, 2],[1, 2, 3],[2, 3, 4]])

inputx = keras.Input(shape=(None,None))
inputy = keras.Input(shape=(None,None))

outputz = keras.layers.add([inputx, inputy])

model = keras.Model(inputs=[inputx, inputy], outputs=[outputz])

x = model([inputx, inputy])

t = K.argmax(x,axis=1)

model = keras.layers.Lambda(lambda _ : t)(x)

model = keras.Model(inputs=[inputx, inputy], outputs=[model])
npw = model.predict([[npx], [npy]])
print(npw)
```

## K.function

使用后端的function，可以将模型转换为函数。可将上节代码的最后三行替换为：

```Python
fun = K.function(inputs=[inputx,inputy],outputs=[x])
npz = fun([npx,npy])
print(npz)
```
