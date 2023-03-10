# 逆向工程 Python 的内存-整数

> 原文：<https://dev.to/nowke/reverse-engineering-python-s-memory-integers-2d4p>

[![Python integers](img/ab24b7dedaa5431f800a8547ed091bff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RtAmtxlP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pj4kywyib47z3hbza4l6.png)

**在 Jupyter 笔记本中查看此贴**:[https://nb viewer . Jupyter . org/github/nowke/notebooks/blob/master/Reverse % 20 engineering % 20 python % 27s % 20 memory % 20-% 20 integers . ipynb](https://nbviewer.jupyter.org/github/nowke/notebooks/blob/master/Reverse%20engineering%20Python%27s%20memory%20-%20Integers.ipynb)

如果您使用 Python 超过一年，您应该听说过它的不同变体。

*   **[CPython](https://www.python.org/)**——可从 python.org[下载的原始实现](https://www.python.org/)。使用 **C** 将 Python 代码编译成字节码。
*   **[Jython](https://www.jython.org/)**——使用 Java 将程序转换成字节码。允许程序在 JVM 上运行。
*   **[PyPy](https://pypy.org/)**——用 Python 本身写的！它使用实时(JIT)编译器，直接翻译成机器码(而不是字节码)。由于本机代码比运行字节码更快，PyPy 提供了比 CPython 更快的速度。

这里我们只谈 **CPython** 变种。

## 基本数据类型

### 确定原始数据类型的大小

`C`，与其他编程语言类似，内置了原始数据类型- `int`、`char`、`long`、`bool`等。

让我们看看如何才能找到你的机器中每种数据类型的大小(我用的是 64 位 MacBook Air)。我们可以使用 [`ctypes`](https://docs.python.org/3/library/ctypes.html) 模块。

```
import ctypes

types = [('int', ctypes.c_int), ('char', ctypes.c_char), 
         ('long', ctypes.c_long), ('bool', ctypes.c_bool), 
         ('voidp', ctypes.c_voidp), ('ulong', ctypes.c_ulong)]

for ctype in types:
    size = ctypes.sizeof(ctype[1])
    print(f'c_{ctype[0]:<5} - {size} byte(s)') 
```

```
c_int    - 4 byte(s)
c_char   - 1 byte(s)
c_long   - 8 byte(s)
c_bool   - 1 byte(s)
c_voidp  - 8 byte(s)
c_ulong  - 8 byte(s) 
```

### 变量表示法

在 Python 中，当一个变量被声明为`var1 = 5`，`var1`并不包含值`5`(就像在 C 中一样)。更确切地说，`var1`指向包含值`5`的对象。

```
var1 = 5
var2 = 5 
```

[![Variable representation](img/c26c569094d8360ff4ce22b5a160b53a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f-sdK_ah--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/puzv2nwilk192m9u9abr.png)

### 访问变量的地址

我们可以使用内置的`id()`函数来访问变量的逻辑地址。

```
id(var1), id(var2), id(5) 
```

```
(4418119136, 4418119136, 4418119136) 
```

`var1`、`var2`、`5`指的是内存中的同一个对象。

让我们检查从`1`到`10`的整数的地址以及地址之间的差异。

```
def print_addr(nums):
    for num in nums:
        addr = id(num)
        print(f'Num = {num:<2}, id = {addr}, diff = {addr - id(num-1)}')

print_addr(range(1, 11)) 
```

```
Num = 1 , id = 4418119008, diff = 32
Num = 2 , id = 4418119040, diff = 32
Num = 3 , id = 4418119072, diff = 32
Num = 4 , id = 4418119104, diff = 32
Num = 5 , id = 4418119136, diff = 32
Num = 6 , id = 4418119168, diff = 32
Num = 7 , id = 4418119200, diff = 32
Num = 8 , id = 4418119232, diff = 32
Num = 9 , id = 4418119264, diff = 32
Num = 10, id = 4418119296, diff = 32 
```

这些数字在内存中是连续的。但是对于大数来说是真的吗？让我们试试从`250`到`265`

```
print_addr(range(250, 266)) 
```

```
Num = 250, id = 4418126976, diff = 32
Num = 251, id = 4418127008, diff = 32
Num = 252, id = 4418127040, diff = 32
Num = 253, id = 4418127072, diff = 32
Num = 254, id = 4418127104, diff = 32
Num = 255, id = 4418127136, diff = 32
Num = 256, id = 4418127168, diff = 32
Num = 257, id = 4463633392, diff = 45506224
Num = 258, id = 4463633328, diff = 32
Num = 259, id = 4463633296, diff = -96
Num = 260, id = 4463633392, diff = 64
Num = 261, id = 4463633328, diff = 32
Num = 262, id = 4463633296, diff = -96
Num = 263, id = 4463633392, diff = 64
Num = 264, id = 4463633328, diff = 32
Num = 265, id = 4463633296, diff = -96 
```

让我们试试负数

```
print_addr(range(-10, 5)) 
```

```
Num = -10, id = 4462902928, diff = -730432
Num = -9, id = 4463633360, diff = 96
Num = -8, id = 4463633264, diff = 730336
Num = -7, id = 4462902928, diff = -730432
Num = -6, id = 4463633360, diff = 96
Num = -5, id = 4418118816, diff = -44784112
Num = -4, id = 4418118848, diff = 32
Num = -3, id = 4418118880, diff = 32
Num = -2, id = 4418118912, diff = 32
Num = -1, id = 4418118944, diff = 32
Num = 0 , id = 4418118976, diff = 32
Num = 1 , id = 4418119008, diff = 32
Num = 2 , id = 4418119040, diff = 32
Num = 3 , id = 4418119072, diff = 32
Num = 4 , id = 4418119104, diff = 32 
```

我们可以观察到这些数字从`-5`到`256`是连续的。这是因为，Python 会为从`-5`到`256`的数字预先分配内存，并在程序启动前创建对象。

## 整数

### 从地址中访问整数

使用`id()`函数，我们看到了如何从**变量转换成>地址**。另一条路怎么样？给定一个地址，如何挖掘里面存储的内容？

```
x = 45
addr_x = id(x)
print(addr_x) 
```

```
4418120416 
```

函数`ctypes.<type>.from_address`采用`address`并返回`<type>`值。让我们把`addr_x`交给`c_int`型。记住:我们需要从`addr_x`
中取回`45`

```
print(ctypes.c_int.from_address(addr_x))
print(ctypes.c_int.from_address(addr_x + 8))
print(ctypes.c_int.from_address(addr_x + 16))
print(ctypes.c_int.from_address(addr_x + 24)) 
```

```
c_int(81)
c_int(122850480)
c_int(1)
c_int(45) 
```

```
x_value = ctypes.c_int.from_address(addr_x + 24)
x_value.value 
```

```
45 
```

为什么要在原地址加`24`？一个整数的存储类似于 C 中的下面的`struct`(实际的结构可能不同)。分配的`32`字节划分如下。

```
struct {
    long   ob_refcnt;  // long   = 8 bytes (c_long)
    void*  ob_type;    // void*  = 8 bytes (c_voidp)
    long   ob_size;    // long   = 8 bytes (c_long)
    uint   ob_digit[]; // uint[] = (8 x ob_size) bytes (c_uint)
} 
```

对于数字`45`，`ob_digit`的数组大小为`1`。现在让我们使用同一个`from_address`函数解包上面的字段，并真正理解返回值。

#### 1。`ob_refcnt` - >表示对对象的引用总数

```
ob_refcnt_x = ctypes.c_long.from_address(addr_x)
print(f'Total references made to {x} - {ob_refcnt_x.value}') 
```

```
Total references made to 45 - 88 
```

#### [2](#2-raw-obtype-endraw-gt-points-to-the-type-object-in-our-case-raw-int-endraw-)。`ob_type` - >指向类型对象，在我们这里是`int`

由于`int`是一个对象，所以它在内存中有一个地址！

```
id(int) 
```

```
4417817776 
```

```
ob_type_x = ctypes.c_void_p.from_address(addr_x + 8)
ob_type_x.value 
```

```
4417817776 
```

注意，`id(int)`与`ob_type_x.value`相同

#### 3。`ob_size`->`ob_digit`数组的大小。

我们将在下面进一步了解它的用途

```
ob_size_x = ctypes.c_long.from_address(addr_x + 16)
ob_size_x.value 
```

```
1 
```

#### 4。`ob_digit[]`

```
ob_digit_x = ctypes.c_uint.from_address(addr_x + 24)
ob_digit_x.value 
```

```
45 
```

```
print(f'''
Integer {x} is represented as,

struct_ {{
    long   ob_refcnt  = {ctypes.c_long.from_address(addr_x).value} void*  ob_type    = {ctypes.c_long.from_address(addr_x + 8).value} long   ob_size    = {ctypes.c_long.from_address(addr_x + 16).value} uint   ob_digit[] = {ctypes.c_long.from_address(addr_x + 24).value} }}
''') 
```

```
Integer 45 is represented as,

struct_ {
    long   ob_refcnt  = 91
    void*  ob_type    = 4417817776
    long   ob_size    = 1
    uint   ob_digit[] = 45
} 
```

### 大整数

在 Python 中，我们可以声明和操作任意大小的整数！

```
big_x = 2**1000
big_x 
```

```
10715086071862673209484250490600018105614048117055336074437503883703510511249361224931983788156958581275946729175531468251871452856923140435984577574698574803934567774824230985421074605062371141877954182153046474983581941267398767559165543946077062914571196477686542167660429831652624386837205668069376 
```

```
big_x + 1 
```

```
10715086071862673209484250490600018105614048117055336074437503883703510511249361224931983788156958581275946729175531468251871452856923140435984577574698574803934567774824230985421074605062371141877954182153046474983581941267398767559165543946077062914571196477686542167660429831652624386837205668069377 
```

Python 如何存储这个值？它使用字段`ob_size`和`ob_digit[]`。计算存储在`ob_digit`中的整数表示的公式如下

[![Python integer formula](img/cadd7c06edba035567c3fbb518fd0823.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UCqgYpEO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1xk7fjya0kbnx8i9mbq9.png)

其中`n = ob_size`

**例一**

```
ob_size = 1
ob_digit = [45]

value = ob_digit[0] x (1024^3)^0
      = 45 x 1
      = 45 
```

**例二**

```
ob_size  = 2
ob_digit = [2, 3]

value = (ob_digit[0] x (1024^3)^0) +
        (ob_digit[1] x (1024^3)^1)
      = (2 x 1) + (3 x 1024^3)
      = 3,221,225,474 
```

让我们来验证一下**例 2**

```
y = 3221225474
addr_y = id(y)
y, addr_y 
```

```
(3221225474, 4463632688) 
```

```
ob_size_y = ctypes.c_long.from_address(addr_y + 16)
ob_size_y.value 
```

```
2 
```

```
ob_digit_y_0 = ctypes.c_uint.from_address(addr_y + 24)
ob_digit_y_0 
```

```
c_uint(2) 
```

```
ob_digit_y_1 = ctypes.c_uint.from_address(addr_y + 28)
ob_digit_y_1 
```

```
c_uint(3) 
```

Ta！我们取回了值`ob_size = 2`和`ob_digit = [2, 3]`

### 整数的大小

`x`和`y`都占用`32 bytes`的内存。更大的整数呢？我们可以利用`sys.getsizeof`功能

```
import sys

sys.getsizeof(x), sys.getsizeof(y) 
```

```
(28, 32) 
```

```
for power in range(10, 101, 10):
    num = 10 ** power
    size = sys.getsizeof(num)
    print(f'Number = 10^{power:<3}, Size = {size}') 
```

```
Number = 10^10 , Size = 32
Number = 10^20 , Size = 36
Number = 10^30 , Size = 40
Number = 10^40 , Size = 44
Number = 10^50 , Size = 48
Number = 10^60 , Size = 52
Number = 10^70 , Size = 56
Number = 10^80 , Size = 60
Number = 10^90 , Size = 64
Number = 10^100, Size = 72 
```

### 重新创建整数

我们利用`from_address`来检索一个整数的不同部分。让我们使用 [`ctypes.Structure`](https://docs.python.org/3/library/ctypes.html#ctypes.Structure) 抽象基类编写一个接受整数的自定义结构。

```
class IntStructure(ctypes.Structure):
    _fields_ = [("ob_refcnt", ctypes.c_long),
                ("ob_type"  , ctypes.c_void_p),
                ("ob_size"  , ctypes.c_long),
                ("ob_digit" , ctypes.c_uint * 0)]

    def __repr__(self):
        digits   = self.get_digits()
        int_size = self.calc_size()
        int_num  = self.calc_integer(digits)
        return (f'Int(ob_refcnt={self.ob_refcnt},\n'
                f'    ob_size={self.ob_size},\n'
                f'    ob_digit={digits},\n'
                f'    int_size={int_size} bytes,\n'
                f'    int_num={int_num})')

    def get_digits(self):
        """Internal representation of `ob_digit` as a list"""

        digits = ctypes.cast(
            ctypes.byref(self.ob_digit),
            ctypes.POINTER(ctypes.c_uint * self.ob_size)
        ).contents

        return list(digits)

    def calc_size(self):
        """Number of bytes consumed by the integer"""

        size = (ctypes.sizeof(ctypes.c_long)   + 
                ctypes.sizeof(ctypes.c_void_p) +
                ctypes.sizeof(ctypes.c_long)   + 
                ctypes.sizeof(ctypes.c_long))
        if self.ob_size > 2:
            size += (self.ob_size - 2) * ctypes.sizeof(ctypes.c_uint)
        return size

    def calc_integer(self, digits):
        """Retrieve the actual integer"""

        return sum(
            [digit * (1024**3)**i
             for i, digit in enumerate(digits)]
        ) 
```

```
a = 50
addr_a = id(a)
int_a = IntStructure.from_address(addr_a)
int_a 
```

```
Int(ob_refcnt=77,
    ob_size=1,
    ob_digit=[50],
    int_size=32 bytes,
    int_num=50) 
```

```
b = 10**40
addr_b = id(b)
int_b = IntStructure.from_address(addr_b)
int_b 
```

```
Int(ob_refcnt=1,
    ob_size=5,
    ob_digit=[0, 668304384, 172751547, 175927511, 7523],
    int_size=44 bytes,
    int_num=10000000000000000000000000000000000000000) 
```

### 改变一个整数的值

参考自 [r/Python](https://www.reddit.com/r/Python/comments/2441cv/can_you_change_the_value_of_1/)

> 不要在实际的程序中这样做！非常糟糕的事情可能会发生

让我们修改整数`120`和

```
# Define a simpler structure (similar to IntStructure) 
class MyInt(ctypes.Structure):
    _fields_ = [("ob_refcnt", ctypes.c_long),
                ("ob_type"  , ctypes.c_void_p),
                ("ob_size"  , ctypes.c_ulong),
                ("ob_digit" , ctypes.c_long)] 
```

```
addr_120 = id(120)
struct_120 = MyInt.from_address(addr_120)
struct_120.ob_digit = 25 
```

```
120 
```

```
25 
```

```
120 + 120 
```

```
50 
```

```
60 + 60 
```

```
25 
```

## 
  
接下来呢？

使用类似的技术，我们可以解码基本数据结构是如何在 Python 中有效实现的。

*   Python 列表
*   字典