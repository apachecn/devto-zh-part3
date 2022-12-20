# 注意 App -第 1 部分:设置节点 API

> 原文：<https://dev.to/akhilaariyachandra/note-app-part-1-setup-the-node-api-48p9>

> 这最初发布在我的博客上。如果您想了解 React 和 JavaScript 的更多信息，请查看它！

在这个系列中，我们将使用 Node、MongoDB & React 创建一个 notes 应用程序。对于第一部分，让我们设置 API。

我将使用 **yarn** 作为我的包管理器，但也可以随意使用 **npm** 或 **pnpm** 来代替。

# 初始化项目

首先让我们创建一个名为**‘notes-API’**的文件夹，并在其中使用
初始化一个项目

```
yarn init --yes 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用 [Backpack](https://github.com/jaredpalmer/backpack) 来构建和运行我们的 API，所以我们将把它作为一个开发依赖项来安装。**背包**有一些有用的功能，比如当任何文件改变时自动重新加载服务器，并且能够在没有任何配置的情况下运行 ES6 代码。

```
yarn add backpack-core -D 
```

Enter fullscreen mode Exit fullscreen mode

然后将以下脚本添加到`package.json`。

```
{  "scripts":  {  "dev":  "backpack",  "build":  "backpack build"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

`yarn dev`用于启动开发服务器，`yarn build`用于获得生产版本。

# 设置数据库

接下来让我们设置 MongoDB 数据库。如果你想要一个关于设置数据库的更详细的解释，你可以看看我以前的帖子[用 mongose](https://akhilaariyachandra.com/post/setup-mongodb-in-nodejs-with-mongoose/)在 Node.js 中设置 MongoDB。

我们将用 [mLab](https://mlab.com/) 创建一个数据库。创建之后，应该为数据库创建一个用户，并记下它的路径。路径应该类似于`mongodb://<dbuser>:<dbpassword>@ds250607.mlab.com:38485/notes-db`，其中`<dbuser>`和`<dbpassword>`分别是您为数据库创建的用户名和密码。

然后安装**猫鼬**依赖。

```
yarn add mongoose 
```

Enter fullscreen mode Exit fullscreen mode

之后，在项目中创建一个名为**‘src’**的文件夹，创建一个名为`database.js`的文件，并在其中插入以下代码。

```
// src/database.js
// Import the dependency
import mongoose from "mongoose";

// The database path
const dbPath =
  "mongodb://<dbuser>:<dbpassword>@ds250607.mlab.com:38485/test-db";

// Connect the database
mongoose.connect(dbPath, {
  useNewUrlParser: true
});

// Check if the API successfully connected to the database
const db = mongoose.connection;
db.on("error", () => {
  console.log("> error occurred from the database");
});
db.once("open", () => {
  console.log("> successfully opened the database");
});

// Export the database instance
export default mongoose; 
```

Enter fullscreen mode Exit fullscreen mode

用数据库的路径替换`dbPath`的值。

# 创建 notes 模型

一旦我们完成了数据库的设置，我们需要创建一个模型来对数据库执行 **CRUD** (创建、检索、更新&删除)操作。

首先在**【src】**中创建一个名为**【车型】**的文件夹。然后在其中创建一个名为`noteModel.js`的文件，并添加以下代码。

```
// src/models/noteModel.js
// Import the database instance
import mongoose from "../database";

// Define the schema for a note
const schema = {
  title: { type: mongoose.SchemaTypes.String, required: true },
  content: { type: mongoose.SchemaTypes.String, required: true }
};

const collectionName = "notes"; // Name of the collection of documents
const noteSchema = mongoose.Schema(schema);
const Note = mongoose.model(collectionName, noteSchema);

// Export the Note model
export default Note; 
```

Enter fullscreen mode Exit fullscreen mode

# 设置服务器和路由

现在我们已经设置了数据库和用户模型，我们可以开始为 API 创建服务器和路由了。我们将使用 [Express](https://expressjs.com/) 创建服务器。

让我们安装依赖项

```
yarn add express body-parser cors 
```

Enter fullscreen mode Exit fullscreen mode

依赖项安装完成后，在**‘src’**中创建文件`index.js`，代码为

```
// src/index.js
// Import the dependencies
import express from "express";
import bodyParser from "body-parser";
import cors from "cors";
import Note from "./models/noteModel";

// Create a new instance of the express server
const app = express();

// Apply middleware
app.use(bodyParser.json({ type: "application/json" })).use(
  cors({
    methods: ["POST", "GET", "PUT", "DELETE"],
    allowedHeaders: [
      "Access-Control-Allow-Origin",
      "Content-Type",
      "x-access-token"
    ]
  })
);

/**
 * We'll define our routes here for the CRUD operations
 */

// Set port number
const portNo = process.env.PORT || 8080;

// Start server
app.listen(portNo, () => {
  console.log(`> Server listening at http://localhost:${portNo}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

只是为了确保我们到目前为止所做的工作运行良好。如果没有问题，它应该显示

```
> Server listening at http://localhost:8080
> successfully opened the database 
```

Enter fullscreen mode Exit fullscreen mode

让我们用 **Ctrl+C** 停止服务器，并继续在 API 中添加路由。

我们的第一条路线将是创建新的笔记。

```
// Route to save a new note - POST method
app.post("/note", async (req, res) => {
  try {
    // Save note to DB
    const result = await Note.create({
      title: req.body.title,
      content: req.body.content
    });

    // Send the result of the save function as the server response
    return res.json(result);
  } catch (error) {
    // Handle error
    // Display error in console
    console.log(error);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

下一条路线是获取整个笔记列表

```
// Route to get all the notes - GET method
app.get("/note", async (req, res) => {
  try {
    // Get all notes from the database
    const result = await Note.find({}).exec();

    // Send the results as the server response
    return res.json(result);
  } catch (error) {
    // Handle error
    // Display error in console
    console.log(error);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一条路线来根据 ID 得到一张纸条。

```
// Route to get one note based on the ID - GET method
app.get("/note/:id", async (req, res) => {
  try {
    // Get the ID
    const id = req.params.id;

    // Find the note from the ID
    const result = await Note.findById(id).exec();

    // Send the result as the server response
    return res.json(result);
  } catch (error) {
    // Handle error
    // Display error in console
    console.log(error);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

倒数第二条路线是更新一条注释。

```
// Route to update a note - PUT method
app.put("/note/:id", async (req, res) => {
  try {
    // Get the ID
    const id = req.params.id;

    // Update the note
    const result = await Note.findByIdAndUpdate(id, {
      title: req.body.title,
      content: req.body.content
    }).exec();

    // Send the result as the server response
    return res.json(result);
  } catch (error) {
    // Handle error
    // Display error in console
    console.log(error);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们最后的方法是删除一条注释。

```
// Route to delete a note - DELETE method
app.delete("/note/:id", async (req, res) => {
  try {
    // Get the ID
    const id = req.params.id;

    // Delete the note
    const result = await Note.findByIdAndDelete(id).exec();

    // Send the result as the server response
    return res.json(result);
  } catch (error) {
    // Handle error
    // Display error in console
    console.log(error);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后`index.js`应该是这样的。

```
// src/index.js
// Import the dependencies
import express from "express";
import bodyParser from "body-parser";
import cors from "cors";
import Note from "./models/noteModel";

// Create a new instance of the express server
const app = express();

// Apply middleware
app.use(bodyParser.json({ type: "application/json" })).use(
  cors({
    methods: ["POST", "GET", "PUT", "DELETE"],
    allowedHeaders: [
      "Access-Control-Allow-Origin",
      "Content-Type",
      "x-access-token"
    ]
  })
);

// Route to save a new note - POST method
app.post("/note", async (req, res) => {
  try {
    // Save note to DB
    const result = await Note.create({
      title: req.body.title,
      content: req.body.content
    });

    // Send the result of the save function as the server response
    return res.json(result);
  } catch (error) {
    // Handle error
    // Display error in console
    console.log(error);
  }
});

// Route to get all the notes - GET method
app.get("/note", async (req, res) => {
  try {
    // Get all notes from the database
    const result = await Note.find({}).exec();

    // Send the results as the server response
    return res.json(result);
  } catch (error) {
    // Handle error
    // Display error in console
    console.log(error);
  }
});

// Route to get one note based on the ID - GET method
app.get("/note/:id", async (req, res) => {
  try {
    // Get the ID
    const id = req.params.id;

    // Find the note from the ID
    const result = await Note.findById(id).exec();

    // Send the result as the server response
    return res.json(result);
  } catch (error) {
    // Handle error
    // Display error in console
    console.log(error);
  }
});

// Route to update a note - PUT method
app.put("/note/:id", async (req, res) => {
  try {
    // Get the ID
    const id = req.params.id;

    // Update the note
    const result = await Note.findByIdAndUpdate(id, {
      title: req.body.title,
      content: req.body.content
    }).exec();

    // Send the result as the server response
    return res.json(result);
  } catch (error) {
    // Handle error
    // Display error in console
    console.log(error);
  }
});

// Route to delete a note - DELETE method
app.delete("/note/:id", async (req, res) => {
  try {
    // Get the ID
    const id = req.params.id;

    // Delete the note
    const result = await Note.findByIdAndDelete(id).exec();

    // Send the result as the server response
    return res.json(result);
  } catch (error) {
    // Handle error
    // Display error in console
    console.log(error);
  }
});

// Set port number
const portNo = process.env.PORT || 8080;

// Start server
app.listen(portNo, () => {
  console.log(`> Server listening at http://localhost:${portNo}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 包装完毕

我故意省略了测试 API，因为我不想让这篇文章太长，但是你可以使用像[邮差](https://www.getpostman.com/)或[失眠](https://insomnia.rest/)这样的软件来测试 API。
我的下一篇文章将是关于创建一个使用 React 的前端来使用这个 API。
我希望这篇文章对你有用。如果你对我如何改进有任何问题或建议，请在下面留言！😊