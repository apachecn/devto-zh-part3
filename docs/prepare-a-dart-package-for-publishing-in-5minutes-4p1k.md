# 在 5 分钟内准备好要发布的 dart 包

> 原文：<https://dev.to/5422m4n/prepare-a-dart-package-for-publishing-in-5minutes-4p1k>

当发布 dart 包[pub.dartlang.org](https://pub.dartlang.org/packages/valgene_cli)时，你应该花一分钟阅读[dart 团队](https://www.dartlang.org/tools/pub/publishing)关于发布的建议。然而，添加一些东西来展示你的新包有多好以及它能做多棒的事情是有意义的。

## dartanalyzer 和 code 林挺

使用类似于`dartanalyzer --fatal-warings lib/ bin/`的东西来扫描你的代码中的问题。

## 测试和覆盖率

假设你对测试很熟悉，并且写了大量的测试，那么收集这些数据并在你的自述文件中准备一些显示百分比的数据会很好。就像这个 [![Coverage Status](img/52b242b3647171f9d2f019da9fa5be0c.png)](https://coveralls.io/github/valgene/valgene-cli?branch=master)

为了达到这个目的，我们使用 *test_coverage* 包，将其添加到您的`pubspec.yaml` :

```
dev_dependencies:
  test: ^1.4.0
  mockito: ^3.0.0
  test_coverage: ^0.2.0 
```

Enter fullscreen mode Exit fullscreen mode

然后您可以使用命令`pub run test_coverage`运行您的测试。
它将在`test/.test_coverage.dart`中生成一个小测试，该测试结合了所有测试的执行，并且覆盖度量将在测试期间被收集并存储在`coverage/lcov.info`中。

### 覆盖率为报告

如果您对覆盖率数据感兴趣，可以使用`genhtml -o coverage coverage/lcov.info`来生成一个很好的 html 报告，以便在浏览器中查看。但只是局部的。

对于 github，我将使用[工作服](https://coveralls.io/)平台来生成报告和漂亮的徽章。这是免费的，而且帐户很容易设置。稍后将详细介绍这一点。

## dartfmt 和好看的可读性

对于标准代码格式规则没有问题，最好运行`dartfmt -n --set-exit-if-changed lib/ test/ bin/`来检查或者`dartfmt -w lib/ test/ bin/`来重新格式化你的代码库。在早期和每次构建时提示它。

# 现在让我们来自动化这个东西

为此，让我们准备一个小的 Makefile 文件(shell 脚本也可以，但我更喜欢为小项目制作)

```
DARTANALYZER_FLAGS=--fatal-warnings

build: lib/*dart test/*dart bin/*dart deps
    dartanalyzer ${DARTANALYZER_FLAGS} lib/ bin/
    dartfmt -n --set-exit-if-changed lib/ test/ bin/
    pub run test_coverage

deps: pubspec.yaml
    pub get

reformatting:
    dartfmt -w lib/ test/ bin/

build-local: reformatting build
    genhtml -o coverage coverage/lcov.info
    open coverage/index.html

publish:
    pub publish 
```

Enter fullscreen mode Exit fullscreen mode

小巧轻便。你可以看到建造目标。默认的是第一个，名为`build`，travis 稍后将使用它来执行构建。

在本地，您可以运行`make build-local`来获得代码重新格式化的帮助，并检查覆盖率报告。

## Travis CI 作为构建服务器

将. travis.yml 添加到您的项目:

```
language: dart
dart:
  - stable
install:
  - gem install coveralls-lcov
script: make
after_success:
  - coveralls-lcov --repo-token $COVERALLS_TOKEN coverage/lcov.info 
```

Enter fullscreen mode Exit fullscreen mode

然后去[travis-ci.org](https://travis-ci.org)建立账户和项目。在那里，您可以定义一个构建变量`COVERALLS_TOKEN`，它应该包含您将在 workalls . io 上获得的令牌(如前所述)。

## 不要忘记您自述文件中的所有徽章

因此，需要添加构建状态、覆盖率和发布版本标记。这里我展示了我的小工具 [valgene](https://github.com/valgene/valgene-cli) 的例子，只是为了演示。

```
[![Build Status](https://travis-ci.org/valgene/valgene-cli.svg?branch=master)](https://travis-ci.org/valgene/valgene-cli#)
[![Coverage Status](https://coveralls.io/repos/github/valgene/valgene-cli/badge.svg?branch=master)](https://coveralls.io/github/valgene/valgene-cli?branch=master)
[![Pub](https://img.shields.io/pub/v/valgene_cli.svg)](https://pub.dartlang.org/packages/valgene_cli) 
```

Enter fullscreen mode Exit fullscreen mode

那就这么看吧:
[![Build Status](img/0e5e36761c66587741a337e7e344f18c.png)](https://travis-ci.org/valgene/valgene-cli#)
[![Coverage Status](img/52b242b3647171f9d2f019da9fa5be0c.png)](https://coveralls.io/github/valgene/valgene-cli?branch=master)
[![Pub](img/0a1fa09fbbdb58df2e1d3392bf5a777f.png)](https://pub.dartlang.org/packages/valgene_cli)

# 最后发布你的包

在所有这些提交之后，创建标签，等待绿色构建，然后运行`make publish`就完成了。刚刚发布了一个漂亮闪亮的 dart 包。

享受并感谢阅读