# 使用 github repos 作为 Haskell 依赖项

> 原文：<https://dev.to/piq9117/using-github-repos-as-haskell-dependency-m2o>

我对 haskell 相当陌生，尤其是在打包库方面。
我很难找到如何将 github 库包含在我项目的`[name of your project].cabal`文件中。我去过 [cabal 用户指南](https://www.haskell.org/cabal/users-guide/)那里没有找到，至少一开始没有。还好 [@samderbyshire](https://twitter.com/samderbyshire/status/1088838958241538048) 看到了我的推文，回答了我的问题。

首先要做的是创建一个`cabal.project`文件，因为这个文件不会在你做`cabal init`时生成，然后包含下面的

```
source-repository-package
  type: git
  location: git://github.com/[repo owner]/[the repo i want to include as dependency].git

packages: ./[name of your project].cabal 
```

然后在您的[项目名称]中。cabal 文件包括`build-depends`部分下的存储库

```
build-depends:    base ^>= 4.11.1.0
                , the-repo-you-want-to-include 
```

我想这么做的原因是因为我想使用我在一个项目中编写的库，而不用麻烦地将它发布到 [stackage](https://www.stackage.org/) 或 [hackage](https://hackage.haskell.org/) 。