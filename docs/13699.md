# 用全新的 Symfony 扩展驯服 Behat

> 原文：<https://dev.to/pamil/tame-behat-with-the-brand-new-symfony-extension-ih9>

这篇博文最初发表于 kamilkokot.com。

在这个[Behat](https://github.com/FriendsOfBehat)的 [Symfony 扩展](https://github.com/FriendsOfBehat/SymfonyExtension)首次发布两年多之后，我很高兴地宣布第二个主要版本的可用性，并写更多关于它的内容。

这是一个已经经过战斗考验的解决方案，sy lius T1 在 1300 多个场景中包含 14000 多个步骤，在像 T2 Akeneo T3 或 T4 Monofony T5 这样的项目中获得了支持。

它背后的使命是简化实现[行为](http://behat.org/en/latest/)上下文的过程，以便您可以专注于与利益相关者的沟通和写下业务特性，而不是搞清楚底层测试基础设施的细节。

### TL；速度三角形定位法(dead reckoning)

*   支持 Symfony 的自动布线和自动配置
*   Symfony Flex contrib recipe 使安装更容易
*   大多数基于 Symfony 3 和 4 的应用程序的零配置
*   统一的应用程序配置和 dotenv 文件处理(类似于 PHPUnit)
*   与貂皮集成，开箱即用
*   简化使用，将分机数量从三个减少到一个
*   更具描述性的异常和更可预测的整体行为改善了开发人员的体验

如果您想先尝试一下，[直接进入文档](https://github.com/FriendsOfBehat/SymfonyExtension#documentation)。

我正在编写 SymfonyExtension v2 的入门教程，如果你不想错过的话，请确保在 Twitter 上关注我。

### 支持 Symfony 的自动布线和自动配置

自从我开始与 Behat 和 Symfony 合作开发更复杂的项目以来，实现简单步骤所需的配置更改数量就成了一个问题，而且越来越严重。

无论是`Behat/Symfony2Extension`还是`FriendsOfBehat/SymfonyExtension`，在一个上下文中注入一个依赖项需要太多的麻烦，把我从建模的领域拉了出来。

常见的用例是有一个上下文来实现像`Given I am a logged in customer`这样的步骤，它被用在每个做 web 验收测试的套件中。

让我们假设我们需要两个使用相同上下文的 Behat 套件，这取决于`kernel`服务，并比较 Behat 的每个 Symfony 扩展所需的配置。

#### 行为/符号 2 扩展

```
# behat.yml
first_suite:
    contexts: - FeatureContext: kernel: '@kernel'            
second_suite:
    contexts: - FeatureContext: kernel: '@kernel' 
```

这个片段看起来没那么糟糕，主要是因为它的大小。但是，随着每一个新的依赖项或套件的出现，它会变得越来越长。向上下文添加新的依赖关系需要更改每个套件的配置。

#### friendsoffehat/SymfonyExtension v1

找到所有使用上下文的地方并修改它的依赖关系是一项枯燥的工作，这是我在第一版的 <abbr title="Friends Of Behat">FOB</abbr> 的 SymfonyExtension 中想要避免的。

将上下文注册为服务是可能的，这从根本上改变了你在行为中使用它们的方式。这就是为什么我决定编写自己的扩展来集成 Behat 和 Symfony。

```
# behat.yml
first_suite:
    contexts_services: - FeatureContext            
second_suite:
    contexts_services: - FeatureContext

# Symfony services file loaded by the extension
services:
    FeatureContext:
        arguments:
            - '@ __symfony__.kernel' tags: ['fob.context_service'] 
```

重复被删除了，但是它引入了更多的样板代码和自己的约定，这在一开始是很难得到的:

*   从 Symfony 应用程序容器引用服务时的前缀阻止了 IDEs 中的自动完成功能正常工作
*   内部基础设施需要标签才能将服务用作上下文，但它经常会导致错误，因为它很容易被遗忘
*   `contexts_services`而不是默认`contexts`并没有引入任何可衡量的好处，如果没有在每个套件中使用上下文服务，它应该可以防止与默认配置的冲突

手动注册上下文的整个过程并不愉快。

#### friendsoffehat/SymfonyExtension v2

最新版本通过将自动连接和自动配置合并为一流的机制并遵循已知的约定，减少了所需的样板代码。

```
# behat.yml
first_suite:
    contexts: - App\Tests\Behat\FeatureContext            
second_suite:
    contexts: - App\Tests\Behat\FeatureContext 
```

如果你的上下文构造函数有内核参数类型提示，这就是你所需要的一切。

无需担心重复的配置、奇怪的约定、标记上下文或在引用服务时使用前缀。您也可以注入私有服务！

该扩展提供了一个依赖注入配置，可以自动找到您的上下文并自动连接它们:

```
# config/services_test.yaml
services:
    _defaults:
        autowire: true
        autoconfigure: true

    App\Tests\Behat\:
        resource: '../tests/Behat/*' 
```

以下文件仅在测试环境中加载。您的生产和开发环境不会受到任何性能损失的影响。但是，如果您不喜欢依赖自动连接，您可以自己将上下文定义为一个服务——但是要记住将其公开:

```
# config/services_test.yaml
services:
    App\Tests\Behat\FeatureContext:
        arguments:
            - '@kernel'
        public: true 
```

### Symfony Flex 配方

如果您使用 Symfony Flex 来管理您的 Symfony 应用程序，安装和配置扩展就再简单不过了。

你所需要做的就是安装扩展并同意为它安装一个 contrib 菜谱:

```
composer install friends-of-behat/symfony-extension:^2.0 --dev
composer exec behat 
```

不到一分钟，您就可以使用 SymfonyExtension 设置 Behat，并准备好编写您的特性。

如果您不使用 Symfony Flex，[遵循安装文档](https://github.com/FriendsOfBehat/SymfonyExtension/blob/master/docs/01_installation.md#installation)。

### 大多数基于 Symfony 3 和 4 的应用程序的零配置

无论你使用的是 Symfony 3 还是 Symfony 4，新的扩展都能满足你的需求。

它将尝试猜测合理的默认配置，并且在大多数情况下，您不需要编写比下面更多的配置(假设 Flex 没有为您做到这一点):

```
# behat.yml
default:
    extensions:
        FriendsOfBehat\SymfonyExtension: ~ 
```

[了解更多关于默认设置的信息](https://github.com/FriendsOfBehat/SymfonyExtension/blob/master/docs/05_configuration_reference.md#configuration-reference)。

### 貂集成

这个扩展基于名为`symfony`的 BrowserKit 为[貂](http://mink.behat.org/en/latest/)提供了一个驱动。

如果您从`Behat\Symfony2Extension`切换，行为基本保持不变。只有一个很小的实现细节值得了解。

来自前一个扩展的驱动程序使用相同的内核来获取应用服务和处理请求。如果您的代码不是等幂的并且依赖于服务状态，这可能会导致意外的结果。

给定以下场景及其实现:

```
# Please do not write scenarios like this
# It is just for demonstration purposes
Given the service state is set to 42
Then I should get 42 from the API
And I should get 42 from the API

# Context implementation
/**
 * @Given the service state is set to :state
 */
public function serviceStateIs(string $state): void
{
    $this->service->setState($state);
}

/**
 * @Then I should get :state from the API
 */
public function iShouldGetFromAPI(string $state): void
{
    $this->minkSession->visit('/api/');
    assert($state === $this->minkSession->getPage()->getContent());
}

# Symfony controller
public function __invoke(): Response
{
    return new Response($this->service->getState());
} 
```

当使用`Behat\Symfony2Extension`时，这个场景将在第二个`Then`步骤失败。

发生这种情况是因为内核在每次请求后都会重新启动，所以第一个`Then`步骤使用的容器与之前的`Given`步骤使用的容器相同，但是第二个`Then`步骤使用的重新启动的容器不共享相同的状态。这可能会使你的测试变得脆弱，例如，当你改变断言的顺序时。

当使用`FriendsOfBehat\SymfonyExtension`运行时，场景将在第一个`Then`步骤失败，因为服务状态没有在 Behat 和 HTTP 应用程序之间共享。

如果你想了解更多，查看一下关于 Symfony 扩展之间差异的文档或者关于 Mink 集成的文档。

### 接下来是什么？

有了上面提到的所有这些新的、闪亮的功能，我仍然有一些想法在未来会很棒:

*   [提供貂皮服务](https://github.com/FriendsOfBehat/SymfonyExtension/issues/61)
*   [步骤定义的自动布线和自动配置](https://github.com/FriendsOfBehat/SymfonyExtension/issues/62)
*   [公开 BrowserKit API 作为 Mink API 的补充(有 Panther 支持)](https://github.com/FriendsOfBehat/SymfonyExtension/issues/67)

我做了一个 GitHub 项目，作为这个项目的路线图。