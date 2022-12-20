# 如何在 Manjaro Linux 中为 Calibre 启用夜间模式

> 原文：<https://dev.to/lobo_tuerto/how-to-enable-night-mode-for-calibre-in-manjaro-linux-204b>

默认情况下，Manjaro Linux i3 采用深色主题设置。

我非常欣赏的东西。

当你安装 **Calibre** 并执行它的时候，你会发现它并没有使用系统的主题。

不过，告诉它这样做真的很容易。

你只需要设置`CALIBRE_USE_SYSTEM_THEME` env 变量，就像这样:

```
echo -e '\nexport CALIBRE_USE_SYSTEM_THEME=1' >> .profile 
```

要立即试用，请导出`CALIBRE_USE_SYSTEM_THEME` var 并推出**口径**,搭配:

```
export CALIBRE_USE_SYSTEM_THEME=1
calibre& 
```

如果你想让它通过`d-menu`(超级+ D) *退出*和*登录*回到会话。

就是这样！