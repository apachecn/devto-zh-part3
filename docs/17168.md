# 通过符号链接使 subl 命令有效，以打开崇高文本 3

> 原文：<https://dev.to/berry_clione/make-subl-command-valid-by-symbolic-link-to-open-sublime-text-3-1474>

> 想用 iTerm2 命令打开 Sublime Text 3。

*   在 iTerm2 上输入下面的命令，创建崇高文本 3 的符号链接

```
$ ln -s /Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl /usr/local/bin/subl 
```

*   执行链接命令以打开 Sublime Text 3

```
$ subl [file] 
```