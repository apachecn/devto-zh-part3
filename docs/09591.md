# 基于角色的授权和视图

> 原文：<https://dev.to/mxoliver/role-based-authorization-and-the-view-1mnj>

在过去的几周里，我一直致力于使用名为 Wikology 的 Node 构建一个客户端维基百科式的 web 应用程序。在维基学中，所有标准成员都可以创建、编辑和阅读公共维基。用户还可以升级到高级帐户(使用 Stripe API 客户端进行支付)并创建只能由他们的合作者阅读和编辑的私有 wikis。

过去，在处理基于角色的授权以及用户角色被授权执行哪些 CRUD(创建、读取、更新、删除)功能时，所有用户，无论他们是否登录，都拥有对所有内容的读取权限。这意味着大多数授权发生在控制器和模型之间。我可以很容易地将一个策略类传递给我的查询文件，并在允许用户继续 CRUD 操作之前检查用户是否得到授权，如果没有，我可以显示一个未经授权的通知并重定向它们。对于 Wikology，私有 wikis 的引入意味着并非所有用户都有读取权限，因此，角色授权不仅要在模型和控制器之间实现，还要在视图中实现。

那么，如何在应用程序的视图部分为 CRUD 方法实现基于角色的授权呢？如果你是一个经验丰富的开发人员，这似乎是一个显而易见的答案，但这是我的第二个客户端应用程序，我正要在一个完全不同的层次上重新熟悉 MVC 框架。

使用模型-视图-控制器框架的视图端的关键是考虑如何向视图传递信息。

```
 show(req, res, next){
        wikiQueries.getWiki(req.params.id, (err, wiki) => {
            if(err || wiki == null){
                res.redirect(404, "/wikis");
            } else if(wiki.private === false || 
(wiki.private == true && wiki.userId == req.user.id)){

                    wiki.body = markdown.toHTML(wiki.body);
                    wiki.title = markdown.toHTML(wiki.title);
                    res.render("wikis/show", 
                     {wiki: wiki, collaborator: null}
                    );
            } else if (wiki.private == true) {

               collaboratorQueries.collaboratorAccess(wiki.id,
               req.user.id, (err, collaborator) => {
                    if(err || collaborator == null) {

                        req.flash("notice", "You do not have permission
                        to view this wiki.");

                        res.redirect('/wikis');

                    } else {

                        wiki.body = markdown.toHTML(wiki.body);
                        wiki.title = markdown.toHTML(wiki.title);

                        res.render("wikis/show", 
                        {wiki: wiki, collaborator: collaborators);
                    }
                })  
            }
        })
    }, 
```

这是处理每个单独 wiki 视图的 show 方法。让我们来分解一下:

1.  获取 wiki——为了做任何事情，我们需要检索与给定 wikiId 匹配的特定实例。

2.  检查 wiki 是否是私有的，如果是，检查我们是否是所有者——如果 wiki 是公共的，或者如果我们编写了 wiki，我们已经保证了读取权限，并且可以继续呈现视图。这里，我们将步骤 1 中的 wiki 作为{wiki: wiki }传递，将协作者作为 null 传递，因为如果我们是所有者或者 wiki 是公共的，它们与 show 方法无关。

3.  如果它是私有的，并且我们不是所有者，我们需要检索这个 wiki 的协作者—每个协作者实例与用户有一对一的关系，与 wiki 有一对多的关系(例如，每个协作者实例只有一个用户和一个 wiki，但是一个 Wiki 可以有多个协作者实例)。这里的特定查询发生在我的 collaboratorQuery 文件上(如下所示),但概念相当简单。我传入步骤 1 中的 wikiId，以及 req.user.Id，这是发出请求的用户的 id。请注意，我并没有试图检索这个 wiki 的所有协作者，我只需要知道当前用户是否是协作者，以便确定他们的读取权限。

```
 collaboratorAccess(wikiId, userId, callback){
        Collaborator.findOne({where: {wikiId: wikiId, userId: userId}})
        .then((collaborator) => {
            callback(null, collaborator);
        })
        .catch((err) => {
            callback(err);
        })
    } 
```

如果出现错误，或者当前用户和 wiki 之间不存在 collaborator 实例，用户将被重定向并发送一条消息，告知他们无权查看该 wiki。

然而，如果一切顺利，我们可以继续调用视图来呈现 wiki。这一次，我们传入了`{wiki: wiki, collaborator: collaborator}`,它不仅将 wiki 传递给视图，还传递了 collaborator 实例，该实例将当前用户作为协作者链接到 wiki。通过将 collaborator 实例传递给视图，我可以确保当前用户能够在呈现之前查看私有 wiki。理想情况下，用户在到达视图之前会被重定向，但是如果他们要手动输入路径(例如，一个不再有读取权限但知道 url 路径的前合作者)，这将在呈现之前仔细检查他们的授权。

同样的策略可以用于其余的 CRUD 方法，将授权用户作为 collaborator 实例传入，以确定应该在视图中呈现什么。其他示例包括，确定编辑和删除按钮是否应该对给定用户可见，或者哪些私人维基标题应该出现在用户的索引页面上(例如，他们拥有或合作的那些)。

每当我被一个问题困住时，我能问自己的最有效的问题是“谁知道什么和如何知道？”本质上——这个组件有我要求它做的事情所需的信息吗？这些信息从何而来，又是如何获取这些知识的？记住，如果你一直盯着同一个问题，却毫无进展——后退一步，这样你就能只见树木不见森林。

— MxOliver