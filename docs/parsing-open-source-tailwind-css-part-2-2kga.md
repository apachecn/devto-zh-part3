# 解析开源:Tailwind CSS 第 2 部分

> 原文：<https://dev.to/mariowhowrites/parsing-open-source-tailwind-css-part-2-2kga>

欢迎回来！首先，我要感谢你对解析开源第一章的热情接待。我回到第二章，在那里我们将完成对顺风 CSS 的分析。这一章深入到了 Tailwind CSS 中比较复杂的部分，所以我会比上一章讲得慢一点，这样我们就能更好地掌握正在发生的事情。我将建立在我们在第 1 章中所建立的基础上，所以阅读那一章的第一部分或者对 PostCSS 如何工作有一些预先存在的知识将是一个好主意。

这一章专门讨论两件事:1)我的解析过程的简要概述，2)对`resolveConfig`函数的扩展介绍，这是一个看似很短的函数，但却封装了定义函数式编程的许多模式和抽象。

## 搜索复杂代码

您可能想知道如何开始解析您使用的开源库中的代码。诚然，我的策略非常简单，但是到目前为止，它们对我来说已经被证明是有效的，我就是用它们来写这一章的。我在这里简单地介绍了它们，以便你下次在努力理解一些代码时可以使用它们。

### 使用测试，卢克

对我写这一章帮助最大的是 Tailwind 写得很好的测试。在帮助理解一个库方面，好的测试有时比文档更好，因为它们提供了作者打算使用的代码库的例子。

因为 Tailwind 最复杂的函数都有独特的测试，解析每个单独的函数可以归结为反复运行一个测试。以下是我经过实战检验的工作流程:

1.  隔离我想用 Jest 的 CLI 运行的测试。如果我在测试`resolveConfig`函数，我在命令行上从项目根目录运行`jest __tests__/resolveConfig.test.js`。
2.  隔离一个封装了我正在检查的功能的特定测试。通常，我会选择测试套件中我能找到的最复杂的测试，并将行`test('complex test here')`改为`test.only('complex test here')`。添加`only`方法告诉 Jest 只运行那个特定的测试。
3.  到处扔`console.log`语句。

你认为我在开玩笑，但我不是。虽然我不愿意承认，但 Node 的调试器对我来说太笨重了。抛开设置和使用 Jest 所需的时间，您必须向代码中添加一个`debugger`语句，然后在代码到达正确位置时运行`repl`命令。所有这些，只是为了给你从`console.log`得到的同样的输出？不，谢谢你。有人请让我知道，如果我错过了什么，但在此之前`console.log`是裴。

如果我感觉特别有野心，我会把输出写到我用 Node 的文件系统模块创建的日志文件中。但是大多数时候，它会带我去我想去的地方。

### 从外面进来工作

好了，我们已经准备好出发了。现在怎么办？

这可能听起来很明显，但我是从外向内工作的。首先，我将在一个函数开始执行的时候记录它的参数——开始时一切看起来像什么？

从那里，找到发生的第一个转变并`console.log`它。然后，将结果与第一次测井的结果进行比较。从那时到现在发生了什么变化？理解第一步后，进入第二步。如果对你有帮助，写下你的结果，然后在下一阶段重复同样的过程。花点时间考虑一下为什么会有这样的差异。考虑代码级发生的事情以及作者以这种方式转换数据的意图。

最终，有了足够的时间和耐心，曾经令人望而生畏的事情将变得容易处理，甚至显而易见。

一步一步来是关键。我经常注释掉并重写代码，以在需要的地方获得一个`console.log`——添加括号将单行函数变成多行函数，将链式函数分解成单个函数，等等。不要害怕改变代码，看看会发生什么；撤销快捷方式的出现是有原因的。最坏的情况下，删除整个回购，从 GitHub 重新克隆。

简而言之，这些就是我的代码解析策略。有了这些，顺风前进！

## 解析配置

我们在第 1 章中没有触及的第一个函数是`resolveConfig`函数。为了从上一章快速刷新，`resolveConfig`函数负责将用户提供的配置与默认配置合并，以创建最终的配置对象。它在 Tailwind 进程开始时被调用，在任何 PostCSS 插件被应用之前，它负责创建设置，当创建最终 CSS 输出时，Tailwind 的其余部分将遵守这些设置。

下面是该函数的代码:

```
 export default function resolveConfig(configs) {
      return defaults(
        {
          theme: resolveFunctionKeys(mergeExtensions(defaults({}, ...map(configs, 'theme')))),
          variants: defaults({}, ...map(configs, 'variants')),
        },
        ...configs
      )
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们首先要注意，这里调用的`defaults`函数是从 Lodash 导入的。这个函数如何操作在配置解析过程中至关重要，所以让我们简单回顾一下。基本上，`defaults`所做的是获取传递给它的第一个对象(也称为**目标**，并用参数列表中的所有其他对象填充它，这些对象称为**源**。该函数从左到右遍历源代码，重要的是，如果目标中已经存在某个属性，它不会重新定义该属性。

我们来看一个简单的例子:

```
 const firstSource = {
        author: "Ursula Le Guin",
    }

    const secondSource = {
        author: "Dan Brown",
        actor: "Daniel Day-Lewis"
    }

    const finalTarget = defaults({}, firstSource, secondSource)

    // console.logging finalTarget would result in the following:
    {
        author: "Ursula Le Guin",
        actor: "Daniel Day-Lewis"
    } 
```

Enter fullscreen mode Exit fullscreen mode

调用`defaults`时会发生两个步骤:

1.  空的目标对象被第一个源对象填充。因为目标里还没有`author`，所以作者设定为乌苏拉·勒奎恩。
2.  目标由第二个源对象填充。因为目标中还没有`actor`，所以目标的演员被设置为丹尼尔·戴·刘易斯。但是，因为在第一步的目标中已经设置了一个`author`,所以该目标不会接受第二个源的`author`属性。丹·布朗理所当然地被扔进了作者身份的垃圾箱。

这很重要的原因一会儿就清楚了。现在，回头看看`resolveConfig`函数，我们可以看到`defaults`函数用于确保最终配置的`theme`和`variants`键首先被解析。从那里，所有其他配置值都从传递给`resolveConfig`的配置中传递进来。

记住`resolveConfig`接受一个数组作为它唯一的参数也很重要，其中用户配置在默认配置之前。这很重要，因为根据我们对`defaults`函数的了解，我们现在知道用户配置中定义的任何属性都不会被默认配置中的属性覆盖。从上面的例子中，用户配置和默认配置可以理解为`firstSource`和`secondSource`更复杂的版本。因为我们的用户配置是我们的第一个来源，所以我们的第二个来源——默认配置——不会优先，而是遵从用户的偏好。

现在，让我们再来看看`resolveConfig`函数:

```
 export default function resolveConfig(configs) {
      return defaults(
        {
          theme: resolveFunctionKeys(mergeExtensions(defaults({}, ...map(configs, 'theme')))),
          variants: defaults({}, ...map(configs, 'variants')),
        },
        ...configs
      )
    } 
```

Enter fullscreen mode Exit fullscreen mode

这里我们要关注的是我们的目标:第一个有主题和变量键的参数。让我们使用更多的缩进来使内容更容易阅读:

```
 {
      theme: 
            resolveFunctionKeys(
                mergeExtensions(
                    defaults(
                        {}, 
                        ...map(configs, 'theme')
                    )
                )
            ),
      variants: 
            defaults(
                {}, 
                ...map(configs, 'variants')
            ),
    }, 
```

Enter fullscreen mode Exit fullscreen mode

让我们先看看在`theme`属性中发生了什么，因为它稍微复杂一些。知道了 JavaScript 引擎会从内到外执行这个函数，我们首先要看的是`defaults`调用。

这段代码看起来像这样:

```
 defaults({}, ...map(configs, 'theme')) 
```

Enter fullscreen mode Exit fullscreen mode

我们看到一个空的目标对象被每个配置中的`theme`键填充。和以前一样，首先填充用户配置，然后从缺省配置中填充用户未定义的任何键。

使用我在本章开始概述的策略，我在 resolveConfig 测试套件中选择了一个测试，在解析过程中重复运行。那个测试看起来是这样的:

```
 test.only('theme values in the extend section are lazily evaluated', () => {
      const userConfig = {
        theme: {
          colors: {
            red: 'red',
            green: 'green',
            blue: 'blue',
          },
          extend: {
            colors: {
              orange: 'orange',
            },
            borderColor: theme => ({
              foo: theme('colors.orange'),
              bar: theme('colors.red'),
            }),
          },
        },
      }

      const defaultConfig = {
        prefix: '-',
        important: false,
        separator: ':',
        theme: {
          colors: {
            cyan: 'cyan',
            magenta: 'magenta',
            yellow: 'yellow',
          },
          borderColor: theme => ({
            default: theme('colors.yellow', 'currentColor'),
            ...theme('colors'),
          }),
        },
        variants: {
          borderColor: ['responsive', 'hover', 'focus'],
        },
      }

      const result = resolveConfig([userConfig, defaultConfig])

    /* expected result not immediately relevant and thus left out for brevity */ 
```

Enter fullscreen mode Exit fullscreen mode

当运行上面的测试并检查第一个`defaults`函数调用的结果时，结果看起来像这样:

```
 { 
        colors: { 
            red: 'red', 
            green: 'green', 
            blue: 'blue' 
        },
        extend: { 
            colors: { 
                orange: 'orange' 
            },
        borderColor: [Function: borderColor] 
        },
        borderColor: [Function: borderColor] 
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们看到用户配置中定义的任何值都会覆盖默认配置中的任何值。也就是说，默认定义的`colors`已经被丢弃，并被用户配置的`colors`所取代。我们还看到`extends`键有一个额外的颜色，橙色，和一个定义边框颜色的额外功能。

知道了这个结果会立即传递给`mergeExtensions`，接下来让我们看看这个函数:

```
 function mergeExtensions({ extend, ...theme }) {
      return mergeWith(theme, extend, (themeValue, extensions) => {
        if (!isFunction(themeValue) && !isFunction(extensions)) {
          return {
            ...themeValue,
            ...extensions,
          }
        }

        return resolveThemePath => {
          return {
            ...value(themeValue, resolveThemePath),
            ...value(extensions, resolveThemePath),
          }
        }
      })
    } 
```

Enter fullscreen mode Exit fullscreen mode

这个函数比乍看起来更复杂，所以让我们一行一行地来看。

首先，我们来看函数参数。我们看到一个对象被接受为唯一的参数，并且这个对象被分解为两个关键部分。从传入的对象中直接取出`extends`键，使用 rest 操作符`...`将对象上的所有其他键组合成一个名为`theme`的对象。因此，以我们上面的结果为例，顶级的`color`和`borderColors`键将被合并成`theme`，而`extends`键将被原样使用。

从那里，另一个 Lodash 函数被调用:`mergeWith`。就我个人而言，我不喜欢这个名字。我可能会把这个方法叫做`customMerge`，因为它所做的是使用一个自定义的合并函数将两个对象合并在一起。换句话说，作为第三个参数传递给`mergeWith`的函数在作为第一个参数传递的对象中的每个键上都被调用。

在我们的测试对象的上下文中，这意味着`mergeWith`将被调用两次:一次用于`colors`，一次用于`borderColors`。对于每个键，会发生以下过程:

1.  比较每个对象中的键值。
2.  如果两个值都不是函数，则组合这些值并返回结果。
3.  如果其中一个值是函数，则返回一个调用这两个函数并返回组合结果的函数。

第三步有点复杂，所以我们必须详细讨论一下。现在，让我们专注于第 2 步，因为这里有一个与我们之前看到的明显不同的机制。

不同之处在于 JavaScript 中省略号运算符`...`的使用方式。spread 操作符有两个主要用途，这两个我们都已经见过了。第一个，正如我们刚才在函数参数中看到的，用于将多个值压缩成一个值。在上面的例子中，`colors`和`borderColors`用省略号组合成一个`theme`对象。以这种方式使用省略号被称为 **rest 语法**，它从多个值中创建一个对象。

省略号还用于执行一个与 rest 语法完全相反的操作。在这种用法中，一个对象或数组被扩展成多个值。这个语法被称为**扩展语法**，我们在`mergeExtensions`中创建返回对象时会看到它的使用。

这里有一个重要的细节需要注意。简而言之，使用 spread 语法与 Lodash 的`defaults`函数正好相反:如果第二个源定义了一个也存在于第一个源中的键，那么第二个源将覆盖第一个源。

用我们之前的例子:

```
 const firstSource = {
        author: "Ursula Le Guin",
    }

    const secondSource = {
        author: "Dan Brown",
        actor: "Daniel Day-Lewis"
    }

    const finalTarget = { ...firstSource, ...secondSource }

    // console.logging finalTarget would result in the following:
    {
        author: "Dan Brown", // secondSource overrides firstSource!
        actor: "Daniel Day-Lewis"
    } 
```

Enter fullscreen mode Exit fullscreen mode

可悲的是，乌苏拉·勒奎因在这一次迭代中被推到一边，为一位远不那么娴熟的作者腾出空间。(我更喜欢勒奎因而不是布朗，如果这一点还没说清楚的话。)

这在 Tailwind 上下文中意味着，给定一个同时存在于`theme`和`extends`对象中的键，`extends`值将优先于`theme`值。

通过这种方式，`extends`键在您想要覆盖一个默认值而不完全替换给定类别的场景中非常有用。例如，如果你想覆盖默认的红色而不覆盖所有的默认颜色，以我的理解，使用`extends`键是一个很好的方法。

对 rest 和 spread 操作符的工作原理有了更好的理解，让我们再来看看第 3 步，如果主题或扩展是一个函数，就会发生这种情况:

```
 function value(valueToResolve, ...args) {
      return isFunction(valueToResolve) ? valueToResolve(...args) : valueToResolve
    }

    mergeWith(theme, extend, (themeValue, extensions) => {
        // if themeValue or extensions is a function...
        return resolveThemePath => {
          return {
            ...value(themeValue, resolveThemePath),
            ...value(extensions, resolveThemePath),
          }
        }
      })
    } 
```

Enter fullscreen mode Exit fullscreen mode

这里有一些与步骤 2 相似的地方:这两个步骤都在主题和扩展值上使用 spread 操作符来构造一个对象。然而，在这种情况下，不是创建对象并直接返回它，而是返回一个函数，该函数的唯一职责是创建对象。

该函数接受`resolveThemePath`并将其传递给`value`函数，然后由后者确定`themeValue`或`extensions`本身是否是一个函数。如果是，它用`resolveThemePath`调用那个函数。然后合并并返回两个`value`调用的结果。

我知道:很多功能。这种逻辑包含了函数式编程的强大功能和令人沮丧的地方。虽然传递函数并在必要时加载相关数据的能力无疑是 JavaScript 最强大的特性之一，但要弄清楚具体在哪个点发生了什么却非常困难。一个函数在哪里被调用，什么时候它只是被创建用于其他地方？

值得注意的是，在上面的代码中，当在`mergeExtensions`期间合并主题和扩展函数时，实际上没有调用任何函数。相反，会返回一个同时调用`themeValue`和`extensions`的函数。

让我们看看当调用我们之前的测试:
时，从`mergeExtensions`返回了什么

```
 { 
        colors: { 
            red: 'red', 
            green: 'green', 
            blue: 'blue', 
            orange: 'orange' 
        },
      borderColor: [Function] 
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到与之前结果的两个主要区别:

1.  来自主题和扩展对象的`colors`键已经被合并。
2.  上一次结果中的两个`borderColors`函数已经合并为一个。

此外，我们看到`extends`键不再存在，因为它已经被合并到主题中。

我们几乎已经完成了主题构建的逻辑。让我们检查最后一个函数，`resolveFunctionKeys` :

```
 function resolveFunctionKeys(object) {
      const resolveObjectPath = (key, defaultValue) => {
        const val = get(object, key, defaultValue)
        return isFunction(val) ? val(resolveObjectPath) : val
      }

      return Object.keys(object).reduce((resolved, key) => {
        return {
          ...resolved,
          [key]: isFunction(object[key]) ? object[key](resolveObjectPath) : object[key],
        }
      }, {})
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们看到一个函数表达式`resolveObjectPath`被定义了——一旦我们理解了使用它的上下文，让我们马上回到这个问题。让我们来看看`mergeExtensions`的结果会发生什么:

1.  `Object.keys`用来创建一个对象的键的数组。对于上面的结果，我们会得到这样一个数组:`[colors, borderColors]`。
2.  我们使用`reduce`函数遍历键数组。如果你不熟悉的话，我强烈建议你做一些关于`reduce`的研究，因为它在很多情况下非常有用。现在，只要说`reduce`循环遍历一个数组以“构建”一个结果就足够了。它本质上是更灵活的版本`map`。
3.  对于每个键，我们查看组合值。如果它是一个函数，则使用`resolveObjectPath`函数调用它。如果不是函数，就原样返回。
4.  结果被添加到我们的“构建”对象中。然后，这个“构建”的对象被传递给数组中的下一个键。

本质上，这个过程将来自`mergeExtensions`的对象转换成一个原始的 JavaScript 对象，所有的函数都由键值对代替。

考虑到这一点，我们来看看`resolveObjectPath` :

```
 function resolveFunctionKeys(object) {
      const resolveObjectPath = (key, defaultValue) => {
        const val = get(object, key, defaultValue)
        return isFunction(val) ? val(resolveObjectPath) : val
      }

      // rest of function here
    } 
```

Enter fullscreen mode Exit fullscreen mode

`resolveObjectPath`函数使用了我们以前见过的模式:使用函数表达式将状态嵌入到函数中。在这种情况下，该函数接受一个`key`和一个`defaultValue`，并使用传入顶级`resolveFunctionKeys`函数的`object`从对象中获取值，如果配置不包含所讨论的值，则使用默认值。如果返回值是一个函数，则使用新函数重复该过程，否则按原样返回该值。

在这一点上，我的头开始有点晕。“功能”这个词我已经写了很多遍了，它已经开始失去所有的意义了。所以让我们把我们正在做的事情放在一些实际的用法中:当我们把一个函数传递给我们的配置时会发生什么？

让我们回到我们一直在做的测试，删除不相关的部分:

```
 test.only('theme values in the extend section are lazily evaluated', () => {
      const userConfig = {
        theme: {
          colors: {
            red: 'red',
            green: 'green',
            blue: 'blue',
          },
          extend: {
            colors: {
              orange: 'orange',
            },
            borderColor: theme => ({
              foo: theme('colors.orange'),
              bar: theme('colors.red'),
            }),
          },
        },
      }

      const defaultConfig = {
        theme: {
          colors: {
            cyan: 'cyan',
            magenta: 'magenta',
            yellow: 'yellow',
          },
          borderColor: theme => ({
            default: theme('colors.yellow', 'currentColor'),
            ...theme('colors'),
          }),
        },
      }

      const result = resolveConfig([userConfig, defaultConfig])

      expect(result).toEqual({
        theme: {
          colors: {
            orange: 'orange',
            red: 'red',
            green: 'green',
            blue: 'blue',
          },
          borderColor: {
            default: 'currentColor',
            foo: 'orange',
            bar: 'red',
            orange: 'orange',
            red: 'red',
            green: 'green',
            blue: 'blue',
          },
        },
      })
    }) 
```

Enter fullscreen mode Exit fullscreen mode

这里额外重要的部分是两个`borderColor`函数:第一个在用户配置的`extends`键中，第二个在默认配置中。

如果我们查看结果，我们会看到两个函数的结果最终都进入了最终的`borderColor`属性。在这种情况下，`foo`和`bar`都分别解析为`orange`和`red`的自定义选项。但是，因为`default`颜色引用了一个`yellow`颜色，而这个颜色没有进入最终配置，所以使用了`currentColor`的后备默认值。

通过这个例子，我们可以更好地理解函数如何在`resolveConfig`的上下文中工作。在替换默认值和合并扩展之后，`theme`键中的任何函数都将在最终的主题值中传递。现在，让我们来探索这个过程到底是如何发生的。

我们看到函数发挥作用的第一个上下文是在`mergeExtensions`中。这是默认功能和扩展功能结合的地方。

让我们以一种更直接的方式重写这段代码，就像我们在 Tailwind 中硬编码测试用例一样:

```
 function mergeExtensions() { // we are hard-coding arguments below rather than passing them in
        function userExtendsBorderColorFunction(theme) { // from user.theme.extend.borderColor
            return {
                foo: theme('colors.orange'),
            bar: theme('colors.red'),
            }
        }

        function defaultBorderColorFunction(theme) { // from default.theme.borderColor
            return {
              default: theme('colors.yellow', 'currentColor'),
              ...theme('colors'),
            }
        }

        return function(resolveThemePath) {
          return {
                ...defaultBorderColorFunction(...resolveThemePath),
            ...userExtendsBorderColorFunction(...resolveThemePath),
          }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

有了这个更具体的例子，当遇到一个带有函数值的键时，`mergeExtensions`会做什么就更清楚了。在这种情况下，当`mergeExtensions`遇到`borderColor`键并看到其值是一个函数时，它会创建一个新函数，该函数将默认函数与用户在`extends`键中定义的函数相结合。和以前一样，用户配置中定义的任何键都会通过 spread 语法覆盖默认配置中的键。

这里需要重复的是，到目前为止，`userExtendsBorderColorFunction`和`defaultBorderColorFunction`都还没有被调用。这是一个重要的区别，因为这些函数被调用的确切时间很重要。如果我们的两个函数在`mergeExtensions`中被调用，它们可能会被使用不正确的值调用。这是因为，如果`mergeExtensions`函数仍然在执行，并且还没有完成它的工作，就不能保证 config 对象已经被所有用户定义的扩展填充了。

顺便说一下，这就是测试被标记为“扩展部分中的主题值被延迟评估”的意思。懒惰，在这里的意思是“等到其他功能完成”，而不是“在网飞疯狂观看公园和娱乐节目重播”，确保当我们的功能最终运行时，它们使用完全更新的主题值。

因此，我们知道从上面修改过的`mergeExtensions`键返回的函数被添加到`borderColor`键，并在一个统一的主题对象中与其他主题值相结合。

与上一段代码类似，让我们以一种更直接的方式重写`resolveFunctionKeys`，在可能的情况下用直接值替换任何抽象值:

```
 function resolveFunctionKeys(object) {
      const resolveObjectPath = (key, defaultValue) => {
        const val = get(object, key, defaultValue)
        return isFunction(val) ? val(resolveObjectPath) : val
      }

        return {
            borderColor: object.borderColor(resolveObjectPath)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

我已经从修改后的函数中删除了`Object.keys`和`reduce`来简化一些事情。

此时，我们可以开始联系关于 Tailwind 如何使用扩展配置对象解析函数的点。`mergeExtensions`函数最终确定所有的静态值(颜色、填充等。)并将所有函数设置为在解析完所有其他值后运行。然后，`resolveFunctionKeys`获取这些最终确定的值，创建一个函数，该函数使用 Lodash 的`get`函数从合并的对象中取出键，并返回它们供用户在任何主题函数中使用。

换句话说，`resolveFunctionKeys`中的`resolveObjectPath`函数是传递给下面主题键的实际函数:

```
 borderColor: theme => ({ // theme === resolveObjectPath from resolveFunctionKeys
      foo: theme('colors.orange') // fetches colors.orange from merged theme object,
      bar: theme('colors.red', 'defaultColor') // fetches colors.red, with a default of defaultColor
    }) 
```

Enter fullscreen mode Exit fullscreen mode

因为主题配置对象是使用函数表达式在`resolveObjectPath`中捕获的，所以最终用户可以在传入的函数中自动访问它。用户所要做的就是指定他们想要的键值，如果找不到键值，还可以选择使用哪个默认值。

现在，让我们再来看看`resolveConfig`函数:

```
 export default function resolveConfig(configs) {
      return defaults(
        {
          theme: resolveFunctionKeys(mergeExtensions(defaults({}, ...map(configs, 'theme')))),
          variants: defaults({}, ...map(configs, 'variants')),
        },
        ...configs
      )
    } 
```

Enter fullscreen mode Exit fullscreen mode

幸运的话，这个功能开始变得更有意义了。几乎所有复杂的逻辑都涉及到将用户提供的主题与默认主题相结合。不久之后，通过一个简单的 Lodash `defaults`调用就可以解决这些变量。一旦主题和变体被解析，配置中定义的所有其他键都通过另一个`defaults`调用被添加到对象中，结果被返回供 Tailwind 的其余部分使用。

### 结束解析配置

我们已经复习了很多，写了很多“函数”这个词，并且通常对 JavaScript 风格的函数式编程进行了一次旋风式的旅行。

让我们概括一下`resolveConfig`将用户配置与默认值合并的步骤:

1.  将用户主题值复制到一个对象中，所有功能保持不变。
2.  将所有默认主题值复制到用户主题值中，不覆盖任何用户设置。
3.  将用户的`theme.extend`属性中的所有值合并到主题对象中。静态值被连接起来，而同一属性的函数被合并成一个函数供以后使用。
4.  使用从上一步中获得的静态值，调用上一步中创建的所有函数，并合并结果以创建最终的主题属性。
5.  通过合并用户配置和默认配置来解析 variants 属性。
6.  通过同一用户解析所有其他键→默认优先级。

我们看到，这个过程是使用到目前为止我们在整个 Tailwind 中看到的相同技术完成的，即:函数编程、函数表达式和 Lodash。我们还更深入地研究了 rest 和 spread 语法，将它们与 Lodash 的`default`函数进行了比较，并观察了如何结合使用两者来根据默认主题设置解析用户主题。

本系列的下一章将介绍 PostCSS 插件链。和往常一样，如果你对我们到目前为止所讨论的内容有任何疑问，或者对我接下来应该解析什么开源库有任何建议，请告诉我。我可以在评论区或者推特上找到我。下次见！