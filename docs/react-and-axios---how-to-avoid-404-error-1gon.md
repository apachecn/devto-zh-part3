# React 和 Axios -如何避免 404 错误

> 原文：<https://dev.to/kayut/react-and-axios---how-to-avoid-404-error-1gon>

我正在尝试编写我的第一个 React 应用程序。在有两个输入字段的表单中，用户在输入字段中输入国家和城市的名称，然后提交表单。
然后，城市和国家变量被传递给下面的组件，该组件将向 openweatehrmap API 发送 API 请求并显示结果。

```
import React, { Component } from 'react';
import axios from 'axios';
import Form from './Form/Form';

class Weather extends Component {
  state = {
    country: '',
    city: '',
    currentTemp: '',
    error: undefined,
  }

  getWeather = async (e) => {
    e.preventDefault();
    const URL = 'http://api.openweathermap.org/data/2.5/weather?q='
    const API_KEY = "8b46d478a9...34a69";
    const country = e.target.elements.country.value;
    const city = e.target.elements.city.value;
    try {
      if (country && city) {
        const response = await 
axios.get(`${URL}${city},${country}&appid=${API_KEY}&units=metric`);
        const data = await response;
        console.log(data);
        this.setState({
          country: data.data.sys.country,
          city: data.data.name,
          currentTemp: data.data.main.temp,
          error: '',
        });
      } else {
        this.setState({
          country: undefined,
          city: undefined,
          currentTemp: undefined,
          error: 'Please enter a country and a city name'
        })
      }
    } catch (err) {
      this.setState({ error: err })
    }
  }

  render() {
    let showResult;
    if (this.state.error !== undefined && this.state.country && this.state.city) {
      showResult =
        <ul>
          <li>Location: {this.state.country} {this.state.city}</li>
          <li>Current Temperature: {this.state.currentTemp}° Celsius</li>
        </ul>
    }
    else {
      showResult = <div className="error">
        {this.state.error}
      </div>
    }

    return (
      <div>
        <Form onGetWeather={this.getWeather} />
        {showResult}
      </div>
    );
  }
}

export default Weather; 
```

如果您让一个或两个输入字段为空并提交表单，UI 会显示一个错误。
如果您填写了两个输入字段，并且国家和城市相关联(例如英国和伦敦)，用户界面将显示温度和其他一些信息。

**然而**，
如果你在两个输入框中只填写一些随机文本，或者如果国家和城市彼此不相关，那么一旦提交表单，应用程序就会崩溃。

我认为这是因为生成的 API-URL 不再有效。控制台显示以下错误:

[https://thepractical dev . S3 . Amazon AWS . com/I/zo 4 rzpflnai 896424 GQ 4 . png](https://thepracticaldev.s3.amazonaws.com/i/zo4rzpflnai896424gq4.png)

我花了一整天，也没找到办法修好。如何才能避免 app 崩溃？我如何验证生成的 API-URL？或者其他什么提示或者解决方法？