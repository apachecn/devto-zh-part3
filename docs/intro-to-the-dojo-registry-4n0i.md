# Dojo 注册中心简介

> 原文：<https://dev.to/odoenet/intro-to-the-dojo-registry-4n0i>

Dojo Registry 是一种在应用程序中使用小部件的强大方式。在以前的帖子中，我们有机会接触到注册表。我们用它通过 [Dojo 容器](https://learn-dojo.com/dojo-containers/)将上下文注入到我们的应用程序中，当您想在应用程序中使用[路由](https://learn-dojo.com/dojo-cli-template-app/)时，也可以使用它。

### 基本注册表

注册表允许您在应用程序中做一些不同的事情。您可以将小部件以字符串值的形式注册到注册表中，然后在整个应用程序中引用这些字符串值。

例如，也许我想为我的 About 页面中使用的 AboutMe 小部件定义一个字符串值。

```
// src/main.ts
import Registry from '@dojo/framework/widget-core/Registry';
...
// define widgets
import AboutMe from './widgets/AboutMe';

const registry = new Registry();
registry.define('about-me', AboutMe);

// src/widgets/About.ts
import WidgetBase from '@dojo/framework/widget-core/WidgetBase';
import { w, v } from '@dojo/framework/widget-core/d';

import * as css from './styles/About.m.css';

export default class About extends WidgetBase {
  protected render() {
    return v('h1', { classes: [css.root] }, [
      'About Page',
      // can reference the string value
      // of widget without importing it
      w('about-me', {}, [])
    ]);
  }
} 
```

这非常有用，因为它可以保持一些小部件代码的整洁，并且可以在注册时在一个位置定义一系列小部件。当我们通过一个[注册表装饰器](https://dojo.io/docs/index.html#doc--dojo__framework__v4_0_0__src__widget-core__README_md___registry-decorator)使用小部件的本地注册表来处理延迟加载时，事情开始变得非常有趣。

## 注册表装饰器

每个小部件都有自己的本地注册表，您可以使用它来延迟加载小部件，直到您需要它。也许我想在我的页面上有一个按钮，当点击它时会在它的位置加载一些其他的小部件。对于 cli 模板应用程序，也许我有一些想要显示的配置文件信息。

```
// src/widgets/MyProfile.ts
import WidgetBase from '@dojo/framework/widget-core/WidgetBase';
import { v } from '@dojo/framework/widget-core/d';

export default class MyProfile extends WidgetBase {
  protected render() {
    return v('aside', {}, [
      v('p', {}, [
        `I don't believe in the moon,
         I think it's just the back of the sun.`
      ])
    ]);
  }
} 
```

这是一个简单的小部件，没有什么太花哨。现在让我们用注册表装饰器将它添加到我们的 profile 小部件中。

```
// src/widgets/Profile.ts
import { registry } from '@dojo/framework/widget-core/decorators/registry';
import { watch } from '@dojo/framework/widget-core/decorators/watch';

...

// the decorator allows us to use a dynamic import
// to lazy load this widget until it's needed
@registry('my-profile', () => import('./MyProfile'))
export default class Profile extends WidgetBase<ProfileProperties> {
  @watch() private _showProfile = false;

  private onButtonClick() {
    this._showProfile = true;
  }

  protected render() {
    const { username } = this.properties;
    // check if the `_showProfile` property
    // is true to determine if I should load
    // the `MyProfile` widget
    const node = this._showProfile ?
                  w('my-profile', {}) :
                  v('button', {
                    onclick: this.onButtonClick
                    }, [ 'Show Profile' ]);
    return v('h1', { classes: [css.root] }, [
      `Welcome ${username}!`,
      v('p', {} , [
        node
      ])
    ]);
  }
} 
```

我们可以利用 watch decorator 来更新小部件的值，以确定小部件应该是什么样子。你可以在我之前的博客文章中读到更多关于手表装饰的内容。*那真是太棒了，对吧！*因为 registry decorator 允许我们使用动态导入，所以 Dojo 构建系统将为这个小部件创建一个可以延迟加载的包。你甚至可以用注册表装饰器做[多条目](https://dojo.io/docs/index.html#doc--dojo__framework__v4_0_0__src__widget-core__README_md___registry-decorator)，以允许在你自己的小部件中访问多个小部件。

你可以在下面的代码沙箱中看到一个演示。

### 总结

在 Dojo 中，注册表是一个非常强大的工具。我们已经看到它被用于路由、预定义一系列可以在整个应用程序中使用的小部件、延迟加载，以及使用[容器和注入器](https://dojo.io/docs/index.html#doc--dojo__framework__v4_0_0__src__widget-core__README_md___containers--injectors)。一旦你开始使用它，你将会发现它可以帮助你构建令人敬畏的应用程序！

请务必[订阅时事通讯](https://learn-dojo.com/sign-up/)，了解最新内容！