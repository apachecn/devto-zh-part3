# 重置与 git 相关的密码的一行程序

> 原文：<https://dev.to/agmm/one-liner-to-reset-the-password-associated-with-git-55h8>

*自我提醒*

重置与 git 相关的密码的一行程序。一旦运行，git 将提示用户在推送之前重新输入密码。在你更改 GitHub 密码的时候很有用。

```
git config --global --unset user.password 
```