# ReactJS——使用 Airbnb 风格指南提交 Git 时自动 Lint & Format

> 原文：<https://dev.to/gijovarghese/reactjs--auto-lint--format-on-git-commit-with-airbnb-styleguide-2815>

当你在一个团队中工作时，每个开发人员都有自己的风格。所有文件保持一致的风格非常重要。

看着一段代码，你应该看不出是谁写的😉

通过本指南，您将能够在 Git 提交时设置自动林挺和格式化。

*如果你是 NodeJS 开发人员，请阅读 Git Commit 上的这个-[NodeJS–Auto Lint&格式与 Airbnb 风格指南](https://coffeencoding.com/nodejs-auto-lint-format-on-git-commit-with-airbnb-styleguide/)T3】*

它分为 4 个部分

你会学会的

1.  使用 Airbnb 风格指南设置 Eslint
2.  用更漂亮的设置格式
3.  Git 提交时自动 Lint 和 Format
4.  为 Eslint 和更漂亮的配置 VS 代码

## 为什么你会需要林挺和格式化？

*   干净的代码
*   轻松找到错误、打字错误、语法错误
*   遵循最佳实践
*   关于使用不推荐的/有害的方法的警告
*   在整个团队中保持一致的代码风格
*   避免提交像 console.log 这样的“有害”代码
*   让 PR 牛逼一点，让评论者少一点头疼！

## 用 Airbnb 风格指南设置 Eslint

Eslint 是一个用于 JavaScript 和 JSX 的林挺工具，有一些不错的规则和插件。任何人都可以为 Eslint 写规则。一个示例规则可以是“避免使用 console.log()”

幸运的是，Airbnb 已经为 JavaScript 写了一个风格指南，涵盖了他们使用的大多数最佳实践。它基本上是不同规则的集合。你可以在这里阅读-[Airbnb JavaScript 风格指南](https://github.com/airbnb/javascript)

**第一步**——通过
`npm i -D eslint eslint-config-airbnb eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react`安装必要的软件包

**第二步**——在你的项目的根目录下创建一个新文件`.eslintrc`并粘贴下面的

```
{  "env":  {  "browser":  true  },  "extends":  ["airbnb",  "prettier"]  } 
```

**步骤 3**–在`package.json`–`"lint": "eslint 'src/**/*.{js,jsx}' --fix"`中给 lint 添加一条新命令

现在，您应该能够通过运行`npm run lint`来 lint 您的代码。它将尝试修复可修复的错误。否则将抛出错误/警告

## 设置格式更美观

Eslint 是为了林挺和查找代码中的错误，而[prettle](https://prettier.io/)纯粹是为了格式化。除此之外，JavaScript beauty 还支持格式化 json、HTML、CSS、markdown、sql、yaml 等。强烈建议同时使用 Eslint 和 appearlier。

**第 1 步**–通过`npm i -D prettier-eslint-cli eslint-config-prettier`安装更漂亮的 CLI 包

**第二步**——在`package.json`—`"format": "prettier --write 'src/**/*.{js,jsx,css,scss}'"`中添加一条新命令进行格式化

就像我们之前做的一样，你现在应该能够运行`npm run format`来使用 Prettier 格式化代码了！

## Git 提交时的  自动 Lint &格式

尽管我们已经构建了运行 lint 和格式化的命令，但大多数时候开发人员会忘记运行它并提交给 git。您可以将 npm run lint 设置到您的 CI/CD 中，这样每当出现一些错误时，它都会失败。然而，如果每次有人提交时我们都做这些检查，那就太好了。

#### 沙哑而皮棉——登台营救

[Husky](https://www.npmjs.com/package/husky) 允许您在提交前添加要运行的命令。它利用了 [git 钩子](https://githooks.com/)。

[Lint 暂存的](https://www.npmjs.com/package/lint-staged)–“对暂存的 git 文件运行 linters”。在每次提交时对所有文件运行 Eslint 和 Prettier 将非常耗时。使用 lint-staged，您只能在暂存文件上运行它们。

由`npm i -D husky lint-staged`安装 husky 和 lint-staged

您需要编辑`package.json`来配置它。下面是完整的文件:

```
{  "scripts":  {  "lint":  "eslint 'src/**/*.{js,jsx}' --fix",  "format":  "prettier --write 'src/**/*.{js,jsx,css,scss}'"  },  "lint-staged":  {  "**/*.js":  [  "eslint --fix",  "prettier-eslint --write",  "git add"  ]  },  "husky":  {  "hooks":  {  "pre-commit":  "lint-staged"  }  },  "devDependencies":  {  "eslint":  "^5.15.0",  "eslint-config-airbnb":  "^17.1.0",  "eslint-config-prettier":  "^4.1.0",  "eslint-plugin-import":  "^2.16.0",  "eslint-plugin-jsx-a11y":  "^6.2.1",  "eslint-plugin-react":  "^7.12.4",  "prettier-eslint-cli":  "^4.7.1",  "husky":  "^1.3.1",  "lint-staged":  "^8.1.3"  }  } 
```

我们告诉 husky 在每次提交时运行 lint-staged。Lint-staged 将对 staged 文件运行 eslint、prettier 和' git add'。最后一个“git add”是将已更改的字段添加回提交，因为它可能是已更改的格式。

#### 无需这些检查就需要提交？

[![](img/885bf7b2e65c51cd0b589cddc7b61079.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0McyRIGS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://coffeencoding.com/wp-content/uploads/2019/03/git-fire-300x300.jpg) 
如果发生火灾怎么办🙂你试着承诺？“请删除控制台日志”之类的话？您通过添加- no-verify ( `git commit –m -n “Urgent commit!”`)来告诉 git 不要运行这些钩子

## 为 Eslint 和 Prettier 配置 VS 代码

Eslint 和 Prettier 都很好地集成了 VS 代码。它会自动突出显示错误/警告，在输入/保存时修复代码等。

通过`ext install dbaeumer.vscode-eslint`和`ext install esbenp.prettier-vscode`安装 [Eslint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 和[beauty](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)扩展

安装完扩展后，打开 VS Code settings.json (Ctrl+，)文件，添加以下内容:

```
{  "editor.formatOnPaste":  true,  "editor.formatOnSave":  true,  "editor.formatOnType":  true,  "prettier.eslintIntegration":  true  } 
```

### 结论

您现在应该已经配置了 Eslint 和 appearlier，这样无论何时您尝试提交文件，它们都会扫描文件并尝试修复所有错误，或者向您显示无法自动修复的错误。希望你喜欢。

如果您遇到任何问题或有任何其他反馈，请在下面评论！

这篇文章最初发表在我的博客 [Coffee N Coding](https://coffeencoding.com) 。在[推特](https://twitter.com/gijovarghese141)上关注我，我分享了很多像这样很酷的东西。

通过 [FB Messenger](https://m.me/775189579517896/?ref=eyJ0YWdzIjpbImNhdGVnb3J5OlJlYWN0SlMiLCJtLm1lIl19) 订阅我的博客