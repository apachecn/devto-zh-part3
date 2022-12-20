# 如何用 Travis CI 和 Minted 构建 LaTeX

> 原文：<https://dev.to/renegadecoder94/how-to-build-latex-with-travis-ci-and-minted-39ff>

老实说，这篇文章有点夸张，但我认为它可以帮助别人。如果您想知道如何使用 Travis CI 和 Minted 构建 LaTeX 文档，那么您来对地方了。

## 问题描述

如果你想知道我是如何陷入这种困境的，[我是一名博士生](https://therenegadecoder.com/blog/journey-to-a-phd/)，我刚刚决定开始学习 LaTeX，因为它似乎很有用。然而，在这个过程中，我决定结合好的软件工程原则，比如版本控制和持续集成。事实证明，后者非常非常具有挑战性。

特别是，我在寻找一种使用 GitHub 内置的 Travis CI 生成 LaTeX PDFs 的方法。虽然这本身就是一个挑战，但我自己决定通过添加一个名为 minted 的包来让事情变得更有趣，这个包需要一些外部依赖。也就是说，我需要 Python 访问、Pygments 库和 shell 访问。事实证明，这比你想象的更具挑战性。

## 如何构建乳胶

通常，我们可以使用任意数量的 LaTeX 工具来构建 LaTeX。对我来说，这意味着在我的 Windows 10 机器上下载 MikTex 和 Perl。之后，我下载了 Atom 的 LaTeX 插件，然后开始工作。

更正式地说，我使用以下步骤来为 LateX 构建设置 Windows 环境:

1.  [下载并安装 MikTex](https://miktex.org/)
2.  [下载并安装草莓 Perl](http://strawberryperl.com/)
3.  [下载并安装 Python](https://www.python.org/)
4.  [下载并安装 Atom](https://atom.io/)
5.  [下载并安装 latex 包](https://atom.io/packages/latex)
6.  启用外壳脚本的 latex 插件设置中的复选框

在那之后，你应该可以起床跑步了。当然，我可能会遗漏一些依赖项，因为我的系统上已经安装了一些。

对于所有的依赖项设置，构建工作流如下所示:

1.  编写并保存文档更改
2.  转到包> LaTeX >重建
3.  利润

老实说，一旦安装好所有的东西，这真的再简单不过了。毕竟，MikTex 负责安装您所缺少的包，所以 LaTeX 依赖应该不是问题。当然，请在评论中告诉我其他情况。

## 如何自动化 LaTeX 构建

不幸的是，当涉及到 Travis CI 时，上述方法是不切实际的。毕竟，我们希望能够在每次构建时使用命令行安装所有这些组件。

幸运的是，[有一个从事持续集成的 LaTeX 团队](https://github.com/PHPirates/travis-ci-latex-pdf),所以已经有了几种持续集成的方法。也就是说，尽管他们很努力，我还是很难站起来跑步。如果你在寻找世界上最悲伤的提交列表，[看看我的拉请求，只是为了让构建工作起来](https://github.com/jrg94/CSE6431/pull/1)。总共有大约 82 次提交，其中一半以上是失败的构建。

当然，我也不指望你去挖掘我的努力。相反，我想为你提供解决方案。

### YAML

对于每个 Travis CI 构建，您需要一个 YAML 文件来告诉服务器如何构建您的代码。在这种情况下，我使用的 YAML 如下:

```
language: generic
# Dependency of the minted package
before_install:
  - sudo apt-get install python-pygments
install:
  - source ./texlive/texlive_install.sh
cache:
  directories:
    - /tmp/texlive
    - $HOME/.texlive
# Change working directory so including files will work
before_script: cd $TRAVIS_BUILD_DIR/assignment01/doc/
script:
  - pdflatex --shell-escape ./assignment01.tex
deploy:
  provider: releases
  api_key:
    secure: ${GITHUB_TOKEN}
  file:
    - ./assignment01.pdf
  skip_cleanup: true
  on:
    tags: true
    branch: master
notifications:
  email: false 
```

Enter fullscreen mode Exit fullscreen mode

打破这一点，有几件事要注意。首先，重要的是要意识到我们不能指定一种语言，因为 Travis CI 没有本地 LaTeX 构建支持。相反，我们必须使用一个通用的设置:

```
language: generic 
```

Enter fullscreen mode Exit fullscreen mode

语言设置完成后，我们可以通过运行安装前命令来选择 Python Pygments 包，它有助于语法突出显示:

```
before_install:
  - sudo apt-get install python-pygments 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们在本地 shell 脚本上运行另一个安装命令——稍后会详细介绍:

```
install:
  - source ./texlive/texlive_install.sh 
```

Enter fullscreen mode Exit fullscreen mode

从那里，我们确保缓存我们的安装，所以我们不必每次构建都这样做:

```
cache:
  directories:
    - /tmp/texlive
    - $HOME/.texlive 
```

Enter fullscreen mode Exit fullscreen mode

初始设置后，我们进入工作目录，这个目录恰好是我的 LaTeX 文件所在的文件夹。您可以根据自己的需要随意修改这一行:

```
before_script: cd $TRAVIS_BUILD_DIR/assignment01/doc/ 
```

Enter fullscreen mode Exit fullscreen mode

现在我们在我们的工作目录中，我们可以安全地构建我们的 PDF。这是构建的真正魔力，因为我们需要一个特殊的选项，-shell-escape，它允许我们访问 python 包来突出显示语法:

```
 script:
  - pdflatex --shell-escape ./assignment01.tex 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，其他一切都只是奖金。例如，我已经设置了我的 YAML 来将所有的 pdf 部署到最新的标记版本。[您需要用自己的令牌](https://docs.travis-ci.com/user/deployment/releases#authenticating-with-an-oauth-token)填充 GITHUB_TOKEN。

```
deploy:
  provider: releases
  api_key:
    secure: ${GITHUB_TOKEN}
  file:
    - ./assignment01.pdf
  skip_cleanup: true
  on:
    tags: true
    branch: master 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们关闭电子邮件通知，因为它们很烦人:

```
notifications:
  email: false 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们做到了！特拉维斯·CI·YAML 完成了自动化构建的工作。

### 附加构建脚本

除了这个 YAML，我们还需要一组脚本来设置 LaTeX 和我们需要的依赖项。幸运的是，我不必写这些脚本，因为它们是我们在 [travis-ci-latex-pdf repo](https://github.com/PHPirates/travis-ci-latex-pdf) 的朋友提供给我的。为了完整起见，我在下面分享了它们。确保将所有这些文件放在您的 repo 根目录下的一个名为 texlive 的目录中:

#### texliv/texliv . profile

```
selected_scheme scheme-basic
TEXDIR /tmp/texlive
TEXMFCONFIG ~/.texlive/texmf-config
TEXMFHOME ~/texmf
TEXMFLOCAL /tmp/texlive/texmf-local
TEXMFSYSCONFIG /tmp/texlive/texmf-config
TEXMFSYSVAR /tmp/texlive/texmf-var
TEXMFVAR ~/.texlive/texmf-var
option_doc 0
option_src 0 
```

Enter fullscreen mode Exit fullscreen mode

#### tex live/tex live _ packages

```
xcolor
fancyhdr
fancyvrb
makecmds
multirow
chngcntr
fvextra
upquote
lineno
ifplatform
xstring
framed
caption
collection-fontsrecommended
minted
oberdiek
etoolbox
float
booktabs 
```

Enter fullscreen mode Exit fullscreen mode

#### tex live/tex live _ install . sh

```
#!/usr/bin/env sh
# Originally from https://github.com/latex3/latex3
# This script is used for building LaTeX files using Travis
# A minimal current TL is installed adding only the packages that are
# required
# See if there is a cached version of TL available
export PATH=/tmp/texlive/bin/x86_64-linux:$PATH
if ! command -v texlua > /dev/null; then
  # Obtain TeX Live
  wget http://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz
  tar -xzf install-tl-unx.tar.gz
  cd install-tl-20*
  # Install a minimal system
  ./install-tl --profile=../texlive/texlive.profile
  cd ..
fi
# Just including texlua so the cache check above works
tlmgr install luatex
# Install package to install packages automatically
tlmgr install texliveonfly
# Install babel languages manually, texliveonfly does't understand the babel error message
tlmgr install collection-langeuropean
# Common fonts with hard to debug errors if not found
#tlmgr install collection-fontsrecommended
# In the case you have to install packages manually, you can use an index of packages like
# http://ctan.mirrors.hoobly.com/systems/texlive/tlnet/archive/
# Or better, check https://www.ctan.org/pkg/some-package to see in which TeX Live package it is contained.
# Then you can add one package per line in the texlive_packages file
# We need to change the working directory before including a file
cd "$(dirname "${BASH_SOURCE[0]}")"
tlmgr install $(cat texlive_packages)
# Keep no backups (not required, simply makes cache bigger)
tlmgr option -- autobackup 0
# Update the TL install but add nothing new
tlmgr update --self --all --no-auto-install 
```

Enter fullscreen mode Exit fullscreen mode

## 这是

至此，我们应该具备了使用 Travis CI 和 Minted 成功启动远程 LaTeX 构建所需的一切。如果您有任何问题，请随时联系我们。也就是说，托马斯·斯豪滕是真正的专家。我建议在某个时候访问他们的回购协议，因为还有其他几种方法可以让事情运转起来。

无论如何，这是我目前的全部。再次感谢你的来访！