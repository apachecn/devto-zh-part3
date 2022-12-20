# Javascript 强制哪里出错了？

> 原文：<https://dev.to/aloksdiptim/where-javascript-coercion-went-wrong-2dmj>

强制是在不知不觉中使用 Javascript 引擎将一种数据类型转换成另一种数据类型。Javascript 中为数不多的惊喜之一。现在，这对于不同的操作符和数据类型有不同的表现，例如:

*   +运算符
*   -操作员
*   布尔代数学体系的
*   等号
*   数组

这主要是因为 javascript 变量的动态类型声明。 **var** 的使用。

随着 c#等其他编程语言的使用，如果我们使用数字和字符串作为数据类型，那么，编译时应该在运行时之前向我们显示错误。

因此，让我们深入研究 Javascript 强制的每个实例:

1.  在实践中，+运算符返回字符串，因此 Javascript 将操作数强制转换为字符串。例如:3+“90”将返回 390
2.  负号充当一个数字，它将操作数强制为一个数字，例如:90-“3”将返回 87
3.  布尔，是的！--> and 为“与”运算符；它返回第二个值，因为它总是假的；
    *   或/和逻辑门
    *   Apple || "orange "返回 Apple，这是 or 运算符的第一个值，也因为它是 true。
    *   如果它是(undefined| ""| 0| false) || "orange "，则返回 orange。
    *   所以你可以写一个这样的函数:`Function Whatsup(nickname){ Nickname = nickname || “Stranger.”; console.log(“Hey ” + nickname); } Whatsup();` 因为昵称是未定义的，然后是假的，控制台将打印出“嘿陌生人。”->> and for the “and” operator; It returns the second value because it’s always false;
4.  相等检查值和类型==检查值，因此强制仍然发生。`If (44 == “44”)`将返回 true，但如果使用三元运算符等号，它将返回 false。例如:如果`(44 === “44”)`返回 false
5.  对于运算符 Ex，数组将返回 Nan `[“Right Now”] - [“Later”]`将返回 Nan(不是一个数字),而`[“Right Now”] + [“Later”]`将连接并立即以字符串形式返回

记住+运算符总是返回字符串作为它们的值。

所以在一个例子中，我们有:

它将返回 true 而不是 false，因为 javascript 将其视为:
`console.log(true < 3);`
“true”将被强制为 1，比较时将为 1 < 3，这将为真。

以下是一些怪异的强迫行为。

它们最好用严格的等式操作符来解决，===

但是话又说回来，强制可能不仅与比较有关，还与 javascript 如何检查布尔值有关。
要检查 Javascript 如何强制输出，请检查 Number(" ")、Number()或 Boolean(未定义)并查看它对给定结果强制了什么。
我可以查一下，
 `boolean("0"); that will be coerce to false
Boolean(undefined); will be coerce to false
Number("3") will be coerce to 3;
Number("Hello") will be coerce to NaN` 

检查优先级也很重要。

谢谢，这都是关于强迫的。

如果你爱/喜欢，请分享/评论。谢谢！