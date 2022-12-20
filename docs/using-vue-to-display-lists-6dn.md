# 使用 Vue 显示列表

> 原文：<https://dev.to/workingwebsites/using-vue-to-display-lists-6dn>

如果你正在构建一个 web 应用程序，屏幕上会出现一个你需要的数据列表。数据可能在表格、项目符号列表或表单选项中。使用 Vue，显示这些数据并不是什么大问题。让我们看看它是怎么做的！

# 列表数据

假设您有一个简单的国家及其短代码列表，可以呈现在应用程序的各个部分。这个列表可能会改变，所以您希望它在一个 Vue 文件中生成。

# 设置它

要在您的应用程序中使用 Vue，您需要三样东西:

1.  Vue 已加载
2.  包含代码的元素
3.  显示内容的代码。

对于本教程，我们将使用一个简单的 HTML 文件来运行应用程序。

## HTML 文件

您的 HTML 页面应该看起来像:

```
<html lang="en">
<head>
    <!-- Load Vue -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <!-- Load script file.  'defer' loads after body is generated -->
    <script src="vue_lists.js" defer></script>
</head>

<body>
    <h1>List</h1>
    <div id="app">
       <!-- Div where Vue runs -->
    </div>
</body>
</html>
```

请注意，您的应用程序文件在脚本标记中有“defer”。延迟意味着脚本将在文档加载完之后加载*。这确保了应用程序的容器元素在应用程序代码加载之前被加载。*

## 应用程序文件

现在是应用程序文件(又名 vue_lists.js):

```
var app = new Vue({
    el: '#app',
    data: {
        countries_list: [
            { "name": "Canada", "code": "CAN" },
            { "name": "Mexico", "code": "MEX" },
            { "name": "United States of America", "code": "USA" }
        ],
    }
})
```

### 打破它

这里重要的是:

```
data: {
countries_list: [
....
]}
```

这意味着这个应用程序的数据有一个名为“countries_list”的对象数组。我们所要做的就是在 HTML 文件中显示对象数组。

# 在 HTML 中显示数据

通常在 Vue 中，通过将数据放在花括号{{ data_object }}中以 HTML 显示数据。因为这是一个由对象组成的*数组*，所以这是行不通的。您需要遍历数组来访问每一项。 **v-for** 指令正是这么做的。

```
<ul id="country_list">
    <li v-for="country in countries_list">
        {{ country.name }} ({{ country.code }})
     </li>
</ul>
```

### 打破它

“v-for”类似于“for each”。它遍历数组，一次一项，并创建另一个元素。在这种情况下，另一个

*   。

“countries_list 中的国家”是指“数组*countries*s*_ list*中的各项*的*，当前的将称为*国家*”。
结果正是我们想要的。**

*   加拿大(加拿大)
*   墨西哥
*   美利坚合众国(美国)

将所有这些放在一起:

```
<html lang="en">
<head>
    <!-- Load Vue -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <!-- Load script file.  'defer' loads after body is generated -->
    <script src="vue_lists.js" defer></script>
</head>

<body>
    <h1>List</h1>
    <div id="app">
       <ul id="country_list">
          <li v-for="country in countries_list">
            {{ country.name }} ({{ country.code }})
         </li>
       </ul>
    </div>
</body>
</html>
```

## 桌子

当然，创建数据表的工作方式是一样的。

```
<table>
    <tr>
        <th>Country</th>
        <th>Short Code</th>
    </tr>
    <tr v-for="country in countries_list">
        <td>{{ country.name }}</td>
        <td>{{ country.code }}</td>
    </tr>
</table>
```

## 选择元素

填充选择元素也是同样的想法。

```
<select name="country_code">
    <option v-for="country in countries_list" v-bind:value="country.code">
        {{ country.name }}
    </option>
</select>
```

等一下！这个“捆绑”是怎么回事？

```
"v-bind:value="country.code"
```

在 Vue 中，花括号对设置标签中的属性不起作用。相反，您可以使用 v-bind 将数据绑定到属性上。

需要知道的是，你可以绑定一个*单值* (country.code)，或者*整个对象* (country)。当你需要处理整个数据对象时非常有用！

# 结论

如您所见，显示数据数组实际上只是使用了 v-for 指令。在 HTML 中使用它给了您显示和格式化数据所需的所有灵活性。是啊，就这么简单。

此外，因为它在 Vue 中，很容易进入任何 web 项目，甚至是 [WordPress。](https://dev.to/workingwebsites/using-vue-in-wordpress-1b9l)

下载代码: [Github](https://github.com/workingwebsites/vue_lists)