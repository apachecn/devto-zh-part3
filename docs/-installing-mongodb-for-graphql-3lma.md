# 为 GraphQL 安装 MongoDB

> 原文：<https://dev.to/graphqleditor/-installing-mongodb-for-graphql-3lma>

我的同事阿图尔关于如何用 GraphQL 设置 MongoDB 的简短教程。

## 用家酿安装 MongoDB

首先，你需要安装一个[自制软件](//brew.sh)。要做到这一点，进入 Visual Code Studio，运行来自 [Brew.sh](//brew.sh) :
的这行代码

```
 /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

现在你应该已经安装了你的自制软件。如果您已经安装了它，我们建议在继续之前进行 brew 更新，然后您可以安装 MongoDB:

```
$ brew update
$ brew install mongo 
```

你现在需要做的是转到一个根目录，为数据库创建目录并设置其权限，然后你就可以启动 Mongo:

```
$ sudo mkdir -p /data/db
$ sudo chmod 777 /data/db
$ mongod 
```

瞧啊。我们已经安装并运行了它。

## 獴

[mongose](https://mongoosejs.com/)提供了一个直接的、基于模式的解决方案来为您的应用程序数据建模。
它包括:

*   内置型铸件，
*   验证，
*   查询大楼，
*   业务逻辑挂钩

所以还是装 Mongoos 吧。确保您在正确的目录中，并使用`$npm install` :

```
$ npm install --save mongoose 
```

## 连接蒙哥

创建一个新文件夹，并将您的`resolvers.js`和`schema.js`文件移动到那里。记得更新你的`index.js`文件，确保所有的东西都能正确的导入到这里。一旦我们完成了，我们可以在我们的新文件夹中创建一个新文件，我们将使用它来连接 Mongo 到我们的数据库，让我们称它为`connect.js`。

```
import mongoose from 'mongoose';

mongoose.Promise = global.Promise;
mongoose.connect =('mongodb://localhost/users', {
    useMongoClient: true
}); 
```

当我们完成后，我们可以开始为 Mongo 创建模式，使用与原始模式相同的元素。

```
const usersSchema = new mongoose.Schema({
    name: 
        type: String
    }
}) 
```

下一个重要的步骤是创建一个包含模型的变量的值，传递模式并导出:

```
const Users = mongoose.model('users', usersSchema)
export {Users}; 
```

现在让我们创建一些 GraphQL 解析器。因此，让我们转到`resolvers.js`，开始将 Mongoose 和用户导入解析器:

```
import mongoose from 'mongoose';
import { Users } from './connect';

export const resolvers ={
    Query: {
        getUser: ({id}) => {
            return new User (id, userDatabase[id]);
        },
    },
    Mutation: {
        createUser: (root, { input}) => {
            const newUser = new Users({
                name: input.name,
            });

            newUser.id = newUser._id;

            return new Promise((resolve, object)) =>
                newUser.save((err) => {
                    if (err) reject (err)
                    else resolve(newUser)
                })
            })    
        },
    },
}; 
```

[![Done!](img/db49da64d9621046e244549e6c39e6eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Csd25QOs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y339isgl6y1so36a7wig.gif)