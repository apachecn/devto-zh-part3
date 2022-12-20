# 🚫😩react 引用的数组

> 原文：<https://dev.to/ajsharp/-an-array-of-react-refs-pnf>

显然你不能在一个数组中存储 [React refs](https://reactjs.org/docs/refs-and-the-dom.html) 。由于某种原因，它们被清除了，所以如果你需要存储一个参考文献的集合，你必须这样做(原谅我，上帝，因为我犯了罪):

```
import React from 'react'

const collection = ["label 1", "label 2"]

class SinFactory extends React.Component {
  constructor(props) {
    super(props)
    this.ref0 = React.createRef()
    this.ref1 = React.createRef()
  }

  render() {
    return (
      <div>
        {collection.map((label, i) => {
          return <div key={label} 
            ref={this[`ref${i}`]}>{label}
          </div>
        })}
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这真的很脏，但它的工作。

* * *

☝️ ***一定要看看 [Sharesecret](https://www.sharesecret.co) ，它让安全共享敏感数据变得很容易。***