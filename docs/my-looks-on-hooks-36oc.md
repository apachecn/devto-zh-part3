# ðæå¯¹âÂ·è¡åæ¯ççæ³

> åæï¼<https://dev.to/droidmakk/my-looks-on-hooks-36oc>

> å¾ä¹ä»¥åææç±çä¸ç¯æç« ãä½ ä¸éè¦èä¸æ¥ç»´ææªæ¥çç¶æ

### åè½æ§æç¶æç»ä»¶ð¡

é£æ¯ä¸ä¸ªè¯åï¼

æä»¥è¿éæä»¬æä¸ç§ä¼ ç»çæ¹å¼æ¥åå»ºæç¶æçç»ä»¶ï¼æ¯å¦`Class App extends`ç­ç­ç­ç­...

ä½ä¸æ¯è¿æ ·ï¼è¿éæä»¬æåè½æ§çæç¶æç»ä»¶ï¼å¦ä¸æç¤ºã

```
import React, { useState } from 'react'; //Importing modules

function App() {

  const [date, updateDate] = React.useState(Date());

  return (
    <div className="App">
      <h1>{date}</h1>
      <button onClick={() => {
        updateDate(Date())
      }}> Update </button>
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

é·å...ð

## ä½¿ç¨ææ

ä¸æ¯è¿ä¸ª...æä»¬çå£æ©±éè¿ææ´é·çä¸è¥¿ãæ¢ç¶æä»¬ç¥éäºå¦ä½å¨åè½ç»ä»¶ä¸­ç»´æ¤ç¶æãå¦æé¨ä»¶ä¸­æä¸äºå½±åï¼æä»¬è¯¥å¦ä½ååº...ð¤ççåªæ¯`useEffect`ðã

è®©æä»¬æ¥å¤çææçååã

```
import React, { useState, useEffect } from 'react'; //Importing modules

function App() {

  let count_p = React.createRef();

  const [date, updateDate] = React.useState(Date());

  React.useEffect(() => {
    count_p.current.textContent = Number(count_p.current.textContent)+1;
  });

  return (
    <div className="App">
      <h1>{date}</h1>
      <button
        onClick={() => {
          updateDate(Date());
        }}
      >
        {"  "}
        Update{"  "}
      </button>
      <p>You've updated dated <span ref={count_p} ></span> time(s)</p>
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

æä»¥æ¯æ¬¡æ´æ° state æ¶ï¼é½ä¼è°ç¨æ¹æ³`useEffect`ãæä»¬èµ°å§ã
æä»¬å¨è¿éä½¿ç¨äº Refï¼ææ´å¥½çæ¹æ³åï¼ð

## useRef

ä»ä¸é¢çä¾å­ä¸­æä»¬å¯ä»¥ä½¿ç¨å¦ä¸ä¸ªé©å­ã`useRef`ãè®©æä»¬å©ç¨è¿ä¸ç¹ãT3ã

```
import React, { useState, useEffect, useRef } from 'react'; //Importing modules

function App() {

  let count_p = useRef(null);

  const [date, updateDate] = React.useState(Date());

  useEffect(() => {
    count_p.current.textContent = Number(count_p.current.textContent)+1;
  });

  return (
    <div className="App">
      <h1>{date}</h1>
      <button
        onClick={() => {
          updateDate(Date());
        }}
      >
        Update
      </button>
      <p>You've updated dated <span ref={count_p} ></span> time(s)</p>
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

## ä½¿ç¨ä¸ä¸æ

å æ­¤ï¼åå¸çä¸ä¸æå·²æä¸ºéçä¸ä¸æãç°å¨æäºä¸ç§æ°çåæ³ãåè®¾ææä¸ä¸ªä¸ä¸æï¼å«åå®éä¸ä¸æãå¨è¿å¼ åå­ä¸ï¼ææ¯å¹´é½æ·»å æ°çå®éãä¸æ¦æè¿æ ·åäºï¼æå¸æå®è½è¢«åå¨ææçéç»ååºéãæä»¥æä»¬ç¨äºä¸ä¸ªå«å**çä¸è¥¿æ¥æè¿°**ãè®©æä»¬ççå®æ¯å¦ä½å·¥ä½çã

```
// RationContext.js
import React,{ Component, createContext } from 'react';

// Create a context which gives Provider and Consumer
const RationContext = React.createContext({
    //Add initial values
    ration: [],
    addRation: () => null
});

//export the consumer
export RationConsumer = RationContext.Consumer;

//export the provider with state and other methods
export class RationProvider extends Component {

    constructor(props){
        super(props);
        this.addRation = this.addRation.bind(this);
        this.state = { ration: [] }
    }

    addRation = (ration) => {
        let { ration } = this.state;
        ration.push(ration);
        this.setState({ ration });
    }

    render(){
        let { ration } = this.state;
        let { children } = this.props;

        return(
            <RationContext.Provider value={{ ration, addRation }} >
                {children}
            </RationContext.Provider>
        )
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

æä»¥æä»¬å¿é¡»å¨æå¤ååºææä¾çéç»éãè®©æä»¬ççå¦ä½ä½¿ç¨é©å­æ¥åè¿ä»¶äºã
ä¸ºæ­¤æä»¬éè¦å¨`RationContext.js`
ä¸­æ´æ¹ä¸è¡

```
 const RationContext to export const RationContext 
```

Enter fullscreen mode Exit fullscreen mode

ç°å¨è®©æä»¬å¨ RationList.js ä¸­ååºéç»é

```
 import React,{ useContext } from 'react';
    import { RationContext } from './RationContext';

    export const RationList = () => {
        let { rations } = useContext(RationContext);

        return(
            <div>
                <ul>
                    {rations.map(ration => <li>{ration}</li>)}
                </ul>
            </div>
        )
    } 
```

Enter fullscreen mode Exit fullscreen mode

é·æ¯äºãç°å¨è®©æä»¬ä» AddRation.js
æ·»å  ration

```
 import React,{ useContext, useState } from 'react';
    import { RationContext } from './RationContext';

    export const AddRation = () => {
        let { addRation } = useContext(RationContext);

        updateRation = (e) => { e.keyCode === 13 ? addRation(e.value) : '' }

        return(
            <div>
                <input type="text" placeholder="Ration name" onKeyDown={updateRation}/>
            </div>
        )
    } 
```

Enter fullscreen mode Exit fullscreen mode

èæ¯ä¸æ¯é­æ³ï¼å®ä½äºå¤§æ çé¡¶ç«¯ðµð¼ââï¸ï¼å¹¶è®©å®çåå·¥(æ¶è´¹è)ä¸ºå®å·¥ä½ãè¿éæä»¬èè *App.js* æ¯ App çå¥å£ãç°å¨è®©æä»¬æ¥çç App.js.

```
// App.js
import React from 'react';
import { RationProvider } from './RationContext';
import { RationList } from './RationList';
import { AddRation } from './AddRation';

const App = (props) => {
    return(
        <RationProvider>
            <RationList />
            <AddRation />
        </RationProvider>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

ç°å¨ææ³ä½ ä¸å®å¯¹ React é©å­æäºå¾å¥½çæ¦è¿°ãä½æ¯ä»ç¶ææ´å¤çé©å­éè¦è¦çãæå¾ç¬¬äºé¨åå¾å¿«ð¤ãç´å°é£æ¶åè§...ç¼ç å¿«ä¹ï¼