# 你会在代码中留下笑话吗？如果有，请分享！

> 原文：<https://dev.to/ahmedmusallam/do-you-leave-jokes-in-your-code-if-so-please-share-2hkb>

有时，我会在代码中留下一些糟糕的笑话，以下是我不久前构建的备份工具中的一些例子:

当用户试图删除备份时:

```
console.log(`Still wanna do it? just checkin'...`) 
```

当试图删除备份但发生错误时:

```
spinner.fail(`ummm, yeah.. this is awkward; we could't delete the backups for some reason`) 
```

当用户试图删除备份，但随后在提示下取消时:

```
console.log(`Whew, nothing happened. That must feel good, eh?`.green) 
```

出问题时:

```
spinner.fail('womp womp... some error occurred *rolls eyes*') 
```

其他时候，我会给一段看起来很奇怪的代码添加有趣的注释，只是因为..

我很好奇其他开发者会不会这么做，你会做类似的事情吗？搞笑评论？搞笑的方法/变量名？或者你给用户看的搞笑消息？请分享！

*取决于你问谁；)