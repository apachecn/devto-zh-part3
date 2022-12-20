# 让我们建立:用 Ruby on Rails -电子商务音乐商店

> 原文：<https://dev.to/justalever/lets-build-with-ruby-on-rails---ecommerce-music-shop-1c21>

欢迎来到我的构建 Ruby on Rails 系列的下一期。在这六个视频中，我将带你了解在 Rails 环境中构建电子商务音乐商店的概念。这篇文章最初发表于[web-crunch.com](https://web-crunch.com)

如果你已经跟踪了我以前的构建，我从那些构建中提取了一些想法，并且建立了一些新的想法和技术。我之前已经讨论过很多内容，但是还有一些新概念，比如我在本系列中深入探讨的数据库播种和会话参数。

#### 源代码

我觉得真正掌握 Ruby on Rails 框架的最好方法是开始写代码，即使你还不太理解它。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)/[侧翼](https://github.com/justalever/flanger)

### 一个以电子商务为特色的新系列，让我们使用 Ruby on Rails 来构建

<article class="markdown-body entry-content container-lg" itemprop="text">

# 让我们建立:用 Ruby on Rails——电子商务音乐商店

[![feature image](img/d46ede609663af5b78937fa270749b04.png)](https://camo.githubusercontent.com/ee8efddffb26d43dbc82d02a33ab9bf8d6d4db0384a8d0a071a2defd2b8c9134/68747470733a2f2f692e696d6775722e636f6d2f7764526d4b62742e6a7067)

欢迎来到我的构建 Ruby on Rails 系列的下一期。在这六个视频中，我将带你了解在 Rails 环境中构建电子商务音乐商店的概念。

如果你已经跟踪了我以前的构建，我从那些构建中提取了一些想法，并且建立了一些新的想法和技术。我之前已经讨论过很多内容，但是还有一些新概念，比如我在本系列中深入探讨的数据库播种和会话参数。

查看之前的系列:

*   [让我们构建:使用 Ruby on Rails——简介](https://web-crunch.com/lets-build-with-ruby-on-rails-introduction/)
*   [让我们构建:使用 Ruby on Rails——安装](https://web-crunch.com/lets-build-with-ruby-on-rails-installation/)
*   [让我们构建:用 Ruby on Rails——带评论的博客](https://web-crunch.com/lets-build-with-ruby-on-rails-blog-with-comments)
*   [让我们构建:用 Ruby on Rails——Twitter 克隆版](https://web-crunch.com/lets-build-with-ruby-on-rails-a-twitter-clone/)
*   [让我们构建:用 Ruby on Rails——dribble 克隆](https://web-crunch.com/lets-build-dribbble-clone-with-ruby-on-rails/)
*   [让我们构建:用 Ruby on Rails](https://web-crunch.com/lets-build-with-ruby-on-rails-project-management-app/) …

</article>

[View on GitHub](https://github.com/justalever/flanger)

### 有什么 app？

本系列将涉及创建一个带有用户身份验证的电子商务应用程序。我称之为“Flanger”的应用将围绕销售和购买乐器。

该应用程序将使用一系列模型来制定购物车和待售商品。在这一过程中，我们将设计一些方便的助手来执行计算，以计算购物车中的商品总数，并且如果用户在没有登录的情况下将商品添加到购物车中，还会记住用户是谁。

拥有帐户的用户可以发布待售工具。

### 我们不会报道什么？

构建一个完整的电子商务应用程序是一个相当大的挑战。通常情况下，您需要用户认证、大量的待售商品列表选项、购物车解决方案，当然还有支付选项。接受付款是我计划在未来涵盖的一个主题，但现在，出于时间和头痛的原因，我从应用程序中省略了这一点！如果你想进一步扩展这个应用程序，我们非常欢迎你。我个人在其他应用程序上使用 Stripe，并发现集成它非常成功。

### 第一部分

[https://www.youtube.com/embed/D5hIc_rnpaY](https://www.youtube.com/embed/D5hIc_rnpaY)

### 第二部分

[https://www.youtube.com/embed/kUoN3Uv9ZTQ](https://www.youtube.com/embed/kUoN3Uv9ZTQ)

### 第三部分

[https://www.youtube.com/embed/gdcXNwvzxd0](https://www.youtube.com/embed/gdcXNwvzxd0)

### 第四部分

[https://www.youtube.com/embed/M8WPKT6KZjE](https://www.youtube.com/embed/M8WPKT6KZjE)

### 第五部分

[https://www.youtube.com/embed/R_LSbReOJHU](https://www.youtube.com/embed/R_LSbReOJHU)

### 第六部分

[https://www.youtube.com/embed/bCFWY9NtdZ0](https://www.youtube.com/embed/bCFWY9NtdZ0)

#### 宝石

如果你碰巧看过我以前的构建，我们的 Gemfile 是相对熟悉的。最值得注意的宝石是载波宝石，我在不久前发布的 [Dribbble clone](https://web-crunch.com/lets-build-dribbble-clone-with-ruby-on-rails/) 上也使用了它。

```
...
gem 'bulma-rails', '~> 0.6.1'
gem 'simple_form', '~> 3.5'
gem 'devise', '~> 4.4'
gem 'gravatar_image_tag', '~> 1.2'
gem 'carrierwave'
gem 'mini_magick'

group :development, :test do
  ...
  gem 'better_errors', '~> 2.4'
  gem 'guard', '~> 2.14', '>= 2.14.1'
  gem 'guard-livereload', '~> 2.5', '>= 2.5.2'
end 
```

Enter fullscreen mode Exit fullscreen mode

## 拉开序幕

### 仪器

我们在应用程序中的主要模型将是`Instrument`模型。它将展示与特定待售仪器相关的所有细节和产品规格。首先，我将带您完成一些常规设置，然后搭建一个仪器模型，如下所示:

```
$ rails g scaffold Instrument brand:string model:string description:text condition:string finish:string title:string price:decimal --no-stylesheets --no-javascripts 
```

Enter fullscreen mode Exit fullscreen mode

这个长长的一行程序创建了我们的`Instrument`模型及其相关参数。注意`price`是一个十进制数据类型。这也可以是一个整数，甚至是一个字符串，这取决于您希望如何处理数据。运行 scaffold 后，确保修改 price 列以匹配以下内容，从而获得最佳结果。

```
class CreateInstruments < ActiveRecord::Migration[5.2]
  def change
    create_table :instruments do |t|
      t.string :brand
      t.string :model
      t.text :description
      t.string :condition
      t.string :finish
      t.string :title
      # could be an integer if you want, here we set a precision, scale, and default starting point for the decimal
      t.decimal :price, precision: 5, scale: 2, default: 0

      t.timestamps
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

另外，请注意我们传递的两个标志`—no-stylesheets —no-javascripts`。当运行任何类型的生成时，Rails 都可以适应。这里我们告诉 rails 不要添加样式表和 javascript 文件。你可以传递更多的旗帜。要找出那些是什么，只需运行这个:

```
$ rails g scaffold 
```

Enter fullscreen mode Exit fullscreen mode

### 推车

所有电子商务应用程序都需要某种购物车。在我们的购物车中，我们还需要一个空间来存放我最后称之为“行项目”的东西。要创建购物车，我们只需定义一个新的模型。

```
$ rails g scaffold Cart --no-stylesheets --no-javascripts 
```

Enter fullscreen mode Exit fullscreen mode

我们不需要在这里指定列和列，也可以使用 cart 作为一般的 restful 模型。

**关注点**

关注点类似于您可以在整个应用程序的控制器中包含的助手。我们需要知道给定用户的浏览会话历史，以便正确地将他们的会话与给定的购物车关联起来。将以下内容添加到`models/concerns/current_cart.rb`。

```
# models/concerns/current_cart.rb

module CurrentCart

  private

    def set_cart
      @cart = Cart.find(session[:cart_id])
    rescue ActiveRecord::RecordNotFound
      @cart = Cart.create
      session[:cart_id] = @cart.id
    end

end 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们找到给定会话的购物车 id(如果它存在的话)。如果没有，我们利用 intro rails 内置的一些方便的救援模式来防止应用程序倾覆。如果没有找到一个`cart_id`,我们就创建一个，并将它的 id 与用户的会话相关联。这最终允许我们引用任何类型的用户，而不需要他们首先创建某种帐户或在任何地方登录。很酷吧？

### 将仪器连接到推车

为了让我们的仪器与手推车对话，我们需要一个介于两者之间的模型。为此，我创建了一个名为`LineItem`的新模型。这一代非常聪明，因为只需几个按键，我们就可以使用仪器模型来引用给定的行项目以及属于购物车。这最终将索引添加到表中，使它们相互了解。

```
$ rails g scaffold LineItem instrument:references cart:belongs_to
$ rails db:migrate 
```

Enter fullscreen mode Exit fullscreen mode

这一代创建了相当多的文件。最引人注目的当然包括模型。

```
# models/line_item.rb 

class LineItem < ApplicationRecord
  belongs_to :instrument
  belongs_to :cart
end 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一种方法来找出给定购物车中商品的数量。让我们添加一个迁移来做到这一点。

```
$ rails g migration add_quantity_to_line_items quantity:integer, default: 1 
```

Enter fullscreen mode Exit fullscreen mode

注意数量是一个整数，默认值为`1`。这是有意的，以便向购物车添加一个商品的用户将看到 quantity 字段中确实有一个商品，而不是 0。传统的编程使用从零开始的整数。这是编程中的一个常见问题，经常困扰着包括您在内的许多开发人员！

```
class AddQuantityToLineItems < ActiveRecord::Migration[5.2]
  def change
    add_column :line_items, :quantity, :integer, default: 1
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

确保运行以下命令:

```
$ rails db:migrate 
```

Enter fullscreen mode Exit fullscreen mode

## 让模特们交流

有了行项目模型，一切就绪，让我们加快`Cart`模型和`Instrument`模型的速度。

```
# models/cart.rb

class Cart < ApplicationRecord
  has_many :line_items, dependent: :destroy

  def add_instrument(instrument)
    current_item = line_items.find_by(instrument_id: instrument.id)

    if current_item
      current_item.increment(:quantity)
    else
      current_item = line_items.build(instrument_id: instrument.id)
    end
    current_item
  end

  def total_price
    line_items.to_a.sum { |item| item.total_price }
  end

end 
```

Enter fullscreen mode Exit fullscreen mode

下面是仪器型号。您将在这里看到相当多的设置和验证。请随意调整这些到你自己喜欢的程度。

```
# models/instrument.rb
class Instrument < ApplicationRecord  
  before_destroy :not_referenced_by_any_line_item
  mount_uploader :image, ImageUploader  # carrierwave support for our image column
  serialize :image, JSON # If you use SQLite, add this line.
  belongs_to :user, optional: true

  validates :title, :brand, :price, :model, presence: true
  validates :description, length: { maximum: 1000, too_long: "%{count} characters is the maximum allowed" }
  validates :title, length: { maximum: 140, too_long: "%{count} characters is the maximum allowed" }
  validates :price, numericality: { only_integer: true }, length: { maximum: 7 }

  BRAND = %w{ Fender Gibson Epiphone ESP Martin Dean Taylor Jackson PRS  Ibanez Charvel Washburn }
  FINISH = %w{ Black White Navy Blue Red Clear Satin Yellow Seafoam }
  CONDITION = %w{ New Excellent Mint Used Fair Poor }

  has_many :line_items

  private

   def not_referenced_by_any_line_item
    unless line_items.empty?
      errors.add(:base, 'Line items present')
      throw :abort
    end
   end

end 
```

Enter fullscreen mode Exit fullscreen mode

行项目模型

```
# app/models/line_item.rb

class LineItem < ApplicationRecord
  belongs_to :instrument
  belongs_to :cart

  def total_price
    instrument.price.to_i * quantity.to_i
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

用户模型

```
# app/models/user.rb

class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable
  has_many :instruments
end 
```

Enter fullscreen mode Exit fullscreen mode

## 控制器！

行项目控制器有点神奇地将仪器与推车关联起来。我们挂钩到一个`instrument_id`参数，它是在我们做行项目支架时添加的。我们在这里最关注的是创建和销毁操作。

```
# app/controllers/line_items_controller.rb

class LineItemsController < ApplicationController
  include CurrentCart
  before_action :set_line_item, only: [:show, :edit, :update, :destroy]
  before_action :set_cart, only: [:create]

  # GET /line_items
  # GET /line_items.json
  def index
    @line_items = LineItem.all
  end

  # GET /line_items/1
  # GET /line_items/1.json
  def show
  end

  # GET /line_items/new
  def new
    @line_item = LineItem.new
  end

  # GET /line_items/1/edit
  def edit
  end

  # POST /line_items
  # POST /line_items.json
def create
    @instrument = Instrument.find(params[:instrument_id])
    @line_item = @cart.add_instrument(@instrument)

    respond_to do |format|
      if @line_item.save
        format.html { redirect_to @line_item.cart, notice: 'Line item was successfully created.' }
        format.json { render :show, status: :created, location: @line_item }
      else
        format.html { render :new }
        format.json { render json: @line_item.errors, status: :unprocessable_entity }
      end
    end
  end

  # PATCH/PUT /line_items/1
  # PATCH/PUT /line_items/1.json
  def update
    respond_to do |format|
      if @line_item.update(line_item_params)
        format.html { redirect_to @line_item, notice: 'Line item was successfully updated.' }
        format.json { render :show, status: :ok, location: @line_item }
      else
        format.html { render :edit }
        format.json { render json: @line_item.errors, status: :unprocessable_entity }
      end
    end
  end

  # DELETE /line_items/1
  # DELETE /line_items/1.json
  def destroy
    @line_item.destroy
    respond_to do |format|
      format.html { redirect_to line_items_url, notice: 'Line item was successfully destroyed.' }
      format.json { head :no_content }
    end
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_line_item
      @line_item = LineItem.find(params[:id])
    end

    # Never trust parameters from the scary internet, only allow the whitelist through.
    def line_item_params
      params.require(:line_item).permit(:instrument_id)
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

cart 控制器非常类似于`line_items_controller.rb`文件，在那里我们最关注的是创建和销毁动作。最终代码如下:

```
# app/controllers/carts_controller.rb

class CartsController < ApplicationController
  rescue_from ActiveRecord::RecordNotFound, with: :invalid_cart
  before_action :set_cart, only: [:show, :edit, :update, :destroy]

  # GET /carts
  # GET /carts.json
  def index
    @carts = Cart.all
  end

  # GET /carts/1
  # GET /carts/1.json
  def show
  end

  # GET /carts/new
  def new
    @cart = Cart.new
  end

  # GET /carts/1/edit
  def edit
  end

  # POST /carts
  # POST /carts.json
  def create
    @cart = Cart.new(cart_params)

    respond_to do |format|
      if @cart.save
        format.html { redirect_to @cart, notice: 'Cart was successfully created.' }
        format.json { render :show, status: :created, location: @cart }
      else
        format.html { render :new }
        format.json { render json: @cart.errors, status: :unprocessable_entity }
      end
    end
  end

  # PATCH/PUT /carts/1
  # PATCH/PUT /carts/1.json
  def update
    respond_to do |format|
      if @cart.update(cart_params)
        format.html { redirect_to @cart, notice: 'Cart was successfully updated.' }
        format.json { render :show, status: :ok, location: @cart }
      else
        format.html { render :edit }
        format.json { render json: @cart.errors, status: :unprocessable_entity }
      end
    end
  end

  # DELETE /carts/1
  # DELETE /carts/1.json
  def destroy
    @cart.destroy if @cart.id == session[:cart_id]
    session[:cart_id] = nil
    respond_to do |format|
      format.html { redirect_to root_path, notice: 'Cart was successfully destroyed.' }
      format.json { head :no_content }
    end
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_cart
      @cart = Cart.find(params[:id])
    end

    # Never trust parameters from the scary internet, only allow the white list through.
    def cart_params
      params.fetch(:cart, {})
    end

    def invalid_cart
      logger.error "Attempt to access invalid cart #{params[:id]}"
      redirect_to root_path, notice: "That cart doesn't exist"
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

最后，仪器控制人员会设计在此创建、编辑或销毁的任何新记录。我们还在每个 restful 路径上验证减去索引和显示动作。

```
# app/controllers/instruments_controller.rb

class InstrumentsController &lt; ApplicationController
  before_action :set_instrument, only: [:show, :edit, :update, :destroy]
  before_action :authenticate_user!, except: [:index, :show]

  # GET /instruments
  # GET /instruments.json
  def index
    @instruments = Instrument.all.order("created_at desc")
  end

  # GET /instruments/1
  # GET /instruments/1.json
  def show
  end

  # GET /instruments/new
  def new
    @instrument = current_user.instruments.build
  end

  # GET /instruments/1/edit
  def edit
  end

  # POST /instruments
  # POST /instruments.json
  def create
    @instrument = current_user.instruments.build(instrument_params)

    respond_to do |format|
      if @instrument.save
        format.html { redirect_to @instrument, notice: 'Instrument was successfully created.' }
        format.json { render :show, status: :created, location: @instrument }
      else
        format.html { render :new }
        format.json { render json: @instrument.errors, status: :unprocessable_entity }
      end
    end
  end

  # PATCH/PUT /instruments/1
  # PATCH/PUT /instruments/1.json
  def update
    respond_to do |format|
      if @instrument.update(instrument_params)
        format.html { redirect_to @instrument, notice: 'Instrument was successfully updated.' }
        format.json { render :show, status: :ok, location: @instrument }
      else
        format.html { render :edit }
        format.json { render json: @instrument.errors, status: :unprocessable_entity }
      end
    end
  end

  # DELETE /instruments/1
  # DELETE /instruments/1.json
  def destroy
    @instrument.destroy
    respond_to do |format|
      format.html { redirect_to instruments_url, notice: 'Instrument was successfully destroyed.' }
      format.json { head :no_content }
    end
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_instrument
      @instrument = Instrument.find(params[:id])
    end

    # Never trust parameters from the scary internet, only allow the white list through.
    def instrument_params
      params.require(:instrument).permit(:brand, :model, :description, :condition, :finish, :title, :price, :image)
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 组合给定购物车中的商品

我们遇到的一个棘手问题是如何在给定的购物车中组合行项目。我们可以用一个相当大的迁移来创造这样做的新想法。

```
$ rails g migration combine_items_in_cart 
```

Enter fullscreen mode Exit fullscreen mode

```
# db/migrate/combine_items_in_cart 

class CombineItemsInCart < ActiveRecord::Migration[5.2]
  def up
    Cart.all.each do |cart|
      sums = cart.line_items.group(:instrument_id).sum(:quantity)
      sums.each do |instrument_id, quantity|
        if quantity > 1
          cart.line_items.where(instrument_id: instrument_id).delete_all

          item = cart.line_items.build(instrument_id: instrument_id)
          item.quantity = quantity
          item.save!
        end
      end
    end
  end

  def down
    #split items with a quantity of 1 or more into multiple items
    LineItem.where("quantity > 1").each do |line_item|
      line_item.quantity.times do
        LineItem.create(
          cart_id: line_item.cart_id,
          instrument_id: line_item.instrument_id,
          quantity: 1
        )
      end
      # remove original line item
      line_item.destroy
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 在朋友(帮手)的一点点帮助下，我勉强度日。)

对于这个应用程序，我们定义了几个助手来确定用户购物车中的商品数量，并在应用程序的任何地方将其显示在导航栏中。

```
# app/helpers/application_helper.rb

module ApplicationHelper

  def cart_count_over_one
    if @cart.line_items.count > 0
      return "<span class='tag is-dark'>#{@cart.line_items.count}</span>".html_safe
    end
  end

  def cart_has_items
    return @cart.line_items.count > 0
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我没有打出相当长的条件，而是选择将其抽象成一个助手，以查看给定工具的作者是否确实是 said，author。

```
# app/helpers/instruments_helper.rb

module InstrumentsHelper

  def instrument_author(instrument)
    user_signed_in? && current_user.id == instrument.user_id
  end

end 
```

Enter fullscreen mode Exit fullscreen mode

## 播种

Rails 在为成功做准备方面非常棒。您可以从一开始就定义一些占位符内容，并将这些内容植入到您的应用程序中。在野外有一些宝石可以帮助你生成一些假数据，供你随意使用。播种数据最终有助于节省调试代码的时间和精力。本系列的最后一步是关于如何为此应用程序播种数据的指南。

如果您已经做到了这一步，我真的非常感谢您跟随我的旅程，让 Ruby on Rails 变得更好。我意识到这个应用程序是不完整的，但我确实计划引入围绕使用 rails 作为过程背后的框架接受支付的想法。未来的构建和/或短视频将有望继续学习。在那之前，一定要看看我的其他视频，订阅我的 [YouTube](https://youtube.com/c/webcrunch) 频道。你也可以在[推特](https://twitter.com/webcrunchblog)上关注我，即时更新新内容。

### 无耻的塞时间

[![](img/e4dc18bd4506d5a2ef46a7e214a4132b.png)](http://hellorails.io)

我有一门新课程叫做 [Hello Rails](https://hellorails.io) 。Hello Rails 是一门现代课程，旨在帮助您快速开始使用和理解 Ruby on Rails。如果你是 Ruby 或 Ruby on Rails 的新手，我邀请你去看看这个网站。该课程将与这些构建非常相似，但是是一个超级深入的版本，具有更现实的目标和可交付成果。[报名今天得到通知](https://hellorails.io/)！

在推特上关注 [@hello_rails](https://twitter.com/hello_rails) 和[本人@justalever](https://twitter.com/justalever) 。