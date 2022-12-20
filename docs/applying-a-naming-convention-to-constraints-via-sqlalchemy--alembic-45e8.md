# 通过 SQLAlchemy & Alembic 对约束应用命名约定

> 原文：<https://dev.to/jdheywood/applying-a-naming-convention-to-constraints-via-sqlalchemy--alembic-45e8>

# 顶尖提示！

因此，我们正在创建一个新的数据库模式，使用 SQLAlchemy 进行 ORM，使用 Alembic 进行模式管理，(如果您使用 Python 并且需要管理数据库结构，我强烈推荐这两个工具)，总之，我们在降级试图删除未命名外键的迁移时遇到了问题。

## Wat?!

为什么它会允许我创建一个不能删除的约束，为什么？问得好，结果是由你(在这种情况下是我)来命名事物。

很明显，我们不希望每次生成一个包含约束的迁移时都必须记住手动完成这项工作，那将是乏味的并且容易出错(我肯定会忘记这样做)，那么我们该怎么办呢？

好了，Alembic 已经涵盖了你，当声明你的基本模型类时，你可以设置一个应用于所有迁移的命名约定(在我看来，这应该是默认的行为，但我不是讨厌，而是喜欢你 Alembic/SQLAlchemy)。

## 开门见山，告诉我怎么回事

嗯像这样；

```
from sqlalchemy import MetaData
from sqlalchemy.ext.declarative import declarative_base

meta = MetaData(
    naming_convention={
        "ix": "ix_%(column_0_label)s",
        "uq": "uq_%(table_name)s_%(column_0_name)s",
        "ck": "ck_%(table_name)s_%(constraint_name)s",
        "fk": "fk_%(table_name)s_%(column_0_name)s_%(referred_table_name)s",
        "pk": "pk_%(table_name)s"
    }
)

Base = declarative_base(metadata=meta) 
```

[供参考的原始 alembic 文档在此](https://alembic.sqlalchemy.org/en/latest/naming.html)谢谢 alembic peeps！

所以现在你只需在你的 alembic env.py 中使用基类，并在你的所有模型类中继承基类，你就可以开始了

你会看到像这样的自动生成的迁移；

```
 ...
    sa.PrimaryKeyConstraint('id', name=op.f('pk_thing'))
    ...
    sa.ForeignKeyConstraint(['wotsit_id'], name=op.f('fk_thing_wotsit_id_wotsit')),
    ... 
```

尽情享受吧！