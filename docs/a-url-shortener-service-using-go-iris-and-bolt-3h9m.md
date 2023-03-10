# 使用 Go、Iris 和 Bolt 的 URL 缩写服务

> 原文：<https://dev.to/kataras/a-url-shortener-service-using-go-iris-and-bolt-3h9m>

# 使用 Go、Iris 和 Bolt 的网址缩写服务

如果你关注像 Twitter 和 Google+这样的社交媒体网站，你会注意到我们没有使用完整的网址，而是使用一个友好的缩写，比如 t.co/HurCcP0nn9 的[和 bit.ly/iriscandothat1 的](https://t.co/HurCcP0nn9)。

在自己的域名中拥有自己的缩写网址不是很好吗？

我们先来复习一下基础。

## 工具

编程语言对我们来说只是工具，但我们需要一种安全、快速且“跨平台”的编程语言来为我们的服务提供动力。

[Go](https://golang.org/) 是一种[快速发展的](http://www.tiobe.com/tiobe-index/)开源编程语言，旨在构建简单、快速、可靠的软件。看看[这里](https://github.com/golang/go/wiki/GoUsers)哪些伟大的公司使用 Go 来加强他们的服务。

### 安装 Go 编程语言

关于下载和安装 Go 的详细信息可以在[这里](https://golang.org/dl/)找到。

[https://www.youtube.com/embed/9x-pG3lvLi0](https://www.youtube.com/embed/9x-pG3lvLi0)

> 这篇文章不包含对语言本身的介绍，如果你是一个新手，我建议你把这篇文章收藏起来，[学习](https://github.com/golang/go/wiki/Learn)这门语言的基础知识，以后再回来。

### 从属关系

在过去，已经有很多文章引导用户不要使用 web 框架，因为它们“不好”。我必须告诉你，没有这样的事情，它总是取决于你将要使用的(web)框架。在生产层面，通常，我们没有时间为大公司的应用程序编写所有需要的代码。简而言之:**好的框架对任何开发者、公司或初创公司都是有用的工具，坏的框架是浪费时间，简单明了**。

您只需要两个必需的依赖项和一个可选的依赖项:

*   Iris，我们的网络框架

*   [Bolt(现在的 bbolt)](https://github.com/etcd-io/bbolt) ，一个嵌入式的键/值数据库

*   [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) 库，将帮助我们生成短网址

使用 go 软件包安装是一项简单的任务，只需打开您的终端并执行以下命令:

```
$ go get github.com/kataras/iris/v12@latest
$ go get -u github.com/etcd-io/bbolt
$ go get -u github.com/satori/go.uuid 
```

Enter fullscreen mode Exit fullscreen mode

## 最好的部分

很好，如果我们都在同一个页面上，那么是时候学习如何创建一个 URL Shortener server 了，它将易于部署并且可以扩展得更多！

为了创建一个短链接，我们生成一个随机字符串，并使用 Bolt 存储原始 URL，并将我们的随机字符串作为键。当一个“GET”请求被发送到缩短的 URL 时，我们从 Bolt 中检索原始的 URL。如果这样的值存在，我们重定向，否则我们用相应的消息响应。

> 为了简单起见，我们假设项目位于*$ GOPATH/src/you/shortener *目录，包名为*main* 。

我们的**前端**是荒谬的简单，它只包含一个索引页面和它的“风格”，模板位于。/templates 文件夹和样式。/resources/css 文件夹。

```
<html>

<head>
    <meta charset="utf-8">
    Golang URL Shortener
    <link rel="stylesheet" href="/static/css/style.css" />
</head>

<body>
    <h2>Golang URL Shortener</h2>
    <h3>{{ .FORM_RESULT}}</h3>
    <form action="/shorten" method="POST">
        <input type="text" name="url" style="width: 35em;" />
        <input type="submit" value="Shorten!" />
    </form>
    {{ if IsPositive .URL_COUNT }}
        <p>{{ .URL_COUNT }} URLs shortened</p>
    {{ end }}

    <form action="/clear_cache" method="POST">
        <input type="submit" value="Clear DB" />
    </form>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
body{
    background-color:silver;
} 
```

Enter fullscreen mode Exit fullscreen mode

直接转到* *数据库* *级别，我们将创建一个简单的实现，它将能够* *保存* *一个缩短的 URL(密钥)及其原始/完整的 URL(值)，* *根据密钥检索* *一个完整的 URL，并将所有已注册 URL 的总数返回到数据库。

```
 package main

import (
    "bytes"

    "github.com/etcd-io/bbolt"
)

// Panic panics, change it if you don't want to panic on critical INITIALIZE-ONLY-ERRORS
var Panic = func(v interface{}) {
    panic(v)
}

// Store is the store interface for urls.
// Note: no Del functionality.
type Store interface {
    Set(key string, value string) error // error if something went wrong
    Get(key string) string              // empty value if not found
    Len() int                           // should return the number of all the records/tables/buckets
    Close()                             // release the store or ignore
}

var (
    tableURLs = []byte("urls")
)

// DB representation of a Store.
// Only one table/bucket which contains the urls, so it's not a fully Database,
// it works only with single bucket because that all we need.
type DB struct {
    db *bbolt.DB
}

var _ Store = &DB{}

// openDatabase open a new database connection
// and returns its instance.
func openDatabase(stumb string) *bbolt.DB {
    // Open the data(base) file in the current working directory.
    // It will be created if it doesn't exist.
    db, err := bbolt.Open(stumb, 0600, nil)
    if err != nil {
        Panic(err)
    }

    // create the buckets here
    var tables = [...][]byte{
        tableURLs,
    }

    db.Update(func(tx *bbolt.Tx) (err error) {
        for _, table := range tables {
            _, err = tx.CreateBucketIfNotExists(table)
            if err != nil {
                Panic(err)
            }
        }

        return
    })

    return db
}

// NewDB returns a new DB instance, its connection is opened.
// DB implements the Store.
func NewDB(stumb string) *DB {
    return &DB{
        db: openDatabase(stumb),
    }
}

// Set sets a shorten url and its key
// Note: Caller is responsible to generate a key.
func (d *DB) Set(key string, value string) error {
    return d.db.Update(func(tx *bbolt.Tx) error {
        b, err := tx.CreateBucketIfNotExists(tableURLs)
        // Generate ID for the url
        // Note: we could use that instead of a random string key
        // but we want to simulate a real-world url shortener
        // so we skip that.
        // id, _ := b.NextSequence()
        if err != nil {
            return err
        }

        k := []byte(key)
        valueB := []byte(value)
        c := b.Cursor()

        found := false
        for k, v := c.First(); k != nil; k, v = c.Next() {
            if bytes.Equal(valueB, v) {
                found = true
                break
            }
        }
        // if value already exists don't re-put it.
        if found {
            return nil
        }

        return b.Put(k, []byte(value))
    })
}

// Clear clears all the database entries for the table urls.
func (d *DB) Clear() error {
    return d.db.Update(func(tx *bbolt.Tx) error {
        return tx.DeleteBucket(tableURLs)
    })
}

// Get returns a url by its key.
//
// Returns an empty string if not found.
func (d *DB) Get(key string) (value string) {
    keyB := []byte(key)
    d.db.Update(func(tx *bbolt.Tx) error {
        b := tx.Bucket(tableURLs)
        if b == nil {
            return nil
        }
        c := b.Cursor()
        for k, v := c.First(); k != nil; k, v = c.Next() {
            if bytes.Equal(keyB, k) {
                value = string(v)
                break
            }
        }

        return nil
    })

    return
}

// GetByValue returns all keys for a specific (original) url value.
func (d *DB) GetByValue(value string) (keys []string) {
    valueB := []byte(value)
    d.db.Update(func(tx *bbolt.Tx) error {
        b := tx.Bucket(tableURLs)
        if b == nil {
            return nil
        }
        c := b.Cursor()
        // first for the bucket's table "urls"
        for k, v := c.First(); k != nil; k, v = c.Next() {
            if bytes.Equal(valueB, v) {
                keys = append(keys, string(k))
            }
        }

        return nil
    })

    return
}

// Len returns all the "shorted" urls length
func (d *DB) Len() (num int) {
    d.db.View(func(tx *bbolt.Tx) error {

        // Assume bucket exists and has keys
        b := tx.Bucket(tableURLs)
        if b == nil {
            return nil
        }

        b.ForEach(func([]byte, []byte) error {
            num++
            return nil
        })
        return nil
    })
    return
}

// Close shutdowns the data(base) connection.
func (d *DB) Close() {
    if err := d.db.Close(); err != nil {
        Panic(err)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们创建我们的**工厂**，它将**为我们生成**缩短的 URL！

```
package main

import (
    "net/url"

    "github.com/satori/go.uuid"
)

// Generator the type to generate keys(short urls)
type Generator func() string

// DefaultGenerator is the defautl url generator
var DefaultGenerator = func() string {
    id, _ := uuid.NewV4()
    return id.String()
}

// Factory is responsible to generate keys(short urls)
type Factory struct {
    store     Store
    generator Generator
}

// NewFactory receives a generator and a store and returns a new url Factory.
func NewFactory(generator Generator, store Store) *Factory {
    return &Factory{
        store:     store,
        generator: generator,
    }
}

// Gen generates the key.
func (f *Factory) Gen(uri string) (key string, err error) {
    // we don't return the parsed url because #hash are converted to uri-compatible
    // and we don't want to encode/decode all the time, there is no need for that,
    // we save the url as the user expects if the uri validation passed.
    _, err = url.ParseRequestURI(uri)
    if err != nil {
        return "", err
    }

    key = f.generator()
    // Make sure that the key is unique
    for {
        if v := f.store.Get(key); v == "" {
            break
        }
        key = f.generator()
    }

    return key, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

我们应该创建一个**主文件**，它将连接并组合所有组件来运行一个 http 服务器，该服务器将为我们的小型 URL Shortener 服务提供服务。

```
package main

import (
    "html/template"

    "github.com/kataras/iris/v12"
)

func main() {
    // assign a variable to the DB so we can use its features later.
    db := NewDB("shortener.db")
    // Pass that db to our app, in order to be able to test the whole app with a different database later on.
    app := newApp(db)

    // release the "db" connection when server goes off.
    iris.RegisterOnInterrupt(db.Close)

    app.Run(iris.Addr(":8080"))
}

func newApp(db *DB) *iris.Application {
    app := iris.Default() // or app := iris.New()

    // create our factory, which is the manager for the object creation.
    // between our web app and the db.
    factory := NewFactory(DefaultGenerator, db)

    // serve the "./templates" directory's "*.html" files with the HTML std view engine.
    tmpl := iris.HTML("./templates", ".html").Reload(true)
    // register any template func(s) here.
    //
    // Look ./templates/index.html#L16
    tmpl.AddFunc("IsPositive", func(n int) bool {
        if n > 0 {
            return true
        }
        return false
    })

    app.RegisterView(tmpl)

    // Serve static files (css)
    app.StaticWeb("/static", "./resources")

    indexHandler := func(ctx iris.Context) {
        ctx.ViewData("URL_COUNT", db.Len())
        ctx.View("index.html")
    }
    app.Get("/", indexHandler)

    // find and execute a short url by its key
    // used on http://localhost:8080/u/dsaoj41u321dsa
    execShortURL := func(ctx iris.Context, key string) {
        if key == "" {
            ctx.StatusCode(iris.StatusBadRequest)
            return
        }

        value := db.Get(key)
        if value == "" {
            ctx.StatusCode(iris.StatusNotFound)
            ctx.Writef("Short URL for key: '%s' not found", key)
            return
        }

        ctx.Redirect(value, iris.StatusTemporaryRedirect)
    }
    app.Get("/u/{shortkey}", func(ctx iris.Context) {
        execShortURL(ctx, ctx.Params().Get("shortkey"))
    })

    app.Post("/shorten", func(ctx iris.Context) {
        formValue := ctx.FormValue("url")
        if formValue == "" {
            ctx.ViewData("FORM_RESULT", "You need to a enter a URL")
            ctx.StatusCode(iris.StatusLengthRequired)
        } else {
            key, err := factory.Gen(formValue)
            if err != nil {
                ctx.ViewData("FORM_RESULT", "Invalid URL")
                ctx.StatusCode(iris.StatusBadRequest)
            } else {
                if err = db.Set(key, formValue); err != nil {
                    ctx.ViewData("FORM_RESULT", "Internal error while saving the URL")
                    app.Logger().Infof("while saving URL: " + err.Error())
                    ctx.StatusCode(iris.StatusInternalServerError)
                } else {
                    ctx.StatusCode(iris.StatusOK)
                    shortenURL := "http://" + app.ConfigurationReadOnly().GetVHost() + "/u/" + key
                    ctx.ViewData("FORM_RESULT",
                        template.HTML("<pre><a target='_new' href='"+shortenURL+"'>"+shortenURL+" </a></pre>"))
                }

            }
        }

        indexHandler(ctx) // no redirect, we need the FORM_RESULT.
    })

    app.Post("/clear_cache", func(ctx iris.Context) {
        db.Clear()
        ctx.Redirect("/")
    })

    return app
} 
```

Enter fullscreen mode Exit fullscreen mode

最后…让我们构建并运行我们的应用程序！

```
$ cd $GOPATH/src/github.com/myname/url-shortener
$ go build # build 
$ ./url-shortener # run 
```

Enter fullscreen mode Exit fullscreen mode

## **奖金**部分

测试我们的应用程序总是一件有用的事情，因此我会给你第一个想法，告诉你如何用 Iris 测试这些类型的网络应用程序，直接到下面的代码。

```
package main

import (
    "io/ioutil"
    "os"
    "testing"
    "time"

    "github.com/kataras/iris/v12/httptest"
)

// TestURLShortener tests the simple tasks of our url shortener application.
// Note that it's a pure test.
// The rest possible checks is up to you, take it as as an exercise!
func TestURLShortener(t *testing.T) {
    // temp db file
    f, err := ioutil.TempFile("", "shortener")
    if err != nil {
        t.Fatalf("creating temp file for database failed: %v", err)
    }

    db := NewDB(f.Name())
    app := newApp(db)

    e := httptest.New(t, app)
    originalURL := "https://google.com"

    // save
    e.POST("/shorten").
        WithFormField("url", originalURL).Expect().
        Status(httptest.StatusOK).Body().Contains("<pre><a target='_new' href=")

    keys := db.GetByValue(originalURL)
    if got := len(keys); got != 1 {
        t.Fatalf("expected to have 1 key but saved %d short urls", got)
    }

    // get
    e.GET("/u/" + keys[0]).Expect().
        Status(httptest.StatusTemporaryRedirect).Header("Location").Equal(originalURL)

    // save the same again, it should add a new key
    e.POST("/shorten").
        WithFormField("url", originalURL).Expect().
        Status(httptest.StatusOK).Body().Contains("<pre><a target='_new' href=")

    keys2 := db.GetByValue(originalURL)
    if got := len(keys2); got != 1 {
        t.Fatalf("expected to have 1 keys even if we save the same original url but saved %d short urls", got)
    } // the key is the same, so only the first one matters.

    if keys[0] != keys2[0] {
        t.Fatalf("expected keys to be equal if the original url is the same, but got %s = %s ", keys[0], keys2[0])
    }

    // clear db
    e.POST("/clear_cache").Expect().Status(httptest.StatusOK)
    if got := db.Len(); got != 0 {
        t.Fatalf("expected database to have 0 registered objects after /clear_cache but has %d", got)
    }

    // give it some time to release the db connection
    db.Close()
    time.Sleep(1 * time.Second)
    // close the file
    if err := f.Close(); err != nil {
        t.Fatalf("unable to close the file: %s", f.Name())
    }

    // and remove the file
    if err := os.Remove(f.Name()); err != nil {
        t.Fatalf("unable to remove the file from %s", f.Name())
    }

    time.Sleep(1 * time.Second)

} 
```

Enter fullscreen mode Exit fullscreen mode

运行测试

```
$ cd $GOPATH/src/github.com/myname/url-shortener
$ go test -v # test 
```

Enter fullscreen mode Exit fullscreen mode

## 就这样，挺简单的。对吗？

分享你对这篇文章的想法，让我知道你打算用 Go + Iris 构建什么样的应用程序！

**感谢您花时间阅读整篇文章，我很佩服您的耐心:)**

> 完整的源代码位于[这里](https://github.com/kataras/iris/tree/v12/_examples/tutorial/url-shortener)。
> 如果您有任何进一步的问题，请随时在下面留下您的评论，或者在这里提问[！](https://chat.iris-go.com)

## 再次感谢

别忘了看看我的媒体简介和 twitter，我也在那里发布了一些东西:)

*   [https://medium.com/@kataras](https://medium.com/@kataras)
*   [https://twitter.com/MakisMaropoulos](https://twitter.com/MakisMaropoulos)