# ES7 异步/等待与反应本机

> 原文：<https://dev.to/daleljefferson/es7-async-await-with-react-native-ica>

Async/Await 是用 JavaScript 编写异步代码的新语法。React Native 0.10 中增加了支持，现在已经达到 TC39 阶段 3(候选)。

## 举例

在本例中，使用承诺返回提取 polyfill。我们将显示 React 原生 GitHub 星计数。

```
const URL = "https://api.github.com/repos/facebook/react-native";

class StarCount extends Component {
  constructor() {
    super();

    this.state = {stars: "?"};
  }

  componentDidMount() {
    this.fetchData().done();
  }

  async fetchData() {
    const response = await fetch(URL);
    const json = await response.json();
    const stars = json.stargazers_count;

    this.setState({stars});
  }

  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.text}>
          React Native has {this.state.stars} stars
        </Text>
      </View>
    );
  }
} 
```

Async / await 可与任何基于 Promise 的 API 或您创建的 API 一起工作。

```
const getStars = () =>
  new Promise(resolve => {
    const FIVE_SECONDS = 5000;
    // Simulate async operation
    setTimeout(() => resolve(1234), FIVE_SECONDS);
  });
const fetchData = async () => {
  const stars = await getStars();
  // Do something with stars
}; 
```