# 防止意外推送到公共存储库的一行程序

> 原文：<https://dev.to/agmm/one-liner-to-prevent-pushes-to-public-repositories-4e97>

*自我提醒*

禁用远程推送的一行程序。有助于防止意外推送到公共存储库。

```
git remote set-url --push origin no_push 
```