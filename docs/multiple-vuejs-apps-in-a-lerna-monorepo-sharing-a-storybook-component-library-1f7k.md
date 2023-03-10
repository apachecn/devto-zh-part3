# 一个 Lerna monorepo 中的多个 VueJs 应用程序，共享一个故事书组件库。

> 原文：<https://dev.to/pixari/multiple-vuejs-apps-in-a-lerna-monorepo-sharing-a-storybook-component-library-1f7k>

*(查看[我的博客](https://raffaelepizzari.com/) )*

> 你好！这是正在进行中的原型，但实际上它已经工作。我有这样一个场景:多个 VueJs 应用共享一个组件库。我想把它们都放在由 Lerna 管理的 monorepo 中。
> 组件库基于故事书。

请随时帮助我/给我你的建议。

## 我想实现什么

*简单性和可维护性*。
在我的场景中，一个或多个团队正在开发组件，并使用*语义版本*更新它们。
所有 VueJs 应用程序都使用共享组件，更改日志根据提交消息和标签自动创建。提交消息和标签由 Lerna 自动管理。

“框架”已经在工作了，但是我仍然需要改进一些步骤，增加一些功能。

这是 GitHub 回购:[https://github.com/pixari/component-library-monorepo](https://dev.topixar/component-library-monorepo)。

这里是“如何做”:

## 入门

### 安装泥浆

让我们从使用 npm 全局安装 Lerna 开始:

```
$ npm install --global lerna 
```

Enter fullscreen mode Exit fullscreen mode

* * *

接下来我们必须创建一个新的 git 存储库:

```
$ git init component-library-monorepo && cd component-library-monorepo 
```

Enter fullscreen mode Exit fullscreen mode

然后，按照 Lerna 的官方文件，将它变成一个 Lerna 回购:

```
lerna init 
```

Enter fullscreen mode Exit fullscreen mode

这个储存库应该看李和这个:

```
component-library-monorepo/
  packages/
  lerna.json
  package.json 
```

Enter fullscreen mode Exit fullscreen mode

如果你想了解更多关于这个过程的信息，你可以查看官方的 Lerna 文档。

### 安装故事书

让我们从使用 npm 全局安装 Lerna 开始:

```
$ npm install @storybook/vue --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

添加对等依赖关系

```
$ npm install vue --save
$ npm install vue-loader vue-template-compiler @babel/core babel-loader babel-preset-vue --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

添加 npm 脚本

```
{  "scripts":  {  "storybook":  "start-storybook"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

对于一个基本的故事书配置，您唯一需要做的事情就是告诉故事书在哪里可以找到故事。

为此，请在创建一个文件。storybook/config.js 包含以下内容:

```
import { configure } from '@storybook/vue';

const req = require.context('../packages', true, /.stories.js$/);
function loadStories() {
  req.keys().forEach(filename => req(filename));
}
configure(loadStories, module); 
```

Enter fullscreen mode Exit fullscreen mode

## 将第一个组件添加到组件库中

我们在根目录下创建一个 packages/index.stories.js 文件，并编写我们的第一个故事:

```
import Vue from 'vue';
import { storiesOf } from '@storybook/vue';
import MyButton from './Button/src/Button.vue';

storiesOf('Button', module)
  .add('as a component', () => ({
    components: { MyButton },
    template: '<my-button>with text</my-button>'
  }))
  .add('with emoji', () => ({
    components: { MyButton },
    template: '<my-button>😀 😎 👍 💯</my-button>'
  }))
  .add('with text', () => ({
    components: { MyButton },
    template: '<my-button :rounded="true">rounded</my-button>'
  })); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们创建真正的“按钮”组件:

```
/packages/Button
  /src
    Button.vue 
```

Enter fullscreen mode Exit fullscreen mode

```
<template>
  <button type="button"><slot /></button>
</template>

<script>
export default {
  name: 'MyButton',
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

index.js

```
/packages/Button
  src/index.js 
```

Enter fullscreen mode Exit fullscreen mode

```
import MyButton from './Button.vue';
export default MyButton; 
```

Enter fullscreen mode Exit fullscreen mode

还有 package.json:

```
{  "name":  "@mylibrary/my-button",  "version":  "0.2.0",  "description":  "Just a simple button component",  "main":  "dist/index.js",  "module":  "src/index.js",  "scripts":  {  "transpile":  "vue-cli-service build --target lib ./src/index.js"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 开始故事书

现在你已经准备好启动 Storybook 并开始玩你的第一个组件:

```
$ npm run storybook 
```

Enter fullscreen mode Exit fullscreen mode

你应该看到它在这里运行:

```
http://localhost:51368 
```

Enter fullscreen mode Exit fullscreen mode

## 创建一个 VueJs App

### 安装

要安装 Vue CLI，请使用以下命令:

```
$ npm install -g @vue/cli
$ npm install --save-dev @vue/cli-service 
```

Enter fullscreen mode Exit fullscreen mode

### 创建新项目

要创建新项目，运行:

```
$ cd packages && vue create my-app 
```

Enter fullscreen mode Exit fullscreen mode

请选择最简单的选项:

```
> default (babel, eslint) 
```

Enter fullscreen mode Exit fullscreen mode

在本教程中，我们不想构建最好的 VueJs 应用程序，而只是展示如何在 VueJs 应用程序之间共享组件库。

## 添加 eslint 配置

创造。/packages/my-app/. eslintrc . js

```
module.exports = {
    "env": {
        "browser": true,
        "es6": true
    },
    "extends": [
        "eslint:recommended",
        "plugin:vue/essential"
    ],
    "globals": {
        "Atomics": "readonly",
        "SharedArrayBuffer": "readonly"
    },
    "parserOptions": {
        "ecmaVersion": 2018,
        "sourceType": "module"
    },
    "plugins": [
        "vue"
    ],
    "rules": {
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 运行 App

让我们运行我们的新应用:

```
$ cd my-app && npm run serve 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该可以在这里看到您的应用程序，启动并运行:

```
http://localhost:8080/ 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 Lerna 来链接依赖关系

将以下依赖项添加到您的 packages/my-app/package . JSON:

```
{  "dependencies":  {  "@mylibrary/my-button":  "*"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 固定斯洛文尼亚语

```
const path = require('path');
module.exports = {
  chainWebpack: config => {
    config.module
      .rule('eslint')
      .use('eslint-loader')
      .tap(options => {
        options.configFile = path.resolve(__dirname, ".eslintrc.js");
        return options;
      })
  },
  css: {
    loaderOptions: {
      postcss: {
        config:{
          path:__dirname
        }
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以“引导”当前 Lerna repo 中的软件包，安装它们的所有依赖项，并链接任何交叉依赖项:

在根:

```
$ lerna bootstrap 
```

Enter fullscreen mode Exit fullscreen mode

## 更新 Vue App

改变的内容。/packages/my-app/src/main . js:

```
import Vue from 'vue'
import App from './App.vue'
import MyButton from '@mylibrary/my-button';

Vue.config.productionTip = false
Vue.component('my-button', MyButton);
new Vue({
  render: h => h(App),
}).$mount('#app') 
```

Enter fullscreen mode Exit fullscreen mode

并更改我们的 HelloWorld 组件的内容(。/packages/my-app/src/components/hello world . vue):

```
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <my-button>It Works!</my-button>
  </div> </template> 
<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

我们现在传输我们的组件:

```
$ lerna run transpile 
```

Enter fullscreen mode Exit fullscreen mode

再次运行..

```
$ cd packages/my-app && npm run serve 
```

Enter fullscreen mode Exit fullscreen mode

转到 [http://localhost:8080](http://localhost:8080) ，你应该看到 HelloWorld 页面中间的按钮:)