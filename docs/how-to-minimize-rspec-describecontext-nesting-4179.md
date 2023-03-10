# 如何最小化 RSpec ' describe `/ ' context '嵌套

> 原文：<https://dev.to/hanachin/how-to-minimize-rspec-describecontext-nesting-4179>

## 深度嵌套规格

你曾经在 RSpec 中编写过深度嵌套的代码吗？老实说，我写了很多。

在应用程序代码中，我避免编写深度嵌套的代码。因为凹凸不平的压痕，很难读，也很难改。当我的同事做代码审查时，他们指出了深度嵌套的代码。但是在 RSpec 中，有时(总是)我编写了深度嵌套的代码，并且通过了代码审查。

比如，像下面:

```
RSpec.describe "Screencast player", type: :system do
  context "When user logged in" do
    let(:user) { create(:user) }

    before do
      login_as(user)
    end

    context "When user register the credit card" do
      let(:card) { create(:card) }

      before do
        user.register_credit_card(card)
      end

      context "When there are screencast" do
        before do
          create(:screencast)
        end

        it "can play the screencast"
      end

      context "When theare not any screencast" do
        it "shows the message, 'no such screencast'"
      end
    end

    context "When user does not register the credit card" do
      it "can not play the screencast"
    end
  end

  context "When user is not logged in" do
    it "can not play the screencast"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么会这样？

RSpec 很牛逼，如果你愿意可以永远嵌套`describe` / `context`。
任何可能出错的事情都会出错。 <sup id="fnref1">[1](#fn1)</sup>
顺便说一下，我已经用小黄瓜写好了验收测试，会怎么样呢？
它从不嵌套，因为它没有嵌套的语法。
[https://docs.cucumber.io/gherkin/reference/](https://docs.cucumber.io/gherkin/reference/)

## 怎么修？

愿小黄瓜陪伴你。

如果你像小黄瓜一样编写规范，那么规范的行为也像小黄瓜一样。它从未筑巢，耶！

## 怎么做？

我用上面的例子来说明怎么做。

首先将安排前提条件移入`shared_context`。

```
RSpec.describe "Screencast player", type: :system do
  shared_context "When user logged in" do
    let(:user) { create(:user) }

    before do
      login_as(user)
    end
  end

  shared_context "When user register the credit card" do
    let(:card) { create(:card) }

    before do
      user.register_credit_card(card)
    end
  end

  shared_context "When there are screencast" do
    before do
      create(:screencast)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

然后将它们组合起来

```
RSpec.describe "Screencast player", type: :system do
  shared_context "When user logged in" do
    let(:user) { create(:user) }

    before do
      login_as(user)
    end
  end

  shared_context "When user register the credit card" do
    let(:card) { create(:card) }

    before do
      user.register_credit_card(card)
    end
  end

  shared_context "When there are screencast" do
    before do
      create(:screencast)
    end
  end

  describe "user play the screencast" do
    include_context "When user logged in"
    include_context "When user register the credit card"
    include_context "When there are screencast"

    it "can play the screencast"
  end

  describe "user knows there are no screencast yet" do
    include_context "When user logged in"
    include_context "When user register the credit card"

    it "shows the message, 'no such screencast'"
  end

  describe "user can not play the screencast before register the credit card" do
    include_context "When user logged in"

    it "can not play the screencast"
  end

  describe "user can not play the screencast before logged in" do
    it "can not play the screencast"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

## 结论

感谢`shared_context`修复了我的深度嵌套规范。

小黄瓜棒极了< 3

* * *

1.  [https://en.wikipedia.org/wiki/Murphy%27s_law](https://en.wikipedia.org/wiki/Murphy%27s_law)↩