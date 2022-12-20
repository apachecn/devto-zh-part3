# sequal:active record 的替代方案

> 原文：<https://dev.to/torianne02/sequel-an-alternative-to-activerecord-5d6l>

周五，我接受了一次带回家的编码评估。我被要求用 Ruby 构建一个 RESTful API，但有一个大问题:没有 Rails。从这里可以学到一些东西-

1.  我非常兴奋能得到这个机会，因为这是我求职的第二个编码挑战。熟能生巧，所以我得到的越多，接下来的时间我就会越自信和舒服。

2.  我获得了用工具/框架构建的宝贵经验，我不知道我是否已经存在和/或使用过。其中一个工具是 Sequel，这也是我今天要写的。

对于我的挑战，我被要求使用 Sequel 而不是 ActiveRecord，这是一个挑战。我对续集的看法仍然悬而未决。我的第一反应是想知道为什么有人会用这个宝石而不是 ActiveRecord。在使用它的时候，我真的很喜欢其中一个方面，它让我的观点变得温和了一些。等我写完这篇博文，我们就能看到它是什么了。

## 什么是续作？

Sequel 是一个 Ruby gem，其功能相当于一个“简单、灵活、强大的 SQL 数据库访问工具包”换句话说，它可以与广泛使用的 ActiveRecord gem 相媲美。Sequel 允许您像 ActiveRecord 一样运行迁移、查询和操作数据库。

### Sequel 支持的数据库类型

Sequel 目前支持以下数据库类型:ADO、Amalgalite、IBM_DB、JDBC、MySQL、Mysql2、ODBC、Oracle、PostreSQL、SQLAnywhere、SQLite3 和 TinyTDS。

## 安装续集

为了使用 Sequel，您必须首先将 gem 安装到您的项目中。为此，请转到您的 gem 文件并添加`gem ‘sequel’`。您需要在您的终端中运行`$ bundle install`来安装 gem。

在您的`application.rb`文件中，您将需要`require ‘sequel’`，就像您使用 ActiveRecord 一样。

## 创建数据库

我发现 Sequel 的一个独特之处(当然我发现了一个错误)是，在尝试连接 Sequel 到数据库之前，必须首先创建数据库。

要创建数据库，运行以下命令(使用您自己的数据库名称)- `$ createdb pets_development`。

*注意*:出于本教程的目的，我使用 PostgresSQL。与 SQLite3 相比，数据库的创建有点独特，所以请记住这一点，如果您使用不同类型的数据库，请[访问本页](http://tutorials.jumpstartlab.com/topics/sql/sequel.html)。

## 创建迁移文件

创建迁移文件与布局中的 ActiveRecord 非常相似，但是，没有像`rake db:create`这样简单的命令可以运行。我手动创建了我的迁移文件，但是需要注意的是，您仍然应该在文件名的开头添加时间戳，因为 Sequel 需要这样做。

```
# 20190514_dogs.rb
Sequel.migration do
  change do
    create_table(:dogs) do
      primary_key :id
      String :name, :null => false
      String :breed, :null => false
      foreign_key :owner_id, :people
    end
  end 
end 

# 20190514_owners.rb
Sequel.migration do
  change do
    create_table(:owners) do
      primary_key :id
      String :name, :null => false
    end
  end 
end 
```

## 连接数据库

现在我们已经创建了数据库和迁移文件，我们需要将 Sequel 连接到数据库。为此，您需要导航到您的`application.rb`文件并包含以下代码:

```
# application.rb

# establishes Sequel connection to db
DB = Sequel.connect(adapter: :postgres, database: ‘pets_development', host: 'localhost') 
```

*注*:连接数据库有多种方式，这只是我喜欢的方式。这篇[文章](https://sequel.jeremyevans.net/rdoc/files/README_rdoc.html)包含其他选项。

*另一个注意事项*:你**在创建你的模型后**无法连接到数据库，所以请确保在创建你的模型前完成这个连接步骤**(我是吃了苦头才知道的)。**

## 创建模型

Sequel 中的模型设置与 ActiveRecord 非常相似。

```
# dog.rb 
class Dog < Sequel::Model
  many_to_one :person

  def validate
    super
    errors.add(:name, "must be present") if name.empty?
    errors.add(:breed, "must be present") if breed.empty?
  end
end

# owner.rb 
class Owner < Sequel::Model
  one_to_many :dogs

  def validate
    super
    errors.add(:name, "must be present") if name.empty?
  end
end 
```

## 运行迁移

现在是设置数据库、运行迁移的最后一步。不幸的是，运行我们的迁移并不像使用 ActiveRecord 那样简单。幸运的是，我们仍然有这样做的命令。运行`$ sequel -m db/migrate/ postgres://localhost/pets_development`。

*注意*:db/migrate/’是我的迁移文件的路径，请用您自己的路径替换它。

## 播种数据库

我为这个项目创建了种子文件，所以我将解释如何设置它。我们需要做的第一件事是安装一个有用的宝石，`Sequel-Seed`。进入你的 gem 文件，添加`require ‘sequel-seed’`，然后运行`$ bundle install`。

接下来，我们需要创建种子文件。种子文件还需要在名称的开头有一个时间戳。

```
# 20190514_dogs_seed.rb 
Sequel.seed do
  def run
    [
      [‘buddy’, ‘labrador’, 1],
      [‘spot’, ‘golden retriever’, 1],
      [‘princess’, ‘beagle’, 2]
    ].each do |name, breed, owner_id|
      Dog.create name: name, breed: breed, owner_id: owner_id
    end
  end
end 

# 20190514_owners_seed.rb
Sequel.seed do
  def run
    [
      [‘angie’],
      [‘bill’],
    ].each do |name|
      Dog.create name: name
    end
  end
end 
```

转到您的`application.rb`文件并插入以下代码，以加载种子扩展并播种数据库:

```
# application.rb
require ‘sequel/extensions/seed’

# loads the extension
Sequel.extension :seed 

# seeds the database
Sequel::Seeder.apply(DB, “db/seeds/“) 
```

## 查询

Sequel 中的查询有点类似于 ActiveRecord 中的查询。使用`Dog.all`将返回数据库中的所有狗。您可以使用`.first`和`.last`来获取数据库中一个对象的第一个和最后一个实例。`.order`允许您在数据库内订购实例。您可以使用`.where()`在数据库中查找匹配给定对象键的所有实例。你可以在这里查看更多这些。

### 原始 SQL 查询

Sequel 的一个真正的优点是，您可以对数据库进行原始 SQL 查询。所以，你可以这样做:

```
DB['select * from owners'].each do |row|
  p row
end 
```

## 最后的想法

我非常喜欢使用原始 SQL 的功能。我真的很喜欢 SQL 查询，我总是利用这个机会来练习我的 SQL 技能。Sequel 的这方面真的改变了我对它的看法。它与 ActiveRecord 并没有什么不同，但我仍然更喜欢使用 ActiveRecord。我的理由是因为`rake db`命令。他们让生活变得更加容易。

不过，我确实认为 Sequel 是 ActiveRecord 的一个很好的替代工具。我希望这篇文章已经激发了你的好奇心，也许激起了你尝试续集的兴趣。

编码快乐！

#### 来源

[续作:Ruby](https://sequel.jeremyevans.net/)
[数据库工具包入门续作](http://tutorials.jumpstartlab.com/topics/sql/sequel.html)