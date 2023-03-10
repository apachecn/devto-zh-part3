# 不变

> 原文：<https://dev.to/rinisimon/string-immutability-all-you-need-to-know-1pbe>

让我们看看这个奇特的词是什么意思。

**不变的**
*形容词*
***随着时间的推移不变的或不能改变的。*T9】**

嗯...

**STRING 是怎么做到的？**

```
String strOne = "sun"; 
```

很简单，创建了一个**字符串** ***【太阳】*** ，并分配了一个引用，即 **strOne** 。
[![alt text](img/0d21c21f2680fd10a2e9f02a15e85e09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M7KWrhaz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hr91ror9zn8wkvpqcc98.png) 
*现在，*

```
String strCopy = strOne; 
```

给 ***【孙】*T3 再分配一个参照，即 **strCopy** 。
[![alt text](img/2b6c4c8bdae5d60c7bf6d818819e4358.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2nFdUh9E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tgcmkrwcbtzl93rfgqyl.png) 
*目前看来不错.....让我们再做一步*** 

```
strOne = strOne.concat("flower"); 
```

这就把**串** ***【花】*T5】追加到**串**。**

[![alt text](img/1ff8039d8025db02af5601e15dc9fcce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y6ECXL-4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/45zcckzqfv5uqexqc64t.png)

但是，**等等！**
**那不变性呢(不能改变字符串对象)？**

**下面是所发生的事情**
执行这条语句时，VM 取**字符串*****【sun】***并追加***【flower】***，结果是***【sun flower】***。
由于**字符串是不可变的**，VM 创建新的**字符串对象**，赋予其值 ***【太阳花】*** ，并赋予其引用 **strOne** 。

[![alt text](img/121756b3c527537e2927d706b0686c61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZtjrAdcD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ekumnilqjdedxej1sta.png)

**注意:**
记住，**字符串对象是不可变的**，但是**不是引用变量**，这就是为什么我们能够用现有变量引用新对象。

*好的，*

此时，我们有两个对象，分别是由 strCopy 指出的“太阳” ***、*** 、 **strOne 指出的“太阳花”*、*** 。但是从技术上来说，我们有三个。第三个是我们的 **concat** 语句中的文字***“flower”***。

[![alt text](img/5093776eb07448c9fce56dd1be82ad5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oS_Sq3WU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2qfudx2q5wfh6aipbwkh.png)

**重要事实:**
***如果我们一力隐射“孙”呢？***
它仍然存在但是，因为没有对它的引用而丢失了。

好吧，让我们试试这个

```
String one = "learn";
one.concat("java");
system.out.prinln("one refers to:" + one); 
```

你看到了什么？惊讶吗？

第一行创建一个新的**字符串** ***【学习】*** 给它一个引用，简单。
第二行 VM 创建一个新的**字符串*****“learn Java”***但是它没有引用。因此，我们不能伸出手去，认为它丢失了。参考变量**一个**还是指 ***【学习】*** 。

几乎每次一个方法试图修改一个**字符串对象**，它就会创建一个新的**字符串对象**。

***这些物体都发生了什么？他们在哪里？***
它们存在于记忆中。

JVM 留出一些内存称为 ***【春恒池】*** 。
当编译器得到一个字符串时，它会在池中寻找它。如果存在匹配，对新文本的引用将指向现有字符串，因此不会创建新的字符串对象。现有字符串对象有一个或多个引用。**这就是字符串对象不可变的原因。**

字符串常量池中的每个字符串对象都可能有一个或多个引用。如果几个引用指向一个对象，而每个都不知道，那么如果任何一个引用修改了对象的值，那就糟了，**这就是为什么字符串对象是不可变的。**

***如果我覆盖了字符串类的功能怎么办？***
嗯，不行。字符串类**被标记为 **final** ，所以没有人可以覆盖它的行为。**

* * *

我希望这是信息丰富的。快乐学习 :D
*图片作者:[里尼西蒙](https://www.instagram.com/rinikaruna/)*