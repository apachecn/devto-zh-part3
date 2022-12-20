# 重装 Ubuntu 时要记住的事情。

> 原文：<https://dev.to/vukhanhtruong/things-to-remember-when-reinstall-ubuntu-5c91>

这只是我在机器上重装软件时的清单。希望这对你有用。

## 备份必不可少的东西。

1.  最重要的是名为 **~/的文件夹。ssh** 包含我访问不同服务器的所有凭证。如你所知，如果你丢失了它，你必须重新生成密钥，并将它们添加到 Github、Gitlab、Bitbucket 等。

2.  确保所有源代码都已备份或被推送到远程存储库。

3.  我最喜欢的 IDE 是 Visual Studio 代码，并且安装了很多扩展。我需要做的就是备份名为**的文件夹。我的主目录`~/.vscode`中的 vscode** 。如果您使用的是另一个 IDE，您应该找出配置存储的保存位置。

4.  我每天都和终端打交道，所以我必须备份名为**的文件。zshrc** 和名为**的文件夹。oh-my-zsh** ，避免重新配置终端，安装一打插件。

## 安装完 Ubuntu 后

1.  像以前一样将所有备份的文件夹移动到相同的路径。所以，现在我的 Linux 环境和旧的差不多。当然，它节省了我的时间。

2.  安装喜欢的应用程序。这里是我最喜欢的应用，只是侧重于开发。

#### 系统

*   Ubuntu 18.10(当前版本)

#### [这里](#ide)

*   [Visual studio 代码](https://code.visualstudio.com/)

#### 互联网

*   [铬合金](https://www.google.com/chrome/)
*   [Rambox](https://rambox.pro/#home) 一体化聊天应用。

#### 终端

*   [zsh](http://www.zsh.org/)
*   [p10k](https://github.com/romkatv/powerlevel10k)
*   [neovim](https://github.com/neovim/neovim)
*   [tmux](https://tmux.github.io/) 和 [tmuxinator](https://github.com/tmuxinator/tmuxinator)
*   oh-my-zsh 一个令人愉快的社区驱动(有 1200 多个贡献者)框架，用于管理您的 zsh 配置。以下是我最喜欢的插件:

```
plugins=(
  zsh-completions
  zsh-autosuggestions
  zsh-syntax-highlighting
  history-substring-search
  git
  docker
  docker-compose
  dotenv
  nvm
  httpie
  rsync
  yarn
  tmux
  node
  helm
  composer
  minikube
  kubectl
) 
```

Enter fullscreen mode Exit fullscreen mode

*   vimrc —这种配置是最终的 vimrc。
*   [vim-plug](https://github.com/junegunn/vim-plug) —插件管理。
*   [vim 语言包](https://github.com/sheerun/vim-polyglot)
*   [vim 语法检查器](https://github.com/w0rp/ale)

```
call plug#begin('~/.vim/plugged')

" Make sure you use single quotes

" On-demand loading
Plug 'scrooloose/nerdtree', { 'on':  'NERDTreeToggle' }

" Language pack for VIM
Plug 'sheerun/vim-polyglot'

" VIM check syntax
Plug 'w0rp/ale' 

" Initialize plugin system
call plug#end()

" Shortcut key to open file explorer
nmap <F6> :NERDTreeToggle<CR>

" ALE linting tool
let g:ale_linters = { \ 'javascript': ['eslint'], \} let g:ale_sign_error = '❌'
let g:ale_sign_warning = '⚠️'
highlight ALEErrorSign ctermbg=NONE ctermfg=red
highlight ALEWarningSign ctermbg=NONE ctermfg=yellow

let g:ale_fixers['javascript'] = ['eslint']

" Fix files automatically on save
let g:ale_fix_on_save = 1 
```

Enter fullscreen mode Exit fullscreen mode

#### DevOps 工具

*   [码头工人](https://www.docker.com/)
*   [坞站-复合](https://github.com/docker/compose)
*   [minikube](https://github.com/kubernetes/minikube)
*   ska fold 一个命令行工具，有助于 Kubernetes 应用程序的持续开发。
*   [复合](https://github.com/kubernetes/kompose)转换复合码头至库柏。
*   [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl) 命令行接口，用于针对 Kubernetes 集群运行命令。
*   [kubectx](https://github.com/ahmetb/kubect) 在 kubectl 中快速切换集群和名称空间的方法。
*   [kubeval](https://github.com/garethr/kubeval) 验证您的 Kubernetes 配置文件，支持多个 Kubernetes 版本。
*   [潜水](https://github.com/wagoodman/dive)探索 docker 图像中每一层的工具

#### 实用程序开发工具

*   [fd](https://github.com/sharkdp/fd/releases) -简单、快速且用户友好的“查找”替代方案。
*   你唯一需要的备忘单。
*   [httpie](https://github.com/jakubroztocil/httpie) —现代命令行 HTTP 客户端—用户友好的 curl 替代方案，具有直观的 UI、JSON 支持、语法突出显示、类似 wget 的下载、扩展等。
*   [postman](https://www.getpostman.com/) Postman 让 API 开发变得简单。
*   ngrok 或 [localtunnel](https://github.com/localtunnel/localtunnel) 将您的本地主机公开，以便于测试和共享。
*   [pet](https://github.com/knqyf263/pet) —简单的命令行片段管理器。
*   [tmux](https://tmux.github.io/) —终端复用器。
*   [tmux 配置](https://github.com/gpakosz/.tmux) —最佳 tmux 配置。
*   my CLI—MySQL 的终端客户端，具有自动补全和语法突出显示功能。
*   [json-server](https://github.com/typicode/json-server) —不到 30 秒就能获得零编码的全假 REST API。
*   [贝吉塔](https://github.com/tsenart/vegeta) HTTP 负载测试工具和库。
*   一个简单的零配置工具，可以用你喜欢的任何名字创建本地可信的开发证书。
*   [doctoc](https://github.com/thlorenz/doctoc) 生成降价文件的目录。

#### 故障排除

如果您遇到了 ssh 的以下问题:

```
sign_and_send_pubkey signing failed agent refused operation 
```

Enter fullscreen mode Exit fullscreen mode

运行以下命令进行修复:

```
eval "$(ssh-agent -s)" && ssh-add ~/.ssh/id_rsa
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub 
```

Enter fullscreen mode Exit fullscreen mode

##### 就是这样。如果你有什么要分享的，请随时留下你的评论。