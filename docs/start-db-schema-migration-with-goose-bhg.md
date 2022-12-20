# 用“goose”启动数据库模式迁移

> 原文：<https://dev.to/msh5/start-db-schema-migration-with-goose-bhg>

*这是分配给“ [LOB 降临日历](https://qiita.com/advent-calendar/2018/lob-inc)的帖子“12 天，玩得开心！*

今天我来分享一个小工具“鹅”。这是一个模式迁移工具，它将模式迁移的管理引入到项目中。说实话，在我了解这个工具之前，我并不熟悉模式迁移。在这篇文章中，我将解释“goose”是什么工具，并为像我这样的新手指出什么是模式迁移。

## 什么是“模式迁移”？

世界上有许多网上服务，如 web、消息传递、金融、广告等等，其中许多都依赖于数据库系统。此外，它们中的许多都面临着在服务生命周期中更改模式的需求，例如创建一个表、修改表以添加/删除列等等。专门为在线数据库改变模式的称为“[模式迁移](https://en.wikipedia.org/wiki/Schema_migration)”。此外，不仅如此，这意味着管理这样的变更以保持可追溯性。 *rails migrate* 是非常著名的提供模式迁移的工具。但它是嵌入在 Rails 框架中的命令，所以非 Rails 项目将欢迎更具可移植性的工具。

## “鹅”是什么？

Goose 是模式迁移工具之一，它很小，但很常用。它是用 Go 编写的，你可以用“go get”或者自带预建的二进制来安装。

```
$ go get -u github.com/pressly/goose/cmd/goose 
```

对了，鹅肉有两个变种。下面两个，第二个是原创，第一个是从原创分叉出来的。我今天介绍的工具是第一个，因为我认为分叉的工具在添加了一些功能方面更胜一筹。

*   [github.com/pressly/goose](https://github.com/pressly/goose)(叉自原著)
*   [bitbucket.org/liamstask/goose](https://bitbucket.org/liamstask/goose)(原创)

## 演示

首先，我们需要创建一个迁移文件。我们可以用`goose create` :
来生成

```
goose DRIVER DBSTRING create NAME TYPE

- DRIVER: A name of driver to query db.
- DBSTRING: A string passed to the driver as parameter.
- NAME: A name of the generated migration file.
- TYPE: "sql" or "go" as the type whose the generated migration file. 
```

并且，您可以如下使用:

```
$ goose sqlite3 ~/database.db create 00001_create_first_table sql
2018/12/11 18:55:01 Created new file: 20181211185501_00001_create_first_table.sql 
```

这意味着该工具创建了新的迁移文件“ <timestamp>_00001_create_first_table”，我们在其中编写了更改位于“~/database.db”上的 sqlite3 数据库的模式的步骤。我们需要更新迁移文件。迁移文件生成如下:</timestamp> 

```
-- +goose Up
-- SQL in this section is executed when the migration is applied.

-- +goose Down
-- SQL in this section is executed when the migration is rolled back. 
```

迁移文件有两个部分。第一个是“+goose Up”，我们可以在这里编写迁移步骤。第二个是“+goose Down”，我们可以在这个区域编写步骤来反转“+goose Up”步骤的变化。最主要的例子如下:

```
-- +goose Up
CREATE TABLE post (
    id int NOT NULL,
    title text,
    body text,
    PRIMARY KEY(id)
);

-- +goose Down
DROP TABLE post; 
```

我们甚至可以在 Go 中编写迁移步骤，而不仅仅是 SQL。但是如果使用 Go，*我们需要定制“goose”二进制文件*来构建迁移步骤 Go 源代码。所以需要一点努力。

```
package main

import (
    "database/sql"

    "github.com/pressly/goose"
)

func init() {
    goose.AddMigration(Up00002, Down00002)
}

func Up00002(tx *sql.Tx) error {
    _, err := tx.Exec("UPDATE users SET username='admin' WHERE username='root';")
    if err != nil {
        return err
    }
    return nil
}

func Down00002(tx *sql.Tx) error {
    _, err := tx.Exec("UPDATE users SET username='root' WHERE username='admin';")
    if err != nil {
        return err
    }
    return nil
} 
```

注意，我们每次更改 schema 时都会创建迁移文件，建议放在同一个目录下。你认为那么多迁移文件是累积的吗？没错，这是非常令人期待的，因为许多迁移文件只是代表迁移历史。因此，您可以用最新的模式和指定点的模式来设置数据库。

```
$ ls migrations
20181211185501_00001_create_tables.sql
20190506082527_00002_add_columns_for_new_feature.sql
20191020225109_00003_alter_field_type_for_bugfix.sql 
```

实际上，要应用写入迁移文件的数据库更改，可以使用`goose up`命令:

```
// apply changes up to latest
goose up

// apply changes up to the specified point
goose up-to 20181211102811 
```

并且您总是使用`goose down`命令恢复模式更改，除非每个文件上的“goose Down”步骤工作正常:

```
// revert previous change
goose down

// revert changes up to the specified point
goose down-to 20181211102811 
```

## 结论

*   Goose 是一个模式迁移工具。
*   Goose 改变数据库的模式并保持其可追踪性。
*   Goose 是可移植的，不依赖于项目的框架。
*   我们甚至可以在围棋中书写，但这需要一点努力。

最后，这是我在 dev.to 上的第一篇帖子，也是我用英文写的第一篇帖子。你可能会在这篇文章中发现奇怪的表达，但是请原谅我，如果可以的话，请指出来给我学习英语；)

***我们正在招聘:*** 我们是“LOB，inc”，乐天集团旗下的日本广告科技初创公司，正在打造新的全球广告平台！即使你不会说日语，也不需要犹豫。在乐天集团，通用语言是英语，许多外国工程师在工作。如果您感兴趣，请联系我或申请以下招聘页面中的条目。

*   LOB 公司:[https://lob-inc.com/recruit/](https://lob-inc.com/recruit/)
*   关于乐天集团:[https://global.rakuten.com/corp/](https://global.rakuten.com/corp/)