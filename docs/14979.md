# 为隐藏文件创建符号链接

> 原文：<https://dev.to/cbillowes/create-a-symlink-for-hidden-files-57oc>

我想为除隐藏目录外的所有隐藏文件创建一个符号链接。我想把我的主目录中的配置文件放到版本控制中。

我需要排除隐藏的目录，因为它们包含二进制文件等等。

使用这个 liner，我可以为我的主目录中的每个隐藏文件创建一个到我的工作(或目标)目录的符号链接。

```
for f in .*; do if [[-f $f]]; then ln -s /home/me/$f /home/me/working/directory/$f; fi; done 
```

Enter fullscreen mode Exit fullscreen mode

我从我的源目录执行这个。

我可以验证我的符号链接存在于我的目标目录

```
cd /home/me/working/directory
ls -lah 
```

Enter fullscreen mode Exit fullscreen mode

我应该会看到如下几行:

```
lrwxrwxrwx. 1 me me 26 Jan 27 10:49 .zsh_history -> /home/me/.zsh_history
lrwxrwxrwx. 1 me me 20 Jan 27 10:49 .zshrc -> /home/me/.zshrc 
```

Enter fullscreen mode Exit fullscreen mode