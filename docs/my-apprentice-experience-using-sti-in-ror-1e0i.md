# 我在 RoR 使用 STI 的学徒经历

> 原文：<https://dev.to/jean182/my-apprentice-experience-using-sti-in-ror-1e0i>

现在，我几乎完成了我过去三个半月一直在做的项目，我的第一个真正的 rails 应用程序和我在 Pernix 的第一个项目。这对我来说是一次很棒的经历，因为我学到了很多新东西，而且我开始觉得使用这个框架很舒服。

我在进入公司之前使用过 Rails，我做过“hello world”Rails 项目(即**博客**)，也就是每个人在学习框架时都会做的项目。虽然我不完全确定我当时正在做的概念和东西，但这样做很棒，因为当我开始这个项目时，至少我对框架如何工作有了一个想法。尽管如此，ORM 的许多东西对我来说是一件大事，我不知道如何使用一些活动记录关联。

当我们开始建模过程时，我注意到我们将在模型之间使用大量的关联，我有点害怕如何让这些关联与 Rails 一起工作。

我负责创建用户模型，当然我使用了**design**，但是用户不是一个简单的普通用户，每个用户都需要有一个角色，我过去常常通过创建一个名为 *role* 的列来完成，它使用活动记录 Enum(你可以在 design[文档](https://github.com/plataformatec/devise/wiki/How-To:-Add-an-Admin-Role#option-3-using-active-record-enum)中找到)。虽然这种方法很好，但在这个项目中它不是最好的，因为每个角色都有自定义信息，但是制作不同的模型也不是一种好的方法，因为它们共享很多东西。

起初我使用了多态关联。

```
class User < ApplicationRecord
  belongs_to :account, polymorphic: true, dependent: :destroy
end

class Admin < ApplicationRecord
  has_one :user, as: :account
end

class Mentor < ApplicationRecord
  has_one :user, as: :account
end

class Client < ApplicationRecord
  has_one :user, as: :account
end 
```

Enter fullscreen mode Exit fullscreen mode

为了实现这一点，你只需要在用户表 **account_id** 和 **account_type** 下添加两个新列，rails 约定告诉你可以随意命名这些列，但是它们应该以 **able** 结尾。在我的例子中，我避免使用 **accountable** 而不是 account，因为 accountable 与在这个项目中使用它的上下文无关。

如果你想知道一个用户的角色，你只需要访问:

```
user.account 
```

Enter fullscreen mode Exit fullscreen mode

这将为您提供相关的表行，在本例中可能是“管理客户”或“导师”。这样，我可以共享用户列，并在其他模型中添加自定义角色列。

例如，如果您想要检索 Admin 角色的用户列，您需要使用:

```
admin.user 
```

Enter fullscreen mode Exit fullscreen mode

然后一个新的项目领导来了，他对我所有的角色管理代码进行了重构，并切换到了 **STI** 。这让我有点泄气，因为我对我的多态方法感到非常满意，但最终 STI 被证明是最好的方法，看起来也更好。

STI 与多态关联的主要区别在于，所有的角色都存储在一个表中，所有的区别都在一个名为 type 的列中。请注意，在多态关联中，所有的模型都继承自应用程序记录。STI 协会就不是这样了。

```
class User < ApplicationRecord

end

class Admin < User

end

class Mentor < User

end

class Client < User

end 
```

Enter fullscreen mode Exit fullscreen mode

正如您在我的例子中看到的，角色继承自用户而不是应用程序记录，所以基本上如果您调用用户

```
user 
```

Enter fullscreen mode Exit fullscreen mode

它将显示带有角色的用户对象，您也不需要调用关联。这适用于调用角色本身。

```
admin 
```

Enter fullscreen mode Exit fullscreen mode

STI 的唯一问题是，如果你把所有东西都存储在一个表中，那么这个表将会被很多列填充。为了避免这个问题，这个项目使用了与另一个表的 has_one 关联，该表将存储自定义角色列。例如，客户端模型与一个名为 ClientInfo
的模型相关联

```
class Client < User
 has_one :client_info, dependent: :destroy
end

class ClientInfo < ApplicationRecord
 belongs_to :client, dependent: :destroy
end 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式，我们可以避免填充用户表，并且我们可以在模型之间存在差异，比如相互之间不共享的自定义关联或自定义字段。

对我来说，理解关联对于 rails 或任何使用 ORM 的框架都是非常重要的，因为如果你不知道如何正确使用数据，操作数据会非常棘手。

(这是我发布在 loserkid.io 博客上的一篇文章，你可以通过[点击这里](https://www.loserkid.io/my-apprentice-experience-using-sti-rails/)在线阅读。)