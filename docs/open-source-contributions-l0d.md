# 开源贡献

> 原文：<https://dev.to/banesag/open-source-contributions-l0d>

[![alt text](img/349f8a884484f0cc682f856d3d437445.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UUFctU5F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fvt9mgu8kod7gw6xbheb.png)

### 贡献一个开源项目的好处

开源是一种很好方式，不仅可以为开源项目做贡献，还可以在通过其他人或组织的代码或项目做贡献的同时实践您所学到的东西。毫无疑问，开源不仅通过贡献者的创新贡献给组织带来好处，而且贡献者也通过将他或她的名字放在项目上而受益，特别是如果你希望获得一些曝光率的话。

为开源做出贡献的另一个好处是，你可以获得虚拟工作的经验，因为你不认识参与项目的人，你的贡献基于书面说明，也可以通过在审查未知代码或项目时使用批判性思维来提供解决方案。

**获得编程经验**

开源的美妙之处在于任何人都可以做出贡献，如果你没有经验，它会让你获得编程经验。如果你害怕破坏代码，不要担心，有适当的控制来防止它，任何提交都需要得到批准。因此，当新程序员在不拿程序(或他们的工作)冒险的情况下获得经验时，有经验的程序员在没有 ***调试*** 干扰的情况下从事其他相关活动。

**寻找开源项目**

你可以通过 GitHub 找到开源项目，但是你也可以通过 twitter 加入开源社区，或者在你想贡献的特定公司的网站上寻找。

**步骤**

1.  在 GitHub 上，导航到您想要贡献的存储库

2.  在存储库名称下，单击克隆或下载。

3.  在“使用 HTTPs 克隆”部分，单击以复制存储库的克隆 URL。

4.  开门，巴什。

5.  将目录切换到您在第 2 步中克隆的 fork 的位置:创建您的 fork 的本地克隆。

    要转到您的主目录，只需键入 cd，不要输入其他文本。要列出当前目录中的文件和文件夹，请键入 ls。
    要进入您列出的目录之一，请键入 cd your_listed_directory。
    要进入上一个目录，键入 cd...

6.  键入 git remote -v，然后按 Enter 键。您将看到当前为您的 fork 配置的远程存储库。

```
 git remote -v
    origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git // (fetch)
    origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git // (push) 
```

1.  键入 git remote add upstream，然后粘贴您在步骤 2 中复制的 URL，并按 Enter 键。它看起来会像这样:

```
 git remote add upstream https://github.com/your_repository 
```

1.  要验证您为 fork 指定的新上游存储库，请键入 git remote -vagain。您应该看到 fork 的 URL 作为起点，原始存储库的 URL 作为上游

```
 git remote -v
    origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git // (fetch)
    origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git // (push)
    upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git // (fetch)
    upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git // (push) 
```