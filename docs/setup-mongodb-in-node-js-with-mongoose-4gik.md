# 用 Mongoose 在 Node.js 中设置 MongoDB

> 原文：<https://dev.to/akhilaariyachandra/setup-mongodb-in-node-js-with-mongoose-4gik>

如果您正在用 Node 构建一个应用程序，您可能需要将它连接到一个 MongoDB 数据库。在这里，我会告诉你如何设置它。本指南假设您已经初始化了一个节点项目。

如果你还没有数据库，mLab 可能是获得免费数据库的最好地方。

# 设置数据库连接

首先让我们安装 mongoose 依赖项。

```
npm install mongoose --save 
```

Enter fullscreen mode Exit fullscreen mode

或者用纱线。

```
yarn add mongoose 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将使用数据库配置和实例创建一个文件，名为 database.js。

首先让我们导入依赖项；

```
const mongoose = require("mongoose"); 
```

Enter fullscreen mode Exit fullscreen mode

然后让我们将数据库的路径存储在一个变量中。路径应该如下所示，替换为您为数据库创建的用户。

```
const dbPath = "mongodb://<dbuser>:<dbpassword>@ds250607.mlab.com:38485/test-db"; 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们将连接到数据库。

```
mongoose.connect(dbPath, {
    useNewUrlParser: true,
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们需要导出数据库的这个实例，以备后用。

```
module.exports = mongoose; 
```

Enter fullscreen mode Exit fullscreen mode

一旦应用程序启动，如果有一个指示器显示应用程序是否成功连接到数据库，那就更好了。因此，让我们添加一些代码来解决这个问题。

```
const db = mongoose.connection;
db.on("error", () => {
    console.log("> error occurred from the database");
});
db.once("open", () => {
    console.log("> successfully opened the database");
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后，database.js 应该是这样的。

```
// database.js
const mongoose = require("mongoose");
const dbPath = "mongodb://<dbuser>:<dbpassword>@ds250607.mlab.com:38485/test-db";
mongoose.connect(dbPath, {
    useNewUrlParser: true,
});
const db = mongoose.connection;
db.on("error", () => {
    console.log("> error occurred from the database");
});
db.once("open", () => {
    console.log("> successfully opened the database");
});
module.exports = mongoose; 
```

Enter fullscreen mode Exit fullscreen mode

# 设置模型/模式

建立数据库连接后，让我们建立一个实体模型来保存和检索。在这个例子中，使用一个**用户**实体。它将有三个领域，名称，电子邮件和密码。我们会将所有模型存储在模型文件夹中。

```
// models/userModel.js
const mongoose = require("../database");
const schema = {
    name: { type: mongoose.SchemaTypes.String, required: true },
    email: { type: mongoose.SchemaTypes.String, required: true },
    password: { 
        type: mongoose.SchemaTypes.String, 
        required: true, 
        select: false
    }
};
const collectionName = "user"; // Name of the collection of documents
const userSchema = mongoose.Schema(schema);
const User = mongoose.model(collectionName, userSchema);
module.exports = User; 
```

Enter fullscreen mode Exit fullscreen mode

这里有一些注释。模式的结构在模式常量中定义。每个属性都应该有一个类型字段。必填字段用于设置该属性是否为必填字段。在上面的例子中，所有三个属性都是。password 属性有一个设置为 true 的额外选择字段。这是为了确保查询时默认情况下不返回 password 属性。

这就是开始使用数据库所需的全部设置。以下是如何使用该模型的一些示例。

```
// Create user
User.create({
    name: name,
    email: email,
    password: password
});
// Find user by email
User.findOne({
    email: email
});
// Find user by email with the password field included
User.findOne({
    email: email
}).select("+password"); 
```

Enter fullscreen mode Exit fullscreen mode

# 包装完毕

我希望本教程对为您的节点应用程序设置 MongoDB 数据库有用。如果你对我如何改进本教程有任何问题或建议，请在下面留言！😊