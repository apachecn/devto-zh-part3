# Mac 配置

> 原文：<https://dev.to/dineshbhagat/mac-configurations-14ga>

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[dineshbhagat](https://github.com/dineshbhagat)/[MAC 配置](https://github.com/dineshbhagat/mac-configurations)

### 这是为开发而配置 mac 的集合/方法

<article class="markdown-body entry-content container-lg" itemprop="text">

[![forthebadge](img/c71c4d8d37780a362fd3302fe19707e5.png) ](https://forthebadge.com) **却是** [ ![forthebadge](img/bef788437d4d198919f97f83107ac079.png)](https://forthebadge.com)

### 配置

* * *

*   [配置](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#configurations)
    *   [ITER m2/端子配置](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#iterm2terminal-configurations)
        *   [Zsh 插件](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#zsh-plugins)
        *   [安装字体](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#install-fonts)
        *   [获得更好版本的手册页](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#get-better-version-of-man-pages)
        *   [安装 Ruby](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#install-ruby)
        *   [使 ls、ll 命令丰富多彩](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#make-ls-ll-commands-colorful-and-much-more-using-colorls)
        *   [在 powerlevel9k](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#powerlevel10k-on-top-of-powerlevel9k) 之上的可选 powerlevel10k
    *   [终端文件管理器](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#terminal-file-manager)
    *   [Python 设置](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#python-setup)
        *   [python 虚拟环境](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#virtual-environment-for-python)
    *   [开始设置](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#go-setup)
        *   [环境变量](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#environment-variables)
    *   [Groovy 设置](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#groovy-setup)
    *   [IntelliJ 想法设置](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#intellij-idea-settings)
    *   [MAC 中的多个 java 版本](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#multiple-java-versions-in-mac)
    *   [SDKMAN](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#sdkman)
    *   [钥匙串-密码](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#keychain-password)
    *   [视频教程](https://raw.githubusercontent.com/dineshbhagat/mac-configurations/master/#video-tutorials)

* * *



#### ITER m2/终端配置

注意:在进行以下安装之前，请确保您拥有*本地管理员权限*。

以下网站将帮助您设置 mac。

1.  安装来自 https://www.iterm2.com/[的 iterm2】和来自 https://brew.sh/](https://www.iterm2.com/)[的自制软件](https://brew.sh/)

2.  安装 brew-leaves.txt
    `brew install $(< brew-leaves.txt)`文件中列出的软件

3.  给 iterm2 添加主题: [Oh-my-Z](https://ohmyz.sh/)
    `sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)""`

4.  向 iterm2 添加额外的插件、主题和字体

    ##### Zsh 插件

    你可以在 zsh 的`.oh-my-zsh/custom/plugins`位置添加额外的插件

    ```
    cd $ZSH/custom/plugins;git clone https://github.com/zsh-users/zsh-syntax-highlighting.git && git clone https://github.com/zsh-users/zsh-autosuggestions && git clone https://github.com/zsh-users/zsh-completions
    ```

    Enter fullscreen mode Exit fullscreen mode

    ##### 安装字体

    [源代码-pro](https://github.com/adobe-fonts/source-code-pro)

    …

</article>

[View on GitHub](https://github.com/dineshbhagat/mac-configurations)