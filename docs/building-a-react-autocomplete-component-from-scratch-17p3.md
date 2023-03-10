# 从头开始构建 React 自动完成组件

> 原文：<https://dev.to/kris/building-a-react-autocomplete-component-from-scratch-17p3>

[![](img/80c8ae0763e102895c05fa08fd9c5328.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L0MaLp4Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ao_OVgC-Mxxx1mx96-ZZ42w.png)

自动完成为应用程序的访问者提供了卓越的用户体验。这也有助于在搜索时避免多余的拼写错误。

在本文中，我们将从头开始构建一个 React 自动完成组件。

然后，您可以将它分享到您的 [**位**](https://bitsrc.io) 收藏中，并在您的所有应用程序中使用它！它将成为一个可重复使用的乐高积木，你可以用它来更快地建造和分享。

[位组件发现和协作](https://bitsrc.io)

<figure>[![](img/084266d0791a37cce1f65ad9d7725683.png)](https://bitsrc.io/components) 

<figcaption>示例:用位反应微调器——选择、学习、使用</figcaption>

</figure>

您可以通过 API 或数据库从 AJAX 请求中获得选项。您可以选择显示的选项。把它们放入一个数组。

这是最终产品的样子。

### 我们在建造什么

该项目的代码可在 codesandbox 的[处获得，也嵌入在帖子的末尾。](https://codesandbox.io/s/5222rxox3k)

[![](img/f5c5e844abb83e27cd6631836262ac8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yn8Fjw4c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AO3rE_iu15F0ubwZoCw95wQ.gif)T3】

```
App
  >Autocomplete 
```

Autocomplete 组件有一个容器组件应用程序，它将选项传递给数组中的 Autocomplete 组件。

```
import React, { Component } from 'react';
import './App.css';
import Autocomplete from './Autocomplete';
const App = () => {
  return (
    <div className="App">
      <Autocomplete
        options={[
          'Papaya',
          'Persimmon',
          'Paw Paw',
          'Prickly Pear',
          'Peach',
          'Pomegranate',
          'Pineapple'
        ]}
      />
    </div>
  );
};

export default App; 
```

### 自动完成组件

这是一切发生的地方。我在 codepen 上从[这里抓取了最初的搜索框。清空默认 index.css 并用新的 css 填充。](https://codepen.io/isaacchansky/pen/Jqhma)

下面是初始的自动完成组件的样子。

```
import React, { Component } from 'react';

export class Autocomplete extends Component {
  render() {
    return (
      <React.Fragment>
        <div className="search">
          <input type="text" className="search-box" />
          <input type="submit" value="" className="search-btn" />
        </div>
      </React.Fragment>
    );
  }
}

export default Autocomplete; 
```

### 数据验证

没有选项，自动完成组件是没有用的。选项需要作为数组进行验证，以便快速捕捉数据类型错误。React PropTypes 做了同样的事情。

他们还可以将属性标记为强制属性或设置默认值。

```
import PropTypes from 'prop-types'; 
```

使用
可以在类内将选项标记为强制和数组类型

```
static propTypes = {
  options: PropTypes.instanceOf(Array).isRequired;
}; 
```

如果您没有从父组件传递选项，它将在控制台上抛出一个错误。

[![](img/dd4de2a5bff8e4c7b2c973627abf492e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bElLtuzj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1022/1%2ARbVhYAQCoU7E7u-Gm-yrqw.png)

这是目前为止我们代码的输出…

[![](img/29396d2ccebc87d69b7d122671260944.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YsGAxwSy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/802/1%2APyFfMXKAA_7B7c7h6ZV4Zw.png)

它什么也没做。

### 用户输入

用户可以:

*   用向上/向下箭头键改变激活选项。
*   通过单击鼠标或按回车键来选择选项。

**所需方法:**

*onChange* :输入改变时检查选项

*onKeyDown* :检查回车键和箭头键

*值* : onChange 阻止用户在输入字段中输入，所以我们必须这样填充值。

**所需状态:**

*显示选项*:布尔型(真/假)

*filteredOptions* :匹配用户输入的项目数组。

*activeOption* :当前选中项目在 *filteredOptions* 中的位置，索引(数字)。

***optionList*** 将渲染 JSX 与选项(在

会有很多地方用到 this.state.filteredOptions 或者 this.onChange，我喜欢名字简短，所以对所有的状态和方法都用了 object destructuring。

下面是自动完成现在的样子。

```
import React, { Component } from 'react';
import PropTypes from 'prop-types';

export class Autocomplete extends Component {
  static propTypes = {
    options: PropTypes.instanceOf(Array).isRequired
  };
  state = {
    activeOption: 0,
    filteredOptions: [],
    showOptions: false,
    userInput: ''
  };
  render() {
    const {
      onChange,
      onChange,
      onKeyDown,
      userInput,
      state: { activeOption, filteredOptions, showOptions, userInput }
    } = this;
    let optionList;
    return (
      <React.Fragment>
        <div className="search">
          <input
            type="text"
            className="search-box"
            onChange={onChange}
            onKeyDown={onKeyDown}
            value={userInput}
          />
          <input type="submit" value="" className="search-btn" />
          {optionList}
        </div>
      </React.Fragment>
    );
  }
}

export default Autocomplete; 
```

**onChange**

当用户在输入字段中进行更改时，我们希望发生一些事情。

```
onChange = (e) => {
    const { options } = this.props;
    const userInput = e.currentTarget.value;

const filteredOptions = options.filter(
      (option) => option.toLowerCase().indexOf(userInput.toLowerCase()) > -1
    );

this.setState({
      activeOption: 0,
      filteredOptions,
      showOptions: true,
      userInput
    });
  }; 
```

它从道具中获取选项，选项用于建议。此外，将 userInput 设置为目标值(输入字段)。

它将选项过滤到 filteredOptions，过滤条件是数组中值的 userInput 子字符串。

filteredOptions 中的第一项(索引 0)是默认选定项。该列表直接影响 optionList。

**onClick**

```
onClick = (e) => {
    this.setState({
      activeOption: 0,
      filteredOption: [],
      showOptions: false,
      userInput: e.currentTarget.innerText
    });
  }; 
```

它关闭建议，并将被单击元素的文本放入输入字段。

**叔叔家**

它处理 *keyDown* 事件。

Return 键(13)与 click 事件做同样的事情，选择项目并在输入字段中输入一个值。

向下箭头(40)选择较低的选项。向上箭头(38)选择上部选项。但它不会低于最后一个选项或高于第一个选项。

```
onKeyDown = (e) => {
    const { activeOption, filteredOptions } = this.state;

if (e.keyCode === 13) {
      this.setState({
        activeOption: 0,
        showSuggestions: false,
        userInput: filteredOptions[activeOption]
      });
    } else if (e.keyCode === 38) {
      if (activeOption === 0) {
        return;
      }

this.setState({ activeOption: activeOption - 1 });
    } else if (e.keyCode === 40) {
      if (activeOption - 1 === filteredOptions.length) {
        return;
      }

this.setState({ activeOption: activeOption + 1 });
    }
  }; 
```

如果你使用 React 开发工具，你可以看到那里的变化…

[![](img/eb9dd5be9745ec3333ea67fab896cf10.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oZbkcJZu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANI61wKdAoFt2hX6YseXmPw.gif)

现在，是时候将这些状态更改传递到界面上，并让用户选择一个选项了。

### 选项列表

*选项列表*是最终用户的选项选择界面。

```
let optionList;
    if (showOptions && userInput) {
      if (filteredOptions.length) {
        optionList = (
          <ul className="options">
            {filteredOptions.map((optionName, index) => {
              let className;
              if (index === activeOption) {
                className = 'option-active';
              }
              return (
                <li className={className} key={optionName} onClick={onClick}>
                  {optionName}
                </li>
              );
            })}
          </ul>
        );
      } else {
        optionList = (
          <div className="no-options">
            <em>No Option!</em>
          </div>
        );
      }
    } 
```

showOptions 为 true，并且输入区域不为空。

它遍历 filteredOptions 来创建一个列表。此外，active option 获取 option-active className。这是使用 index.css 中的 css 样式化的。

这是它的样子。

[![](img/f5c5e844abb83e27cd6631836262ac8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yn8Fjw4c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AO3rE_iu15F0ubwZoCw95wQ.gif)

如果输入字段值与任何内容都不匹配，则表示没有选项。

[![](img/4173f3b16d451d6bfdd85bf82bdfa4b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZPtn-GJO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/763/1%2AaO5QY0MYGkQJd1ncIwFoJw.png)

下面嵌入了 Codesandbox 演示。

[https://medium . com/media/75a 78 e 88 DCD 5d 394137 e9 BC 5080 ab 4 e 8/href](https://medium.com/media/75a78e88dcd5f394137e9bc5080ab4e8/href)

### 结论

因此，我们创建了一个您的用户可以享受的自动完成组件，为您的产品创造更好的体验。这将有助于他们减少混乱和错误，并快速浏览您的应用程序。

欢迎随时评论，有什么问题问我！我很乐意帮忙:)

### 了解更多

*   [在 React 中加快开发的 5 种工具](https://blog.bitsrc.io/5-tools-for-faster-development-in-react-676f134050f2)
*   [2019 年你应该知道的 11 个 React UI 组件库](https://blog.bitsrc.io/11-react-component-libraries-you-should-know-178eb1dd6aa4)
*   【2019 年 11 个 JavaScript 动画库

* * *