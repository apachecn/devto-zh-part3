# schema-DTS:Schema.org 链接数据类型和代码生成

> 原文：<https://dev.to/eyassh/schema-dts-schemaorg-linked-data-types--code-generation-544p>

Schema-DTS 是 Google 的一个开源项目，它使得用 Schema.org 词汇表编写 JSON-LD 文字成为可能。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [谷歌](https://github.com/google) / [ schema-dts](https://github.com/google/schema-dts)

### Schema.org 词汇的 JSON-LD 打字稿类型

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Build Status](img/17a95e29e5eee9c758fc5a34638ad546.png)](https://travis-ci.org/google/schema-dts)[![Coverage Status](img/2c54c1aef1fc4984165ffa6f62966eb1.png)](https://coveralls.io/github/google/schema-dts?branch=master)[![schema-dts npm version](img/b5c1e240c6dbb53ec8781371a68a96e7.png)](https://www.npmjs.com/package/schema-dts)[![schema-dts-gen version](img/734259f2ef437caa71e2cc465cff9d81.png)T11】](https://www.npmjs.com/package/schema-dts-gen)

# 架构-dts

Schema.org 词汇的 JSON-LD 类型。

**schema-dts** 为 JSON-LD 格式的【Schema.org】词汇[提供了类型脚本定义。类型化被公开为完全不同的类型联合，允许容易的完成和更严格的验证。](https://schema.org/)

[![Example of Code Completion using schema-dts](img/6a2b5f13dd12e91569a9ae51ea505b15.png)](https://raw.githubusercontent.com/google/schema-dts/master/./example-1.gif)

该存储库包含两个 NPM 软件包:

*   **[schema-dts-gen](https://www.npmjs.com/package/schema-dts-gen)** 提供命令行工具，基于特定的模式版本和层生成 TypeScript 文件。
*   **[schema-dts](https://www.npmjs.com/package/schema-dts)** 最新 Schema.org schema 的预打包打字稿打字，没有[待定](https://pending.schema.org/)等非核心层。

注意:这不是官方支持的谷歌产品。

## 使用

要为您的项目使用这些类型，只需将 [`schema-dts`](https://www.npmjs.com/package/schema-dts) NPM 包添加到您的项目中:

```
npm install schema-dts 
```

然后导入`"schema-dts"`就可以用了。

### 根上下文

你通常希望你的顶层项目包含一个`@context`，比如`https://schema.org`。为了让你的对象类型接受这个属性，你可以用`WithContext`增加它，例如:

```
import { Person, WithContext
```

…</article>

[View on GitHub](https://github.com/google/schema-dts)

它由两个 NPM 软件包组成:

1.  [schema-dts](https://npmjs.com/package/schema-dts)——代表 schema.org 的一组默认类型和属性，包括不推荐使用的类型、待定类型和某些扩展词汇表。

2.  [schema-dts-gen](https://npmjs.com/package/schema-dts-gen) -给定 Schema.org 层(例如“schema”或“all layers”)动态生成类型脚本定义的 CLI，带有自定义标志和配置(例如自定义“@context”)，跳过不推荐使用的类型和属性等。).