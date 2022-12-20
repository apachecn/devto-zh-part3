# 轻松维护多个 GitHub 帐户

> 原文：<https://dev.to/mrprofessor/maintaining-multiple-github-accounts-with-ease-4eld>

我最近离开了一家大型 IT 公司，去了一家很有前途的初创公司。我被要求更改我的 GitHub 处理程序名，因为对他们来说这个名字太酷了(我认为)。我没有换，而是用我公司的邮箱创建了另一个账户。

现在我有个问题。每天当我回到家开始研究我自己的项目时，我不得不在 git config 中手动设置我的用户名和电子邮件 id，以便在图表中反映我的贡献，但大多数时候我都忘了这样做。

所以我创建了一些别名来切换我的两个句柄。

```
# Set user 1 as current user
gitfirst() {
    git config --global user.email 'xxx.yyyy@gmail.com' && git config --global user.name 'mrprofessor'
    gituser
}

# Set user 2 as current user
gitsecond() {
    git config --global user.email 'zzzzz@corporation.com' && git config --global user.name 'rudrabot'
    gituser
}

# Print current user
gituser() {
    git config --global user.name && git config --global user.email
} 
```

我每天从这次黑客攻击中节省下来五分钟..花费在...我不知道。

再见。

这篇文章最初发表于 [rudra.dev](https://rudra.dev)