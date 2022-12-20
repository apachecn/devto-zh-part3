# React Native 中的无状态组件

> 原文：<https://dev.to/daleljefferson/stateless-components-in-react-native-5aod>

随着 React 0.14 进入 React Native，我们现在有了在 React Native 中创建组件的第三种方式，即无状态组件。

## 三种方式

```
// Create Class
const Heading = createClass({
  render() {
    return <Text>{this.props.title}</Text>;
  }
});

// ES6 Classes
class Heading extends Component {
  render() {
    return <Text>{this.props.title}</Text>;
  }
}

// Stateless functions
const Heading = ({title}) => <Text>{title}</Text>; 
```

## 举例

```
// index.ios.js
import React, {
  AppRegistry,
  StyleSheet,
  View,
  Text
} from "react-native";

const styles = StyleSheet.create({
  page: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    backgroundColor: "rgb(142, 68, 173)"
  },
  heading: {
    fontSize: 36,
    fontWeight: "bold",
    color: "white"
  }
});

const Heading = ({title}) => (
  <Text style={styles.heading}>{title}</Text>
);

const App = () => (
  <View style={styles.page}>
    <Heading title="Stateless" />
  </View>
);

AppRegistry.registerComponent("MyApp", () => App); 
```

在这个例子中，我使用了项目根中的无状态组件，但是显然，您至少需要一个有状态组件。一个好的模式是让容器组件使用无状态组件包装表示性组件。

无状态组件没有生命周期方法或明显的状态，但它们确实减少了您必须编写的样板代码的数量。

编辑:Scott Feeney 指出，无状态组件可以有 propTypes 和 defaultProps。

```
const Heading = ({title}) => (
    <Text style={styles.heading}>
        {title}
    </Text>
)

Heading.propTypes = { PropTypes.string }
Heading.defaultProps = { title: 'Stateless' } 
```