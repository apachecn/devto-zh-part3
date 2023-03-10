# 使用此功能将超能力添加到 Python 列表中

> 原文：<https://dev.to/abdurrahmaanj/add-superpowers-to-your-python-lists-using-this-feature-24nf>

python 列表很好，deque 增加了趣味性。让我们看看怎么做。

免责声明:这是一份关于德克族的*真正的*完整指南。

额外奖励:最后的示例程序

## *的*代表什么？

deque 代表 **d** 双端**队列**

让我们只导入 deque，因为我们正在试验

```
from collections import deque 
```

接下来，我们创建它的一个实例

```
list1 = deque() 
```

## 创建时添加元素

那个被很多很多(再一次)很多德克教程错过了

```
list1 = deque(['a', 'b', 'c']) 
```

事实上，您可以在创建时填充它

## 添加元素

很像一个列表。可以使用追加

```
>>> list1.append('a')
>>> list1
deque(['a']) 
```

## 延伸

也可以通过扩展
来添加

```
>>> list1.extend('a')
>>> list1
deque(['a'])
>>> list1.extend('bc')
>>> list1
deque(['a', 'b', 'c']) 
```

您也可以使用 list
进行扩展

```
>>> list1.extend(['def'])
>>> list1
deque(['a', 'b', 'c', 'def'])
>>> list1.extend(['g', 'h', 'i'])
>>> list1
deque(['a', 'b', 'c', 'def', 'g', 'h', 'i']) 
```

## 从左边开始

```
>>> list1.extendleft([1, 2, 3])
>>> list1
deque([3, 2, 1, 'a', 'b', 'c', 'def', 'g', 'h', 'i'])
>>> list1.appendleft([4, 5, 6])
>>> list1
deque([[4, 5, 6], 3, 2, 1, 'a', 'b', 'c', 'def', 'g', 'h', 'i']) 
```

## 获取元素

就像列表一样工作

```
>>> list1[0]
[4, 5, 6] 
```

## 获取元素的索引

⚠️再一次和列表一样，只是用了**py 3.5>=**T2】

```
>>> list1.index('a')
4 
```

## 获取区间指标

假设您想要搜索索引 5 和 9
之间的元素，我们想要搜索‘b’

```
>>> deque(['a', 'b', 'c', 'd', 'r', 't', 'd', 'b', 's', 'p']).index('b', 5, 9)
7 
```

其中搜索是下限包含的

## 天王加入游戏

```
>>> list1.pop()
>>> list1
deque([[4, 5, 6], 3, 2, 1, 'a', 'b', 'c', 'def', 'g', 'h']) 
```

从左边开始

```
>>> list1.popleft()
>>> list1
deque([3, 2, 1, 'a', 'b', 'c', 'def', 'g', 'h']) 
```

## 关于非 pep8 符合性的说明

注意 popleft 等不跟 pep8

## 一个固定宽度的列表(太棒了！不需要检查和删除)

一个队列可以被约束如下

```
pipe = deque(maxlen=5) 
```

现在让我们添加元素

```
for i in range(0, 10+1):
    pipe.append(i)
print(pipe) 
```

发出:

```
deque([6, 7, 8, 9, 10], maxlen=5) 
```

我们看到 0 到 5 已经被自动消除了

## 旋转令人惊叹

假设你有

```
>>> alphs = deque()
>>> alphs.extends('abcdef')
>>> alphs.rotate(2)
>>> alphs
deque(['e', 'f', 'a', 'b', 'c', 'd']) 
```

按负指数旋转，例如-2 向相反方向旋转

## 实现一个简单的密码

使用旋转，加密非常简单

```
from collections import deque
import string

original = deque()
rotated = deque()

original.extend(string.ascii_lowercase) # a .. z rotated.extend(string.ascii_lowercase)
rotated.rotate(2)

print(original)
print(rotated)

to_encode = 'myhouse'
encoded_text = ''
for c in to_encode:
    index_char = original.index(c)
    encoded_char = rotated[index_char]
    encoded_text += encoded_char

print(encoded_text)

decoded_text = ''
for c in encoded_text:
    index_char = rotated.index(c)
    decoded_char = original[index_char]
    decoded_text += decoded_char

print(decoded_text) 
```

发出

```
deque(['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z'])
deque(['y', 'z', 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x'])
kwfmsqc
myhouse 
```

## 前车之鉴

不要做

```
original = rotated = deque() 
```

由于 python 引用，更改一个也会更改另一个

如片段所示

```
>>> original = rotated = deque()
>>> rotated.extend('abc')
>>> rotated
deque(['a', 'b', 'c'])
>>> original
deque(['a', 'b', 'c'])
>>> original.extend('d')
>>> rotated
deque(['a', 'b', 'c', 'd']) 
```

## 我们没有列举什么例子

*   。remove(4):删除队列中的元素 4，否则将引发 ValueError
*   。reverse():反转 deque，[1，2，3]变成[3，2，1]
*   。clear():清除 deque，deque(['a '，' b '，' c'])变成 deque([])
*   。count('a '):给出出现的次数。德克(['a '，' b '，' c'])。count('a ')给出 1
*   。insert(索引，元素)。x = deque(['a '，' b '，' c '])；x.insert(1，' t ')；其中 x 变成了 deque(['a '，' t '，' b '，' c'])