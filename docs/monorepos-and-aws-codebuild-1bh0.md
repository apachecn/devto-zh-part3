# monrepos 和 aws 代码构建

> 原文：<https://dev.to/matttyler/monorepos-and-aws-codebuild-1bh0>

一旦达到可观的规模，单一回购通常需要专门的工具来有效管理。我们最近与一个客户合作，该客户有一个大型的基于节点的 monorepo，它遇到了越来越长的构建时间。通过引入包括使用 lerna、更新的 npm 功能(ci 安装、包缓存)和消除重复开发依赖项在内的优化，我们能够实现 1000%的速度提升。

这个故事从一个简单的谜题开始。一个特定的存储库需要很长时间来构建。目前的流程是在每次 PR 出现时触发一次构建，这样就可以针对 PR 运行测试，并确认代码合并是安全的。然而，存储库变得越来越大，并且在 CI 期间增加了额外的检查，以对代码执行额外的静态分析。虽然这一切都很好，但生活中没有什么是免费的，整个开发团队以增加构建时间的形式为此付出了代价。

其结果是增加了开发人员等待反馈的时间。这通常会鼓励一些我们想要避免的负面行为；例如，避免编写测试，以越来越大的批量执行工作——这增加了失败的风险，直接推送到主设备以避免检查，禁用检查等。这造成了一个越来越差的反馈循环，降低了发布给最终用户的代码的质量。

改善这一点的第一步是好好看看存储库是如何构造的。目前的状态是一个相当大的 monorepo，几乎完全是用 node/typescript 编写的。它由几个节点模块组成，其中一些模块相互嵌套。此外，不同模块之间存在一些代码重复。没有一个模块被发布到 NPM 或一个私人托管的 NPM 存储库。有几个“服务”模块由直接部署到 AWS 的无服务器项目组成，还有几个 AWS CDK 项目生成云形成模板，存储在 S3 桶中并按需实例化。

最近有很多关于单边回购的文章，既有[支持](https://medium.com/@adamhjk/monorepo-please-do-3657e08a4b70)的，也有[反对](https://medium.com/@mattklein123/monorepos-please-dont-e9a279be011b)的。有一件事经常没有被提及，那就是包管理在你的选择中扮演了怎样的角色。我生活和工作在硅谷泡沫之外——在开发流程次优的公司工作对我来说很正常，如果一切都是阳光和彩虹，我就不会在那里工作了。例如，这个项目位于一家企业公司，该公司既不想将代码发布到公共的 NPM 存储库中，也没有像 JFrog Artifactory 或 Sonatype Nexus 这样的托管包管理解决方案。根据我的个人经验，这种情况在使用遗留工具的企业和小型商店中并不少见，或者与很少有开源经验的开发人员一起工作。在后一种情况下，快速部署托管包管理服务器通常不会太困难。在较大的组织中，这可能有点困难，因为商业案例需要仔细准备和批准，然后可能需要一些时间进行采购和安装。当这种情况发生时，不使用单一回购是一种奢侈，你负担不起。考虑到这一点，我开始考虑如何改进现有的 monorepo 及其与 AWS CodeBuild 的交互。

> ...在协作和代码共享方面，大规模地，开发人员通过更高层的工具接触到代码的子部分。代码是在单回购协议中还是在多回购协议中并不重要；正在解决的问题是相同的...——Matt Klein，工程师@ Lyft &特使的创造者

第一步是引入一些东西来帮助我们管理单一回购。我之前曾使用“lerna”成功管理过另一个基于节点的 monorepo。我开始了一项相当漫长的任务，对我们已经拥有的东西进行重组。这意味着从高度嵌套的模块结构转移到更加扁平的结构——从技术上讲，如果每个模块都被分离到自己的存储库中，就会出现这种情况。有了这些，现在管理每个模块的依赖关系变得更容易了，并且可以清晰地分离关注点。我们还在 lerna 中使用了一个名为“提升”的功能，该功能可以消除许多软件包可能依赖的依赖性。

不幸的是，我们有一个起重小问题，这意味着我们必须删除它。提升包括将软件包安装在存储库的基本 node_modules 目录中，而不是安装在特定的软件包中——因此，您的“子”软件包将解析它们在基本目录中的所有依赖关系，而不是它们自己的 node_modules 文件夹。然而，我们的一些包需要捆绑它们的依赖项，不幸的是，这对于 lerna 的提升来说是不可能的，因为它们会试图打包它们的本地 node_modules 文件夹，而这些文件夹什么也不包含。如果 lerna 的提升有能力被限制在开发依赖上，这个问题可能已经消失了。但是不幸的是，它没有，因此我们需要禁用提升。

尽管转移到 lerna 是为了更好地管理库，这意味着我们仍然不得不考虑相当长的构建时间，这是由提升的移除引起的。正是在这一点上，我开始检查我们的构建环境和行动，以确定可能的改进。这包括学习更多关于 npm 打包、缓存和安装如何工作的知识，从中我能够想出一些事情来尝试。

接下来要做的改进是使用`npm ci`。这指示 npm 不直接从 package.json 文件中的定义解析依赖项，而是使用 package-lock.json 文件。从根本上说，这指示 npm 安装锁文件中调用的所有包，而不是从顶层依赖项开始解决所有问题。这提供了适度的速度提升(大约 20%)，对于一行代码的微小变化来说，这已经不错了。

尽管如此，安装时间仍然很长——花费的时间和运行我们整个测试套件的时间差不多。这很奇怪，因为我们的测试套件包含了大量的集成测试，通过网络轮询外部端点。正是在这一点上，我开始研究如何缓存我们的包，以便它们可以更快地安装。

幸运的是，代码构建确实能够在构建之间缓存资产。为此，使用对要缓存的项(通常是一个目录)的引用来配置构建步骤，并提供对 S3 位置(缓存项将被持久化的位置)的引用。这确实要求您的包管理器在某个位置缓存任何已安装的依赖项——幸运的是，npm 的更高版本做到了这一点。

配置代码构建步骤以使用缓存相对简单，例如

```
 CodeBuildProjectApplication:
    Type: 'AWS::CodeBuild::Project'
    DependsOn: CodeBuildRole
    Properties:
    # The cache setting is used to configure where our cached items will be stored
      Cache:
        Type: S3
        Location: !Join ['/', [!Ref ArtifactRepositoryBucket, 'cache']]
      Artifacts:
        Name: !Ref ArtifactFileName
        Location: !Ref ArtifactRepositoryBucket
        OverrideArtifactName: true
        Packaging: 'ZIP'
        Type: 'S3'
      Description: Build Application
      Environment:
        Type: linuxContainer
        ComputeType: BUILD_GENERAL1_SMALL
        Image: aws/codebuild/nodejs:8.11.0
      Name: !Ref 'AWS::StackName'
      ServiceRole: !GetAtt CodeBuildRole.Arn
      Source:
        BuildSpec: !Ref BuildSpecFileName
        Location: !Ref GitHubProjectURL
        GitCloneDepth: 0
        Type: 'GITHUB_ENTERPRISE'
      TimeoutInMinutes: 10 
```

一旦完成，您需要在您的 buildspec 文件中实际指定哪些文件构成缓存。AWS Codebuild 将确保在各步骤之间缓存该位置的文件。

```
version: 0.2
phases:
  install:
    commands:
      # npm configuration
      # here i configure npm to set the cache at /root/.npm
      - npm config -g set prefer-offline true
      - npm config -g set cache /root/.npm
      - npm config get cache

      - cd ${CODEBUILD_SRC_DIR}
      # perform a CI installation of base packages
      - npm ci

  pre_build:
    commands:
      # Clean entire monorepo and install dependent packages
      - ./node_modules/.bin/lerna bootstrap --ci --ignore-scripts --concurrency 4

  build:
    commands:
      # Build everything
      - ./node_modules/.bin/lerna run build --concurrency 4

  post_build:
    commands:
      # execute all the tests
      - NODE_OPTIONS="--max-old-space-size=4096" ./node_modules/.bin/jest --ci --config=jest.config.ci.js --coverage=true --no-cache --maxWorkers=4

artifacts:
  files:
    # include built artefacts

# At this step, we instruct codebuild to cache all items that in the NPM Cache
# that we configured earlier
cache:
  paths:
    - /root/.npm/**/* 
```

有了这个配置，我希望它能在安装我的依赖项的时间上有一个公平的改进。不幸的是，这并没有发生，我得到了一个几乎不明显的改善。这让我挠头了一会儿。我浏览了一下本地机器上的软件包缓存，注意到这些软件包作为压缩归档文件(tar.gz)存储在 npm 缓存文件夹中——如果您试图安装一个以前安装过的软件包，它会通过将匹配的归档文件解压缩到适当的 node_modules 文件夹来从缓存中安装。此时，我决定看看一个公共(尽管复杂)包有多少依赖项。我使用下面的[网站](https://npm.anvaka.com/)来了解 Jest 有多少依赖项，实际上我们所有的包都依赖这些依赖项。然后我注意到一个很有启发性的事实，jest 有一个大约 900 个包的完整依赖树。Eep。那时我意识到我们的“安装”时间不受远程获取包的网络时间的限制——而是将这些依赖项解压缩到每个目录的时间。

有两种方法可以改善这一点——更好的硬件，以及减少安装这些依赖项的次数。前者是通过扩大构建环境的规模来实现的。后者稍微复杂一些。我们通过将开发依赖项移动到顶层 package.json 来模拟提升特性，并将这些依赖项作为对等依赖项调用，以提醒它们在子包中是必需的。

为了让 Jest 在这种安排下表现得更好，还需要做一些额外的修改。以前，我们在每个项目上单独调用 jest，每个项目都有自己单独的 jest 配置。相反，我们在 monorepo 的基础上提供了一个全局 jest 配置，它能够定位和执行整个存储库中的所有测试。这确实需要您根据惯例来命名和定位测试，幸运的是我们正在这样做。

> 在这一点上，我们已经解决了最糟糕的构建时间问题，从而将完整构建存储库的时间从大约 20 分钟减少到了 3 分钟。

还可以进行额外的优化。我们添加了一个在多项目模式下使用 Jest 的配置，当与 lerna 的‘changed’命令结合使用时，可以用来确保我们只构建和测试库中已经更改的包。这使得我们的 CI 检查对于只涉及几个包的变更运行得更快(这有一个额外的效果，即鼓励我们的开发人员进行许多较小的变更，而不是较少的较大变更)。我们还删除了 ts-jest，支持 Jest 24 对 babel 7 的内置支持。

我们在多项目模式下使用 jest 的配置如下所示-

```
module.exports = {
    globals: {},
    // Each path in the array below uses the jest configuration
    // at that path for that particular 'project'.
    projects: [
        "<rootDir>/packages/package_one",
        "<rootDir>/packages/package_two",
        "<rootDir>/packages/package_three",
        "<rootDir>/packages/package_four",
    ],
    testEnvironment: "node",
    testPathIgnorePatterns: ["/lib/", "/node_modules/"],
    moduleFileExtensions: ["ts", "tsx", "js", "jsx", "json", "node"],
    coverageDirectory: "./.cover",
    collectCoverageFrom: ["**/src/**/*.{ts}", "!**/node_modules/**"],
    coverageThreshold: {
        "global": {
            "branches": 80,
            "functions": 80,
            "lines": 80,
            "statements": 80
        }
    }
}; 
```

这样，我们可以用下面的命令指定执行一组特定的项目-

```
./node_modules/.bin/jest --ci "/tests/.*\\.(test|spec|integration)?\\.(ts|tsx)$" --projects ./packages/package_one ./packages/package_two 
```

如果我们将这一点与 lerna changed 结合起来，我们现在可以确定自从我们向 master 提交任何数据之后哪些包发生了更改，并且只测试那些包。在我们的例子中，可以发出下面的命令。

```
./node_modules/.bin/jest --ci "/tests/.*\\.(test|spec|integration)?\\.(ts|tsx)$" --projects $(./node_modules/.bin/lerna list --all -p --since master | grep -Eo -e packages/.+) 
```

这允许我们只针对已经改变的包执行测试。这确实需要您在 AWS Codebuild 中对存储库执行完整的签出，这与执行浅层克隆的默认行为相反。

> 在这一点上，对于典型的变更，我们的构建和测试周期通常在 2 分钟内执行——或者说执行速度提高了 1000%。

我希望这能让每个人都很好地了解保持 monorepo 构建过程高效运行所需采取的步骤。在这种情况下，它包括:

*   安装专门的工具来管理 monorepo，在我们的例子中是 lerna。
*   利用 AWS 代码构建中的依赖缓存。
*   利用 npm ci 解决和安装依赖关系。
*   在多项目模式下运行 jest 以及 lerna changed。

希望这对任何想采用 Monorepo 方法的人有所帮助。

需要帮助解决存储库问题、安抚管道或增强构建吗？通过[contact @ mechanical rock . io](mailto:contact@mechanicalrock.io)取得联系。