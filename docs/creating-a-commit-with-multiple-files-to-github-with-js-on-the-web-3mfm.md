# 在 web 上用 JS 创建一个包含多个文件的 Github 提交

> 原文：<https://dev.to/chromiumdev/creating-a-commit-with-multiple-files-to-github-with-js-on-the-web-3mfm>

我的网站是完全静态的。它由[雨果](https://gohugo.io)建造，由[时代](https://zeit.co)主持。我对这个设置非常满意，我获得了近乎即时的构建和超快的 CDN 内容交付，并且我可以做我需要做的所有事情，因为我不必管理任何状态。

我已经为这个网站创建了一个简单的用户界面(UI )( T1 ),还有我的播客创建器(T2 )( T3 ),它可以让我快速地将新内容发布到我的静态托管网站上。

[![](img/8a4e8f74201362748f69eea3f7b6f3e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3BgIiU50--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://paul.kinlan.img/2019-05-24-creating-a-commit-with-multiple-files-to-github-with-js-on-the-web-0.jpeg)

所以。我是怎么做到的？

它结合了 Firebase Auth 和我的 Github Repo，EditorJS 来创建和编辑内容(很简洁)，Octokat.js 来提交 Repo，然后 Zeit 的 Github 集成来完成我的 hugo 构建。有了这个设置，我就能够拥有一个完全自托管的静态 CMS，类似于用户如何在数据库支持的 CMS(如 Wordpress)中创建帖子。

在这篇文章中，我将只关注基础设施的一部分——向 Github 提交多个文件，因为我花了一些时间来解决这个问题。

完整的代码可以在我的 [repo](https://github.com/PaulKinlan/podcastinabox-editor/blob/master/record/javascripts/main.mjs#L90) 上看到。

如果你正在构建一个需要直接提交给 Github 的 Web UI，我发现的最好的库是 octo kat——它与 CORS 一起工作，并且它似乎处理 Github API 的整个 API 表面。

当谈到理解树、分支和其他部分如何工作时，Git 可能是一个复杂的野兽，所以我做了一些决定使它变得更容易。

1.  我将只能推进到称为`heads/master`的主分支。
2.  我将知道某些文件将被存储在哪里(Hugo 强迫我有一个特定的目录结构)

记住这一点，使用多个文件创建提交的一般过程如下:

获取回购的参考。

1.  获取对`heads/master`分支上的树梢的引用。
2.  对于我们想要提交的每个文件，创建一个`blob`，然后将对`sha`标识符、路径、模式的引用存储在一个数组中。
3.  创建一个新的`tree`，它包含所有要添加到对`heads/master`树顶端的引用的斑点，并存储指向该树的新的`sha`指针。
4.  创建一个指向这个新树的提交，然后推送到`heads/master`分支。

代码非常符合这个流程。因为我可以假设某些输入的路径结构，所以我不需要为文件构建任何复杂的 UI 或管理。

```
const createCommit = async (repositoryUrl, filename, data, images, commitMessage, recording) => {
  try {
    const token = localStorage.getItem('accessToken');
    const github = new Octokat({ 'token': token });
    const [user, repoName] = repositoryUrl.split('/');

    if(user === null || repoName === null) {
      alert('Please specifiy a repo');
      return;
    }

    const markdownPath = `site/content/${filename}.markdown`.toLowerCase();
    let repo = await github.repos(user, repoName).fetch();
    let main = await repo.git.refs('heads/master').fetch();
    let treeItems = [];

    for(let image of images) {
      let imageGit = await repo.git.blobs.create({ content: image.data, encoding: 'base64' });
      let imagePath = `site/statimg/${image.name}`.toLowerCase();
      treeItems.push({
        path: imagePath,
        sha: imageGit.sha,
        mode: "100644",
        type: "blob"
        });
    }

    if (recording) {
      let audioGit = await repo.git.blobs.create({ content: recording.data, encoding: 'base64' });
      let audioPath = `site/static/audio/${recording.name}.${recording.extension}`.toLowerCase();
      treeItems.push({
        path: audioPath,
        sha: audioGit.sha,
        mode: "100644",
        type: "blob"
        });
    }

    let markdownFile = await repo.git.blobs.create({ content: btoa(jsonEncode(data)), encoding: 'base64' });
    treeItems.push({
      path: markdownPath,
      sha: markdownFile.sha,
      mode: "100644",
      type: "blob"
    });

    let tree = await repo.git.trees.create({
      tree: treeItems,
      base_tree: main.object.sha
    });

    let commit = await repo.git.commits.create({
      message: `Created via Web - ${commitMessage}`,
      tree: tree.sha,
      parents: [main.object.sha]});

    main.update({sha: commit.sha})

    logToToast('Posted');
  } catch (err) {
    console.error(err);
    logToToast(err);
  }
} 
```

让我知道你是否做过类似的静态托管。我非常兴奋，我可以为一个完全无服务器的托管基础设施构建一个现代化的前端。