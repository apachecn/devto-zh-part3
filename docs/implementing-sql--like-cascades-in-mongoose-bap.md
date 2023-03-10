# 在 Mongoose 中实现类似 SQL 的级联

> 原文：<https://dev.to/kwabenberko/implementing-sql--like-cascades-in-mongoose-bap>

我最近在做一个业余项目:一个基于角色的访问控制应用程序，我需要在一些模型中实现`cascade delete`。
例如，用户模型有一个`roles`属性，它是角色模型
的`ObjectId`的数组

```
const mongoose = require("mongoose");

const UserSchema = new mongoose.Schema({
  username: {
    type: String,
    unique: true,
    required: true
  },
  password: {
    type: String,
    required: true
  },
  roles: [
    {
      type: mongoose.Schema.Types.ObjectId,
      required: true,
      ref: "Role"
    }
  ]
}); 
```

Enter fullscreen mode Exit fullscreen mode

角色是动态创建并分配给用户的。但是我面临的问题是，当一个角色被删除时，它在用户模型中的`ObjectId`引用没有被删除，因此成为[孤立的](https://database.guide/what-is-an-orphaned-record)，因此破坏了引用完整性。

为了防止这种情况，我需要实现`cascade delete`。在 SQL 中，创建`users_roles`表时添加`ON DELETE CASCADE`引用动作非常简单，如下:

```
CREATE TABLE users_roles (
    user_id int unsigned not null, 
    role_id int unsigned not null, 
    PRIMARY KEY (user_id, role_id), 
    FOREIGN KEY (user_id) REFERENCES users (id) 
        ON DELETE CASCADE, 
    FOREIGN KEY(role_id) REFERENCES roles (id)
            ON DELETE CASCADE
); 
```

Enter fullscreen mode Exit fullscreen mode

但是我用的是 [mongodb](https://www.mongodb.com) 和[mongose](https://mongoosejs.com)ODM。我该怎么做呢？我可以通过使用 mongose[中间件](https://mongoosejs.com/docs/middleware.html)来解决这个问题。根据他们的文档，mongoose 中间件或钩子是在异步函数执行期间被传递控制的函数。特别是中间件或钩子正是我所需要的，因为它允许我在数据库操作完成后立即执行一些操作:

```
const mongoose = require("mongoose");
const User = require("./user");

const RoleSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true
  }
});

RoleSchema.post("remove", document => {
  const roleId = document._id;
  User.find({ roles: { $in: [roleId] } }).then(users => {
    Promise.all(
      users.map(user =>
        User.findOneAndUpdate(
          user._id,
          { $pull: { roles: roleId } },
          { new: true }
        )
      )
    );
  });
});

module.exports = mongoose.model("Role", RoleSchema); 
```

Enter fullscreen mode Exit fullscreen mode

我来简单描述一下上面的代码。在删除一个特定的角色之后，我立即使用 mongodb 的`$in`操作符找到了所有在角色数组中拥有该角色 id 的用户(mongoose 允许您在查询中使用原生的 mongodb [操作符](https://docs.mongodb.com/manual/reference/operator/))。

```
....
User.find({ roles: { $in: [roleId] } }) 
```

Enter fullscreen mode Exit fullscreen mode

然后，对于满足该标准的每个用户，使用另一个操作符,`$pull`操作符从他们的角色数组中“拉出”或删除特定的角色 id。

```
....
User.findOneAndUpdate(
  user._id,
  { $pull: { roles: roleId } },
  { new: true }
) 
```

Enter fullscreen mode Exit fullscreen mode

这是我用来在 mongoose 中实现`cascade delete`以确保引用完整性的方法。我很乐意听到你可以用其他方法来解决 mongoose 中的类似问题。

各位编码快乐！