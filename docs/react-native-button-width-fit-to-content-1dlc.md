# 根据内容调整本机按钮宽度

> 原文：<https://dev.to/skptricks/react-native-button-width-fit-to-content-1dlc>

来源:[根据内容调整本机按钮宽度](https://www.skptricks.com/2019/02/react-native-button-width-fit-to-content.html)

本教程解释了如何使按钮宽度适合 react 本机应用程序中的内容。这是一个非常容易和简单的，只是我们需要用一些样式表设计将按钮组件包装在视图组件中。

使按钮宽度适合 react native 中的内容:
让我们看看下面完整的源代码，它有助于在 react native 应用程序中设置适合内容的宽度。
使用下面的布局设计来创建按钮。
应用下面的样式表设计。
/**

*   React 本地应用示例
*   [https://github.com/facebook/react-native](https://github.com/facebook/react-native)
*   @格式
*   @流量
*   @ lint-ignore-every xplatjscopyright 1 */

从“react”导入 React，{ Component }；
从“react-native”导入{平台，样式表，视图，按钮，文本}；

导出默认类应用程序扩展组件{

render() {
return (

```
 <View style={styles.buttonOuterLayout}>

      <View style={styles.buttonLayout}>
        <Button title="Login " />
      </View>

      <View style={styles.buttonLayout}>
        <Button title="Register " />
      </View>

      <View style={styles.buttonLayout}>
        <Button title="Logout " />
      </View>

      <View style={styles.buttonLayout}>
        <Button title="Forgot Password " />
      </View>

    </View>

  </View>

); 
```

}
}

const styles = style sheet . create({
container:{
flex:1，
justifyContent: 'center '，
}，
headerText: {
fontSize: 20，
textAlign: "center "，
margin: 10，
fontWeight: "bold"
}，
buttonOuterLayout:{
flex:1，
flexDirection: 'column '，
justifyContent: 'center '，【T11

截图:

这是所有关于反应原生按钮宽度适合内容。感谢你阅读这篇文章，如果你有任何问题，有关于这篇文章的另一个更好的有用的解决方案，请在评论区写消息。