# 针对不同的环境变量和部署阶段进行测试

> 原文：<https://dev.to/dvddpl/testing-agaist-different-environment-variables-and-deployment-stages-4hpl>

我非常喜欢用 AVA 写单元测试的一个原因是，每个测试文件都在一个单独的 Node.js 进程中运行，因此对全局变量的改变(无论如何都应该尽可能避免..但那是后话)不影响其他测试。

默认情况下，AVA 会将`process.env.NODE_ENV`设置为*测试*，因此，它会搜索一个*。env* 文件，以便填充所有的环境全局变量(通过`process.env.MY_VARIABLE`访问的变量)。

可能会发生这样的情况，您想要使用不同的 env 文件(在我们最近的案例中，我们进行了几次集成测试，我们想要确保它们工作在 **staging** 上，而不是工作在生产数据库上)。
或者您想要测试一个缺少、损坏或无效值的文件。

如果你想用 AVA 测试不同于`.env.test`的文件，那么你可以使用 [dotenv](https://www.npmjs.com/package/dotenv) 将你的特定文件加载到你的测试文件中。

```
import test from 'ava';
require('dotenv').config()

test('make sure env variables are loaded', t => {
    console.log(process.env.DB_HOST);
    console.log(process.env.DB_USER);
    t.truthy(process.env.DB_HOST);
}); 
```

Enter fullscreen mode Exit fullscreen mode

因为在我们的项目中我们使用无服务器框架，并且我们依赖于 **YAML** 文件作为我们的 env 变量，所以我们使用了一个类似的包， [env-yaml](https://www.npmjs.com/package/env-yaml) ，它的用法非常相似:

```
require('env-yaml').config({path: path.resolve('./envs/env.dev.yml')})
test('make sure env variables are loaded', t => {
    console.log(process.env.DB_HOST);
    t.truthy(process.env.DB_HOST);
}); 
```

Enter fullscreen mode Exit fullscreen mode

通过在每个测试文件中直接导入您需要的文件，由于**每个测试文件都在其节点进程**中运行，您可以在不同的测试中使用不同的 env 文件，或者在不同的环境中测试变量，或者再次测试丢失的变量，而不必担心弄乱全局配置，因此不必在拆卸阶段重置一切。

假设您有一个 *envs* 文件夹，其中有以您的环境/部署阶段命名的不同环境变量文件，每个文件都包含

```
DB_NAME: 'current_env_db'
DB_USER: 'current_env_user' 
```

Enter fullscreen mode Exit fullscreen mode

您可以运行不同的测试(来自不同的文件),并确保您在进程上设置了不同的 env 全局变量。

env-vars-production.test.js 内部

```
test("staging variables are loaded", t => {
  require('env-yaml').config({path: path.resolve('./envs/env.staging.yml')})
    t.is(process.env.DB_NAME, 'staging_db');
}) 
```

Enter fullscreen mode Exit fullscreen mode

env-vars-development.test.js 内部

```
test("development variables are loaded", t => {
    require('env-yaml').config({path: path.resolve('./envs/env.dev.yml')})
    t.is(process.env.DB_NAME, 'dev_db');
}) 
```

Enter fullscreen mode Exit fullscreen mode

在 env-vars-missing.test.js 内部

```
test('ops we forgot to load env variables', t=> {
    t.falsy(process.env.DB_NAME);
}) 
```

Enter fullscreen mode Exit fullscreen mode

当然，我并不是建议进行单元测试，以确保所有的变量都在那里，并且有一个特定的值，我只是想展示如何使用不同的 env 文件运行测试，并对那些可能依赖于特定的全局变量或者在没有设置这些值的情况下可能中断的代码进行单元测试。

希望这有所帮助:-)