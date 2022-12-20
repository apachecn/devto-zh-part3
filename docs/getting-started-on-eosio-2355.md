# EOSIO 入门

> 原文：<https://dev.to/th3ch33s3/getting-started-on-eosio-2355>

几个月前，我对区块链炒作产生了兴趣。嗯，这真的不是炒作，但那是我当时的想法。

作为敢于冒险的我，我全身心地投入。当时，EOS 刚刚要推出。所以我选择了 EOS。几个月后，我可以高兴地说，这不是一个徒劳的努力，也不是一个错误的决定。

EOSIO 社区是我有幸参与的最好的社区之一。在很短的时间内，我就跟上了进度，这要感谢帮助我跟上进度的各种开发人员。

老实说，我从像 [@cc32d9](https://github.com/cc32d9) 这样的人那里得到的帮助和建议让我比自己走得更远。谢了伙计。

好吧好吧。关于我的咆哮到此为止，让我们开始吧！

你想赶上区块链的潮流，但不知道它是怎么回事？也许这篇文章是给你的。拿起你最喜欢的一杯咖啡，我们出发吧。

### 什么是区块链？

所以，从最基本的开始，区块链是一个廉洁的数字账本。在这个分类账上，我们可以存储任何有价值的交易。

##### 那么，等等，我们真的可以使用区块链技术来存储除货币交易之外的交易吗？但是比特币....

答案是肯定的。我们可以存储除货币交易以外的其他交易。你说得对，比特币是区块链。但区块链不是比特币。比特币是区块链的实现之一。

[这里](https://www.youtube.com/watch?v=3xGLc-zz9cA)是一个很好解释的视频。[这里](https://www.youtube.com/watch?v=SSo_EIwHSd4)是另一个，然而[又是另一个](https://www.youtube.com/watch?time_continue=83&v=27nS3p2i_3g)

既然你已经理解了区块链的本质，我们就可以来点冒险了。

我们将在本地机器上设置一个测试 EOS 节点。

#### 我的环境

*   Linux 为 19.1
*   8 GiB RAM
*   256 GiB 固态硬盘

以下是官方支持的设置列表(截至撰写本文时):

*   亚马逊 2017.09 及更高版本
*   Centos 7
*   Fedora 25 和更高版本(推荐 Fedora 27)
*   薄荷 18
*   Ubuntu 16.04(建议使用 Ubuntu 16.10)
*   Ubuntu 18.04
*   MacOS Darwin 10.12 及更高版本(建议使用 MacOS 10.13.x)

有 2 种(或者可能 3 种)方法可以在你的机器上安装 EOSIO 软件，但是我将重点介绍 2 种。首先是从他们的 GitHub Repo 中克隆和编译 EOSIO 软件。第二步是下载预编译的二进制文件并安装在我的机器上。

##### 编译

###### 注意:要完成这一步，你需要 8 GiB 或更多的 RAM 和 20 GiB 的磁盘空间。(但是，如果您知道自己在做什么，您可以编辑构建脚本来跳过对这些条件的检查。)

要实现这一点，克隆 EOS io/EOS repo；
`git clone https://github.com/EOSIO/eos --recursive`

这可能需要一段时间(现在，那杯咖啡可能会派上用场:)
完成克隆后，导航到克隆的文件夹。

那里有自动构建和安装脚本。务必运行`sudo ./eosio_build.sh && sudo ./eosio_install.sh`

如果一切顺利，(如果你使用的是上述官方支持的平台之一，我看不出有什么困难)，你应该在你的系统中安装 eosio。

要卸载，只需从 **eosio** 根目录发出`sudo ./eosio_uninstall.sh`。

要检查是否一切顺利，只需输入`nodeos -h`。如果你的屏幕显示出很长的输出，你是安全的。

##### 从预编译的二进制文件安装

那么，如果我没有 8 GiB 的内存备用呢？您可能必须使用[预编译的二进制文件](https://github.com/EOSIO/eos/releases)
在撰写本文时，最新的发布标签是`1.6.0-rc1`。下载适合您的发行版的二进制文件。
见下面一些例子:

**麦克 OS X**
`brew tap eosio/eosio`
`brew install eosio`

**Ubuntu 18。x 和所有基于 Ubuntu 18 的版本。x**
T0
T1】

**Centos**
`wget https://github.com/EOSIO/eos/releases/download/v1.6.0-rc1/eosio-1.6.0-rc1.el7.x86_64.rpm`
`sudo yum install ./eosio-1.6.0-rc1.el7.x86_64.rpm`

**软呢帽**
`wget https://github.com/EOSIO/eos/releases/download/v1.6.0-rc1/eosio-1.6.0-rc1.fc27.x86_64.rpm`
`sudo yum install ./eosio-1.6.0-rc1fc27.x86_64.rpm`

确保在您喜欢的终端上发出`nodeos -h`来测试您的安装。我们希望你的屏幕上有一些长输出。否则，您可能需要再次检查上述安装步骤。

#### 发展

当您在机器上安装了 EOSIO 软件后，创建您的工作目录。

`mkdir project && cd projects`

使用
弹出您的节点

`nodeos -e -p eosio \
--plugin eosio::producer_plugin \
--plugin eosio::chain_api_plugin \
--plugin eosio::http_plugin \
--plugin eosio::history_plugin \
--plugin eosio::history_api_plugin \
--data-dir data \
--access-control-allow-origin='*' \
--contracts-console \
--http-validate-host=false \
--verbose-http-errors \
--filter-on='*'`

您应该能够看到一些输出加速了您的终端。你猜怎么着？你在积极生产积木！因此，在我们过于兴奋而错过理解我们刚刚做的事情之前，让我们停下来回顾一下刚刚发生的事情。

上面的命令使用我们上面指定的各种标志旋转一个节点。这将加载所有的基本插件，设置服务器地址，启用 CORS，并添加一些合同调试和日志记录，以及一个用于写入数据的目录。

在本演练的第二部分，我们将进一步创建钱包、密钥、帐户，并在本地节点上部署几个智能合约。

到时候见！