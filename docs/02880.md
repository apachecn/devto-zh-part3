# 自动将您的故事书部署到 Github 页面

> 原文：<https://dev.to/christopherkade/automatically-deploying-your-storybook-to-github-pages-4f6c>

故事书是一个很棒的工具，它可以让你把你的组件组织成一个整洁的小界面，看起来有点像 T2 这个 T3。

你基本上创建了*故事*，负责返回你想要显示给用户的组件。

这里有一个 React 组件的例子:

```
//...

import { Button } from "../components/Button"

storiesOf('Button', module)
  .add('With text', () => <Button>Enter World</Button>)
  .add('With emojis', () => <Button>🔥⚔️</Button>) 
```

Enter fullscreen mode Exit fullscreen mode

很简单的东西，当然，有很多方法可以定制你的故事书，从主题到添加令人敬畏的 T2 插件。

但是这篇文章不是故事书的介绍，如果你想要一本，我真心推荐 Emma Wedekind 的[“用故事书记录 React 组件”](https://dev.to/emmawedekind/documenting-react-components-with-storybook-4h3b)。今天，我们将部署一个现有的故事书到 Github 页面，比如[这个](https://christopherkade.com/ReactCraft)，同时在一个给定的分支上自动执行这个过程。

## 设置特拉维斯 CI

Travis CI 是一个持续集成服务，用于构建和测试 Github 上托管的软件。它对开源项目是免费的，并且允许我们非常容易地部署到 Github 页面。

在我们完成所有这些之前，需要遵循几个步骤:

1.  去 travis-ci.com 注册 GitHub。
2.  接受 Travis CI 的授权。你会被重定向到 GitHub。
3.  单击绿色的激活按钮，并选择要部署的存储库
4.  按照这个[链接](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line)上的简短说明，在 Github 上生成一个个人访问令牌，并赋予它`public_repo`权限。
5.  在 Travis 上进入您的项目设置，在环境变量部分，创建一个`GITHUB_TOKEN`变量，并给它您刚刚生成的令牌。该令牌将允许 Travis 推送至您的存储库以便进行部署。

[![render of the component mentioned](img/1bbfc5c0a50e2432ad431c2f8cfebda8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yLTSAb4a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/15229355/57936079-78135700-78c3-11e9-8735-0aac4881d546.png)

我们现在可以在项目的根目录下创建一个`.travis.yml`文件，并赋予它以下内容:

```
# Tells Travis we're running on a Node environment
language: 'node_js'
node_js: '8'

# Script that is run before the script phase
before_script:
- yarn build-storybook

# Deployment information
deploy:
  provider: pages # Tell Travis we want to deploy to Github Pages
  skip-cleanup: true 
  github-token: $GITHUB_TOKEN # Will take the environment variable you created on step 5
  local_dir: storybook-static # The folder that needs to be deployed
  repo: christopherkade/ReactCraft # Add your username/project_name here
  target_branch: gh-pages # Tell Travis to deploy on the gh-pages branch
  on:
    branch: master # Tell Travis to trigger a deploy only when we push to master 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:如果您希望在推送到特定分支时触发部署，可以随意更改配置文件的`branch: master`部分。

## 为部署设置我们的故事书

我们差不多完成了，现在我们需要将我们的故事书导出到一个静态应用程序中。这样，Travis 将负责将生成的应用程序推送到我们的`gh-pages`分支，分支将部署它！

在您的`package.json`中，添加以下脚本:

```
{  "scripts":  {  //  ...  "build-storybook":  "build-storybook -s public",  },  } 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本是在我们的`.travis.yml`中的`before_script`阶段运行的。它会在你的项目根目录下生成一个`storybook-static`文件夹，包含你的网站的静态实例。

## 部署你的故事书

我们现在可以开始第一次部署了😍

将您的`package.json`和`.travis.yml`文件推送到您的存储库中。

如果一切按计划进行，您应该会在 Travis 仪表板上看到一个构建被触发。过了一会儿(先等几分钟)，如果你的构建显示成功，你会在下面的 URL 下找到你的故事书:username.github.io/project-name.

请记住，一些事情可能会导致构建失败:

1.  你的测试失败了
2.  忘记了本文中的一个步骤，例如在 Travis 项目中设置包含 Github 令牌的环境变量。
3.  你的 linter 抛出一个错误(如果你有一个设置)

因此，您可以随时修复这些问题，并将您的更改推送到 Github。

祝贺您将故事书部署到 Github 页面🎉

如果您有任何问题或想保持联系，请随时关注我的 Twitter [@christo_kade](https://twitter.com/christo_kade) ，我会定期在那里发布关于 JS 和 CSS 的文章，并会在有新文章时通知您😄