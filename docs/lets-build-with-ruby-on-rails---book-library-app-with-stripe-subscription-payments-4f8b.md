# 让我们来构建:使用 Ruby on Rails -带 Stripe 订阅支付的图书图书馆应用程序

> 原文：<https://dev.to/justalever/lets-build-with-ruby-on-rails---book-library-app-with-stripe-subscription-payments-4f8b>

欢迎来到我的关于 Ruby on Rails 的构建系列。这一期重点关注使用 Ruby on Rails 作为 web 应用程序框架接受支付。我们将 Ruby on Rails 与 Stripe 合作，为一个虚拟的图书图书馆应用程序创建了一个基于订阅的 SaaS 模型。

### 这个版本有什么新特性？

*   Rails 5.2 出现了
*   我们使用 Rails 5.2 中新增的[动态存储](http://edgeguides.rubyonrails.org/active_storage_overview.html)。我们不再需要回形针或 Carrierwave 等第三方宝石。
*   新的加密秘密，使管理任何环境变量或密钥的方式更容易。由于加密的质量，您可以放心地将它们提交给版本控制
*   [Stripe 计费](https://stripe.com/billing) - Stripe 最近发布了一种围绕其订阅产品的新模式。在条带计费中，您可以定义具有关联计划的覆盖产品。这是一种更好的捆绑销售的方式，但仍然能够以不同的方式销售，即统一价格、多种计划、按座位、基于使用和统一价格+超额。

我使用了一个新的 Ruby on Rails 应用程序模板(基于本系列中的[布尔玛 CSS](https://bulma.io/) ，你可以在这里下载

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ justalever ](https://github.com/justalever) / [图书 _ 图书馆](https://github.com/justalever/book_library)

### 一个基于 SaaS 的演示应用程序，围绕图书下载和条纹订阅

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Let's Build](img/edcee5e791ca451d3df661bc3ead7e38.png)](https://camo.githubusercontent.com/d74ab506a95ed289aafa177981e37a7fe61f24df2e35493dd58d4d3222e58656/68747470733a2f2f692e696d6775722e636f6d2f6b47594e724e472e6a7067)

# 让我们来构建:用 Ruby on Rails——一个带 Stripe 订阅支付的图书图书馆应用

欢迎来到我的第十一期让我们构建以 Ruby on Rails 为特色的系列。这一期再次关注使用 Ruby on Rails 作为 web 应用程序框架接受支付。我们将 Ruby on Rails 与 Stripe 合作，为图书图书馆应用程序创建一个基于订阅的 SaaS 模型。

### 这个建筑有什么新特点？

*   Rails 5.2 出现了
*   我们使用 Rails 5.2 中新增的[动态存储](http://edgeguides.rubyonrails.org/active_storage_overview.html)。我们不再需要回形针或 Carrierwave 等第三方宝石。
*   新的加密秘密，使管理任何环境变量或密钥的方式更容易。由于加密的质量，您可以放心地将它们提交给版本控制
*   [Stripe 计费](https://stripe.com/billing) - Stripe 最近发布了一种围绕其订阅产品的新模式。在条带计费中，您可以定义具有关联计划的覆盖产品。这是一种更好的捆绑方式…

</article>

[View on GitHub](https://github.com/justalever/book_library)

### 图书借阅申请

该应用程序是一个简单的图书应用程序，允许用户在图书馆中添加和删除图书。一旦用户订阅，这些书就可以下载阅读。只有在用户订阅的情况下，才能访问库。这款应用不同于以前的应用，以前的应用要求用户必须先付费才能创建账户。

向用户呈现用于向他们的图书馆添加书的按钮。正如您所猜测的，要执行这个操作，用户首先需要一个帐户。点击“添加到库”按钮后，面向公众的用户(没有帐户的人)被重定向到一个注册页面，我们使用[device](https://github.com/plataformatec/devise)gem 实现该页面。

Devise 的一个很好的回调函数允许我们在成功注册后重定向用户。在这种情况下，我们将用户重定向到一个定价页面，该页面有三种方案可供选择。每个计划都有自己的相关参数。点击任意一层上的“subscribe”按钮，用户将被重定向到一个支付页面，该页面带有必要的参数以连接到 Stripe。

看似简单的应用程序承载了一些逻辑和重量来覆盖我们所有的轨迹。这个应用程序还远未完成，但是你可以，无论如何，扩展它。

我没有时间了，但是扩展这个应用程序的一个明显的地方是保存用户已经订阅的计划。这可以在他们创建订阅时完成。在这里，您可以查询应用程序中的不同位置，以根据其平面图类型显示和隐藏特定要素。

#### 第一部分

[https://www.youtube.com/embed/eS52517o6Ck](https://www.youtube.com/embed/eS52517o6Ck)

#### 第二部分

[https://www.youtube.com/embed/JG1whaC_8i8](https://www.youtube.com/embed/JG1whaC_8i8)

#### 第三部分

[https://www.youtube.com/embed/mOS-tzf7xXA](https://www.youtube.com/embed/mOS-tzf7xXA)

#### 第四部分

[https://www.youtube.com/embed/56vbxhw3S2o](https://www.youtube.com/embed/56vbxhw3S2o)

#### 第五部分

[https://www.youtube.com/embed/GUbLcffLY7I](https://www.youtube.com/embed/GUbLcffLY7I)

#### 第六部分

[https://www.youtube.com/embed/cSKEFco7HdA](https://www.youtube.com/embed/cSKEFco7HdA)

#### 模型和关系

我们的应用程序只有三种型号。我们总是通过图书馆模型来访问书籍和用户。这最终成为一个`many-to-many`关联。

**用户**

```
 email
name
stripe_id
stripe_subscription_id
card_last4
card_exp_month
card_exp_year
card_type
admin
subscribed 
```

**预定**

```
 title
description
author
user_id 
```

**图书馆**

```
 book_id
user_id 
```

这些关系最终如下所示:

```
 #app/models/book.rb

class Book < ApplicationRecord
  has_one_attached :thumbnail
  belongs_to :user
  has_many :libraries
  has_many :added_books, through: :libraries, source: :user
 end 
```

```
 #app/models/library

class Library < ApplicationRecord
  belongs_to :book
  belongs_to :user
end 
```

```
 #app/models/user
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable
  has_many :charges
  has_many :books, dependent: :destroy
  has_many :libraries
  has_many :library_additions, through: :libraries, source: :book

  def subscribed?
    stripe_subscription_id?
  end
end 
```

### 控制器

当提交一个成功的付款以及订阅一个新用户时，我们的控制器是许多奇迹发生的地方。

最容易开始的控制器是我们的图书控制器。这模仿了我们以前的许多系列，其中用户需要与另一个模型相关联。确保你的`Book`模型有一个`user_id`字段。

#### 账本控制器

```
 class BooksController < ApplicationController
  before_action :set_book, only: [:show, :edit, :update, :destroy, :library]
  before_action :authenticate_user!, except: [:index, :show]

  # GET /books
  # GET /books.json
  def index
    @books = Book.all
  end

  # GET /books/1
  # GET /books/1.json
  def show
  end

  # GET /books/new
  def new
    @book = current_user.books.build
  end

  # GET /books/1/edit
  def edit
  end

  # POST /books
  # POST /books.json
  def create
    @book = current_user.books.build(book_params)

    respond_to do |format|
      if @book.save
        format.html { redirect_to @book, notice: 'Book was successfully created.' }
        format.json { render :show, status: :created, location: @book }
      else
        format.html { render :new }
        format.json { render json: @book.errors, status: :unprocessable_entity }
      end
    end
  end

  # PATCH/PUT /books/1
  # PATCH/PUT /books/1.json
  def update
    respond_to do |format|
      if @book.update(book_params)
        format.html { redirect_to @book, notice: 'Book was successfully updated.' }
        format.json { render :show, status: :ok, location: @book }
      else
        format.html { render :edit }
        format.json { render json: @book.errors, status: :unprocessable_entity }
      end
    end
  end

  # DELETE /books/1
  # DELETE /books/1.json
  def destroy
    @book.destroy
    respond_to do |format|
      format.html { redirect_to books_url, notice: 'Book was successfully destroyed.' }
      format.json { head :no_content }
    end
  end

  # Add and remove books to library
  # for current_user
  def library
    type = params[:type]

    if type == "add"
      current_user.library_additions << @book
      redirect_to library_index_path, notice: "#{@book.title} was added to your library"

    elsif type == "remove"
      current_user.library_additions.delete(@book)
      redirect_to root_path, notice: "#{@book.title} was removed from your library"
    else
      # Type missing, nothing happens
      redirect_to book_path(@book), notice: "Looks like nothing happened. Try once more!"
    end
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_book
      @book = Book.find(params[:id])
    end

    # Never trust parameters from the scary internet, only allow the whitelist through.
    def book_params
      params.require(:book).permit(:title, :description, :author, :thumbnail, :user_id)
    end
end 
```

在这个控制器中，您将看到一个新的动作。此操作允许我们实际向给定用户的图书馆添加一本书。我们从视图中获取传递的参数，并确定我们是添加还是删除图书。在此过程中，我们会显示通知消息以改善 UX。

```
 # Add and remove books to the library
  # for current_user
  def library
    type = params[:type]

    if type == "add"
      current_user.library_additions << @book
      redirect_to library_index_path, notice: "#{@book.title} was added to your library"

    elsif type == "remove"
      current_user.library_additions.delete(@book)
      redirect_to root_path, notice: "#{@book.title} was removed from your library"
    else
      # Type missing, nothing happens
      redirect_to book_path(@book), notice: "Looks like nothing happened. Try once more!"
    end
  end 
```

该操作确实需要对我们的 routes 文件进行一些更改。最终的路由文件如下所示:

```
 #config/routes.rb

require 'sidekiq/web'

Rails.application.routes.draw do

  resources :library, only:[:index]
  mount Sidekiq::Web => '/sidekiq'

  resources :books do
    member do
      put "add", to: "books#library"
      put "remove", to: "books#library"
    end
  end

  devise_for :users, controllers: { registrations: "registrations" }
  root to: 'books#index'
  resources :pricing, only:[:index]
  resources :subscriptions
end 
```

请注意，我们在`resources :books`中声明了一个块，并添加了一个`member` do 块。

在我们的`_book.html.erb`部分中，使这一切工作的相应标记如下所示。

```
 <div class="content">
   <% if subscribed? %>

    <% if user_added_to_library?(current_user, book) %>

      <%= link_to 'Remove from library', add_book_path(book, type: "remove"), method: :put, class: "button is-danger is-fullwidth" %>

      <% if controller.controller_name == "library" %>
        <%= link_to 'Download', '#', class:"button is-success is-fullwidth mt1" %>
      <% end %>

    <% else %>
      <%= link_to 'Add to library', add_book_path(book, type: "add"), method: :put, class: "button is-link is-fullwidth" %>
    <% end %>

  <% else %>
    <%= link_to 'Add to library', new_user_registration_path, class: "button is-link is-fullwidth" %>
  <% end %>
</div> 
```

我们在这里做了很多事情，但是主要的焦点是`link_to`方法。这些通过`add_book_path()`和`book`，以及`type`的“添加”或“删除”。您可以使用这样的链接传递任何参数，但是因为我们希望我们的路由匹配命名约定，所以它们才这样做。

#### 订阅控制器

```
 class SubscriptionsController < ApplicationController
  layout "subscribe"
  before_action :authenticate_user!, except: [:new, :create]

  def new
    if user_signed_in? && current_user.subscribed?
      redirect_to root_path, notice: "You are already a subscriber!"
    end
  end

  def create
    Stripe.api_key = Rails.application.credentials.stripe_api_key

    plan_id = params[:plan_id]
    plan = Stripe::Plan.retrieve(plan_id)
    token = params[:stripeToken]

    product = Stripe::Product.retrieve(Rails.application.credentials.book_library)

    customer = if current_user.stripe_id?
                 Stripe::Customer.retrieve(current_user.stripe_id)
               else
                 Stripe::Customer.create(email: current_user.email, source: token)
               end

    subscription = customer.subscriptions.create(plan: plan.id)

    options = {
      stripe_id: customer.id,
      stripe_subscription_id: subscription.id,
      subscribed: true,
    }

    options.merge!(
      card_last4: params[:user][:card_last4],
      card_exp_month: params[:user][:card_exp_month],
      card_exp_year: params[:user][:card_exp_year],
      card_type: params[:user][:card_type]
    ) if params[:user][:card_last4]

    current_user.update(options)

    redirect_to root_path, notice: "🎉 Your subscription was set up successfully!"
  end

  def destroy
    customer = Stripe::Customer.retrieve(current_user.stripe_id)
    customer.subscriptions.retrieve(current_user.stripe_subscription_id).delete
    current_user.update(stripe_subscription_id: nil)

    redirect_to root_path, notice: "Your subscription has been cancelled."
  end

end 
```

我们的订阅控制器获得了与 Stripe gem 关联所需的所有逻辑。结合 Stripe 元素库中的一些标记和 Javascript，我们从视图中获取参数。

```
 plan_id = params[:plan_id] # passed in through the view
 plan = Stripe::Plan.retrieve(plan_id) # retrives the plans on stripe.com of which we already made
 token = params[:stripeToken] # necessary to submit payment to stripe 
```

然后，我们可以检索一个现有的客户，或者用下面几行创建一个新客户:

```
 customer = if current_user.stripe_id?
                 Stripe::Customer.retrieve(current_user.stripe_id)
               else
                 Stripe::Customer.create(email: current_user.email, source: token)
               end 
```

最后，为了创建新的订阅，我们将这一行称为:

```
 subscription = customer.subscriptions.create(plan: plan.id) 
```

在此期间，您可以更新一些与您的用户相关的内容:

```
 options = {
      stripe_id: customer.id, # saves customer id for later
      stripe_subscription_id: subscription.id, # allows us to modify subscription where applicable
      subscribed: true, # dictates if a user is subscribed or not
    }

    options.merge!(
      card_last4: params[:user][:card_last4],
      card_exp_month: params[:user][:card_exp_month],
      card_exp_year: params[:user][:card_exp_year],
      card_type: params[:user][:card_type]
    ) if params[:user][:card_last4]

    current_user.update(options) # updates/adds all options to user account from above

    redirect_to root_path, notice: "🎉 Your subscription was set up successfully!" 
```

大量数据的视图显示如下:

```
 <!-- app/views/subscriptions/new -->
<section class="section">
  <div class="columns is-centered">
    <div class="column is-6 border pa5">
    <h1 class="title is-3">Subscribe</h1>
    <hr />
      <p>Chosen plan: <strong><%= params[:plan] %></strong></p>
    <hr />
  <%= form_tag subscriptions_path, id: "payment-form" do |form| %>

    <div class="field">

      <label class="label" for="card-element">
        Enter credit or debit card
      </label>

      <div id="card-element">
        <!-- A Stripe Element will be inserted here. -->
      </div>

      <!-- Used to display Element errors. -->
      <div id="card-errors" role="alert"></div>

      <%= hidden_field_tag :plan_id, params[:plan_id] %>

    <button class="button is-fullwidth is-link mt4">Submit</button>
  </div>

<% end %>

</div>
</div>
</section> 
```

最终，我们将使用 Stripe 元素来呈现支付表单。表单输入是使用 JavaScript 和错误处理生成的。我发现这是最简单的方法，而不是滚动你自己的表单。如果你需要像贝宝一样处理多个商家，你可以自己动手。

在用户登陆这个页面之前，他们已经从他们选择计划的定价页面来了。每个计划都有一个传递给该视图的相关参数，该参数解释了`params[:plan]`以及隐藏的字段标签:

```
 <%= hidden_field_tag :plan_id, params[:plan_id] %> 
```

我们需要一种方法来知道哪个计划向用户收费。这是一种快速的方法。这个 id 来自定价页面，我将在下面讨论，但在此之前，我们先来看看实现这一切所需的 JavaScript:

```
 document.addEventListener("turbolinks:load", function() {
  const publishableKey = document.querySelector("meta[name='stripe-key']").content;
  const stripe = Stripe(publishableKey);

  const elements = stripe.elements({
    fonts: [{
      cssSrc: "https://rsms.me/inter/inter-ui.css"
    }],
    locale: 'auto'
  });

  // Custom styling can be passed to options when creating an Element.
  const style = {
    base: {
      color: "#32325D",
      fontWeight: 500,
      fontFamily: "Inter UI, Open Sans, Segoe UI, sans-serif",
      fontSize: "16px",
      fontSmoothing: "antialiased",

      "::placeholder": {
        color: "#CFD7DF"
      }
    },
    invalid: {
      color: "#E25950"
    }
  };

  // Create an instance of the card Element.
  const card = elements.create('card', { style });

  // Add an instance of the card Element into the `card-element` <div>.

    card.mount('#card-element');

    card.addEventListener('change', ({ error }) => {

      const displayError = document.getElementById('card-errors');
      if (error) {
        displayError.textContent = error.message;
      } else {
        displayError.textContent = '';
      }
    });

    // Create a token or display an error when the form is submitted.
    const form = document.getElementById('payment-form');

    form.addEventListener('submit', async(event) => {
      event.preventDefault();

      const { token, error } = await stripe.createToken(card);

      if (error) {
        // Inform the customer that there was an error.
        const errorElement = document.getElementById('card-errors');
        errorElement.textContent = error.message;
      } else {
        // Send the token to your server.
        stripeTokenHandler(token);
      }
    });

    const stripeTokenHandler = (token) => {
      // Insert the token ID into the form so it gets submitted to the server
      const form = document.getElementById('payment-form');
      const hiddenInput = document.createElement('input');
      hiddenInput.setAttribute('type', 'hidden');
      hiddenInput.setAttribute('name', 'stripeToken');
      hiddenInput.setAttribute('value', token.id);
      form.appendChild(hiddenInput);

      ["type", "last4", "exp_month", "exp_year"].forEach(function(field) {
        addCardField(form, token, field);
      });

      // Submit the form
      form.submit();
    }

    function addCardField(form, token, field) {
      let hiddenInput = document.createElement('input');
      hiddenInput.setAttribute('type', 'hidden');
      hiddenInput.setAttribute('name', "user[card_" + field + "]");
      hiddenInput.setAttribute('value', token.card[field]);
      form.appendChild(hiddenInput);
    }

}); 
```

如果你一步一步地做，你可能在你的`app/assets/javascripts`目录中有一个`subscriptions.coffee`文件。如果是这样，将其重命名为`subscriptions.js`，并输入上面的代码。

我们为支付流程创建了一个新的自定义布局。这在我们的`subscriptions_controller.rb`中用简单的一行表示在最顶端:

```
 class SubscriptionsController < ApplicationController
  layout "subscribe"
  ...
 end 
```

这允许我们在`app/views/layouts/`中创建新的文件`subscribe.html.erb`。

您需要在您的`subscribe.html.erb`布局文件中添加一个 meta 标签来实现这一点。注意我是如何添加 stripe js 库的`https://js.stripe.com/v3/`。该代码如下所示:

```
 <head>
<%= javascript_include_tag 'application', 'https://js.stripe.com/v3/', 'data-turbolinks-track': 'reload' %>

 <%= tag :meta, name: "stripe-key", content: Rails.application.credentials.stripe_publishable_key %>
</head> 
```

作为 Rails 5.2 的一部分，我们正在以不同于之前系列的方式访问我们的新凭证。请务必观看如何编辑/更新/存储这些内容的视频。

通过输出您的`test` stipe 可发布密钥，您现在已经准备好为 Stripe 实现 javascript 来实现它的魔力。

### 定价页面

获取我们需要的所有计划和参数需要一些用户输入。定价页面对此负责。

```
 <section class="section">
  <div class="has-text-centered box">
    <h1 class="title">Pricing</h1>
    <h2 class="subtitle">Choose the best plan that matches your reading consistency and budget.</h2>
    <div class="pricing-table">
      <div class="pricing-plan is-warning">
        <div class="plan-header">Starter</div>
        <div class="plan-price"><span class="plan-price-amount"><span class="plan-price-currency">$</span>5</span>/month</div>
        <div class="plan-items">
          <div class="plan-item">5 Downloads</div>
        </div>
        <div class="plan-footer">
          <% if user_signed_in? %>
          <%= link_to 'Subscribe', new_subscription_path(plan: "starter", plan_id: Rails.application.credentials.starter), class:"button is-fullwidth" %>
            <% else %>
          <%= link_to 'Subscribe', new_user_registration_path, class:"button is-fullwidth" %>
          <% end %>
        </div>
      </div>
      <div class="pricing-plan is-link is-active">
        <div class="plan-header">Book Worm</div>
        <div class="plan-price"><span class="plan-price-amount"><span class="plan-price-currency">$</span>10</span>/month</div>
        <div class="plan-items">
          <div class="plan-item">10 Downloads</div>
        </div>
        <div class="plan-footer">
          <% if user_signed_in? %>
          <%= link_to 'Subscribe', new_subscription_path(plan: "book_worm", plan_id: Rails.application.credentials.book_work), class:"button is-fullwidth" %>
          <% else %>
          <%= link_to 'Subscribe', new_user_registration_path, class:"button is-fullwidth" %>
          <% end %>
        </div>
      </div>
      <div class="pricing-plan is-danger">
        <div class="plan-header">Scholar</div>
        <div class="plan-price"><span class="plan-price-amount"><span class="plan-price-currency">$</span>30</span>/month</div>
        <div class="plan-items">
          <div class="plan-item">30 Downloads</div>
        </div>
        <div class="plan-footer">
          <% if user_signed_in? %>
          <%= link_to 'Subscribe', new_subscription_path(plan: "scholar", plan_id: Rails.application.credentials.scholar), class:"button is-fullwidth" %>
          <% else %>
          <%= link_to 'Subscribe', new_user_registration_path, class:"button is-fullwidth" %>
          <% end %>
        </div>
      </div>
    </div>
  </div>
</section> 
```

开箱即用，布尔玛不支持这种标记。你可以用这个库来扩展 bulma，添加额外的 CSS 来完成这个任务。

除此之外，您可以看到每个订阅按钮都有自己的一组参数。我们将`plan_id`参数存储为秘密凭证。你可以从你在 stripe.com 仪表盘上创建的计划中获取这些信息。我们还传递计划名称本身，只是为了帮助下一个屏幕上的 UX，让用户知道他们正在购买什么。正如我之前所说的，这是将计划类型保存到您的用户模型的好地方，这样您就可以指定您想要每个计划类型的可用特性。

### 四舍五入

总的来说，这个应用程序似乎相当简单。我们正在使用 Rails 5.2 的一些令人兴奋的新功能，我将在视频中更深入地讨论这些功能。我邀请您继续关注，并一如既往地随时提问。下面的评论或 YouTube 上的评论是获得反馈的好方法。Github 上的源代码也是如此。

如果你能走到这一步，我真不知道该怎么感谢你。这次经历对我来说是一次巨大的学习。我选择在公共场合学习，不仅是为了分享我学到的东西，也是为了获得反馈，了解我是否做错了什么或没有达到要求。欢迎批评，如果你是新来的，一定要看看我以前的所有系列。

**相关系列**

1.  [让我们构建:使用 Ruby on Rails——简介](https://web-crunch.com/lets-build-with-ruby-on-rails-introduction/)
2.  [让我们构建:使用 Ruby on Rails——安装](https://web-crunch.com/lets-build-with-ruby-on-rails-installation/)
3.  [让我们构建:用 Ruby on Rails——带评论的博客](https://web-crunch.com/lets-build-with-ruby-on-rails-blog-with-comments)
4.  [让我们构建:用 Ruby on Rails——Twitter 的克隆版](https://web-crunch.com/lets-build-with-ruby-on-rails-a-twitter-clone/)
5.  [让我们用 Ruby on Rails 构建一个可移植的克隆版本](https://web-crunch.com/lets-build-dribbble-clone-with-ruby-on-rails/)

### 无耻的塞时间

[![](img/e4dc18bd4506d5a2ef46a7e214a4132b.png)](http://hellorails.io)

我有一门新课程叫做 [Hello Rails](https://hellorails.io) 。Hello Rails 是一门现代课程，旨在帮助您快速开始使用和理解 Ruby on Rails。如果你是 Ruby 或 Ruby on Rails 的新手，我邀请你去看看这个网站。该课程将与这些构建非常相似，但是是一个超级深入的版本，具有更现实的目标和可交付成果。[报名今天得到通知](https://hellorails.io/)！

在推特上关注 [@hello_rails](https://twitter.com/hello_rails) 和[本人@justalever](https://twitter.com/justalever) 。