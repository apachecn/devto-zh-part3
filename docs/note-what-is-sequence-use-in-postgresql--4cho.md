# 注意 Postgresql 中使用序列是什么

> 原文：<https://dev.to/udomsak/note-what-is-sequence-use-in-postgresql--4cho>

序列是一种特殊的数据库对象，用于生成唯一的数字标识符。它通常用于生成人工主键。序列与 MySQL 中的 AUTO_INCREMENT 概念相似，但不完全相同。

## Thai

Sequence 是 PostgreSQL 中的数据库对象，通常用于生成密钥(用于主密钥)。Sequence 表示它与 mye 中的自动引用类似。

没问题，关键是碰撞，因为序列已经是为此设计的

链接:[http://www.neilconway.org/docs/sequences/](http://www.neilconway.org/docs/sequences/)