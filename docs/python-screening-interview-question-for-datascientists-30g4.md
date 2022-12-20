# 面向数据科学家的 Python 筛选面试问题

> 原文：<https://dev.to/julienkervizic/python-screening-interview-question-for-datascientists-30g4>

### Python 为数据科学家筛选面试问题

<figure>[![](img/a72dab0ad2e535f33e2a5f50dc26bd17.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s3_jYDnG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AAVvpe5YdYeRiA1ML) 

<figcaption>照片由[马里乌斯·马萨拉尔](https://unsplash.com/@marius?utm_source=medium&utm_medium=referral)上传 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

数据科学需要跨学科的技能，从处理数据库，到运行统计模型，到建立业务案例和编程本身。通常情况下，数据科学家的技术面试更多的是评估特定数据操作 API 的知识，如 pandas，sklearn 或 spark，而不是编程思维方式。

虽然我认为在雇用数据科学家时，应该测试更“实用”的 API 知识，但更传统的编程知识也是如此。

### 串反转&回文

字符串反转问题可以提供一些信息，说明某些候选人在处理 python 文本和基本操作方面做得如何。

#### ***问题 1*** :

**问题:**反串《狐狸跳过懒狗》

***答案:***

```
a = "the fox jumps over the lazy dog"
a[::-1]

or ''.join(x for x in reversed(a)) [less efficient]
or ''.join(a[-x] for x in range(1, len(a)+1)) [less efficient] 
```

***评估:***

*   这更像是一个热身问题，虽然知道快捷符号很好，特别是当它表示 python 如何处理字符串的一些知识时(例如 fox 的 substr a[0:7]),但对于大多数数据科学的目的来说，这是不必要的

#### 问题二:

**问题**:识别以下句子中所有为回文的单词“Lol，这是个插科打诨，我好久没笑这么多了”

***答案:***

```
def isPalindrome(word: str) -\> bool:
 if(word == word[::-1]):
 return True
 return False

def getPalindromesFromStr(inputStr: str) -\> list:
 cleanStr = inputStr.replace(",","").lower()
 words = set(cleanStr.split(" "))
 wPalindromes = [
 word for word in words 
 if isPalindrome(word) and word != ""
 ]
 return wPalindromes

getPalindromesFromStr(“Lol, this is a gag, I didn’t laugh so much in a long time”) 
```

***评估:***

*   候选人是否考虑清理他/她的输入？
*   考生是否了解 python 中的基本或文字处理如替换/拆分/降低？
*   候选人知道如何使用列表理解吗？
*   候选人如何构建他/她的代码？

### **FizzBuzz**

FizzBuzz 是一个传统的编程筛选问题，它允许测试候选人是否能够思考一个不是简单的 if else 语句的问题。他们所采取的方法也能帮助他们理解语言。

问题:写一个程序，打印 1 到 50 的数字，打印 2 的倍数而不是数字，打印 3 的倍数，打印 2 和 3 的倍数。

***答案:***

```
def fizzbuzzfn(num) -\> str:
 mod\_2 = (num % 2 == 0) 
 mod\_3 = (num % 3 == 0)
 if (mod\_2 or mod\_3):
 return (mod\_2 \* 'Fizz') + (mod\_3 \* 'Buzz')
 return str(num)

print('\n'.join([fizzbuzzfn(x) for x in range(1,51)])) 
```

**评估:**

*   他们知道模运算符并能够应用它吗？
*   他们是否将模运算符的结果存储在变量中以供重用？
*   他们了解真/假如何与字符串交互吗？
*   他们在用 if 语句轰炸他们的代码吗？
*   它们是返回一致的类型还是混合了整数和字符串？

### 第一个重复字

如果候选人了解 python 中文本处理的基本知识，并且能够处理一些基本的数据结构，那么首先发现重复单词就可以确定候选人。

#### **问题 1**

**问题:**给定一个字符串，找出第一个重复的单词，例如字符串:“这只是一个奇迹，不知道我为什么会有这个想法”

***答案:***

```
string = "this is just a wonder, wonder why do I have this in mind"

def firstduplicate(string: str) -\> str:
 import re
 cleanStr = re.sub("[^a-zA-Z -]", "", string)

 words = cleanStr.lower().split(" ")
 seen\_words = set()
 for word in words:
 if word in seen\_words:
 return word
 else: 
 seen\_words.add(word)
 return None

firstduplicate(string) 
```

***评估:***

*   我是否有需要处理的约束，例如在记忆方面？
*   清除字符串中的标点符号？替换还是正则表达式？如果使用正则表达式替换，我应该编译正则表达式还是直接使用它？
*   知道正确的数据结构来检查是否存在。
*   它是一找到匹配就终止函数还是？

#### 问题二:

**问题:**如果我们想在一个字符串中找到第一个有两个以上重复的单词会怎样？

***答案:***

```
string = "this is just a wonder, wonder why do I have this in mind. This is all that matters."

def first2timesduplicate(string: str) -\> str:
 import re
 cleanStr = re.sub("[^a-zA-Z -]", "", string)

 words = cleanStr.lower().split(" ")
 seen\_words = dict()

for word in words:
 previous\_count = seen\_words.get(word, 0)
 seen\_words[word] = previous\_count + 1
 if previous\_count \>= 2:
 return word
 return None

first2timesduplicate(string) 
```

***评估:***

*   为了适应这种变化，需要做一些小的修改，主要的修改是由于使用了字典数据结构而不是集合。[计数器](https://docs.python.org/2/library/collections.html)也是这个用例的有效数据结构。
*   修改前面的函数来处理这个更改请求没有什么困难，考虑到默认值，检查候选函数是否正确地实例化了特定的键是值得的。

### 快速开火提问

也可以问一些快速的问题来测试 python 语言的一般知识。

#### 问题 1:

**问题:**复制任意数量变量的和，例如 sum(1，2，3，4，5..)

***回答***

```
def sum(\*args):
 val = 0
 for arg in args:
 val += arg
 return val 
```

***评估:***

*   快速面试问题，检查可变参数的知识，以及如何设置一个最基本的函数。

#### 问题二:

围绕斐波那契数列的问题是编程面试的经典，一般来说，应聘者至少应该熟悉这些问题。它们允许测试递归思维。

**问题:**斐波那契数列定义如下:

```
F\_0 = 0 ; F\_1 = 1
F\_n = F\_{-1} + F\_{-2} 
```

写一个函数，给出从 0 到 n 的所有斐波那契数列的和。

***答案:***

```
def fibonacci(n: int) -\> int:
 # fib series don't exist \< 0 
 # might want to throw an error or a null 
 # for that
 if n \<= 0: 
 return 0
 if n == 1: 
 return 1
 else:
 return fibonacci(n-1) + fibonacci(n-2)

def naiveFibSum(n: int) -\> int:
 return sum([fibonacci(x) for x in range(0, n+1)])

def sumFib(n: int) -\> int:
 return fibonacci(n + 2) -1 
```

***评估:***

*   首先，候选人能够递归地思考吗？
*   候选人只是在想斐波纳契数列求和的一个天真的解决方案，还是他/她知道它也可以以更有效的方式总结为 a？

### 总结起来

这些问题只是对数据科学家的第一次筛选，应该结合统计和数据处理类型的问题。它们旨在让你快速了解候选人是否具备通过一轮完整面试的最基本的知识。

对于 Python 来说，更高级的编程问题可能会涉及到生成器、装饰器、cython 的使用，或者诸如 pandas/numpy 等库的有效使用。

更多来自我的[黑客分析](https://medium.com/analytics-and-data):

*   [面向有抱负的数据科学家的 SQL 面试问题—直方图](https://medium.com/analytics-and-data/sql-interview-questions-for-aspiring-data-scientist-the-histogram-76a46de9b0a3)
*   [成为 Pandas 的专家，Python 的数据操作库](https://dev.to/julienkervizic/become-a-pro-at-pandas-python-s-data-manipulation-library-4214-temp-slug-5230196)
*   [电子商务分析:数据结构和应用](https://medium.com/analytics-and-data/e-commerce-analysis-data-structures-and-applications-6420c4fa65e7)
*   [在 Azure 上设置气流&连接到 MS SQL 服务器](https://medium.com/analytics-and-data/setting-up-airflow-on-azure-connecting-to-ms-sql-server-8c06784a7e2b)
*   [构建机器学习模型的 3 个简单规则，增加价值](https://medium.com/analytics-and-data/3-simple-rules-to-build-machine-learning-models-that-add-value-61106db88461)

* * *