# 在 Rails 和 Postgres 中将 YAML 列数据转换为 JSONB

> 原文：<https://dev.to/edwinthinks/convert-yaml-column-data-to-jsonb-in-rails-and-postgres-4mko>

## 简介

我的团队希望利用 Postgres 9.4+提供的极其有用的 [JSON 特性](https://www.postgresql.org/docs/9.4/functions-json.html)，来处理我们的 rails 应用程序以 YAML 的形式存储在文本列中的数据。我们安装了一个非常有用的 gem，叫做 [audited](https://github.com/collectiveidea/audited) ，它负责跟踪我们的 ActiveRecord 记录中“何时”&“发生了什么”变化。不幸的是,“是什么”数据以 YAML 的形式存储在一个文本列中，这使得查询这些数据变得更加困难。下面是“什么”数据的一个例子:

```
---
pilot:
- false
- true 
```

Enter fullscreen mode Exit fullscreen mode

这就是我想要的样子:

```
{  "pilot":  [false,  true]  } 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，被审计的 gem 的作者包含了安装它的选项，这样数据就存储在 JSONB 中(默认为 YAML)。

最近，我们对构建更高级的查询感兴趣，以便根据“什么”数据的内容进行搜索。因此，我们的目标是创建一个迁移，可以安全地将数据从 YAML 来回转换到 JSONB(最好是可逆的)。

## 结果

下面是我们最终生成的一个迁移文件，它将`audits`表上的`audited_changes`列(即“什么”数据)从 YAML 转换为 JSONB。您还可以利用这段代码将您的 YAML 列转换成 JSONB。

#### 免责声明——迁移风险自担！

```
class ChangeAuditedChangesFromYamlToJson < ActiveRecord::Migration
  def up
    updated_records = fetch_audit_records.map do |r|
      {
        id: r['id'],
        audited_changes: escape_sql(JSON.dump(YAML.safe_load(r['audited_changes'])))
      }
    end

    remove_column :audits, :audited_changes
    add_column :audits, :audited_changes, :jsonb

    Transaction.transaction do
      updated_records.each do |ur|
        ActiveRecord::Base.connection.execute(
          """
            UPDATE audits 
            SET audited_changes = '#{ur[:audited_changes]}'::jsonb 
            WHERE id = #{ur[:id]} """
        )
      end
    end
  end

  def down
    updated_records = fetch_audit_records.map do |r|
      {
        id: r['id'],
        audited_changes: escape_sql(YAML.dump(JSON.parse(r['audited_changes'])))
      }
    end

    remove_column :audits, :audited_changes
    add_column :audits, :audited_changes, :text

    Transaction.transaction do
      updated_records.each do |ur|
        ActiveRecord::Base.connection.execute(
          """
            UPDATE audits 
            SET audited_changes = '#{ur[:audited_changes]}'::text 
            WHERE id = #{ur[:id]} """
        )
      end
    end
  end

  private

  #
  # Returns the id & audit_changes column of the audits table.
  # @return [PG::Result]
  def fetch_audit_records
    ActiveRecord::Base.connection.execute('SELECT id, audited_changes FROM audits')
  end

  #
  # Returns the SQL escaped version of the string provided 
  # For example, a "'" needs to be escaped by doubling it.
  # @param [String] the string to be converted
  # @return [String] the escaped version of the string provided
  def escape_sql(string)
    return string.gsub("'","''")
  end

end 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我们进行了一些反复试验，并浏览了一些“疑难杂症”,从而实现了最终的迁移。例如，我们必须通过在每个实例旁边添加另一个单引号来转义单引号字符(参见代码片段中的`escape_sql`方法)。尽管如此，我们对结果还是很满意，并开始用 Postgres 给我们的超级棒的 JSON 特性构建查询。

希望这对别人有帮助=)。编码快乐！