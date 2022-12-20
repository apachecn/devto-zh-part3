# Web 组件:从零到英雄，第三部分

> 原文：<https://dev.to/thepassle/web-components-from-zero-to-hero-part-three-3c5h>

# Web 组件:从零到英雄，第三部分

## Web 组件英雄同💥文学元素💥

> *   [x] restatement
> *   [attributes and attributes]
> *   [Life cycle and re-rendering]
> *   [Conclusion]

Lit-html 和 LitElement 终于发布了官方版本(分别为 1.0 和 2.0)，这是总结 Web 组件的好时机:从零到英雄博客系列。我希望你在阅读这些博客时会觉得它们很有用；他们写得很棒，我非常感谢所有的反馈和回应！

> 啊？2.0 版本？Lit-element 已经从`@polymer/lit-element`名称空间中移出，简化为:`lit-element`。`lit-element` npm 包之前被其他人拥有，并且已经有了一个版本，因此有了 2.0 版本。

我们开始吧！

在上一篇博文中，我们学习了如何实现 lit-html 来处理 web 组件的模板。让我们快速回顾一下 lit-html 和 lit-element 之间的区别:

*   Lit-html 是一个*渲染*库。它提供了*什么*和*如何*。
*   LitElement 是一个 web 组件基类。它提供了时的*和*时的*。*

我还想强调，LitElement 是*而不是*一个框架。它只是一个扩展了`HTMLElement`的基类。我们可以将 LitElement 看作是标准`HTMLElement`类的*增强*，它将负责我们的属性和属性管理，并为我们提供更精细的渲染管道。

让我们快速看一下用 LitElement 重写的`to-do-item`组件。你可以在这里找到完整的演示[，在](https://stackblitz.com/edit/web-components-zero-to-hero-part-three) [github](https://github.com/thepassle/webcomponents-from-zero-to-hero/tree/part-three) 页面:

```
import { LitElement, html, css } from 'https://unpkg.com/lit-element@latest/lit-element.js?module';

class TodoItem extends LitElement {
    static get properties() {
        return {
            text: { 
                type: String,
                reflect: true
            },
            checked: { 
                type: Boolean, 
                reflect: true 
            },
            index: { type: Number }
        }
    }

    constructor() {
        super();
        // set some default values
        this.text = '';
        this.checked = false;
    }

    _fire(eventType) {
        this.dispatchEvent(new CustomEvent(eventType, { detail: this.index }));   
    }

    static get styles() {
      return css`
      :host {
        display: block;
        font-family: sans-serif;
      }

      .completed {
        text-decoration: line-through;
      }

      button {
        cursor: pointer;
        border: none;
      }
      `;
    }

    render() {
        return html`
            <li class="item">
                <input 
                    type="checkbox" 
                    ?checked=${this.checked} @change=${() => this._fire('onToggle')}>
                </input>
                <label class=${this.checked ? 'completed' : ''}>${this.text}</label>
                <button @click=${() => this._fire('onRemove')}>❌</button>
            </li>
        `;
    }
} 
```

### 💅属性和特性

> *   [x] restatement
> *   [x] attributes and attributes
> *   [Life cycle and re-rendering]
> *   [Conclusion]

让我们直接进入它。您可能注意到的第一件事是，我们所有的 setter 和 getter 都不见了，取而代之的是 LitElement 的静态属性 getter。这是*伟大的*，因为我们已经抽象出了许多锅炉板代码，而是让 LitElement 来处理它。

让我们看看这是如何工作的:

```
static get properties() {
    return {
        text: { 
            type: String,
            reflect: true
        },
        checked: { 
            type: Boolean, 
            reflect: true 
        },
        index: { type: Number }
    }
} 
```

我们可以使用静态属性 getter 来声明我们可能需要的任何属性，甚至可以向它们传递一些选项。在这段代码中，我们已经设置了一个`text`、`checked`和`index`属性，并且我们还将*将*、`text`和`checked`属性反映到属性中。就这样。记得以前有多少工作量吗？我们有整整一章[致力于将属性反映到属性上！](https://github.com/thepassle/webcomponents-from-zero-to-hero#-reflecting-properties-to-attributes)

我们甚至可以指定*如何*我们希望属性被反映:

```
static get properties() {
    return {
        text: { 
            type: String,
            reflect: true,
            attribute: 'todo'
        }
    }
} 
```

将文本属性在我们的 DOM 中反映为以下属性:

```
<to-do-item todo="Finish blog"></to-do-item> 
```

> 您还在困惑如何将属性映射到属性吗？考虑重新访问本博客系列的第一部分来赶上进度。

此外，也许最重要的是，静态属性 getter 将对更改做出反应，并在属性发生更改时触发 rerender。我们不再需要手动调用渲染函数来更新，我们只需要更新一个属性，LitElement 就会替我们完成所有的工作。

> ✨ *嘿！听着！*
> 
> 你*仍然可以*使用自定义的 getters 和 setters，但是你必须手动调用`this.requestUpdate()`来触发一个 rerender。自定义的 getters 和 setters 对于计算属性很有用。

### ♻️生命周期和重新渲染

> *   [x] restatement
> *   [x] attributes and attributes
> *   [x] Lifecycle and re-rendering
> *   [Conclusion

最后，我们来看看我们的`to-do-app`组件:

```
import { LitElement, html } from 'lit-element';
import { repeat } from 'lit-html/directives/repeat';
import './to-do-item.js';

class TodoApp extends LitElement {
    static get properties() {
        return {
            todos: { type: Array }
        }
    }

    constructor() {
        super();
        this.todos = [];
    }

    firstUpdated() {
        this.$input = this.shadowRoot.querySelector('input');
    }

    _removeTodo(e) {
        this.todos = this.todos.filter((todo, index) => {
            return index !== e.detail;
        });
    }

    _toggleTodo(e) {
        this.todos = this.todos.map((todo, index) => {
            return index === e.detail ? {...todo, checked: !todo.checked} : todo;
        });
    }

    _addTodo(e) {
        e.preventDefault();
        if(this.$input.value.length > 0) {
            this.todos = [...this.todos, { text: this.$input.value, checked: false }];
            this.$input.value = '';
        }
    }

    static get styles() {
      return css`
         :host {
             display: block;
             font-family: sans-serif;
             text-align: center;
         }
         button {
             border: none;
             cursor: pointer;
         }
         ul {
             list-style: none;
             padding: 0;
         }
          `;
    }

    render() {
        return html`
            <h1>To do</h1>
            <form id="todo-input">
                <input type="text" placeholder="Add a new to do"></input>
                <button @click=${this._addTodo}>✅</button>
            </form>
            <ul id="todos"> ${repeat(this.todos, 
                   (todo) => todo.text, 
                   (todo, index) => html`
                     <to-do-item 
                       .checked=${todo.checked} .index=${index} .text=${todo.text} @onRemove=${this._removeTodo} @onToggle=${this._toggleTodo}>    
                    </to-do-item>`
                  )} </ul>
        `;
    }
}

window.customElements.define('to-do-app', TodoApp); 
```

你会注意到我们稍微改变了一下我们的功能。我们这样做是因为为了让 LitElement 获得更改并触发重新呈现，我们需要*不变地*设置数组或对象。您*仍然可以*使用可变模式来更改嵌套的对象属性或数组中的对象，但是您必须通过调用`this.requestUpdate()`来手动请求重新呈现，如下所示:

```
_someFunction(newValue) {
    this.myObj.value = newValue;
    this.requestUpdate();
} 
```

这就把我们带到了 LitElement 的生命周期。值得注意的是，`LitElement` *扩展了* `HTMLElement`，这意味着我们仍然可以访问标准的生命周期回调，如`connectedCallback`、`disconnectedCallback`等。

此外，LitElement 自带一些生命周期回调。你可以在这里看到 LitElement 生命周期[的完整例子。](https://stackblitz.com/edit/open-wc-lit-demos?file=02-intermediate%2F03-lifecycle.js)

### `shouldUpdate()`

您可以实现`shouldUpdate()`来控制当属性值改变或调用 requestUpdate()时是否应该进行更新和呈现。当你*不想*重新招标时，这很有用。

### `firstUpdated()`

`firstUpdated`叫做当...你的元素已经在第一时间更新了。这个方法对于在组件中查询 dom 很有用。

### `updated()`

在元素更新和重新呈现后立即调用。您可以通过 DOM APIs 实现它来执行更新后的任务，例如，聚焦一个元素。在这个方法中设置属性将*而不是*触发另一个更新。

正如我之前提到的，你仍然可以实现`connectedCallback()`和`disconnectedCallback()`。

### 结论

> *   [x] restatement
> *   [x] attributes and attributes
> *   [x] Life cycle
> *   [x] conclusion

如果你一路走来。恭喜你！你现在是 web 组件的超级英雄。我希望这个博客系列对您有所帮助和启发，并且当您需要记住一些关于 web 组件的知识时，它可以作为一个参考。

如果你对开始使用 Web 组件感兴趣，一定要看看 [open-wc](https://www.open-wc.org) 。Open-wc 提供的建议包括任何介于两者之间的东西:开发、林挺、测试、工具、演示、发布和自动化，并且将帮助你立即开始。

如果你想与 lit-html/LitElement 社区保持同步，我推荐你去看看 [awesome-lit-html](https://github.com/web-padawan/awesome-lit-html) repo，或者加入 [Polymer slack](https://polymer.slack.com) 。

如果您有任何问题，请随时通过 [twitter](https://www.twitter.com/passle_) 联系我。