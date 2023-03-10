# 反应本地:常见的有机成分

> 原文：<https://dev.to/oieduardorabelo/react-native-como-organizar-seus-componentes-301o>

编写代码可能很容易-但你如何组织它？

我和几十个开发人员一起合作过，与原生试剂合作，当主题是整理代码时，我看到了惊人的多样性。将所有内容放入平均少于 10 行的文件的文件中。

在本教程中，我想谈一谈平衡的方法。一种组织代码以便于工作和维护的方法，但不是管理负担。

# 总体项目结构

本教程仅讨论用户界面组件，但我想概述一下我的 React Native 项目通常是如何构造的。

```
# Estrutura de diretórios do projeto
/app
  /assets
  /components
  /config
  /navigation
  /screens
  /util
  index.js 
```

未提及是其馀的 React 原生文件。我喜欢把我的代码封装在一个目录中`app`或`src`，清楚地知道什么是“我的”。这样还可以更轻松地更新项目，并降低发生合并冲突的可能性。

# 我的成分哲学

在继续之前，我想说的是，在本教程的上下文中，我指的是应用程序的用户界面组件。他们通常只处理数据的呈现，然后另一个组成部分(a `screen`将处理数据。

例如:我有一个登录屏幕，上面有 3 个文本条目和一个按钮。我将对其进行构造，以便文本输入组件显示数据并以样式处理。当用户键入文本/按按钮时，这些条目将传递到“`screen`”以了解如何处理。

这是我用于所有组件的方法。

# Á地区功能组织

随着应用程序的增长，您将拥有更多组件。当您的组件变得越来越复杂时，您可能希望将它们分成更小的部分，以便更容易思考和维护。

如此一来，您就可以终止一个庞大的组件目录！这也许是好的——即使在我下面的例子中，我仍然认为是可以接受的。

```
# Grande lista de componentes
/components
  TextInput.js
  Switch.js
  Button.js
  List.js
  ListItem.js
  Loading.js
  Calendar.js
  CalendarItem.js
  CardContainer.js
  CardBodyImage.js
  CardBodyText.js
  Header.js
  HeaderLeftButton.js
  HeaderCenterContent.js
  HeaderRightButton.js
  ... 
```

**但是-我...。**可能很难弄清大家是如何相互联系的。假设您在卡片元件、标头元件、表单、清单等之间共用型式。你把这些逻辑的共享部分放在哪里？

这就是为什么我喜欢按功能区域更深入地划分组件。让我们举上面的例子，把它组织得更有重点。

```
# Componentes organizados por área funcional
/components
  /Form
    TextInput.js
    Switch.js
  /List
    List.js
    ListItem.js
  /Calendar
    Calendar.js
    CalendarItem.js
  /Card
    CardContainer.js
    CardBodyImage.js
    CardBodyText.js
  /Header
    Header.js
    HeaderLeftButton.js
    HeaderCenterContent.js
    HeaderRightButton.js
  Loading.js
  Button.js 
```

我们添加了一个嵌套级别，这有助于按功能区域组织组件。我们知道什么是相关的。

现在，如果需要在一组组件之间共享样式，只需将其放置在该目录中即可。很简单。

最后一件事——我喜欢使组件功能区导出 API。这使我可以随意重命名/重组事物。这意味着我会在每个导出组件的目录中添加一个“`index.js`”。

```
// Form/index.js

import TextInput from './TextInput.js';
import Switch from './Switch.js';

export { TextInput, Switch }; 
```

这样做的另一个好处是，我从其他显示器/组件的导入量会减少。而不是去做:

```
import TextInput from '../components/Form/TextInput';
import Switch from '../components/Form/Switch'; 
```

我可能有一个进口如:

```
import { TextInput, Switch } from '../components/Form'; 
```

# 避免深度嵌套

现在，对这种做法提出一个快速警告。我建议您不要深入嵌套文件夹中的内容。它们可能会变成一个很大的头痛点！

不一致引起混乱。混乱导致*天网*。不要创造出*【天网】*。

这里有一个例子。就说我们有一套标准的颜色`config/colors.js`。为了从我们的文件`TextInput.js`，我们需要:

```
// TextInput.js

import colors from '../../config/colors.js'; 
```

到时候见。但如果我们深入，你就会越来越多地出现`../../../...`。

这方面最大的挑战只是看看你要爬多少个目录。两个我很容易理解。3 我得开始数了。

**怀疑**深层嵌套！

# 保持灵活

简而言之，我只想提醒你保持灵活性。对我有用的东西对你没用。保持灵活性。有时，在目录(例如表单)中放置功能区是有意义的。有时没有原因(如加载指示器)。保持灵活性，并在应用程序中建立和实施系统/规则！

过了一会儿，审查这些决定。你将会有更丰富的经验，知道为什么有些事情不对劲，让你舒适地前进和修复。

不要花太多时间担心“完美”代码的组织。我向你保证，如果你花 10 分钟或 10 个小时把一切都安排好，你就会讨厌某件事。

# [t1【学分】](#cr%C3%A9ditos-%EF%B8%8F)

*   [如何组织您的组件](https://www.reactnativeschool.com/how-to-organize-your-components)，escrito origination por[斯潘塞卡利](https://dev.to/spencercarli)