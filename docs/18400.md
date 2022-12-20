# 测试…为你将来自己(和其他人)的代码正确性提供文档

> 原文：<https://dev.to/lancew/tests-documentation-for-your-future-self-and-others-of-code-correctness-4e4l>

想象一下…你开始工作，一个同事跑来对你说他们的软件有问题，一切都错了，救命！

拿起一杯咖啡，你坐在椅子上，打开令人讨厌的代码:

```
// Random Javascript module

function adds_up_to(nums, total) {
  var status = false;   
  for ( let first of nums) {
    for ( let second of nums ) {
    var sum = first + second;
        if (sum == total) {
        status = true;
    }
    }
  } 
  return status;
}
module.exports = adds_up_to; 
```

Enter fullscreen mode Exit fullscreen mode

嗯嗯……它返回“状态”。嗯什么？嗯，为什么？是不是应该返回“状态”，到底什么是“状态”？

你在那里抓耳挠腮，想知道问题出在哪里，更糟糕的是，你想知道为什么这个代码会存在，为什么它会这样做。

但幸运的是，你的过去的自己对你有一点点关心，留下了一点点测试来帮助你。

```
// Random test script

const adds_up_to = require('./adds_up_to');

test('adds up to', () => {

  expect(adds_up_to([10,15,3,7],17)).toBe(true);
  expect(adds_up_to([1,1,1,1,1,1,1], 4)).toBe(false);
  expect(adds_up_to( [1, 2, 3, 4, 5, 6, 7, 8, 9], 7 )).toBe(true);
  expect(adds_up_to([-1,2,-2],-3)).toBe(true);

}); 
```

Enter fullscreen mode Exit fullscreen mode

好的……一个很好的测试，让我们开始吧！

```
$ npm test

> adds_up_to@1.0.0 test /home/lance/dev/not_real_code
> jest

 PASS ./adds_up_to.test.js
  ✓ adds up to (3ms)

Test Suites: 1 passed, 1 total
Tests: 1 passed, 1 total
Snapshots: 0 total
Time: 0.597s, estimated 1s
Ran all test suites. 
```

Enter fullscreen mode Exit fullscreen mode

根据这个，测试通过了，所以代码在做你过去的自己想要做的事情。这是一件了不起的事情……但是我们仍然不清楚这个代码的意图是什么，我们也不确定这个代码是否“正确”。

我们所说的“正确”是指与“测试通过”或“代码按设计运行”不同的东西。正如 Itamar Turner-Trauring 最近写的那样，测试并不是让你的软件正确的灵丹妙药。

在上面的示例 Javascript 代码中，代码是有效的。测试证实了这一点。但是(假设的)同事告诉你这是不正确的。

**那么问题是什么呢？**

这里的问题是，测试确认方法/功能在技术层面上有效。我们已经测试了方法代码的工作情况……这非常好，解决了一大堆关于确保代码在技术实现层面上是正确的问题。

如果我们看上面的代码示例，我们不知道代码的上下文，就不容易确定代码要解决什么问题。在撰写本文时，开发人员了解问题以及问题存在的领域。但是没有在代码中包括这种理解(意味着功能和测试)，所以我们不能确定问题是否仍然相关，更不用说我们的方法是否正确了。

那么，我们可以做些什么来证明这是正确的呢？

这里的想法是我们想要证明我们正在解决一个问题。所以我想第一步是找出问题所在。

所以上面的代码所要解决的实际问题是通过非常有趣的“[日常编码问题](https://www.dailycodingproblem.com/?utm_source=rss&utm_medium=rss)”邮件列表:

```
Given a list of numbers and a number k, return whether any two numbers from the list add up to k.

For example, given [10, 15, 3, 7] and k of 17, return true since 10 + 7 is 17. 
```

Enter fullscreen mode Exit fullscreen mode

这就给出了一些上下文，所以第一步可能是将这些文本作为注释和/或函数复制并粘贴到测试中。这至少意味着你未来的自己可能会理解你想做什么。如果我们知道这对我们的业务或用户“为什么”重要，那就更好了。

```
// Random test script

const adds_up_to = require('./adds_up_to');

/*
Given a list of numbers and a number k, return whether any two numbers from the list add up to k.

For example, given [10, 15, 3, 7] and k of 17, return true since 10 + 7 is 17.
*/

test('adds_up_to', () => {
  // 17 = 10 + 7, so return true
  expect(adds_up_to([10,15,3,7],17)).toBe(true);

  // 4 != 1 +1, so return false
  expect(adds_up_to([1,1,1,1,1,1,1], 4)).toBe(false);

  // 7 = 6 + 1, so return true
  // 7 = 5 + 2, so that also would have made it return true
  expect(adds_up_to( [1, 2, 3, 4, 5, 6, 7, 8, 9], 7 )).toBe(true);

  // -3 = -1 + -2, so return true
  // Check if two negative numbers works ok
  expect(adds_up_to([-1,2,-2],-3)).toBe(true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这实际上并没有改变我们的测试输出，但是现在我们有了更多的上下文来提醒我们明年再看这段代码。我们添加了一堆评论。首先，我们陈述问题(或者我们现在所知道的)，我们也解释测试试图证明更多的东西。

阅读这篇文章，我们可以理解更多的代码的意图。即取任意两个数，将它们相加；它们是否等于提供的另一个数字。我们也解释了最后的测试，其中我们测试两个负数的边缘情况。

我们可以并且确实应该扩展和构建这些测试，这样测试本身就可以解释上下文:

```
// Random test script

const adds_up_to = require('./adds_up_to');

/*
Given a list of numbers and a number k, return whether any two numbers from the list add up to k.

For example, given [10, 15, 3, 7] and k of 17, return true since 10 + 7 is 17.
*/

test('given an array of values, if any two add up to the value provided then return true', () => {
  // 17 = 10 + 7, so return true
  expect(adds_up_to([10,15,3,7],17)).toBe(true);
});

test('given an array of values, if no two add up to the value provided then return false', () => {
  // 4 != 1 +1, so return false
  expect(adds_up_to([1,1,1,1,1,1,1], 4)).toBe(false);
});

test('given an array of values, if any two add up to the value provided then return true (this time more than one pair meet the criteria)', () => {
  // 7 = 6 + 1, so return true
  // 7 = 5 + 2, so that also would have made it return true
  expect(adds_up_to( [1, 2, 3, 4, 5, 6, 7, 8, 9], 7 )).toBe(true);
});
test('given an array of values, if any two add up to the value provided then return true (even if numbers are negative)', () => {
  // -3 = -1 + -2, so return true
  // Check if two negative numbers works ok
  expect(adds_up_to([-1,2,-2],-3)).toBe(true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常冗长的例子，仍然没有解释业务需求；然而，我们正在解释我们打算实现的目标。所以对我来说，如果你想确保正确性，这是一个更好的价值测试。下面是 Jest 输出:

```
$ npm test

> adds_up_to@1.0.0 test /home/lancew/dev/challenges/1/js
> jest

 PASS ./adds_up_to.test.js
  ✓ given an array of values, if any two add up to the value provided then return true (3ms)
  ✓ given an array of values, if no two add up to the value provided then return false
  ✓ given an array of values, if any two add up to the value provided then return true (this time more than one pair meet the criteria)
  ✓ given an array of values, if any two add up to the value provided then return true (even if numbers are negative)

Test Suites: 1 passed, 1 total
Tests: 4 passed, 4 total
Snapshots: 0 total
Time: 0.73s, estimated 1s
Ran all test suites. 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，测试的文本/名称现在明确地说明了它试图证明什么。因此，如果有一个测试失败了，希望在你阅读测试本身之前，这个消息就能给你提供背景信息。

但是，这仍然只是证明技术上的正确性；我们真正想要证明的是，这些代码提供了它所要达到的商业利益。所以我们需要去找个人谈谈，弄清楚这个代码实际上是干什么的，在这个假设的例子中，答案是:

> Hi Lance,
> 
> 在订单系统中，我们允许人们使用代金券，如果他们有两张代金券加起来达到购买价值；然后我们不需要触发信用卡支付过程，我们只需运送产品。
> 
> 规则是他们一次只能使用两张优惠券，而且必须与总价完全一致。
> 
> 销售经理卡罗尔。

好了…这给了我们业务上下文，所以让我们重写测试来表达这个:

```
// Random test script

const adds_up_to = require('./adds_up_to');

/*
Given a list of numbers and a number k, return whether any two numbers from the list add up to k.

For example, given [10, 15, 3, 7] and k of 17, return true since 10 + 7 is 17.
*/

test('Return true if two voucher value add up to the total price', () => {
  // 17 = 10 + 7, so return true
  expect(adds_up_to([10,15,3,7],17)).toBe(true);
});

test('Return false if no two voucher values add up to the total price', () => {
  // 4 != 1 +1, so return false
  expect(adds_up_to([1,1,1,1,1,1,1], 4)).toBe(false);
});

test('Return true if two voucher value add up to the total price (even if more than one pair match)', () => {
  // 7 = 6 + 1, so return true
  // 7 = 5 + 2, so that also would have made it return true
  expect(adds_up_to( [1, 2, 3, 4, 5, 6, 7, 8, 9], 7 )).toBe(true);
});

test('???????????? Negative Voucher values ???????????', () => {
  // -3 = -1 + -2, so return true
  // Check if two negative numbers works ok
  expect(adds_up_to([-1,2,-2],-3)).toBe(true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

等一下！

突然，我们对负数的测试在业务标准的正确性方面不再有意义。企业不会到处给顾客价值为负数的代金券。我们也不允许负的总购买价格。

我们的“代码”是正确的，但只是在技术层面上。在业务层面上，这是非常错误的，作为开发人员，如果没有从销售经理那里获得的上下文并写入到我们的测试中，我们是不会知道的。

我们过去的自己，写了一个函数，它能很好地处理正的凭证值，但是如果我们的代码能保护我们免受无效凭证值的影响，也许会更好。

这也可能让我们思考，如果一张代金券加起来就是总数呢？因此，我们可以询问业务问题，并确保我们的功能确实满足业务需求。

**概要:**

所以在这个虚构的例子中，我们实际上涵盖了一个作为开发人员我实际上一直遇到的问题。有时我们在没有表达代码的意图和上下文的情况下编写代码。

如果我们编写测试来表达意图和上下文，我们可能会发现去年的负凭证问题。现在我们已经找到了它，我们可能要去检查交易数据库，看看是否有一些鬼鬼祟祟的客户发现了漏洞。

作为第一次遇到这种代码的开发人员，我们现在有很好的机会不做一些愚蠢的事情，因为我们可以看到代码的商业案例是什么。这节省了我们的时间，也减少了我们出错的机会，因为测试明确表达了更多关于正确行为的商业理念。

根据正确的业务理念，很难或者可能不可能证明正确性，除非我们编写测试来证明正确操作的业务理念。

对于正在阅读这篇文章的人来说，你可能对这篇文章中的观点有一种熟悉感…为什么？因为这也是 Eric Evans 推广的[领域驱动设计(DDD)](https://amzn.to/2zTipr2?utm_source=rss&utm_medium=rss) 的一个核心思想。

**作业:**

读到这里，也许现在是一个很好的时机去看看你的代码库中正在工作的一个测试，并决定你是否认为这些测试是在测试业务正确性或代码正确性。

让我知道你发现了什么。