# pandoc 将 markdown 文件转换为 reveal.js 幻灯片的设置

> 原文：<https://dev.to/berry_clione/set-up-to-convert-a-markdown-file-to-revealjs-slides-by-pandoc-58n5>

# 环境

*   麦克 OS X 10.14.2 莫哈韦
*   zsh ( [哦我的 zsh](https://dev.to/berry_clione/switch-from-bash-to-zsh-and-install-oh-my-zsh-on-iterm2-18d9)

# 关于 reveal.js

reveal.js 可以让你轻松成为一个很酷的演讲者。
[https://revealjs.com/#/](https://revealjs.com/#/)

安装 reveal.js，通过一个 pandoc 命令从 markdown 文件制作 html 演示幻灯片。

参考文献。[https://github.com/hakimel/reveal.js/#full-setup](https://github.com/hakimel/reveal.js/#full-setup)

# 概述

1.  自制安装 pandoc
2.  通过 nodebrew 安装 node.js
3.  克隆揭示. js
4.  选中可将减价文件转换为 html 演示文稿幻灯片

* * *

# 如何设置

## 自制安装 pandoc

*   通过[自制](https://dev.to/berry_clione/install-homebrew-on-macos-mojave-4m3m)安装 pandoc，如下图。

```
$ brew install pandoc 
```

## 通过 nodebrew 安装 node.js

*   Node.js 尚未安装。

```
$ which node
node not found 
```

*   自制安装 nodebrew。

```
$ brew install nodebrew
==> Downloading https://github.com/hokaccha/nodebrew/archive/v1.0.1.tar.gz
==> Downloading from https://codeload.github.com/hokaccha/nodebrew/tar.gz/v1.0.1
######################################################################## 100.0%
==> Caveats
You need to manually run setup_dirs to create directories required by nodebrew:
  /usr/local/opt/nodebrew/bin/nodebrew setup_dirs

Add path:
  export PATH=$HOME/.nodebrew/current/bin:$PATH

To use Homebrew's directories rather than ~/.nodebrew add to your profile:
  export NODEBREW_ROOT=/usr/local/var/nodebrew

Bash completion has been installed to:
  /usr/local/etc/bash_completion.d

zsh completions have been installed to:
  /usr/local/share/zsh/site-functions
==> Summary
🍺   /usr/local/Cellar/nodebrew/1.0.1: 8 files, 38.6KB, built in 10 seconds 
```

*   检查 nodebrew 的安装。

```
$ nodebrew -v
nodebrew 1.0.1

Usage:
    nodebrew help Show this message
    nodebrew install <version>            Download and install <version> (from binary)
    nodebrew compile <version>            Download and install <version> (from source)
    nodebrew install-binary <version>     Alias of `install` (For backword compatibility)
    nodebrew uninstall <version>          Uninstall <version>
    nodebrew use <version>                Use <version>
    nodebrew list                         List installed versions
    nodebrew ls Alias for `list`
    nodebrew ls-remote                    List remote versions
    nodebrew ls-all                       List remote and installed versions
    nodebrew alias <key> <value>          Set alias nodebrew unalias <key>                Remove alias nodebrew clean <version> | all        Remove source file
    nodebrew selfupdate                   Update nodebrew
    nodebrew migrate-package <version>    Install global NPM packages contained in <version> to current version
    nodebrew exec <version> -- <command>  Execute <command> using specified <version>

Example:
    # install
    nodebrew install v8.9.4

    # use a specific version number
    nodebrew use v8.9.4 
```

*   添加 nodebrew 的路径。

```
$ echo 'export PATH=$HOME/.nodebrew/current/bin:$PATH' >> ~/.zprofile 
```

*   读取配置文件(，或重新启动 shell)。

```
$ source ~/.zprofile 
```

*   检查可用的 node.js 版本。

```
$ nodebrew ls-remote
v0.0.1    v0.0.2    v0.0.3    v0.0.4    v0.0.5    v0.0.6

v0.1.0    v0.1.1    v0.1.2    v0.1.3    v0.1.4    v0.1.5    v0.1.6    v0.1.7
v0.1.8    v0.1.9    v0.1.10   v0.1.11   v0.1.12   v0.1.13   v0.1.14   v0.1.15
v0.1.16   v0.1.17   v0.1.18   v0.1.19   v0.1.20   v0.1.21   v0.1.22   v0.1.23
v0.1.24   v0.1.25   v0.1.26   v0.1.27   v0.1.28   v0.1.29   v0.1.30   v0.1.31
v0.1.32   v0.1.33   v0.1.90   v0.1.91   v0.1.92   v0.1.93   v0.1.94   v0.1.95
v0.1.96   v0.1.97   v0.1.98   v0.1.99   v0.1.100  v0.1.101  v0.1.102  v0.1.103
v0.1.104
...
v11.0.0   v11.1.0   v11.2.0   v11.3.0   v11.4.0   v11.5.0   v11.6.0   v11.7.0
v11.8.0   v11.9.0

io@v1.0.0 io@v1.0.1 io@v1.0.2 io@v1.0.3 io@v1.0.4 io@v1.1.0 io@v1.2.0 io@v1.3.0
io@v1.4.1 io@v1.4.2 io@v1.4.3 io@v1.5.0 io@v1.5.1 io@v1.6.0 io@v1.6.1 io@v1.6.2
io@v1.6.3 io@v1.6.4 io@v1.7.1 io@v1.8.1 io@v1.8.2 io@v1.8.3 io@v1.8.4

io@v2.0.0 io@v2.0.1 io@v2.0.2 io@v2.1.0 io@v2.2.0 io@v2.2.1 io@v2.3.0 io@v2.3.1
io@v2.3.2 io@v2.3.3 io@v2.3.4 io@v2.4.0 io@v2.5.0

io@v3.0.0 io@v3.1.0 io@v3.2.0 io@v3.3.0 io@v3.3.1 
```

*   安装稳定(最新)版本的 node.js。但是，...

```
$ nodebrew install-binary stable
$ # nodebrew install-binary latest
Fetching: https://nodejs.org/dist/v10.15.1/node-v10.15.1-darwin-x64.tar.gz
Warning: Failed to create the file
Warning: /Users/xxxxxxxx/.nodebrew/src/v10.15.1/node-v10.15.1-darwin-x64.tar.gz:
Warning: No such file or directory
                                                                           0.0%
curl: (23) Failed writing body (0 != 1056)
download failed: https://nodejs.org/dist/v10.15.1/node-v10.15.1-darwin-x64.tar.gz 
```

*   必须在安装前设置 nodebrew，如下所示。

```
$ nodebrew setup
Fetching nodebrew...
Installed nodebrew in $HOME/.nodebrew

========================================
Export a path to nodebrew:

export PATH=$HOME/.nodebrew/current/bin:$PATH
======================================== 
```

*   设置好 nodebrew 之后，安装 node.js。

```
$ nodebrew install-binary stable
$ # nodebrew install-binary latest
Fetching: https://nodejs.org/dist/v10.15.1/node-v10.15.1-darwin-x64.tar.gz
######################################################################## 100.0%
Installed successfully 
```

*   检查 node.js 是否由 nodebrew 安装。

```
$ nodebrew ls v10.15.1

current: none
$ which node
node not found
$ node -v
zsh: command not found: node 
```

*   使 node.js 有效。

```
$ nodebrew use v10.15.1
use v10.15.1 
```

*   检查 node.js 验证。

```
$ node -v
v10.15.1
$ npm -v
6.4.1
$ which node
/Users/xxxxxxxx/.nodebrew/current/bin/node 
```

* * *

## 克隆揭示. js

*   克隆 reveal.js(，或者下载)。

```
$ cd your/work/directory
$ git clone https://github.com/hakimel/reveal.js.git
Cloning into 'reveal.js'...
remote: Enumerating objects: 11068, done.
remote: Total 11068 (delta 0), reused 0 (delta 0), pack-reused 11068
Receiving objects: 100% (11068/11068), 8.57 MiB | 82.00 KiB/s, done.
Resolving deltas: 100% (6115/6115), done. 
```

```
$ tree # at your/work/directory
.
├── reveal.js
│   ├── ...
... 
```

* * *

## 勾选将降价文件转换为 html 演示幻灯片

*   在你的/work/目录下创建一个 markdown 文件。

```
% Title
% Name
% yyyy/mm/dd

# Header1

---

### Header3

- foo
- bar
- foobar

---

### header

1\. foo
1\. bar
1\. baz

# HEADER

---

_foo_

__bar__

___baz___

[foobar_link](https://github.com/hakimel/reveal.js/#full-setup)

> foo bar  
> baz 
```

```
$ tree # at your/work/directory
.
├── input.md
└── reveal.js
    ├── ...
... 
```

*   用 pandoc 创建 reveal.js 的 html 幻灯片

```
$ pandoc -s -t revealjs -o output.html input.md
$ tree
.
├── input.md
├── output.html
└── reveal.js
    ├── ...
...

$ open output.html 
```

[![](img/a1adc131af9e507c7c5ed2f90f5f9b3b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5ZWzj5GE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/16lqkvalpbi8a2e7b5au.png)

*   为你喜欢的主题起别名。

```
$ alias revealjs='pandoc -s -t revealjs --variable transition=linear -V theme=blood -o '
$ revealjs output.html input.md
$ open output.html 
```