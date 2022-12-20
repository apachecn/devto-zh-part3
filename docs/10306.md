# 轨道发电机备忘单

> 原文：<https://dev.to/alicannklc/rails-generator-cheatsheet-1dfn>

备忘单很棒，但是它们不能代替学习框架和阅读文档，因为我们在这里肯定没有涵盖所有潜在的例子。更多信息请参考 [Rails 命令行文档](http://guides.rubyonrails.org/command_line.html)。

## 命令行生成器信息

[参考](https://stackoverflow.com/questions/12900170/is-there-a-reference-cheat-sheet-for-rails-generate-command)

您可以在命令行上获得所有这些信息。

将输出所有可用生成器的列表和一些关于全局选项的信息。
`rails generate GENERATOR --help`会列出可以传递给指定生成器的选项。

## Rails 生成实例

#### 创建一个资源

```
rails generate scaffold Post name:string title:string content:text 
```

#### 生成模型

```
rails generate model Post title:string body:text published:boolean 
```

#### ERB 和 API 命名空间为/api/v1 的脚手架

```
 rails g scaffold categories name:string image:string description:text status:boolean  -c=scaffold_controller
    rails g scaffold_controller api/v1/categories name:string image:string description:text status:boolean  --api --model-name=Category 
```

#### 向现有模型添加列

```
rails generate migration AddFieldToModel field:type 
```

#### 列类型

```
:primary_key, :string, :text, :integer, :float, :decimal, :datetime, :timestamp, :time, :date, :binary, :boolean 
```

#### 给字段添加唯一属性

```
rails generate scaffold Post name:string title:string content:text slug:string:uniq 
```

#### 多对多关系([引用](https://stackoverflow.com/questions/5322067/generating-a-model-with-many-to-many-in-ruby-on-rails)

请记住，您不需要连接表的 id，所以请确保添加:id => false |t|

```
create_table assemblies_parts, :id => false do |t|
  t.integer :assembly_id
  t.integer :part_id
end 
```

如果你使用 rails

```
rails generate model Assemblies_parts assembly:references part:references 
```

你会有两个索引，但是你想要的是

```
add_index :assemblies_parts, [:assembly_id, :part_id], :unique => true 
```

对于 Rails 5，请改用 create_join_table。

#### 添加修饰符([参考](http://www.chrisjmendez.com/2016/07/01/rails-cheatsheet/)

修饰符通过花括号插入，它们允许你包含诸如 null 和 limit 之类的东西。

给有限制的朋友添加年龄

```
rails g model friend age:integer{2} 
```

将价格添加到具有两位小数的产品中

```
rails g model product 'price:decimal{10,2}' 
```

将导致迁移的`scale`为`2`，而`percision`为`10`

```
class CreateProducts < ActiveRecord::Migration
  def change
    create_table :products do |t|
      ...
      t.decimal :price, precision: 10, scale: 2
      ...
      t.timestamps null: false
    end
  end
end 
```

#### 参照另一个模型创建一个新模型([参照](https://www.ralfebert.de/snippets/ruby-rails/models-tables-migrations-cheat-sheet/)

```
rails g model Supplier name:string
rails g model Product name:string:index sku:string{10}:uniq count:integer description:text supplier:references popularity:float 'price:decimal{10,2}' available:boolean availableSince:datetime image:binary 
```

结果迁移:

```
class CreateSuppliers < ActiveRecord::Migration
  def change
    create_table :suppliers do |t|
      t.string :name

      t.timestamps null: false
    end
  end
end

class CreateProducts < ActiveRecord::Migration
  def change
    create_table :products do |t|
      t.string :name
      t.string :sku, limit: 10
      t.integer :count
      t.text :description
      t.references :supplier, index: true, foreign_key: true
      t.float :popularity
      t.decimal :price, precision: 10, scale: 2
      t.boolean :available
      t.datetime :availableSince
      t.binary :image

      t.timestamps null: false
    end
    add_index :products, :name
    add_index :products, :sku, unique: true
  end
end 
```

### 多态性([参考](http://www.chrisjmendez.com/2016/07/01/rails-cheatsheet/))

假设您正在构建一个协作应用程序(如 Pivotal Tracker ),并且您想要为项目、任务和附件添加评论。你可以通过使注释多态来做到这一点。

```
rails g model Comment body:text commentable:references{polymorphic}:index 
```

### 列默认值([参考](https://stackoverflow.com/questions/6167994/assigning-default-value-while-creating-migration-file))

默认迁移生成器不处理默认值(支持列修饰符，但不包括 Default 或 null)，但是您可以创建自己的生成器。

您也可以在运行 rake db:migrate 之前，通过向 add_column 添加选项来手动更新迁移文件:

```
add_column :tweet, :retweets_count, :integer, :null => false, :default => 0 
```

...并读取 [Rails API](http://api.rubyonrails.org/)

## Rspec 发电机

[文档](https://relishapp.com/rspec/rspec-rails/docs/generators)

```
rails generate rspec:model widget 
```

将在`spec/models/widget_spec.rb`中创建新的规格文件

相同的生成器模式适用于所有规格:

```
scaffold
model
controller
helper
view
mailer
observer
integration
feature
job 
```

生成特定视图

```
rails g rspec:view widget index edit new show
      create  spec/views/widget
      create  spec/views/widget/index.html.erb_spec.rb
      create  spec/views/widget/edit.html.erb_spec.rb
      create  spec/views/widget/new.html.erb_spec.rb
      create  spec/views/widget/show.html.erb_spec.rb 
```

- [条](https://gist.github.com/cdesch/2f8de645cad1d83aa251c0a20b0f7097)