# 表单验证，你做错了，第二部分

> 原文：<https://dev.to/dexygen/form-validation-youre-doing-it-wrong-part-two-5545>

这是我昨天帖子[的后续。我将再次引用同一篇关于](https://dev.to/dexygen/form-validation-youre-likely-doing-it-wrong-37h9)[表单验证的糟糕状态](https://baymard.com/blog/inline-form-validation)的优秀文章，尽管我对下面的断言颇有微词:

> 当用户离开字段或达到预期的输入长度时，应该检查每个字段输入的有效性。

实际上，我认为他们更一般的立场是更好的指导方针:

> 在用户有机会完整地键入有效输入之前，不应该检查输入的有效性。"

这里强调的是我；我认为一个输入是否有效有时可以在之前*确定，“用户离开字段或者达到预期的输入长度。”为此，我创建了[的另一个要点](https://gist.github.com/dexygen/b0225fffe4e6d3b39c088a72e431e0dd/56f33d8023a9c7bfb316df47b9d2c000e43ab935)，这个要点演示了当用户离开字段时，使用可选的破折号加四位数字(zip-plus-4)*和*来验证邮政编码。*

具体来说，我使用了两个略有不同的“正则表达式”来实现这一点，一个用于匹配用户输入的零到五个开头数字和一个可选的破折号，然后是零到四个数字，一个用于匹配用户离开要求*准确地说是*五个开头数字的字段，然后是一个可选的破折号和*准确地说是*四个数字——实际输入是为了长期提交:

```
 const keyupRegex = new RegExp("^\\d{0,5}(-\\d{0,4})?$");
  const blurRegex = new RegExp("^\\d{5}(-\\d{4})?$"); 
```

下面是一个屏幕截图，显示用户可以输入部分正确的条目，而不会被打扰:

[![](img/096699146d048858717cd9d0eafd5ad5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--517Cb5kZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i83pss7rs67wkj9vck7x.png)

但是如果他们通过点击复选框模糊了邮政编码字段....

[![](img/fe4997515659da43e811637c7474ac7b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bGZhQY0t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s3qvzllm3lrl2is5c6kv.PNG)

但是，如果用户重新输入邮政编码字段，并开始输入可能有效的内容(另一个数字)，错误消息就会消失！

[![](img/39c243dcfd56b207c348668dd117ae8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qU0Glf9j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yybz5d18mj85m72qb5x0.png)

最后，如果他们输入了一个有效的输入，并且取消了复选框，同样错误也不会出现，这是应该的。

[![](img/5535662466761fa92cac502669026b60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QU9VN2GJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xmomkxodue8nm19l19g8.PNG)

这种技术同样可以用于电子邮件地址，尽管正则表达式会更复杂。希望你和我学得一样多！