# 你见过的最简单的感知机

> 原文：<https://dev.to/victorqribeiro/the-simplest-perceptron-youll-ever-see-1glb>

# 感知器

你见过的最简单的感知器。

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

const y = [0,0,1,1]; 
```

然后只需创建一个新的感知器，传递数据的形状(身高和体重)，学习率和迭代次数。默认情况下，学习率设置为 0.01，迭代次数设置为 10。

```
const p = new Perceptron( x[0].length ); 
```

调用拟合函数

```
p.fit(); 
```

你已经准备好做预测了

```
p.predict([178, 70]) 
```

超级简单。

看一下这里的代码