# 用 reduce 对数组进行分组排序

> 原文：<https://dev.to/jacobmparis/sorting-an-array-into-groups-with-reduce-4mi3>

我帮助运行 discord 服务器 Devcord，并花费大量时间帮助新手程序员解决技术难题。从现在开始，我将把我在那里提供的任何帮助整理成简短的博客帖子，这样信息就不会被淹没在谈话的海洋中。

这里我有一个用户，他有一个按域名排序的电子邮件地址列表。

```
const emails = [
  "alice@gmail.com"
  "bob@gmail.com"
  "carol@yahoo.ca"
  "doug@hotmail.com"
  "ellie@protonmail.com"
]; 
```

Enter fullscreen mode Exit fullscreen mode

虽然有很多方法可以解决这个问题，但我更喜欢使用 Array.prototype.reduce
的函数模型

```
const sortedEmails = emails.reduce((groups, email) => {
  const splitString = email.split('@');
  const account = splitString[0];
  const domain = splitString[1];

  if(!groups[domain]) groups[domain] = [];

  groups[domain].push(account);
  return groups;
}, {}); 
```

Enter fullscreen mode Exit fullscreen mode

reduce 函数迭代数组的每个元素，并将返回对象传递给下一次迭代。`accumulator`(此处命名为`groups`)最初被设置为一个`{}`空对象。

对于每封电子邮件，我们将它分解为每个帐户和域的变量。如果我们当前的域还不属于我们的组，将其初始化为一个空数组。

然后将新的帐户名添加到组中，并使用`return groups`将其传递到下一个迭代中。

```
{
  "gmail.com": ["alice", "bob"],
  "yahoo.ca": ["carol"],
  "hotmail.com": ["doug"],
  "protonmail.com": ["ellie"]
} 
```

Enter fullscreen mode Exit fullscreen mode

应该注意的是，这段代码并不完全适合生产环境。虽然几乎每个电子邮件地址都只有一个`@`符号，而且这对所有的电子邮件地址都适用，但也有多个有效的电子邮件地址。解析有效电子邮件地址的全部规范超出了本文的范围。