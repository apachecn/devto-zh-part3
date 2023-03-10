# React 本机选取器组件的统一替代方案

> 原文：<https://dev.to/civanozseyhan/an-unified-alternative-to-react-native-picker-component-o3i>

React Native 附带了一个 Picker 组件，提供了对这个最基本的 UI 元素的本机实现的绑定。它在 iOS 上呈现为 **UIPickerView** ，在 Android 设备上呈现为 **Spinner** :

[![](img/1e555c7eb6baedeff4d41feadc8b16d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EXGRh_Gd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/plg0ojq69msy3sdaxmt2.gif)

从演示中可以看出，iOS 和 Android 上的输出完全不同。如果你想在两个平台上有一致的外观和感觉，官方的 React Picker 组件肯定是**而不是**要走的路。

stock 组件的另一个问题是特定于平台的 props，这导致您向项目添加特定于平台的代码，以使它在两个平台上都可以工作。

我们采用了另一种方法，开发了一个基于模态视图的列表选择器组件。它在两个平台上呈现一致，并提供统一的 API。

此外，该组件包括对文本搜索和字母索引的内置支持，这使它成为不适合“车轮选择器”的较长列表的理想选择:

[![](img/8af03eba32a48659b92f37fdcfd17b5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fPq7MSFN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mc7p450p2hrqj94s45jl.gif)

如果你想试试，可以在 [Expo](https://snack.expo.io/@pankod/github.com-pankod-react-native-picker-modal-view:example) 上看到现场演示，Github 上有完整的源代码和完整的 API 文档:

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [潘科德](https://github.com/pankod) / [反应-原生-拾取-模态-视图](https://github.com/pankod/react-native-picker-modal-view)

### iOS 和 Android 的统一 React 原生选取器模态组件。

<article class="markdown-body entry-content p-5" itemprop="text">[![](img/b60a4ce85aaf7f88cb3d346f7723d1b7.png)](https://raw.githubusercontent.com/pankod/react-native-picker-modal-view/master/screenshots/ios.gif) [![](img/4bfa2ee863745d64789f9e683105d0aa.png)](https://raw.githubusercontent.com/pankod/react-native-picker-modal-view/master/screenshots/android.gif)

### 反应本机选择器模式视图

React Native Module to select item picker modal.

[![Maintainability](img/c77b9618eec5d779aa71c4bcad344094.png)](https://codeclimate.com/github/pankod/react-native-picker-modal-view/maintainability)[![Test Coverage](img/1f0935a0b386df1773229ee86cf22a7b.png)](https://codeclimate.com/github/pankod/react-native-picker-modal-view/test_coverage)[![npm version](img/9d499b452210f823c796791371cc9ec0.png)](https://www.npmjs.com/package/react-native-picker-modal-view)[![npm downloads per month](img/8c7afb83b941bab4e9effcc97cc20315.png)](https://www.npmjs.com/package/react-native-picker-modal-view)[![dependencies Status](img/647f921e2eb278e8ba46c31f21d8160f.png)](https://david-dm.org/pankod/react-native-picker-modal-view)[![dev-dependencies Status](img/7b59d09613d3fccb7862b81ead135f27.png)](https://david-dm.org/pankod/react-native-picker-modal-view?type=dev)[![Build Status](img/70deda97c226367adae0c17a9201de43.png)](https://travis-ci.com/pankod/react-native-picker-modal-view)

<sub>Created by [Pankod](https://www.pankod.com)</sub>

一个替代 [Picker](https://facebook.github.io/react-native/docs/picker) 和 [PickerIOS](https://facebook.github.io/react-native/docs/pickerios) 组件的产品，在两个平台上都有统一的 API 和一致的外观&。它是完全可配置的，包括对文本搜索和字母索引的内置支持。理想的较长的列表不适合“车轮采摘”。

## 入门指南

```
$ npm install react-native-picker-modal-view --save 
```

或者

```
$ yarn add react-native-picker-modal-view 
```

## 世博会现场演示

[![Explore with Expo Snack](img/d25a4e1b3ac48ae8afa835efbd74cb93.png)](https://snack.expo.io/@pankod/github.com-pankod-react-native-picker-modal-view:example)

## 例子

```
import * as React from 'react'
import { Button, SafeAreaView, Text, View } from 'react-native'
import PickerModal from 'react-native-picker-modal-view';

import data from '../../../top20.json';

export default class Main extends React.Component<{}, { selectedItem: {} }> {

    constructor(props: {}) {
        super(props);

        this.state = {
            selectedItem: {}
        };
    }

    public render():
```

…</article>

[View on GitHub](https://github.com/pankod/react-native-picker-modal-view)