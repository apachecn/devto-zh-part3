# ActiveRecord 回调的好与坏

> 原文：<https://dev.to/mickeytgl/the-good-and-bad-of-activerecord-callbacks-p4a>

rails 控制器内部的回调引发了很多争议。一方面，DHH 提倡使用回调来管理辅助复杂性，另一方面，有人称之为你应该避免的最大的 Rails 代码味道。在这篇文章中，我想分析争论的双方，并给出一个例子，说明回调是如何在我们的代码库的一个区域中实现的，以及它如何对我们有利或有害。

### 优点

1) **它允许将复杂性放在次要的方面:**当我们想要获得对逻辑的高级理解时，它允许开发人员以一种直接的方式关注逻辑的主要流程。大多数时候，如果你在查看你的`create`方法，你关心的是如何处理`create`动作，而不是发出的`:welcome_email`或发送给接收者的`:notification`。

2) **它“工作正常”:**拥有触发副作用的回调函数，比如发送通知，或者触发后台作业，可以让你的控制器操作一目了然，因为你不必弄清楚它内部正在发生的一切。因为你不需要理解流程的每一个细节，你可以在你的应用中更快地做出改变，假设你遵循默认的路径，这就是约定胜于配置的原则。DHH 实际上在他 2018 年的 T2 主题演讲中把这一点很好地框定为“概念压缩”。

### 弊端

1) **不想要的副作用:**起作用的副作用在起作用的时候很棒，但不起作用的时候就特别痛苦和难以处理。遵循你的正常流程可能会让你认为某种价值正在回归，但随后得到完全不同的东西肯定会令人沮丧。

2) **难以选择退出:**这是连 DHH 都承认的一个可能的罪魁祸首，解释了为什么回调会有如此糟糕的名声，因为在大量回调的情况下，这很容易反过来咬许多开发者。为了解决这个问题，他在 Basecamp 代码库中引入了`supressed`的概念。

3) **紧耦合动作:**这是我特别发现最危险的一个，使用回调意味着你不能单独测试回调动作，因为你需要首先在你的对象上调用`create`或`update`，这将设置一系列回调。作为一个极端但非常真实的例子，你无法在不触发`#charge_credit_card`的情况下测试你的`#send_receipt`方法，这可能会非常快地出错。

### 示例实现:

下面是我们的`MatchOpportunities#update`动作的一个例子:

```
class CatchOpportunitiesController < ApplicationController

  before_action :set_catch_opportunity
  ...

  def update
    if @catch_opportunity.update(catch_opportunity_params)
      if catch_opportunity_params[:interested_in_fish]
        unless catch_opportunity_params[:fisherman_profile_up_to_date]
          post_lack_of_interest_survey(
            catch_opportunity_params[:response_text],
            @catch_opportunity.id,
            'fisherman_profile_up_to_update'
          )
        end
      else
        catch_opportunity_params[:interested_in_fish]
        post_lack_of_interest_survey(
          catch_opportunity_params[:response_text],
          @catch_opportunity.id,
          'survey_results'
        )
      end
      MatchOpportunityMailer
        .with(catch_opportunity: @catch_opportunity)
        .fisherman_response_email
        .deliver_now
      render json: @catch_opportunity, status: 200
    else
      render json: @catch_opportunity.errors, status: 422
    end
  end 
```

正如您所看到的，这仅仅是`#update`方法的大量职责，利用 ActiveRecord 回调的能力，新的代码看起来会像这样:

```
class CatchOpportunitiesController < ApplicationController

  after_action :send_email_with_fisherman_response,
                  only: :update, if: :profile_response_complete?
  after_action :post_fisherman_response, only: :update

  ...

  def update
    if @catch_opportunity.update(catch_opportunity_params)
      render json: @catch_opportunity, status: 200
    else
      render json: @catch_opportunity.errors, status: 422
    end
  end

  private

    def post_fisherman_response
      # handle posting to external service
    end

    def send_email_with_fisherman_response
      # handles email delivery
    end

    def profile_response_complete?
      # decides if callback should be performed at all.
    end 
```

这个代码片段的第二个版本看起来可读性更好，事实上我们成功地消除了双重嵌套条件是一个巨大的胜利，尽管`#update`方法确实没有给你任何关于电子邮件将被发送出去的提示，但最终，这项工作并没有被隐藏，它让你在文件的顶部知道将执行什么操作以及在哪里执行。当然，这可能不是这段代码最完美、最漂亮、最易于测试的形式，但它无疑是对原始代码的改进。

rails 框架的创始人 DHH(有意识地)支持使用回调，这一事实对任何人来说都不奇怪，因为 Rails 框架和 ruby 语言本身就有很多神奇之处(隐含的逻辑允许你更快地启动和运行，但隐藏了复杂性，使得内部工作不那么明显)

### 个人想法

有一些反对回调的观点，我觉得不是很有说服力。例如，在我看来，声明显式代码总是天生比隐式代码好有点言过其实。尽管像函数式语言这样的地方将这一思想作为他们哲学的核心部分，但 Ruby 和 Rails 都以拥有大量的语法糖并嵌入其中而闻名，这恰恰有助于人们快速使用它们。

我确实看到了这是如何容易被滥用的，我发现它引入了紧密耦合的动作，这些动作不能在没有另一个的情况下运行，并且违反了控制器的[单一责任原则](https://de.wikipedia.org/wiki/Single-Responsibility-Prinzip)(它应该只关注将数据保存到数据库)。在试图测试发送电子邮件时向用户的信用卡收费是一个极端但真实的例子。

说到底，我认为这是 Ruby 价值观的展示:一个非常强大的工具，就像[猴子打补丁](https://stackoverflow.com/questions/5626193/what-is-monkey-patching)一样，它让你可以自由地构建非常强大的软件，但也可能让你被错误地使用。我不认为应该不惜一切代价避免它，但像大多数事情一样，它有它的权衡，所以要谨慎对待。