# 我如何添加 AppVeyor，Codecov 到？Net Core 开源项目

> 原文：<https://dev.to/codingdefined/how-i-added-appveyor-codecov-to-net-core-open-source-project-54m6>

[源链接](https://www.codingdefined.com/2018/10/how-i-added-appveyor-codecov-to-net.html)

AppVeyor 是一个持续集成和部署解决方案，您可以在任何平台上更快地构建、测试和部署您的应用程序。使用 appveyor 的一个好处是它对开源库是免费的。当你的回购协议接受人们的拉请求，然后你可以测试拉请求是否合并，这实际上是很好的。

另一方面，Codec 帮助您对代码进行健康检查，也就是说，它是在执行自动化测试时对执行了多少行代码的度量。现在 100%的代码覆盖率意味着所有的代码都被测试覆盖，但是这很难实现。

我必须做大量的提交来测试 AppVeyor 和 Codecov，最后，它成功了，我可以为我的应用程序构建和生成代码覆盖报告了。首先，我已经开始使用 NuGet 包 OpenCover 在我的本地环境中创建代码覆盖，并且它在试错方法后工作。

我使用的脚本如下

```
OpenCover.Console.exe -register:user
-target:"C:\Program Files\dotnet\dotnet.exe"
-targetargs:"test\LetsDisc.Tests\bin\Debug\netcoreapp2.1\LetsDisc.Tests.dll -noshadow"
-oldStyle -output:".\coverage.xml"
-filter:"+[LetsDisc*]* -[Tests*]*" -coverbytest:"XUnit" 
```

但是作为一个开发者，我们应该知道，在本地有效的东西，在其他一些环境中有 100%的概率是无效的。这就是我在 AppVeyor 中尝试运行上述脚本时发生的情况，出现了一些或其他问题。但最后，我成功了。所以最终的剧本是

```
version: 1.0.{build}
image: Visual Studio 2017
configuration: Release
skip_tags: true
before_build:
 - nuget restore
 - nuget install OpenCover -OutputDirectory packages -Version 4.6.519
build_script:
 - msbuild /verbosity:quiet LetsDisc.sln
test_script:
 - .\packages\OpenCover.4.6.519\tools\OpenCover.Console.exe
-register:user
-target:"C:/Program Files/dotnet/dotnet.exe"
-targetargs:"test --logger:trx;LogFileName=results.trx
/p:DebugType=full
C:\projects\letsdisc\test\LetsDisc.Tests\LetsDisc.Tests.csproj"
-output:"coverage.xml" -filter:"+[LetsDisc*]* -[Tests*]*"
after_test:
  - ps: |
      $env:PATH = 'C:\msys64\usr\bin;' + $env:PATH
      Invoke-WebRequest -Uri 'https://codecov.io/bash' -OutFile codecov.sh
      bash codecov.sh -f "coverage.xml" 
```

上面的脚本意味着在每次构建时，我都会用 1.0 版本更新它。{buildNumber}，然后使用 Visual Studio 2017 作为映像来构建项目，然后使用 configuration 作为发布模式，不进行调试，以便于部署。在构建之前，只需恢复所有的 nuget 包，以及安装与 AppVeyor 一起工作的 OpenCover 特定版本。之后，使用 msbuild 构建项目，然后运行我的测试脚本来运行测试并创建覆盖率报告。测试成功后，将 coverage.xml 文件部署到 Codecov 网站。

回购:[https://github.com/codingdefined/LetsDisc](https://github.com/codingdefined/LetsDisc)

这很有趣，也是一次学习实验，我学会了使用 Appveyor 应用程序，并将覆盖报告添加到我的项目中。