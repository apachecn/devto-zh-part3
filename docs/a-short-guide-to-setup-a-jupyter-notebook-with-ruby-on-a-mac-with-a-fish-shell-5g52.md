# 为 Ruby 设置 Jupyter 笔记本的简短指南(在 mac 上，带有鱼壳)

> 原文：<https://dev.to/vrinek_94/a-short-guide-to-setup-a-jupyter-notebook-with-ruby-on-a-mac-with-a-fish-shell-5g52>

### 0。安装自制软件

有关说明，请参见 [https://brew.sh](https://brew.sh)

### 0。以某种形式安装 Ruby

我建议 [rbenv](https://github.com/rbenv/rbenv)

### 1。从 https://www.anaconda.com/downloads[下载蟒蛇](https://www.anaconda.com/downloads)

### 2。安装☝️ (VS 代码是可选的，可以随意跳过)

### 3。`set -g fish_user_paths /anaconda3/bin/ $fish_user_paths`

### 4。用`jupyter notebook`测试

*   它应该会打开一个工作目录的浏览器窗口
*   接下来的步骤将添加一个“Ruby”选项到“新建>笔记本”菜单(右上)

### 5。安装 iRuby

说明改编自[https://github.com/sciruby/iruby#homebrew](https://github.com/sciruby/iruby#homebrew)

```
brew install automake gmp libtool wget
brew install zeromq --HEAD
brew install czmq --HEAD

set -x LIBZMQ_PATH (brew --prefix zeromq)/lib
set -x LIBCZMQ_PATH (brew --prefix czmq)/lib

gem install cztop
gem install iruby --pre
iruby register --force 
```

### ⑥。重启你的 Jupyter 笔记本(在终端中运行，然后再次启动)

# 🎉