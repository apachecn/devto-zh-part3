# 使用 Shell 脚本实现自动化

> 原文：<https://dev.to/sergiodxa/automatizacin-con-shell-scripts-5ch7>

*最初发表于[https://sdx . im/articles/automation-shell 脚本](https://sdx.im/articles/automatizacion-shell-scripts)*

前几天，我需要创建一个脚本来自动化项目的安装过程，我不得不学习 Shell 脚本。这是我从创作中学到的东西。

> *注*:这是基于我为正在工作的项目所做的脚本。

## 创建文件

首先是创建文件，我们将它命名为`setup.sh`。我们可以把它放在任何文件夹中，例如在`~/`做`touch ~/setup.sh`。

## 条件

接下来，我们将添加代码。我们将使我们的脚本有两个功能，`install`和`run`，为此我们需要简单的条件。

```
if [ "$1" = "install" ]
then
  # Install the app
elif [ "$1" = "run" ]
then
  # Run the app
else
  # Throw an error
fi 
```

Enter fullscreen mode Exit fullscreen mode

如果我们看到使用了一种叫做`$1`的东西，那就是一个变量，在这种情况下，引用我们运行脚本时传递给它的第一个参数。例如，如果要运行它，我们这样做:

```
bash ~/setup.sh install 
```

Enter fullscreen mode Exit fullscreen mode

所以`install`是`$1`的值。

下面我们看到的是条件是怎样做的，语法是:

```
if [ condición ]
then
  # algo
elif [ otra condición]
then
  # otra cosa
else
  # algo más
fi 
```

Enter fullscreen mode Exit fullscreen mode

条件以`if`开头。`if`后面是方括号内的第一个条件，下一行是`then`，接下来是条件通过时要执行的代码，做一个`else if`用关键词`elif`后跟方括号内的条件和`then`，就像对于后一种情况，我们做`else`不用放`then`。最后放一个`fi`用来完成条件块。

## 功能

我们将创建一些函数来对代码进行排序。我们创建了一个功能，允许我们根据发生的情况向用户显示不同颜色的文本，然后我们将分别有`throw`、`warn`和`print`，分别以红色、黄色和绿色显示文本。

```
NC='\033[0m'

throw()
{
  COLOR='\033[0;31m'
  >&2 echo -e ${COLOR}$1${NC}
}

print()
{
  COLOR='\033[0;32m'
  echo -e ${COLOR}$1${NC}
}

warn()
{
  COLOR='\033[0;33m'
  echo -e ${COLOR}$1${NC}
} 
```

Enter fullscreen mode Exit fullscreen mode

定义函数的方法只是在函数名称后面加上“`()`”。然后，我们使用键创建一个代码块，函数中的代码包含在其中。在我们的例子中，它们大致是相似的函数，我们都创建了一个名为`COLOR`的变量，其值为`'\033[0;31m'`表示红色，`'\033[0;32m'`表示绿色，`'\033[0;33m'`表示黄色，我们在函数之前创建的变量`NC`表示*不着色。【t】*

接下来我们做的是做一个`echo`在屏幕上显示文字，我们把`-e`标志传给它支持`\`，这是使用颜色所必需的，我们把它作为值传递给屏幕上显示`${COLOR}$1${NC}`就是这个意思吗？我们先把我们的颜色变量的内容放进去，然后放入`$1`，如果你还记得，这是我们传递给脚本的第一个参数，原来在函数内是我们传递给函数的第一个参数，在我们的例子中是文本，最后放入`${NC}`，它不再有颜色，也不再剩下

有一种情况与函数`echo`的其馀部分不同，在`echo`之前添加`>&2`。也就是说`echo`的内容应该转移到`stderr`而不是`stdout`(正常)。这就是说，如果有任何程序使用我们的脚本，它将能够正确地识别出“`throw`”错误。

现在，我们可以开始使用这些功能，例如，我们添加了一条红色的错误消息在我们的“`else`”中显示。

```
NC='\033[0m'

throw()
{
  COLOR='\033[0;31m'
  >&2 echo -e ${COLOR}$1${NC}
}

print()
{
  COLOR='\033[0;32m'
  echo -e ${COLOR}$1${NC}
}

warn()
{
  COLOR='\033[0;33m'
  echo -e ${COLOR}$1${NC}
}

if [ "$1" = "install" ]
then
  # Install the app
elif [ "$1" = "run" ]
then
  # Run the app
else throw "Invalid command, available values are 'install' or 'run'"
fi 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们要做的。现在让我们安装。

## 安装功能

我们创建了一个名为“`install`”的功能，该功能将安装我们的应用程序。安装过程必须涵盖一切，想法是我们可以在一台新计算机上运行这个脚本，让我们的环境做好准备。

为此，我们需要执行几个步骤:

*   Instalar 家酿啤酒
*   安装程序
*   创建 SSH 密钥
*   Agregarla a GitHub
*   Instalar Yarn y Node.js
*   从 GitHub 克隆我们的存储库
*   安装相依性

在程式码中，这将会是这样的

```
confirm_ssh()
{
  read -p "Have you added the SSH Key? [y/N] " CONFIRM_SSH
  if [[ $CONFIRM_SSH -ne "y" || $CONFIRM_SSH -ne "Y" ]]
  then warn "Please add it to continue."
    confirm_ssh
  fi
}

install()
{
  /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
  print "Homebrew installed"

  brew install git
  print "Git installed"

  warn "Please, enter your GitHub email address and name"
  read -p "Email address: " EMAIL
  read -p "Name: " NAME
  git config --global user.name "$NAME"
  git config --global user.email "$EMAIL"
  echo "Generating SSH Key"
  ssh-keygen -t rsa -b 4096 -C "$EMAIL"
  eval "$(ssh-agent -s)"
  ssh-add -K ~/.ssh/id_rsa
  pbcopy < ~/.ssh/id_rsa.pub
  warn "We have copied your new SSH Key to your clipboard, please add it to your GitHub account going to https://github.com/settings/keys"
  sleep 2.5
  open "https://github.com/settings/keys"
  confirm_ssh
  print "Git & GitHub configured"

  brew install yarn
  print "Yarn & Node.js installed"

  git clone git@github.com:sergiodxa/personal-site.git ~/website
  print "Repository clonned"

  cd ~/website && npm install ; cd -
  print "Dependencies installed"

  print "Project succesfully installed, you could run it with 'bash ~/setup.sh run'"
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看他们做这些功能`install`和`confirm_ssh`。

让我们从`install`开始，我们做的第一件事是安装 Homebrew，最后我们显示一条消息说它已经安装好了。然后我们用 Homebrew 安装 Git，我们再次通知用户已安装。

我们通知用户我们将需要您的姓名和电子邮件地址，并使用`read`提示您输入这些值，`-p`标志允许我们通过文本向左侧显示用户将要输入其姓名和电子邮件的位置，最后将变量名称传递给`read`

一旦我们有了“`$NAME`”和“`$EMAIL`”之后，我们将配置 Git 以使用这些值，并生成一个新的 SSH 密钥，其值将使用“`pbcopy < ~/.ssh/id_rsa.pub`”复制到剪贴板。我们警告用户，我们已将 SSH 密钥复制到剪贴板，您必须将其添加到 GitHub 帐户中，我们等待两秒半，然后在浏览器中打开添加 SSH 密钥的 URL，这一等待是为了给读取时间。

之后我们调用函数`confirm_ssh`。此非常简单的功能使用`read`(如上所述)，询问用户是否已添加 SSH 密钥，如果用户不键入`y`或`Y`，则会显示消息“请将其添加到继续操作中”，然后递归地再次调用`confirm_shh`功能，这样我就

之后我们用 Homebrew 安装了 Yarn，而 home brew 还安装了 Node.js，这给了我们一个接一个的。我们将项目存储库克隆到已知文件夹中，也可以使用“`read`”提示用户克隆存储库到哪个文件夹。

之后，我们运行`cd ~/website && yarn ; cd -`，这样做是为了移动到我们克隆项目的文件夹，运行`yarn`，并在安装完依存对象后返回到原来所在的文件夹。

最后，将显示一条消息，说明如何运行项目。

## 执行函数

现在，我们已经安装了所有内容，我们将创建函数以开始我们的项目。

```
run()
{
  print "Running project"
  cd ~/website && yarn dev ; cd -
} 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们显示了一条消息，说我们将运行该项目，导航到克隆存储库的文件夹，然后运行`yarn dev`启动该存储库，此脚本`dev`用于我的个人站点，以便在开发模式下启动该存储库。完成后，您将返回到初始文件夹。

## 把所有东西放在一起

我们现在有条不紊地结合起来，在适当的条件下履行我们的新职能。

```
NC='\033[0m'

throw()
{
  COLOR='\033[0;31m'
  >&2 echo -e ${COLOR}$1${NC}
}

print()
{
  COLOR='\033[0;32m'
  echo -e ${COLOR}$1${NC}
}

warn()
{
  COLOR='\033[0;33m'
  echo -e ${COLOR}$1${NC}
}

run()
{
  print "Running project"
  cd ~/website && yarn dev ; cd -
}

confirm_ssh()
{
  read -p "Have you added the SSH Key? [y/N] " CONFIRM_SSH
  if [[ $CONFIRM_SSH -ne "y" || $CONFIRM_SSH -ne "Y" ]]
  then warn "Please add it to continue."
    confirm_ssh
  fi
}

install()
{
  /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
  print "Homebrew installed"

  brew install git
  print "Git installed"

  warn "Please, enter your GitHub email address and name"
  read -p "Email address: " EMAIL
  read -p "Name: " NAME
  git config --global user.name "$NAME"
  git config --global user.email "$EMAIL"
  echo "Generating SSH Key"
  ssh-keygen -t rsa -b 4096 -C "$EMAIL"
  eval "$(ssh-agent -s)"
  ssh-add -K ~/.ssh/id_rsa
  pbcopy < ~/.ssh/id_rsa.pub
  warn "We have copied your new SSH Key to your clipboard, please add it to your GitHub account going to https://github.com/settings/keys"
  sleep 2.5
  open "https://github.com/settings/keys"
  confirm_ssh
  print "Git & GitHub configured"

  brew install yarn
  print "Yarn & Node.js installed"

  git clone git@github.com:sergiodxa/personal-site.git ~/website
  print "Repository clonned"

  cd ~/website && npm install ; cd -
  print "Dependencies installed"

  print "Project succesfully installed, you could run it with 'bash ~/setup.sh run'"
}

if [ "$1" = "install" ]
then install elif [ "$1" = "run" ]
then run
else throw "Invalid command, available values are 'install' or 'run'"
fi 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，如果你把这个文件的内容复制到`~/setup.sh`，然后用`bash ~/setup.sh install`或`bash ~/setup.sh run`运行，我们就可以准备好了。

此外，我们还可以在安装结束时调用功能`run`，以使一切就绪。

## 结语

一开始使用 Shell 脚本有点奇怪，因为以前从来没有必要使用它，但它相当简单和有趣，我认为其他事情可以通过 Shell 脚本实现自动化，以便更轻松地完成任务。