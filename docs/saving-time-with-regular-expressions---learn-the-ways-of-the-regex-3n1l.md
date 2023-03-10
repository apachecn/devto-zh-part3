# 正则表达式的⌛Saving 时间-学习正则表达式的方法！⏱

> 原文：<https://dev.to/r4h33m/saving-time-with-regular-expressions---learn-the-ways-of-the-regex-3n1l>

正则表达式(通常缩写为 regex)是一个字符序列，它定义了一个搜索模式，可用于查找字符串中出现的上述模式。通过正则表达式，可以使用 regex 语法在字符串中搜索电话号码、日期、ISBN 代码等。Regex 语法非常强大，使得编写几乎可以找到任何东西的正则表达式成为可能。

正则表达式节省了时间，因为您不用用代码编写每个规则，而是编写一个小而紧凑的搜索模式，其中包括您试图匹配的序列的所有规则。假设您想要一个正则表达式来匹配有效的电子邮件地址。

有效的电子邮件地址必须遵守以下规则:

*   没有空格
*   只有一个“@”符号
*   必须只有一个“.”，并放在@符号之后
*   表示“@”后必须有文字
*   表示“.”后必须有文本

大概是这样的: [raheem@dev.to](mailto:raheem@dev.to)

我知道有效电子邮件地址的规则要复杂得多- [维基百科“电子邮件地址示例”](https://en.wikipedia.org/wiki/Email_address#Examples) -但是为了这篇文章，让我们遵循上面的规则。

不使用正则表达式在文本中搜索有效的电子邮件地址可能如下所示:

```
def findValidEmailAddresses(text):

    validEmailAddresses = []

    #Spilt by whitespace, no need to check for whitespaces then
    words = text.split()

    for word in words:

        #Check if there is only one "@" symbol
        if word.count("@") != 1:
            continue

        #Must have text before and after the "@" symbol
        if word.find("@") == 0 or word.find("@") == (len(word)-1):
            continue

        #Only one "." symbol after the "@" symbol
        if word.count(".") != 1 and not word.find(".") > word.find("@"):
            continue

        #"." symbol cannot be directly after the "@" symbol
        if word.find("@") + 1 == word.find("."):
            continue

        #Check for text after the "." symbol ("." cannot be the last character)
        if word.find(".") == (len(word)-1):
            continue

        #All tests passed, this word is a valid email address
        validEmailAddresses.append(word)

    #Return the email addresses
    return(validEmailAddresses)

print(findValidEmailAddresses("Thanks for reading my post dude. Contact me at plzdont@nooooo.com"))
#Prints ["plzdont@nooooo.com"] 
```

这当然是一种方法，但是让我们用正则表达式来解决同样的问题。

```
import re

emailRegex = re.compile(r"\w+@\w+\.\w+")
print(emailRegex.findall("Thanks for reading my post dude. Contact me at plzdont@nooooo.com"))
#Prints ["plzdont@nooooo.com"] 
```

**哇！每次都让我明白正则表达式是多么的巧妙！让我们分解正则表达式。** 

```
r"\w+@\w+\.\w+" 
```

*   **"\w"** 是一个**字符**，它象征着任何单词字符。
*   **“+”**是一个**量词**，允许一个或多个前面的字符。
*   **“@”**和**。”**从字面上看，它们需要成为字符串的一部分才能匹配。
*   **“**”在**之前**还有就是因为**“”**本身是一个通配符(除换行符以外的任何字符)。“\”正在对“The”进行转义。，使其成为一个文字“.”这就是我们想要的，而不是通配符。

在英语中，正则表达式“\w+@\w+\w+”的意思是:

*   一个或多个任何单词字符，
*   后跟一个“@”符号，
*   后跟一个或多个任意单词字符，
*   后面跟着一个“.”符号，
*   后跟一个或多个任意单词字符。

很可爱，对吧？🍬

正则表达式的语法简单易学，但是足够复杂，以至于不存在不能与正则表达式匹配的字符序列。此外，正则表达式适用于任何编程语言。学习语法一次，并使用它来搜索任何地方的字符序列！

这里有一些资源可以帮助你获得正则表达式的启示:

*   [综合语法备忘单](https://www.rexegg.com/regex-quickstart.html)
*   [如何使用 python 正则表达式“re”库的教程](https://www.tutorialspoint.com/python/python_reg_expressions.htm)
*   [在浏览器中了解、制作和测试正则表达式](https://regexr.com/)

感谢阅读我的帖子，希望你喜欢！这是我在 dev.to 上的第一篇帖子，说真的，我是在拿“[plzdont@nooooo.com](mailto:plzdont@nooooo.com)”开玩笑。如果你对我有任何反馈，请评论！你怎么看待这种幽默的风格？我应该采用更正式的语气吗？更多代码示例？表情符号不够？全部说出来，真的很感谢！

这一次到此为止！