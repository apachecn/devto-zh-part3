# 使用 React 生命周期方法的异步/等待

> 原文：<https://dev.to/daleljefferson/async-await-with-react-lifecycle-methods-pn1>

如果你像我一样，你也会在 componentWillMount/componentDidMount 中完成大量的异步工作。本文将向您展示如何在 React 的生命周期事件中使用 Async/Await。React 的生命周期方法可以返回任何值，包括承诺。

## 无极版

```
componentDidMount() {
  fetch('https://example.com')
    .then((res) => res.json())
    .then((something) => this.setState({something}))
} 
```

## 异步/等待版本

```
async componentDidMount() {
  const res = await fetch('https://example.com')
  const something = await res.json()

  this.setState({something})
} 
```

注意:如果你确实从生命周期方法中返回了一个承诺，它将不会被等待。

我个人觉得 Async/Await 版本更容易阅读。

## 完整示例(React Native)

```
class AwesomeProject extends Component {
  state = {};

  setStateAsync(state) {
    return new Promise(resolve => {
      this.setState(state, resolve);
    });
  }

  async componentDidMount() {
    StatusBar.setNetworkActivityIndicatorVisible(true);

    const res = await fetch("https://api.ipify.org?format=json");
    const {ip} = await res.json();

    await this.setStateAsync({ipAddress: ip});

    StatusBar.setNetworkActivityIndicatorVisible(false);
  }

  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>
          My IP is {this.state.ipAddress || "Unknown"}
        </Text>
      </View>
    );
  }
} 
```