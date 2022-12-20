# Mithril.js 中的锚链接

> 原文：<https://dev.to/weirdmayo/anchor-links-in-mithriljs-277l>

最近，在为我的朋友制作一个小网站的过程中，我决定使用 Mithril.js MVC 框架，因为这是我最喜欢的，并且我已经烂熟于心。给我时间，我现在可以用这个框架做任何事情。但是，最近，我遇到了它的路由技术实现的问题。我不能链接到锚点，也不能让我的页面传送/滚动到他们那里，但是我解决了这个问题。

### 问题是如何出现/起作用的。

当您使用链接路由到应用程序的不同页面时，您将组件硬连接到每个路由:

```
m.route( rootElement, "/home", {
    "/home": HomeComponent,
    "/settings": SettingsComponent
} 
```

你可以指定你自己的路由策略，但是默认情况下最终结果是这样的:`https://website.com/#!/home`。如果你的网站会有一个以上的某种类型的 Mithril 应用程序/用例，hashbang 在开始时会隔离到一个单独的 Mithril 应用程序。

Mithril 知道您不想麻烦地写出应用程序中的每个`a`链接到`/#!/whatever`链接，所以他们允许您执行`<a href='/home'>`，如果您通过要调用的`m.route.link`函数传递`oncreate`方法，Mithril 将负责安全地将用户路由到您指定的路径。

柠檬的代码应该是这样的:

```
m("a[href='/home']", {
    oncreate: m.route.link
}, "Home"); 
```

### 问题本身

现在，也就是说，如果您没有将一个组件硬连线到某个路由，它将会忽略路由调用，让您停留在原来的页面上，或者将您传送到默认路由(在`m.route`调用中指定为第二个参数的那个)。

> 那你怎么设置锚呢？
> 与*路线参数*。

大概是这样的:`https://website.com/home?filter=all`

要传递路由参数，您必须调用`m.route.set()`函数，而不是通过`a[href]`链接。这种功能的一个例子是:

```
//button component
const Button = {
    view: function(vnode){
        return m("button", {
            onclick: (e)=>{ m.route.set("/home", {
                //your parameters here
                "filter": "all",
        }, "Go Home")
    }
}
//app component
const App = {
    view: function(vnode){
        return m("div.app", [
            m(Button)
        ]);
    }
} 
```

因此，您可能已经猜到了，您可以将锚点名称传递给路由参数。

```
/* ...button component script... */ {
    onclick: (e)=>{
        m.route.set("/home", {
            "a": "contact-me", // a for Anchor
        }
    }   
} 
```

但是仅仅传递主播名字并不会有什么作用。你必须滚动到锚点。为此，我们创建了一个名为`scrollToAnchor( anchorName )`的函数，我们可以在`onclick`事件期间调用它；

```
function scrollToAnchor( anchorName ){
    let is = (el)=>{return el !== undefined && el !== null};
    //if you pass an undefined anchor it will scroll to the top of the body
    let targetEl = is(anchor) ?
        document.querySelector("a[name="+ anchor +"]")
    : document.body;
    let scrollTop = window.pageYOffset || document.documentElement.scrollTop;
    let target = is(targetEl) ? targetEl.getBoundingClientRect().top : 0;
    window.scroll({
        top: target + scrollTop - 70,
        left: 0,
        behavior: "smooth"
    });
} 
```

现在只需给我们制作的按钮上的`onclick`调用添加一个函数

```
onclick: (e)=>{
    let anchor = "contact-me";
    m.route.set( "/home", { "a": anchor } );
    scrollToAnchor( anchor );
} 
```

就是这样。我们解决了。

如果你想保持同样的路线，但只是瞬移到锚点，可以通过`m.route.get()`作为`m.route.set`的第一个自变量。

如果你想变得*漂亮*，你可以通过自动集成的功能使按钮组件变得更好:

```
const Button = {
    oninit: function(vnode){
        this.ctrl = {
            route: function(route, anchorName){
                if(anchorName){
                    m.route.set(route, {"a": anchorName});
                    scrollToAnchor( anchorName );
                } else {
                    m.route.set( route );
                }
            }
        }
    },
    view: function(vnode){
        return m("button", {
            onclick: (e)=>{
                that.ctrl.route( vnode.attrs.route, vnode.attrs.anchor )
            }, vnode.attrs.text );
    }
}

const App = {
    view: function(vnode){
        return m("div.app", [
            m(Button, {
                route: "home",
                anchor: "contact-me",
                text: "Contact Me"
            })
        ]);
    }
} 
```

### 结论

仅仅在这个框架上制作一个锚定链接似乎要做的工作太多了。但是，坦率地说，大多数框架都有类似的路由策略。由于锚点链接主要用于博客和教程帖子，这些是静态网站，不需要 MVC 框架来构建，所以 MVC 框架路由策略几乎没有包含锚点解决方案。这种“修复”可以在任何地方实现，而不仅仅是 Mithril.js