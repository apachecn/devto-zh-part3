# 让我们构建:用 Ruby on Rails-dribble 克隆

> 原文：<https://dev.to/justalever/lets-build-with-ruby-on-rails---dribbble-clone--1n7i>

欢迎阅读由五部分组成的“让我们一起构建:用 Ruby on Rails”系列，在这个系列中，我将教您如何用 Ruby on Rails 构建一个 Dribbble 克隆。这个系列是我们迄今为止最彻底的构建！

Dribbble 是一个活跃的设计社区，各种各样的设计师在这里发布他们正在做的任何事情的“照片”。原本打算成为“展示你的进步”类型的网站，现在已经变成了一个面向未来的设计师和经验丰富的专业人士的投资组合。(我现在碰巧在这里工作💃)

我们的克隆版将引入一些新概念，以及我在之前的版本中提到的其他概念。如果您刚刚接触 Ruby on Rails，我邀请您看看我下面的其他系列文章，了解框架背后的一些基本原则。

*   [Ruby on Rails 简介](https://web-crunch.com/lets-build-with-ruby-on-rails-introduction/)
*   [在你的机器上安装 Ruby on Rails](https://web-crunch.com/lets-build-with-ruby-on-rails-installation/)
*   [使用 Ruby on Rails 创建一个带有评论的基本博客](https://web-crunch.com/lets-build-with-ruby-on-rails-blog-with-comments/)
*   [用 Ruby on Rails 构建一个 Twitter 克隆](https://web-crunch.com/lets-build-with-ruby-on-rails-a-twitter-clone/)

### 我们将深入构建什么

我们的应用程序将具有以下功能:

*   创建、编辑和销毁“镜头”以及喜欢和不喜欢单个镜头的能力。
*   用户角色和身份验证
*   拖放功能
*   评论功能
*   查看计数/分析
*   使用 CSS 网格的自定义响应快照网格用户界面

在这个版本的引擎盖下有很多东西，所以我在 Github 上创建了一个公共回购，供你跟随/参考。为了节省时间，你会注意到在下面的一些视频中，我复制并粘贴了一些通用的样式和标记

#### 下载源代码

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[just alever](https://github.com/justalever)/[dribbble _ clone](https://github.com/justalever/dribbble_clone)

### 如何使用 Ruby on Rails 构建一个可滴克隆的演示

<article class="markdown-body entry-content container-lg" itemprop="text">

# 让我们用 Ruby on Rails 构建一个 Dribbble 克隆

[![Let's Build A Dribbble Clone](img/f7ef591aa448c79ededa3e6411bfeb84.png)](https://camo.githubusercontent.com/68d07755833451cc7078e6421c1e95308d735a66c0329d80f416ab5fe6324f93/68747470733a2f2f692e696d6775722e636f6d2f6947626b3831692e6a7067)

欢迎来到这个由五部分组成的迷你系列，在这里我将教你如何用 Ruby on Rails 构建一个可滴克隆。这个系列是我们迄今为止最彻底的构建！

Dribbble 是一个活跃的设计社区，各种各样的设计师在这里发布他们正在做的任何事情的“照片”。原本打算成为“展示你的进步”类型的网站，现在已经变成了一个面向未来的设计师和经验丰富的专业人士的投资组合。

我们的 Dribbble 克隆将引入一些新概念，以及我在以前的版本中提到的其他概念。如果您刚刚接触 Ruby on Rails，我邀请您看看我下面的其他系列文章，了解框架背后的一些基本原则。

*   [Ruby on Rails 简介](https://web-crunch.com/lets-build-with-ruby-on-rails-introduction/)
*   [在你的机器上安装 Ruby on Rails](https://web-crunch.com/lets-build-with-ruby-on-rails-installation/)
*   [造一个](https://web-crunch.com/lets-build-with-ruby-on-rails-blog-with-comments/) …

</article>

[View on GitHub](https://github.com/justalever/dribbble_clone)

### 什么不会出现在构建中

*   独立用户配置文件
*   搜索
*   磨尖
*   像 Dribbble 目前所做的那样自定义调色板生成。
*   一大堆其他很酷的功能

我们可以做很多事情来扩展克隆，但是因为它是一个“示例”类型的应用程序，所以我决定暂时放弃对它进行大幅扩展。将来，我可能会添加到这个系列中，并进一步扩展它，但我主要邀请您自己做同样的事情，看看您如何能够更好地模仿真实的应用程序。

### 手表第一部分

[https://www.youtube.com/embed/u3-tZMKYegg](https://www.youtube.com/embed/u3-tZMKYegg)

### 手表第二部分

[https://www.youtube.com/embed/7rti-QamKlg](https://www.youtube.com/embed/7rti-QamKlg)

### 手表第三部分

[https://www.youtube.com/embed/ywMt-R5QIHU](https://www.youtube.com/embed/ywMt-R5QIHU)

### 手表第四部分

[https://www.youtube.com/embed/vP0lMyO59Yc](https://www.youtube.com/embed/vP0lMyO59Yc)

### 手表第五部分

[https://www.youtube.com/embed/LSMbwIUS558](https://www.youtube.com/embed/LSMbwIUS558)

### 入门

假设你已经在你的机器上安装了 rails 和 ruby([在这里学习如何](https://web-crunch.com/lets-build-with-ruby-on-rails-installation/))你已经准备好开始了。从您最喜欢的工作目录，使用您最喜欢的命令行工具运行以下命令:

```
$ rails new dribbble_clone
```

这将搭建一个新的 rails 项目，并创建名为`dribbble_clone`的文件夹。

作为开始，我已经将这个项目中的所有宝石添加到我的`Gemfile`中。如果你还没有拿到我在这里使用的相同版本，你可以参考回购协议。

#### 宝石清单

自从上一次建造以来，我们的宝石列表已经增加了。你会发现你的应用程序越大，你需要的宝石就越多。

*   [carrier wave](https://github.com/carrierwaveuploader/carrierwave)+[Mini Magick](https://github.com/minimagick/minimagick)-用于图像上传和优化
*   [设计](https://github.com/plataformatec/devise) -用户认证和角色
*   守卫(Guard)——在文件改变时对其进行处理——类似于任务运行者
*   [Guard Livereload](https://github.com/guard/guard-livereload) -当文件改变时，结合您最喜爱的浏览器上的实时重新加载扩展，重新加载浏览器。
*   [更好的错误](https://github.com/charliesome/better_errors) -在开发过程中显示更好的错误。
*   [简单形式](https://github.com/plataformatec/simple_form)-Rails 中更简单的形式
*   [布尔玛轨道](https://github.com/joshuajansen/bulma-rails) -我最近最喜欢的基于 Flexbox 的 CSS 框架。
*   印象派画家 -我们用这个来获得照片的点击率
*   [Gravatar 图像标签](https://github.com/mdeering/gravatar_image_tag) -根据用户的帐户电子邮件获取用户 Gravatar 图像的简单方法
*   [充当可变](https://github.com/ryanto/acts_as_votable) -喜欢和不喜欢的镜头

我会尊重视频的指导，让我们的项目都与每个单独的宝石设置。第 1 部分的大部分都致力于这个过程。我们定制设计位添加一个新用户注册的“名称”字段。再次参考回购协议，看看需要什么。

## 脚手架的拍摄

我们可以浏览并为我们的镜头创建一个控制器、模型和许多其他文件，但是删除我们之后不需要的文件会更容易，也可能更有效。Rails 通过生成运行 scaffold 命令时所需的所有必要文件和迁移文件来帮助我们。

运行以下代码来生成带有标题和描述属性的`Shot`:

```
 rails g scaffold Shot title:string description:text user_id:integer
rails db:migrate 
```

### 将用户关联到快照

在`app`目录中导航到您的`models`方向。我们需要把用户和镜头联系起来。修改您的文件，如下所示。请注意，我们在安装 Devise 时已经添加了一些数据。你可能已经看到了一些这样的关联。否则，此时它们应该如下所示:

```
 # app/models/user.rb
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable
  has_many :shots
end 
```

```
 # app/models/shot.rb
class Shot < ApplicationRecord
    belongs_to :user
end 
```

### 载波设置

一张照片的基础当然是它的图像。Carrierwave 是图片上传的一颗宝石，有一堆的铃铛和哨子。确保你已经安装了 [CarrierWave gem](https://github.com/carrierwaveuploader/carrierwave) ，然后运行以下程序生成一个名为`user_shot_uploader.rb`的新上传文件。确保你已经安装了我提到的[迷你宝石](https://github.com/minimagick/minimagick)。

```
$ rails generate uploader user_shot
```

这将为您提供一个文件:

`app/uploaders/user_shot_uploader.rb`

在这个文件中，我们可以定制一些关于载波如何处理我们的图像。我的档案看起来是这样的。注意:我已经删除了一些不必要的评论。

```
 class UserShotUploader < CarrierWave::Uploader::Base

  # Include RMagick or MiniMagick support:
  # include CarrierWave::RMagick
  include CarrierWave::MiniMagick

  # Choose what kind of storage to use for this uploader:
  storage :file

  # Override the directory where uploaded files will be stored.
  # This is a sensible default for uploaders that are meant to be mounted:
  def store_dir
    "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
  end

  # Create different versions of your uploaded files:
  version :full do
    process resize_to_fit: [800, 600]
  end
  version :thumb do
    process resize_to_fit: [400, 300]
  end

  def extension_whitelist
    %w(jpg jpeg gif png)
  end
end 
```

### 将我们的用户镜头与一个镜头关联

我们需要我们的新用户拍摄的图像是我们的数据库表上的一个字段。为此，我们需要将它添加为一个迁移，这将为我们提供一个类型为`string`的`user_shot`列。

```
 $ rails g migration add_shot_to_users shot:string
$ rails db:migrate 
```

在`app/models/shot.rb`内的`shot.rb`文件中添加以下代码，使其全部工作。

```
 class Shot < ActiveRecord::Base
  belongs_to :user
  mount_uploader :user_shot, UserShotUploader # add this line
end 
```

为了真正允许用户上传一个镜头，我们需要配置我们的控制器来允许所有这些新参数。

### 创建新镜头

此时，我们的镜头控制器需要一些 TLC 才能正常工作。除了控制器，我还想在`new`和`edit`视图中引入一些拖放类型的功能。Dribbble 会自动上传图像，作为他们最初拍摄的一部分。为了节省时间，我模仿了这一点，但它肯定达不到正常工作的效果。

在`app/controllers/`中找到的`shots_controller.rb`最后看起来是这样的:

```
 class ShotsController < ApplicationController
  before_action :set_shot, only: [:show, :edit, :update, :destroy, :like, :unlike]
  before_action :authenticate_user!, only: [:edit, :update, :destroy, :like, :unlike]
  impressionist actions: [:show], unique: [:impressionable_type, :impressionable_id, :session_hash]

  # GET /shots
  # GET /shots.json
  def index
    @shots = Shot.all.order('created_at DESC')
  end

  # GET /shots/1
  # GET /shots/1.json
  def show
  end

  # GET /shots/new
  def new
    @shot = current_user.shots.build
  end

  # GET /shots/1/edit
  def edit
  end

  # POST /shots
  # POST /shots.json
  def create
    @shot = current_user.shots.build(shot_params)

    respond_to do |format|
      if @shot.save
        format.html { redirect_to @shot, notice: 'Shot was successfully created.' }
        format.json { render :show, status: :created, location: @shot }
      else
        format.html { render :new }
        format.json { render json: @shot.errors, status: :unprocessable_entity }
      end
    end
  end

  # PATCH/PUT /shots/1
  # PATCH/PUT /shots/1.json
  def update
    respond_to do |format|
      if @shot.update(shot_params)
        format.html { redirect_to @shot, notice: 'Shot was successfully updated.' }
        format.json { render :show, status: :ok, location: @shot }
      else
        format.html { render :edit }
        format.json { render json: @shot.errors, status: :unprocessable_entity }
      end
    end
  end

  # DELETE /shots/1
  # DELETE /shots/1.json
  def destroy
    @shot.destroy
    respond_to do |format|
      format.html { redirect_to shots_url, notice: 'Shot was successfully destroyed.' }
      format.json { head :no_content }
    end
  end

  def like
    @shot.liked_by current_user
    respond_to do |format|
      format.html { redirect_back fallback_location: root_path }
      format.json { render layout:false }
    end
  end

  def unlike
    @shot.unliked_by current_user
    respond_to do |format|
      format.html { redirect_back fallback_location: root_path }
      format.json { render layout:false }
    end
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_shot
      @shot = Shot.find(params[:id])
    end

    # Never trust parameters from the scary internet, only allow the whitelist through.
    def shot_params
      params.require(:shot).permit(:title, :description, :user_shot)
    end
end 
```

`like`和`unlike`动作是特定于我们作为可投票宝石的行为的，我将很快讨论这一点。关于`shot_params`私有方法的通知我们允许`app/views/_form.html.erb`内部的字段。

### 添加视图和拖放功能

部分表单将被重新用于编辑和创建新镜头。我想利用一些拖放。为了做到这一点，我引入了一些 JavaScript 代码，这些代码很神奇，让这个过程看起来很流畅。我们根据元素在浏览器窗口中的位置向定义的拖放区添加类。当图像被放下时，它会使用大多数现代浏览器内置的 FileReader API 自动显示。它还将其路径附加到一个隐藏的文件输入中，当一个新的帖子被创建时，该文件输入将被传递给我们在`shots_controller.rb`上的 create 操作。

当一切完成后，我们的新快照就创建好了，并与当前登录的用户或本例中的`current_user`相关联。

在我们的表单分部里面`app/views/_form.html.erb`我有下面的代码:

```
 <%= simple_form_for @shot, html: { multipart: true }  do |f| %>
  <%= f.error_notification %>
  <div class="columns is-centered">
    <div class="column is-half">

    <% unless @shot.user_shot.blank? %>
    <%= image_tag (@shot.user_shot_url), id: "previewImage" %>
    <% end %>

    <output id="list"></output>
    <div id="drop_zone">Drag your shot here</div>
    <br />

    <%= f.input :user_shot, label: false, input_html: { class: "file-input", type: "file" }, wrapper: false, label_html: { class: "file-label" } %>

    <div class="field">
      <div class="control">
        <%= f.input :title, label: "Title", input_html: { class: "input"}, wrapper: false, label_html: { class: "label" } %>
      </div>
    </div>

    <div class="field">
        <div class="control">
        <%= f.input :description, input_html: { class: "textarea"}, wrapper: false, label_html: { class: "label" } %>
      </div>
    </div>

    <div class="field">
        <div class="control">
        <%= f.button :submit, class:"button is-primary" %>
      </div>
    </div>
  </div>
</div>
<% end %> 
```

这里没有什么太疯狂的。我为布尔玛添加了特定的标记，以便很好地与 SimpleForm 配合使用，并为我们的 JavaScript 添加了一些 HTML 来进行交互:

```
 <output id="list"></output>
<div id="drop_zone">Drag your shot here</div> 
```

我们的降落区是奇迹发生的地方。结合一些 CSS，我们可以在用户与区域交互时进行更改。检查 [repo](https://github.com/justalever/dribbble_clone/blob/master/app/assets/stylesheets/application.scss) 获取此处需要的 CSS。

完成所有这些工作的 JavaScript 可以在`app/assets/javascripts/shot.js`中找到

```
 document.addEventListener("turbolinks:load", function() {

    var Shots = {
        previewShot() {
            if (window.File && window.FileList && window.FileReader) {

                function handleFileSelect(evt) {
                    evt.stopPropagation();
                    evt.preventDefault();

                    let files = evt.target.files || evt.dataTransfer.files; 
                    // files is a FileList of File objects. List some properties.
                    for (var i = 0, f; f = files[i]; i++) {

                        // Only process image files.
                        if (!f.type.match('image.*')) {
                            continue;
                        }
                        const reader = new FileReader();

                        // Closure to capture the file information.
                        reader.onload = (function(theFile) {
                            return function(e) {
                                // Render thumbnail.
                                let span = document.createElement('span');
                                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'
                                ].join('');
                                document.getElementById('list').insertBefore(span, null);
                            };
                        })(f);

                        // Read in the image file as a data URL.
                        reader.readAsDataURL(f);
                    }
                }

                function handleDragOver(evt) {
                    evt.stopPropagation();
                    evt.preventDefault();
                    evt.dataTransfer.dropEffect = 'copy'; // Explicitly show this is a copy.
                }

                // Setup the dnd listeners.
                // https://stackoverflow.com/questions/47515232/how-to-set-file-input-value-when-dropping-file-on-page
                const dropZone = document.getElementById('drop_zone');
                const target = document.documentElement;
                const fileInput = document.getElementById('shot_user_shot');
                const previewImage = document.getElementById('previewImage');
                const newShotForm = document.getElementById('new_shot');

                if (dropZone) {
                    dropZone.addEventListener('dragover', handleDragOver, false);
                    dropZone.addEventListener('drop', handleFileSelect, false);

                    // Drop zone classes itself
                    dropZone.addEventListener('dragover', (e) => {
                        dropZone.classList.add('fire');
                    }, false);

                    dropZone.addEventListener('dragleave', (e) => {
                        dropZone.classList.remove('fire');
                    }, false);

                    dropZone.addEventListener('drop', (e) => {
                        e.preventDefault();
                        dropZone.classList.remove('fire');
                        fileInput.files = e.dataTransfer.files;
                        // if on shot/id/edit hide preview image on drop
                        if (previewImage) {
                            previewImage.style.display = 'none';
                        }
                        // If on shots/new hide dropzone on drop
                        if(newShotForm) {
                            dropZone.style.display = 'none';
                        }
                    }, false);

                    // Body specific 
                    target.addEventListener('dragover', (e) => {
                        e.preventDefault();
                        dropZone.classList.add('dragging');
                    }, false);

                    // removes dragging class to body WHEN NOT dragging
                    target.addEventListener('dragleave', (e) => {
                        dropZone.classList.remove('dragging');
                        dropZone.classList.remove('fire');
                    }, false);
                }
            }
        },
        // Displays shot title, description, and created_at time stamp on home page on hover.
        // Be sure to include jquery in application.js
        shotHover() {
            $('.shot').hover(function() {
                $(this).children('.shot-data').toggleClass('visible');
            });
        }

    };
    Shots.previewShot();
    Shots.shotHover();

});
```

我在视频中对这段代码做了完整的解释，所以请务必观看以了解这里发生了什么。

## 注释设置

评论是 Dribbble 社区的重要组成部分。我想把这些作为练习和解释添加到组合中，说明如何允许用户与 Devise 结合进行交互以及创建、编辑和销毁他们自己的评论。

首先，我们需要生成一个控制器。这里我指定只创建`create`和`destroy`动作。这也生成了那些视图。你可以删除`app/views/comments/`里面的。

```
$ rails g controller comments create destroy 
```

为了与我们的数据库进行交互，我们需要一个注释模型。运行以下命令:

```
$ rails g model comment name:string response:text
```

所有评论都有一个名称和回复字段。在这个系列中，我没有深入讨论验证是如何工作的，但是为了增强应用程序，我肯定会考虑添加这些，这样用户就必须输入他们的名字和有价值的响应。换句话说，没有空字段。

创建模型后，请确保运行:

```
 $ rails db:migrate 
```

我们需要将一个评论关联到一个镜头，为此需要给评论添加一个`shot_id`参数。Rails 足够聪明，知道`shot_id`是`shot`引用评论的方式。这适用于您关联在一起的任何类型的模型。

```
 $ rails g migration add_shot_id_to_comments 
```

在该代(`db/migrate/XXXXXXXXX_add_shot_id_to_comments`)中添加以下内容:

```
 class AddShotIdToComments < ActiveRecord::Migration[5.1]
  def change
    add_column :comments, :shot_id, :integer
  end
end 
```

然后运行:

```
 $ rails db:migrate 
```

接下来，我们需要直接关联模型。在注释模型中添加以下内容:

```
 # app/models/comment.rb
class Comment < ApplicationRecord
    belongs_to :shot
    belongs_to :user
end 
```

并更新拍摄模型:

```
 # app/model/shot.rb
class Shot < ApplicationRecord
    belongs_to :user
    has_many :comments, dependent: :destroy # if a shot is deleted so are all of its comments
    mount_uploader :user_shot, UserShotUploader
end 
```

更新路线以在快照中包含嵌套注释:

```
 # config/routes.rb

resources :shots do 
  resources :comments
end 
```

在我们的`comments_controller.rb`文件中，接下来是创建注释的逻辑。

```
 class CommentsController < ApplicationController
  # a user must be logged in to comment
    before_action :authenticate_user!, only: [:create, :destroy]

  def create
    # finds the shot with the associated shot_id
    @shot = Shot.find(params[:shot_id]) 
     # creates the comment on the shot passing in params 
    @comment = @shot.comments.create(comment_params)
     # assigns logged in user's ID to
    @comment.user_id = current_user.id if current_user comment
    # saves it
    @comment.save!
    # redirect back to Shot
    redirect_to shot_path(@shot)

  end

  def destroy
    @shot = Shot.find(params[:shot_id])
    @comment = @shot.comments.find(params[:id])
    @comment.destroy
    redirect_to shot_path(@shot)
  end

  private

  def comment_params 
    params.require(:comment).permit(:name, :response)
  end
end 
```

接下来是我们的观点。在`app/views/comments`中，我们将添加两个新的部分文件。这些最终会在`app/views/shots/shot.html.erb`中的镜头展示页面上呈现。

```
 <!-- app/views/comments/_form.html.erb -->
<%= simple_form_for([@shot, @shot.comments.build]) do |f| %>

    <div class="field">
        <div class="control">
            <%= f.input :name, input_html: { class: 'input'}, wrapper: false, label_html: { class: 'label' } %>
        </div>
    </div>

    <div class="field">
        <div class="control">
            <%= f.input :response, input_html: { class: 'textarea' }, wrapper: false, label_html: { class: 'label' } %>
        </div>
    </div>

    <%= f.button :submit, 'Leave a reply', class: 'button is-primary' %>
<% end %> 
```

这是我们的评论表，显示在照片下方。

```
 <!-- app/views/comments/_comment.html.erb -->
<div class="box">
  <article class="media">
    <figure class="media-left">
      <p class="image is-48x48 user-thumb">
        <%= gravatar_image_tag(comment.user.email.gsub('spam', 'mdeering'), alt: comment.user.name, gravatar: { size: 48 }) %>
      </p>
    </figure>
    <div class="media-content">
      <div class="content">
        <p>
          <strong><%= comment.user.name %></strong>
          <div><%= comment.response %></div>
        </p>
      </div>
    </div>
    <% if (user_signed_in? && (current_user.id == comment.user_id)) %>
       <%= link_to 'Delete', [comment.shot, comment],
                  method: :delete, class: "button is-danger", data: { confirm: 'Are you sure?' } %>
    <% end %>
  </article>
</div> 
```

这是实际的注释标记本身。这里我决定获取登录用户的名字，而不是他们在评论表单上输入的名字。如果你想扩展它，允许面向公众的用户发表评论，这当然是可能的。此时，我们的`comments_controller.rb`要求用户先登录或注册。

最后在我们的`show.html.erb`中呈现我们的评论。

```
 <!-- app/views/shots/show.html.erb - Note: This is the final version which includes some other methods from other gems/helpers. Check the repo or videos for the step by step here! -->
<div class="section">
    <div class="container">
        <h1 class="title is-3"><%= @shot.title %></h1>
        <div class="columns">
            <div class="column is-8">
                <span class="by has-text-grey-light">by</span>
                <div class="user-thumb">
                    <%= gravatar_image_tag(@shot.user.email.gsub('spam', 'mdeering'), alt: @shot.user.name, gravatar: { size: 20 }); %>
                </div>
                <div class="user-name has-text-weight-bold"><%= @shot.user.name %></div>
                <div class="shot-time"><span class="has-text-grey-light">posted</span><span class="has-text-weight-semibold">
                    <%= verbose_date(@shot.created_at) %>
                </span></div>
            </div>    
        </div>

        <div class="columns">
            <div class="column is-8">
                <div class="shot-container">
                    <div class="shot-full">
                         <%= image_tag @shot.user_shot_url unless @shot.user_shot.blank? %>
                    </div>

                    <% if user_signed_in? && (current_user.id == @shot.user_id) %>        
                        <div class="buttons has-addons">
                            <%= link_to 'Edit', edit_shot_path(@shot), class: "button" %>
                            <%= link_to 'Delete', shot_path, class: "button", method: :delete, data: { confirm: 'Are you sure you want to delete this shot?'} %>
                        </div>
                    <% end %>

                    <div class="content">
                        <%= @shot.description %>
                    </div>

                    <section class="comments">
                        <h2 class="subtitle is-5"><%= pluralize(@shot.comments.count, 'Comment') %></h2>
                            <%= render @shot.comments %>
                        <hr />
                        <% if user_signed_in? %>
                            <div class="comment-form">
                                <h3 class="subtitle is-3">Leave a reply</h3>
                                <%= render 'comments/form' %>
                            </div>
                        <% else %>
                            <div class="content"><%= link_to 'Sign in', new_user_session_path %> to leave a comment.</div>
                        <% end %>
                    </section>

                </div>
            </div>
            <div class="column is-3 is-offset-1">
                <div class="nav panel show-shot-analytics">
                    <div class="panel-block views data">
                        <span class="icon"><i class="fa fa-eye"></i></span>
                        <%= pluralize(@shot.impressionist_count, 'View') %>
                    </div>
                    <div class="panel-block comments data">
                        <span class="icon"><i class="fa fa-comment"></i></span>
                        <%= pluralize(@shot.comments.count, 'Comment') %>
                    </div>
                    <div class="panel-block likes data">
                        <% if user_signed_in? %>
              <% if current_user.liked? @shot %>
                <%= link_to unlike_shot_path(@shot), method: :put, class: "unlike_shot" do %>
                  <span class="icon"><i class="fa fa-heart has-text-primary"></i></span>
                  <span class="vote_count"><%= pluralize(@shot.get_likes.size, 'Like') %></span>
                <% end %>
              <% else %>
                <%= link_to like_shot_path(@shot), method: :put, class: "like_shot" do %>
                  <span class="icon"><i class="fa fa-heart"></i></span>
                  <span class="vote_count"><%= pluralize(@shot.get_likes.size, 'Like') %></span>
                <% end %>
              <% end %>
            <% else %>
                <%= link_to like_shot_path(@shot), method: :put, class: "like_shot" do %>
                  <span class="icon"><i class="fa fa-heart"></i></span>
                  <span class="vote_count"><%= pluralize(@shot.get_likes.size, 'Like') %></span>
                <% end %>
            <% end %>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div> 
```

## 印象派设置

拍摄独特的视角是一个很好的功能。为了让自己过得更轻松，我找到了一块名为印象派的宝石。它让印象变得很容易。

```
 $ rails g impressionist
$ rails db:migrate 
```

如果在指定使用哪个 rails 版本时出现错误，您需要将`[5.1]`添加到迁移中。使用您使用的任何版本的 rails。

将以下内容添加到`shots_controller.rb`文件中:

```
 class ShotsController < ApplicationController
    # only recording when a user views the shot page and is unique
    impressionist actions: [:show], unique: [:impressionable_type, :impressionable_id, :session_hash]

    ...  
end 
```

将以下内容添加到`app/models/`中的 shot.rb 文件:

```
 class Shot < ApplicationRecord
    belongs_to :user
    has_many :comments, dependent: :destroy

    mount_uploader :user_shot, UserShotUploader
    is_impressionable # adds support for impressionist on our shot model
end 
```

在视图中显示它:

```
 <!-- on our shot index page app/views/shots/index.html.erb -->
<%= shot.impressionist_count %>
<!-- on our shot show page app/views/shots/show.html.erb -->
<%= @shot.impressionist_count %> 
```

## 添加喜欢作为投票

另一个很好的功能是能够喜欢和不喜欢一个镜头。Dribbble 已经有了这个功能，尽管他们在幕后做了一点 AJAX，让一切都可以工作，而无需刷新浏览器。

### 安装

将以下内容添加到您的 gem 文件中，以安装最新版本。

```
 gem 'acts_as_votable', '~> 0.11.1' # check if this version is supported in your project 
```

然后用一个`bundle install`来跟进。

### 数据库迁移

充当 Votable 使用投票表来存储所有投票信息。要生成和运行迁移，请使用。

```
 $ rails generate acts_as_votable:migration
$ rake db:migrate 
```

我们需要将`acts_as_votable`标识符添加到我们想要投票的模型中。在我们的例子中是`Shot`。

```
 # app/models/shot.rb
class Shot < ApplicationRecord
    belongs_to :user
    has_many :comments, dependent: :destroy

    mount_uploader :user_shot, UserShotUploader
    is_impressionable
    acts_as_votable
end 
```

我还想将用户定义为*投票人*，因此我们将`acts_as_voter`添加到用户模型中。

```
 # app/models/user.rb
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable

  has_many :shots
  has_many :comments, dependent: :destroy
  acts_as_voter

end 
```

一定要创建一个可点击的喜欢或不喜欢，我们需要在我们的应用程序中引入一些路线。下面我添加了一个`member do`块，它本质上添加了嵌套在我们的`shots` restful 路由中的非 restful 路由。在这里运行`rake routes`将显示应用程序中的每条路线。

```
 Rails.application.routes.draw do
  resources :shots do 
    resources :comments
     member do
        put 'like', to: "shots#like"
        put 'unlike', to: "shots#unlike"
    end
  end
  devise_for :users, controllers: { registrations: 'registrations' }
  root "shots#index"
end 
```

控制器在这里需要一些工作，所以我在混合中添加了`:like`和`:unlike`动作。这些也通过我们的`before_actions`传递，它处理查找提及的镜头以及确保用户登录。

```
 # app/controllers/shots_controller.rb

before_action :set_shot, only: [:show, :edit, :update, :destroy, :like, :unlike] 
# sets up shot for like and unlike now keeping things dry.
before_action :authenticate_user!, only: [:edit, :update, :destroy, :like, :unlike] 
# add routes as authenticated. 
....

def like
  @shot.liked_by current_user
  respond_to do |format|
    format.html { redirect_back fallback_location: root_path }
    format.js { render layout: false }
  end
end

def unlike
  @shot.unliked_by current_user
  respond_to do |format|
    format.html { redirect_back fallback_location: root_path }
    format.js { render layout: false }
  end
end 
```

在我们的`app/views/show/`中的`index.html.erb`文件中，我们将以下内容添加到“likes”块中:

```
 <!-- app/views/shots/index.html.erb -->
<div class="level-item likes data">
   <div class="votes">
   <% if user_signed_in? %>
      <% if current_user.liked? shot %>
        <%= link_to unlike_shot_path(shot), method: :put, class: 'unlike_shot' do %>
            <span class="icon"><i class="fa fa-heart has-text-primary"></i></span>
            <span class="vote_count"><%= shot.get_likes.size %></span> 
        <% end %>
      <% else %>
        <%= link_to like_shot_path(shot), method: :put, class: 'like_shot' do %>
          <span class="icon"><i class="fa fa-heart"></i></span>
          <span class="vote_count"><%= shot.get_likes.size %></span> 
        <% end %>
      <% end %>
    <% else %>
        <%= link_to like_shot_path(shot), method: :put, class: 'like_shot' do %>
          <span class="icon"><i class="fa fa-heart"></i></span>
          <span class="vote_count"><%= shot.get_likes.size %></span> 
        <% end %>
    <% end %>
  </div>
</div> 
```

然后，为了重复`show.html.erb`文件中的功能，我们添加了以下内容:

```
 <!-- app/views/shots/show.html.erb -->
<div class="panel-block likes data">
    <% if user_signed_in? %>
    <% if current_user.liked? @shot %>
      <%= link_to unlike_shot_path(@shot), method: :put, class: "unlike_shot" do %>
        <span class="icon"><i class="fa fa-heart has-text-primary"></i></span>
        <span class="vote_count"><%= pluralize(@shot.get_likes.size, 'Like') %></span>
      <% end %>
    <% else %>
      <%= link_to like_shot_path(@shot), method: :put, class: "like_shot" do %>
        <span class="icon"><i class="fa fa-heart"></i></span>
        <span class="vote_count"><%= pluralize(@shot.get_likes.size, 'Like') %></span>
      <% end %>
    <% end %>
  <% else %>
      <%= link_to like_shot_path(@shot), method: :put, class: "like_shot" do %>
        <span class="icon"><i class="fa fa-heart"></i></span>
        <span class="vote_count"><%= pluralize(@shot.get_likes.size, 'Like') %></span>
      <% end %>
  <% end %>
</div> 
```

### 最后的触动

在主页上，我们可以添加一点营销组合，并为没有登录的人显示一个普通的英雄点。如果他们已经登录，我不想显示这个。在我们的索引视图中，最终结果如下所示:

```
 <!-- app/views/shots/index.html.erb -->

<%= render 'hero' %> <!-- render the hero partial-->

<section class="section">
  <div class="shots">
    <% @shots.each do |shot| %>
    ... 
```

在上面的部分中，我有以下内容。我们正在检查用户是否未登录。如果不是，我们就展示英雄旗帜。很简单。

```
 <% if !user_signed_in? %>
    <section class="hero is-dark">
        <div class="hero-body">
            <div class="container has-text-centered">
                <h1 class="title is-size-5">
                    What are you working on? <span class="has-text-grey-light">Dribbble is where designers get inspired and hired.</span>
                </h1>
                <div class="content">
                    <%= link_to "Login", new_user_session_path, class: "button is-primary" %>
                </div>
            </div>
        </div>
    </section>
<% end %> 
```

#### 悬停效果

在索引页面上，我想模仿 Dribbble 在他们的照片缩略图上的悬停效果。为此需要一点 jQuery。我在前面提到的`shots.js`文件中添加了下面的方法。我们最终的`shots.scss`文件也在下面供参考。

```
 // app/assets/javascripts/shot.js
...
},
shotHover() {
  $('.shot').hover(function() {
      $(this).children('.shot-data').toggleClass('visible');
    });
  }
};
Shots.previewShot();
Shots.shotHover();

}); 
```

```
 /* app/assets/stylesheets/shots.scss */
.hero.is-dark {
    background-color: #282828 !important;
}

.shot-wrapper {
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.07);
    border-radius: 2px;
    padding: 10px;
    background: white;
}

.shots {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    grid-gap: 1rem;
    @media only screen and (min-width: 1600px) {
        grid-template-columns: repeat(6, 1fr);
    }
    @media only screen and (max-width: 1300px) {
        grid-template-columns: repeat(4, 1fr);
    }
    @media only screen and (max-width: 1100px) { 
        grid-template-columns: repeat(3, 1fr);
    }
    @media only screen and (max-width: 800px) {
        grid-template-columns: 1fr 1fr;
    }
    @media only screen and (max-width: 400px) {
        grid-template-columns: 1fr;
    }

}

.shot {
    position: relative;
    display: block;
    color: #333 !important;

    .shot-data {
        display: none;
        position: absolute;
        top: 0;
        right: 0;
        bottom: 0;
        left: 0;
        padding: 10px;
        background: rgba(white, .9);
        cursor: pointer;
        .shot-title {
            font-weight: bold;
        }
        .shot-description {
            font-size: .9rem;
        }
        .shot-time {
            font-size: .8rem;
            padding-top: .5rem;
        }
    }
}

.user-data {
    padding: 1rem 0 0 0;
}

.user-name {
    display: inline-block;
    position: relative;
    top: -4px;
    padding-left: 5px;
}

.user-thumb {
    display: inline-block;
    img {
        border-radius: 50%;
    }
}

.by,
.shot-time {
    display: inline-block;
    position: relative;
    top: -4px;
}

.shot-analytics {
    text-align: right;
    @media only screen and (max-width: 800px) {
        text-align: right;
        .level-item {
            display: inline-block;
            padding: 0 4px;
        }
        .level-left+.level-right {
            margin: 0;
            padding: 0;
        }
        .level-item:not(:last-child) {
            margin: 0;
        }
    }
}

.shot-analytics,
.panel.show-shot-analytics {
    font-size: .9rem;
    a,
    .icon {
        color: #aaa;
    }
    .icon:hover,
    a:hover {
        color: darken(#aaa, 25%);
    }
}

.panel.show-shot-analytics {
    a { color: #333; }
    .icon {
        padding-right: .5rem;
    }
    .likes .vote_count {
        margin-left: -4px;
    }
}

.shot-container {
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.07);
    border-radius: 2px;
    padding: 40px;
    background: white;
    @media only screen and (max-width: 800px) {
        padding: 10px;
    }
    .content,
    .comments {
        margin-top: 1rem;
        padding: 20px;
        @media only screen and (max-width: 800px) {
            padding: 0;
        }
    }
}

.shot-full {
    text-align: center;
} 
```

### 最后的思念和感谢

这个版本是一个很大的努力，即使从外面看它可能在功能上有点轻。Dribbble 有很多活动部件，我们的克隆体只是触及了表面。我邀请你继续添加你认为合适的特性和集成。作为一名 web 开发人员，仅仅是投入进去，打破和修复一些东西，最终就能让你学习和成长。

感谢收看这个系列节目。如果你继续关注或者有反馈，请在评论中或者在 [Twitter](https://twitter.com/webcrunchblog) 上告诉我。如果你还没有，一定要看看博客的[部分，获取更多关于设计和开发的文章。](https://web-crunch.com/archive)

#### 观看/阅读完整的 Ruby on Rails 让我们构建系列:

1.  [Ruby on Rails 简介](https://web-crunch.com/lets-build-with-ruby-on-rails-introduction/)
2.  [在你的机器上安装 Ruby on Rails](https://web-crunch.com/lets-build-with-ruby-on-rails-installation/)
3.  [使用 Ruby on Rails 创建一个带有评论的基本博客](https://web-crunch.com/lets-build-with-ruby-on-rails-blog-with-comments/)
4.  [用 Ruby on Rails 构建一个 Twitter 克隆](https://web-crunch.com/lets-build-with-ruby-on-rails-a-twitter-clone/)

## 不要脸的插头

如果你喜欢这篇文章，我在 [YouTube](https://youtube.com/c/webcrunch) 和我的[博客](https://web-crunch.com)上有更多内容。我计划在这里开始创作更多的作品。希望收件箱中有更多这样的内容吗？[订阅我的简讯](https://web-crunch.com/subcribe)自动获取。

 [![](img/c009079376aa2fccf2abd73f5ab135d8.png)](https://hellorails.io) 
☝想从头开始学习 Ruby on Rails？看看我即将推出的课程 [Hello Rails](https://hellorails.io) 。