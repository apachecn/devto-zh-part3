# 在 Twig 中模拟组件

> 原文：<https://dev.to/jakedohm_34/emulating-components-in-twig-cjo>

组件是 Vue.js 等 JavaScript 框架中我最喜欢的东西之一。它们允许您通过删除标记中的重复代码来遵循枯燥的原则，并且它们提供了出色的开发人员体验。当我在 Twig 模板中工作时，无法访问真正的组件绝对是我最怀念的事情之一。正因为如此，我一直在寻找解决这个问题的方法。

在我谈论我尝试过的解决方案和我找到的解决方案之前，让我先告诉你我需要组件或组件替换的哪些功能的标准。

## 标准

*   Props:我应该能够将命名参数(通常称为“props”)传递到我的组件中。
*   Slots:我应该能够将大块的标记(HTML、文本等)传递到我的组件中，组件应该决定如何处理这些标记(即在哪里呈现)。

如果您有使用 Vue.js 或其他基于组件的框架的经验，这些概念应该对您非常熟悉。如果没有，你必须相信我，一旦你使用了这些组件特性，试图在没有它们的情况下编写标记是很痛苦的😂

## 探究解决方案

我认为 Twig 有两个主要特性可以用来在 Twig 中实现类似组件的功能:宏和包含。

### 宏

所以我开始尝试宏。如果你不熟悉，宏是 Twig 的“功能”。这里有一个宏的例子(详见[树枝文档](https://twig.symfony.com/doc/2.x/tags/macro.html) ):

```
{% import _self as components %}

{% macro button(props) %}
    <button>{{ props.content }}</button>
{% endmacro %}

<p>{{ components.button({ content: 'Click Me' }) }}</p> 
```

使用宏满足了一个标准，传递道具，但是它没有解决我想要通过命名槽将标记传递到我的“组件”中的问题。

### 包括

接下来，我试了试 [Twig 的收录](https://twig.symfony.com/doc/2.x/tags/include.html)。Includes 是一种将你的代码分解成多个文件的方法，我经常用它来分解我的页面模板，使它们更小，更容易搜索和查找。但是，它们有一个内置的特性“with”，允许你将上下文(即变量)传递给 include，这让我希望我可以将它们作为组件很好地工作！

但同样，他们只满足第一个标准。没有(内置的)方法来传递要在组件标记中呈现的标记块。

补充说明:从技术上来说，对于宏或包含，我可以做一些不太好的事情，比如将一个变量赋给一堆标记，然后传入这个变量，但这感觉很不对，也不直观。

## 最终解决

在放弃希望后，我最终偶然发现了 Twig 的嵌入。我读了文档，立刻兴奋起来，因为 Embeds 看起来满足了开箱即用的两个标准！我立即在 twig fiddle(twigfiddle.com)上编写了一个例子，我发现嵌入完全符合我的两个标准:使用“with”功能和“blocks ”,我可以将 props **和**块标记传递到嵌入中。

下面是一个嵌入声明的例子，以及它的用法:

```
{% embed "page.twig" with {
    social: ['facebook', 'twitter', 'snapchat']
} %}
    {% block header %}
        <div>Header Content</div>
    {% endblock %}

    {% block main %}
        <div>Main Content</div>
    {% endblock %}
{% endembed %} 
```

```
{# File: page.twig #}

<div class="page-component">
    <header>{% block header %}Default Header Content{% endblock %}</header>

    <main>{% block main %}Default Main Content{% endblock %}</main>

    <footer>{% block footer %}Default Footer Content{% endblock %}</footer>

    {% if social is defined %}
        <ul class="social">
            {% for item in social %}<li>{{ item }}</li>{% endfor %}
        </ul>
    {% endif %}
</div> 
```

这种在 Twig 中模拟组件的方法并非没有缺点，但我认为它对大多数场景都很有效。下面是一些关于嵌入优缺点的说明:

### 优点

*   可以传入 props(变量)和 slots(块)。
*   您可以为每个块指定默认内容。
*   使用“with only”特性，您可以封装您的组件，这样它就不会从使用它的上下文中隐式接收任何变量。

### 弊

*   您必须在每次使用时明确引用嵌入的文件路径
*   你需要**让**使用“only”关键字来确保你的父模板的变量不会泄漏到组件中。

## 最后的想法

我不认为嵌入是一个很好的长期解决方案，我也很想在 Twig 中拥有组件功能，但是我很高兴能找到满足我需求的东西，并允许我以自己喜欢的方式清理模板。

我还对 [Twig Components Bundle](https://packagist.org/packages/olveneer/twig-components-bundle) 很感兴趣，这是一个 Symfony 的包，似乎是基于 Vue.js 的组件系统为 Twig 添加了组件功能。但是，我还没有尝试过。如果有，我一定会回来报告的！

* * *

我在一家很棒的公司工作，叫做好工作。我们是一个网络开发专家团队，帮助机构、品牌和创业公司的设计团队为网络和移动设备开发产品。

如果你想找人一起做你的网络项目，不要犹豫[联系](https://simplygoodwork.com/contact)！