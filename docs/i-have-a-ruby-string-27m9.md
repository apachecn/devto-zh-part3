# 我有一根红宝石线...

> 原文：<https://dev.to/burdettelamar/i-have-a-ruby-string-27m9>

有时我有一个红宝石字符串，但我想有其他基于字符串的东西。

这里有一些相关的代码示例。

如果我确切知道字符串中的内容，就不需要这些技术，但有时我不知道。例如:解析。

这些技术大家都会知道一些，有的可能全知道。但是:

*   "人们需要更多的提醒，而不是指导。"塞缪尔·约翰逊博士

这些例子是在交互式 Ruby Shell 中编写的，`irb`。

每个示例:

*   显示一个字符串。
*   对字符串执行一些操作。
*   显示结果。

### 我有一根红宝石弦，但我想要的是...

#### A 类

```
s
# => "Hash"
c = Object.const_get(s)
# => Hash
c.class
# => Class 
```

#### 一个类的实例

```
s
# => "Hash"
h = Object.const_get(s).new
# => {}
h.class
# => Hash 
```

#### 一个符号

这很简单。

```
s
# => "my string"
sym = s.to_sym
# => :"my string"
sym.class
# => Symbol 
```

另外:

```
s
# => "my string"
sym = s.intern
# => :"my string"
sym.class
# => Symbol 
```

#### 整数

```
s
# => "100"
i = s.to_i
# => 100
i.class
# => Integer 
```

在不同的基础上解释:

```
s
# => "111"
i = s.to_i(2)
# => 7
i.class
# => Integer 
```

解释为八进制:

```
s
# => "0246"
o = s.oct
# => 166
o.class
# => Integer 
```

解释为十六进制:

```
s
# => "0xdeadbeef"
h = s.hex
# => 3735928559
h.class
# => Integer 
```

#### 一辆花车

```
s
# => "1.5"
f = s.to_f
# => 1.5
f.class
# => Float 
```

带指数:

```
s
# => "0.314159e1"
f = s.to_f
# => 3.14159
f.class
# => Float 
```

#### 理性的

```
s
# => "10/4"
r = s.to_r
# => (5/2)
r.class
# => Rational 
```

#### 复杂的

```
s
# => "2+2i"
c = s.to_c
# => (2+2i)
c.class
# => Complex 
```

#### 字节数组

```
s
# => "Do you feel lucky?"
b = s.bytes
# => [68, 111, 32, 121, 111, 117, 32, 102, 101, 101, 108, 32, 108, 117, 99, 107, 121, 63]
b.class
# => Array 
```

#### 一组字符

```
s
# => "Do you feel lucky?"
a = s.chars
# => ["D", "o", " ", "y", "o", "u", " ", "f", "e", "e", "l", " ", "l", "u", "c", "k", "y", "?"]
a.class
# => Array 
```

#### 代码点数组

```
s
# => "Do you feel lucky?"
c = s.codepoints
# => [68, 111, 32, 121, 111, 117, 32, 102, 101, 101, 108, 32, 108, 117, 99, 107, 121, 63]
c.class
# => Array 
```

#### 一排线

```
s
# => "Twas brillig, and the slithy toves\n      Did gyre and gimble in the wabe:\nAll mimsy were the borogoves,\n      And the mome raths outgrabe. \n"
l = s.lines
# => ["Twas brillig, and the slithy toves\n", "      Did gyre and gimble in the wabe:\n", "All mimsy were the borogoves,\n", "      And the mome raths outgrabe. \n"]
l.class
# => Array 
```

#### Split 中的数组

```
s
# => "Twas brillig, and the slithy toves\n      Did gyre and gimble in the wabe:\nAll mimsy were the borogoves,\n      And the mome raths outgrabe. \n"
a = s.split("\n")
# => ["Twas brillig, and the slithy toves", "      Did gyre and gimble in the wabe:", "All mimsy were the borogoves,", "      And the mome raths outgrabe. "]
a.class
# => Array 
```

#### 字节的枚举数

```
s
# => "xyzzy"
enum = s.each_byte
# => #<Enumerator: "xyzzy":each_byte>
enum.class
# => Enumerator
enum.each do |b|
    p b
  end
120
121
122
122
121
# => "xyzzy" 
```

#### 字符的枚举器

```
s
# => "xyzzy"
enum = s.each_char
# => #<Enumerator: "xyzzy":each_char>
enum.class
# => Enumerator
enum.each do |char|
    puts char
  end
x
y
z
z
y
# => "xyzzy" 
```

#### 代码点的枚举数

```
s
# => "xyzzy"
enum = s.codepoints
# => [120, 121, 122, 122, 121]
enum.class
# => Array
enum.each do |codepoint|
    puts codepoint
  end
120
121
122
122
121
# => [120, 121, 122, 122, 121] 
```

#### 行的枚举器

```
s
# => "Twas brillig, and the slithy toves\n      Did gyre and gimble in the wabe:\nAll mimsy were the borogoves,\n      And the mome raths outgrabe. \n"
enum = s.each_line
# => #<Enumerator: "Twas brillig, and the slithy toves\n      Did gyre and gimble in the wabe:\nAll mimsy were the borogoves,\n      And the mome raths outgrabe. \n":each_line>
enum.class
# => Enumerator
enum.each do |line|
    puts line
  end
Twas brillig, and the slithy toves
      Did gyre and gimble in the wabe: All mimsy were the borogoves,
      And the mome raths outgrabe.
# => "Twas brillig, and the slithy toves\n      Did gyre and gimble in the wabe:\nAll mimsy were the borogoves,\n      And the mome raths outgrabe. \n" 
```