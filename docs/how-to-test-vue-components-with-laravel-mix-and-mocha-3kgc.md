# 如何用 Laravel Mix 和 Mocha 测试 Vue 组件

> 原文：<https://dev.to/tuandm/how-to-test-vue-components-with-laravel-mix-and-mocha-3kgc>

## 简介

在给 [Laravue](https://github.com/tuandm/laravue) 添加测试的时候，我发现用 Laravel Mix 给 vue 设置单元测试并不像 [Vue 的官方文件](https://vuejs.org/v2/guide/unit-testing.html)那么简单。经过一些谷歌搜索，我已经通过了设置步骤，现在只想分享一下我到目前为止所做的工作。

## 设置 Laravel 和 npm 的依赖关系

首先，我们用[命令](https://laravel.com/docs/5.8/installation#installing-laravel)创建新的 Laravel 项目，每个 Laravel 开发者都应该知道:

```
composer create-project --prefer-dist laravel/laravel test
cd test
npm install 
```

非常容易！

然后，我们必须设置测试环境以及所需的库/依赖项。我选了 [Mocha + Webpack](https://vue-test-utils.vuejs.org/guides/#testing-single-file-components-with-mocha-webpack) 因为很多文章都提到了这个。

```
npm install --save-dev webpack vue-template-compiler @vue/test-utils mocha mocha-webpack jsdom jsdom-global expect 
```

好的，看起来一切都准备好了，让我们用`npm list --depth=0`再检查一遍

哇，出事了！

```
➜  test npm list --depth=0
...
├── UNMET PEER DEPENDENCY mocha@6.0.2
...
└── UNMET PEER DEPENDENCY webpack@4.29.6

npm ERR! peer dep missing: mocha@>=2.4.5 <=5, required by mocha-webpack@1.1.0
npm ERR! peer dep missing: webpack@^2.0.0 || ^3.0.0, required by mocha-webpack@1.1.0
npm ERR! peer dep missing: webpack@^2.0.0 || ^3.0.0, required by mocha-webpack@1.1.0 
```

它说依赖关系的版本不匹配。似乎 mocha-webpack 没有更新 webpack 和 mocha，它需要旧版本(mocha 2.4.5-5 和 webpack 2 或 3，而最新版本是 mocha@6 和 [webpack@2.49.6](mailto:webpack@2.49.6) )。现在让我们去 [mocha-webpack](https://github.com/zinserjan/mocha-webpack) 回购看看会发生什么。的确，从去年开始就没有更新了。好，接下来做什么？我们可能希望检查问题列表，以查看任何类似的案例和解决方案。幸运的是，[第一期](https://github.com/zinserjan/mocha-webpack/issues/308)就是我们要找的:

```
Hi there, mocha-webpack users!

mocha-webpack has no activity since May, 2018\. We at SysGears are maintaining a fork here:
https://github.com/sysgears/mochapack

A new version of mochapack@1.1.0 has been released today with Mocha 6 support.

Check it out! 
```

现在我们按照作者的推荐去 [Mochapack](https://github.com/sysgears/mochapack) ，可以看到 Mochapack 的安装指南:

```
npm install --save-dev mochapack 
```

似乎很有效。好了，让我们去掉过时的摩卡网络包:

```
npm remove mocha-webpack 
```

哇，`npm list --depth=0`返回非常干净的结果。不要忘了给 mochapack 一颗星来奖励这项出色的工作。

## 设置测试环境

现在我们已经准备好按照这个[指南](https://vue-test-utils.vuejs.org/guides/#testing-single-file-components-with-mocha-webpack)为我们的 Vue 组件创建测试用例。首先，我们必须通过在`/tests/`中创建新文件夹来准备测试环境。

```
mkdir tests/js 
```

然后在文件夹`tests/js`中创建文件`setup.js`，并将这一行放在最上面:

```
require('jsdom-global')(); 
```

下一步将是运行测试的 npm 命令，我们必须打开`package.json`并在`scripts`部分
中添加这一行

```
 "test": "mochapack --webpack-config=node_modules/laravel-mix/setup/webpack.config.js --require tests/js/setup.js tests/js/\\*\\*/\\*.spec.js" 
```

您可以为`tests/js`文件夹和`setup.js`文件选择任何名称，不要忘记在测试命令中指明它们

现在是表演时间:

```
➜ npm test

 WEBPACK  Compiled successfully in 140ms

 MOCHA  Testing...

  0 passing (0ms)

 MOCHA  Tests completed successfully 
```

哇，它的工作与 0 测试通过-因为我们还没有添加。我们现在就去做。

## 写作测试

为了节省时间，我们可以使用 Laravel 的`resources/js/components/ExampleComponent.vue`来编写测试。如果你看一下这个组件，你会看到它只显示了`Example Component`头。现在我们创建一个测试文件`tests/js/components/ExampleComponent.spec.js`，并将这些行放到文件
中

```
// tests/js/components/ExampleComponent.spec.js
import { mount } from '@vue/test-utils';
import expect from 'expect';
import ExampleComponent from '../../../resources/js/components/ExampleComponent.vue';

describe('ExampleComponent.vue', () => {
  it('says that it is an example component', () => {
    const wrapper = mount(ExampleComponent);
    expect(wrapper.html()).toContain('Example Component');
  });
}); 
```

这个测试用例是检查 ExampleComponent 挂载后，`Example Component`应该在输出 html。

保存文件并再次运行测试命令:

```
➜ npm test
 WEBPACK  Failed to compile with 1 error(s)

Error in ./resources/js/components/ExampleComponent.vue?vue&type=template&id=299e239e&

  Module build failed (from ./node_modules/vue-loader/lib/loaders/templateLoader.js):
  TypeError: Super expression must either be null or a function
      at /.../test/node_modules/prettier/index.js:40358:5
      at /.../test/node_modules/prettier/index.js:40378:4

npm ERR! Test failed.  See above for more details. 
```

好了，我们有错误了。你会疯狂地从网上找到这个错误的答案。不要担心，只需这样修复:再次打开`tests/js/setup.js`，将这一行放在第一行的旁边:

```
window.Date = Date; 
```

保存`setup.js`文件并再次运行测试命令

```
➜ npm test

 WEBPACK  Compiled successfully in 1436ms

 MOCHA  Testing...

  ExampleComponent.vue
Component mounted.
    ✓ says that it is an example component

  1 passing (22ms)

 MOCHA  Tests completed successfully 
```

是的，成功了，测试通过了。现在我们可以放入更多的 Vue 组件并进行测试。

请享受测试！你可以在这里找到我的样本:[https://github.com/tuandm/laravel-vue-tests-with-mocha](https://github.com/tuandm/laravel-vue-tests-with-mocha)

注意:我使用的是 MacOS Mojave 10.14.2，所有相关工具(npm，composer...)被视为最新版本。