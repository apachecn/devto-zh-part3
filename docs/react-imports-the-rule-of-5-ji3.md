# 反应进口:5 的规则。

> 原文：<https://dev.to/jorgealvarez/react-imports-the-rule-of-5-ji3>

我是 S.O.L.I.D .发展原则的忠实拥护者。它们使代码易于阅读、理解，最重要的是，当您的业务规则发生变化时，代码也易于更改。

在这 5 个原则中，有两个我特别关注 S 和 I。S 代表[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)对我来说是所有原则中最重要的，I 代表[界面分离原则](https://en.wikipedia.org/wiki/Interface_segregation_principle)。

这是一个例子，说明 5 的规则如何让我思考我正在编写的代码，并在必要时进行重构。

## 5 的法则

5 的规则很容易定义:**我尽量把导入次数控制在 5 以下。**

这是 react 本机文件的一个示例:

```
import React from 'react';
import { graphql } from 'react-apollo';
import { View } from 'react-native';

import composeHOC from '../../../lib/compose';
import { WithFailedQuery, WithLoadingQuery } from '../../shared/hoc';
import { AllLandmarksQuery } from '../../../queries';
import LandmarksListIndex from './index';
import LandmarksMapIndex from '../map/index';

const LandmarksListFinderQuery = ({ navigation, resultsQuery, userLocation, showItemList }) => {
  const { landmarks } = resultsQuery; 
  return ( 
    <View> 
      { showItemList ? <LandmarksListIndexlandmarks={ landmarks } navigation={ navigation } landmarkType="landmark"\> 
      : <LandmarksMapIndexnavigation={ navigation } landmarks={ landmarks } userLocation={ userLocation } /> } 
    </View> 
  ); 
}
export default LandmarksListFinder = composeHOC( graphql(AllLandmarksQuery, {    
  name: 'resultsQuery', options: ({ 
    maximumDistance, userLocation }) => ({ variables: { distance:            
    maximumDistance, latitude: userLocation.latitude, longitude: 
    userLocation.longitude } }) 
}), WithFailedQuery, WithLoadingQuery)(LandmarksListFinderQuery); 
```

Enter fullscreen mode Exit fullscreen mode

我对进口做了两点区分。首先，我有所有包括来自第三方库的代码的导入，然后我有所有包括来自我自己的应用程序的代码的导入。我尝试做的是将两个部分都保持在 5 以下。**如果我必须导入 5 个以上的第三方库，或者如果我必须从自己的代码中导入 5 个以上的文件，那么我可能做了太多的事情**。

本例中的文件从 graphql 服务器加载一个查询，然后以列表或地图的形式显示结果。只有 3 个从第三方库的进口这很好，但我有 5 个自己的代码进口。现在脑子里有个警讯。为什么我需要太多的文件？

我正在导入一个组合文件，将 graphql 查询与几个处理错误并显示加载符号的高阶组件混合在一起。我包含了查询，然后包含了两个 react 组件，它们将结果显示为一个项目列表或 google 地图。

我的大脑现在开始思考。如何减少文件数量？一个显而易见的方法是删除 render 方法中的所有逻辑。现在这个组件做两件事:它从服务器加载所需的数据，并决定哪个组件将显示数据。我可以创建一个组件来处理显示商品列表或地图的所有逻辑。通过这样做，我将能够删除两个导入的文件，但我必须导入新的组件，所以我将有 4 个导入而不是 5 个。此外，这个重构不需要视图组件，因此第三方导入将从 3 变为 2。

我应该现在做吗？我应该等到一个新的需求到来，然后被迫添加一个新的导入吗？...这取决于时间限制和我目前对业务逻辑的了解。如果我认为这不会改变，那么我可能会让代码保持原样，并在将来重构它。但是如果我知道结果的表示有很多可能会改变，那么我会立刻重构代码。

## 总结

有时候，你太忙于赶最后期限，以至于忘记了最佳实践，只专注于完成工作。这一点都不差，这也是你得到报酬的原因，但拥有一套好的指标总是好的，它可以帮助你保持正轨，提醒你还有改进的空间，并帮助你编写易于理解并在未来易于更改的代码。

五次进口的规则对我来说非常有用。希望也能帮到你。

*本帖最早出现在这里:[https://www . Alvarez Navarro . es/Desarrollo-web/2018/3/react-native-imports-the-rule-of-5](https://www.alvareznavarro.es/desarrollo-web/2018/3/react-native-imports-the-rule-of-5)T3】*