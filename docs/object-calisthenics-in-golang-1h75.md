# 格朗目标健身操

> 原文：<https://dev.to/eminetto/object-calisthenics-in-golang-1h75>

杰夫·贝在他的《T4 思想著作选集一书中引入了**物体健身操**这个术语。它是一组良好的实践和编程规则，可以提高我们代码的质量。

我第一次看到这些技术是在拉斐尔·多姆斯和 T2·古伊列梅·布兰科把它们从 Java 语言改编成 PHP 时。如果你用 PHP 编写代码，并且不知道对象健美操，我推荐他们做的两个演示:

[你的代码很烂，让我们来修复它](https://www.slideshare.net/rdohms/your-code-sucks-lets-fix-it-15471808)

[应用于 PHP 的对象健身操](https://www.slideshare.net/guilhermeblanco/object-calisthenics-applied-to-php)

但是这些规则到底是什么？在最初的版本中，它们是:

*   每个方法一级缩进。
*   不要使用 ELSE 关键字。
*   将所有原语和字符串包装在类中。
*   一流的收藏。
*   每行一个点。
*   不要缩写。
*   保持所有课程少于 50 行。
*   没有包含两个以上实例变量的类。
*   没有 getters 或 setters。

正如我前面提到的，Jeff 基于 Java 语言创建了这些规则，它们需要适应其他环境。正如拉斐尔和古伊列梅对 PHP 所做的那样，我们需要查看并分析每一项，看看它在 Go 中是否有意义。

首先要考虑的一点是名字本身。*健美操*来自希腊语，意为达到某一目的的一系列练习，如增强体质。在这种情况下，练习改善了我们代码的条件。问题在于*对象的*术语，因为这个概念在围棋中并不存在。因此，我建议第一个变化:从*实物健身操*到*代码健身操*。我在下面的评论区留下来讨论这是不是一个好的建议。

让我们分析一下其他项目。

## 每种方法一级缩进

应用这条规则可以让我们的代码更具可读性。

让我们将规则应用于代码:

```
package chess

import "bytes"

type board struct {
    data [][]string
}

func NewBoard(data [][]string) *board {
    return &board{data: data}
}

func (b *board) Board() string {
    var buffer = &bytes.Buffer{}

    // level 0
    for i := 0; i < 10; i++ {
        // level 1
        for j := 0; j < 10; j++ {
            // level 2
            buffer.WriteString(b.data[i][j])
        }
        buffer.WriteString("\n")
    }

    return buffer.String()
} 
```

Enter fullscreen mode Exit fullscreen mode

一个更具可读性的结果是:

```
package chess

import "bytes"

type board struct {
    data [][]string
}

func NewBoard(data [][]string) *board {
    return &board{data: data}
}

func (b *board) Board() string {
    var buffer = &bytes.Buffer{}

    b.collectRows(buffer)

    return buffer.String()
}

func (b *board) collectRows(buffer *bytes.Buffer) {
    for i := 0; i < 10; i++ {
        b.collectRow(buffer, i)
    }
}

func (b *board) collectRow(buffer *bytes.Buffer, row int) {
    for j := 0; j < 10; j++ {
        buffer.WriteString(b.data[row][j])
    }

    buffer.WriteString("\n")
} 
```

Enter fullscreen mode Exit fullscreen mode

## 不要使用 ELSE 关键字

这一项的目标是避免使用 *else* 关键字，生成更干净、更快的代码，因为它的执行流程更少。

我们来看代码:

```
package login

import (
    "github.com/user/project/user/repository"
)

type loginService struct {
    userRepository *repository.UserRepository
}

func NewLoginService() *loginService {
    return &loginService{
        userRepository: repository.NewUserRepository(),
    }
}

func (l *loginService) Login(userName, password string) {
    if l.userRepository.IsValid(userName, password) {
        redirect("homepage")
    } else {
        addFlash("error", "Bad credentials")

        redirect("login")
    }
}

func redirect(page string) {
    // redirect to given page
}

func addFlash(msgType, msg string) {
    // create flash message
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以应用*提前返回*的概念，从函数*登录* :
中移除 *else*

```
 func (l *loginService) Login(userName, password string) {
    if l.userRepository.IsValid(userName, password) {
        redirect("homepage")
        return
    }
    addFlash("error", "Bad credentials")
    redirect("login")
} 
```

Enter fullscreen mode Exit fullscreen mode

## 将所有原语和字符串包装在类中

这条规则表明具有行为的原始类型**必须被封装，在我们的例子中，在*结构*或*类型*中，而不是在*类*中。这样，我们封装了行为逻辑，使代码易于维护。我们来看例子:** 

```
package ecommerce

type order struct {
    pid int64
    cid int64
}

func CreateOrder(pid int64, cid int64) order {
    return order{
        pid: pid, cid: cid,
    }
}

func (o order) Submit() (int64, error) {
    // do some logic

    return int64(3252345234), nil
} 
```

Enter fullscreen mode Exit fullscreen mode

应用规则，使用*结构* :

```
package ecommerce

import (
    "strconv"
)

type order struct {
    pid productID
    cid customerID
}

type productID struct {
    id int64
}

// some methods on productID struct

type customerID struct {
    id int64
}

// some methods on customerID struct

type orderID struct {
    id int64
}

func (oid orderID) String() string {
    return strconv.FormatInt(oid.id, 10)
}

// some other methods on orderID struct

func CreateOrder(pid int64, cid int64) order {
    return order{
        pid: productID{pid}, cid: customerID{cid},
    }
}

func (o order) Submit() (orderID, error) {
    // do some logic

    return orderId{int64(3252345234)}, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

使用*类型*的另一种可能且更惯用的重构可能是::

```
package ecommerce

import (
    "strconv"
)

type order struct {
    pid productID
    cid customerID
}

type productID int64

// some methods on productID type

type customerID int64

// some methods on customerID type

type orderID int64

func (oid orderID) String() string {
    return strconv.FormatInt(int64(oid), 10)
}

// some other methods on orderID type

func CreateOrder(pid int64, cid int64) order {
    return order{
        pid: productID(pid), cid: customerID(cid),
    }
}

func (o order) Submit() (orderID, error) {
    // do some logic

    return orderID(int64(3252345234)), nil
} 
```

Enter fullscreen mode Exit fullscreen mode

除了可读性之外，新代码还允许业务规则的简单发展，以及与我们所操作的内容相关的更高的安全性。

## 头等收藏品

如果您有一组元素并且想要操作它们，那么为该集合创建一个专用的结构。因此，我们可以在结构中实现与集合相关的所有行为，比如过滤器、验证规则等等。

所以，代码:

```
package contact

import "fmt"

type person struct {
    name    string
    friends []string
}

type friend struct {
    name string
}

func NewPerson(name string) *person {
    return &person{
        name:    name,
        friends: []string{},
    }
}

func (p *person) AddFriend(name string) {
    p.friends = append(p.friends, name)
}

func (p *person) RemoveFriend(name string) {
    new := []string{}
    for _, friend := range p.friends {
        if friend != name {
            new = append(new, friend)
        }
    }
    p.friends = new
}

func (p *person) GetFriends() []string {
    return p.friends
}

func (p *person) String() string {
    return fmt.Sprintf("%s %v", p.name, p.friends)
} 
```

Enter fullscreen mode Exit fullscreen mode

可以重构为:

```
package contact

import (
    "strings"
    "fmt"
)

type friends struct {
    data []string
}

type person struct {
    name    string
    friends *friends
}

func NewFriends() *friends {
    return &friends{
        data: []string{},
    }
}

func (f *friends) Add(name string) {
    f.data = append(f.data, name)
}

func (f *friends) Remove(name string) {
    new := []string{}
    for _, friend := range f.data {
        if friend != name {
            new = append(new, friend)
        }
    }
    f.data = new
}

func (f *friends) String() string {
    return strings.Join(f.data, " ")
}

func NewPerson(name string) *person {
    return &person{
        name:    name,
        friends: NewFriends(),
    }
}

func (p *person) AddFriend(name string) {
    p.friends.Add(name)
}

func (p *person) RemoveFriend(name string) {
    p.friends.Remove(name)
}

func (p *person) GetFriends() *friends {
    return p.friends
}

func (p *person) String() string {
    return fmt.Sprintf("%s [%v]", p.name, p.friends)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 每行一个点

这条规则规定您不应该链接函数，而应该只使用那些属于同一上下文的函数。

示例:

```
package chess

type piece struct {
    representation string
}

type location struct {
    current *piece
}

type board struct {
    locations []*location
}

func NewLocation(piece *piece) *location {
    return &location{current: piece}
}

func NewPiece(representation string) *piece {
    return &piece{representation: representation}
}

func NewBoard() *board {
    locations := []*location{
        NewLocation(NewPiece("London")),
        NewLocation(NewPiece("New York")),
        NewLocation(NewPiece("Dubai")),
    }
    return &board{
        locations: locations,
    }
}

func (b *board) squares() []*location {
    return b.locations
}

func (b *board) BoardRepresentation() string {
    var buffer = &bytes.Buffer{}
    for _, l := range b.squares() {
        buffer.WriteString(l.current.representation[0:1])
    }
    return buffer.String()
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将重构为:

```
package chess

import "bytes"

type piece struct {
    representation string
}

type location struct {
    current *piece
}

type board struct {
    locations []*location
}

func NewPiece(representation string) *piece {
    return &piece{representation: representation}
}

func (p *piece) character() string {
    return p.representation[0:1]
}

func (p *piece) addTo(buffer *bytes.Buffer) {
    buffer.WriteString(p.character())
}

func NewLocation(piece *piece) *location {
    return &location{current: piece}
}

func (l *location) addTo(buffer *bytes.Buffer) {
    l.current.addTo(buffer)
}

func NewBoard() *board {
    locations := []*location{
        NewLocation(NewPiece("London")),
        NewLocation(NewPiece("New York")),
        NewLocation(NewPiece("Dubai")),
    }
    return &board{
        locations: locations,
    }
}

func (b *board) squares() []*location {
    return b.locations
}

func (b *board) BoardRepresentation() string {
    var buffer = &bytes.Buffer{}
    for _, l := range b.squares() {
        l.addTo(buffer)
    }
    return buffer.String()
} 
```

Enter fullscreen mode Exit fullscreen mode

这个规则强化了 [*【得墨忒耳定律】*](http://wiki.c2.com/?LawOfDemeter) 的用法:

> 只和你最亲近的朋友交谈

## 不要缩写

这条规则不适用于围棋。社区有自己的创建变量名的规则，包括使用较小变量名的原因。我推荐阅读这一章: [*‌Practical 围棋:编写可维护围棋程序的真实世界建议*](https://dave.cheney.net/practical-go/presentations/qcon-china.html?utm_campaign=Golang%20Ninjas%20Newsletter&utm_medium=email&utm_source=Revue%20newsletter#_identifiers)

## 保持所有类少于 50 行

虽然 Go 中没有*类*的概念，但是这条规则规定实体要小。我们可以修改这个想法来创建小的*结构*和*接口*，我们可以通过组合使用它们来形成更大的组件。例如，*接口* :

```
type Repository interface {
    Find(id entity.ID) (*entity.User, error)
    FindByEmail(email string) (*entity.User, error)
    FindByChangePasswordHash(hash string) (*entity.User, error)
    FindByValidationHash(hash string) (*entity.User, error)
    FindByChallengeSubmissionHash(hash string) (*entity.User, error)
    FindByNickname(nickname string) (*entity.User, error)
    FindAll() ([]*entity.User, error)
    Update(user *entity.User) error
    Store(user *entity.User) (entity.ID, error)
    Remove(id entity.ID) error
} 
```

Enter fullscreen mode Exit fullscreen mode

可以适应::

```
type Reader interface {
    Find(id entity.ID) (*entity.User, error)
    FindByEmail(email string) (*entity.User, error)
    FindByChangePasswordHash(hash string) (*entity.User, error)
    FindByValidationHash(hash string) (*entity.User, error)
    FindByChallengeSubmissionHash(hash string) (*entity.User, error)
    FindByNickname(nickname string) (*entity.User, error)
    FindAll() ([]*entity.User, error)
}

type Writer interface {
    Update(user *entity.User) error
    Store(user *entity.User) (entity.ID, error)
    Remove(id entity.ID) error
}

type Repository interface {
    Reader
    Writer
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，其他接口和场景可以重用*阅读器*和*编写器*。

## 没有包含两个以上实例变量的类

这个规则在围棋中似乎没有什么意义，但如果大家有什么建议，请分享。

## 没有 getter/setter

像前面的规则一样，这似乎也不适用于 Go，因为它不是社区使用的模式，正如在有效 Go 的这个[主题](https://golang.org/doc/effective_go.html#Getters)中所看到的。但是欢迎建议。

应用这些规则，以及其他好的实践，有可能得到一个更干净、简单和易于维护的代码。我很想听听大家对规则的看法，以及对 Go 改编的这些建议。

## 参考文献

[https://javflores.github.io/object-calisthenics/](https://javflores.github.io/object-calisthenics/)

[https://williamdurand.fr/2013/06/03/object-calisthenics/](https://williamdurand.fr/2013/06/03/object-calisthenics/)

[https://medium . com/we b-engineering-vox/improving-code-quality-with-object-alcanthemics-aa 4a d67 a 61 f 1](https://medium.com/web-engineering-vox/improving-code-quality-with-object-calisthenics-aa4ad67a61f1)

我从知识库中改编了一些本文中使用的例子:[https://github.com/rafos/object-calisthenics-in-go](https://github.com/rafos/object-calisthenics-in-go)

## 致谢

感谢 Wagner Riffel 和 Francisco Oliveira 对如何改进示例的建议。