# 使用 SuiteScript 2.0 进行 Netsuite 搜索

> 原文：<https://dev.to/smith288/netsuite-searching-with-suitescript-20-2k51>

什么是 Netsuite？

Netsuite 正迅速成为最流行的基于云的(cloud-based 名词，它只是一个在互联网上翻转的网站...*感叹*ERP 平台。ERP 代表企业资源规划。因此，它将处理库存，人力资源，管理网上商店。你明白了。Netsuite 很酷的一点是它非常适合用户定制。您可以添加字段、脚本、记录...许多解决方案提供商以提供直接插入其平台的服务为生。

这样一来，Netsuite 为开发人员提供了一个大型工具集来与他们的平台进行交互。他们称之为“SuiteScript ”,但实际上，它是内置于服务器基础设施中的 Javascript。

他们有各种类型，你也可以针对这些类型进行编码。客户端、计划、批量更新、映射/缩减、用户事件和其他一些...根据您的目标，每种类型与 Netsuite 的交互都略有不同。

在我的例子中，我想把重点放在用户事件脚本上。简而言之，它只是一个脚本，您可以指定在页面加载之前、提交之前或提交之后运行。

对于初学者来说，Netsuite 现在有很好的文档，但我想提供一些更简单的东西。

使用异步模块定义(AMD)原理，我们可以插入我们需要的自定义脚本。有关这方面的更多信息，请阅读[本](https://requirejs.org/docs/why.html)，它将提供更多背景信息。

如何做到这一点是我们定义我们的模块，给它们分配一个变量，并在密封函数中使用它们。

```
/**
 *@NApiVersion 2.x
 *@NScriptType UserEventScript
 */
define(['N/search', 'N/log'],
    function(search, log) {
        return {beforeLoad: beforeLoad};

        function beforeLoad(context){

            var srch = search.create({type: 'itemfulfillment', filters: [], columns:[]};

            var srchResults = srch.run();

            srchResults.each(function(result){
                log.debug('Result', result.id);
            });
        }
    }
); 
```

所以让我们来看看发生了什么:

```
/**
 *@NApiVersion 2.x   <--- Required to declare what API version (2.0, 2.x or 2.X)
 *@NScriptType UserEventScript <--- Required to declare what type of suitescript this is, in this case, a UserEventScript
 */ 
```

```
define(['N/search', 'N/log'], <--- Defining what Netsuite modules we wish to use The 'N/search' and 'N/log' are my most utilized modules
    function(search, log) { <-- Defining the globals to the modules we defined above 
```

```
 return {beforeLoad: beforeLoad} <--- Returning the callback function to the assignable return variable. In this case, beforeLoad is being used.  beforeSubmit and afterSubmit is also available to us. 
```

我们也可以通过编写回调函数来改变我们声明它的方式。我发现引用函数比内联写更具可读性，但这是个人偏好。

context 参数是 UserEventScript 的标准参数，包含对我们有价值的信息，例如脚本信息或特定于该脚本的其他入口点信息。

```
 return {beforeLoad: function(context){/* stuff */}}; 
```

现在是肉和土豆。为 beforeLoad 运行的回调函数

```
 function beforeLoad(context){

            // Initialize an array of filter objects based on a (name, condition, value struct)
            var myFilters = [{'internalid', 'is', '1234'}];

            // Initialize an array of columns you want to return
            var myColumns = ['name'];

            // Create search object by declaring the type and any filters and columns that you want to return
            var srch = search.create({type: 'itemfulfillment', filters: myFilters, columns: myColumns};

            // Run the created search (the run routine only runs 1000 max so if you need more, you would want to use the runPaged() routine 
            var srchResults = srch.run();

            // Loop through the array of results using either by inlining the function callback or by assigning the function to the each function.  In this scenario, I do use the inline callback for readability. I'm weird and inconsistent that way.
            srchResults.each(function(result){
                // Log to the script's execution log in debug verbosity of the result and it's build in id property
                log.debug('Result', result.id);
            });
        } 
```

这是 Suitescript 2.0 中搜索的最基本用法。它比 Suitescript 1.0 稍微复杂一点，但是它提供了更加模块化和灵活的设计模式。

我希望这能帮助任何学习 Netsuite 脚本的新手。我知道我是自学成才的，这些类型的文章对我来说是天赐之物，有助于解释代码，而不是功能和属性的通用 API 文档。