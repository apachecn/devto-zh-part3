# 使用 React.js 旋转动态元素

> [https://dev . to/sergiodxa/旋转-动力-com-reacjs-4oe 0](https://dev.to/sergiodxa/carrusel-de-elementos-dinmicos-con-reactjs-4oe0)

*最初发表于[https://sdx . im/articles/Carriles-elements-dynamic-react](https://sdx.im/articles/carrusel-elementos-dinamicos-react)T3]*

想象一下下面的情况，我们有一个项目列表需要横向显示，这些项目不会显示出来，所以我们想放一个旋转木马在它们之间移动，但是这些项目的大小不同，有些项目的宽度是 100px，有些项目的宽度是 300px 等等。

让我们看一下如何装备一个作为子项接收元素列表的 react 组件，并创建一个水平页面，使旋转操作能够在到达屏幕上的最后一个元素时移动以显示下一组元素。

> **注**:这一切都是基于*真实的组成部分*我不得不为我目前正在工作的项目装配的。

为此，我们将通过扩展“`React.Component`”来创建简单的反应器组件。

```
import React, { Component } from "react";

class Carousel extends Component {
  render() {
    return null;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将如何使用此组件将如下所示

```
import React from "react";
import { render } from "react-dom";

import Carousel from "./carousel";

function App() {
  return (
    <Carousel component="ul" leftPadding={100} focus={0}>
      <li>Featured</li>
      <li>Highlighted</li>
      <li>Top</li>
      <li>For You</li>
      <li>Trending</li>
      <li>Coming Soon</li>
    </Carousel>
  );
}

render(<App />, document.getElementById("root")); 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所见，我们的组件将收到四个 props，第一个是经典的`children`及其子项列表。

这是一种服务，它为用户提供个性化的组件标签，帮助用户理解 hijos 元素。

第三个是`leftPadding`，它将用于定义页面切换时必须始终留在左侧的空间，这样下一页的元素就不会粘贴到我们的旋转木马屏幕或容器的边缘。

第四个，也是最后一个，让我们告诉您目前的焦点是什么，这将有助于我们了解使用者的位置。

对于组件，我们必须做好准备。

```
import React, { Component } from "react";

class Carousel extends Component {
  render() {
    // armamos nuestro objeto con los estilos que vamos a aplicar para mover el carrusel
    const style = {
      transition: "transform 200ms linear", // agregamos una transición de 200ms linear a la propiedad transform
      transform: `translateX(-${this.state.x}px)` // aplicamos un translateX en base a un valor del state llamado x
    };

    return (
      <this.props.component
        children={this.props.children}
        style={style} // nuestro componente custom debe soportar un prop `style` para aplicar estilos inline */}
      />
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们将开始设置逻辑，定义一个‘t0’，使我们能够在属性‘t1’发生变化时了解情况，并计算旋转木马的新位置。

```
import React, { Component } from "react";

class Carousel extends Component {
  state = {
    x: 0,
  };

  componentDidUpdate(prevProps) {
    // si los props cambiaron
    if (prevProps.focus !== this.props.focus) {
      // movemos el carrusel para la izquierda o derecha (-1 izquierda, 1 derecha)
      // ej. está en 2 y antes estaba en 1 entonces se mueve a la derecha
      this.move(this.props.focus - prevProps.focus)
    }
  }

  render() {
    // armamos nuestro objeto con los estilos que vamos a aplicar para mover el carrusel
    const style = {
      transition: "transform 200ms linear", // agregamos una transición de 200ms linear a la propiedad transform
      transform: `translateX(-${this.state.x}px)` // aplicamos un translateX en base a un valor del state llamado x
    };

    return (
      <this.props.component
        children={this.props.children}
        style={style} // nuestro componente custom debe soportar un prop `style` para aplicar estilos inline */}
      />
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

此方法将接收旧焦点并检查其是否更改，如果更改，则会减去当前焦点减去先前的焦点，这将给出`-1`或`+1`，具体取决于它是向左移动还是向右移动`+1`，此值将传递给一个方法让我们看看如何实施它。

```
import React, { Component } from "react";

class Carousel extends Component {
  state = {
    x: 0,
    currentPage: 1,
  };

  componentDidUpdate(prevProps) {
    // si los props cambiaron
    if (prevProps.focus !== this.props.focus) {
      // movemos el carrusel para la izquierda o derecha (-1 izquierda, 1 derecha)
      // ej. está en 2 y antes estaba en 1 entonces se mueve a la derecha
      this.move(this.props.focus - prevProps.focus)
    }
  }

  move = (direction = 0) => {
    // obtenemos los tamaños de todos los elementos la primera vez
    // o los traemos de los que ya calculamos en this.sizes.
    this.sizes = this.sizes || this.calculateSizes()
    // obtenemos la página a la que pertenece el nuevo elemento
    const { page } = this.sizes[this.props.focus];
    // si la página no cambió no hacemos nada
    if (this.state.currentPage === page) return;
    // obtenemos el punto de inicio del primer elemento de la página
    const { start } = this.sizes.find(element => element.page === page);
    // actualizamos el estado
    this.setState(state => ({
      // guardamos la nueva página
      currentPage: page,
      // guardamos la nueva posición en X usando el punto de inicio menos el leftPadding
      x: start - this.props.leftPadding < 0 ? 0 : start - this.props.leftPadding
    }));
  };

  render() {
    // armamos nuestro objeto con los estilos que vamos a aplicar para mover el carrusel
    const style = {
      transition: "transform 200ms linear", // agregamos una transición de 200ms linear a la propiedad transform
      transform: `translateX(-${this.state.x}px)` // aplicamos un translateX en base a un valor del state llamado x
    };

    return (
      <this.props.component
        children={this.props.children}
        style={style} // nuestro componente custom debe soportar un prop `style` para aplicar estilos inline */}
      />
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经有了移动我们的旋转木马的功能，它是有评论的，但让我们看看它是如何工作的。首先，我们确定我们已经计算了大小，如果没有计算，则调用方法`calculateSizes`。

然后，如果当前页面(保存在状态中)与新页面相同，我们将从大小列表中获取具有焦点的项目，并返回到它所属的页面(我们将看到计算结果)。

然后，我们获取页面上的第一个元素，并从该元素中提取它所在的像素位置。最后，我们通过保存当前页面和 x 轴上的旋转木马位置来更新状态，计算方法是:如果结果小于 0，则将结果放在 0，否则放在 0(这将使第一页起作用)。

现在，让我们来看看如何计算旋转木马的大小和页面，这是最重的逻辑。

```
import React, { Component, createRef } from "react";

class Carousel extends Component {
  state = {
    x: 0,
    currentPage: 1,
  };

  $carousel = createRef();

  componentDidUpdate(prevProps) {
    // si los props cambiaron
    if (prevProps.focus !== this.props.focus) {
      // movemos el carrusel para la izquierda o derecha (-1 izquierda, 1 derecha)
      // ej. está en 2 y antes estaba en 1 entonces se mueve a la derecha
      this.move(this.props.focus - prevProps.focus)
    }
  }

  calculateSizes = () => {
    // obtenemos la lista de elementos del DOM de los children
    const children = this.$carousel.current.children;
    // obtenemos el width del elemento que representa nuestro carrusel
    const pageWidth = this.$carousel.current.clientWidth;

    const { elements } = Array.from(children) // convertimos a un array
      .map(child => child.getBoundingClientRect()) // obtenemos su posición en x/y y su tamaño en width/heigh
      .map(({ x, width }) => ({
        start: x, // guardamos x como start
        width, // guardamos el width
        end: x + width, // calculamos donde termina el elemento sumando x y width
      }))
      .reduce(
        (result, { end, start, width }) => {
          // calculamos la paǵina (abajo vamos a ver la explicación)
          const page = Math.ceil((end + result.rest + this.props.leftPadding) / pageWidth);

          // devolvemos el resto de la página, la última página calculada y la lista de elementos con su página
          return {
            lastPage: result.lastPage !== page ? page : result.lastPage,
            elements: result.elements.concat({ width, start, end, page }),
            rest: result.lastPage !== page ? pageWidth * result.lastPage - start : result.rest,
          };
        },
        { rest: 0, lastPage: 1, elements: [] }, // empezamos el reduce con resto 0, página 1 y sin elementos
      );

    // devolvemos la lista de elementos
    return elements;
  };

  move = (direction = 0) => {
    // obtenemos los tamaños de todos los elementos la primera vez
    // o los traemos de los que ya calculamos en this.sizes.
    this.sizes = this.sizes || this.calculateSizes()
    // obtenemos la página a la que pertenece el nuevo elemento
    const { page } = this.sizes[this.props.focus];
    // si la página no cambió no hacemos nada
    if (this.state.currentPage === page) return;
    // obtenemos el punto de inicio del primer elemento de la página
    const { start } = this.sizes.find(element => element.page === page);
    // actualizamos el estado
    this.setState(state => ({
      // guardamos la nueva página
      currentPage: page,
      // guardamos la nueva posición en X usando el punto de inicio menos el leftPadding
      x: start - this.props.leftPadding < 0 ? 0 : start - this.props.leftPadding
    }));
  };

  render() {
    // armamos nuestro objeto con los estilos que vamos a aplicar para mover el carrusel
    const style = {
      transition: "transform 200ms linear", // agregamos una transición de 200ms linear a la propiedad transform
      transform: `translateX(-${this.state.x}px)` // aplicamos un translateX en base a un valor del state llamado x
    };

    return (
      <this.props.component
        ref={this.$carousel}
        children={this.props.children}
        style={style} // nuestro componente custom debe soportar un prop `style` para aplicar estilos inline */}
      />
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法比较复杂，让我们逐步看看它是如何工作的。我们首先引用我们的组件，并使用它来获取子 DOM 节点及其宽度的列表。我们将 DOM 中的这个节点清单转换成阵列，然后使用阵列方法重复执行。

以下是使用`getBoundingClientRect()`将列表中的每个节点转换为其值，此 DOM 元素方法将返回具有以下属性的对象:`left`、`top`、`right`、`bottom`、`x`、`y`、`width`和]其中以`x`为`start`、`width`并加起来计算`end`，这告诉我们元素的起始位置、大小和终止位置。

以下是计算页面，为此，我们制作了一个`reduce`，其初始值为具有属性`rest`【值 0】、`lastPage`【值 1】和`elements`的对象，作为空数组。在我们的`reduce`的每一个迭代中，我们都会使用`Math.ceil((end + result.rest + this.props.leftPadding) / pageWidth)`方程计算页面，这是项的结尾加上上一页的其馀部分(`rest`加上`leftPadding`，再除以容器宽度，即每一页的宽度。

然后，我们通过计算其新值，返回一个与“`reduce`”开头具有相同特性的对象。第一，如果`lastPage`与我们计算更新的`lastPage`页面不同，那么，在我们的项目清单中，我们会将新对象与其计算的`width`、`start`、`end`和`page`连接起来。最后，我们计算其馀部分，仅在页面发生变化时才计算剩馀部分，这是取页面宽度、乘以最后一页并减去项目起点的结果。

如果某个项目从第 1 页开始，但在第 2 页结束，则该项目必须属于第 2 页，因为这是它在屏幕上完整显示的唯一方法。为此，在计算页面时，我们将当前页面的其馀部分加上“T0”结尾的位置，然后再加上所有这些内容

一旦我们做了所有的计算，我们只得到元素(它们的大小、位置和页面)，然后返回。

## Usando 挂钩

现在我们看到了它是如何工作的，我们将把它移植到胡克斯，看看如何才能更现代地做到这一点。

```
import React, { useRef, useState, useEffect } from "react";

function Carousel({ children, focus = 0, leftPadding = 0, component = "div" }) {
  // definimos nuestros estados
  const [x, setX] = useState(0);
  const [currentPage, setCurrentPage] = useState(1);
  // creamos refs para guardar valores que necesitamos guardar entre renders
  // pero que no se usan en la UI (no son estado)
  const $carousel = useRef(null);
  const sizes = useRef(null);
  const currentFocus = useRef(focus);

  useEffect(() => {
    // cada vez que cambio focus vamos a llamar a la función move
    move(focus - currentFocus.current);
    // y guardamos el nuevo foco
    currentFocus.current = focus;
  }, [focus])

  function calculateSizes() {
    // obtenemos la lista de elementos del DOM de los children
    const children = $carousel.current.children;
    // obtenemos el width del elemento que representa nuestro carrusel
    const pageWidth = $carousel.current.clientWidth;

    const { elements } = Array.from(children) // convertimos a un array
      .map(child => child.getBoundingClientRect()) // obtenemos su posición en x/y y su tamaño en width/heigh
      .map(({ x, width }) => ({
        start: x, // guardamos x como start
        width, // guardamos el width
        end: x + width, // calculamos donde termina el elemento sumando x y width
      }))
      .reduce(
        (result, { end, start, width }) => {
          // calculamos la paǵina (abajo vamos a ver la explicación)
          const page = Math.ceil((end + result.rest + leftPadding) / pageWidth);

          // devolvemos el resto de la página, la última página calculada y la lista de elementos con su página
          return {
            lastPage: result.lastPage !== page ? page : result.lastPage,
            elements: result.elements.concat({ width, start, end, page }),
            rest: result.lastPage !== page ? pageWidth * result.lastPage - start : result.rest,
          };
        },
        { rest: 0, lastPage: 1, elements: [] }, // empezamos el reduce con resto 0, página 1 y sin elementos
      );

    // devolvemos la lista de elementos
    return elements;
  }

  function move(direction = 0) {
    // obtenemos los tamaños de todos los elementos la primera vez
    // o los traemos de los que ya calculamos en this.sizes.
    sizes.current = sizes.current || calculateSizes()
    // obtenemos la página a la que pertenece el nuevo elemento
    const { page } = sizes.current[focus];
    // si la página no cambió no hacemos nada
    if (currentPage === page) return;
    // obtenemos el punto de inicio del primer elemento de la página
    const { start } = sizes.current.find(element => element.page === page);
    // actualizamos el estado
    setCurrentPage(page);
    setX(start - leftPadding < 0 ? 0 : start - leftPadding);
  }

  // armamos nuestro objeto con los estilos que vamos a aplicar para mover el carrusel
  const style = {
    transition: "transform 200ms linear", // agregamos una transición de 200ms linear a la propiedad transform
    transform: `translateX(-${x}px)` // aplicamos un translateX en base a un valor del state llamado x
  };

  const Component = component;
  return (
    <Component
      ref={$carousel}
      children={children}
      style={style} // nuestro componente custom debe soportar un prop `style` para aplicar estilos inline */}
    />
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

就像我们看到的代码变得更短、更简单。重要的是要记住胡克斯还不稳定，为了测试它，你需要用`react-dom@next`和`react-dom@next`安装试剂和试剂-dom。

## 结语

以此为例，我们刚刚为不同尺寸的孩子实施了我们的旋转木马，看起来有些复杂，但逻辑却相当简单。我们可以更好地添加到这个手推车中，例如支持`rtl`或使其更便于使用。

最后，在下面，你可以看到它是如何工作的(带类的版本)。

[https://codesandbox.io/embed/93yzmvzw0w](https://codesandbox.io/embed/93yzmvzw0w)