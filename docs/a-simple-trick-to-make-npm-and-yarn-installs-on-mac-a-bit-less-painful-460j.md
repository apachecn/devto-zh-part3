# 一个让 npm 和 yarn 在 Mac 上安装不那么痛苦的简单技巧

> 原文：<https://dev.to/aurelio/a-simple-trick-to-make-npm-and-yarn-installs-on-mac-a-bit-less-painful-460j>

互联网世界中 JavaScript 方面最有趣、最被滥用的迷因之一肯定是下面这个。

[![node modules are heavier than a Black Hole](img/bb941b09001f2a57cb763a58e751a754.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fvTqF4Fk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nobitagit.github.io/blimg/2019-4-6-node_modules.png)

如果你曾经冒险进入一个中等规模项目的 node_module 目录。)这个成功形象的作者显然有一个观点。

例如，如果你必须用 Create React App 启动一个新项目，并且在运行“npx create-react-app my-app”之后，你要计算文件的数量，你会看到类似这样的内容:

```
$ find ./node_modules -type f | wc -l
  27032 
```

就这样，你给了我们的朋友 Spotlight 超过 27k 的新条目来做索引。

现在，除了快速打开应用程序，我很少使用 Spotlight，当我这样做时，就会发生这种情况

> ![Bruno Lemos profile image](img/2173861dc6eeba078167760fe5478623.png)

但是我跑题了。重要的是，我很少使用它，但这并不妨碍 Spotlight 抓取我的高清内容。
与文件系统中的任何其他条目一样，默认情况下，node_modules 的内容由 Spotlight 进行索引。
我从来没有真正想过这个问题，直到我看到下面的推文。

> ![Roel Van Gils profile image](img/47af3a6eefe89300bc65a3198a965f3c.png)罗埃尔·范吉尔斯@罗埃尔·范吉尔斯![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)你的 Mac 是否变得缓慢/无反应(风扇踢进等。)当你“npm 安装”一个有一百万个微小依赖项的大项目时？我了解到，预先在/node _ modules *中添加一个空的`. metadata_never_index `将阻止 Spotlight 索引所有这些垃圾。2019 年 04 月 02 日下午 13:43[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1113074439976075264)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1113074439976075264)271[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1113074439976075264)1042

再加上我们可能经常运行类似于`rm -rf ./node_modules && yarn`的命令，这意味着 Spotlight 可能会在一天内多次重新索引这些内容。

有了这些新获得的知识，我们可以使用一些技巧来避免这个问题，并有希望为我们的机器节省一些浪费的内存密集型工作。

例如，您可以向 package.json 脚本中添加一个 postinstall 块，以便在安装项目的依赖项时在后台调用它。

```
{  "scripts":  {  "clean":  "rm -rf ./build",  "start":  "node index.js",  "postinstall":  "touch ./.metadata_never_index"  }  } 
```

这很好也很简单，但是你需要为你的所有项目做同样的事情，而且它将为其他所有使用 package.json 的人运行，即使不是在 Mac 上(想想运行 Linux 或 Windows 的人)。
要在你的 Mac 上到处都有类似的功能，而不需要接触 package.json，你可以将它添加到你的`~/.bash_profile`或`./zsh_profile`。

```
# open your profile with `vim ~/.bash_profile`
# and paste the function below
function npmi {
    mkdir node_modules 2>/dev/null
    touch ./node_modules/.metadata_never_index
    if [ -f yarn.lock ]; then yarn install $@
    else npm install $@
    fi
} 
```

在任何节点项目的根上运行`npmi`将会自动添加`.metadata_never_index`文件，就在安装依赖项之前。
最后，对我们的文件系统中所有现有的项目做同样的事情可能是有用的。

```
find . -type d -name "node_modules" -exec touch "{}/.metadata_never_index" \; 
```

这是 Yarn 的 Github 在本期的评论中提出的，似乎最早出现在 2016 年[的 StackExchange 回答](https://apple.stackexchange.com/a/258791/44487)中。

希望这个简单的技巧能节省我们一点时间(和电池电量！)以后。