# React-schema org:React 的强类型 Schema.org JSON-LD

> 原文：<https://dev.to/eyassh/react-schemaorg-strongly-typed-schemaorg-json-ld-for-react-4lhd>

当我在一个站点中插入 JSON-LD 并注意到缺少一种 TypeScript-y、开发人员友好的方式来做这件事时，我制作了这个库(以及相关的、较低级别的 [schema-dts](https://github.com/google/schema-dts) )。大多数验证 Schema.org[JSON-LD 的工具都是使用这些在线“验证器”，但是这打破了(或者延长了)开发的编写-构建-测试循环。](https://Schema.org)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [谷歌](https://github.com/google) / [ react-schemaorg](https://github.com/google/react-schemaorg)

### 针对 React 对 Schema.org JSON-LD 进行了类型检查

<article class="markdown-body entry-content container-lg" itemprop="text">

[![react-schemaorg npm version](img/30b20ba123b739d2ba82f2b44483d949.png)](https://www.npmjs.com/package/react-schemaorg)[![Build Status](img/4def58ec732af066dffc5b38b6a83ff1.png)](https://travis-ci.org/google/react-schemaorg)[![Coverage Status](img/b611bc63c3ddf81bc70bc6142634ad03.png)](https://coveralls.io/github/google/react-schemaorg?branch=master)

# react-schemaorg

在 React 应用程序中轻松插入有效的 Schema.org JSON-LD。

这个库为普通的 React 应用程序提供了`<JsonLd>`，为配合 [`<Helmet>`](https://github.com/nfl/react-helmet) 使用提供了`helmetJsonLdProp()`。

将 [schema-dts](https://github.com/google/schema-dts) 用于 Schema.org 类型脚本定义。

注意:这不是官方支持的谷歌产品。

## 使用

安装 [`react-schemaorg`](https://www.npmjs.com/package/react-schemaorg) 和你想要的 [`schema-dts`](https://www.npmjs.com/package/schema-dts) 版本:

```
npm install schema-dts
npm install react-schemaorg
```

### 简单反应用法

要插入一个简单的 JSON-LD 代码片段:

```
import { Person } from "schema-dts";
import { JsonLd } from "react-schemaorg";
export function GraceHopper() {
  return (
    <JsonLd<Person>
      item={{
        "@context": "https://schema.org",
        "@type": "Person",
        name: "Grace Hopper",
        alternateName: "Grace Brewster Murray Hopper",
        alumniOf: {
          "@type": "CollegeOrUniversity",
          name: ["Yale University", "Vassar College"],
        },
        knowsAbout: ["Compilers", "Computer Science"],
      }}
    />
  )
}
```

…</article>

[View on GitHub](https://github.com/google/react-schemaorg)

这个库允许您插入经过类型检查的 JSON-LD，并提供补全等功能。，只是利用了 TypeScript 的类型系统。react-schemaorg 是 schema-dts 的一个简单包装器，它插入了`<script type="application/ld+json">`标记，并要求您指定一个顶级的`"@context"`。