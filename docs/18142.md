# 具有 Percel 和 WebComponents 的最小 Elm 组件

> 原文：<https://dev.to/mizchi/minimum-elm-component-with-percel-and-webcomponents-2oh9>

```
yarn add parcel-bundle node-elm-compiler -D 
```

目录

```
elements/
  foo.js
  Foo.elm
run.js
index.html # import run.js
package.json 
```

## 写出 elm-foo 组件

```
-- elements/Foo.elm
import Html exposing (div, text)
main = div [] [text "foo"] 
```

```
// elements/foo.js
import { Elm } from "./Foo.elm";

customElements.define(
  "elm-foo",
  class extends HTMLElement {
    connectedCallback() {
      Elm.Main.init({ node: this });
    }
  }
); 
```

## 运行

挂载这个 elm-foo 元素。

```
// run.js
import "./elements/foo";
document.body.innerHTML = '<elm-foo></elm-foo>' 
```