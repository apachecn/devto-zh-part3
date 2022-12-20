# 使用 React with hooks 的动态表单字段

> 原文：<https://dev.to/email2vimalraj/dynamic-form-fields-using-react-35ci>

我试图创建一个表单，让用户添加尽可能多的输入字段，还可以删除任何特定的输入字段。

添加一个字段工作正常，但是，删除总是删除最后一个字段，而不是我想要删除的字段。

```
function App() {
  const [fields, setFields] = useState([{ value: null }]);

  function handleChange(i, event) {
    const values = [...fields];
    values[i].value = event.target.value;
    setFields(values);
  }

  function handleAdd() {
    const values = [...fields];
    values.push({ value: null });
    setFields(values);
  }

  function handleRemove(i) {
    const values = [...fields];
    values.splice(i, 1);
    setFields(values);
  }

  return (
    <div className="App">
      <h1>Hello CodeSandbox</h1>

      <button type="button" onClick={() => handleAdd()}>
        +
      </button>

      {fields.map((field, idx) => {
        return (
          <div key={`${field}-${idx}`}>
            <input
              type="text"
              placeholder="Enter text"
              onChange={e => handleChange(idx, e)}
            />
            <button type="button" onClick={() => handleRemove(idx)}>
              X
            </button>
          </div>
        );
      })}
    </div>
  );
} 
```

代码沙箱在这里:[https://codesandbox.io/s/q555kp8jj?fontsize=14](https://codesandbox.io/s/q555kp8jj?fontsize=14)

同样的事情也适用于类组件，下面是代码沙箱:[https://codesandbox.io/s/wznq443xl?fontsize=14](https://codesandbox.io/s/wznq443xl?fontsize=14)

要重现该问题，请按照下列步骤操作:

*   在第一个输入文本字段中添加一些文本
*   点击 **+** 按钮，添加新的文本字段
*   点击第一个文本字段旁边的 **X** 按钮

您可以看到，上述步骤删除了第 2 个字段，而不是第 1 个字段。

当我检查时，当我点击 remove 按钮时,`fields`状态正在正确更新。好心帮忙。

**更新**:制作`input`到**受控**组件后问题已解决。感谢一位 reddit 用户发现了这个问题。更多[参见此处](https://www.reddit.com/r/reactjs/comments/b0mo2d/help_needed_dynamic_form_fields_using_react_with/eigmygl/?context=3)