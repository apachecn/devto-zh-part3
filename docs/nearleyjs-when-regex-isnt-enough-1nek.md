# Nearley.js:当正则表达式不够用时

> 原文：<https://dev.to/ijlee2/nearleyjs-when-regex-isnt-enough-1nek>

*原贴于[crunching numbers . live](https://crunchingnumbers.live/2019/01/24/nearley-js-when-regex-isnt-enough/)T3】*

在过去的两周里，我让自己负责[帮助用户创建和可视化 LDAP 过滤器](https://ldap-builder.herokuapp.com/)。LDAP 过滤器允许用户以强大的方式进行搜索，并且可以采用以下任何形式:

<figure>

[![LDAP filters can take various forms.](img/a5ec7862e2ae8044d2745db4fc67c25d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y8Eg26GV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/01.png%3Fw%3D600%26h%3D452)

<figcaption>LDAP filters can take various forms.</figcaption>

</figure>

## 问题描述

我们可以用 **regex** (正则表达式)解析上面的简单例子。不幸的是，LDAP 过滤器也可以是由 and ( `&`)、or ( `|`)或 not ( `!`)运算符连接的 LDAP 过滤器的组合。

<figure>

[![A combination of LDAP filters also forms an LDAP filter.](img/48b263aa82d914856ee672abc9157bb9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--50rivHH0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/02.png%3Fw%3D600%26h%3D452)

<figcaption>A combination of LDAP filters also forms an LDAP filter.</figcaption>

</figure>

呈现递归的 LDAP 过滤器是**上下文无关语法**的一个例子。源于**正则语法**(上下文无关的子类)的 Regex 不能独立处理递归。作为开发人员，我们需要实现它，并冒着产生错误的风险。

<figure>

[![Ask your doctor if regex is right for you.](img/382c3d9b0dc8d7153de3d549cd0298fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BHU1RcWg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/03.png%3Fw%3D600%26h%3D452)

<figcaption>Ask your doctor if regex is right for you.</figcaption>

</figure>

有了 [Nearley](https://nearley.js.org/) ，我们可以很容易地定义一个上下文无关的语法。事实上，它总共帮助我们解决了 5 个问题。

### a .认可

第一个问题是识别，这正是 regex 和 Nearley 等工具的目的。给定一个字符串，判断它是否是 LDAP 过滤器。

<figure>

[![Problem 1\. Recognize LDAP filters.](img/207511d7c5a2887564186558026b010b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rk2CBjte--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/04.png%3Fw%3D600%26h%3D452)

<figcaption>Problem 1\. Recognize LDAP filters.</figcaption>

</figure>

### b .分类

第二个问题，分类，意味着我们知道*为什么*一个字符串是 LDAP 过滤器。

<figure>

[![Problem 2\. Show why an LDAP filter is valid.](img/63d724ccf6dbfe68844c81efffd03c87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YRZ6wL7P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/05.png%3Fw%3D600%26h%3D452)

<figcaption>Problem 2\. Show why an LDAP filter is valid.</figcaption>

</figure>

### c .错误处理

相反，我们想告诉用户为什么一个字符串是 LDAP 过滤器。

<figure>

[![Problem 3\. Explain why an LDAP filter is not valid.](img/87a83ed1e40ce478499439b51a2041e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RCZi7opW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/06.png%3Fw%3D600%26h%3D452)

<figcaption>Problem 3\. Explain why an LDAP filter is not valid.</figcaption>

</figure>

### d .转换

而不是简单的`true`或者`false`，输出应该有丰富的，容易消化的结构和内容。然后，我们可以使用输出来帮助用户可视化他们的输入。

<figure>

[![Problem 4\. Make an output useful for our UI.](img/ee671f30371fd90a0d538ad84e8d56df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kD5XGZW1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/07.png%3Fw%3D600%26h%3D452)

<figcaption>Problem 4\. Make an output useful for our UI.</figcaption>

</figure>

### e .维修性

也许最重要的是，我们必须能够容易地理解语法。不仅仅是今天我们写它的时候，而是任何时候我们回到它的时候。

<figure>

[![Problem 5\. We can easily understand the grammar.](img/a4e378fdbd803cd4b52b39e73cf7df0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MF-IECOc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/09.png%3Fw%3D600%26h%3D452)

<figcaption>Problem 5\. We can easily understand the grammar.</figcaption>

</figure>

注意代码是如何自我记录的。内尔利甚至可以画铁路图来帮助我们形象化语法:

<figure>

[![Railroad diagrams help us visualize our grammar.](img/b4eac695aee368a7b9aa7ff5dfd93fdf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SvqMscvX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/railroad-diagram.png%3Fw%3D600%26h%3D900)

<figcaption>Railroad diagrams help us visualize our grammar.</figcaption>

</figure>

相比之下，这里是正则表达式:

<figure>

[![A regex today becomes a mystery tomorrow.](img/199e31fff3ef87d85fbc04077142900d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BcZoFfA9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/08.png%3Fw%3D600%26h%3D452)

<figcaption>A regex today becomes a mystery tomorrow.</figcaption>

</figure>

## 2。例子

我们将把 LDAP 过滤器看作 Nearley 的一个具体的、真实的应用程序。完整的定义可以在 [RFC 2254](https://tools.ietf.org/html/rfc2254) 和 [RFC 4515](https://tools.ietf.org/html/rfc4515) 中找到。

为了简单起见，我们将 LDAP 过滤器定义为简单的或 T2 的表达式。让我们以自上而下的方式进行定义:

<figure>

[![1\. An LDAP filter is an expression surrounded by parentheses.](img/bd3a8bf32c7d243f8f475b5d1a809e02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1ffxf3_D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/10.png%3Fw%3D600%26h%3D452)

<figcaption>1\. An LDAP filter is an expression surrounded by parentheses.</figcaption>

</figure>

<figure>

[![2\. An expression can be simple or present.](img/eef821b5cce60cd99ef6781483d2e9ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7NMBNIPu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/11.png%3Fw%3D600%26h%3D452)

<figcaption>2\. An expression can be simple or present.</figcaption>

</figure>

<figure>

[![3\. A simple expression has an attribute, operator, and value.](img/dcdca8756ab580fdc4d16edd4d28ce47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hIxR_QPF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/12.png%3Fw%3D600%26h%3D452)

<figcaption>3\. A simple expression has an attribute, operator, and value.</figcaption>

</figure>

<figure>

[![4\. For simplicity, we allow letters, numbers, and underscores for attribute and value. The operator takes one of four forms: equal, similar, greater, or less.](img/e1d644d747e90eb51d17c534dcdb98e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fZW_5hz9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/13.png%3Fw%3D600%26h%3D452)

<figcaption>4\. For simplicity, we allow letters, numbers, and underscores for attribute and value. The operator takes one of four forms: equal, similar, greater, or less.</figcaption>

</figure>

<figure>

[![5\. A present expression has an attribute, followed by =*.](img/360b4a09d4b7f2320da7e4f48da3aa5a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9CkvIAw8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/14.png%3Fw%3D600%26h%3D452)

<figcaption>5\. A present expression has an attribute, followed by =*.</figcaption>

</figure>

在语法语境中，白色的成分被称为**终结符**，而黄色的成分被称为**非终结符**。终端就像原子；它们是不能再分的最小单位(这个类比中不存在亚原子粒子)。非末端就像分子和化合物；它们通过**规则**建立在终端上。

## 3。密码

我们将使用 [Ember](https://emberjs.com/) (3.7.0)来存放语法。通过这种方式，我们可以编写回归测试并自信地对语法进行修改。

到最后，我们将已经创建并修改了 6 个文件:

```
Folder structure

.
|-- app
|   └-- utils
|       └-- ldap-builder.js
|
|-- tests
|   └-- unit
|       └-- utils
|           └-- ldap-builder-test.js
|
|-- vendor
|   └-- ldap-filter-grammar.ne
|
|-- compile-ldap-filter-grammar.sh
|
|-- ember-cli-build.js
|
└-- package.json 
```

### 余烬

首先全局安装 Nearley 和 [Browserify](http://browserify.org/) ，这样我们就可以编译语法文件， **ldap-filter-grammar.ne** 。

```
Terminal: /

npm install -g nearley
npm install -g browserify 
```

然后，我们编写一个脚本来帮助编译:

```
File: /compile-ldap-filter-grammar.sh

nearleyc ./vendor/ldap-filter-grammar.ne -o ./vendor/ldap-filter-grammar-temp.js
browserify ./vendor/ldap-filter-grammar-temp.js -o ./vendor/ldap-filter-grammar.js -s ldap-filter-grammar
rm ./vendor/ldap-filter-grammar-temp.js 
```

编译后的文件 **ldap-filter-grammar.js** 必须位于**供应商**文件夹中。[为了告诉 Ember 这个文件存在](https://guides.emberjs.com/release/addons-and-dependencies/managing-dependencies/#toc_compiling-assets)，我们需要更新 **ember-cli-build.js** 。

```
File: /ember-cli-build.js

'use strict';

const EmberApp = require('ember-cli/lib/broccoli/ember-app');

module.exports = function(defaults) {
    let app = new EmberApp(defaults, {
        // Add options here
    });

    app.import('vendor/ldap-filter-grammar.js', {
        using: [
            { transformation: 'amd', as: 'ldap-filter-grammar' },
        ],
    });

    return app.toTree();
}; 
```

最后，我们创建一个实用程序文件，使用我们的语法来解析传入的字符串。为此，我们还需要在本地安装 Nearley。

```
Terminal: /

npm install nearley --save-dev
ember install ember-auto-import
ember g util ldap-builder 
```

```
File: /app/utils/ldap-builder.js

import ldapFilterGrammar from 'ldap-filter-grammar';
import nearley from 'nearley';

export default {
    createLdapObject(ldapFilter) {
        try {
            const parser = new nearley.Parser(nearley.Grammar.fromCompiled(ldapFilterGrammar));

            parser.feed(ldapFilter);
            const results = parser.results;

            // If there is a match, return the first result
            if (results.length > 0) {
                return results[0];
            }

        } catch (error) {
            // If there is no match, return false
            return false;

        }

        return false;
    },
}; 
```

然后我们可以根据我们的语法运行测试:

```
Terminal: /

ember t --server --filter='Unit | Utility | ldap-builder' 
```

### b .内尔利

在内尔利写语法很容易。我们复制粘贴自顶向下方法中显示的代码。Nearley 使用基于 BNF 的语法( **Backus-Naur 形式**)。如果您的应用程序的语法也使用 BNF(例如 LDAP 过滤器)，那么您很幸运！

```
File: /vendor/ldap-filter-grammar.ne

# Define an LDAP filter
filter ->
    "(" expression ")" {%
        (data) => data[1]
    %}

expression ->
    simple {% id %} |
    present {% id %}

# Define a simple expression
simple ->
    attribute operator value {%
        (data) => {
            const attribute = data[0];
            const operator = data[1];
            const value = data[2];

            // Handle success
            return {
                expression: `(${attribute}${operator}${value})`,
                metadata: {
                    expressionType: 'simple',
                    attribute,
                    operator,
                    value,
                },
            };
        }
    %}

attribute ->
    [\w]:+ {%
        (data) => data[0].join('')
    %}

operator ->
    "=" {% id %} |
    "~=" {% id %} |
    "<=" {% id %} |
    ">=" {% id %}

value ->
    [\w]:+ {%
        (data) => data[0].join('')
    %}

# Define a presence expression
present ->
    attribute "=*" {%
        (data) => {
            const attribute = data[0];
            const operator = '=';
            const value = '*';

            // Handle success
            return {
                expression: `(${attribute}${operator}${value})`,
                metadata: {
                    expressionType: 'present',
                    attribute,
                    operator,
                    value,
                },
            };
        }
    %} 
```

我认为大部分代码都是不言自明的。遵循规则的`{%`和`%}`标记一个**后处理器**。后处理器转换输入数据(终端或非终端)的输出，以定义输出非终端的输出。最精彩的部分？我们用 JavaScript 编写后处理器，所以我们可以使用像`map`、`join`和`reduce`这样的方法来转换数组。

说到数组，Nearley 的默认匹配输出是一个数组。例如，如果因为输入有`'~='`而匹配了`op`的规则，Nearley 将返回数组`['~=']`。虽然数组允许 Nearley 处理语法中的**歧义**并呈现所有匹配，但我们可能对数组的内容更感兴趣，这样我们就可以将它传递给下一个规则。为此，我们可以使用内置的后处理器`id`(我相信代表身份)。

如果我们想要组合一个数组的内容，我们可以使用数组索引来访问每个内容。由于后处理器只是 JavaScript，我们甚至可以使用析构:

```
simple ->
    attribute operator value {%
        (data) => {
            const [ attribute, operator, value ] = data;

            [...]
        }
    %}

present ->
    attribute operator value {%
        ([ attribute, operator, value ]) => {
            [...]
        }
    %} 
```

## 4。最佳实践

<figure>

[![Best practice 1\. Follow the grammar rules strictly.](img/514ede2a4d67e4cc1830100c675da45a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B5Sq0ig7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/15.png%3Fw%3D600%26h%3D452)

<figcaption>Best practice 1\. Follow the grammar rules strictly.</figcaption>

</figure>

相信我，在 LDAP 过滤器上工作。规范对空格的要求很严格，但是我很宽松地允许使用空格，这样用户可以输入类似于`(id >= 123)`的表达式，中间可以有空格。虽然这增加了可用性，但我错误地将表达式如`cn=Isaac Lee`标记为无效。解决这个问题花费了两天时间。从右边开始。

<figure>

[![Best practice 2\. Write tests to check your grammar.](img/dc217b3cc3eed748aa2c6329b7c9cf79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HJxaTtP2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/16.png%3Fw%3D600%26h%3D452)

<figcaption>Best practice 2\. Write tests to check your grammar.</figcaption>

</figure>

Nearley 创建一个数组作为默认输出，但允许您使用后处理器定制输出。如果这是你第一次写上下文无关的语法，你可能不确定你的输出。编写测试来验证它。你也可以使用现有的测试进行回归测试，这样你就可以放心地编写额外的语法。

<figure>

[![Best practice 3\. Relax the rules to improve user experience.](img/f7436d922494671e9018aa05703019e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SIO76v7K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/17.png%3Fw%3D600%26h%3D452)

<figcaption>Best practice 3\. Relax the rules to improve user experience.</figcaption>

</figure>

现在规定到位了，可以考虑放宽了。这可能会引入歧义(降低性能)，但会增加用户体验。例如，通过使`attribute`、`operator`和`value`可选，我可以检测到缺失的元素并提醒用户。

```
simple ->
    attribute:? operator:? value:? {%
        (data) => {
            [...]
        }
    %} 
```

<figure>

[![Best practice 4\. Write more tests to check the relaxed rules.](img/b40fe974238f3df475bca621cb9ee4ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zZkzLwS1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/18.png%3Fw%3D600%26h%3D452)

<figcaption>Best practice 4\. Write more tests to check the relaxed rules.</figcaption>

</figure>

首先，检查现有的测试(对于正确的规则)是否仍然通过。之后，为宽松的规则编写额外的测试。

我的公开回购包括 5 个测试。万一这个小数字阻碍了你为你的应用程序编写测试，我想提一下[完整版](https://ldap-builder.herokuapp.com/)有超过 220 个测试。这就是为什么我对我的 LDAP 解决方案充满信心。

<figure>

[![Best practice 5\. Refactor your code to increase maintainability.](img/b9573861b9a1152d3334e1aaf46ee466.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---pn3XKbG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://crunchingnumbersdotlive.files.wordpress.com/2019/01/19.png%3Fw%3D600%26h%3D452)

<figcaption>Best practice 5\. Refactor your code to increase maintainability.</figcaption>

</figure>

最后，花时间重构你的代码。这可能意味着将变量重命名为更具描述性的变量，创建助手函数以删除重复的代码，以及更新规则以减少或消除歧义。这样做，你的语法将变得更容易维护。

### 备注

有关 Nearley 的更多信息，我鼓励您访问以下网站:

*   [正式文件](https://nearley.js.org/docs/grammar)
*   [使用 Earley 算法解析 JavaScript 中的任何东西——Gajus Kuizinas](https://medium.com/@gajus/parsing-absolutely-anything-in-javascript-using-earley-algorithm-886edcc31e5e)
*   [内尔利解析器游乐场](https://omrelli.ug/nearley-playground/)

要了解正则和上下文无关语法背后的理论，你可以看看詹姆斯·海恩的《离散结构、逻辑和可计算性》

最后，你可以在 LDAP.com 的 LDAP 过滤器上阅读(比上面链接的 RFC 可读性更好)。

您可以在这里找到完整的代码:

[从 GitHub 下载](https://github.com/ijlee2/nearley-tutorial)