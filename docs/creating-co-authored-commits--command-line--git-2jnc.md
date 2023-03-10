# 创建合作提交-命令行- git

> 原文：<https://dev.to/blackode/creating-co-authored-commits--command-line--git-2jnc>

[![Photo by [NeONBRAND](https://unsplash.com/photos/mqoLpeeYBic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/thanking-co-worker?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)](img/00538fd393b669364dc3617227fd7d07.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--867GQ24V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/10368/1%2AHgp04st2WJ0oFDnR3ED1QA.jpeg) *照片由 [NeONBRAND](https://unsplash.com/photos/mqoLpeeYBic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [Unsplash](https://unsplash.com/search/photos/thanking-co-worker?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)* 

给⭐️应得的荣誉

## 简短的故事

假设，你正在做一个使用 git 工具进行版本管理的项目，这是一个团队项目，你会看到多个**作者**贡献代码。

你被分配了一个**任务**，你从**的一个合作程序员**那里得到了**的帮助**来完成编码。

如果你真的关心帮助，并且想**信任合作程序员**，那么你可以通过让**合作作者提交**来做到这一点。

# *给程序员学分，肯定他的另一个帮助——黑码*

## 将合作者添加到提交前需要了解的事项

1.  合著者电子邮件
2.  合著者对**的承诺算作贡献——使用与其 Github 账户相关的**电子邮件
3.  如果合著者的电子邮件是私人的，使用 GitHub 提供的不回复邮件来保护他们的隐私。

## 提示

### 帮助合著者找到他们未回复的电子邮件并分享

设置>电子邮件

[![no-reply github email finding](img/65d0645f10faf421252dc01ba1aa1605.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ive9WSU8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AUzCb4YG21OVWcaYvWkNi6g.gif)

## 合著-提交

这里没有你不知道的事情。但是，我们的做法不同。

就像您对常规提交消息所做的一样，在添加有意义的描述后，您需要添加两行空白的新行，而不是添加结束引号”。

```
git commit -m "New style of coding
>
> 
```

[![](img/23b8c90fa7a2e7b3c3438b4b5667b51d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gtPRBl9W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3812/1%2ApHLOMkCghw4KtEBq01BKSw.png)

在提交消息的下一行，键入
`Co-authored-by: author-name <name@xxx.com>`以及每位合著者的具体信息。在合著者信息后，加一个右引号。

### 添加多个合著者

在结束引号之前，给每个合著者提供他们自己的行和合著者:提交预告片。

[![](img/fbbb6172de199e3a5b00d0f4961ba163.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mjB0vsvu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3810/1%2ALxekoycPfAO68WeWPCcnaA.png)

在添加了漂亮的提交消息后，在下一次推送中，该消息会与合著者一起出现

[![](img/56844ba6f4c5d63806baa46aefd957cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lqV6HiCF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3794/1%2AlPELsnwpAXCtI1p24_9ksg.png)

## 现场执行

[![](img/dbb4b083edeaacce3d33e0cf4e48d315.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h0ta5YSY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A-5aw23mL1uOH2TkVABs1HQ.gif)

希望你喜欢它们。感谢阅读…

## 加入我们的电报频道

[![[https://t.me/blackoders](https://t.me/blackoders)](img/fe7186a7f8b7cded5d8ebbad1d535deb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u75ZpDh0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AgpCp7vuRX80aWxRQy7sWVA.png)

**编码快乐！**