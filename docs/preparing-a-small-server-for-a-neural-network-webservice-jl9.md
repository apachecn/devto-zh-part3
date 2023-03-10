# 为神经网络 web 服务准备小型服务器

> 原文：<https://dev.to/ladvien/preparing-a-small-server-for-a-neural-network-webservice-jl9>

以前，我写过关于训练 CNN 单独从文本中检测有毒评论的文章。但是，我意识到，即使一个人有一个漂亮的小神经网络来解决世界上所有的问题，它也没有用，除非它在生产中。

本文将介绍如何准备服务器和所需的单词嵌入，以便在 Flask webservice 中机械化 NN。

### 服务器设置:序言

在这个项目中，我使用了一个来自 Linode 的小型服务器——叫做“Nanode”在写这篇文章的时候，这些服务器每月才 5 美元。有什么问题吗？他们只有 1GB 的内存。在那里部署我们的 CNN 肯定会很棘手，但还是让我们来看看吧。

*   [https://www.linode.com/pricing](https://www.linode.com/pricing)

至于设置服务器，我已经在其他地方写过了:

*   [在 Linode 上设置 Nginx(步骤 3-5)](https://ladvien.com/creating-jekyll-website/)

对于这个特殊的项目，我决定使用 CentOS 7 发行版。

对于那些认识我的人来说；我没有背叛 Arch Linxu，然而，这个项目将使用 MongoDB，并且有一点儿[戏剧正在进行](https://lists.archlinux.org/pipermail/arch-dev-public/2019-January/029430.html)。我将在附录中留下一些 Arch Linux 指令，以防这个问题被解决。

我选择 CentOS 是因为它是我们在工作中使用的发行版，我希望获得一些使用它的经验。

### 在 Centos 上设置用户

以 root 身份登录并更新系统

```
yum update -y 
```

Enter fullscreen mode Exit fullscreen mode

让我们添加另一个用户；将系统设置为超级用户不是最佳做法。

```
useradd my_user
passwd my_user 
```

Enter fullscreen mode Exit fullscreen mode

为`my_user`设置密码

现在，让我们给`my_user` sudo 力量

```
EDITOR=nano visudo 
```

Enter fullscreen mode Exit fullscreen mode

使用
查找行

```
root    ALL=(ALL)    ALL 
```

Enter fullscreen mode Exit fullscreen mode

并为`my_user`添加完全相同的条目。完成后应该是这样的

```
root    ALL=(ALL)    ALL
my_user    ALL=(ALL)    ALL 
```

Enter fullscreen mode Exit fullscreen mode

保存文件并退出。

让我们以新用户的身份登录。退出您的 shell 并以`my_user`的身份重新登录。它应该看起来像这样，在您的本地计算机命令行上键入。

```
ssh my_user@erver_ip_address 
```

Enter fullscreen mode Exit fullscreen mode

登录后，让我们测试一下`my_user`的 sudo 能力

```
sudo ls 
```

Enter fullscreen mode Exit fullscreen mode

如果你被问候:

```
We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for my_user: 
```

Enter fullscreen mode Exit fullscreen mode

那么任务完成！否则，欢迎在评论中提问。

### 在 Centos 上设置 Miniconda

Anaconda 是 Python 数据分析工具的一个很好的打包系统。它处理了很多愚蠢的事情。Miniconda 是 Anaconda 的命令行版本，我们将使用它。

通过输入以下内容并同意条款来安装它。

```
sudo yum install -y wget bzip2
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
chmod +x Miniconda3-latest-Linux-x86_64.sh
./Miniconda3-latest-Linux-x86_64.sh
source .bashrc 
```

Enter fullscreen mode Exit fullscreen mode

这里要注意的是，如果你安装了 Miniconda 并且在执行`conda`时遇到了问题，很可能它没有将可执行路径添加到你的`PATH`变量中。

这将为您的用户和 root 添加路径:

```
echo "export PATH='/usr/local/miniconda/bin:$PATH'" &>> /home/my_user/.bashrc
echo "export PATH='/usr/local/miniconda/bin:$PATH'" &>> /root/.bashrc 
```

Enter fullscreen mode Exit fullscreen mode

在添加了`conda`路径之后，您需要确保重新加载您的 shell(注销并重新登录或者运行`source .bashrc`)。

截至本文撰写之时，Tensorflow 仅支持 Python 3.6 版本，而 Miniconda 将您的环境设置为使用 3.7 版本。为了纠正这一点，我们可以使用 Miniconda 安装程序`conda`将 Python 设置为 3.6.8。

```
conda install -y -vv python=3.6.8 
```

Enter fullscreen mode Exit fullscreen mode

此外，我们需要安装几个 Python 包。

```
conda install -y -vv tensorflow scikit-learn keras pandas 
```

Enter fullscreen mode Exit fullscreen mode

好了，最后一个重要步骤:**重启并重新登录。**

```
sudo reboot now 
```

Enter fullscreen mode Exit fullscreen mode

### 创建 MongoDB Tokenizer 集合

这就是我们变聪明的地方。我们试图将我们的模型放入不到 1GB 的 RAM 中，为此，我们需要找到一种方法来访问 word-embedding 的'`index2word`和`word2index`查找对象，而不像我们在培训中那样将它们加载到 RAM 中。我们将把它们放入数据库，只有在需要某个特定单词的时候才加载到 RAM 中。

磁盘访问比较慢，但是嘿！我不想每月花 40 美元买一个业余爱好服务器，你呢？

要移动`word-embeddings`需要几步。首先，我们将运行一个 Python 脚本来保存与我们原始训练的上下文相匹配的嵌入。然后，我们将从本地 MongoDB 中导出这些嵌入。接下来，我们将它们移动到远程服务器，并导入到 MongoDB 中。简单！

#### 本地安装 MongoDB

为了创建本地单词嵌入数据库，我们需要在本地安装 MongoDB。这可能因您的操作系统而异。我用自制软件在 Mac 上安装过。

*   [https://brew.sh/](https://brew.sh/)

以下是在 Mac 上安装 MongoDB 的说明:

*   [安装 MongoDB](https://treehouse.github.io/installation-guides/mac/mongo-mac.html)

不要忘记，在开始下一步之前，您需要启动 MonogDB 服务。

在 Mac 上，使用 Homebrew，可以从:
开始

```
brew services start mongodb 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建一个单词嵌入数据库

一旦您在本地安装了它，下面是我用来将`word_embeddings`转换成 MongoDB 数据库的脚本。它使用`gensim`加载单词嵌入，标记它们。

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*- """
Created on Tue Jan 22 05:19:35 2019
@author: cthomasbrittain
"""
import pymongo
import gensim.downloader as api
import pandas as pd
from keras.preprocessing.text import Tokenizer

# Convenience Macros word_embedding_name = "glove-wiki-gigaword-50"

BASE_DIR = '/path/to/embeddings'
TRAIN_TEXT_DATA_DIR = BASE_DIR + 'train.csv'
MAX_NUM_WORDS = 20000

# Load embeddings info = api.info() # show info about available models/datasets embedding_model = api.load(word_embedding_name) # download the model and return as object ready for use 
vocab_size = len(embedding_model.vocab)

index2word = embedding_model.index2word
word2idx = {}
for index in range(vocab_size):
    word2idx[embedding_model.index2word[index]] = index

# Get labels print('Loading Toxic Comments data.')
with open(TRAIN_TEXT_DATA_DIR) as f:
    toxic_comments = pd.read_csv(TRAIN_TEXT_DATA_DIR)

# Convert Toxic Comments to Sequences print('Processing text dataset')

tokenizer = Tokenizer(num_words=MAX_NUM_WORDS)
tokenizer.fit_on_texts(toxic_comments['comment_text'].fillna("DUMMY_VALUE").values)
sequences = tokenizer.texts_to_sequences(toxic_comments['comment_text'].fillna("DUMMY_VALUE").values)
word_index = tokenizer.word_index

# Save Embeddings to MongoDB mong = pymongo.MongoClient('127.0.0.1', 27017)

# Create collection database mongdb = mong["word_embeddings"]

# Create this word_embeddings coll = mongdb[word_embedding_name]

for i, word in enumerate(index2word):
    if i % 1000 == 0:
        print('Saved: ' + str(i) + ' out of ' + str(len(index2word)))
    try:
        embedding_vector = list(map(str, embedding_model.get_vector(word)))
        post = {
                'word': word,
                'index': word_index[word],
                'vector': list(embedding_vector)
         }
        posts = coll.posts
        post_id = posts.insert_one(post).inserted_id
    except:
        continue 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意的是，你可以直接在你的遥控器上设置数据库。然而，我发现通过 38.8bps 的 SSH 连接一次保存一个> 2 GB 的条目要花掉大部分时间。所以，我选择在本地创建它们，然后批量复制它们。

### 安装 MongoDB 远程服务器

MongoDB 拥有带有一些严格再分发条款的许可证。大多数发行版不再包括它在软件包回购。然而，MongoDB 有几个自己的发行版 repos 幸运的是，REHL 和 Centos 包括在内。但不是 Arch Linux？真的吗？:|

好了，要从私有回购安装 MongoDB，我们需要将它添加到本地回购地址。

我们可以通过键入:
来创建文件

```
sudo nano /etc/yum.repos.d/mongodb-org-4.0.repo 
```

Enter fullscreen mode Exit fullscreen mode

提醒一句，以下文字是从 MongoDB 网站上复制的。

*   [MongoDB 安装指令](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/)

可能最好直接从上面的链接复制回购信息，万一有更新的版本。

或者，下面是我放在文件里的:

```
[mongodb-org-4.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc 
```

Enter fullscreen mode Exit fullscreen mode

保存文件。

运行

```
sudo yum install -y mongodb-org 
```

Enter fullscreen mode Exit fullscreen mode

Yum 现在应该找到私有 repo 并安装 MongoDB。

#### 设置 MongoDB

我们需要启用 mongod.service

```
sudo systemctl enable mongod.service 
```

Enter fullscreen mode Exit fullscreen mode

并重启

```
sudo reboot now 
```

Enter fullscreen mode Exit fullscreen mode

我将把 MongoDB 设置为仅*本地访问*。这使得我们的 Flask 程序可以访问它，但不能远程访问。这是保护服务器的最佳实践。但是，如果您想启用对 MongoDB 的远程访问，我在附录中提供了说明。

### 将模型移动到服务器

因为我们在本地训练了模型，所以让我们把它移到服务器上。在存储模型的目录中打开您的终端。

```
scp toxic_comment_detector.h5 my_user@my_server_ip:/home/my_user 
```

Enter fullscreen mode Exit fullscreen mode

用我们之前创建的用户名替换`my_user`,用您的服务器地址替换`my_server_ip`。然后，它应该提示您输入服务器密码，就像您正在通过 ssh 连接到服务器一样。一旦输入，模型应该被复制到服务器。

#### 将 word_embeddings 数据库移动到服务器

一旦创建了本地`word_embeddings` DB，在本地终端键入以下命令进行复制:

```
mongodump --out /directory_to_save 
```

Enter fullscreen mode Exit fullscreen mode

现在，将这个数据库备份复制到您的远程服务器

```
scp -r /directory_to_save/name_of_output_folder user_name@remote_ip_address:/home/user_name/ 
```

Enter fullscreen mode Exit fullscreen mode

现在，登录到您的远程服务器，从数据转储创建一个 DB。

```
mkdir /home/user_name/word_embeddings
mongorestore --db word_embeddings /home/user_name/word_embeddings 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要重启 MongoDB 服务

```
sudo systemctl restart mongod.service 
```

Enter fullscreen mode Exit fullscreen mode

如果您想远程访问数据库(参见附录中的说明)，您可以使用 [Robo3T](https://robomongo.org/) 来确保一切就绪。但是如果你没有得到任何错误，我们可能是好的。

### 测试模型

登录到您的服务器。我们将测试该模型，因为它需要适合可用的内存。脚本中的`my_user`应该替换为您在设置服务器和项目时创建的用户名。

类型:

```
python 
```

Enter fullscreen mode Exit fullscreen mode

现在，在 Python 解释器中输入以下内容。

```
from keras.models import load_model
model = load_model('/home/my_user/toxic_comment_detector.h5') 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，它会提到它正在使用 Tensorflow 后端，并返回到解释器提示符。

如果您像我一样训练您的网络，那么下面将允许您完全测试远程部署的模型。

```
import numpy as np
test_prediction = np.array([[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0, 1873,147,6, 3476,324, 15, 29,141]])
model.predict(test_prediction) 
```

Enter fullscreen mode Exit fullscreen mode

如果你得到类似于:
的东西

```
array([[0.97645617, 0.21598859, 0.92201746, 0.01897666, 0.7753273,
0.11565485]], dtype=float32) 
```

Enter fullscreen mode Exit fullscreen mode

我们状态很好。这些分别是对以下的预测:

```
["toxic", "severe_toxic", "obscene", "threat", "insult", "identity_hate"] 
```

Enter fullscreen mode Exit fullscreen mode

`test_prediction`是以下预编码的文本序列。

```
"C*#%`SUCKER BEFORE YOU PISS AROUND ON MY WORK" 
```

Enter fullscreen mode Exit fullscreen mode

所以，`toxic`和`obscene`的标签绝对应该靠近`1`。看起来我们状态不错。

在下一篇文章中，我将展示如何创建 Flask webservice 来访问模型。嗯，至少我希望如此，但还不确定该怎么做。

### 附录

#### Arch Linux Miniconda 设置

```
sudo pacman -Syu
sudo pacman -S git wget tk valgrind gcc make
adduser -m user_name
passwd user_name
EDITOR=nano visudo
(add user_name to sudo)
su user_name

wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
source .bashrc
conda install keras h5py pillow flask numpy gensim pandas scikit-learn matplotlib
conda install tensorflow=1.8 
```

Enter fullscreen mode Exit fullscreen mode

#### [T1】在 Arch Linux 上设置 MongoDB](#setup-mongodb-on-arch-linux)

显然，MongoDB 的许可变更意味着 Arch Linux 官方 repos 不能分发它。所以，我们必须从源代码编译。*哇哇哇啊*。

**注意，从源代码编译需要 1GB 以上的内存。**

*   [https://lists . arch Linux . org/piper mail/arch-dev-public/2019-January/029430 . html](https://lists.archlinux.org/pipermail/arch-dev-public/2019-January/029430.html)
*   [https://TechCrunch . com/2018/10/16/MongoDB-switches-up-its-开源-license/](https://techcrunch.com/2018/10/16/mongodb-switches-up-its-open-source-license/)

```
sudo pacman -S fakeroots automake autoconf gcc make snappy \ 
            yaml-cpp lsb-release  gperftools \
            libstemmer scons python2-setuptools python2-regex \
            python2-cheetah python2-typing python2-requests \
            python2-yaml python2-pymongo 
git clone https://aur.archlinux.org/wiredtiger.git
cd wiredtiger
makepkg -i
git clone https://aur.archlinux.org/mongodb.git
cd mongodb
makepkg -i 
```

Enter fullscreen mode Exit fullscreen mode

#### 启用远程访问 MongoDB

要启用远程连接，请编辑`mongod.conf`文件:

```
sudo nano /etc/mongod.conf 
```

Enter fullscreen mode Exit fullscreen mode

在文件中找到以下几行并注释掉`bindIp`。

您的文件应该如下所示:

```
# network interfaces
net:
  port: 27017
  #bindIp: 127.0.0.1  # Enter 0.0.0.0,:: to bind to all IPv4 and IPv6 addresses or, alternatively, us$ 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们从任何 IP 地址连接到 MongoDB。如果我们不使用这一行，那么我们只能从服务器内部连接到数据库(127.0.0.1 = local)。

### 监控系统资源

我喜欢用`htop`来做这个，但是你必须在 Centos
上从源代码构建它

```
wget dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-7-11.noarch.rpm
sudo rpm -ihv epel-release-7-11.noarch.rpm
sudo yum install -y htop 
```

Enter fullscreen mode Exit fullscreen mode