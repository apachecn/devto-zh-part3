# Linux 迷因领主的 Bash 脚本

> 原文：<https://dev.to/weirdmayo/bash-script-for-meme-lords-of-linux-5c6>

也许我对这个迷因来的有点晚，但它仍然值得到处去笑一笑。这是一个“提交数独”迷因。数独，作为 Sepuku(切腹自杀，自杀等)的误拼。).所以我为 meme 和某人写了一个小脚本，让他们在打字的时候多打几下。

```
commit(){
    if [[ "$1" == "sepuku" ]]; then logout fi
    if [[ "$1" == "sudoku" ]]; then sudo systemctl suspend fi
    if [[ "$1" == "lifent" ]]; then sudo shutdown -P now
    fi
    fi [[ "$1" == "tensei" ]]; then sudo shutdown -r now
    fi
} 
```

Enter fullscreen mode Exit fullscreen mode

所以现在你可以像老板`commit sepuku`
一样注销你的 SSH 或者关闭你的电脑`commit lifent`

如果您是 Linux/bash 新手，要使用这段代码，您必须将我上面写的函数粘贴到您的`~/.bashrc`文件的末尾。并激活对 rc 文件的更改，只需做`source ~/.bashrc`。

*   用鼻子吹气，因为大多数时候人们都是这样做的，而不是笑出声来。

## 更新

您可以通过复制粘贴以下命令将该脚本安装到您的`~/.bashrc`中:

```
curl https://gitlab.com/snippets/1787198/raw | cat >> ~/.bashrc 
```

Enter fullscreen mode Exit fullscreen mode