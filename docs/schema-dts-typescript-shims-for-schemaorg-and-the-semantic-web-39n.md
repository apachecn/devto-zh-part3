# Schema-DTS:用于 Schema.org 和语义网的打字稿垫片

> 原文：<https://dev.to/eyassh/schema-dts-typescript-shims-for-schemaorg-and-the-semantic-web-39n>

[**schema-dts**](https://github.com/google/schema-dts) 是一个新的库(和相关工具)，以 JSON-LD 格式为 Schema.org词汇提供了打字稿定义。类型化被公开为完全不同的类型联合，允许容易的完成和更严格的验证。

[![Example of Code Completion using schema-dts](img/c5129eb6d29d050a2ff671ed02250920.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1b5z1QQL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/google/schema-dts/HEAD/example-1.gif)

## 动机

我注意到，虽然 Schema.org 模式在 web 上已经有了很多应用，但是很少有模式验证工具被集成到开发人员的工具链中。例如， [Google 的结构化数据测试工具](https://search.google.com/structured-data/testing-tool)仍然是许多人验证手写 Schema.org 模式正确性的主要资源。

输入 TypeScript，以及使用类型定义 JSON 模式的便利性。由于 JSON-LD 格式以及 Google 和其他搜索引擎推荐使用 JSON-LD(而不是微数据和其他结构化数据格式)编码结构化数据，有机会使编写结构化数据变得更加容易。我们可以通过在我们的开发工具链中包含我们正在编写的 Schema.org 模式来收紧编写-测试-调试迭代。

## 用法

要在您的项目中使用这些类型，只需将`schema-dts` NPM 包添加到您的项目中:

```
npm install schema-dts 
```

然后导入`"schema-dts"`就可以用了。

## 例子

### 定义简单属性

```
import {Person} from "schema-dts";

const inventor: Person = {
    "@type": "Person",
    "name": "Grace Hopper",
    "disambiguatingDescription": "American computer scientist",
    "birthDate": "1906-12-09",
    "deathDate": "1992-01-01",
    "awards": [
        "Presidential Medal of Freedom",
        "National Medal of Technology and Innovation",
        "IEEE Emanuel R. Piore Award",
    ]
}; 
```

### 使用“上下文”

JSON-LD 要求在顶级 JSON 对象上设置一个`"@context"`属性，以描述代表被引用的类型和属性的 URIs。schema-dts 提供了`WithContext<T>`类型来实现这一点。

```
import {Organization, Thing, WithContext} from "schema-dts";

export function JsonLd<T extends Thing>(json: T): string {
    return `<script type="application/ld+json"> ${JSON.stringify(json)} </script>`;
}

export const MY_ORG = JsonLd<Organization>({
    "@context": "https://schema.org",
    "@type": "Corporation",
    "name": "Google LLC"
}); 
```

### 亦见

*   [GitHub 页面](https://github.com/google/schema-dts)
*   [NPM 包](https://www.npmjs.com/package/schema-dts) ( [发电机 NPM 包](https://www.npmjs.com/package/schema-dts-gen))
*   [谷歌的开源项目清单](https://opensource.google.com/projects/schema-dts)