# ð«ð©react å¼ç¨çæ°ç»

> åæï¼<https://dev.to/ajsharp/-an-array-of-react-refs-pnf>

æ¾ç¶ä½ ä¸è½å¨ä¸ä¸ªæ°ç»ä¸­å­å¨ [React refs](https://reactjs.org/docs/refs-and-the-dom.html) ãç±äºæç§åå ï¼å®ä»¬è¢«æ¸é¤äºï¼æä»¥å¦æä½ éè¦å­å¨ä¸ä¸ªåèæç®çéåï¼ä½ å¿é¡»è¿æ ·å(åè°æï¼ä¸å¸ï¼å ä¸ºæç¯äºç½ª):

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

è¿ççå¾èï¼ä½å®çå·¥ä½ã

* * *

âï¸ ***ä¸å®è¦çç [Sharesecret](https://www.sharesecret.co) ï¼å®è®©å®å¨å±äº«æææ°æ®åå¾å¾å®¹æã***