# 在 Go 编程中使用 Sqlite。

> 原文：<https://dev.to/fevziomurtekin/using-sqlite-in-go-programming-3g2c>

[![alt text](img/9c09a0bd7ffea0f19711a3ba0b9c6c31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P3QZrytU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sav2nuu7iubw2qv2sxfa.png)

sqlite 是一个关系型 SQL 搜索引擎的开源代码。主机最重要的优点是服务器不需要本地服务器。

在这个例子中，我将使用 go 在 sqlite 上运行一个简单的例子。

## 用过的库

```
import (
  "database/sql"
  "fmt"
  "strconv"
  _ "github.com/mattn/go-sqlite3"
) 
```

Enter fullscreen mode Exit fullscreen mode

## 模型结构

```
type User struct {
  id         int
  username   string
  surname    string
  age        int
  university string
  //I created a struct with a struct to select the rows in the table and add data.
} 
```

Enter fullscreen mode Exit fullscreen mode

我使用的是由“id”、“用户名”、“姓氏”、“年龄”和“大学”组成的用户模型。

## CRUD 方法

我用 Go 编程语言用 Sqlite 数据库做过 CRUD 操作。

```
func addUser(db *sql.DB, username string, surname string, age int, university string) {
  tx, _ := db.Begin()
  stmt, _ := tx.Prepare("insert into testTable (username,surname,age,university) values (?,?,?,?)")
  _, err := stmt.Exec(username, surname, age, university)
  checkError(err)
  tx.Commit()
}
func getUsers(db *sql.DB, id2 int) User {
  rows, err := db.Query("select * from testTable")
  checkError(err)
  for rows.Next() {
    var tempUser User
    err =
      rows.Scan(&tempUser.id, &tempUser.username, &tempUser.surname, &tempUser.age, &tempUser.university)
    checkError(err)
    if tempUser.id == id2 {
      return tempUser
    }
  }
  return User{}
}
func updateUser(db *sql.DB, id2 int, username string, surname string, age int, university string) {
  sage := strconv.Itoa(age) // int to string
  sid := strconv.Itoa(id2)  // int to string
  tx, _ := db.Begin()
  stmt, _ := tx.Prepare("update testTable set username=?,surname=?,age=?,university=? where id=?")
  _, err := stmt.Exec(username, surname, sage, university, sid)
  checkError(err)
  tx.Commit()
}
func deleteUser(db *sql.DB, id2 int) {
  sid := strconv.Itoa(id2) // int to string
  tx, _ := db.Begin()
  stmt, _ := tx.Prepare("delete from testTable where id=?")
  _, err := stmt.Exec(sid)
  checkError(err)
  tx.Commit()
} 
```

Enter fullscreen mode Exit fullscreen mode

## 主要功能

```
db, _ := sql.Open("sqlite3", "database/godb.db")
  db.Exec("create table if not exists testTable (id integer,username text, surname text,age Integer,university text)") 
```

Enter fullscreen mode Exit fullscreen mode

我正在编写允许你连接到数据库并运行的代码。然后，我使用让我执行上述 CRUD 操作的方法。

```
func main() {
  db, _ := sql.Open("sqlite3", "database/godb.db")
  db.Exec("create table if not exists testTable (id integer,username text, surname text,age Integer,university text)")
  addUser(db, "fevzi omur ", "tekin", 24, "Sakarya University") // added data to database
  updateUser(db, 2, "Ken", "Thompson", 75, "California university") //update data to database
  deleteUser(db, 1) // delete data to database
  fmt.Println(getUsers(db, 2)) // printing the user
} 
```

Enter fullscreen mode Exit fullscreen mode

**注:**项目代码可在[这个地址找到。](https://github.com/fevziomurtekin/go-sqlite)