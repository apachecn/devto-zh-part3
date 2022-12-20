# 如何在 Rails 中将主键从 bigint 迁移到 UUID

> 原文：<https://dev.to/jkostolansky/how-to-migrate-primary-keys-from-bigint-to-uuid-jfn>

Rails 使用`bigint`作为 PostgreSQL 数据库的默认主键类型。这是一个可靠的选择，但是您可以考虑使用`UUID`作为主键。关于每一种方法的利弊都有无休止的争论，所以我不打算在这里重复这些争论。但是，如果您已经决定迁移到 UUIDs，这里有一个可能的解决方案。

假设我们的数据库中有两个表- `users`和`comments` :

```
create_table :users do |t|
  # ...
end

create_table :comments do |t|
  t.references :user, null: false, foreign_key: true
  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

将这些表迁移到 UUIDs 的一种可能方法是:

1.  向表中添加 UUID 列
2.  迁移关联
3.  删除 ID 列，将 UUID 重命名为 ID，并将其用作主键

这个迁移过程在下面的例子中，这是不言自明的。

```
class MigrateToUuid < ActiveRecord::Migration[5.2]
  def up
    # Add UUID columns
    add_column :users,    :uuid, :uuid, null: false, default: -> { "gen_random_uuid()" }
    add_column :comments, :uuid, :uuid, null: false, default: -> { "gen_random_uuid()" }

    # Add UUID columns for associations
    add_column :comments, :user_uuid, :uuid

    # Populate UUID columns for associations
    execute <<-SQL UPDATE comments SET user_uuid = users.uuid
      FROM users WHERE comments.user_id = users.id; SQL

    # Change null
    change_column_null :comments, :user_uuid, false    

    # Migrate UUID to ID for associations
    remove_column :comments, :user_id
    rename_column :comments, :user_uuid, :user_id

    # Add indexes for associations
    add_index :comments, :user_id    

    # Add foreign keys
    add_foreign_key :comments, :users    

    # Migrate primary keys from UUIDs to IDs
    remove_column :users,    :id
    remove_column :comments, :id
    rename_column :users,    :uuid, :id
    rename_column :comments, :uuid, :id
    execute "ALTER TABLE users    ADD PRIMARY KEY (id);"
    execute "ALTER TABLE comments ADD PRIMARY KEY (id);"

    # Add indexes for ordering by date
    add_index :users,    :created_at
    add_index :comments, :created_at
  end

  def down
    raise ActiveRecord::IrreversibleMigration
  end 
```

Enter fullscreen mode Exit fullscreen mode

`gen_random_uuid()`函数来自于`pgcrypto`扩展，所以在运行这个迁移:
之前，请确保在您的数据库中启用了它

```
CREATE EXTENSION IF NOT EXISTS pgcrypto; 
```

Enter fullscreen mode Exit fullscreen mode

您也可以很容易地编写`down`方法来使迁移可逆。

* * *

原文:[如何将主键从 bigint 迁移到 UUID](https://www.kostolansky.sk/posts/how-to-migrate-to-uuid/)