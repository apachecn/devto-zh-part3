# 在 React Native JSX 中添加单行和多行注释语法

> 原文：<https://dev.to/skptricks/add-single-line-and-multiple-line-comments-syntax-in-react-native-jsx-3c8a>

* * *

## 来源:[在 React Native JSX 中添加单行和多行注释语法](https://www.skptricks.com/2019/01/add-single-line-and-multiple-line-commnent-in-react-native.html)

本教程解释了**如何在 react 原生应用**中添加单行和多行注释。正如您已经知道，注释行向用户提供了关于现有代码行及其功能的信息。react 原生应用中有 **2 种注释语法**。

[![Add Single Line And Multiple Line Comments Syntax in React Native JSX](img/72f6df72ec91203d95361976843d82fb.png "Add Single Line And Multiple Line Comments Syntax in React Native JSX")](https://1.bp.blogspot.com/-UAx5IYC_xek/XDrxOZJjBaI/AAAAAAAACUE/d8ZeyNuN37cRBp3ctvDhoMDdhAjqPbe-ACLcBGAs/s1600/comment.PNG)

**编程语言中注释行的重要性:**

1.  在程序中使用注释是优秀程序员的标志。
2.  因为它增强了程序的可理解性，也就是说，如果其他人走过你的代码行，他/她就很容易理解它的工作和功能。
3.  它使得代码的维护和修改更加容易，而不会产生新的错误。

## * *单行注释在 react native : **

```
export default class App extends Component {  

  constructor() {  
super();  
//this.state = { TextInputDisableStatus: true }  
}  

---------------  
---------------  
--------------- } 
```

## **react native 中的多行注释:**

```
------------------------  
-----------------------  
------------------------ 
 render() {  
return (  
<View style={styles.container}>  

<TextInput  
          placeholder="Enter Your Userame"  
          underlineColorAndroid='transparent'  
          style={[styles.TextInputStyle, { backgroundColor: this.state.TextInputDisableStatus ? '#FFF' : '#C0C0C0' }]}  
          editable={this.state.TextInputDisableHolder}  
/>  
{  
/\*  
          updated by : skptricks  
          updated on : 12 jan 2019  
          Description : This button helps to disable the textfield when user click on it...  
          \*/  
}  

<Button  
          onPress={this.onPressButton}  
          title="Update Username"  
          color="#841584"  
          accessibilityLabel="Update Username"  
/>  

</View>  
);  
} 
```

这就是 React Native 中的**单行和多行注释语法。感谢你阅读这篇文章，如果你有任何问题，有关于这篇文章的另一个更好的有用的解决方案，请在评论区写消息。**