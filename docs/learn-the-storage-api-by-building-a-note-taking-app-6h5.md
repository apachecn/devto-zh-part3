# 通过构建笔记应用程序了解存储 API

> 原文：<https://dev.to/healeycodes/learn-the-storage-api-by-building-a-note-taking-app-6h5>

让我们通过用一些简单的 HTML、CSS 和 JavaScript 构建一个小小的笔记应用来了解`window.localStorage`。我们将编写一个小的 UI，并将用户的注释自动保存到他们的浏览器中。我的同事把他所有的片段都保存在这样的东西上。我无法相信他从 50-60 行代码中获得的收益！

大多数客户端存储交互过去都涉及到操作 cookies，但幸运的是，我们现在有了现代 API。使用 cookies，用户发送每个请求中存储的所有内容，这意味着对双方来说性能都更差。

输入:`localStorage`。在`window`对象上可用的键/值 API。与在页面会话期间持续的`sessionStorage`不同，`localStorage`没有到期时间。

所有的键和值都被转换成字符串。让我们添加、更新和删除一个键，然后尝试从一个空键中读取。这是控制台输入，所以返回值跟在命令后面。

```
// Add key - or overwrite existing key
window.localStorage.setItem('foo', 'bar')
> undefined

// Read a value from a key
window.localStorage.getItem('foo')
> "bar"

// Delete key - deleting empty yields same return value
window.localStorage.removeItem('foo')
> undefined

// Read from a deleted or unknown key
window.localStorage.getItem('foo')
> null 
```

与 localStorage 交互的其他方式包括清除所有键和遍历现有键。

```
// Get the read-only length
window.localStorage.length
> 6

// Get the nth key
window.localStorage.key(0)
> "algoliasearch-client-js"

// Delete all keys
window.localStorage.clear()
> undefined

// Iterate through all keys
for (let i = 0; i < window.localStorage.length; i++) {
    console.log(window.localStorage.key(i));
} 
```

您可以在这里的开发人员控制台中尝试其中的一些方法。在我写的时候，DEV 为我存储了下面的 localStorage 键。但是请确保**不要删除**任何这些内容！`algoliasearch-client-js`、`config_body_class`、`current_user`、`https://dev.to/new`、`pusherTransportTLS`。

#### App

这是我们要做的。一个小而实用的笔记 app。除了间距，它很少使用样式([发送一个拉请求](https://github.com/healeycodes/tiny-note-taker))。重要的是应用程序如何与客户端的浏览器存储进行通信。

[![](img/dd24213d8369821bdc887eb22b41c950.png)](https://healeycodes.github.io/tiny-note-taker/)

希望你已经想到了我们需要的`localStorage`方法。让我们使用一个现成的 HTML 模板来命名我们将要使用的函数。

```
<h5>Tiny Note Taker</h5>
<div>
    <button onclick="newNote()">New</button>
        <select id="notes" onchange="changeNote()"></select>
        <button onclick="deleteNote()">Delete</button>
</div>
<textarea id="editor" rows="10" onkeyup="saveNote()"
    onkeydown="checkEmpty()" autofocus></textarea>
<script src="./index.js"></script> 
```

花点时间想想这些函数与我们刚才看到的存储方法有什么关系，以及它们将如何操作 DOM。

* * *

`newNote()`提示用户输入音符名称并创建它。

当所选元素改变时，切换到新音符。

`deleteNote()`删除当前选中的便笺。

`saveNote()`将当前选中的便笺保存为用户键入的内容。

这个不太明显。

如果用户在创建新便笺之前到达页面并开始输入，名称将是空字符串`""`。相反，当他们键入*时，但在保存笔记之前*命名当前笔记`untitled`。我们通过将逻辑分成两个事件来简化我们的逻辑。`onkeydown`检查是否有空便笺，并在必要时进行修复。这意味着`onkeyup`可以简单地保存笔记，而不需要任何额外的检查。由此，`checkEmpty()`。

**提醒**:关键事件[几乎都是](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent#Usage_notes)按这个顺序`onkeydown -> onkeypress -> onkeyup`叫的，他们都有自己的位置！

让我们从基本案例开始。当用户键入时保存一个笔记。因为我们已经将脚本 include 放在了 HTML 的底部，所以我们可以获取对我们需要的两个元素的引用，而无需等待任何文档加载事件。

```
const notes = document.querySelector('#notes');
const editor = document.querySelector('#editor'); 
```

太好了。这些引用将在这个脚本的生命周期内持续，因为我们永远不会删除这些元素。接下来，如果用户没有先点击 New 按钮就输入了内容，我们需要将名为空的便笺命名为 untitled。

```
/**
 * Check for empty note title.
 */
function checkEmpty() {
    if (notes.length === 0) {
        const untitled = document.createElement('option');
        untitled.innerText = 'untitled';
        notes.appendChild(untitled);
    }
} 
```

这里我们用 JavaScript 创建了一个选项元素，并使它在 select 下拉列表中可用。我们还不需要做任何保存，这将在几毫秒后当用户将手指从当前键上移开时发生。

几毫秒后。

```
/**
 * Save editor text to storage under the current note.
 */
function saveNote() {
    window.localStorage.setItem(notes.value, editor.value);
} 
```

还有`localStorage.setItem`！我们知道那有什么用。它将保存注释文本作为值，注释标题(`"untitled"`)作为键。现在我们有一个问题要解决。如果用户离开后又回来，选择下拉列表将为空。更糟糕的是:他们会重写他们的`"untitled"`笔记。

当我们的脚本运行时，就在我们获取对这些元素的引用之后，让我们用所有用户保存的注释填充 select 下拉列表。

```
const notes = document.querySelector('#notes');
const editor = document.querySelector('#editor');

// Load user's saved notes
for (let i = 0; i < window.localStorage.length; i++) {
    const newNote = document.createElement('option');
    newNote.innerText = window.localStorage.key(i);
    notes.appendChild(newNote);
}
changeNote(); 
```

这里我们使用了`localStorage`的只读属性`length`以及它的方法`key()`。所有保存的注释都被加载到选择下拉列表中。我们让 HTML 作为我们的状态运行。这样我们就不需要跟踪 JavaScript 中的任何注释。我们依靠简单的函数将 DOM 链接到存储 API。

在最后一个代码片段的底部，我们看到了`changeNote()`，它将当前选中的注释加载到 textarea 元素中，也就是编辑器。这个函数也会在创建一个新注释后被调用，这将清空编辑器。新便笺用空字符串初始化。

```
/**
 * Change editor text to the currently selected note.
 */
function changeNote() {
    editor.value = window.localStorage.getItem(notes.value);
} 
```

存储 API 为我们抽象了繁重的工作。

用户需要一种命名和创建笔记的方法。`newNote()`确实如此。它使用 [Element#insertBefore](https://developer.mozilla.org/en-US/docs/Web/API/Node/insertBefore) 将新注释放在选择框的顶部。在父元素上调用此方法。`var insertedNode = parentNode.insertBefore(newNode, referenceNode);`。这个方法的巧妙之处在于它接受一个空值作为第二个参数。意思是我们可以在空的父元素上调用`insertBefore`！

```
/**
 * Ask the user to name their new note then create it.
 * Add this note to the select div then focus to it.
 */
function newNote() {
    const note = prompt('Name of note?');
    window.localStorage.setItem(note, '');

    const noteElem = document.createElement('option');
    noteElem.innerText = note;
    notes.insertBefore(noteElem, notes.firstChild);

    // Focus this note
    notes.value = note;
    changeNote();
} 
```

我们的笔记应用程序即将完成。所需的最后一个功能是删除注释。当用户完成一个注释时，他们可以选择它，然后点击删除按钮。这个函数最棘手的部分是在选择下拉列表中找到要删除的注释。使用 DOM 作为我们的状态的缺点之一。我们必须遍历每个选项，检查`value`。

```
/**
 * Delete currently selected note
 */
function deleteNote() {
    const note = notes.value;
    window.localStorage.removeItem(note);
    editor.value = '';
    for (let i = 0; i < notes.length; i++) {
        const option = notes[i];
        if (option.value === note) {
            notes.removeChild(option);
        }
    }
} 
```

我们现在有了一个全功能的笔记应用程序，可以通过 CDN 无限扩展。我们使用了强大的[存储 API](https://developer.mozilla.org/en-US/docs/Web/API/Storage) ，并学习了如何使用 DOM 作为应用程序状态。走吧我们。🙌

点击查看最终应用代码[，点击](https://github.com/healeycodes/tiny-note-taker)查看现场版[。](https://healeycodes.github.io/tiny-note-taker/)

* * *

加入我的关于编程和个人成长的[时事通讯](https://buttondown.email/healeycodes)的 150 多人注册！

我发关于科技的微博。