# 将类型脚本库与毕丽捆绑在一起

> 原文：<https://dev.to/egoist/bundle-a-typescript-library-with-bili-19h4>

[毕丽](https://bili.egoist.sh)是一个构建在[汇总](https://rollupjs.org)之上的捆绑器，如果你想以多种格式捆绑你的库，这真的很方便，现在构建一个在 CommonJS、UMD 和 ES 模块中工作的 JavaScript 库是很常见的。

毕丽也可以无缝地使用 TypeScript，这篇文章将带你创建一个简单的 TypeScript 库。

## 开始使用

让我们从创建一个新项目开始，学习如何通过毕丽使用 TypeScript:

```
mkdir my-lib
cd my-lib
yarn init -y # Create a package.json
yarn add bili --dev 
```

接下来安装 TypeScript 相关的依赖项:

```
yarn add typescript rollup-plugin-typescript2 --dev 
```

我们将使用[roll up-plugin-typescript 2](https://github.com/ezolenko/rollup-plugin-typescript2)而不是官方的 [rollup-plugin-typescript](https://github.com/rollup/rollup-plugin-typescript) ，因为后者在编译期间不执行类型检查。

现在，如果你正在构建一个`.ts`文件，毕丽将自动使用 rollup-plugin-typescript2。

## 打字稿配置

为了让 TypeScript 执行正确的类型检查，一个`tsconfig.json`是必要的，您可以通过运行带有`--init`标志:
的 TypeScript 编译器来创建一个

```
yarn tsc --init 
```

随意调整`tsconfig.json`中的选项以满足您的需求。

## 以多种格式捆绑

我们将在`my-lib` :
中创建一个`src/index.ts`

```
# Create src/index.ts
mkdir src
echo "export default 42" > src/index.ts
# Bundle it in CommonJS and ESM format
# Omit `--format <format>` to bundle in CommonJS only
yarn bili src/index.ts --format cjs --format esm 
```

然后`src/index.ts`会捆绑到`dist/index.js` :

```
'use strict';

var index = 42;

module.exports = index; 
```

和`dist/index.mjs` :

```
var index = 42;

export default index; 
```

建议在`.gitignore`文件中添加`dist`(由毕丽生成)和`.rpt2_cache`(由 rollup-plugin-typescript2 生成)。

## 生成申报文件

要为`src`文件夹中的文件生成相应的`.d.ts`文件，首先需要在`tsconfig.json` :
中启用`compilerOptions.declaration`

```
{  "compilerOptions":  {  "declaration":  true  }  } 
```

然后创建`bili.config.ts`来配置 TypeScript 只包含`src`文件夹:

```
import { Config } from 'bili'

const config: Config = {
  plugins: {
    typescript2: {
      // Override the config in `tsconfig.json`
      tsconfigOverride: {
        include: ['src']
      }
    }
  },

  // Let's take this opportunity to move the CLI flags here as well
  input: 'src/index.ts',
  output: {
    format: ['cjs', 'esm']
  }
}

export default config 
```

注意，我们没有直接在`tsconfig.json`中设置`include: ['src']`，因为在大多数情况下，你的编辑器比如 VS 代码会默认使用那个文件，而你不希望其他文件比如`./my-lib/test/index.test.ts`被排除在外。

最后让我们运行`yarn bili`，申报文件将生成到`dist/index.d.ts` :

```
declare const _default: 42;
export default _default; 
```

## 配置 package.json

```
{  "name":  "my-lib",  "main":  "dist/index.js",  "module":  "dist/index.mjs"  "types":  "dist/index.d.ts",  "files":  ["dist"],  "scripts":  {  "build":  "bili",  "prepublishOnly":  "npm run build"  }  } 
```

现在你可以在 npm 上发布它，当其他人使用它的时候，你可以找到它的类型。

查看[https://github.com/egoist/objql](https://github.com/egoist/objql)获得一个简单的真实世界的类型脚本库。

## 相关链接

*   [在 GitHub 上为毕丽做贡献](https://github.com/egoist/bili)
*   [毕丽的 JavaScript/TypeScript](https://bili.egoist.sh/#/recipes/javascript)
*   [在推特上关注毕丽的作者](https://twitter.com/_egoistlily)
*   为了毕丽的生存而捐款