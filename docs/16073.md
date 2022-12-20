# 如何在 React 中循环这个对象？

> 原文：<https://dev.to/kayut/how-can-i-loop-over-this-object-in-react-16ob>

嗨，

我在学习反应。在下面的组件中，我导入了一个 JS 文件(data.js)，它看起来像一个包含对象的 JSON 文件。

在 TinTinTile 类的返回中，我使用该对象来显示所有数据。在我下面的例子中，我只能显示对象中的第一个数组。但是我需要一种方法来循环 jsonResponse.characters 数组。

现在我的问题是:
如何循环 jsonResponse.characters？

```
import React, { Component } from 'react';
import jsonResponse from './data';

console.log(jsonResponse);
// const ArrayLength = jsonResponse.characters.length;
// console.log(ArrayLength);

// jsonResponse.characters.foEach(function(i) {
//   console.log(i);
// });

class TinTinTile extends Component {
  state = {};
  render() {
    return (
      <ul id="container" className="cf">
        <li className="list-container">
          <img
            className="image-container"
            src={jsonResponse.characters[0].image_url}
            alt="kir"
          />
          <div className="text-container">
            <h4>Name: {jsonResponse.characters[0].name}</h4>
            <p>Job: {jsonResponse.characters[0].job}</p>
            <p>Age: {jsonResponse.characters[0].details.age}</p>

            <button className="btn">More details ...</button>
          </div>
        </li>
      </ul>
    );
  }
}

export default TinTinTile; 
```