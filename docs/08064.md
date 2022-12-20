# 在 WordPress 中使用 Vue

> 原文：<https://dev.to/workingwebsites/using-vue-in-wordpress-1b9l>

就像花生酱和果酱一样，有时候两种好东西放在一起会更好。

在本教程中，我将向你展示如何在 WordPress 网站中使用 Vue。本教程是为那些用 WordPress 和 Vue 编程的人或者对其中任何一个感兴趣的人准备的。PHP，WordPress，Javascript，Vue 方面的知识会有帮助。

## Vue 程序员:为什么是 WordPress？

据统计，WordPress 运营着全球约三分之一的网站。如果你在 Vue 中创建了一些你想分享的东西，WordPress 是一个很好的平台。

你可以定制你自己的网站，或者在 WordPress 的插件库中分享你的代码。

## WordPress 程序员:为什么是 Vue？

简而言之，Vue 以其简单性和健壮性而闻名，是开发的可靠选择。如果你在你的网站上添加功能，它是一个很好的工具。它是用 JavaScript 编写的，所以大多数开发人员都很熟悉。

WordPress 使用 React，我甚至可以使用 Vue 吗？
当然！虽然 WordPress 中的一些功能可能在 React 中，但你并不局限于此。

WordPress 是开源的，做让你开心的事。

## 教程概述

我们将编写一个简短的代码来显示在 Vue 中生成的内容。我们将使用一个简单的例子，因为这更多的是关于如何把 Vue 和 WordPress 放在一起，而不是它们做什么。

方法是:

1.  在 WordPress 中创建一个插件来使用代码
2.  **在插件中创建一个短代码**
3.  **创建您的 Vue** 代码
4.  **加载 Vue** ，然后**你的代码**文件。
5.  确保你的**。画完 dom** 后加载 js 文件。

基本就是这样。让我们进入细节。

## 插件。

插件既简单又健壮，它们允许你给你的网站添加功能。这个插件将是一个简单的“hello world”类型。我们称之为' **wp-vue-tutorial** '，所以让我们开始吧:

### 1)在你的 WordPress 插件目录中创建一个新文件夹。

这是保存文件的地方，应该是插件的名字。对于这个教程，让我们使用:
**/WP-content/plugins/WP-vue-tutorial**

我们需要一个“主文件”，所以让我们这样做。

### 2)在目录中，用插件名创建一个主文件。

这个文件名为“**wp-vue-tutorial.php**”，所以它看起来应该是:
**/WP-content/plugins/WP-vue-tutorial/WP-vue-tutorial . PHP**

是的，**插件名、文件夹名和主文件名都是相同的**，所以 WordPress 知道在哪里可以找到它。

一旦 WordPress 打开它，它需要更多关于这个东西的信息。那包括在标题里。我们保持简单，所以在**wp-vue-tutorial.php**中，添加以下代码:

```
<?php
/**
 * Plugin Name: WordPress Vue Tutorial
 * Description: A demo on how to use Vue in WordPress.
 */
?>
```

更多信息:[https://developer.wordpress.org/plugins/](https://developer.wordpress.org/plugins/)

如果你打开你的 WordPress 仪表盘，你会看到你的插件。
**仪表盘** - > **插件**
它应该出现在列表中。

### 3)激活你的插件。

**点击插件上的激活**。

现在它被加载了，但是没有发生什么，因为没有代码要运行。所以，让我们创建一个短代码。

一个 shortcode 告诉 WordPress 将你的 PHP 代码生成的内容插入到页面或帖子的内容中。例如。用户输入类似*【我的短码】*的内容，当页面显示时，它显示“*这是来自我的短码的内容。*

注意:WordPress 当前的文本编辑器(Gutenberg)允许你在。短代码仍然可以使用，事实上有一个专门针对短代码的模块。我们在本教程中使用短代码，因为它是一个有效的标准。

更多信息:[https://codex.wordpress.org/Shortcode_API](https://codex.wordpress.org/Shortcode_API)

### 4)在你的插件中创建一个短代码。

将以下代码添加到您的插件文件中(**wp-vue-tutorial.php**):

```
 //Add shortscode
 function func_wp_vue(){
   return "Hello Shortcode";
}
add_shortcode( 'wpvue', 'func_wp_vue' );
```

分解一下:
函数 func_wp_vue()返回文本 Hello Shortcode。

| 添加 _ 短码( | 使这个短代码可用。 |
| wpvue '， | *用户在内容中使用的代码*
*【wpvue】。* |
| func _ wp _ vue 函数 | *返回要显示字符串的函数。* |
| ); | *短码结束。* |

您的文件应该是这样的:

```
<?php
/**
 * Plugin Name: WordPress Vue Tutorial
 * Description: A demo on how to use Vue in WordPress.
 */

//Add shortscode
 function func_wp_vue(){
 return "Hello Shortcode";
}
add_shortcode( 'wpvue', 'func_wp_vue' );
?>
```

测试一下。
**编辑**，或者**添加新的** WordPress **页面**。在**内容**区域，输入**【WP vue】**。
**发布**页面看看吧。你应该看到 *Hello Shortcode* 。

短代码可以返回不止一个单词，我们也可以在。

让我们将代码改为:

```
//Add shortscode
function func_wp_vue(){
  $str= "<div id='divWpVue'>"
    ."Message from Vue: "
    ."</div>";
  return $str;
} // end function
```

再测试一次。

## 设置视图

要使用 Vue，你需要三样东西:

1.  Vue js 文件需要被加载到页面上，这样你才能使用框架。
2.  你需要一个**元素**在你的页面上。通常是一个 div。
3.  您需要. js 文件中的 **Vue 代码在元素中工作。**

### load view . js-载入检视. js

先说加 Vue。您可以通过 CDN 的链接添加 Vue。有两个版本，一个用于带有调试选项的开发，另一个用于更快更精简的生产。我们将使用开发版:**https://cdn.jsdelivr.net/npm/vue/dist/vue.js**

我们需要将它加载到 WordPress 中，这可以通过' **wp 入队脚本**和' **wp_register_script** 来完成。
这个钩子在需要的时候自动把脚本加载到 WordPress 的脑袋里。

看起来像是:

```
function func_load_vuescripts() {
 wp_register_script( 'wpvue_vuejs', 'https://cdn.jsdelivr.net/npm/vue/dist/vue.js');
}
add_action('wp_enqueue_scripts', 'func_load_vuescripts');
```

分解:

| 函数 func_load_vuescripts() { | *定义功能。* |
| wp _ 注册 _ 脚本( | *告诉 WP 这个脚本存在*
*(我们稍后加载)*。 |
| ' wpvue_vuejs '， | 这是 WordPress 对脚本的引用。 |
| " https://cdn . jsdelivr . net/NPM/view/dist/view . js "； | *实际脚本文件。* |
| ); | 关闭 wp_register_script。 |
| } | *关闭该功能。* |
| add_action( | WordPress，这样做。 |
| wp_enqueue_scripts '， | *挂钩到‘WP _ enqueue _ scripts’。* |
| ' func_load_vuescripts ' | *对‘WP _ enqueue _ scripts’执行此函数。* |
| ); | *结束动作。* |

欲了解更多信息:

*   注册脚本:[https://developer . WordPress . org/reference/functions/WP _ register _ script/](https://developer.wordpress.org/reference/functions/wp_register_script/%20)
*   enquee 脚本:[https://codex . WordPress . org/Plugin _ API/Action _ Reference/WP _ enqueue _ Scripts](https://codex.wordpress.org/Plugin_API/Action_Reference/wp_enqueue_scripts%20)

我们将**把代码添加到你的插件文件**中，在短代码上面。
应该是这样的:

```
<?php
/**
 * Plugin Name: WordPress Vue Tutorial
 * Description: A demo on how to use Vue in WordPress.
 */

//Register Scripts to use 
function func_load_vuescripts() {
 wp_register_script( 'wpvue_vuejs', 'https://cdn.jsdelivr.net/npm/vue/dist/vue.js');
}
add_action('wp_enqueue_scripts', 'func_load_vuescripts');

//Add shortscode
function func_wp_vue(){
  //Build String
  $str= "<div id='divWpVue'>"
  ."Vue code here: "
  ."</div>";

  //Return
  return $str;
} // end function

//Add shortcode to WordPress
add_shortcode( 'wpvue', 'func_wp_vue' );
?>
```

现在，我们告诉 WordPress 关于 Vue.js 的消息，但是我们还没有加载它。您将它加载到 shortcode 函数中，以便在 shortcode 运行时触发它。有道理？

在您的短代码函数中包含以下内容:

```
//Add Vue.js
  wp_enqueue_script('wpvue_vuejs');
```

情绪完全失控

| wp _ 入队 _ 脚本( | 加载这个脚本 |
| ' wpvue_vuejs ' | 我们在注册脚本时定义了一个名为‘WP vue _ vuejs’的脚本。 |
| ); | *结束入队脚本* |

它应该看起来像:

```
function func_wp_vue(){
  //Add Vue.js
  wp_enqueue_script('wpvue_vuejs');

  //Build String
  $str= "<div id='divWpVue'>"
  ."Vue code here: "
  ."</div>";

  //Return
  return $str;
} // end function
```

现在，如果你运行它，没有什么变化，但是如果你查看页面的**源代码**，你会发现:

**<脚本类型= ' text/JavaScript ' src = ' https://cdn . jsdelivr . net/NPM/view/dist/view . js？见= 5 . 1 . 1 `></脚本>**

Vue 现在已经加载了，我们可以使用 shortcode (' *divWpVue* ')中生成的 div，我们所需要的只是一些 Vue 代码来在那个 div 中运行。

### 为 Vue 创建您的代码文件

新建一个文件: **vuecode.js** 你的插件目录:
**WP-content/plugins/WP-vue-tutorial**

这里应该有两个文件:

*   wp-vue-tutorial.php
*   你看，js

更多信息:
[https://vuejs.org/v2/guide/](https://vuejs.org/v2/guide/)

这是一个非常简单的演示，因此包含以下代码:

```
var app = new Vue({
  el: '#divWpVue',
  data: {
    message: 'Hello Vue!'
  }
})
```

情绪完全失控

| var app =新视图。[ | *我们正在创建一个名为“app”的 var，它实际上是一个 Vue 实例。* |
| el: '#divWpVue '， | *这个应用程序运行在元素‘divWpVue’中。* |
| 数据:{ | 这个应用程序有数据。 |
| 消息:“您好，视图！” | 一条数据被称为“消息”，其值为“Hello Vue！” |
| }
}) | *关闭数据部分
关闭应用部分* |

有关 Vue 的更多信息:

*   [https://vuejs.org/v2/guide/index.html](https://vuejs.org/v2/guide/index.html)
*   [https://vuejs.org/v2/guide/#Declarative-Rendering](https://vuejs.org/v2/guide/#Declarative-Rendering)

为了显示消息，您需要在 div 中包含数据消息。更改您的短代码函数( **func_wp_vue** )以包含 **{{message}}** 。

双花括号是在 HTML 文件中包含 Vue 内容的一种方式。如果在已经分配给 Vue 实例的元素中添加 *{{message}}* ，它将神奇地出现。这当然是简短的非技术性答案。

**重要！如果你检查你的文件，一切正常，你会看到*‘你好，Vue！’*放置 *{{message}}* 的地方。如果您看到 *{{message}}* ，说明有问题。**

让我们**将*****{ {消息}}*** **添加到短码**中。

```
function func_wp_vue(){
  //Add Vue.js
  wp_enqueue_script('wpvue_vuejs');

  //Build String
  $str= "<div id='divWpVue'>"
  ."Message from Vue: {{ message }}"
  ."</div>";

  //Return
  return $str;
} // end function
```

如果你现在测试，就不行了，会显示 *{{message}}* ，而不是*‘Hello Vue！’*

为什么？因为我们还没有加载你的 *vuecode.js* 文件。我们可以像加载 Vue CDN 文件一样加载它，对吗？

嗯（表示踌躇等）...不完全是。你在正确的轨道上，但有几个陷阱要注意。让我们走一遍。

要注册 vuecode.js，您可能会想到下面这样的情况:

```
 wp_register_script('my_vuecode', 'vuecode.js')
```

不，不会，因为 WordPress 找不到 *vuecode.js.*

**抓到你了:你的文件没有加载**

记住短代码显示在一个不同于插件路径的页面上。这是一种逻辑的镜子大厅，没有什么是它出现的地方。

要修复它，您需要更精确地知道文件的位置。这是通过添加一个 WordPress 函数来处理的，该函数返回你正在使用的插件文件的路径。 **plugin_dir_url** 就可以了。

```
wp_register_script('my_vuecode', plugin_dir_url( __FILE__ ).'vuecode.js') 
```

更多关于插件网址的信息:[https://codex.wordpress.org/Function_Reference/plugins_url](https://codex.wordpress.org/Function_Reference/plugins_url)

**抓住:Vue.js 需要先加载**

你的 vuecode.js 在 Vue 上运行，所以确保那些文件在你开始运行 Vue 代码之前已经被加载了。

为了确保这一点，通过**向 register_script 函数添加依赖关系**，告诉 WordPress 它*依赖于 Vuejs 的*。使用 Vue.js 脚本寄存器中的句柄。

```
wp_register_script('my_vuecode', plugin_dir_url( __FILE__ ).'vuecode.js', 'wpvue_vuejs' );
```

**抓到你了:你的 Vue 代码文件必须在 DOM 加载后出现**

为什么？它在*头*中加载脚本，但是你的 vuecode.js 在*体*中控制一个 div，它还没有被加载。它找不到它并抛出一个错误。

因此，**首先加载 vue . js**，然后加载 DOM 的主体，然后加载控制脚本(vuecode.js)。你可以通过**寄存器脚本**通过**给参数**添加一个‘真’值来实现。 *true* 告诉 WordPress 在页脚加载脚本。

它应该是这样的:

```
wp_register_script('my_vuecode', plugin_dir_url( __FILE__ ).'vuecode.js', 'wpvue_vuejs', true );
```

打破它

| wp _ 注册 _ 脚本( | 注册这个脚本。 |
| ' my_vuecode ' . | *其柄(参考)。* |
| plugin_dir_url( __ **文件** __)。' vuecode.js '， | 该文件与该插件位于同一目录。这是它的链接。 |
| ' wpvue_vuejs '， | 确保先将它装入。这是对另一个**注册的**剧本的引用。 |
| 真实的 | *在页脚加载脚本。* |
| ); | *关闭该功能。* |

加载脚本的函数应该如下所示:

```
//Register scripts to use
function func_load_vuescripts() {
    wp_register_script('wpvue_vuejs', 'https://cdn.jsdelivr.net/npm/vue/dist/vue.js');
    wp_register_script('my_vuecode', plugin_dir_url( __FILE__ ).'vuecode.js', 'wpvue_vuejs', true );
}
add_action('wp_enqueue_scripts', 'func_load_vuescripts');
```

关于注册脚本的更多信息:

*   注册脚本:[https://developer . WordPress . org/reference/functions/WP _ register _ Script/](https://developer.wordpress.org/reference/functions/wp_register_script/)
*   插件网址:[https://codex . WordPress . org/Function _ Reference/plugin _ dir _ URL](https://codex.wordpress.org/Function_Reference/plugin_dir_url)

应该可以了。但是，你还是什么都看不到，为什么不呢？做完这些后，你只注册了脚本。WordPress 对此了如指掌，但你并没有告诉它解雇它。

**抓住你了:不要忘记排队/加载脚本**

好的，这个就像查询 Vue.js 脚本一样。将以下内容添加到短代码函数- ' **func_wp_vue** '。

```
wp_enqueue_script('my_vuecode');
```

**func_wp_vue** '应该是这样的:

```
//Return string for shortcode
function func_wp_vue(){
  //Add Vue.js
  wp_enqueue_script('wpvue_vuejs');
  //Add my code to it
  wp_enqueue_script('my_vuecode');

  //Build String
  $str= "<div id='divWpVue'>"
    ."Message from Vue: {{ message }}"
    ."</div>";

  //Return to display
  return $str;
} // end function

//Add shortcode to WordPress
add_shortcode( 'wpvue', 'func_wp_vue' );
```

## 将所有这些放在一起:

你应该有一个名为“wp-vue-tutorial”的插件。在插件目录中，应该有两个文件:

*   **vuecode.js** (里面有 Vue 代码)
*   **wp-vue-tutorial.php**(带有 WordPress 代码)

### **vuecode.js**

在 Vue.js 下运行的 Javascript 文件应该如下所示:

```
var app = new Vue({
  el: '#divWpVue',
  data: {
    message: 'Hello Vue!'
  }
})
```

### **wp-vue-tutorial.php**

你的 WordPress 文件定义了插件并包含了短代码，看起来应该是这样的:

```
<?php
/**
 * Plugin Name: WordPress Vue Tutorial
 * Description: A demo on how to use Vue in WordPress.
 */

//Register scripts to use
function func_load_vuescripts() {
    wp_register_script('wpvue_vuejs', 'https://cdn.jsdelivr.net/npm/vue/dist/vue.js');
    wp_register_script('my_vuecode', plugin_dir_url( __FILE__ ).'vuecode.js', 'wpvue_vuejs', true );
}
//Tell WordPress to register the scripts 
add_action('wp_enqueue_scripts', 'func_load_vuescripts');

//Return string for shortcode
function func_wp_vue(){
  //Add Vue.js
  wp_enqueue_script('wpvue_vuejs');
  //Add my code to it
  wp_enqueue_script('my_vuecode');

  //Build String
  $str= "<div id='divWpVue'>"
    ."Message from Vue: {{ message }}"
    ."</div>";

  //Return to display
  return $str;
} // end function

//Add shortcode to WordPress
add_shortcode( 'wpvue', 'func_wp_vue' );
?>
```

注意:你可以在一个主题的 functions.php 文件中包含这个短代码，只要这个主题还在使用中，或者它的 functions.php 文件没有被升级覆盖，这个短代码仍然可以工作。我更喜欢插件，因为它将风格和内容分开，通常我的 Vue 代码都是关于内容的。

更多信息:
[https://codex.wordpress.org/Functions_File_Explained](https://codex.wordpress.org/Functions_File_Explained)

如果你使用主题函数文件，你需要调用与你的主题相关的文件路径，而不是插件。

更多信息:
[https://codex . WordPress . org/Function _ Reference/get _ template _ directory](https://codex.wordpress.org/Function_Reference/get_template_directory)

**下载文件**

您可以在
[【https://github.com/workingwebsites/wp-vue-tutorial】](https://github.com/workingwebsites/wp-vue-tutorial)下载文件

## 结论

好吧，所以“你好，Vue”并没有引起轰动。然而，一旦你有了这些，你就可以看到如何通过 Vue 给你的 WordPress 站点增加很多功能。

通过在你的 WordPress 站点中使用 Vue，访问 API 并使你的站点更具交互性变得更加容易。同样，如果你有一些用 Vue 写的巧妙的代码，把它放到 WordPress 站点(有很多)也不是什么大技巧。

就像花生酱和果酱一样，把两种好东西结合在一起会让它变得更好！