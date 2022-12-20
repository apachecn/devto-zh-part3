# 一种更简单的方法来适应活动存储附件的删除

> 原文：<https://dev.to/kylekeesling/an-easier-way-to-accommodate-deletion-of-activestorage-attachments-2ndn>

## 有点背景

随着我继续逐步淘汰[回形针](https://github.com/thoughtbot/paperclip)以支持[动态存储](https://edgeguides.rubyonrails.org/active_storage_overview.html)，我希望我用来管理这些资产的方法尽可能简洁和可重用。

ActiveStorage 为我们提供了一种非常简单的方式来保存和更新资产，但是独立于父记录删除资产的能力，尤其是如果您使用的是`has_many_attached`，已经留给了每个应用程序来解决。

我偶然发现了几个关于如何潜在地做到这一点的不同观点，但是我对他们的方法并不满意。许多没有考虑授权和许可，如果你不小心，允许任何用户通过随机点击你的应用程序中的 URL 来删除他们选择的任何附件。考虑到这一点，我开始尝试推出自己的产品。

## 走近

由于所有附件都保存为相同的对象/模型类型，即[附件](https://api.rubyonrails.org/classes/ActiveStorage/Attachment.html)和[斑点](https://api.rubyonrails.org/classes/ActiveStorage/Blob.html)，我们可以使用一个通用的控制器来与它们进行交互和修改，而不管它属于哪个父记录。

在我的例子中，我选择严格遵守已经给我们的命名约定，所以我创建了一个名为`ActiveStorage::AttachmentsController`的新控制器，因为现在我只担心删除附件，所以我们只需要一个方法`delete`。

我使用[设计](https://github.com/plataformatec/devise)进行认证，所以我们需要确保用户在我们让他们做任何事情之前已经登录，这是我们用`:authenticate_user!`做的。

我还想确保用户有修改这些附件的权限，所以我检查他们的权限，看他们是否可以使用`authorize_attachment_parent!`方法修改父记录。在这种情况下，我使用 [CanCanCan](https://github.com/CanCanCommunity/cancancan) ,但是您可以随时替换您的 auth 调用以适应您使用的任何方法。这部分对于确保你的用户不会删除任何他们不应该删除的东西是非常关键的。

这个`purge_later`调用将负责后台队列中的实际文件删除，并将删除相应的`Blob`记录。

```
# app/controllers/active_storage/attachments_controller.rb
    class ActiveStorage::AttachmentsController < ApplicationController
      before_action :authenticate_user!
      before_action :set_attachment, :authorize_attachment_parent!

      def destroy
        @attachment.purge_later
      end

      private

        def set_attachment
          @attachment = ActiveStorage::Attachment.find(params[:id])
          @record = @attachment.record
        end

        def authorize_attachment_parent!
          authorize! :manage, @record
        end
    end 
```

Enter fullscreen mode Exit fullscreen mode

另外，请务必在您的路线中连接这个新控制器:

```
# config/routes.rb
    scope :active_storage, module: :active_storage, as: :active_storage do
      resources :attachments, only: [:destroy]
    end 
```

Enter fullscreen mode Exit fullscreen mode

更新用户界面以删除任何对附件的引用也很重要，在本例中，我使用了 Rails UJS。从我的 UI 中删除元素所需的 javascript 简单明了:

```
// app/views/active_storage/attachments/destroy.js.erb
    document.getElementById("<%= dom_id @attachment %>").remove() 
```

Enter fullscreen mode Exit fullscreen mode

这里唯一的假设是您将附件的表示包装在一个 ID 为`attachment_#{id}`的 div 中，您可以使用 Rails 便利的`dom_id`方法轻松呈现它。

现在，您可以在所有附件旁边使用以下标记，以便于删除:

```
link_to "Delete", active_storage_attachment_path(attachment),
             method: :delete, remote: :true,
             data: { confirm: "Are you sure you wanna this?" } 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

这种方法相对简单，并且非常依赖 Rails 提供给我们的工具和特性。

在我的例子中，我还利用了一个公共的 partial 来呈现缩略图、元数据和附件的链接，使得将来向我选择的任何模型添加附件变得非常简单。

一如既往，事情会随着时间的推移而发展，虽然我不认为我是完美的，但我肯定有办法改进这段代码，所以如果你有任何建议或改进[我很乐意听到它们](https://twitter.com/kylekeesling)。