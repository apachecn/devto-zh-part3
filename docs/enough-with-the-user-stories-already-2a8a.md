# 用户故事已经讲得够多了！

> 原文：<https://dev.to/redfred7/enough-with-the-user-stories-already-2a8a>

### 敏捷之雾

敏捷的核心是一套[指导原则](https://agilemanifesto.org/principles.html)，由各种过程、方法和框架的创造者和实践者来解释。就像有许多方言的语言一样，这导致了同一个单词在每种方言中的意思略有不同。问问题“什么是用户故事？”会给你十几个不同的答案。一个用户故事可以是一个需求、一个特性、一个描述、一个最终目标、一个高层次的抽象、一小块商业价值和许多其他的东西，这取决于你问谁——以及当你问他们的时候*；)*

我倾向于同意迈克·科恩的定义:用户故事是对一个特性的描述。当然，这只是把问题移到了“什么是特性？”。快速的谷歌搜索向我们展示了丰富的定义:一个特性是一个功能，一个能力，一个需求，一个以形式表达的客户价值函数，一个史诗的一部分，等等。如此循环往复。

这里有一个我遇到过很多次的常见模式，我敢打赌你也遇到过:一个利益相关者要求查看“用户故事”。涉众后来回来抱怨用户故事太技术化或太抽象，或者涉及了错误的参与者

这里有一个例子:

```
As an end-user 
I want to log-in with my social media credentials
so I don't have to remember extra usernames and passwords 
```

Enter fullscreen mode Exit fullscreen mode

这是一个用户故事，对吗？你可以把这个给你的利益相关者，但是他们可能会抱怨这个太普通了，没有任何价值。他们是对的。然后你给他们这个故事:

```
As an end-user
When I log-in I want to be re-directed to the login page of my selected Identity Provider
So that, upon entering my credentials, the IP can redirect me back to our system with a valid SAML token 
```

Enter fullscreen mode Exit fullscreen mode

利益相关者茫然地看着你。你不明白问题出在哪里。他们想要用户故事，你给他们用户故事，那么 WTF？问题是，利益相关者想要非常具体的东西，但是他们没有合适的词语来表达，因为用户故事是一个负载的术语。让我们把这个想法保持一分钟。

### 一个需求域模型

John Ferguson Smart 在他的《BDD in Action》一书中详细阐述了影响图的使用，这是一种由 [Gojko Adzic](https://www.impactmapping.org/index.html) 引入的技术，对于回答需求和规范模型的大问题非常有用:

*   我们为什么要建立这个系统？
*   谁从中受益？
*   我们需要建造什么？
*   我们将如何建造它？

这些可以直接转化为需求和规范领域实体:

*   业务目标(为什么):我们系统的预期收益
*   涉众:受我们系统影响的人
*   能力(什么):使涉众能够实现业务目标的系统能力
*   特性(如何):有助于交付能力的功能

就像那样，我们有定义良好的领域实体。我们的需求由涉众和业务目标上下文中的能力来表示。我们的规范是特性，它描述了我们将实现的系统功能，以交付系统的能力。在真正的 BDD 方式中，一个特性是由一个用户故事来描述的，后面跟着一些场景/接受标准。一个功能**不是一个用户故事**！我们也可以使用用户故事来描述功能、特性和业务目标。

[![Impact Map](img/369017d752b2199a0304afbf0e5ec8d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--apggNcT6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://filedn.com/lBg7bSLnQRakb21qRGSa3pS/Impact%2520Map%2520social%2520login.png)

1.  我们的系统需求是受益涉众和有效业务目标上下文中的能力
2.  我们可以用用户故事来描述功能和业务目标。这并不意味着故事就是这些东西。
3.  我们的规范描述了交付一项功能(即特性)所需的系统行为

### 用户故事只是需求领域实体的描述工具。

所以让我们回到困惑的利益相关者。当他们问你用户故事时，他们问的是*特性*！他们需要你提供的系统功能的描述，以帮助他们实现目标。不多不少。用户故事不是领域实体，它们只是描述符。我们需要开始使用领域实体进行交流，而不是描述符。

因此，我们向我们的利益相关者提供以下内容:

```
Feature: Login with Twitter
As a end-user with a Twitter account
I want to log-in with my Twitter credentials
so I don't have to know extra usernames and passwords

  Scenario: First-time login with Twitter, already signed-on
    Given the user visits our system's login page
    And the user hasn't signed-on our system with Twitter before
    And the user is not already signed onto Twitter
    When the user chooses to sign-on our system with Twitter credentials
    Then the user is presented with a Twitter login page

  Scenario: First-time login with Twitter, not currently signed-on
    Given the user visits our system's login page
    And the user hasn't signed-on our system with Twitter before
    And the user is already signed onto Twitter
    When the user chooses to sign-on our system with Twitter credentials
    Then the user is presented with a Twitter permissions and confirmation page

... (more scenarios) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，为了最好地描述和说明我们的功能，我们是如何在功能标题下呈现用户故事的。还要注意，即使我们不使用用户故事，我们的特性*仍然是有效和可用的。用户故事不是特色！它只是给了我们一个很好的叙述，所以在我们的特写中使用它是很有用的。但仅此而已。我们不是在创造用户故事，我们是在定义功能。*

作为开发人员、产品所有者或业务分析师，我们被客户的愿望、需求和欲望所淹没。我们的第一个问题应该是:“利益相关者应该能够用我们的系统做什么，以满足他们的愿望或需求？”。一旦我们回答了这个问题，我们的下一个问题应该是“我们如何交付这个能力？”。这一思考过程的结果将为我们提供一套规范(特性),实现利益相关者对实现某些业务目标的要求。因此，让我们拨开迷雾，开始更多地讨论功能和特性，而不是它们的通用和变体描述符。用户故事已经够多了！