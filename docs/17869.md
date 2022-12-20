# Python 基类中方法的私有副本

> 原文：<https://dev.to/tutu10/private-copy-of-method-in-python-base-class-3b81>

Python 文档中有一个代码示例如下:
映射:
def **init** (self，iterable):
self . items _ list =[]
self。_ _ update(iterable)
def update(self，iterable):
对于 iterable 中的项:
self . items _ list . append(item)
_ _ update = update #原始 update()方法的私有副本

最后一行代码是注释#private copy of original update()。根据文献，如果在子类中创建了更新方法，这应该在基类中创建更新方法的私有副本。

有人知道这样做的意义吗？