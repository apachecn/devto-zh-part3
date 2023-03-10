# 应用程序中的多种语言

> 原文：<https://dev.to/drfrost/multilingualism-in-applications-2phn>

[![Thumbnail](img/9d35c8951ad927c2f0d4f7ed815cb66e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nme5LUqV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mczo17hjst5hhiw5silz.jpeg)

* * *

你好，Guten Tag，dziendobry，Bonjour，Salve，السَّلَامُ عَلَيْكُمْ！

大约一年前，我们在工作中的项目需要某种翻译管理系统。我们已经建立了一个非常简单的内部库，它提供了一个包含翻译的对象，包含翻译后的文本。

所有的翻译都存储在一个 JSON 文件中，然后我们转移到 YAML——它实际上在构建时被转换成 JSON，所以 JavaScript 可以正确地解释它。

YAML 文件如下所示。

```
unique.yes:
   de_DE: Ja
   en_US: Yes
   pl_PL: Tak
unique.no:
   de_DE: Nein
   en_US: No
   pl_PL: Nie
unique.example:
   de_DE: Beispiel
   en_US: Example
   pl_PL: Przykład 
```

Enter fullscreen mode Exit fullscreen mode

一切都很好..除了我们现在有一个大约 10 000 行翻译的文件，这是非常难以维护的，而且应用程序仍在开发中的事实让我担心。

我们一直在尝试重构翻译，建立工具，这有助于排序，但它没有做太多，我们的翻译仍然混乱。

有一天在使用 JSS 的时候，我想到了一个实现翻译的想法，就像在 JSS 和…

tada tada

litera 诞生了！

* * *

# 简介

lit tra(/ˇlit . te . ra/)是一个非常简单的机制，它接受一个对象，该对象的键代表一种语言，值代表该语言中的特定字符串，并且只返回活动语言的字符串。例如，您将句子“这是一个示例”作为“en_US”键的值，并对每种语言重复同样的操作。

```
{  "en_US":  "This is an example",  "de_DE":  "Das ist ein Beispiel",  "pl_PL":  "To przykład"  } 
```

Enter fullscreen mode Exit fullscreen mode

# 设置

现在我们知道了核心概念是如何工作的，我们可以设置 Littera。要使用这个库，您必须用上下文包装您的组件，并提供活动语言。为此，我们将使用 LitteraProvider。你可以传给它 3 个道具:

*   语言-活动语言(字符串)
*   预设-全局翻译集(对象)
*   setLanguage -以编程方式改变活动语言的函数(函数)

我们将以 create-react-app 为例。在你进入代码之前，记得用 npm 或者 yarn 安装 Littera。

`npm install react-littera`
T1】

让我们开始，在 App.js 文件中导入提供程序，包装所有组件，如下所示。

```
import React, { useState } from "react";
import ReactDOM from "react-dom";

import LitteraProvider from "react-littera";
function App() {
  const [language, setLanguage] = useState("en_US");

  return (
    <div className="App">
      <LitteraProvider language={language} setLanguage={setLanguage}>
        <YourComponents />
      </LitteraProvider>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode

就这样，Littera 就可以用在您的组件中了。

我建议您使用 Redux 来处理活动语言状态。

# 用法

现在让我们创建一个示例组件来展示 Littera 的强大功能。我们的组件将是一个按钮，点击将切换语言。

首先，我们创建一个返回按钮的基本功能组件。

```
import React from 'react';

const SuperButton = () => {
    return <button>Example</button>;
}

export default SuperButton; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们必须导入 useLittera 钩子，用我们的翻译声明一个对象并使用钩子。

```
import React from 'react';
import { useLittera } from 'react-littera';

const translations = {
    example: {
        en_US: "Example",
        de_DE: "Beispiel",
        pl_PL: "Przykład"
    }
};

const SuperButton = () => {
     const [translated, language, setLanguage] = useLittera(translations);

     const switchLanguage = () => setLanguage("de_DE");

     return <button onClick={switchLanguage}>{translated.example}</button>;
}

export default SuperButton; 
```

Enter fullscreen mode Exit fullscreen mode

### 全球翻译

如上所述，我们可以将一个预置传递给 LitteraProvider。它可以像 JSS 处理主题一样使用。只需用预设属性创建一个翻译常量函数并返回一个对象，然后使用该属性重用现有的预设。

```
const translations = preset => ({
    example: {
        en_US: `This is an ${preset.example}`,
        de_DE: `Das ist ein ${preset.example}`,
        pl_PL: `To ${preset.example}`
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 钩子还是 HOC？

Littera 支持挂钩和 hoc。两者都可以在相同的上下文中使用，但是让我们现实一点，类组件几乎已经灭绝了。😅

无论如何，在 CodeSandbox 上可以找到两者的例子。

霍克:[https://codesandbox.io/s/6299pk9r1r](https://codesandbox.io/s/6299pk9r1r)T2[https://codesandbox.io/s/ywl2lm8r4z](https://codesandbox.io/s/ywl2lm8r4z)

# 结论

在大型应用程序中管理翻译可能有更好的方法，但是如果你正在构建一个单页程序，Littera 将满足你的需要。如果你想拥有更多的观众，试试 Littera 吧。💖

你可以把源代码放在 GitHub 上，也许我们可以一起消灭一些 bug！
[https://github.com/DRFR0ST/react-littera](https://github.com/DRFR0ST/react-littera)