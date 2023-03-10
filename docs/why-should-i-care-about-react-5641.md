# 我为什么要在乎反应？

> 原文：<https://dev.to/raddevon/why-should-i-care-about-react-5641>

假设您已经知道如何编写普通的 Javascript 或 jQuery 来做一些类似 app 的事情。您可以在没有任何框架的情况下获取数据、发送数据和操作 DOM。如果你已经可以不用 React 就能做自己想做的事情，为什么还要学 React 呢？

## 什么是*有什么反应？*

 *React 是一个 Javascript 库，可以更容易地为单页应用程序构建用户界面。您构建了 **[组件](https://reactjs.org/docs/components-and-props.html)** ，它们最终就像您自己的定制 HTML 标签一样在您的 Javascript 中工作。

你直接在你的 React 代码中写一些看起来像 HTML 的东西。它叫做 **JSX** 。您可以在您的 JSX 中包含 Javascript 表达式，这样您就可以做一些很酷的事情，比如在呈现的组件发生变化时输出数据。

您可以将一大堆实际的 HTML 元素填充到一个组件中，每次使用该组件时，浏览器都会呈现其中包含的所有元素。您的组件**还可以捆绑行为，甚至样式**,在您重用它的任何时候都会随之而来。

你的组件可以有状态。这是存储对该组件重要的数据的变量的集合。如果组件的状态发生变化，可以更新渲染的组件以反映状态的变化。

每个组件都有一个 **[生命周期](https://reactjs.org/docs/react-component.html#the-component-lifecycle)** 。您可以在组件的预定义生命周期方法中编写自己的代码。当该方法描述的事件发生时，React 运行您的生命周期方法。以下是生命周期方法的几个示例以及触发它们的原因:

*   `constructor()`–组件创建后立即运行。
*   `componentDidMount()`–将组件添加到 DOM 后运行。这是一个从数据库或外部 API 获取数据以在组件中使用并将其添加到组件状态的好地方。
*   `componentDidUpdate()`–React 更新组件时运行。当组件的状态改变或者当组件的一个属性获得一个新值时，这个函数将运行。

这是一个简单的反应，尽管这是一个庞大的主题。这篇文章不打算作为一个教程；我只是想给你足够的背景，让你知道为什么它是有用的。如果你想*学习*反应，你可以从[官方教程](https://reactjs.org/tutorial/tutorial.html)开始。

## 更新 DOM 中的数据是件痛苦的事🍑

更新数据是一件痛苦的事情，原因并不是普通的 Javascript 很难做到；更多的是事后很难推理。实际过程非常简单。我只需选择一个元素并更改它的`innerHTML`属性。瞧啊。数据更新。

```
const elementToChange = document.querySelector('h1');
elementToChange.innerHTML = "New Heading Value"; 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们必须回头维护这个应用程序时，我们需要查看我们已经更新了 DOM 的所有不同位置。如果页面上的数据出现错误，那么任何一点都可能出现问题。通过`innerHTML`将数据直接输入 DOM 也很危险，因为攻击者可能会在您的站点上运行他们自己的代码，窃取您用户的信息。

在 React 中，我们维护对应用程序重要的数据的状态，并让 React 负责在数据改变时更新 DOM。

## 广泛支持的组件

考虑一个签到表。在我们的头脑中，它是一个单一的东西，但是，在 web 上，它通常是五个不同的元素:两个标签、两个输入框和一个按钮。

如果在构建应用程序的时候，你能把它想成一个单独的组件，那不是很好吗？好消息，各位！Web Components 让你不需要任何框架就能做到…只要你只需要支持 Chrome、Firefox 和 Opera 的更高版本。

这很好，但是您可能需要更广泛的浏览器支持。React 允许你构建与 IE 9+和所有现代浏览器兼容的组件，这样，下次你想在视图中添加一个登录表单时，**你可以像这样添加一个登录表单【T1:** 

```
<SignInForm /> 
```

Enter fullscreen mode Exit fullscreen mode

你必须自己构建一次那个组件(因为 React 天生不知道你的应用程序中应该有什么“SignInForm”)，但关键是**你可以选择只构建*一次*一次**并尽你所能地重用它。无论它走到哪里，它的样子和行为都会跟着它走。

> 需要帮助成为一名网站开发人员吗？无论你在过渡的哪个阶段，我都能帮你。在[拉德德文郡](https://raddevon.com/)请求一次免费辅导！

## 举个例子:日出时分

为了展示这两者的区别，我构建了一个简单的应用程序，它可以显示给定纬度和经度的日出和日落时间。

### 日出时分(香草 JS)

这是该应用程序普通版本的 Javascript 代码。如果你想看 HTML 和 CSS，看看下面 Codepen 演示中的标签。

```
function debounced(delay, fn) {
  let timerId;
  return function(...args) {
    if (timerId) {
      clearTimeout(timerId);
    }
    timerId = setTimeout(() => {
      fn(...args);
      timerId = null;
    }, delay);
  };
}

function updateTimes(lat, long) {
  if (lat && long) {
    return fetch(`https://api.sunrise-sunset.org/json?lat=${lat}&lng=${long}`)
      .then(response => {
        if (!response.ok) {
          sunriseTimeElement.innerHTML = "Invalid";
          sunsetTimeElement.innerHTML = "Invalid";
          throw Error(`${response.status} response on times request`);
        }
        return response.json();
      })
      .then(data => {
        sunriseTimeElement.innerHTML = data.results.sunrise;
        sunsetTimeElement.innerHTML = data.results.sunset;
      })
      .catch(error => {
        console.error(error.message);
      });
  }
}

function updateTimesFromInput() {
  const lat = latField.value;
  const long = longField.value;
  updateTimes(lat, long);
}

const updateTimesFromInputDebounced = debounced(500, updateTimesFromInput);

const sunriseTimeElement = document.querySelector(".sunrise .time");
const sunsetTimeElement = document.querySelector(".sunset .time");
const latField = document.querySelector("#lat");
const longField = document.querySelector("#long");

navigator.geolocation.getCurrentPosition(function(position) {
  const lat = position.coords.latitude;
  const long = position.coords.longitude;
  latField.value = lat;
  longField.value = long;
  updateTimes(lat, long);
});

[latField, longField].forEach(field => {
  const events = ["keyup", "change", "input"];
  events.forEach(event => {
    field.addEventListener(event, updateTimesFromInputDebounced);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

参见 [CodePen](https://codepen.io) 上 Devon Campbell ( [@raddevon](https://codepen.io/raddevon) )的 Pen [Sunrise app(香草变奏曲)](https://codepen.io/raddevon/pen/OBGoNL/)。

您会注意到这个版本的一些优点。比 React 版本短很多。这部分是因为它不使用组件(这在 React 版本中增加了一些开销)，部分是因为状态都在文档中(这使得很难维护和判断何时出错)。

其他一些可能有助于您了解背景的注释:

*   我使用了数字字段，所以我不需要验证。实际上，我仍然应该进行验证，因为不是所有的浏览器都支持数字字段，但是我并不担心这个例子是否可以投入生产。
*   整个文档(标题、表单和输出)已经在 HTML 文档中表示出来了。我们在 Javascript 中所做的就是在从新输入中获得结果后更新输出的值。
*   因为我的代码依赖于选择正确的元素，所以如果有人来到我身后更改了元素的类或 id，应用程序可能会崩溃。这就是我选择它们的方式。由于状态是在那里维护的，如果我无法访问它们，应用程序就无法完成它需要做的事情。
*   请注意我必须绑定多个事件的繁琐方式，以确保捕捉到对 lat 和 long 字段的任何更改。数字字段根据它们被改变的方式发出不同的事件，所以我必须为每个字段创建一个绑定。(你会在 Javascript 的最后明白我的意思。)
*   你会注意到这个应用的这个和 React 版本都有一个奇怪的`debounced`功能。时间来自一个 API。我不想让我的 API 不受欢迎，所以我真的不想为我的任何用户的每一次按键发出请求。去抖功能限制了调用一个函数的频率。这个 app 最多半秒钟就会向 API 发出一次请求。

### 【日出时间】(React)

这是 React 版本的 Javascript 代码。同样，如果你好奇的话，你可以在下面嵌入的 Codepen 演示中看到 HTML 和 CSS。

```
function isNumeric(n) {
  return !isNaN(parseFloat(n)) && isFinite(n);
}

function debounced(delay, fn) {
  let timerId;
  return function(...args) {
    if (timerId) {
      clearTimeout(timerId);
    }
    timerId = setTimeout(() => {
      fn(...args);
      timerId = null;
    }, delay);
  };
}

class CoordinatesForm extends React.Component {
  constructor(props) {
    super(props);
  }

  render() {
    return (
      <form>
        <LatField
          updateLat={newLat => {
            this.props.updateCoords({ lat: newLat });
          }}
          lat={this.props.lat}
        />
        <LongField
          updateLong={newLong => {
            this.props.updateCoords({ long: newLong });
          }}
          long={this.props.long}
        />
      </form>
    );
  }
}

class LatField extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      lat: this.props.lat
    };
  }

  componentDidUpdate(prevProps) {
    const lat = this.props.lat;
    if (lat !== prevProps.lat) {
      this.setState({ lat });
    }
  }

  updateLat = event => {
    let newLat = event.target.value;
    if (!newLat) {
      this.setState({ lat: 0 });
      return this.props.updateLat(0);
    }

    if (isNumeric(newLat)) {
      newLat = parseFloat(newLat);
    } else if (newLat !== "-") {
      return;
    }
    this.setState({ lat: newLat });
    this.props.updateLat(newLat);
  };

  render() {
    return (
      <label for="">
        lat:
        <input
          type="text"
          id="lat"
          value={this.state.lat}
          onChange={this.updateLat}
        />
      </label>
    );
  }
}

class LongField extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      long: this.props.long
    };
  }

  componentDidUpdate(prevProps) {
    const long = this.props.long;
    if (long !== prevProps.long) {
      this.setState({ long });
    }
  }

  updateLong = event => {
    let newLong = event.target.value;
    if (!newLong) {
      this.setState({ long: 0 });
      return this.props.updateLong(0);
    }

    if (isNumeric(newLong)) {
      newLong = parseFloat(newLong);
    } else if (newLong !== "-") {
      return;
    }
    this.setState({ long: newLong });
    this.props.updateLong(newLong);
  };

  render() {
    return (
      <label for="">
        long:
        <input
          type="text"
          id="long"
          value={this.state.long}
          onChange={this.updateLong}
        />
      </label>
    );
  }
}

class TimesDisplay extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      sunrise: "Unknown",
      sunset: "Unknown"
    };
  }

  componentDidUpdate(prevProps) {
    const lat = this.props.lat;
    const long = this.props.long;
    if (lat !== prevProps.lat || long !== prevProps.long) {
      this.updateTimesDebounced(lat, long);
    }
  }

  updateTimes = (lat, long) => {
    if (isNumeric(lat) && isNumeric(long)) {
      return fetch(`https://api.sunrise-sunset.org/json?lat=${lat}&lng=${long}`)
        .then(response => {
          if (!response.ok) {
            this.setState({
              sunrise: "Invalid",
              sunset: "Invalid"
            });
            throw Error(`${response.status} response on times request`);
          }
          return response.json();
        })
        .then(data => {
          this.setState({
            sunrise: data.results.sunrise,
            sunset: data.results.sunset
          });
        })
      .catch(error => {
        console.error(error.message);
      });
    }
  }

  updateTimesDebounced = debounced(500, this.updateTimes)

  render() {
    return (
      <div>
        <SunriseTime time={this.state.sunrise} />
        <SunsetTime time={this.state.sunset} />
      </div>
    );
  }
}

class SunriseTime extends React.Component {
  render() {
    return <div class="sunrise">Sunrise: {this.props.time}</div>;
  }
}

class SunsetTime extends React.Component {
  render() {
    return <div class="sunset">Sunset: {this.props.time}</div>;
  }
}

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      lat: 0,
      long: 0
    };
  }

  updateCoords = updateObject => {
    this.setState(updateObject);
  };

  componentDidMount() {
    navigator.geolocation.getCurrentPosition(position => {
      const lat = position.coords.latitude;
      const long = position.coords.longitude;
      this.setState({ lat, long });
    }, error => {
      console.error('Couldn\'t get your current position from the browser');
    });
  }

  render() {
    return (
      <div>
        <CoordinatesForm
          lat={this.state.lat}
          long={this.state.long}
          updateCoords={this.updateCoords}
        />
        <TimesDisplay lat={this.state.lat} long={this.state.long} />
      </div>
    );
  }
}

ReactDOM.render(<App />, document.querySelector(".app")); 
```

Enter fullscreen mode Exit fullscreen mode

参见 [CodePen](https://codepen.io) 上 Devon Campbell ( [@raddevon](https://codepen.io/raddevon) )的 Pen[Sunrise app(React variation)](https://codepen.io/raddevon/pen/KGYEVY/)。

我是 React 的粉丝，但是我承认我很震惊 React 需要这么多代码来完成。这主要是因为我已经组件化了所有的东西*和*，我必须在组件之间共享状态。我的意思是。

有一个我称之为“App”的外部组件，它包含所有其他内容，并保存所有其他组件使用的状态。app 组件内部有两个组件:“CoordinatesForm”和“TimesDisplay”它们各有两个组成部分，分别对应于它们的两个值(分别为纬度/经度和日出/日落)。用户将在例如“纬度”字段中进行改变。这种变化需要反映在应用程序的状态中，因为除非通过一个共同的祖先，否则组件不能容易地共享数据。

这意味着，我在组件树中上下传递状态。在 React 中，要设置父组件的状态，我需要在父组件上创建一个设置其状态的方法。然后，我可以通过 **props** (React 对传递到子组件中的数据的命名)将它传递给子组件。子节点调用该方法并传入他们需要的数据，以进入祖先的状态。

为了让数据下行，我只是不断地通过 props 传递它，直到它到达需要它的组件。这并不特别难做，但是比直接操作 DOM 需要更多的代码。

所有这一切的好处是，即使我不得不变得有点复杂，我只需要改变状态。我从来不需要编写任何脆弱的 DOM 操作。因为我使用的是在渲染组件中传递的属性，所以当这些属性改变时，React 会智能地更新组件。

更多内容:

*   这个版本的 HTML 文档非常稀疏。这是因为页面的大部分内容都是在组件呈现时由 React 呈现的。HTML 只需要一个元素来呈现 React 应用程序。
*   React 似乎不想与我在另一个应用程序中使用的数字输入合作，所以我决定在这里使用标准的文本输入，并进行一些验证。验证也增加了一些代码。可能有一种方法可以让数字输入工作得更好，但这个应用程序目前工作得很好。

## 结果:为什么你应该关心

您可以看到，在一个有大量数据和 UI 变化的大型应用程序中，React 为您提供了一种很好的方式来分离组件，只需担心数据。它并不适合每一个单独的项目，但是围绕你的项目建立一些结构并使它更容易维护是很棒的。

如果你是一个开发 web 应用程序的开发人员，你应该注意反应。它可以让你和其他维护你的代码的人在将来省去很多麻烦。*