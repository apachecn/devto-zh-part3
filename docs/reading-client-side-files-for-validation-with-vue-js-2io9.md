# 使用 Vue.js 读取客户端文件进行验证

> 原文：<https://dev.to/raymondcamden/reading-client-side-files-for-validation-with-vue-js-2io9>

web 开发新手可能不知道使用`file`类型的表单输入是只读的。当然是有原因的。您不会希望邪恶的(我已经等了一段时间才使用这个词)JavaScript 程序设置字段的值并在幕后进行上传——这将是从您的计算机上窃取信息的一个好方法。然而，仅仅因为这个字段是只读的，并不意味着我们不能用它做很酷的事情。事实上，一旦用户选择了一个文件(或多个文件，记住`multiple`属性！)，您不仅可以看到文件类型、名称和大小，还可以阅读它。这为你提供了一些有趣的可能性。

让我们假设你有一张邮寄服务的表格。您希望用一组电子邮件地址作为收件人列表的种子。您可以允许用户从他们的机器上选择一个文本文件并上传。但是，在他们这样做之前，您可以先检查文件并向最终用户显示名称。

另一个选择是允许上传 JSON 格式数据的表单。在该文件被发送到服务器之前，您可以读取它，检查有效的 JSON 数据，然后潜在地呈现信息。你也可以做其他的检查，例如，你可能要求你的 JSON 数据是一个对象数组，其中关键字`name`和`gender`是必需的，而`age`是可选的。

与往常一样，您需要对用户发送的任何内容进行服务器端验证，但是能够先发制人地检查文件并向用户提供反馈可以为他们节省大量时间。我想我会分享一些使用 Vue.js 的例子，但是当然，你可以用任何(或者根本不用)框架来这样做。

### 读取文件

对于第一个例子，让我们考虑一个超级简单的例子

*   注意当在输入字段中选择一个文件时
*   检查它是否是一个文本文件
*   读入内容并显示出来

对于我的 HTML，我保持它简洁明了:

```
<div id="app" v-cloak>

  <input type="file" ref="myFile" @change="selectedFile"><br/>
  <textarea v-model="text"></textarea>

</div> 
```

Enter fullscreen mode Exit fullscreen mode

这是非常标准的 Vue 东西，但是请注意`ref`的用法。这是我们稍后读取值的方式。

现在 JavaScript:

```
Vue.config.productionTip = false;
Vue.config.devtools = false;

const app = new Vue({
  el:'#app',
  data: {
    text:''
  },
  methods:{
    selectedFile() {
      console.log('selected a file');
      console.log(this.$refs.myFile.files[0]);

      let file = this.$refs.myFile.files[0];
      if(!file || file.type !== 'text/plain') return;

      // Credit: https://stackoverflow.com/a/754398/52160
      let reader = new FileReader();
      reader.readAsText(file, "UTF-8");
      reader.onload = evt => {
        this.text = evt.target.result;
      }
      reader.onerror = evt => {
        console.error(evt);
      }

    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

所以这里的主要动作是`selectedFile`方法。每当输入字段触发一个`change`事件时就会运行。我使用`this.$refs.myFile`来引用我使用过的原始 DOM 元素并读取值。注意这是一个数组，所以为了简单起见，我只取第一个值。理论上，最终用户可以使用开发工具来添加`multiple`，然后选择多个文件，但我不必担心这一点。

接下来，我使用`FileReader` API 读入文件。这是异步的，你可以看到两个处理程序来响应`onload`和`onerror`事件。对于`onload`，我简单地将值传递给`this.text`，它将在文本区域中呈现。您可以在下面的代码栏中看到这一点。

[https://codepen.io/cfjedimaster/embed/qGPrJY?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/qGPrJY?height=600&default-tab=result&embed-version=2)

### 一串名字

想象你被冤枉了。严重委屈。你已经有了一份名单。罪有应得的人。举个例子，就叫你艾莉亚吧。

[![Arya Stark, don't get on her bad side](img/f00fff68495108b09e2dca70b769c2dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--81zvg_pD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2019/05/arya.jpg)

为了帮助处理这个名称列表，让我们构建一些代码，这些代码将读入名称的文本文件，报告总长度，并显示前十名。列表可能非常大，但是通过显示一个小的子集，用户可以很快确定文件是否设置正确，或者他们选择了错误的文件。这里有一个简单的例子。

```
<div id="app" v-cloak>

  <h2>Upload List of Names</h2>
  <input type="file" ref="myFile" @change="selectedFile"> 
  <input type="submit" value="Upload File" />
  <div v-if="allNames.length">
    <p>Your file contains {{allNames.length}} names. Here's the first ten names.</p>
    <ul>
      <li v-for="name in names">{{name}}</li>
    </ul>
  </div>

</div> 
```

Enter fullscreen mode Exit fullscreen mode

顶部提示输入文件，并使用与第一个示例相似的属性。接下来我要展示的是。我打印出文件中有多少个名字，然后迭代一个`names`值。这将是一个只有前十个值的虚拟财产。(对了，我不喜欢用`allNames.length`。虽然我很欣赏 Vue 让我在我的 HTML 中做一点逻辑，但我更喜欢使用一个简单的布尔值来代替`v-if`和另一个长度值。)

好了，这里是 JavaScript:

```
Vue.config.productionTip = false;
Vue.config.devtools = false;

const app = new Vue({
  el:'#app',
  data: {
    allNames:[]
  },
  computed:{
    names() {
      return this.allNames.slice(0,10);
    }
  },
  methods:{
    selectedFile() {
      console.log('selected a file');
      console.log(this.$refs.myFile.files[0]);

      let file = this.$refs.myFile.files[0];
      if(!file || file.type !== 'text/plain') return;

      // Credit: https://stackoverflow.com/a/754398/52160
      let reader = new FileReader();
      reader.readAsText(file, "UTF-8");

      reader.onload = evt => {
        let text = evt.target.result;
        this.allNames = text.split(/\r?\n/);
        //empty string at end?
        if(this.allNames[this.allNames.length-1] === '') this.allNames.pop();
      }

      reader.onerror = evt => {
        console.error(evt);
      }

    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

一般来说，唯一有趣的是在`reader.onload`事件中。我仍在检查文件类型，但现在当我在新行上拆分它并删除文件值时，它是空白的。这将设置`allNames`值。`names`值在`computed`块中，仅由前十个值组成。你可以在下面玩这个——只需列出你自己的名字。请不要把我的名字写在上面。

[https://codepen.io/cfjedimaster/embed/dEVvgq?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/dEVvgq?height=600&default-tab=result&embed-version=2)

### 猫文件

[![A cat filing its nails](img/3d68a1294c73a9ad25f036b679ccb2f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dFyLaBSR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://static.raymondcamden.cimg/2019/05/catfile.gif)

看，这是猫的档案。明白了吗？不好意思，用那个 gif 我等了好久了。所以在这个场景中，我将演示一个解析 JSON 文件的例子。它将首先检查文件是否包含 JSON 文本，如果包含，则呈现结果。与前面的例子不同，我将只呈现每一行数据。数据将是一个 cat 数组。你知道一群猫被称为牛逼吗？是的-我在维基百科上看到的。

布局如下:

```
<div id="app" v-cloak>

  <h2>Upload Cat Data</h2>
  <input type="file" ref="myFile" @change="selectedFile"> 
  <input type="submit" value="Upload File" />
  <div v-if="cats">
    <h3>Data Preview</h3>
    <table border="1">
      <thead>
        <tr>
          <th>Name</th>
          <th>Age</th>
          <th>Gender</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="cat in cats">
          <td>{{cat.name}}</td>
          <td>{{cat.age}}</td>
          <td>{{cat.gender}}</td>
        </tr>
      </tbody>
    </table>
  </div>

</div> 
```

Enter fullscreen mode Exit fullscreen mode

我用一张桌子来渲染猫，是的，就是这样。下面是 JavaScript:

```
Vue.config.productionTip = false;
Vue.config.devtools = false;

const app = new Vue({
  el:'#app',
  data: {
    cats:null
  },
  methods:{
    selectedFile() {
      console.log('selected a file');
      console.log(this.$refs.myFile.files[0]);

      let file = this.$refs.myFile.files[0];
      if(!file || file.type !== 'application/json') return;

      // Credit: https://stackoverflow.com/a/754398/52160
      let reader = new FileReader();
      reader.readAsText(file, "UTF-8");

      reader.onload = evt => {
        let text = evt.target.result;
        try {
          this.cats = JSON.parse(text);
        } catch(e) {
          alert("Sorry, your file doesn't appear to be valid JSON data.");
        }
      }

      reader.onerror = evt => {
        console.error(evt);
      }

    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里重要的一点是我如何测试有效的 JSON，一个简单的围绕着`JSON.parse`的`try/catch`。仅此而已。你可以在这里做更多的检查:

*   有效的 JSON 数据是数组吗？
*   是不是至少一排？
*   我们有必需的栏目吗？
*   如果我们有额外的数据，比如一个“isFeisty”布尔值，我们会认为这是一个错误吗？

诸如此类。随意叉下面的 CodePen，去猫疯！

[https://codepen.io/cfjedimaster/embed/JqrJeR?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/JqrJeR?height=600&default-tab=result&embed-version=2)

所以一如既往，我很想知道人们是否在使用这些技术。在下面给我留言，分享你的所作所为吧！

*标题图片由[万基·金](https://unsplash.com/photos/2Nca6Aum17o?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 Unsplash 上拍摄*