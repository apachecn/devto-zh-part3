# Ruby On Rails 中使用 Cleave.js 和 Money gem 进行货币输入屏蔽

> 原文：<https://dev.to/bilalbudhani/currency-input-masking-with-cleave-js-and-money-gem-in-ruby-on-rails-490l>

依赖用户的输入是构建健壮应用程序的关键部分。这就是为什么我试图对表单或其他形式的输入数据进行强验证。需要非常精确地捕捉的一个重要输入是数量。你的铁路生态系统有一个惊人的宝石称为金钱与数量。然而，我希望在前端为我的用户处理与数量相关的输入时有同样优雅的体验。

在尝试了几个 Javascript 库之后，我最终选择了 Cleave.js，我将其配置为与 Money gem 协同工作。这是最终结果

[![](img/7e53ce9c7b1888f2dd5f1a3f4f6a7fb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wWWaPA-e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6gdgr56ck13b9w97izn3.gif)

### 下面介绍如何获得这种体验

首先，让我们将依赖项添加到我们的项目中

```
gem 'money-rails' 
```

Enter fullscreen mode Exit fullscreen mode

```
yarn add cleave.js

rails g money_rails:initializer 
```

Enter fullscreen mode Exit fullscreen mode

然后，一旦我们根据需求配置了`config/initializers/money.rb`，我们将开始设置 Javascript 部分。

在我们的`app/views/layouts/application.html.slim`中，我们将在`head`标签中定义一个关于 Cleave.js 配置的全局对象。它应该是这样的

```
/ Global config for input masking in Cleave library
javascript:
    window.__AMOUNT_INPUT_MASK__ = {
    prefix: "#{raw Money.default_currency.symbol} ",
    numeral: true,
    numeralThousandsGroupStyle: 'lakh',
    rawValueTrimPrefix: true
}; 
```

Enter fullscreen mode Exit fullscreen mode

*根据您的要求调整`numeralThousandsGroupStyle`配置*

在我们的`app/assets/javascripts/application.js`中，我们将编写一小段 JS 代码片段，它将根据我们定义的 Money 配置将输入屏蔽附加到任何具有类`currency-input-mask`
的输入

```
document.addEventListener("DOMContentLoaded", function(event) {
    // Input Mask
    document.querySelectorAll("input.currency-input-mask").forEach(function(el) {
        new Cleave(el, window.__AMOUNT_INPUT_MASK__);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，任何具有上述类的输入字段将允许我们的用户以更可控的方式输入值——通过更精确的数据减少人为错误的机会。

在我们继续之前，还有最后一步需要注意。因为我们的输入值现在保存了其他数据，比如货币和逗号，当用户提交表单时，我们将在控制器的参数中收到这些数据。

为了处理这一部分，我们需要在将值传递给模型之前对其进行清理。让我们在`app/helpers`中创建一个名为`input_helper.rb`的助手文件，并定义一个方法从值
中去掉想要的字符

```
module InputHelper 
    def strip_inputmask(amount)
      amount.is_a?(String) ? amount.gsub(/#{Money.default_currency.symbol}|,/, '').strip.to_i : amount
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

最后，在我们的控制器中，我们将包含这个助手并使用我们刚刚定义的方法

```
class ApplicationController 
 include InputHelper

 ...
 model.amount = strip_amount(params[:model][:amount])
 ...
end 
```

Enter fullscreen mode Exit fullscreen mode

我相信对我们的应用程序的这一点小小的关心对整个项目产生了巨大的影响，并给所有的利益相关者带来了和平。

> 👋如果这在任何方面有价值，请让我知道你的想法或反馈
> 
> 最初发布于[https://bilalbudhani . com/currency-input-masking-with-cleave-js-and-money-gem-in-ruby-on-rails/](https://bilalbudhani.com/currency-input-masking-with-cleave-js-and-money-gem-in-ruby-on-rails/)