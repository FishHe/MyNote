# Getting Started

我不是一个脾气火爆的人，但是看了Keras的官方文档以后，我真的气不打一处来。找遍文字说明，你都找不到如何使用训练好的模型来预测一个输入。

我们先不急于训练有参数的模型，先来看看Keras是如何进行计算的。

* 定义模型
* 训练模型
* 使用模型

## Define the Model

概括地说，Keras在定义模型时，只需指明输入张量的形状，和中间算法。

在训练时，还需要指明损失函数和优化器。

在评价时，还需要指明评价函数。

下面我们来定义一个非常简单的模型，计算两个向量的和：
$$\mathbf{z} = \mathbf{x} + \mathbf{y}$$

```Python
# 定义输入
# 定义一个1阶张量（就是向量），且向量的维度任意时，赋值为None（k维向量就赋值为k）
inputx = keras.Input(shape=(None,))
inputy = keras.Input(shape=(None,))

# 定义中间算法
outputz = keras.layers.add([inputx,inputy])

# 构建模型，并指明输入和输出
model = keras.Model(inputs = [inputx,inputy],outputs = [outputz])
```

## Use the Model

模型不包含任何参数，所以模型不需要经过训练就能够使用。给定两个任意维度的向量并求其和。

```Python
# 定义两个numpy的变量
npx = np.array([0, 1, 2])
npy = np.array([0, 1, 2])

# 使用模型求其输出
npz = model.predict([[npx], [npy]])

# 打印输出
print("z =", str(npadded))
```

输入如下：

> z = [[0. 2. 4.]]

## Train the Model

这部分内容请参考官方文档。
