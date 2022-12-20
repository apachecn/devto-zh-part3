# 数组:Ruby 中的线性容器

> 原文：<https://dev.to/dandyvica/lists-arrays-vectors-linear-containers-in-python-ruby-and-rust-17kb>

在本文中，我将重点介绍 Ruby。我将使用版本 2.5.1:

```
ruby 2.5.1p57 (2018-03-29 revision 63029) [x86_64-linux-gnu] 
```

Enter fullscreen mode Exit fullscreen mode

# 基本的红宝石数组

就像我以前关于 Python 的文章一样，这里 [Python 列出了](https://dev.to/dandyvica/lists-arrays-vectors-linear-containers-in-python-ruby-and-rust-17mn)，我将关注一些更高级的数组特性，而不是基本的特性。

```
digits = [0,1,2,3,4,5,6,7,8,9] 
a = ["one", "two", "three", "four"] 
```

Enter fullscreen mode Exit fullscreen mode

在 Ruby 中创建数组和在 Python 中一样简单:

```
digits = [0,1,2,3,4,5,6,7,8,9] 
a = ["one", "two", "three", "four"]

# empty araay
empty = []
empty = Array.new()
empty = Array.new 
```

Enter fullscreen mode Exit fullscreen mode

您可以混合类型:

```
mixed_array = ["one", 2, "three", 4] 
```

Enter fullscreen mode Exit fullscreen mode

在处理字符串时，您可以使用 *%w()* 构造从一个任意的字符串:
构建一个数组

```
lipsum = %w(Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.) 
```

Enter fullscreen mode Exit fullscreen mode

或者甚至创建一个数组的数组:

```
binomial_coefficients = [
    [1],
    [1,1],
    [1,2,1],
    [1,3,3,1],
    [1,4,6,4,1],
    [1,5,10,10,5,1]
] 
```

Enter fullscreen mode Exit fullscreen mode

要用相同的元素初始化一个数组，只需使用* :

```
['A'] * 5       # gives ['A', 'A', 'A', 'A', 'A']
# or
Array.new(5,'A') 
```

Enter fullscreen mode Exit fullscreen mode

一个数组的元素个数由 *length()* 方法:
给出

```
binomial_coefficients.length            # gives 6
lipsum.length                           # 19 
```

Enter fullscreen mode Exit fullscreen mode

你也可以在一个数组中存储对象、类或函数:

```
# array of functions
trigo = [Math.method(:sin),Math.method(:cos),Math.method(:tan)]

# array of lambdas
powers = [
    ->(x) { x*x }, 
    ->(x) { x**3 },
    ->(x) { x**4 }
]

# array of classes
require 'set'
collections = [Array, Hash, Set]

# array of objects
empty_collections = [Array.new, Hash.new, Set.new] 
```

Enter fullscreen mode Exit fullscreen mode

## 访问元素

访问数组元素和往常一样，只是做了一些非常有用的改进:

```
first_binomial = binomial_coefficients[0]
last_binomial = binomial_coefficients[-1]
fifth_binomial = binomial_coefficients[-2]
binomial_coefficients[4].length # gives 5 
```

Enter fullscreen mode Exit fullscreen mode

使用索引片的子数组是可能的:

```
first3_binomials = binomial_coefficients[0...3]
first4_binomials = binomial_coefficients[0..3] 
```

Enter fullscreen mode Exit fullscreen mode

注意打开范围`(...)`和关闭范围`(..)`之间的差异。

## 阵列操作

*   添加元素

```
digits.push(10) 
```

Enter fullscreen mode Exit fullscreen mode

*   通过索引删除元素

```
digits.delete_at(10) 
```

Enter fullscreen mode Exit fullscreen mode

*   串联数组

```
digits = [0,1,2,3,4] + [5,6] + [7,8,9] 
```

Enter fullscreen mode Exit fullscreen mode

*   测试元素成员资格

```
# methods ending with a trailing ? return a boolean
if digits.include?(9) then
    puts("9 is a digit ! Such a surprise ;-)")
end 
```

Enter fullscreen mode Exit fullscreen mode

## 循环遍历一个数组

使用 for-in 构造(也可用于*到*、*而*、*到* )

```
for d in digits do
    puts(d)
end 
```

Enter fullscreen mode Exit fullscreen mode

但是一个更面向功能的方法是使用*每个*方法:

```
digits.each { |d| puts(d) } 
```

Enter fullscreen mode Exit fullscreen mode

要在循环时获取元素索引，使用 *each_with_index* 方法:

```
digits.each_with_index { |d,i| 
    puts("#{d} is the #{i}-th digit")
} 
```

Enter fullscreen mode Exit fullscreen mode

# 更高级的用法

## 数组上一些有用的函数

```
digits = [0,1,2,3,4,5,6,7,8,9]
digits.sum      # gives 45
digits.max      # gives 9
digits.min      # gives 0

# get any word being the longest
lipsum.max{ |a, b| a.length <=> b.length }           # gives 'consectetur' 
```

Enter fullscreen mode Exit fullscreen mode

*zip()* 内置数组方法将几个数组组合在一起，创建一个结果数组，通过取每个数组的第 I 个元素来创建:

```
a = [0,1,2,3]
b = [4,5,6,7]
a.zip(b) # gives [[0, 4], [1, 5], [2, 6], [3, 7]] 
```

Enter fullscreen mode Exit fullscreen mode

## 无数组理解

在 Ruby 中，没有 Python 中的列表理解那样清晰的语法。但是这些非常类似于高阶函数，如*映射*。所以你可以用*地图*或者*采集*方法
达到同样的效果

```
# extract words ending with 't'
# compact() method is used to get rid of nil values
lipsum = %w(Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.)
end_with_t = lipsum.map { |w| w if w.end_with?('t') }.compact  # gives ['sit', 'incididunt', 'ut', 'et']

# convert to uppercase
lipsum.map { |w| w.upcase }
lipsum.map(&:upcase)

# get only words of length 5 (including commas)
lipsum.map { |w| w if w.length == 5 }.compact # gives ['Lorem', 'ipsum', 'dolor', 'amet,', 'elit,', 'magna']

# create new objects from an array of classes
require 'set'
[Array, Hash, Set].map{ |cls| cls.new }

# same as before when function called doesn't require an argument
[Array, Hash, Set].map(&:new)

# get pi/4 value from trigonometric functions array
trigo = [Math.method(:sin),Math.method(:cos),Math.method(:tan)]
trigo.map { |f| f.call(Math::PI/4) } 
```

Enter fullscreen mode Exit fullscreen mode

## 在可枚举对象上使用 *to_a* 方法

类似于 Python 中内置的 *list()* 函数，Ruby 附带了 *to_a* 方法，该方法从一个可枚举(迭代器的 Ruby 名称):
中创建一个数组

```
# this creates an array of a-z chars
a_to_z = ('a'..'z').to_a

# create digits and the first 100 even numbers
digits = (0..9).to_a
even = (0...100).step(2).to_a

# to_a() is idempotent, but this creates another reference not a new array
digits.to_a # gives back a copy of digits 
```

Enter fullscreen mode Exit fullscreen mode

这显然适用于用户定义的迭代器:

```
class One
    include Enumerable

    def each
        yield "one"
        yield "un"
        yield "ein"
        yield "uno"
    end
end

# gives ['one', 'un', 'ein', 'uno']
One.new.to_a 
```

Enter fullscreen mode Exit fullscreen mode

## 继承数组类

没有什么可以阻止你子类化 *Array* 类来创建你自己的用户定义数组。这个例子实现了一种通过给定几个索引来访问数组元素的方法:

```
# Based on the Array class, but accept sparse indexes
# Doesn't manage range of chars
class MyArray < Array
    def 
        if index.length == 1 && 
            (index[0].class == Range || index[0].class == Integer)
            super(index[0])
        else
            index.collect { |i| super(i) }
        end
    end
end

a = MyArray.new(('a'..'z').to_a)
a[1]              # "b"
a[0..3]           # ["a", "b", "c", "d"]
a[0...3]          # ["a", "b", "c"]
a[0,24,25]        # ["a", "y", "z"] 
```

Enter fullscreen mode Exit fullscreen mode

# 作用于数组

使用函数式编程内置函数，您可以从一个数组中提取值，或者从源数组中获取另一个数组。

### *【地图()*或*收藏()*

使用 *map()* 内置函数，可以获得数组映射的图像。如果您将数组视为元素的数学集合， *map()* 通过所考虑的函数给出图像集。

```
a_to_z = ('a'..'z').to_a
A_to_Z = a_to_z.map{ |c| c.upcase }

# map() uses a block which can be more advanced
# greek letters: ["α", "β", "γ", "δ", "ε", "ζ", "η", "θ", "ι", "κ", "λ", "μ", "ν", "ξ", "ο", "π", "ρ", "ς", "σ", "τ", "υ", "φ", "χ", "ψ", "ω", "ϊ", "ϋ", "ό", "ύ", "ώ"]
greek = ('α'..'ω').to_a
greek.map{ |g| 
    case g
        when "α"
            "A"
        when "β"
            "B"
        when "γ"
            "C"
        # and so on
        else
            "X"
    end
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，作为第一个参数传递的 map 函数可以是任何函数，任何有一个参数的 lambda 都是可能的:

```
digits = (0..9).to_a
digits.map( &->(x) { x*10 } )        # gives tenths

# refer to binomial_coefficients above
binomial_coefficients.map(&:sum)  # gives [1, 2, 4, 8, 16, 32] 
```

Enter fullscreen mode Exit fullscreen mode

甚至是用户自定义函数:

```
# contrived example
def square(x)
    x**2
end

# calculate first 9 perfect squares
digits = (0..9).to_a
digits.map(&method(:square))      # gives [0, 1, 4, 9, 16, 25, 36, 49, 64, 81] 
```

Enter fullscreen mode Exit fullscreen mode

*   *选择()*

这个内置函数用于使用一些标准从数组中筛选元素。保留的元素是作为第一个参数提供给 *select()* 的函数返回 *true* 的元素。

```
# extract even numbers
digits = (0..9).to_a
digits.select { |n| n%2 == 0 }        # gives [0, 2, 4, 6, 8]

# extract words less than 4 chars
lipsum = %w(Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.)
lipsum.select { |w| w.length < 4 }   # gives ['sit', 'sed', 'do', 'ut', 'et'] 
```

Enter fullscreen mode Exit fullscreen mode

*   *inject()*

参考我之前关于 Python 的 *reduce()* 的文章，了解一些关于 *inject()* 方法的细节。

例子:

```
digits = (0..9).to_a

# sum of first 10 digits
digits.inject { |x,y| x+y }   # gives 45

# this uses an initializer
digits.inject(10) { |x,y| x+y } # gives 55 = 45+10

# a more sophisticated example: this uses the nested multiplication to compute the value of a polynomial, given its coefficients and the unknown value z. BTW, an example of how to extend the Array class ;-)
class Array
    def nested(z)
        self.inject { |x,y| z*x+y }
    end
end
[1,5,10,10,5,1].nested(1)       # gives 32

# easy computation of the nested square root which converges to the golden ratio
([1]*100).inject { |x,y| Math::sqrt(x+y) }   # gives 1.618033988749895

# same for the canonical continued fraction. Note to to_f method to convert to float when dividing, otherwise no division is applied
([1]*100).inject { |x,y| y+1/x.to_f }

# this sums all columns of a matrix
matrix = (0..3).to_a.map { |i| [i]*4 }

matrix.each_with_index.inject([0]*4) { |x, (y,i)|
    x.map!.with_index { |e,i| e+y[i] }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后一部分将致力于锈菌载体。

> 苏珊·尹在 Unsplash 上的照片