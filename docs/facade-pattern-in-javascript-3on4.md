# JavaScript 中的外观模式

> 原文：<https://dev.to/tomekbuszewski/facade-pattern-in-javascript-3on4>

在构建应用程序时，我们经常会面临外部 API 的问题。一种方法简单，另一种方法非常复杂。将它们统一在一个公共接口下是 facade 模式的用途之一。

假设我们正在构建一个显示电影、电视节目、音乐和书籍信息的应用程序。对于每一种产品，我们都有不同的供应商。它们使用不同的方法实现，具有不同的需求等。我们必须记住或记下如何查询每种类型。

还是我们？

门面模式解决了这样的问题。这是一个通用的接口，不管它下面使用了什么，都有相同的方法。

我准备了一个资源服务的四种不同实现:

```
class FetchMusic {
  get resources() {
    return [
      { id: 1, title: "The Fragile" },
      { id: 2, title: "Alladin Sane" },
      { id: 3, title: "OK Computer" }
    ];
  }

  fetch(id) {
    return this.resources.find(item => item.id === id);
  }
}

class GetMovie {
  constructor(id) {
    return this.resources.find(item => item.id === id);
  }

  get resources() {
    return [
      { id: 1, title: "Apocalypse Now" },
      { id: 2, title: "Die Hard" },
      { id: 3, title: "Big Lebowski" }
    ];
  }
}

const getTvShow = function(id) {
  const resources = [
    { id: 1, title: "Twin Peaks" },
    { id: 2, title: "Luther" },
    { id: 3, title: "The Simpsons" }
  ];

  return resources.find(item => item.id === 1);
};

const booksResource = [
  { id: 1, title: "Ulysses" },
  { id: 2, title: "Ham on Rye" },
  { id: 3, title: "Quicksilver" }
]; 
```

它们使用不同的模式命名，它们实现得更好，更差，需要更多或更少的工作。因为我不想过于复杂，所以我使用了具有通用响应格式的简单示例。但尽管如此，这很好地说明了问题。

## 设计我们的门面

要创建一个门面，首先我们需要了解每个供应商的各个方面。如果需要额外授权、更多参数等。，这是必须实现的。这是额外的，当与不需要它的供应商一起使用时可以丢弃。

**立面的积木是公共接口**。无论要查询哪个资源，都应该只使用一种方法。当然，在它的下面，可能躺着更多，但公共访问应该是有限的，易于使用。

首先，我们应该决定公共 API 的形式。对于这个例子，一个 getter 就足够了。这里唯一的区别是媒体类型——书籍、电影等。所以类型将是我们的基础。

接下来，资源之间的共同之处。每个人都可以通过 ID 查询。所以，我们的 getter 应该接受一个参数，一个 ID。

# 建筑我们的门面

(我已经决定为此使用一个类，但这不是必需的。由对象文字甚至函数集合组成的模块可能就足够了。然而，我喜欢这个符号。)

```
class CultureFasade {
  constructor(type) {
    this.type = type;
  }
} 
```

首先，我们在构造函数中定义类型。这意味着，每个 facade 实例将返回不同的。我知道这可能看起来是多余的，但是使用函数的单个实例并且每次传递更多的参数更方便。

好了，接下来的事情是定义我们的公共和私有方法。为了说明“私有”的，我使用了著名的`_`而不是`#`，因为 CodePen 还不支持它。

如前所述，唯一的公共方法应该是我们的 getter。

```
class CultureFacade {
  constructor(type) {
    this.type = type;
  }

  get(id) {
    return id;
  }
} 
```

基本实现(框架)就在那里。现在，让我们来看看我们类中真正的*肉*——private getters。

首先，我们需要确定如何查询每个资源:

*   Music 需要一个新的实例，然后在方法`get`中传递和 ID；
*   电影的每个实例返回数据，初始化时需要 ID；
*   TV Show 只是接受一个 ID 并返回数据的单个函数；
*   书籍只是一种资源，需要我们自己去查询。

我知道这一步看起来单调乏味，没有必要，但是请注意，现在我们真的不需要解决任何问题。**概念阶段在设计和建造过程中非常重要**。

好了，音乐，开始。

```
class CultureFacade {
  ...

  _findMusic(id) {
    const db = new FetchMusic();
    return db.fetch(id);
  }
} 
```

我们已经创建了一个简单的方法，它完全符合我们之前描述的内容。剩下的三个只是形式。

```
class CultureFacade {
  ...

  _findMusic(id) {
    const db = new FetchMusic();
    return db.fetch(id);
  }

  _findMovie(id) {
    return new GetMovie(id);
  }

  _findTVShow(id) {
    return getTvShow(id);
  }

  _findBook(id) {
    return booksResource.find(item => item.id === id);
  }
} 
```

现在，我们有了查询数据库的所有方法。

## 获取公共 API

作为一名程序员，我学到的最重要的事情之一就是永远不要依赖你的供应商。你永远不知道会发生什么。他们可能会受到攻击，被关闭，你的公司可能会停止支付服务等。

知道了这一点，我们的 getter 也应该使用一种 facade。它应该*尝试*获取数据，而不是假设它会成功。

所以，让我们写这样的方法。

```
class CultureFacade {
  ...

  get _error() {
    return { status: 404, error: `No item with this id found` };
  }

  _tryToReturn(func, id) {
    const result = func.call(this, id);

    return new Promise((ok, err) => !!result
      ? ok(result)
      : err(this._error));
  }
} 
```

让我们在这里停一会儿。如您所见，这个方法也是私有的。为什么？公众并没有从中受益。它需要其他私有方法的知识。接下来，它需要两个参数—`func`和`id`。后者很明显，而前者则不然。好的，这将接受一个函数(或者说我们类的方法)来运行。如你所见，执行被分配给`result`变量。接下来，我们检查它是否成功，并返回一个`Promise`。为什么是这样的巴洛克建筑？使用`async/await`甚至简单的`then/catch`语法，承诺很容易调试和执行。

哦，还有错误。没什么大的，只是一个 getter 返回消息。这可以更详细，有更多的信息等。我没有实现任何花哨的东西，因为这并不真的需要它，而且我们的供应商也没有任何错误。

好吧，那我们现在有什么？查询供应商的私有方法。我们内心的正面试图去质疑。和我们的公众吸气剂骨架。让我们把它扩展成一个生命体。

因为我们依赖于预定义的类型，所以我们将使用非常强大的`switch`语句。

```
class CultureFacade {
  constructor(type) {
    this.type = type;
  }

  get(id) {
    switch (this.type) {
      case "music": {
        return this._tryToReturn(this._findMusic, id);
      }

      case "movie": {
        return this._tryToReturn(this._findMovie, id);
      }

      case "tv": {
        return this._tryToReturn(this._findTVShow, id);
      }

      case "book": {
        return this._tryToReturn(this._findBook, id);
      }

      default: {
        throw new Error("No type set!");
      }
    }
  }
} 
```

### 关于定义字符串类型的说明

我们的字体是手写的。这不是最佳做法。它应该被定义在一边，所以没有打字错误会导致错误。为什么不，让我们做它。

```
const TYPE_MUSIC = "music";
const TYPE_MOVIE = "movie";
const TYPE_TV = "tv";
const TYPE_BOOK = "book";

class CultureFacade {
  constructor(type) {
    this.type = type;
  }

  get(id) {
    switch (this.type) {
      case TYPE_MUSIC: {
        return this._tryToReturn(this._findMusic, id);
      }

      case TYPE_MOVIE: {
        return this._tryToReturn(this._findMovie, id);
      }

      case TYPE_TV: {
        return this._tryToReturn(this._findTVShow, id);
      }

      case TYPE_BOOK: {
        return this._tryToReturn(this._findBook, id);
      }

      default: {
        throw new Error("No type set!");
      }
    }
  }
} 
```

这些类型应该导出，然后在应用程序范围内使用。

## 用法

所以，看来我们到此为止了。让我们去兜一圈吧！

```
const music = new CultureFacade(TYPE_MUSIC);
music.get(3)
    .then(data => console.log(data))
    .catch(e => console.error(e)); 
```

使用`then/catch`非常简单的实现。它只是记录下我们正在寻找的专辑，在这个例子中是电台司令的 *OK 电脑*。顺便说一句，听得很好。

好的，但是让我们也试着得到一个错误。当我们的供应商没有所需的资源时，他们没有一家能够真正提供任何信息。但是我们可以！

```
const movies = new CultureFacade(TYPE_MOVIE);
movie.get(5)
    .then(data => console.log(data))
    .catch(e => console.log(e)); 
```

我们这里有什么？哦，控制台抛出一个错误，说“没有找到这个 id 的项目”。实际上，它是一个符合 JSON 的对象！耶！

—

如您所见，如果使用得当，facade 模式会非常强大。当你有多个相似的来源、相似的操作等时，这是非常有益的。，并想统一用法。

—

所有代码都可以在 [CodePen](https://codepen.io/tomekbuszewski/pen/jXVpMJ?editors=0010) 上获得。