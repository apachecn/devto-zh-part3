# 使用纯 CSS 框架的样式组件

> 原文：<https://dev.to/whoisryosuke/styled-components-using-pure-css-framework-3npf>

最近我对无数的设计系统做了大量的研究，我偶然发现了纯 CSS，一个很小的 CSS 框架，只有创建网站的基本模块。我一直想尝试样式化组件，我认为将 CSS 框架转换成 React 组件将是一个很好的锻炼一些技能的机会。纯粹的 CSS 是如此的小和模块化，非常适合翻译成 CSS-in-JS。

最终，我只用了 135 KB 的代码就创建了一个完整的 React 组件系统😳🤯*(未缩小/压缩，但也不包括像 React 和 styled-components 这样的对等依赖)。*

## 如何使用

您现在就可以使用 npm 将它安装到您的项目中[:](https://www.npmjs.com/package/pure-styled-components)

`npm i pure-styled-components`

> 确保安装了所有的对等依赖项:React、styled-components 和 styled-system。`npm i react react-dom styled-components styled-system`

然后只需导入你需要的组件:

```
import React from 'react'
import { Base, Grid, Button, Menu } from 'pure-styled-components'

function App() {
  return(
    <Button>Click Here</Button>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个带有示例博客布局的 CodeSandbox 设置:

[![Screenshot of the blog layout](img/fcbcda7be958bca3e6d30dfc6d56fe67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OagJp5tv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./pure-styled-components-layout-blog.jpg)

如果你对贡献或派生代码感兴趣，请点击这里查看 Github repo。

## 为什么让*又多了一个* React UI 套件？

有一个针对[纯组件](https://www.npmjs.com/package/pure-components)的回购，但是它需要你自己导入 CSS。

为了使组件更加模块化，我选择使用 CSS-in-JS。这允许您只将按钮导入到应用程序中，并拥有显示它所需的一切(而不是为 CSS 计算一些 Webpack 配置)。

## 流程

首先，我概述了组件系统的几个目标:

*   ✨保持简单。就像纯 CSS 一样，bundle 大小要最小，性能要高。
*   **🔧最小 API。**过多的组件和道具会造成混乱，并带来过多的开销。应该是一个易于使用的库，可扩展的用户寻求更多的复杂性。
*   **♻️回收**。尽可能多地使用纯 CSS。升级与 React 冲突的内容。
*   **📚文档**。文档应该从代码中 JSDoc 格式的注释块自动生成。

### 组件

以下是我最终得到的组件列表:

*   基础
*   纽扣
*   形式
*   格子
*   网格。单位
*   菜单
*   菜单。页眉
*   菜单。项目
*   掉下
*   桌子

在分析了 CSS 并查看了可以分解的内容之后，我基本上为每个纯 CSS 模块设计了一个组件，并添加了一些额外的组件，为 DOM 元素和 Pure 的必需类名提供了更多基于语义组件的替代方案。

### 菜单

纯 CSS 菜单由“标题”和菜单“项目”本身组成。这里我没有使用类名，而是选择创建子组件。使用`<Menu.Header>`相当于`<div class="pure-menu-header">`。这可能比仅仅使用类名的性能差，但是它添加的语义糖使得汇编和阅读源代码更加人性化。

为了在 React 中创建子组件，您必须过滤组件的子组件，并检查子组件的名称是否与您正在寻找的组件相匹配:

```
import React from "react";

/**
 * Loops through children and adds provided props to specified component
 *
 * @param {React.Children} children - React Component's children
 * @param {React.Component} component - Component to filter children by
 * @returns {React.Children}
 */
const findByType = (children, component) => {
  const result = [];
  const type = [component.displayName] || [component.name];
  React.Children.forEach(children, child => {
    const childType =
      child && child.type && (child.type.displayName || child.type.name);
    if (type.includes(childType)) {
      result.push(child);
    }
  });
  return result;
};
export default findByType; 
```

Enter fullscreen mode Exit fullscreen mode

这在组件内部非常有效，可以呈现单个子组件:

```
 class List extends React.Component {
  renderList() {
    const { children } = this.props;
    const list = findByType(children, List);
    const items = list.map(listItems =>
      listItems.props.children.map(item => (
        <Item className="item" key={item.props.children}>
          {item.props.children}
        </Item>
      ))
    );
    if (!list) {
      return null;
    }
    return <List className="list">{items}</List>;
  }
  render() {
    return(<div>{this.renderList()}</div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法的唯一问题是标题和列表是分开呈现的，所以一次只能呈现一个标题。经过一点重构，我最终用这个方法来呈现这两者:

```
class BaseMenu extends PureComponent {
  renderMenu() {
    const { children } = this.props;

    const listType = [List.displayName] || [List.name];
    const headingType = [Heading.displayName] || [Heading.name];
    const items = React.Children.map(children, child => {
      const childType =
        child && child.type && (child.type.displayName || child.type.name);
      if (childType.includes(listType)) {
        return child.props.children.map(item => (
          <Item className="item" key={item.props.children}>
            {item.props.children}
          </Item>
        ));
      }
      if (childType.includes(headingType)) {
        return <Heading className="heading">{child.props.children}</Heading>;
      }
    });
    if (!items) {
      return null;
    }
    return <List className="list">{items}</List>;
  }

  render() {
    const { className } = this.props;
    return (
      <nav className={className} {...this.props}>
        {this.renderMenu()}
      </nav>
    );
  }
}

BaseMenu.Heading = Heading;
BaseMenu.List = List;
BaseMenu.Item = Item; 
```

Enter fullscreen mode Exit fullscreen mode

### 下拉菜单

`<Dropdown>`是另一个被创建的组件，因为它的功能可以完全通过 CSS 来处理，但是在 React 中，我们可能希望使用 Javascript 事件处理来改变传统的功能。当我完成组件系统的第一个版本(1.0)时，它包含了一个纯 CSS 下拉菜单。我立即注意到它不像我喜欢的键盘输入那样容易访问，于是我升级了该组件以使用 React 状态(用 CSS 作为后备)。

下拉菜单(道具外的演示组件):

```
const showDropdown = ({ active }) => {
  if (active) {
    return `display:block;`;
  }
  return ``;
};

/**
 * Dropdown used in `<Menu>` component
 *
 * @param {*} { className, children }
 */
const Dropdown = styled(BaseDropdown)`
  display: none;
  position: absolute;
  left: 100%;
  top: 0;
  margin: 0;
  padding: 0;
  z-index: 3; ${props => showDropdown(props)};
`; 
```

Enter fullscreen mode Exit fullscreen mode

菜单项(带状态的容器组件，作为道具传递给 drop down):

```
class BaseItem extends PureComponent {
  constructor(props) {
    super(props);
    this.state = {
      active: false
    };
  }

  /**
   * Toggles active state of dropdown
   *
   * @memberof BaseItem
   * @private
   */
  handleDropdown = e => {
    const { active } = this.state;
    this.setState({ active: !active });
  };

  /**
   * Renders all child elements
   * and applies state as props to nested Dropdown menus
   *
   * @memberof BaseItem
   * @private
   */
  renderDropdown = () => {
    const { children } = this.props;
    const { active } = this.state;

    /**
     * Find Dropdown components and add active state as prop
     */
    const propsToAdd = { active };
    const elements = addPropsToComponent(children, Dropdown, propsToAdd);
    if (!elements) {
      return null;
    }
    return elements;
  };

  render() {
    const { className, classes } = this.props;
    return (
      <li
        className={`${className}  ${classes}`}
        onMouseEnter={this.handleDropdown}
        onMouseLeave={this.handleDropdown}
        onFocus={this.handleDropdown}
        onBlur={this.handleDropdown}
      >
        {this.renderDropdown()}
      </li>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 我把这个作为一个纯组件，因为 React 的钩子还在 alpha 阶段，只在最新版本中可用。通过选择旧的(但仍受支持的)API，这些组件可以用于更广泛的项目中。

### 网格

因为`<Grid>`基本上是一个 flexbox 容器，所以`<Grid.Unit>`实际上指定了每一列的宽度。由于使用 CSS-in-JS 可以更有效地完成这个过程，所以这个过程由一个带有 props 的 React 组件来处理(而不是添加类`.pure-u-1-2`)。

纯 CSS 提供了硬编码的 5 列和 24 列网格。使用 Grunt，您可以将网格定制为任何其他列数。我没有用每个列的类来膨胀代码，而是创建了一个 Javascript 函数来按需返回列宽。通过提供一个列大小和列数的数组，你可以在任何时间为任何网格生成一个列:

```
/**
 * Calculates width of a grid element.
 *
 * Accepts an array of two numbers, the column size
 * and total number of columns (respectively).
 *
 * Uses the total columns to determine total width,
 * then multiplies by the column size to calculate
 * current column width.
 *
 * For example: a two column grid, with 50% wide columns,
 * would be an array of `[1,2]`. 2 total columns, with a
 * column taking up 1 of the 2 (so 50%). Same as `[3,6]`.
 *
 * @param {array} columns [Column size, Number of columns]
 */
const calculateGridWidth = columns => {
  if (columns) {
    return `width: ${(100 / columns[1]) * columns[0]}%;`;
  }
};

export default calculateGridWidth; 
```

Enter fullscreen mode Exit fullscreen mode

### 响应式设计

我开始编写自己的函数，使用上面的网格计算函数生成[响应媒体查询](https://www.styled-components.com/docs/advanced#media-templates)，但是我发现了[风格的系统](https://github.com/jxnblk/styled-system)，它提供了我正在寻找的相同功能，并且我还想添加更多功能。所以我安装了它，废弃了我的代码，然后运行它。

这段代码创建了一个网格，其中 50%的列在**手机**上，33%在**平板**上，25%在**桌面** :
上

```
<Grid>
  <Grid.Unit width={[1 / 3, 1 / 2, 1 / 3, 1 / 4]} bg="red">Thirds</Grid.Unit>
  <Grid.Unit width={[1 / 3, 1 / 2, 1 / 3, 1 / 4]} bg="blue">Thirds</Grid.Unit>
  <Grid.Unit width={[1 / 3, 1 / 2, 1 / 3, 1 / 4]} bg="teal">Thirds</Grid.Unit> </Grid> 
```

Enter fullscreen mode Exit fullscreen mode

> styled-system 还提供了控制属性的道具，如背景颜色(如上所示)、字体颜色和大小，以及大小(甚至填充和边距)。

## 各种问题😭

### 样式化组件+ React-Styleguidist

我有一个问题，样式化的组件不能与 React-Styleguidist 一起工作。我找到了第三方库的 Styleguidist 文档，它们显示您必须在样式化组件的导出之上添加一个`/** @component */`注释块。

这使得样式化的组件弹出，但是现在它们缺少了我在代码中包含的描述和属性类型。我花了一点时间才弄明白，但是我已经将 JSDocs 应用于一个“基础”组件，而不是实际的样式化组件。一旦我将注释块和属性类型移到实际的样式化组件中，我就能够传播文档了。

### 升级后升级

我开始时使用的是较早的 react-styleguidist，它使用的是 Babel 的 v6，但我的开发环境使用的是 v7(我最近升级了)。所以我的 Babel 配置文件包含了 styleguidist 无法使用的 v7 预置，崩溃了。将 styleguidist 升级到最新版本后，一切又正常了。

当你花 10 分钟去维护你的开发环境而不是实际编码的时候，我很喜欢🙃**#生产率**

## 未来🌠

纯样式的组件系统在 1.0.3 中是稳定的，并且可以在您的应用程序中使用，没有任何大的问题(据我所知)🙊).我已经从纯 CSS 中导入了所有的组件和样式，所以没有什么会真正极大地影响 API。

### 优化💪

在这一点上，所有的都是关于精炼和优化我转换成样式化组件的 CSS(可能移除更多的类定义，确保所有的变体都工作，等等)——以及 React 端的性能优化(看着你`React.memo()`👀).

### 检测+型式检查✅

我们目前有`prop-types`用于最小类型检查。我希望**全面测试覆盖**尽可能快地为组件使用 Jest，我甚至在考虑重构**类型脚本**来利用静态类型的好处。

### 布局+更好的文档📚

纯 CSS 的特点是文档的整个部分都是使用框架预先做好的布局。我转换了**博客布局**作为初始测试，但我想转换其余的。我还想使用**盖茨比主题**中的布局(比如博客)。这将鼓励更多地使用组件，并给人们更多授权工具。

文档非常少，而且大部分来自纯 CSS 文档本身。我希望**更多专注于 React 的**文档来帮助用户处理诸如**主题化**，扩展组件+ **定制**等事情。

### 前言？看到了吗？♻️

因为这些组件非常基本，而且大部分功能都很强大，所以我想看看这个库能支持多少不同的前端视图层。Preact 将是第一个，因为它的 API 非常接近 react。 **Vue** 也是优先考虑的，因为它允许对组件更大的“插入”支持(相对于 React 的 JSX 需要一个构建过程)。

## 创建自己的设计系统

使用现成的组件系统，如 Bootstrap 或 Material UI，总是很棒(也更容易)。但是如果你正在接受 UI/UX 开发人员的培训，从头开始构建自己的 UI/是非常宝贵的。通过这种方式，您可以发现所有不同的细微差别，这些细微差别使更流行的 UI 工具包变得无懈可击。从头开始构建东西，而不是说，一盒混合食谱，是很有趣的事情。

### 设计系统样板文件👇

如果你对创建自己的设计/组件系统感兴趣，我推荐你分叉我的[设计系统-jss 报告](https://github.com/whoisryosuke/design-system-jss)。它基本上是创建自己的 React 组件系统的样板文件，连接了开发、记录和部署组件代码所需的一切。

> 确保浏览 repo 上的分支，有不同版本的样板文件可以交换(比如 Gatsby for docs，而不是 Styleguidist)。

让我知道你对组件的想法，如果你有任何问题，欢迎在下面评论[(或者在 Github 上提出问题)](https://github.com/whoisryosuke/pure-components/issues)。

享受吧！
亮

* * *

**参考文献**:

*   [Github 上的纯风格组件](https://github.com/whoisryosuke/pure-components)
*   [NPM 上的纯风格组件](https://www.npmjs.com/package/pure-styled-components)
*   [纯 CSS](https://purecss.io/)
*   [故事书](https://storybook.js.org/)
*   [Styleguidist 第三方样式组件](https://github.com/styleguidist/react-styleguidist/blob/master/docs/Thirdparties.md#styled-components)