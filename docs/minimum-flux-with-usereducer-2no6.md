# 用户减少时的最小流量

> 原文：<https://dev.to/mizchi/minimum-flux-with-usereducer-2no6>

通过 React 注入状态和调度程序上下文。上下文和`useContext`

```
import React, { useReducer, useContext, Dispatch, ReactElement } from "react";
import ReactDOM from "react-dom";

type CounterState = {
  count: number;
};

const initialState: CounterState = { count: 0 };

function reducer(state: CounterState, action: any) {
  switch (action.type) {
    case "reset": {
      return initialState;
    }
    case "increment": {
      return { count: state.count + 1 };
    }
    case "decrement": {
      return { count: state.count - 1 };
    }
    default: {
      return state;
    }
  }
}

// Container
const CounterContext = React.createContext<CounterState>(null as any);
const DispatchContext = React.createContext<Dispatch<any>>(null as any);

function App({ initialCount }: { initialCount: number }) {
  const [state, dispatch] = useReducer(reducer, { count: initialCount });
  return (
    <CounterContext.Provider value={state}>
      <DispatchContext.Provider value={dispatch}>
        <Counter />
      </DispatchContext.Provider>
    </CounterContext.Provider>
  );
}

// Connected component
function Counter() {
  const state = useContext(CounterContext);
  const dispatch = useContext(DispatchContext);
  return (
    <div>
      Count: {state.count}
      <button onClick={() => dispatch({ type: "reset" })}>Reset</button>
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
    </div>
  );
}

ReactDOM.render(<App initialCount={2} />, document.querySelector(".root")); 
```