# python 数据类更加干燥，有点像方法的脚手架

> 原文：<https://dev.to/andi/python-dataclasses-more-dry-a-kinda-of-scaffold-for-methods-3ppa>

#### data class-3.7 版本新增(【https://docs.python.org/3/library/dataclasses.html】T2)

#### f ' '格式化-从 3.6(【https://realpython.com/python-f-strings/】T2)开始

### 描述于 PEP0557

> 尽管它们使用非常不同的机制，但是数据类可以被认为是“具有默认值的可变命名元组”。因为数据类使用普通的类定义语法，所以您可以自由地使用继承、元类、文档字符串、用户定义的方法、类工厂和其他 Python 类特性。
> 
> 装饰器将生成的方法定义添加到类中，以支持实例初始化、repr、比较方法和可选的其他方法(...)这样的类被称为数据类，但是这个类并没有什么特别的地方:装饰者将生成的方法添加到这个类中，并返回给它的同一个类。

[https://www.python.org/dev/peps/pep-0557/](https://www.python.org/dev/peps/pep-0557/)

## 举例！

```
@dataclass
class InventoryItem:
    '''Class for keeping track of an item in inventory.'''
    name: str
    unit_price: float
    quantity_on_hand: int = 0

    def total_cost(self) -> float:
        return self.unit_price * self.quantity_on_hand 
```

@dataclass 装饰器将把这些方法的等价物添加到 InventoryItem 中

```
def __init__(self, name: str, unit_price: float, quantity_on_hand: int = 0) -> None:
    self.name = name
    self.unit_price = unit_price
    self.quantity_on_hand = quantity_on_hand
def __repr__(self):
    return f'InventoryItem(name={self.name!r}, unit_price={self.unit_price!r}, quantity_on_hand={self.quantity_on_hand!r})'
def __eq__(self, other):
    if other.__class__ is self.__class__:
        return (self.name, self.unit_price, self.quantity_on_hand) == (other.name, other.unit_price, other.quantity_on_hand)
    return NotImplemented
def __ne__(self, other):
    if other.__class__ is self.__class__:
        return (self.name, self.unit_price, self.quantity_on_hand) != (other.name, other.unit_price, other.quantity_on_hand)
    return NotImplemented
def __lt__(self, other):
    if other.__class__ is self.__class__:
        return (self.name, self.unit_price, self.quantity_on_hand) < (other.name, other.unit_price, other.quantity_on_hand)
    return NotImplemented
def __le__(self, other):
    if other.__class__ is self.__class__:
        return (self.name, self.unit_price, self.quantity_on_hand) <= (other.name, other.unit_price, other.quantity_on_hand)
    return NotImplemented
def __gt__(self, other):
    if other.__class__ is self.__class__:
        return (self.name, self.unit_price, self.quantity_on_hand) > (other.name, other.unit_price, other.quantity_on_hand)
    return NotImplemented
def __ge__(self, other):
    if other.__class__ is self.__class__:
        return (self.name, self.unit_price, self.quantity_on_hand) >= (other.name, other.unit_price, other.quantity_on_hand)
    return NotImplemented 
```

[https://docs.python.org/3/library/dataclasses.html](https://docs.python.org/3/library/dataclasses.html)