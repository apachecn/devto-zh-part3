# 使用 React 钩子在模态中形成-错误和经验教训

> 原文：<https://dev.to/email2vimalraj/form-in-modal-using-react-hooks-mistakes-and-lesson-learnt-19b4>

最近，我在**模态**框中构建**表单**时偶然发现了一个问题。我想分享这一经验，并相信它可能会有所帮助。

## 模态和门户

我想创建一个可以显示一些内容或形式的模型。在 React 中创建模态的最佳方式是使用[门户](https://reactjs.org/docs/portals.html)。因为，模态应该总是 DOM 层次结构之外的一个单独的组件。传送门让你做到这一点。请通读 react 的文档，以了解更多关于该门户及其优势的信息。另外，这个[帖子](https://www.nearform.com/blog/exploring-react-portals/)可能会帮助你更好地理解。

所以，我们知道什么是传送门！让我们构建我们的`Modal`组件，并呈现为`Portal`。我正在使用 [create-react-app](https://facebook.github.io/create-react-app/) CLI 工具来生成我的 react 项目。在创建门户之前，让我们确保我们的`./public/index.html`具有外部 DOM 层次结构。

之前:

```
<body>
  <noscript>
    You need to enable JavaScript to run this app.
  </noscript>
  <div id="root"></div>
</body> 
```

之后:

```
<body>
  <noscript>
    You need to enable JavaScript to run this app.
  </noscript>
  <div id="root"></div>
  <div id="modal-root"></div>
</body> 
```

我添加了另一个 id 属性值为`modal-root`的`div`。那是我们渲染所有情态动词的地方。

让我们在`Portal`的支持下创建我们的`Modal`组件。我在`components/modal/index.js`下创建了这个

```
import React, { useEffect } from "react";
import { createPortal } from "react-dom";
import { StyledModal } from "./style";

// Creates a portal outside the DOM hierarchy
function Portal({ children }) {
  const modalRoot = document.getElementById("modal-root"); // A div with id=modal-root in the index.html
  const element = document.createElement("div"); // Create a div element which will be mounted within modal-root

  // useEffect bible: https://overreacted.io/a-complete-guide-to-useeffect/
  useEffect(() => {
    modalRoot.appendChild(element);

    // cleanup method to remove the appended child
    return function cleanup() {
      modalRoot.removeChild(element);
    };
  });

  return createPortal(children, element);
}

// A modal component which will be used by other components / pages
function Modal({ children, toggle, open }) {
  return (
    <Portal>
      {open && (
        <StyledModal.ModalWrapper onClick={toggle}>
          <StyledModal.ModalBody onClick={event => event.stopPropagation()}>
            <StyledModal.CloseButton onClick={toggle}>
              &times;
            </StyledModal.CloseButton>
            {children}
          </StyledModal.ModalBody>
        </StyledModal.ModalWrapper>
      )}
    </Portal>
  );
}

export default Modal; 
```

这里，`Portal`方法创建门户，并使用`useEffect`钩子将`div`元素附加到`modal-root`元素，并移除 while `unmounting`。这是我所面临的问题，但请等待我们发现问题。

`StyledModal`是样式化的组件，代码如下(在`/components/modal/style.js`下创建):

```
import styled from "styled-components";

const ModalWrapper = styled.div`
  position: fixed;
  z-index: 1;
  padding-top: 100px;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
`;

const ModalBody = styled.div`
  background-color: #fefefe;
  margin: auto;
  padding: 20px;
  border: 1px solid #888;
  width: 30%;
`;

const CloseButton = styled.span`
  color: #aaaaaa;
  float: right;
  font-size: 28px;
  font-weight: bold;

  &:hover,
  &:focus {
    color: #000;
    text-decoration: none;
    cursor: pointer;
  }
`;

export const StyledModal = {
  ModalWrapper,
  ModalBody,
  CloseButton
}; 
```

如果你注意到我们的`Modal`组件，有 3 个道具:

*   open:决定是否显示模态的布尔属性。
*   toggle:将`open`从`true`切换到`false`的方法道具，反之亦然。
*   children:要在 modal 中呈现的子组件。这通常是模态内容。

为了切换`Modal's`状态，让我们创建一个新的定制钩子，并将其命名为`useToggle`。我正在`src`目录中创建`useToggle.js`:

```
import { useState, useCallback } from "react";

// Toggles between true or false
function useToggle(initialValue = false) {
  const [toggle, setToggle] = useState(initialValue);

  return [toggle, useCallback(() => setToggle(status => !status), [])];
}

export default useToggle; 
```

用户可以在`true`或`false`之间切换。这将用于我们的`App`组件。

让我们重写`index.js`中的`App`组件:

```
function App() {
  const [open, setOpen] = useToggle(false);

  return (
    <div className="App">
      <h1>Hello CodeSandbox</h1>
      <button type="button" onClick={() => setOpen()}>
        Open Modal
      </button>

      {open && (
        <Modal open={open} toggle={setOpen}>
          <h1>Hello Modal</h1>
        </Modal>
      )}
    </div>
  );
} 
```

`useToggle`钩子通过一个名为`open`的参数给出`toggle`的状态，而`setOpen`让你切换`open`的值。代码的其余部分不言自明。

当你跑步的时候，你看不到任何问题。太好了！我们已经建立了显示航向的模型。让我们扩展它，用一个输入框向我们的模态组件添加一个表单。

我在`form`下用一个`input`元素修改了我的`App`组件。

```
function App() {
  const [open, setOpen] = useToggle(false);
  const [username, setUsername] = useState("");

  const onChangeUsername = e => setUsername(e.target.value);

  return (
    <div className="App">
      <h1>Hello CodeSandbox</h1>
      <button type="button" onClick={() => setOpen()}>
        Open Modal
      </button>

      {open && (
        <Modal open={open} toggle={setOpen}>
          <h1>Hello Modal</h1>

          <form onSubmit={e => e.preventDefault()}>
            <input
              type="text"
              name="username"
              value={username}
              onChange={e => onChangeUsername(e)}
            />
          </form>
        </Modal>
      )}
    </div>
  );
} 
```

现在运行代码并打开模态。尝试在显示的输入框中输入多个字符。天哪，它坏了！！！对于每个字符，模态重新呈现。你看到了吗？

好吧，现在怎么解决？我花了大量的时间来理解这个问题。在 reddit 用户和`useEffect` [圣经](https://overreacted.io/a-complete-guide-to-useeffect/)的帮助下，我在我们的`Portal`组件中发现了一个问题。

在我们的`Portal`组件中，我们必须将`div`元素放入状态，并添加`modal-root`和`div`作为`useEffect`的依赖项。这样就不会重新渲染了。让我们这样做:

```
function Portal({ children }) {
  const modalRoot = document.getElementById("modal-root"); // A div with id=modal-root in the index.html
  const [element] = useState(document.createElement("div")); // Create a div element which will be mounted within modal-root

  // useEffect bible: https://overreacted.io/a-complete-guide-to-useeffect/
  useEffect(() => {
    modalRoot.appendChild(element);

    // cleanup method to remove the appended child
    return function cleanup() {
      modalRoot.removeChild(element);
    };
  }, [modalRoot, element]);

  return createPortal(children, element);
} 
```

现在运行，并尝试导致问题的相同方法。瞧啊。现在成功了。

所以要记住，确保`useEffect`正确设置了依赖关系，以避免重新渲染。

示例代码沙箱可在此处找到:

[https://codesandbox.io/embed/1z2p4wqyjq](https://codesandbox.io/embed/1z2p4wqyjq)

我希望我的经历能帮助到一些人。如果你喜欢这篇文章，请点击**喜欢**按钮和**分享**。