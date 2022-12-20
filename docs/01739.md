# 如果 a=2，那么 a++ ++ a = = 42。怎么会？

> 原文：<https://dev.to/anuragrana/if-a-2-then-a-a-42-how-9bj>

[![](img/e0063973598716e3efe78c15bfebba6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R68YluQg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/910/1%2AHpzZs-O3aA1KwYD4ZILrVg.jpeg)

任何有客观题的 C 程序设计考试，无论是课堂考试还是校园分班考试，我相信你一定不止一次遇到过这种类型的问题。

是的……我知道…我能理解…

[![](img/06a14c49e603f907d00787c439871cde.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Davi4xSG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/234/0%2AhFbxvs6Rb391YFv3.png)

很少有人理解这背后的逻辑，他们一遍又一遍地问这样的问题…..

如果 a=2，那么++a + a++ + a++的值会是多少？

一些天才甚至超越了这一点，他们会做一个很长的陈述，甚至不适合在一张 A4 纸上的一行。

a = a++ ++ a+a+++ a++ ++ a…..诸如此类…

规则是如果 a=2，那么 a++ ++ a 可能等于 2，它可能等于 4，5，42，99 甚至 2048。基本上在这种情况下，编译器可以输出任何它想要的东西。

规则是什么:

正如你们中的一些人可能知道的那样，上述问题的答案是“依赖于编译器”或者“不可预测”。没错。

**如何？**

所以在 c 语言中有一种叫做‘序列点’的东西，在每一个序列点之后，编译器都会对前一个序列点之后遇到的东西进行求值。

这么一个分号(；ASCII 59)是 c 语言中的序列点。编译器在进入下一条语句之前，遇到分号时会计算所有内容。

好像有什么东西

```
int numberOne = 2, numberTwo=3;   
sum = numberOne + numberTwo;  
printf(“%d”, sum); 
```

sum 将被打印为 5，因为编译器将在转到 printf 语句之前计算第二条语句中的 sum。

但是编译器如何在一个序列点内求值却没有保证。在这样的声明中

T2`i = j++ + k++;`

谁也不能保证是 j 先递增还是 k 先递增。尽管在某些情况下先评估哪一个并不重要。但在某些情况下，它可能会将 CSE 学生的包裹从 10 LPA 减少到 6 LPA。

**未定义的行为呢:**

c 标准规定，一个变量的值在一个序列点内最多只能改变一次。但是在像 a++ ++ a 这样的语句中，我们不止一次地改变 a 的值。根据 C 编译器，这是不允许的。你违反了规则，编译器也会这么做。

所以基本上，如果你将来遇到这种问题，那么最合适的答案是

——不可预测或依赖于编译器

如果没有给出这样的选项，和老师谈谈，礼貌地引导他到这个博客。

**更多来自作者:**

*   [**如何在 pythonanywhere 上免费托管 django app**](https://www.pythoncircle.com/post/18/how-to-host-django-app-on-pythonanywhere-for-free/)
*   [**排名前 5 的 Python 书籍**](https://www.pythoncircle.com/post/646/top-5-python-books/)
*   [**用 Python 在树莓 Pi 上编程:控制 LED**](https://www.pythoncircle.com/post/652/programming-on-raspberry-pi-with-python-controlling-led/)
*   [**用 Python 在 Raspberry Pi 上编程:激活运动检测上的 LED 和蜂鸣器**](https://www.pythoncircle.com/post/654/programming-on-raspberry-pi-with-python-activate-led-and-buzzer-on-motion-detection/)
*   [**用 Python 在树莓 Pi 上编程:树莓 Pi 重启时在电报通道上发送 IP 地址**](https://www.pythoncircle.com/post/651/programming-on-raspberry-pi-with-python-sending-ip-address-on-telegram-channel-on-raspberry-pi-reboot/)
*   [**使用 Python 和 Gmail 发送邮件**](https://www.pythoncircle.com/post/628/sending-emails-using-python-and-gmail/)