# 如何在 golang 应用程序中测试数据库交互

> 原文：<https://dev.to/ale_ukr/how-to-test-database-interactions-in-golang-applications-3041>

测试与数据库交互的功能总是具有挑战性。最近，我发现了一个很棒的库 [go-sqlmock](https://github.com/DATA-DOG/go-sqlmock) ,它将大大简化在 golang 应用程序中编写测试和模拟数据库查询的工作。我想分享一个简短的例子来说明如何使用它。

首先，我们必须安装它

`go get github.com/DATA-DOG/go-sqlmock`

我们用 SQL 查询这个函数:

```
func MenuByNameAndLanguage(ctx context.Context, db *sql.DB, name string, langcode string) (*models.Menu, error) {
    result, err := db.Query("SELECT id, langcode, title, link__uri, view_sidemenu FROM menu_link_content_data WHERE menu_name=? AND langcode=?",
         name,
         langcode,
    )
    defer result.Close()

    var menuLinks []models.MenuLink
    for result.Next() {
         menuLink := models.MenuLink{}
         err = result.Scan(&menuLink.ID, &menuLink.Langcode, &menuLink.Title, &menuLink.URL, &menuLink.SideMenu)
         menuLinks = append(menuLinks, menuLink)    
    }
    menu := &models.Menu{
         Name: name,
         Links: menuLinks,
    }
    return menu, err
} 
```

这个函数只是通过菜单名称和语言获得菜单链接。

现在我们来测试一下。

我们将测试 MenuByNameAndLanguage 函数是否会返回正确的菜单结构。

```
package menu

import (
    "context"
    "testing"

    "github.com/DATA-DOG/go-sqlmock"
    "github.com/stretchr/testify/assert"
    "gitlab.mfb.io/user/graphql_server/models"
)

func TestShouldReturnCorrectMenu(t *testing.T) {

    // Creates sqlmock database connection and a mock to manage expectations.
    db, mock, err := sqlmock.New()

    if err != nil {
        t.Fatalf("an error '%s' was not expected when opening a stub database connection", err)
    }
    // Closes the database and prevents new queries from starting.
    defer db.Close()

    // Here we are creating rows in our mocked database.
    rows := sqlmock.NewRows([]string{"id", "langcode", "title", "link__uri", "view_sidemenu"}).
        AddRow(1, "en", "enTitle", "/en-link", "0").
        AddRow(2, "en", "enTitle2", "/en-link2", "0")

    // This is most important part in our test. Here, literally, we are altering SQL query from MenuByNameAndLanguage
﻿    // function and replacing result with our expected result. 
    mock.ExpectQuery("^SELECT (.+) FROM menu_link_content_data*").
        WithArgs("main", "en").
        WillReturnRows(rows)

    ctx := context.TODO()

    // Calls MenuByNameAndLanguage with mocked database connection in arguments list. 
    menu, err := MenuByNameAndLanguage(ctx, db, "main", "en")

    // Here we just construction our expecting result.
    var menuLinks []models.MenuLink
    menuLink1 := models.MenuLink{
        ID:       1,
        Title:    "enTitle",
        Langcode: "en",
        URL:      "/en-link",
        SideMenu: "0",
    }
    menuLinks = append(menuLinks, menuLink1)

    menuLink2 := models.MenuLink{
        ID:       2,
        Title:    "enTitle2",
        Langcode: "en",
        URL:      "/en-link2",
        SideMenu: "0",
    }

    menuLinks = append(menuLinks, menuLink2)

    expectedMenu := &models.Menu{
        Name:  "main",
        Links: menuLinks,
    }

    // And, finally, let's check if result from MenuByNameAndLanguage equal with expected result.// Here I used Testify library (https://github.com/stretchr/testify).
    assert.Equal(t, expectedMenu, menu)
} 
```

如你所见，这个例子中的一切都非常简单。

生产订单详情可参考 [GoDocs](https://godoc.org/github.com/DATA-DOG/go-sqlmock) 。