# 了解活动记录关联

> 原文：<https://dev.to/justalever/understanding-active-record-associations-13em>

在 Ruby on Rails 中，关联模型非常简单，但是在使用框架构建渐进式 web 应用程序时，您需要熟悉一些约定。这篇文章试图让你理解 Ruby on Rails 框架中的活动记录关联。

请将此演练视为您可能在 Rails 应用程序中使用的关联的快速概述。这些关联是一种便捷的方式，可以让数据一直向下关联到数据库层。每种关系都发生在 rails 应用程序的模型层中。如果需要的话，你可以根据自己的应用需求进一步扩展关联。

[https://www.youtube.com/embed/5mhuNSkV_vQ](https://www.youtube.com/embed/5mhuNSkV_vQ)

### 属于 _ 属于

```
# belongs_to

# app/models/book.rb
class Book < ApplicationRecord
  belongs_to :author

  # must be singular
  # Sets up a 1:1 connection with another model - Author in this case
  # Books associated to author via `author_id` on books table
end 
```

Enter fullscreen mode Exit fullscreen mode

### 有 _ 多

```
# has_many

# app/models/author.rb
class Author < ApplicationRecord
  has_many :books

  # naming is plural
  # indicates a one-to-many association
  # an author can have many books in this example
  # books associated via `author_id`
end 
```

Enter fullscreen mode Exit fullscreen mode

### 有 _ 个

```
# has_one

class Supplier < ApplicationRecord
  has_one :account

  # only contains one instance of another model
  # Supplier will only ever have a single account in this example
  # Account associate via `supplier_id`
end 
```

Enter fullscreen mode Exit fullscreen mode

### 已 _ 多历

```
# has_many :through

# sets up a many-to-many association using another model

# app/models/physician.rb
class Physician < ApplicationRecord
  has_many :appointments
  has_many :patients, through: :appointments
end

# app/models/appoinment.rb
# the appointments table would have both a `physician_id` and `patient_id` column
class Appointment < ApplicationRecord
  belongs_to :physician
  belongs_to :patient
end

# app/models/patient.rb
class Patient < ApplicationRecord
  has_many :appointments
  has_many :physicians, through: :appointments
end 
```

Enter fullscreen mode Exit fullscreen mode

### 已经 _ 一个接一个

```
# has one through

# sets up a one-to-one connection with another model but can proceed through a third model

# app/models/supplier.rb

class Supplier < ApplicationRecord
  has_one :account
  has_one :account_history, through: :account
end

# app/models/account.rb

# suppliers reference `supplier_id`
class Account < ApplicationRecord
  belongs_to :supplier
  has_one :account_history
end

# app/models/account_history.rb

# account_histores referenced by `account_id`
class AccountHistory < ApplicationRecord
  belongs_to :account
end 
```

Enter fullscreen mode Exit fullscreen mode

### 拥有并属于许多

```
# Has and Belongs to many (has_and_belongs_to_many - HABTM)

# A direct many-to-many connection with another model without a third intervening model. You'll need to create a simple join table to use this type of association. It's limiting in terms of advanced querying so use with caution.

# The naming convention of the join table would take the name of both models and become plural so

# assemblies_parts is the new table name which contains an `assembly_id` and `part_id` only.

# app/models/assembly.rb
class Assembly < ApplicationRecord
  has_and_belongs_to_many :parts
end

# app/models/part.rb
class Part < ApplicationRecord
  has_and_belongs_to_many :assemblies
end 
```

Enter fullscreen mode Exit fullscreen mode

### 多态

```
# polymorphic
# With polymorphic associations, a model can belong to more than one other model, on a single association.

# app/models/picture.rb
# pictures table has both `imageable_id`, and `imageable_type`
class Picture < ApplicationRecord
  belongs_to :imageable, polymorphic: true
end

# app/models/employee.rb
class Employee < ApplicationRecord
  has_many :pictures, as: :imageable
end

# app/models/product.rb
class Product < ApplicationRecord
  has_many :pictures, as: :imageable
end 
```

Enter fullscreen mode Exit fullscreen mode

### 无耻的塞时间

[![](img/f572d5df905b6366d1201f6c51d1b8c2.png)](http://hellorails.io)

我有一门新课程叫做 [Hello Rails](https://hellorails.io) 。Hello Rails 是一门现代课程，旨在帮助您快速开始使用和理解 Ruby on Rails。如果你是 Ruby 或 Ruby on Rails 的新手，我邀请你去看看这个网站。该课程将与这些构建非常相似，但是是一个超级深入的版本，具有更现实的目标和可交付成果。[报名今天得到通知](https://hellorails.io/)！

在推特上关注 [@hello_rails](https://twitter.com/hello_rails) 和[本人@justalever](https://twitter.com/justalever) 。

帖子[了解活动记录关联](https://web-crunch.com/understanding-active-record-associations/)最早出现在[的 Web-Crunch](https://web-crunch.com) 上。