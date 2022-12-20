# 风格组件第二部分

> 原文：<https://dev.to/independnt/styled-components-pt-2-3ccg>

最近，我开始使用样式化的组件来制作样式化的 div，以便使样式化单个信息变得更容易，现在，我正在更深入地挖掘，我一直在使用它们来构建彼此的样式，以便使某些组件基于条件做出反应！

例如，我一直在开发一个应用程序，它能够根据条件添加、删除或取消某些组件。底部有一个网格，允许添加对象，顶部显示新添加的对象。底部网格应该允许添加对象，顶部网格应该允许删除对象，任何已经添加的对象都应该被取消，从而消除重复的可能性。

首先，我们创建一个样式化的 div，使用我们默认的期望样式，这是我们期望包装在这个 div 中的每个组件的基本样式。

```
///Imported 
///subtleBoxShadow = `box-shadow: 0px 0px 5px 1px ${lightTheme ? '#a9b6ff' : '#121d5b'}`;
///lightBlueBackground = `background-color: #061a44`;
///greenBoxShadow = `box-shadow: 0px 0px 4px 2px #5fff17`;
///redBoxShadow = `box-shadow: 0px 0px 2px 2px #e41111`;

export const Tile = styled.div ` ${subtleBoxShadow}  ${lightBlueBackground} padding: 10px;
` 
```

现在，我们可以在基础“瓷砖”的基础上创建其他样式的组件。我们现在需要可选择的瓷砖，在悬停时有一个光标动作，所以我们再添加一个。这一次，我们使用 styled(Tile ),而不是 styled.div，这样我们就可以在已经显示的基础样式上进行构建。

```
export const SelectableTile = styled(Tile)`
  &:hover {
    cursor: pointer; ${greenBoxShadow} }
` 
```

现在我们再构建两个来处理可删除的图块和禁用的图块(以避免重复)。

```
export const DeletableTile = styled(SelectableTile)`
  &:hover {
    cursor: pointer; ${redBoxShadow} }
`

export const DisabledTile = styled(Tile)`
  pointer-events: none;
  opacity: 0.4;
` 
```

现在我们有了悬停时带有绿色阴影的可选磁贴，但我们的可删除样式将覆盖这些样式，并在悬停时添加红色阴影。最后，我们禁用的瓷砖有一个指针事件设置为无，不透明度显示它已被选中，并删除点击事件的行动。

现在，在所需的组件中，我将“Current Tile”变量设置为我们创建的导入的可选 Tile，并加入一些条件。我将 topSection prop 添加到顶部的所有图块，并创建一个名为“AlreadyInFavorites()”的方法来查找所需的选定图块是否已经处于我的收藏夹状态。现在我要导出的函数是这样的:

```
export default function({coinKey, topSection}){
  return <AppContext.Consumer>
    {({coinList, isInFavorites}) => {
      let coin = coinList[coinKey];

      let TileClass = SelectableTile;
      if(topSection){
        TileClass = DeletableTile
      }else if(isInFavorites(coinKey)){
        TileClass = DisabledTile
      }
      return <TileClass>
        <CoinHeaderGrid topSection={topSection} name={coin.CoinName} symbol={coin.Symbol}/>
        <CoinImage coin={coin}/>
      </TileClass>
    }}
  </AppContext.Consumer> } 
```

为了一点“背景”....咳咳。我使用 React 上下文并返回一个消费者，它将在状态改变时更新。我从存放所有对象的州传递“coinList”。我认为 Tileclass 等于我的可选平铺 div，如果对象的 topSection 属性等于 true，那么这意味着它在顶部，应该是可删除的，所以设置为可删除平铺。如果该互动程序已经存在于“我的收藏夹”中，则在底部(最初添加的位置)我们会将其设为禁用的互动程序，因此我们无法再次添加它。Styled components 正在改变我在 React 中创建样式和悬停/单击事件的方式。绝对是新宠！