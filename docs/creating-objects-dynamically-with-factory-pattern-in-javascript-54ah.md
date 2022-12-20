# 用工厂模式在 javascript 中动态创建对象

> 原文：<https://dev.to/duranenmanuel/creating-objects-dynamically-with-factory-pattern-in-javascript-54ah>

当创建特征时，我们经常需要基于来自端点或类似动态源的参数来创建实例。在这篇文章中，我将试着*给你一个关于我们如何使用单工厂模式来处理这种情况的想法*。

我们将重构一段代码，使其更具可伸缩性和可理解性，假设我们有这样一个前提:

给定一个元素数据的对象，编写一个算法，允许用户提供一种元素类型来动态地呈现它。另外，请根据需要验证元素:

1.  应当验证电子邮件和密码等通用输入文本字段。
2.  *确保从 textarea 元素中去除换行符。*

一个数据源的例子，姑且称之为 **elementsData.js** :

```
export default {
    elements: {
        email: {
            type: 'email',
            text: 'Email',
            name: 'userEmail'
        },
        summary: {
            type: 'textarea',
            text: 'Summary',
            name: 'summary'
        },
        role: {
            type: 'select',
            text: 'Role',
            name: 'role',
            options: [
                {
                    value: 1,
                    display: 'Software Developer'
                },
                {
                    value: 2,
                    display: 'Designer'
                },
                {
                    value: 3,
                    display: 'Manager'
                },
                ...
            ]
        },
        ...
    }
}; 
```

### 一种不可扩展的方法

现在，我将编写一个伪“解决方案”来动态创建表单元素的类型并验证它们(注意，我将只定义与本文目的相关的方法):

```
import config from './elementsData';

export default class FormElement {

    constructor(type) {
        this.type = type;
        this.elements = config.elements;
    }

    getElementByType() {
        return this.type in this.elements ? this.elements[this.type] : null;
    }

    /* This would validate our email*/
    emailValidator() { ... }

    /* this would remove line breaks from our textareas */
    textareaSanitizer() { ... }

    /* We would use this to bind all the validators and sanitizers events */
    bindEventListeners() { ... }

    renderFormElement() {
        const element = this.getElementByType();

        if (!element) {
            return false;
        }

        switch(this.type) {
            case 'email':
                return `
                    <div class="field-wrapper">
                        <input type="email" name=${element.name} placeholder=${element.text} />
                    </div>
                `;
                break;
            case: 'textarea':
                return `
                    <div class="field-wrapper">
                        <textarea name=${element.name} placeholder=${element.text} />
                    </div>
                `;
            case 'select':
                return `
                    <div class="field-wrapper">
                        <select name=${element.name}> ${element.options.map(option => `
                                <option value=${option.value}> ${option.display} </option>
                            `)} </select>
                    </div>
                `;
        }
    }
} 
```

我们将在我们的 **main.js** 中实例化该类，如下所示:

```
const formElement = new FormElement('email');
formElement.renderFormElement(); 
```

这应该可以，对吧？，我们消费数据，动态地创建元素并验证它们...但是，有些事情我们没有看到，我希望你想一想，在未来，当你或其他人需要添加越来越多的表单元素时，这个类会发生什么？，方法将会增长，我们最终将会拥有一个庞大的方法，有无限的条件、验证方法，我们甚至不要谈论复杂性和可伸缩性。

### 实现单一工厂

工厂模式是一种设计模式，是*creative*组的一部分，它主要处理创建对象的问题，当实例化它的类需要动态时，它也对组织你的代码有很大帮助，因为:

1.  隔离需要创建的对象。
2.  提倡责任较少的小班教学。
3.  将对象创建的责任委托给一个名为“工厂”的类。
4.  通过在入口点接收动态值来创建实例。

这是我创建的一个可视化表示来演示工厂是如何工作的。

[![](img/20ab8d4bd60385cd4949849bce7c879d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8_ro8hWQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/2018-10-07-factory-pattern.png)

现在，我们将从基于上面创建的列表重构我们的“解决方案”开始。

#### 隔离对象，保持单一责任

表单元素 *select* 、 *email* 、 *textarea* 可以通过将涉及它们的逻辑移动到一个名为`/FormElements`或`/FormElementTypes`的文件夹中(您可以给出任何有意义的名称)来轻松隔离:

**/FormElements/email.js**

```
export default class Email {
    constructor(element) {
        this.name = element.name;
        this.text = element.text;
    }

    bindEmailValidator() { ... }

    emailValidator() { ... }

    render() {
        return `
            <div class="email-wrapper">
                <input type="email" name=${this.name} placeholder=${this.text} />
            </div>
        `;
    }
} 
```

注意，我们将验证和绑定方法移动到元素的类中，我们将对其他元素( *textarea* ， *select* ，...).这将允许我们扩展并保持每种类型元素的逻辑隔离。

#### 将对象创建的责任委托给一个名为“工厂”的类

工厂做几件事:

1.  导入元素的类型。
2.  用可用的元素类型创建一个`ELEMENTS`对象。
3.  创建一个静态方法，以便能够通过使用类名直接创建实例。
4.  基于传递的类型动态实例化。

下面是表示这一点的代码:

**factory/elements factory . js**

```
import Email from './FormElements/email';
import Textarea from './FormElements/textarea';
import Select from './FormElements/select';

const ELEMENTS = {
    Email,
    Textarea,
    Select
};

export default class ElementsFactory {
    static createInstance(data) {
        const elementCreator = ELEMENTS[data.type];
        const element = elementCreator ? new elementCreator(data) : null;

        return element;
    }
} 
```

#### 通过在您的入口点(在本例中是我们的 main.js)中接收动态值来创建实例。

这里不需要解释什么复杂的东西，我们只是验证传递的参数是否存在于我们的元素对象中，如果存在，那么我们使用工厂方法`createInstance`创建一个实例，我们传递所需的数据并用`render`方法呈现元素，下面你可以看到代码:

**main.js**

```
import ElementsFactory as Factory from './factories/FormElementsFactory';
import config from './elementsData';

function Init() {
    /* This could be received dynamically */
    const dynamicElement = 'email';

    if (!(dynamicElement in config.elements)) {
        return false;
    }

    const element = Factory.createInstance(config.elements[dynamicElement]);

    element.render();
}

Init(); 
```

最后，这是重构后我们的文件夹结构的示意图

```
├── main.js
├── factories/
│   ├── ElementsFactory.js
└── formElements/
    ├── email.js
    ├── select.js
    └── textarea.js 
```

很酷，对吧？现在，每当您想要添加一个新元素时，只需将其添加到`/formElements`文件夹中，并将其导入到我们的工厂中，这样它就可以被实例化，此外，如果您想要删除一个元素，只需从`/formElements`文件夹中删除导入行和文件。

好了，我想这就是这篇文章的全部内容，希望你能对工厂模式有更多的了解，如果你记得在 twitter 或 facebook 上分享，你也可以订阅我们下面的电子邮件列表。

下一集再见！