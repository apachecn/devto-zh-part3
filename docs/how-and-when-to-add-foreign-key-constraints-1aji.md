# 如何以及何时添加外键约束

> 原文：<https://dev.to/kevincolemaninc/how-and-when-to-add-foreign-key-constraints-1aji>

许多 rails 项目依赖应用程序验证来确保数据完整性。使用 rails `presence`验证，您可以要求存在关联，以便保存对象。这种方法一直有效，直到失效。

## 没有外键约束

如果开发人员忘记定义 rails 验证的依赖选项(如`has_many :users, dependent: :nullify`)，使用`#delete`代替`#destroy`，甚至通过查询手动删除，关联的行将指向空记录。这并不理想，因为现在您不能通过查看 id 是否存在来可靠地测试关联是否存在。

```
# good
puts "company exists!" if user.company_id

# bad - N+1 to load the company
puts "company exists!" if user.company.id 
```

如果公司被删除，但是用户没有随公司一起被删除，您可能会意外地使您的模型失效，从而阻止您保存任何属性更改！

```
class User < ApplicationRecord
  belongs_to :company, required: true
end
user.company.delete
user.update name: 'kevin' # false - company is missing 
```

瓦特。公司不在了我就不能保存名字了？

## 添加外键约束

在 Rails 5 中，添加外键约束是为了让数据库保护相关数据的完整性。一旦定义了外键约束，您的数据库将不允许您删除其他表所需的记录。

```
add_foreign_key :users, :companies 
```

[![logo of the yeet_dba gem](img/0dca49ea5e246512abe52d9805752c5b.png "logo of the yeet_dba gem")](https://res.cloudinary.com/practicaldev/image/fetch/s--ymB1j8kt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/KevinColemanInc/yeet_dba/master/yeet_dba.png)

向整个 rails 模式添加外键约束的一个快速方法是使用 [yeet_dba](https://github.com/kevincolemaninc/yeet_dba) gem。`yeet_dba`包括 rake 任务和生成器，它们扫描整个数据库，搜索缺少外键约束的列。如果数据是有效的，它可以添加外键约束来加速您的连接和 where 查询，或者如果数据是无效的，它将帮助您解决问题。

通过向数据库添加外键约束，可以减少 N+1 调用，提高连接和 where 查询性能，并防止因缺少关联而导致意外失败。

### 进一步阅读

*   lol_dba -这个 gem 帮助找到丢失的索引，但不是外键。
*   [Postgres 外键指南](http://www.postgresqltutorial.com/postgresql-foreign-key/)
*   [Rails 外键约束](https://www.kcoleman.me/2019/03/14/how-to-add-foreign-key-constraints-rails.html)