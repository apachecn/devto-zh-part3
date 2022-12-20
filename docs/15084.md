# 无法读取未定义的属性“props”

> 原文：<https://dev.to/kayut/cannot-read-property-props-of-undefined-2220>

如果我运行下面的代码，我得到错误:“无法读取未定义的属性‘props’”。两个文件在同一个文件夹中。你能解释一下我做错了什么吗？

**app . js**T2】

```
import React, { Component } from 'react';
import SeasonDisplay from './SeasonDisplay';

class App extends Component {
  state = {
    lat: null,
    errorMessage: ''
  };

  componentDidMount() {
    window.navigator.geolocation.getCurrentPosition(
      position => {
        this.setState({
          lat: position.coords.latitude
        });
      },
      err => {
        console.log(err);
        this.setState({ errorMessage: err.message });
      }
    );
  }

  render() {
    if (this.state.errorMessage && !this.state.lat) {
      return <div>Error: {this.state.errorMessage}</div>;
    }
    if (!this.state.errorMessage && this.state.lat) {
      // return <div>latitude is {this.state.lat}</div>;
      return <SeasonDisplay lat={this.state.lat} />;
    }
    return <div>Loading...</div>;
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

**seasondisplay . js**T2】

```
import React from 'react';

const SeasonDisplay = props => {
  return <div>{props.lat}</div>;
};

export default SeasonDisplay; 
```

Enter fullscreen mode Exit fullscreen mode