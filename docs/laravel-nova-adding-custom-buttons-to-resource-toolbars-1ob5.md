# Laravel Nova:向资源工具栏添加自定义按钮

> 原文：<https://dev.to/jake/laravel-nova-adding-custom-buttons-to-resource-toolbars-1ob5>

[![header image](img/433ddb833d8ca66eeb9894d397844fe1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eJDgd5wL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/FEVhO9n.png) 
如果你已经在 laravel/nova 上看到[issue 786-在 Github 上看到 issues](https://github.com/laravel/nova-issues/issues/786) 或者试图在 Laravel Nova 中给资源添加自定义按钮，你可能会感到沮丧。我花了一整天的时间试图解决这个问题。但是比你想的简单多了！

Laravel Nova 由 Vue 提供动力。JS 一个非常强大的 JS 微框架。每个资源都有一个自定义组件，该组件有自己的范围。这允许我们覆盖 Nova 的内置组件并添加自定义按钮。

让我们首先创建一个新的 Nova 资源工具:

```
php artisan nova:resource-tool 0x15f/custom-resource-toolbar 
```

Enter fullscreen mode Exit fullscreen mode

对所有提示说`yes`...

现在您已经创建了您的资源工具，导航到`nova-components/custom-resource-toolbar/resources/js`目录，在您最喜欢的 JS 编辑器中打开`tool.js`,然后粘贴以下内容。

```
Nova.booting((Vue, router) => {
    Vue.component('custom-detail-toolbar', require('./components/CustomDetailToolbar'));
    Vue.component('quotes-detail-toolbar', require('./components/QuotesDetailToolbar'));
}) 
```

Enter fullscreen mode Exit fullscreen mode

导航到`components`目录并删除`Tool.vue`。现在创建两个文件，一个名为`CustomDetailToolbar.vue`，另一个名为`QuotesDetailToolbar.vue`。将以下内容粘贴到`CustomDetailToolbar.vue` :

```
<template>
    <div class="flex w-full justify-end items-center mx-3">
        <component v-if="hasComponent" :is="component" />
    </div>
</template>

<script>
    export default {
        props: ['resourceName', 'resourceId'],
        computed: {
            component(){
                return this.resourceName + '-detail-toolbar';
            },
            hasComponent()
            {
                return this.component in this.$options.components;
            }    
        }
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

并将以下内容粘贴到`QuotesDetailToolbar.vue` :

```
<template>
    <div>
        <div class="flex w-full justify-end items-center">
           <a
                class="btn btn-default btn-icon btn-white"
                :href="'/nova-vendor/custom-resource-toolbar/export-quote/' + this.$parent.resourceId"
                style="background-color: var(--primary); color: white;">
                Export as PDF
             </a>
        </div>
    </div>
</template> 

<script>
    export default {
        props: ['resourceName', 'resourceId', 'field'],
        mounted() {
            var elements = document.getElementById('nova').querySelectorAll('h4');
            [].forEach.call(elements, function(element) {
                if(element.innerHTML === 'Custom Detail Toolbar') {
                    element.parentNode.remove();
                }
            });
        }
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以使用`npm run watch`构建您的资源工具，将其添加到您的资源中，打开您的`CustomResourceToolbar.php`文件，并将组件名称更改为`custom-detail-toolbar`。下面我来解释一下我们做了什么。

在`tool.js`中，我们注册了两个新组件`custom-detail-toolbar`和`quotes-detail-toolbar`，`custom-detail-toolbar`被 Nova 用来加载任何应该显示在工具栏上的组件。Nova 然后寻找资源的工具栏。您可以通过用您的资源复数名称后跟`-detail-toolbar`注册一个组件来定义您的资源工具栏组件。在该组件中，您可以添加任何应该放在工具栏中的 CSS/JS。

如果你注意到我的组件中有一个`mounted`函数，代码如下:

```
mounted() {
    var elements = document.getElementById('nova').querySelectorAll('h4');
    [].forEach.call(elements, function(element) {
        if(element.innerHTML === 'Custom Detail Toolbar') {
            element.parentNode.remove();
        }
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

通常，资源工具在资源详细信息页面上有一个面板。当我们的组件被安装以移除 nova 添加的给你的页面一个干净的感觉的面板时，这个函数运行。

你可以在 github 上找到本教程[使用的所有代码。](https://github.com/0x15f/custom-resource-toolbars)

**注:**这篇文章我很快就写好了，我稍后再整理。