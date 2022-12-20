# GitStub -一个 GitHub-first 开发 boostrapper，用于 dotnet 核心

> 原文：<https://dev.to/erikest/gitstub-a-github-first-development-boostrapper-for-dotnet-core-1i5f>

### TL；博士；医生

我对自己的开源项目的第一次尝试是一个降低 GitHub 代码门槛的工具。我创建它是因为我想开始一个不同的开源项目，并且在学习做什么的过程中，意识到有许多重复的步骤。起初，我试图为自己创建一组脚本，通过与 GitHub rest api 和 dotnet core cli 交互来简化这个过程。很快，我意识到，采取这些步骤并创建一个跨平台的 dotnet core 全球工具不仅在易用性方面更好，而且还允许我与最广泛的受众分享这一点——希望能为其他人节省一些时间和乏味。

可以用
`dotnet tool install --global gitstub`安装自己试试

GitHub 上的问题、请求和改进意见

## 问题

您想要创建一个新的. net 核心项目，因此您

```
mkdir MyKillerWebApp
cd MyKillerWebApp
dotnet new webapp 
```

事实上，它需要一个解决方案和一点点重新安排你决定

```
mkdir -p src/MyKillerWebApp
mv * src/MyKillerWebApp
dotnet sln -n MyKillerWebApp 
```

然后你想把它放到一个 git 存储库中，但是首先你需要找到一个`.gitignore`来添加——也许你有一个你以前在另一个项目中使用过的

```
cp /users/myuser/repos/someotherdotnetproject/.gitignore ./ 
```

现在您可以设置您的第一次提交

```
git init
git add -A
git commit -m "First Commit! The only way is up from here!" 
```

在某些时候你意识到，它实际上应该是一个 GitHub 项目，因为你想与世界分享这个杀手级的 webapp。从这里，你去你的浏览器和 GitHub.com，你登录，创建一个新项目，填写细节。

几分钟后，GitHub 项目出现了，但是它和您的本地 git 没有连接，所以您仍然需要

```
git remote add origin https://user:pass@github.com/user/projectName.git
git push --set-upstream origin master 
```

最后，你已经在 GitHub 上获得了一切，你可以从那里迭代。

或者您可能会在此之前的某个地方停下来，甚至在创建本地 git repo 之前。你告诉自己，“一旦它是‘值得的’”，你就会采取这些步骤，做一个 GitHub 项目。也许你知道，也许你不知道。也许你会有第二种想法——如果我的代码还不够“好”呢？但是有时候，足够好或者足够好的代码只是停留在你的笔记本电脑上，在那里死去。或者另一次，远离你的机器，你和另一个 dev 在一起，你想——“哦！我开始做类似这样的东西——但是，它在我的机器上，所以我必须创建一个 GitHub 项目，并把它推上来给 T2 你看...也许等我回到电脑前。”或者也许这并不是一个很大的障碍，你每次都要经历这些步骤——但是如果能更快一点，更容易一点，那不是很好吗？

## 解决方案-GitHub-使用 GitStub 的首次开发

这个项目的目标有两个。

1.  **节省时间**——简单来说，这些都是重复性的任务，他们在乞求自动化。
2.  推广一种开发模式，你可以在 GitHub 上开始项目，无论是公共的还是私人的，然后马上提交- **GitHub-first** 。
    *   这不仅使代码在任何地方都可以立即获得，还通过让项目的启动开始贡献的循环，促进了您在开源方面的进展。你要克服你的恐惧和完美主义，代之以持续的公共改进，作为你发展生活的一个特征。
    *   通过将其与 dotnet 新项目模板相结合，您可以获得一个自举的、搭建的项目，并准备好进行迭代。第一个提交是现成的模板。您的第二次提交将包含基本模板和您到目前为止添加的值之间的所有增量——您可以开始了！
    *   希望它能消除一个小小的障碍，这个障碍可能会阻止你尝试一些东西并把它放到 GitHub 上，因为你“懒得”设置项目并连接所有东西。

## 如何使用

首先，使用以下命令安装工具

`dotnet tool install --global gitstub`

现在，假设我想在名为`MyKillerApp`的文件夹中启动一个新的控制台应用程序。
从命令行的那个文件夹里，我可以

```
gitstub console -gsu myusername -gsp mypassword 
```

默认情况下，`gitstub`会使用当前文件夹作为 GitHub 上公共项目的名称。`console`之后的两个参数是我的 GitHub 用户名(`-gsu`)和 GitHub 密码(`-gsp`)。这将完成一些工作，包括添加一个. gitignore 文件，该文件忽略了 dotnet 项目的大多数常见内容。过了一会儿，瞧，我现在可以去 https://github.com/myusername/MyKillerApp 的[看那里的一切了。如果我在 Visual Studio 中打开项目，我可以继续使用团队资源管理器窗口来处理进一步的提交。](https://github.com/myusername/MyKillerApp)

也许我想要一个多一点的结构，解决方案/项目的布局如下

*   /
    *   MyKillerApp.sln
    *   src/
        *   MyKillerApp/
            *   MyKillerApp.csproj
            *   <other files=""></other>

然后我可以说

```
gitstub console --sln -gsu myusername -gsp mypassword 
```

并且`gitstub`将创建一个解决方案和一个项目，并对它们进行排列。

如果我想为项目和解决方案使用一个不同的名字，我也可以指定这个名字

```
gitstub console -gsr "FirstKillerApp" -gss "KillerApps" -gsu myusername -gsp mypassword 
```

如果它需要是私有存储库，那么我可以添加`--private`。

```
gitstub console -gsr "FirstKillerApp" -gss "KillerApps" -gsu myusername -gsp mypassword --private 
```

我可以给 GitHub 项目一个描述，也可以修改第一个提交消息

```
gitstub console --sln -gsu myusername -gsp mypassword -gsd "The best app yet" -gsc "Initialized with the console template" 
```

如果我的模板有额外的参数，我可以传递这些参数——任何没有保留给`gitstub`的参数都将按照原来的顺序传递给`dotnet new`命令，所以

```
gitstub webapp -au Individual --sln -gsu myusername -gsp mypassword -gsd "Authed web app example" -gsc "Initialized with webapp and individual authentication" 
```

将在项目创建步骤运行`dotnet new webapp -au Individual`。

如果我有一个现有的项目，我仍然可以通过包含`--existing`开关来使用`gitstub`让事情进展。这样，`gitstub`将跳过项目/解决方案创建步骤，只提交文件夹中已经存在的内容。它还是加了`.gitignore`，所以不会接 obj/bin/。vs 什么的。

```
gitstub -gsu myusername -gsp mypassword --existing 
```

## 接下来是什么

你可以跟进，发布问题，功能需求，告诉我如何在开源方面做得更好，等等。在 [GitHub 回购](https://github.com/erikest/gitstub)。

在我看来，未来的特色是

*   通过实现 GitHub OAuth 并为会话缓存一个令牌，不必每次都传递用户名和密码。
*   一个可以创建 Azure Devops 项目并将其连接到 GitHub 项目的开关
*   一个`nuget`开关，它将在模板输出中搜索一个. nuspec 文件，并用适当的项目存储库和项目名称修改它

我非常欢迎反馈和建议，所以请让我知道你的想法！