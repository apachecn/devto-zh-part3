# 保存 nssecurecode-decoding for dummy

> 原文：<https://dev.to/zeitschlag/save-nssecurecoding-decoding-for-dummies-8a6>

在我上一次错误的假设——我不需要查找容易的东西——之后，我又偶然发现了这个假设。本周，我没能以正确的方式实现`NSSecureCoding`。最后，我学到了一些东西。

我想用[`NSSecureCoding`](https://developer.apple.com/documentation/foundation/nssecurecoding?language=objc)[`NSKeyedArchiver`](https://developer.apple.com/documentation/foundation/nskeyedarchiver?language=objc)和 [`NSKeyedUnarchiver`](https://developer.apple.com/documentation/foundation/nskeyedunarchiver?language=objc) 【持久化一些数据。我以前没用过`NSSecureCoding`，但是嘿，这能有多难？

1.  只需实现[`-encodeWithCoder:`](https://developer.apple.com/documentation/foundation/nscoding/1413933-encodewithcoder?language=objc)[`-initWithCoder:`](https://developer.apple.com/documentation/foundation/nscoding/1416145-initwithcoder?language=objc)[`supportSecureCoding`](https://developer.apple.com/documentation/foundation/nssecurecoding/1855946-supportssecurecoding?language=objc)。
2.  用`NSKeyedArchiver`编码，用`NSKeyedUnarchiver`解码。
3.  没有三个

这些都是我的假设，但它不起作用——多么令人吃惊啊！🎉。如果我事先阅读文档，我就可以节省几个小时。

苹果的文档和 NSHipster 都声明:

> 具体来说，覆盖`-initWithCoder`并符合 NSSecureCoding 的类应该使用`-decodeObjectOfClass:forKey:`而不是[`decodeObjectForKey:`
> 
> 来源:](https://developer.apple.com/documentation/foundation/nscoder/1418185-decodeobjectforkey?language=objc) [NSHipster](https://nshipster.com/nssecurecoding/)
> 
> 覆盖了`initWithCoder:`的对象必须使用 [`decodeObjectOfClass:forKey:`](https://developer.apple.com/documentation/foundation/nscoder/1442558-decodeobjectofclassforkey?language=objc) 方法解码任何包含的对象。
> 
> 来源:[苹果关于`NSSecureCoding`的文档](https://developer.apple.com/documentation/foundation/nssecurecoding?language=objc)

我的错误是只使用`-decodeObjectForKey:`进行解码。对于一个`NSString`，一切都很顺利。当我在想，为什么我不能解码我的编码`NSData`时，我发现了我的错误。调试器中有一些神秘的错误消息，但我仍然怀疑我的代码:这太容易了，甚至不值得堆栈溢出这个问题！

过了一段时间，偶然看了文档。我发现了自己的误解，解决了问题，突然间，一切都如预期的那样工作了。最后，我觉得自己相当愚蠢，尽管我学到了一些东西:

*   使用`NSSecureCoding`解码的工作原理
*   伙计们，看看这该死的文档！NSHipster 可能也值得一读。
*   不要忽略了小小的“不要假设，要证明！”-我屏幕旁边的便利贴。

感谢阅读！