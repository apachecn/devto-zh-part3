# python 和 orgmode 中的 Piglatin。

> 原文：<https://dev.to/zeeter/piglatin-in-python-and-orgmode-2k69>

# 目录

1.  [概念](#org835aead)
2.  [寻找元音](#org03ef308)
3.  [剥离第一个字母](#org7878e91)
4.  [组装在一起](#orgdcf180d)

# 概念

Pig Latin translator 是一个程序，它获取一个字符串，并通过将第一个字母移动到最后一个字母并在其上添加“ay”来转换它。
然而如果字母会是元音，整个过程就用
的“方式”代替了，所以:

```
Dog
Nyx
Puppy
Gizmo
Kitten
Luna-fish
Tuna-fish
Apple
Orange 
```

会变成:

```
Ogday
Yxna
Uppypay
Izmogay
Ittenkay
Una-fishlay
Una-fishtay
Ppleway
Rangeway 
```

我确信实现完整的句子是可能的，但这不在
挑战的范围之内，所以我们暂时不会这么做。

# 寻找元音

要找到元音，我们只需要把它们放在一个方便的地方。
在本例中为字符串。

```
vowels = "aeiou" 
```

然后，我们可以简单地通过查看输入的字符串中是否有元音来检查它是否是元音。

```
def isVowel(letter):

    vowels = "aeiou"
    return letter in vowels 
```

让我们看看它是否有效！

```
def isVowel(letter):

    vowels = "aeiou"
    return letter in vowels
con = isVowel(letter.lower())

if con:
    return "{} is a vowel".format(letter.lower())
else:
    return "{} is not a vowel".format(letter.lower()) 
```

*   a 是元音字母
*   b 不是元音
*   abc 不是元音

但是为了更好，让我们实际制作一个 docstring 来描述这个“函数”是做什么的。

```
Checks to see if a letter is a vowel.
for example:
>>> isVowel("a")
True
>>> isVowel("b")
False 
```

然而，正如所写的，它不需要大写字母。但这是有意的。为了避免
大小写的怪异，我把所有东西都变成小写字母。所以危机暂时避免了。

# 剥离第一个字母

现在我们可以检查元音，但那又怎样？现在我们开始真正拥有我们的话语。
对于这个，我们真的不需要函数。但是用 orgmode 编写使得
代码块作为函数的概念看起来如此简单。
这也使得测试每个代码块更加容易，所以 python 可以在这一点上咬我一口。
它们毕竟不是毒蛇。

```
def stripFirst(word):

    first = word[0]
    rest = word[1:]

    return (first, rest) 
```

现在是我们的 docstring 时间:

```
Strip first takes a word and returns two strings.
One consisting of just the first letter and then the rest of the word.
Example:
>>> stripFirst("Daniel")
("D","aniel") 
```

# 拼凑

所以我可以进一步分解代码，但至少在我看来不会有太大的价值。相反，我将向前跳几步，组装 piglatin 函数。

```
def isVowel(letter):

    vowels = "aeiou"
    return letter in vowels
def stripFirst(word):

    first = word[0]
    rest = word[1:]

    return (first, rest)

def pigLatin(word):
    first, rest = stripFirst(word.lower())

    if isVowel(first):
    return rest + "way"
    else:
    return rest + first + "ay" 
```

严格来说，我们结束了。

```
def isVowel(letter):

    vowels = "aeiou"
    return letter in vowels
def stripFirst(word):

    first = word[0]
    rest = word[1:]

    return (first, rest)

def pigLatin(word):
    first, rest = stripFirst(word.lower())

    if isVowel(first):
    return rest + "way"
    else:
    return rest + first + "ay"

string = '{} is {} in piglatin'.format(word, pigLatin(word).title())

return string 
```

我在后期处理中添加了一些东西，让它看起来更漂亮一些。将来，我可能会扩展这篇文章。谁知道呢？我希望你喜欢阅读我的随笔。