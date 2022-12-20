# 重命名析构属性的真实用例

> 原文：<https://dev.to/krichdev/real-world-use-case-for-renaming-destructed-properties-1ld3>

您听说了一个新的 javascript 特性，以及它将如何帮助您提高工作效率或编写更简洁的代码。你很兴奋。你去查阅文档，找出如何实现它，然后遇到一个`var foo = bar`例子。

然后你用谷歌搜索类似`real world example of ...`的东西，或者至少这是我通常的工作流程。

### 将析构变量赋予新的变量名

我知道你可以给析构的对象赋予新的变量名。我在 [mozilla 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Assigning_to_new_variable_names)上读到了更多关于这个特性的内容，并查看了他们的`foo/bar`示例:

```
 var o = {p: 42, q: true};
    var {p: foo, q: bar} = o;

    console.log(foo); // 42 
    console.log(bar); // true 
```

这看起来很容易理解，但是我仍然想不出一个真实的用例。直到我在一个  网站上工作，对页面数据进行多次 graphql 查询。

### 重命名析构对象属性的真实世界用例

我有一个页面，它有多个数据查询，并且有相似的模式。这些查询如下所示:

```
 export const query = graphql`
        query {
            imageFiles {
                edges {
                    node {
                        ...
                    }
                }
            }
            sourceYaml {
                edges {
                    node {
                        ...
                    }
                }
            }
        }
    ` 
```

我想在我的页面组件中使用这两组数据，正如您所看到的，它们有相似的形状和相同的属性名。

该查询的结果如下所示:

```
 data {
        imageFiles {
            edges {
                node {
                    ...
                }
            }
        }
        sourceYaml {
            edges {
                node {
                    ...
                }
            }
        }
    } 
```

然后，为了在我的页面组件中使用这些数据，我会像这样将它作为一个道具传入:

```
 const Page = ({ data }) => {
        return (
            ... page html
        )
    } 
```

这是可行的，但是因为我不想写出像`data.imageFiles.edges`这样冗长的钻取，所以我把数据对象析构成这样

```
 const Page = ({ 
        data: {
            imageFiles: { edges },
            sourceYaml: { edges },
        } 
     }) => {
        return (
            ... page html
        )
     } 
```

现在这个不会编译，因为`edges`定义了两次。写完之后，看到我会遇到问题，我立刻意识到我可以使用这里的重命名特性来解决这个问题。

下面是更新和重命名后的数据对象析构的样子

```
 const Page = ({ 
        data: {
            imageFiles: { edges: sourceImages },
            sourceYaml: { edges: sources },
        } 
     }) => {
        return (
            ... page html
        )
     } 
```

现在在这个组件内部，我可以使用分配给`imageFiles`和`sourceYaml`的`edges`属性的`sourceImages`和`sources`。

这不仅会缩短数据支柱，而且我认为它还会使它更容易阅读。因此，当我或另一个开发人员将来看到这段代码时，看到`sourceImages`和`sources`会比看到`data.imageFiles.edges`更有意义

请留下您在现实世界中如何使用该功能的任何评论！