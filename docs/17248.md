# 从 bash 切换到 zsh，并在 iTerm2 上安装 oh-my-zsh

> 原文：<https://dev.to/berry_clione/switch-from-bash-to-zsh-and-install-oh-my-zsh-on-iterm2-18d9>

> 在 Mac OS 10.14.2 Mojave 上首次设置 shell。
> 安装 oh-my-zsh。

## env。

*   Mac OS 10.14.2 Mojave
*   iTerm2 内部版本 3.2.6

* * *

## 如何安装

### 从 bash 切换到 zsh

*   通过下一个命令将 shell 更改为 zsh...

```
$ chsh -s /bin/zsh 
```

### 安装 oh-my-zsh

*   通过下面的命令安装 oh-my-zsh

```
$ curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   146  100   146    0     0    234      0 --:--:-- --:--:-- --:--:--   234
100  3952  100  3952    0     0   4481      0 --:--:-- --:--:-- --:--:--  4481
Cloning Oh My Zsh...
Cloning into '/Users/foo/.oh-my-zsh'...
remote: Enumerating objects: 1000, done.
remote: Counting objects: 100% (1000/1000), done.
remote: Compressing objects: 100% (920/920), done.
remote: Total 1000 (delta 23), reused 846 (delta 16), pack-reused 0
Receiving objects: 100% (1000/1000), 643.21 KiB | 3.28 MiB/s, done.
Resolving deltas: 100% (23/23), done.
Looking for an existing zsh config...
Using the Oh My Zsh template file and adding it to ~/.zshrc
         __                                     __
  ____  / /_     ____ ___  __  __   ____  _____/ /_
 / __ \/ __ \   / __ `__ \/ / / /  /_  / / ___/ __ \
/ /_/ / / / /  / / / / / / /_/ /    / /_(__  ) / / /
\____/_/ /_/  /_/ /_/ /_/\__, /    /___/____/_/ /_/
                        /____/                       ....is now installed!

Please look over the ~/.zshrc file to select plugins, themes, and options.

p.s. Follow us at https://twitter.com/ohmyzsh.

p.p.s. Get stickers and t-shirts at https://shop.planetargon.com. 
```

*   退出 shell，重新打开 iTerm2，然后就可以使用 oh-my-zsh 了

* * *

## 设定哎呀

### 主题

*   在`~/.zshrc`中编辑 oh-my-zsh 主题

*   比较[https://github.com/robbyrussell/oh-my-zsh/wiki/themes](https://github.com/robbyrussell/oh-my-zsh/wiki/themes)

```
#ZSH_THEME="robbyrussell"
ZSH_THEME="tonotdo" 
```

### 历史

*   在`~/.oh-my-zsh/lib/history.zsh`中编辑历史尺寸

```
HISTSIZE=500000
SAVEHIST=100000 
```