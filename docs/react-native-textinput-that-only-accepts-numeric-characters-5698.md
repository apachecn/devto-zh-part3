# 对只接受数字字符的本机文本输入做出反应

> 原文：<https://dev.to/skptricks/react-native-textinput-that-only-accepts-numeric-characters-5698>

* * *

## 来源:[反应只接受数字字符的原生文本输入](https://www.skptricks.com/2019/01/react-native-textinput-that-only-accept-numeric-characters.html)

本教程解释了**如何在 React Native** 应用程序中从 TextInput 中只获取数值。默认情况下，TextInput 组件不会在 qwerty 键盘上显示数字文本。默认情况下，Android 和 iOS 应用程序都会打开带有字母键、数字键和特殊符号键的**普通多用途键盘。但是，当开发者需要专门从用户那里获得电话号码或任何其他数值时，这是必须的，只有数字键盘在 TextInput 选择上打开，以便用户不会错误地输入字母值。**

[![](img/1842899ae24bdbd004ece0af00cb610b.png)](https://3.bp.blogspot.com/-m1BlWXvRN2Y/XEQXS4OzPCI/AAAAAAAACV0/sKgV7RolY6wneF9qiTeJQhfe3z2RFqzFACLcBGAs/s1600/numeric.png)

## **对只接受数字字符的原生文本输入做出反应**

在这个例子中，我们将创建 TextInput 组件，它将通过数字键盘只接受数字字符。为此我们需要在 **[TextInput 组件](https://www.skptricks.com/2018/07/working-with-textinput-in-react-native.html)** 中显式指定 **keyboardType={'numeric'}** 属性。

让我们看看下面完整的源代码，它有助于在 android 或 ios qwerty 键盘上显示数值。

```
/\*\*  
 \* Sample React Native App  
 \* https://github.com/facebook/react-native  
 \* @flow  
 \*/  

import React, { Component } from "react";  
import { Platform, StyleSheet, View, Button, TextInput, } from "react-native";  

export default class App extends Component {  

  render() {  
return (  
<View style={styles.container}>  

<TextInput  
          placeholder="Enter Your Mobile Number"  
          underlineColorAndroid='transparent'  
          style={styles.TextInputStyle}  
**keyboardType={'numeric'}**  
/>  

</View>  
);  
}  
}  

const styles = StyleSheet.create({  
  container: {  
    flex: 1,  
    justifyContent: 'center',  
},  
  headerText: {  
    fontSize: 20,  
    textAlign: "center",  
    margin: 10,  
    fontWeight: "bold"  
},  
TextInputStyle: {  
    textAlign: 'center',  
    height: 40,  
    borderRadius: 10,  
    borderWidth: 2,  
    borderColor: '#009688',  
    marginBottom: 10  
}  
}); 
```

**截图:**

[![React Native TextInput that only accepts numeric characters](img/674db2ca973295406748b7ea6b30daa2.png "React Native TextInput that only accepts numeric characters")](https://2.bp.blogspot.com/-Ul2szA7SpNc/XEQWizz4daI/AAAAAAAACVs/HikdDNlb3XM63yOOCzlQfmIQgIQTRy_TQCLcBGAs/s1600/Screenshot_1547526127.png) 
这都是关于**只接受数字字符的 React 原生文本输入**。感谢你阅读这篇文章，如果你有任何问题，有关于这篇文章的另一个更好的有用的解决方案，请在评论区写消息。