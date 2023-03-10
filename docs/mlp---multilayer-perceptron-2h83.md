# MLP 多层感知器

> 原文：<https://dev.to/victorqribeiro/mlp---multilayer-perceptron-2h83>

# MLP -多层感知器

JavaScript 中的多层感知器实现。

## 关于

这是我在 JavaScript 中实现的一个 MLP。
它附带了一个矩阵库来帮助矩阵乘法。
现在代码还没有经过测试，只有基本的检查，但我仍在努力。
代码中有一个 *s* 变量被注释掉了，它可以用来度量迭代过程中的错误。随着 MLP 的训练，误差会变小。html 示例中使用的数据集取自这里的[和](https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data)。

## 如何使用

假设您有以下数据集:

| 高度(厘米) | 重量(千克) | 类别(0-1) |
| --- | --- | --- |
| one hundred and eighty  | Eighty | Zero |
| One hundred and seventy-five | Sixty-seven | Zero |
| One hundred | Thirty | one |
| One hundred and twenty | Thirty-two | one |

0 -成人

1 -儿童

您需要将表格处理成以下格式:

```
const x = [
    [180, 80],
    [175, 67],
    [100, 30],
    [120, 32]
];

const y = [
    [1,0],
    [1,0],
    [0,1],
    [0,1]
]; 
```

注意，与我的[感知器](https://github.com/perceptron)不同，标签现在是[一键编码](https://en.wikipedia.org/wiki/One-hot)

然后只需创建一个新的 MLP，传递输入的数量、隐藏层的节点数量、输出的数量、
学习速率和迭代次数。

```
const nn = new MLP( x[0].length, x[0].length * 2, 2, 0.03, 500 ); 
```

调用拟合函数

```
nn.fit( x, y ); 
```

你已经准备好做预测了

```
nn.predict( [178, 70] ) 
```

还有一个 [shuffle](https://datascience.stackexchange.com/questions/24511/why-should-the-data-be-shuffled-for-machine-learning-tasks) 功能，可以在训练前使用。

```
nn.shuffle( x, y ); 
```

你可以在这里看一下代码