# 将样式化组件与 React Native 一起使用

> 原文：<https://dev.to/amanhimself/using-styled-components-with-react-native-4k15>

[![cover](img/3f73253ec3a8139f6569af978b71a679.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GMwFS5ma--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sul5tqzlpgbrrglwedi8.jpeg)

**Tldr；**

*   介绍
*   关于样式组件
*   安装样式组件
*   使用样式组件
*   样式组件中的道具
*   构建应用程序-杂货用户界面
*   添加用户头像图像
*   React Native 中的绝对定位
*   在 React Native 中添加图标
*   添加水平滚动视图
*   添加垂直滚动视图
*   构建卡组件
*   结论

## 简介

无论你是 web 开发人员还是移动应用程序开发人员，你都知道如果你的应用程序没有很好的样式，UI 可能会很糟糕。应用程序的样式很重要。对于一个手机应用程序来说，拥有令人愉悦的设计和良好的色彩运用是多么重要，这一点我怎么强调都不为过。

如果你开始接触 React Native 或者已经尝试过，一定要知道你可以用不同的方式来设计 React Native 应用。在下面的文章中，我已经讨论了一些基础知识和一些不同的方法来设计您的 React 本地组件。比如，创建一个新的样式对象你使用 **StyleSheet.create()** 方法并封装它们。去看看👇

[https://hacker noon . com/styling-the-react-native-way-3c C6 D3 ef 52d 0](https://hackernoon.com/styling-the-react-native-way-3cc6d3ef52d0)

本教程是关于使用 **[来设计你的 React 原生应用💅样式组件](https://www.styled-components.com/docs/basics)** 。是的，styled-components 是第三方库。使用它是一个选择的问题，但也是样式化组件的另一种方式，你们中的许多人可能会发现它很容易使用。特别是，如果您以前在其他框架中使用过这个库。一个常见的用例是 React。

## 什么是风格化组件？

Styled Components 是一个 *CSS-in-JS* 库，它以某种方式迫使开发人员用他们自己的风格编写每个组件，并将它们放在一个地方。这种强制措施为一些快乐的开发者带来了快乐的时光，从而优化了他们的体验和产出。

在 React Native 中，组件的样式已经通过创建 JavaScript 对象完成了，如果你不[封装它们](https://hackernoon.com/styling-the-react-native-way-3cc6d3ef52d0)，在大多数情况下，你的组件和它们的样式将会在一个地方结束。

React Native 在设计应用程序样式时往往会遵循某种惯例。比如所有 CSS 属性名都应该在`camelCase`里比如对于`background-color`在 React Native 里是:

```
backgroundColor: 'blue` 
```

Enter fullscreen mode Exit fullscreen mode

一些 web 开发人员对这些约定感到不舒服。使用像样式组件这样的第三方库可以让你插上翅膀。除了属性和 React Native 自己的 Flexbox 规则之外，您不必在约定的上下文之间切换太多。

在幕后，样式化组件只是将 CSS 文本转换成 React 本机样式表对象。你可以在这里查看它是如何做到的[。](https://github.com/styled-components/css-to-react-native)

故事讲完了，我们开始工作吧！

## 安装样式组件

要在 react 原生项目中安装库样式组件，我们首先需要有一个 React 原生项目。为了快速上手，我打算用牛逼的 **[世博](http://expo.io/)** 。确保您已经安装了`expo-cli`。

```
# To install expo-cli

npm install -S expo-cli

# Generate a project

expo init [YourApp-Name] 
```

Enter fullscreen mode Exit fullscreen mode

当运行最后一个命令时，命令行提示符会提示您几个问题。第一个是`Choose a template`，我选择了`expo-template-blank`，然后输入你的应用程序的显示名称，然后使用`npm`或`yarn`来安装依赖项。我和 npm 一起去。

一旦安装了所有的依赖项，您就可以在您喜欢的代码编辑器中打开这个项目。下一步是安装最新版本的`styled-components`库。

```
npm install -S styled-components 
```

Enter fullscreen mode Exit fullscreen mode

安装到此为止。

## 使用样式化组件

现在打开`App.js`文件并做一些修改。

```
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';

export default class App extends React.Component {
    render() {
        return (
            <View style={styles.container}>
                <Text>Open up App.js to start working on your app!</Text>
            </View>
        );
    }
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        backgroundColor: '#fff',
        alignItems: 'center',
        justifyContent: 'center'
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果你在 macOS 上，从你喜欢的终端窗口运行命令:`npm run ios`。对于 Linux 和 Windows 用户，命令是`npm run android`,但要确保你有 android 虚拟设备在后台运行。我们的代码目前看起来如下。

[![ss1](img/28b20ff80fe7145804b1556050d81153.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tmNwHYXO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AFJYy0ggO0KlAjamE81Z19w.png)

让我们对它进行一些修改，并使用我们新安装的库。首先，像下面这样导入库。

```
import styled from 'styled-components'; 
```

Enter fullscreen mode Exit fullscreen mode

对组件的渲染函数进行如下修改。用`Container`和`Title`替换`View`和`Text`。这些新元素将使用样式化组件的语义进行定制。

```
export default class App extends React.Component {
    render() {
        return (
            <Container>
                React Native with 💅 Styled Components
            </Container>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`styled-components`利用带标签的模板文字，通过反勾号来设计组件的样式。当使用`styled-components`在 React 或 React Native 中创建一个组件时，每个组件都将被附加上样式。

```
const Container = styled.View`
    flex: 1;
    background-color: papayawhip;
    justify-content: center;
    align-items: center;
`;

const Title = styled.Text`
    font-size: 20px;
    font-weight: 500;
    color: palevioletred;
`; 
```

Enter fullscreen mode Exit fullscreen mode

请注意，所包含的是一个 React Native `View`但却附加了样式。

[![ss1](img/28b20ff80fe7145804b1556050d81153.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tmNwHYXO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AFJYy0ggO0KlAjamE81Z19w.png)

修改后的`App.js`文件的完整代码。

```
import React from 'react';
import styled from 'styled-components';

export default class App extends React.Component {
    render() {
        return (
            <Container>
                React Native with 💅 Styled Components
            </Container>
        );
    }
}

const Container = styled.View`
    flex: 1;
    background-color: papayawhip;
    justify-content: center;
    align-items: center;
`;

const Title = styled.Text`
    font-size: 24px;
    font-weight: 500;
    color: palevioletred;
`; 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，请注意我们没有导入 React 本地核心组件，如`View`、`Text`或`StyleSheet`对象。就这么简单。它使用与原生布局相同的`flexbox`模型。这样做的好处是，您可以使用 Web 开发中一直使用的相同且可理解的语法。

## 在样式化组件中使用道具

您经常会发现自己在为应用程序创建定制组件。这给了你保持干燥的优势。使用`styled-components`也没什么不同。您可以通过构建需要来自其父组件的`props`的定制组件来利用这种编程模式。`props`通常被称为对特定成分的附加属性。为了演示这一点，创建一个名为`CustomButton.js`的新文件。

在这个文件中，我们将创建一个定制按钮，它需要诸如`backgroundColor`、`textColor`这样的道具和按钮本身的文本。您将使用`TouchableOpacity`和`Text`来创建这个定制按钮，但是没有使用功能组件`CustomButton`导入`react-native`库。

```
import React from 'react';
import styled from 'styled-components';

const CustomButton = props => (
    <ButtonContainer
        onPress={() => alert('Hi!')}
        backgroundColor={props.backgroundColor}
    >
        <ButtonText textColor={props.textColor}>{props.text}</ButtonText>
    </ButtonContainer> );

export default CustomButton;

const ButtonContainer = styled.TouchableOpacity`
    width: 100px;
    height: 40px
    padding: 12px;
    border-radius: 10px;    
    background-color: ${props => props.backgroundColor};
`;

const ButtonText = styled.Text`
    font-size: 15px;
    color: ${props => props.textColor};
    text-align: center;
`; 
```

Enter fullscreen mode Exit fullscreen mode

通过将插值函数`${props => props...}`传递给样式化组件的模板文本，可以扩展其样式。现在将这个按钮添加到`App.js`文件中。

```
render() {
        return (
            <Container>
                React Native with 💅 Styled Components
                <CustomButton text="Click Me" textColor="#01d1e5" backgroundColor="lavenderblush" />
            </Container>
        );
    } 
```

Enter fullscreen mode Exit fullscreen mode

在运行模拟器时，您将获得以下结果。

[![ss2](img/847d7e701a6922b4749f71e77ef62253.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oFgrEk9D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ANtpTxTjtBxNl_IR4W-W7lA.png)

## 构建 app -杂货 UI

我们在这部分要建造什么？一个可能是杂货店的应用程序的 UI 屏幕。您将构建如下图所示的主屏幕。

[![ss3](img/f217b89054ed8a0f4e5e0418f4a81fdd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GmUQUp3P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AqRd6EyuiB_nvs3jolhOCeA.png)

我们将使用我们的`styled-components`知识，所以让我们开始吧！打开`App.js`。使用 styled 声明一个新的`Container`视图。在反勾号内部，您可以使用完全相同的语法放置纯 CSS 代码。一般来说，`View`元素就像 HTML 或 web 编程中的`div`。另外，在`Container`中创建另一个名为`Titlebar`的视图。

在`Titlebar`里面，它将包含三个新元素。一个是图像，`Avatar`，另外两个是文本:`Title`和`Name`。

```
import React from 'react';
import styled from 'styled-components';

export default class App extends React.Component {
    render() {
        return (
            <Container>
                
                    <Avatar />
                    Welcome back,
                    <Name>Aman</Name>
                
            </Container>
        );
    }
}

const Container = styled.View`
    flex: 1;
    background-color: white;
    justify-content: center;
    align-items: center;
`;

const Titlebar = styled.View`
    width: 100%;
    margin-top: 50px;
    padding-left: 80px;
`;

const Avatar = styled.Image``;

const Title = styled.Text`
    font-size: 20px;
    font-weight: 500;
    color: #b8bece;
`;

const Name = styled.Text`
    font-size: 20px;
    color: #3c4560;
    font-weight: bold;
`; 
```

Enter fullscreen mode Exit fullscreen mode

运行`npm run ios`并查看其运行情况。

[![ss4](img/73ad5f27b4476cb855509383aa4af4b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lTPiD0It--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AQWsjF7juUsD8wHsuD_4M-A.png)

现在，屏幕中间的一切都是怎样的。我们需要在手机屏幕的顶部显示`Titlebar`及其内容。因此`Container`的样式如下。

```
const Container = styled.View`
    flex: 1;
    background-color: white;
`; 
```

Enter fullscreen mode Exit fullscreen mode

### 添加用户头像图片

我将使用存储在项目根目录下的`assets`文件夹中的图像。如果你可以自由使用你自己的图片，你也可以在下面下载这个项目的资源。

[https://github . com/amandeepmittal/react-native-workspace/tree/master/03-rn furry-ui/assets](https://github.com/amandeepmittal/react-native-workspace/tree/master/03-RNgrocery-ui/assets)

为了用`styled-components`创建图像，您需要`Image`组件。您可以使用`source`道具根据图像所在的位置来引用图像。

```

    <Avatar source={require('./assets/avatar.jpg')} />
    Welcome back,
    <Name>Aman</Name>  
```

Enter fullscreen mode Exit fullscreen mode

`Avatar`的样式将以`44`像素的宽度和高度开始。有一个正好是宽度和高度的一半的`border-radius`,增加了图像的圆形。`border-radius`是您将大量使用来创建角落的属性。

```
const Avatar = styled.Image`
    width: 44px;
    height: 44px;
    background: black;
    border-radius: 22px;
    margin-left: 20px;
`; 
```

Enter fullscreen mode Exit fullscreen mode

你会得到下面的结果。

[![ss5](img/4357709972671b79e3a57fb46a121094.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UL0TM719--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AAR9C05E4OOHGmWVkrnxjVQ.png)

现在请注意，头像图像和文本堆积在一起。它们在屏幕上占据相同的空间。为了避免这种情况，您将使用`position: absolute` CSS 属性。

### 绝对定位于 React Native

CSS 属性如`padding`和`margin`被用来增加 UI 元素之间的空间。这是默认的布局位置。但是，在您当前所处的场景中，使用 UI 元素的绝对定位并将所需的 UI 元素放在您想要的确切位置是非常有益的。

一般来说，在 React Native 和 CSS 中，如果`position`属性设置为`absolute`，那么元素相对于其父元素进行布局。CSS 对`position`有其他值，但是 React Native 只支持`absolute`。

如下修改`Avatar`样式。

```
const Avatar = styled.Image`
    width: 44px;
    height: 44px;
    background: black;
    border-radius: 22px;
    margin-left: 20px;
    position: absolute;
    top: 0;
    left: 0;
`; 
```

Enter fullscreen mode Exit fullscreen mode

通常，对于绝对位置属性，您将使用以下属性的组合:

*   顶端
*   左边的
*   正确
*   底部

在上面的例子中，我们使用的`top`和`left`都设置为`0`像素。您将获得以下输出。

[![ss6](img/f408827fcc07071bac00eee8cf62a747.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TS3fwBAx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AxuQEJUyE0rGBNCuzz14ajw.png)

### 在 React Native 中添加图标

Expo 样板文件带有一套不同的图标库，如 Ionicons、FontAwesome、Glyphicons、Material icons 等等。完整的图标列表你可以在这里找到，一个可搜索的网站。

要使用这个库，您所要做的就是编写 import 语句。

```
import { Ionicons } from '@expo/vector-icons'; 
```

Enter fullscreen mode Exit fullscreen mode

在`Titlebar`视图中，添加图标。

```

    {/* ... */}
    <Ionicons name="md-cart" size={32} color="red" />
 
```

Enter fullscreen mode Exit fullscreen mode

每个图标需要道具的名称，你可以选择，大小和颜色。现在，如果你看看模拟器，你会注意到我们在添加头像时遇到的同样的问题。标题栏内的图标和其他 UI 元素之间没有空格。

[![ss7](img/8cfdd9330676333048314cd6219aef9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0_Wpua5g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Ak0ongnUA5ZLGzvK-QBDvXQ.png)

为了解决这个问题，让我们使用绝对定位属性作为`<Ionicons />`到
的内联样式

```
<Ionicons
    name="md-cart"
    size={32}
    color="red"
    style={{ position: 'absolute', right: 20, top: 5 }}
/> 
```

Enter fullscreen mode Exit fullscreen mode

为什么是内嵌风格？因为`Ionicons`不是使用样式化组件生成的。

[![ss8](img/913a887ca0f61eaf506140338d91c5ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QzMgwxec--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AEjxFzga9cQWUDNXesQ5KkA.png)

### 通过列表映射

在`components/`文件夹中创建一个名为`Categories.js`的新文件。这个文件将为杂货 UI 应用程序呈现一个类别项目列表。

```
import React from 'react';
import styled from 'styled-components';

const Categories = props => (
    <Container>
        <Name>Fruits</Name>
        <Name>Bread</Name>
        <Name>Drinks</Name>
        <Name>Veggies</Name>
    </Container> );

export default Categories;

const Container = styled.View``;

const Name = styled.Text`
    font-size: 32px;
    font-weight: 600;
    margin-left: 15px;
    color: #bcbece;
`; 
```

Enter fullscreen mode Exit fullscreen mode

所有的数据都是静态的。在`App.js`中导入该组件，并将其放在`Titlebar`之后。

```
import Categories from './components/Categories';

// ...

return (
    <Container>
        {/* ... */}
        <Categories />
    </Container> ); 
```

Enter fullscreen mode Exit fullscreen mode

您将获得以下输出。

[![ss9](img/8944f8c011d75f1739d805bc3f22e96c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CqjlT6nT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AkkLH38JDwcNg6gNCBFs-eA.png)

他们可以有很多种类。为了使类别名称动态化，我们可以通过`App.js`文件发送它。

```
const Items = [
    { text: 'Fruits' },
    { text: 'Bread' },
    { text: 'Drinks' },
    { text: 'Veggies' },
    { text: 'Meat' },
    { text: 'Paper Goods' }
];

// Inside the render function replace <Categories /> with

{
    items.map((category, index) => (
        <Categories name={category.text} key={index} />
    ));
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，您正在使用 JavaScript 中的`map`函数来遍历一个数组，以此类别名称呈现一个项目列表。需要添加一个`key`道具。

要使这个工作，也要修改`Categories.js`。

```
const Categories = props => <Name>{props.name}</Name>; 
```

Enter fullscreen mode Exit fullscreen mode

### 添加水平滚动视图

该列表现在不可滚动。为了使它可滚动，让我们将它放在一个`ScrollView`中。打开`App.js`文件，将类别放在`ScrollView`中，但是首先从 React Native core 导入。

```
import { ScrollView } from 'react-native';

// ...

<ScrollView>
    {items.map((category, index) => (
        <Categories name={category.text} key={index} />
    ))}
</ScrollView>; 
```

Enter fullscreen mode Exit fullscreen mode

您将注意到 UI 中没有任何变化。默认情况下，React Native 中使用`ScrollView`的可滚动列表是垂直的。通过添加道具`horizontal`使其水平。

```
<ScrollView horizontal={true}>
    {items.map((category, index) => (
        <Categories name={category.text} key={index} />
    ))}
</ScrollView> 
```

Enter fullscreen mode Exit fullscreen mode

它可以工作，但是看起来不太好。

[![ss10](img/591f8b6ecb33723a383bca02067920b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A24R7hRG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Aynb8YhFnnn56-nLYqSPThg.gif)

让我们给`ScrollView`添加一些内联样式。

```
<ScrollView
    horizontal={true}
    style={{
        padding: 20,
        paddingLeft: 12,
        paddingTop: 30,
        flexDirection: 'row'
    }}
    showsHorizontalScrollIndicator={false}
>
    {items.map((category, index) => (
        <Categories name={category.text} key={index} />
    ))}
</ScrollView> 
```

Enter fullscreen mode Exit fullscreen mode

现在看起来好多了。道具`showsHorizontalScrollIndicator`隐藏了默认情况下出现在类别名称下面的水平滚动条。

[![ss11](img/9ba930f84ca3593b83af15a67b12f429.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WUBUMzR1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AIYqopJpgJkPAyif2slhVEw.png)

### 添加垂直滚动视图

下一步是在`Container`视图中添加一个充当包装器的`ScrollView`，这样整个区域就可以垂直滚动了。这样做是有原因的。现在，您将把项目分成两列，作为带有与特定类别相关的文本的图像。

修改`App.js`文件。

```
return (
    <Container>
        <ScrollView>
            {/* and its contents */}
            <ScrollView horizontal={true}>
                {/* Categories being rendered */}
            </ScrollView>
            <Subtitle>Items</Subtitle>
        </ScrollView>
    </Container> ); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们添加了另一个名为`Subtitle`的样式组件，它只是一个文本。

```
const Subtitle = styled.Text`
    font-size: 20px;
    color: #3c4560;
    font-weight: 500;
    margin-top: 10px;
    margin-left: 25px;
    text-transform: uppercase;
`; 
```

Enter fullscreen mode Exit fullscreen mode

它呈现如下。

[![ss12](img/b231d232f255c6df38e691470e2a8e2c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2V0hxggI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AX9Db80WNDFSLRVGC1sdkWw.png)

### 构建卡片组件

在本节中，我们将创建一个 card 组件，它以文本的形式保存商品的图像、商品的名称和价格。每个卡片组件都有弯曲的边框和方框阴影。这就是它看起来的样子。

[![ss13](img/d03f1f70efee0f99d5088813535afa57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N0xahhIu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AbNCJQ4koGEZVKtbGu6EeqA.png)

在`components`目录下创建一个名为`Card.js`的新组件文件。`Card`组件的结构将会是。

```
import React from 'react';
import styled from 'styled-components';

const Card = props => (
    <Container>
        <Cover>
            <Image source={require('../assets/pepper.jpg')} />
        </Cover>
        <Content>
            Pepper
            <PriceCaption>$ 2.99 each</PriceCaption>
        </Content>
    </Container> );

export default Card; 
```

Enter fullscreen mode Exit fullscreen mode

目前，它有静态数据，如图像、标题和内容。让我们在这个文件中为每个样式化的 UI 元素添加样式。

```
const Container = styled.View`
    background: #fff;
    height: 200px;
    width: 150px;
    border-radius: 14px;
    margin: 18px;
    margin-top: 20px;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.15);
`;

const Cover = styled.View`
    width: 100%;
    height: 120px;
    border-top-left-radius: 14px;
    border-top-right-radius: 14px;
    overflow: hidden;
`;

const Image = styled.Image`
    width: 100%;
    height: 100%;
`;

const Content = styled.View`
    padding-top: 10px;
    flex-direction: column;
    align-items: center;
    height: 60px;
`;

const Title = styled.Text`
    color: #3c4560;
    font-size: 20px;
    font-weight: 600;
`;

const PriceCaption = styled.Text`
    color: #b8b3c3;
    font-size: 15px;
    font-weight: 600;
    margin-top: 4px;
`; 
```

Enter fullscreen mode Exit fullscreen mode

`Container`视图的默认背景是白色。这在从第三方 API 获取图像的情况下非常有用。此外，它还为图像下方的文本区域提供了背景。

在`Container`视图中，添加一个`Image`并将其包装在一个`Cover`视图中。在 React Native 中，有两种方法可以获取图像

如果您从静态资源中获取一个图像，就像我们的例子一样，您使用带有关键字`require`的 use `source` prop，该关键字包含存储在项目文件夹中的图像资产的相对路径。在联网图像或从 API 获取图像的情况下，您使用相同的道具和不同的关键字`uri`。这是一个从 API 获取图像的例子。

```
<Image
    source={{
        uri: 'https://facebook.github.io/react-native/docs/assets/favicon.png'
    }}
/> 
```

Enter fullscreen mode Exit fullscreen mode

`Cover`视图使用带有`overflow`属性的圆角。这样做是为了反映圆角。如果图像来自子组件，iOS 会对其进行剪辑。在我们的例子中，图像来自一个`Card`组件，它是`App`组件的子组件。

`Image`组件获取整个`Cover`视图的宽度和高度。

现在让我们将这个组件导入到`App.js`文件中，在`Subtitle`之后，让我们看看会得到什么结果。

```
render() {
    return (
    <Container>
        <ScrollView>
        {/* ... */}
        <Subtitle>Items</Subtitle>
            <ItemsLayout>
                <ColumnOne>
                    <Card />
                </ColumnOne>
                <ColumnTwo>
                    <Card />
                </ColumnTwo>
            </ItemsLayout>
        </ScrollView>
    </Container>
    )
}

// ...

const ItemsLayout = styled.View`
    flex-direction: row;
    flex: 1;
`;

const ColumnOne = styled.View``;

const ColumnTwo = styled.View``; 
```

Enter fullscreen mode Exit fullscreen mode

在`Subtitle`之后，添加一个名为`ItemsLayout`的新视图。这将是一种布局，允许不同的卡被划分在每行的两列之间。这可以通过给这个视图一个值为`row`的`flex-direction`属性来实现。`ColumnOne`和`ColumnTwo`是两个空视图。

在渲染模拟器的屏幕上，看起来像下面这样。

[![ss14](img/f217b89054ed8a0f4e5e0418f4a81fdd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GmUQUp3P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AqRd6EyuiB_nvs3jolhOCeA.png)

## 结论

你以前用过 React Native 的样式化组件吗？如果没有，你会在下一个项目中尝试吗？如果您觉得在 React 原生应用程序中使用`styled-components`不舒服，请在下面评论。*您也可以扩展这个应用程序！让你的想象力漫游。*如果你这样做，提交一份简历。

您可以在 Github repo 中找到本文的完整代码👇

[https://github . com/amandeepmittal/react-native-workspace/tree/master/03-rn furry-ui](https://github.com/amandeepmittal/react-native-workspace/tree/master/03-RNgrocery-ui)

* * *

*本帖最初发表于[这里](https://medium.com/swlh/using-styled-components-with-react-native-de645fcf4787)。*

我在 [**Twitter**](https://twitter.com/amanhimself) 上有空，所以如果你需要，请随时给我发短信。我还向那些对学习更多 web 技术感兴趣的开发者发送了一份 **[每周简讯](https://tinyletter.com/amanhimself)**