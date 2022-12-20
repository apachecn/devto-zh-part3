# 如何完全定制 Rails 事务性电子邮件(设计)

> 原文：<https://dev.to/rmtwrk/how-to-fully-customize-rails-transactional-emails-devise-24ac>

在[rmt work](https://rmtwrk.com)我们正在建立世界上[最大的仅远程工作委员会](https://rmtwrk.com)，以帮助雇佣远程或分布式团队的公司与寻求更灵活工作环境的人匹配。因此，我们的 rails 应用程序有两种用户类型，公司用户、招聘人员和个人用户、希望被聘用的人员，我们希望为每种用户类型分别发送确认电子邮件。此外，我们希望使用 [Mailgun 的](http://mailgun.com/) API 来发送电子邮件，而不是通过 SMTP(稍后将详细介绍)，最后，我们希望使用令人惊叹的 [Premailer](https://github.com/premailer/premailer) gem 在发送电子邮件之前内嵌 CSS 样式。

device 是历史最悠久、编写得最好的 ruby/rails 库之一，也是最容易定制、变通和覆盖的库之一。虽然它做的太多了，但有时很难知道从哪里开始。

我们将讨论定制 Devise 电子邮件的 3 种方法(或者程度，如果你愿意的话)。

**方法一:**简单定制 device 的默认邮件
**方法二:**定制 device 发送邮件使用的“布局”和“模板”
**方法三:**覆盖 device 发送邮件的方式使用 API (Mailgun)而不是 SMTP

我将简要介绍前两种方法，并链接到其他资源。

### 方法一:简单定制 Devise 的默认邮件

如果你只想修改一些简单的东西，比如**电子邮件确认**电子邮件或者**密码重置**电子邮件的布局，那么这就是适合你的方法。首先在您的终端中运行以下程序，然后您可以在*视图/设计/邮件*中编辑您感兴趣的邮件的视图。

`rails generate devise:views`

要了解更多细节，最好的地方是 GitHub 上的 Devise Readme。

### 方法二:定制 Devise 发送邮件时使用的“布局”和“模板”

如果您想更进一步，定制每个电子邮件模板呈现的布局，那么您可以通过创建自己的 Mailer 类来实现。Devise 也有一个很好的自述文件，告诉你如何做到这一点:[如何:创建自定义布局](https://github.com/plataformatec/devise/wiki/How-To:-Create-custom-layouts)。

使用这种方法需要注意几个潜在的问题。如果您有两种资源类型，比如说`User`和`Individual`(我们实际模型的名称)，拥有两个单独的邮件程序的最简单方法是在每个 resource.rb 模型文件中使用**device _ mailer**方法(下面是简化的例子)。如果您计划移动邮件模板的位置，请务必删除*device/mailer*中的现有模板，因为它们会覆盖您的其他视图。

这是一个简单的定制设计邮件的例子:

```
class RmtwrkDeviseMailer < Devise::Mailer
  helper  :application # helpers defined within `application_helper`
  include Devise::Controllers::UrlHelpers # eg. `confirmation_url`
  default template_path: 'rmtwrk/devise/mailer
  default from: 'RMTWRK <rmtwrk@wrkhq.com>'
  default reply_to: 'RMTWRK <rmtwrk@wrkhq.com>'
  layout  'mailer_rmtwrk'
end 
```

您可以告诉您的`Individual`模型使用这样的 devise _ mailer 方法来使用这个邮件程序:

```
class Individual < ApplicationRecord
  devise :database_autenticatable,..., :confirmable
  def devise_mailer
    RmtwrkDeviseMailer
  end
end 
```

但是如果你想在发送之前对邮件做一些其他的事情呢？这就是方法 3 的用武之地。

### 方法 3:覆盖 Devise 发送电子邮件的方式，使用 API 而不是 SMTP

我们的限制:

1.  通过 API 而不是 SMTP 发送电子邮件。出于各种原因，大多数电子邮件提供商建议使用他们的 API 而不是 SMTP。以下是 Sendrid 和 Mailgun 的理由作为例子。
2.  两种资源模型；用户和个人，使用设备。
3.  单独的品牌，即每个型号的布局文件。
4.  使用 Premailer 内联我们的 CSS。

为了实现这一点，这个难题的关键部分是覆盖您感兴趣的`Devise::Mailer`方法。在本例中，我们将只关注电子邮件确认电子邮件。默认情况下，这由`confirmation_instructions`方法处理，默认情况下，该方法使用`ActionMailer`和 SMTP。

这是我们最终定制设计邮件程序的样子:

**rmt wrk _ device _ mailer . Rb**

```
class RmtwrkDeviseMailer < Devise::Mailer
  helper :application # gives access to all helpers defined within `application_helper`.
  include Devise::Controllers::UrlHelpers # Optional. eg. `confirmation_url`
  default from: 'RMTWRK <rmtwrk@inflowhq.com>'
  default reply_to: 'RMTWRK <rmtwrk@inflowhq.com>'

  layout 'mailer_rmtwrk'

  #############################
  # CONFIRMATION EMAIL
  # overrides Devise's confirmation_instructions method
  #############################
  def confirmation_instructions(record, token, opts={})
    @individual = record
    @token = token
    @to_email = @individual.email

    template = render_to_string(template: "mailers/rmtwrk/confirmation_instructions")
    premailer = Premailer.new(template, :with_html_string => true, :warn_level => Premailer::Warnings::SAFE)

    mg_client = Mailgun::Client.new
    mb_obj    = Mailgun::MessageBuilder.new

    mb_obj.from("RMTWRK <rmtwrk@wrkhq.com>")
    mb_obj.add_recipient(:to, @to_email, {'first' => "#{@individual.first_name}", 'last' => "#{@individual.last_name}"})
    mb_obj.subject("RMTWRK - email confirmation instructions")
    # mb_obj.body_text("Plaint text email goes here")
    mb_obj.body_html((premailer.to_inline_css).to_str)

    # mg_client.send_message(Rails.application.credentials.dig(Rails.env.to_sym, :mailgun, :domain), mb_obj)
    mg_client.send_message("mailgun.domain.url/goes/here", mb_obj)
  end
end 
```

**mailer _ rmtwrk . html . erb**

```
<!-- views/layouts/mailer_rmtwrk.html.erb -->

<!DOCTYPE html>
<html>
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <meta name="viewport" content="width=device-width"/>

    <style type="text/css">
      @font-face {
        font-family: 'Roboto';
        font-style: normal;
        font-weight: 400;
        src: local('Roboto'), local('Roboto-Regular'), url(https://fonts.gstatic.com/s/roboto/v18/KFOmCnqEu92Fr1Mu4mxK.woff2) format('woff2');
      }
      @font-face {
        font-family: 'Roboto';
        font-style: normal;
        font-weight: 500;
        src: local('Roboto Medium'), local('Roboto-Medium'), url(https://fonts.gstatic.com/s/roboto/v18/KFOlCnqEu92Fr1MmEU9fBBc4.woff2) format('woff2');
      }
      body,
      .body {
        font-family: 'Roboto', sans-serif;
        color: #1C1C1C;
        font-weight: 400;
        font-size: 14px;
      }
      .logo {
        margin-bottom: 8px;
      }
      .even-more-styles {
        // etc.
      }
    </style>
  </head>

  <body>
    <a href="https://rmtwrk.com"><img class="logo" src="https://s3.amazonaws.com/inflow-public/rmtwrkEmailLogo.png" width="64"/></a>
    <%= yield %>
  </body>
</html> 
```

**确认 _ 说明. html.erb**

```
<!-- views/mailers/rmtwrk/confirmation_instructions.html.erb -->

<div class="secondary-text light-gray space-below">Just need to confirm your email!</div>

<div class="secondary-text light-gray">You can activate your RMTWRK subscription through the link below:</div>

<div class="secondary-text space-below"><%= link_to 'Confirm my email address', @individual.devise_confirmation_url(@token) %></div> 
```

我希望这有所帮助！请留下你的意见或问题，我很乐意回复。

感谢阅读-安德鲁