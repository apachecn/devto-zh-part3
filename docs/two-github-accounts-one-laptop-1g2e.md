# 两个 github 帐户一台笔记本电脑

> 原文：<https://dev.to/sroy8091/two-github-accounts-one-laptop-1g2e>

# 问题陈述

当我的个人笔记本电脑离开这个世界时，一个独特的问题出现了，即使在许多软件和硬件修复后，我也无法修复它。现在只剩下我的办公室笔记本和悲伤。

所以基本上我的个人 github 账户有我个人笔记本电脑的 ssh 密钥，而工作的 github 账户有我办公室笔记本电脑的 ssh 密钥。现在，我需要用办公室的笔记本电脑访问我的个人 github 账户。但是但是但是.....您只能在一个 github 帐户中添加一个 ssh 密钥。那么现在该怎么办呢？我如何用同一台笔记本电脑管理两个账户？

# 解

这个问题有一个非常简单有趣的解决方案。我会一步步解释。

### 第一步:-

给你办公室的笔记本电脑添加一个新的 ssh 密钥对

```
ssh-keygen -t rsa -f ~/.ssh/id_rsa.home
ssh-keygen -t rsa -f ~/.ssh/id_rsa.office 
```

Enter fullscreen mode Exit fullscreen mode

这将生成两个名称不同的 ssh 密钥对，用于识别个人 github 帐户和官方帐户的密钥。好了，现在如何配置哪个账户使用哪个密钥呢？

### 第二步:-

现在是编辑 ssh 配置文件的时候了，这样它就可以根据它连接到的服务器选择 ssh 密钥

```
# Personal GitHub account:
Host home-github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa.home
  PreferredAuthentications publickey
  PasswordAuthentication no
  IdentitiesOnly yes

# Work GitHub account:
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa.office
  PreferredAuthentications publickey
  PasswordAuthentication no
  IdentitiesOnly yes 
```

Enter fullscreen mode Exit fullscreen mode

从“#”开始的行通常被视为注释。然后是几个用于 ssh 的主机名、用户和文件的键值对，以及几个额外的设置。
还有一个圈套。在本地 repo 中添加远程 url 时，我们必须根据我们在配置文件中提供的主机名修改 url。所以比如说我的远程网址是

`git@github.com:sroy8091/...`

哪个是我的 perosnal github 账号这个会变成

`git@home-github.com:sroy8091/...`

但是提交时 github 用户名怎么办？？

### 第三步:-

我们可以通过添加一些别名来更改 git 配置，这样 commit 将根据帐户类型(官方和个人)拥有用户电子邮件 id

```
#git alias
alias gitmeHome='git config user.email sroy8091@gmail.com'
alias gitmeWork='git config user.email sumit.roy@bigbasket.com' 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。每次你需要切换领域的时候，只要调用这个别名，你就可以控制它了。

### 注意事项:-

*   我只为两个 git 帐户演示了这一点，但这可以应用于区分任何两个服务器或任何数量的服务器。事实上，对于每一个，我们应该有不同的 ssh 密钥，这样，即使一个受到损害，我们仍然有其他的安全。
*   为了更改 git 配置文件，如果有必要，我们也可以更改用户名。
*   我写博客快一年多了，所以请原谅我的错误。

我希望我清楚地解释了这个话题，如果你有任何疑问，请在下面的评论中写下。

博客评论人:迪皮卡·维尔马

| 喜欢我的帖子吗？ |
| --- |
| [![](img/f4f06ea384c5e485d3474b718bbe6f95.png)](https://ko-fi.com/A0A5WBC1) |