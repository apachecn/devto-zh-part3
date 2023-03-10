# 一些有用的 Pacman 命令

> 原文：<https://dev.to/siatwe/some-useful-pacman-commands-278f>

*[原载于我的博客](https://siatwe.github.io/blog/index.html)*

这里有一些 Pacman 命令，我觉得很有用，可能不是每个人都知道:

*   该命令显示您自己安装的所有软件包，除了在安装 Arch 时安装的“base”和“base-devel”软件包:

```
pacman -Qei | awk '/^Name/ { name=$3 } /^Groups/ { if ( $3 != "base" && $3 != "base-devel" ) { print name } }' 
```

显示所有已安装的软件包及其各自的内存大小，从最小的开始:

```
pacman -Qi | awk '/^Name/{name=$3} /^Installed Size/{print $4$5, name}' | sort -h 
```

*   卸载所有不需要的软件包及其未使用的依赖项。偶尔清理一下这里和那里是很有用的:

```
sudo pacman -Rsn $(pacman -Qdtq) 
```

*   使用此命令，您可以显示通过 AUR 收到的所有包裹。(列出所有外国软件包，对于大多数用户来说，这意味着 AUR):

```
pacman -Qqm 
```

Pacman 存储提取的软件包，不会自动删除旧的或卸载的软件包。当然，这有几个好处:

*   所以你可以很容易地降级软件包。
*   已经卸载需要重装的包可以直接从缓存文件夹安装(当然前提是 repos 里没有更新的版本)。

但是，您应该到处清理这个缓存，以免它变得无限大。为此有一个叫做 paccache 的脚本。它卸载已安装和已卸载软件包的所有存储的软件包数据，最后三个除外。这通常就足够了:

```
sudo paccache -r 
```

*   也可以实现 paccache 的自动化。使用以下命令，paccache 每周删除一次缓存:

```
sudo systemctl enable paccache.timer --now 
```