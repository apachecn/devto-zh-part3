# 在 Golang 测试中使用模拟

> 原文：<https://dev.to/rafaacioly/mocking-tests-with-golang-45d3>

这些天我在学习 golang，需要写一些测试。我是 golang 的新人，对测试之类的东西不熟悉，所以对我来说有点“难”,这篇文章是为了让你更容易理解，因为我希望它对我来说也很容易，我将介绍使用主要 golang 结构(接口)用 sql mock 编写测试的基本原则，以保持简单。

> 我想你已经熟悉 golang 了

## 戈朗接口

如果你来自类似于`java`、`PHP`或任何其他 OOP 语言的语言，你必须熟悉`interfaces`，简而言之，一个接口就是一个`contract`，用来保证一个特定的类实现确定的方法/属性。
`Go`也有接口，写起来很简单:

```
type repository interface {} 
```

我将使用`go`中内置的`Error`接口作为例子，我们知道`go`中的`errors`是一种类型，或者像一些人所说的一等公民。我们可以返回一个错误，创建一个新类型的错误，将错误作为参数传递，将错误存储在变量中，等等...[检查错误接口](https://golang.org/pkg/builtin/#error)
如你所见`Error`接口只需要一个名为 error 的返回字符串的方法，所以如果我们这样做；

```
type MyCustomError struct {
    message string
}

func (e MyCustomError) Error() string {
    return e.message
} 
```

我们可以使用`MyCustomError`作为错误类型。

```
 func myMethod() error {
    return MyCustomError{message: "my custom error"}
} 
```

你可以看到我们的`myMethod`声明这个方法的返回是一个内置的错误类型，这个方法返回一个`MyCustomError`，这是可能的，因为`MyCustomError`实现了`Error`接口所需的所有方法，这样我们就可以说`MyCustomError`是一个错误类型。

这是编写 mocks 的主要原则。在`go`中，我们创建了一个自定义结构，它实现了所需的方法，这些方法提供了特定资源的所有方法。

我们不会为`sql.DB` 编写[一个完整的结构，感谢~~神~~ `DATA-DOG`我们已经有了](https://golang.org/src/database/sql/sql.go?s=9508:11278#L339)[包来完成这项工作](https://github.com/DATA-DOG/go-sqlmock)

## 动起手来

> 在下面的例子中，我会在代码中加入一些`TODOS`，这样你就可以看到缺少了什么，最后我会给你一个挑战，祝你好运。

好了，知道我们对接口/结构有所了解，让我们开始`mocks`吧，首先用这些文件创建一个新项目:`database.go`和`test_database.go`

`database.go`文件:

```
package database

import "database/sql"

type Repository interface {
    Find(id int) error
}

type repository struct {
    // the db field is of type `DB` from the `database` package
    // that provides the method to interact with our real database
    db *sql.DB
}

func NewRepository(db *sql.DB) Repository {
    return &repository{db: db}
}

// Find retrieves a single record from database
// based on id column
func (r repository) Find(id int) error {} 
```

**备注**:
`NewRepository`方法声明将返回一个`Repository`接口，并接受`sql.DB` struct 作为参数，这个接口只是为了学习 propose，我们在这个例子中不用写。

让我们从`Find`方法开始，在你的`database_test`文件中编写下面的代码:

```
package database

import (
    "gopkg.in/DATA-DOG/go-sqlmock.v1"
    "testing"
)

func TestFindDatabaseRecord(t *testing.T) {
    // the db satisfy the sql.DB struct
    db, mock, err := sqlmock.New()
    if err != nil {
        t.Fatalf("an error '%s' was not expected when opening a stub database connection", err)
    }
    defer db.Close()

    // our sql.DB must exec the follow query
    mock.ExpectQuery("SELECT \\* FROM my_table").
        // the number 3 must be on the query like "where id = 3"
        WithArgs(3)
        // TODO: define the values that need to be returned from the database

    myDB := NewRepository(db) // passes the mock to our code

    // run the code with the database mock
    if err = myDB.Find(3); err != nil {
        t.Errorf("something went wrong: %s", err.Error())
    }
} 
```

**Resume** :
上面的代码创建了一个`sql.DB` struct 的 mock，并将其作为参数传递给我们的代码，mock 表示查询`SELECT * FROM my_table`必须使用参数`3`来执行，`DATA-DOG`包提供了许多方法，如`ExpectExec`、`WillReturnResult`和`ExpectRollback`，该包足够智能，可以知道何时运行特定的方法，并在需要时比较您的预期。

`Find`方法:

```
 func (r repository) Find(id int) error {
    // r is the reference to our struct and db is the mock that we passed
    stmt, err := r.db.Prepare("SELECT * from my_table WHERE id = $1")
    if err != nil {
        return err
    }
    defer stmt.Close()

    // change the placeholder $1 to use the id parameter
    _, err = stmt.Exec(id) // should be result, err = stmt.Exec(id)
    if err != nil {
        return err
    }

    // TODO: we need to fill a struct with the database result
    // and return it with nil

    return nil // should be mystruct, nil
} 
```

你已经注意到,`Find`方法不返回任何数据库信息，这个代码的例子与你实现它的目的非常相似，你能处理这个吗？

你现在要做的是:

1.  返回关于`Find`方法的数据以及错误
2.  比较返回值

**提示**:

*   看看[法`NewRows`](https://godoc.org/github.com/DATA-DOG/go-sqlmock#NewRows) 和 [`WillReturnRows`](https://godoc.org/github.com/DATA-DOG/go-sqlmock#ExpectedQuery.WillReturnRows) 。
*   创建一个结构来保存数据库值，并在`Find`方法上返回这个结构

当你完成了和我分享你所做的！:)

谢谢你读到这里，如果你有任何意见，问题或建议，我很乐意谈谈。