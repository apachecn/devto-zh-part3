# 使用动作邮件程序和 Gmail 在 Rails 中发送电子邮件

> 原文：<https://dev.to/morinoko/sending-emails-in-rails-with-action-mailer-and-gmail-35g4>

在这篇文章中，我将在 Rails 中设置一个邮件程序，当客户提交订单时，它将向网站所有者发送一封电子邮件。(我使用的是 Rails 6，但这应该也适用于 Rails 5)。

步骤:

1.  使用`rails generate mailer`设置邮件程序
2.  创建电子邮件模板(视图)
3.  告诉适当的控制人员发送电子邮件
4.  设置电子邮件预览器
5.  为 Gmail 配置邮件设置。
6.  设置测试

让我们假设我们已经有了一个基本的订单模型和订单控制器设置，它只是在订单成功时通过 flash 消息向客户显示一个确认:

```
# app/controllers/orders_controller.rb

class OrdersController < ApplicationController
  def new
    @order = Order.new
  end

  def create
    @order = Order.new(order_params)

    if @order.save
      flash[:success] = t('flash.order.success')
      redirect_to root_path
    else
      flash.now[:error] = t('flash.order.error_html')
      render :new
    end
  end

  private

  def order_params
    params.require(:order).permit(:name, :email, :address, :phone, :message)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们还想在收到订单时将电子邮件发送给网站所有者，否则，他们怎么会知道呢？

首先，我们需要用 Rails 的`generate mailer`命令创建一个邮件程序。在这种情况下，邮件程序将在`OrdersController`中使用，因此我们将其命名为`OrderMailer` :

```
$ rails generate mailer OrderMailer 
```

Enter fullscreen mode Exit fullscreen mode

这将创建以下文件并输出:

```
create  app/mailers/order_mailer.rb
invoke  erb
create    app/views/order_mailer
invoke  test_unit
create    test/mailers/order_mailer_test.rb
create    test/mailers/previews/order_mailer_preview.rb 
```

Enter fullscreen mode Exit fullscreen mode

让我们把`app/mailers`文件夹拿出来。我们会找到`application_mailer.rb`和新创建的`order_mailer.rb`文件。

```
# app/mailers/application_mailer.rb

class ApplicationMailer < ActionMailer::Base
  default from: 'from@example.com' # Replace this email address with your own
  layout 'mailer'
end 
```

Enter fullscreen mode Exit fullscreen mode

```
# app/mailers/order_mailer.rb

class OrderMailer < ApplicationMailer
end 
```

Enter fullscreen mode Exit fullscreen mode

新款`OrderMailer`的工作原理与普通控制器非常相似。控制器准备类似 HTML 的内容，并通过视图显示给用户。邮件发送者以电子邮件的形式准备内容并发送它。我们可以通过向邮件程序添加方法来创建电子邮件，就像您向控制器添加操作一样。

让我们为订单邮件添加一个方法到`OrderMailer` :

```
# app/mailers/order_mailer.rb

class OrderMailer < ApplicationMailer
  def new_order_email
    @order = params[:order]

    mail(to: <ADMIN_EMAIL>, subject: "You got a new order!")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

*将`<ADMIN_EMAIL>`替换为您想要使用的电子邮件，最好作为环境变量隐藏起来。

在邮件视图中可以使用`new_order_email`中的任何实例变量。当我们告诉`OrderController`发送电子邮件时，就会提供`params[:order]`(我将在下面详细介绍)。

现在让我们创建一个电子邮件视图文件，确保将文件命名为与方法相同的名称。在`app/views/order_mailer/`文件夹中制作一个`new_order_email.html.erb`:

```
# app/views/order_mailer/new_order_email.html.erb

<!DOCTYPE html>
<html>
  <head>
    <meta content='text/html; charset=UTF-8' http-equiv='Content-Type' />
  </head>
  <body>
    <p>You got a new order from <%= @order.name %>!</p>
    <p>
    Order details<br>
    --------------------------
    </p>
    <p>Name: <%= @order.name %></p>
    <p>Email: <%= @order.email %></p>
    <p>Address: <%= @order.address %></p>
    <p>Phone: <%= @order.phone %></p>
    <p>
    Message:<br>
    ----------
    </p>
    <p><%= @order.message %></p>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

作为最佳实践，我们还可以创建一个文本版本的电子邮件，以防收件人不使用 HTML 电子邮件。这个文件放在同一个文件夹中，具有相同的文件名，但是使用了扩展名`text.erb`而不是`html.erb`。

```
# app/views/order_mailer/new_order_email.text.erb

You got a new order from <%= @order.name %>!
===============================================

Order Details:
--------------------------

Name: <%= @order.name %>
Email: <%= @order.email %>
Address: <%= @order.address %>
Phone: <%= @order.phone %>

Message:
<%= @order.message %> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经设置了电子邮件，接下来我们将告诉`OrdersController`在订单生成时发送电子邮件，也就是说，在`create`操作中保存订单之后。

```
class OrdersController < ApplicationController
  def create
    @order = Order.new(order_params)

    if @order.save
      OrderMailer.with(order: @order).new_order_email.deliver_later

      flash[:success] = "Thank you for your order! We'll get contact you soon!"
      redirect_to root_path
    else
      flash.now[:error] = "Your order form had some errors. Please check the form and resubmit."
      render :new
    end
  end

  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们在保存订单后添加了下面一行代码:

```
OrderMailer.with(order: @order).new_order_email.deliver_later 
```

Enter fullscreen mode Exit fullscreen mode

注意`with(order: @order)`代码。这就是让`OrderMailer`以参数形式访问订单信息的原因。还记得在`OrderMailer`中用`@order = params[:order]`设置实例变量吗？param 就是从那里来的！

那么，我们如何在发送这封邮件之前预览它呢？当我们生成邮件时，在`test/mailers/previews/`文件夹中创建了一个预览文件。它包含一个名为`order_mailer_preview.rb`的文件，其中有一个空的`OrderMailerPreview`类:

```
# test/mailers/previews/order_mailer_preview.rb

# Preview all emails at http://localhost:3000/rails/mailers/order_mailer
class OrderMailerPreview < ActionMailer::Preview

end 
```

Enter fullscreen mode Exit fullscreen mode

如果我们尝试访问`http://localhost:3000/rails/mailers/order_mailer` URL，它只会显示一个白色页面，上面写着“订单邮件”。

要为我们的新订单电子邮件设置预览，只需添加一个与您想要预览的邮件程序方法同名的方法(在本例中为`new_order_email`并设置邮件程序:

```
# Preview all emails at http://localhost:3000/rails/mailers/order_mailer
class OrderMailerPreview < ActionMailer::Preview
  def new_order_email
    # Set up a temporary order for the preview
    order = Order.new(name: "Joe Smith", email: "joe@gmail.com", address: "1-2-3 Chuo, Tokyo, 333-0000", phone: "090-7777-8888", message: "I want to place an order!")

    OrderMailer.with(order: order).new_order_email
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

重启 Rails 服务器并导航到`http://localhost:3000/rails/mailers/order_mailer/new_order_email`来查看邮件。你甚至可以看到 HTML 和文本版本。厉害！调整电子邮件的外观，直到你喜欢为止。(另外，`http://localhost:3000/rails/mailers/order_mailer`现在显示可用预览列表。)

最后，我们需要配置我们的 Rails 应用程序通过 Gmail 发送电子邮件。为此，我们将把以下设置添加到我们的`config/environments/production.rb` :

```
# config/environments/production.rb

config.action_mailer.delivery_method = :smtp
host = 'example.com' #replace with your own url
config.action_mailer.default_url_options = { host: host }

# SMTP settings for gmail
config.action_mailer.smtp_settings = {
  :address              => "smtp.gmail.com",
  :port                 => 587,
  :user_name            => <gmail_username>,
  :password             => <gmail_password>,
  :authentication       => "plain",
  :enable_starttls_auto => true
} 
```

Enter fullscreen mode Exit fullscreen mode

用您自己的用户名和密码替换`<gmail_username>`和`<gmail_password>`，最好将它们作为环境变量隐藏起来。关于密码的说明:我*强烈*建议启用两步验证，并注册一个“应用密码”在应用中使用，否则你很可能会遇到 Gmail 阻止电子邮件的问题。请参见下面的故障排除部分。

仅限本地使用或开发使用，请使用

```
host = 'localhost:3000'
config.action_mailer.default_url_options = { :host => 'localhost:3000', protocol: 'http' } 
```

Enter fullscreen mode Exit fullscreen mode

而不是以上对`host`和`config.action_mailer.default_url_options`的设置。

为了测试邮件*是否真的在开发中*被发送，给`config/environments/development.rb`添加相同的设置。您可以稍后将行`config.action_mailer.delivery_method = :smtp`改为`config.action_mailer.delivery_method = :test`，以防止在开发期间发送真实的电子邮件。

# 排除邮件发送错误

由于谷歌的安全特性，上面的配置起初对我不起作用，我得到了`Net::SMTPAuthenticationError`错误。我是这样去上班的。

### 账户两步验证

如果你的 Gmail 帐户使用两步验证，你将需要获得一个应用程序密码，并使用该密码代替常规密码。如果你不使用两步验证，我建议你打开它，以免邮件被谷歌屏蔽。

要创建应用程序密码，请转到您的 Google 帐户设置并导航到“安全”选项卡。在“登录谷歌”下，点击“应用程序密码”菜单项(只有在你打开两步验证时才可用)。接下来，选择“选择应用程序”下拉菜单下的`Other (Custom name)`，输入应用程序的名称或其他有用的名称。点击“生成”，您的新应用程序密码将出现在屏幕上。请确保在关闭窗口之前拷贝它，否则您将无法再次看到密码。

现在，在你的 Rails 应用程序邮件设置中，用新的应用程序密码替换`<gmail_password>`。

### 账户无两步验证

如果你不使用两步验证，你将不得不允许“不太安全的应用程序”访问你的帐户。在“安全”标签下的谷歌设置中，找到“不太安全的应用程序访问”部分，然后点击“打开访问”。

在推向生产后，我仍然有发送邮件的问题，因为谷歌阻止了未知位置的访问，在这种情况下，应用程序在生产中。我能够通过“显示解锁验证码”的过程来解决这个问题。如果完成上述操作后您仍有问题，这将授予您几分钟的帐户访问权限，允许您注册新的应用程序。

通过前往[http://www.google.com/accounts/DisplayUnlockCaptcha](http://www.google.com/accounts/DisplayUnlockCaptcha)激活该选项。之后，让应用程序再次发送电子邮件。这应该注册应用程序，这样你就可以从此发送电子邮件了！

更新:几个月后，出于某种原因，通过该应用程序发送的电子邮件再次被阻止，安全警告将被发送到该电子邮件帐户。为了避免麻烦，我会选择两步验证+应用程序密码的方法。

### Rails 凭证问题与 Heroku

我在 Heroku 产品中遇到的最后一个问题是忘记在 Heroku 应用程序设置中注册我的 Rails 应用程序的主密钥，并配置 Rails 来要求主密钥。如果您使用 Rails 的凭证文件(`credentials.yml.enc`)来跟踪您的密钥(在本例中是您的电子邮件和密码)，这将是一个问题。

在开发中，Rails 可以访问秘密凭证，因为主密钥可以在系统上直接获得，但是在使用 Heroku 的产品中，如果不首先向 Heroku 注册主密钥，应用程序就无法访问秘密密钥。

要解决这个问题，在`config/environments/production.rb`文件中，取消注释或添加以下行:

```
config.require_master_key = true 
```

Enter fullscreen mode Exit fullscreen mode

然后在 Heroku app 设置里注册一个名为`RAILS_MASTER_KEY`的键。输入在`config/master.key`文件中找到的值。这使得 Heroku 能够访问`credentials.yml.enc`文件中注册的密钥。

# 测试

最后，让我们确保设置了一些测试！

如果`test_fixtures`下还没有`orders.yml`文件，创建一个并添加:

```
# test/fixtures/orders.yml

one:
  name: "Joe  Smith"
  email: "joe@gmail.com"
  address: "1-2-3  Chuo,  Tokyo,  333-0000"
  phone: "090-7777-8888"
  message: "I  want  to  place  an  order!" 
```

Enter fullscreen mode Exit fullscreen mode

这是一种简单的方式来获取一个样本订单，以便在邮件测试器中使用。

接下来，在`test/mailers/order_mailer_test.rb`(如果还没有这个文件，就创建这个文件)中，我们可以添加一个简单的测试，断言电子邮件正在发送，并且内容是正确的。

```
require 'test_helper'

class OrderMailerTest < ActionMailer::TestCase
  test "new order email" do
    # Set up an order based on the fixture
    order = orders(:one)

    # Set up an email using the order contents
    email = OrderMailer.with(order: order).new_order_email

    # Check if the email is sent
    assert_emails 1 do
      email.deliver_now
    end

    # Check the contents are correct
    assert_equal [<ADMIN_EMAIL>], email.from
    assert_equal [<ADMIN_EMAIL>], email.to
    assert_equal "You got a new order!", email.subject
    assert_match order.name, email.html_part.body.encoded
    assert_match order.name, email.text_part.body.encoded
    assert_match order.email, email.html_part.body.encoded
    assert_match order.email, email.text_part.body.encoded
    assert_match order.address, email.html_part.body.encoded
    assert_match order.address, email.text_part.body.encoded
    assert_match order.phone, email.html_part.body.encoded
    assert_match order.phone, email.text_part.body.encoded
    assert_match order.message, email.html_part.body.encoded
    assert_match order.message, email.text_part.body.encoded
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

`<ADMIN_EMAIL>`应该替换为您正在使用的电子邮件。

`email.html_part.body.encoded`检查 HTML 邮件的内容，而`email.text_part.body.encoded`检查文本邮件。

# 结论

我们现在应该有一个功能邮件通知我们(或网站所有者)新来的订单！

一旦 Rails 邮件程序和 Gmail 设置被正确配置，我们就可以通过以几乎相同的方式生成和设置新的邮件程序，轻松地从其他控制器操作发送其他电子邮件。:)