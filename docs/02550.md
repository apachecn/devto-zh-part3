# 高级 Python:为了更吸引人的代码

> 原文：<https://dev.to/teosoft7/advanced-python-for-more-attractive-code-5fc1>

# 高级 Python 的技巧和窍门

[![Python Logo](img/ee7437a05f47ce4de74b63633b6dd3a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--08xSjFaZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://python.rs/pylogo.png)

Python 是一种高级的、解释性的、交互式的和面向对象的脚本语言。Python 被设计成具有高度可读性。它经常使用英语关键字，而其他语言使用标点符号，并且它的语法结构比其他语言少。

与 C/C++、JAVA 和 Swift 等其他编程语言相比，Python 的语法清晰、直观且易于学习。Python 有许多内置的标准特性，如理解、字典、集合和生成器，以提供现代编程语言所需的最常见的函数。Python 也有大量由自己的社区提供的库，比如“pandas”、“numpy”、“matplotlib”等等。正因为如此，Python 是最流行的编程语言之一，尤其是对于数据科学。

除了 Python 的简单性和用户友好性，学习如何编写代码也不是一件容易的事情。学习一门新的不熟悉的语言是非常相似的。它需要一遍又一遍的练习，直到你能在其中感到舒适。为了编写更清晰的代码，它需要理解 Python 解释器是如何工作的，但这可能超出了数据科学的范围。但是这里有七个提示和技巧，可以让数据科学家编写更有吸引力的 Python 代码。

### 1。代表大数的下划线(_)分隔符

_ '可以用作分隔符来表示一个大的数字

```
ten_billion = 10_000_000_000

print(f'{ten_billion:,}')
10,000,000,000 
```

### 2。用 if 语句赋值

When 'if '语句中只有一个赋值代码

```
# general isHappy = True

if isHappy == True:
    result_string = 'Happy'
else:
    result_string = 'Not Happy'

print(result_string)

Happy

# advanced isHappy = True

result_string = 'Happy' if isHappy else 'Not Happy'

print(result_string)

Happy 
```

### 3。在两个变量之间交换值

Python 可以在没有临时变量的情况下交换两个变量之间的值

```
# general low = 10
high = 9

if low > high:
    temp = low
    low = high
    high = temp

print(low, high)

9 10

# advanced low = 10
high = 9

if low > high:
    low, high = high, low

print(low, high)

9 10 
```

### 4。枚举函数

如果需要 for 循环中的索引值，请使用枚举函数

```
# general grades = ['A', 'B', 'C', 'D', 'E', 'F']

i = 1
for grade in grades:
    print(f'{i} : {grade}')
    i += 1

1 : A
2 : B
3 : C
4 : D
5 : E
6 : F

# advanced grades = ['A', 'B', 'C', 'D', 'E', 'F']

for i, grade in enumerate(grades, 1):
    print(f'{i} : {grade}')

1 : A
2 : B
3 : C
4 : D
5 : E
6 : F 
```

### 5。列出理解

创建列表的简明方法

```
# beginner numbers = [1, 2, 3, 4, 5, 6, 7]

squared = []
for number in numbers:
    squared.append(number * number)

print(squared)

# advanced numbers = [1, 2, 3, 4, 5, 6, 7]

squared = [number * number for number in numbers]

print(squared) 
```

### 6。拆包

解包元组内的值

```
 # from 13 cards cards = ('A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K' )

# extract ace, 2, 3 only ace, two, three, *_ = cards
print(ace, two, three)
A 2 3

# extract ace, [numbers], J, Q, K ace, *numbers, J, Q, K = cards
print(ace, numbers, J, Q, K)
A ['2', '3', '4', '5', '6', '7', '8', '9', '10'] J Q K 
```

### 7。For ~否则

else 语句也用在 for 循环之后，它在 for 循环之后执行

```
# For ~ Else grades = ['A', 'B', 'C', 'D', 'E', 'F']

my_grade = 'A+'

for grade in grades:
    if grade == my_grade:
        print('grade found')
        break
else:
    print('grade not found')

grade not found 
```