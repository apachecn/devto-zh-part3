# 如何在 JavaScript 中使用本地存储

> 原文：<https://dev.to/justalever/how-to-use-local-storage-with-javascript-19fl>

自从我用 JavaScript 写了另一篇文章《让我们来构建》( Let's Build: With JavaScript ),已经有一段时间了，所以我又写了一篇新的文章，集中在使用本地存储上。

作为本教程的前奏，我强烈建议不要对注释数据使用本地存储，因为我将向您展示，而应该更多地使用小型键值分配。例如，假设您在站点上显示通知提示、电子邮件订阅提示或某种警报。您可以连接到浏览器的本地存储 API，以保存给定用户是否希望在您的站点上显示这些内容。

这一切都可以通过 JavaScript 的点击事件来实现。

本教程进一步展示了向现有评论提要添加新评论的概念验证。这绝不是完整的，但它让您大致了解了如何向 DOM 添加新数据，以及如何用本地存储来存储这些数据。

我邀请您扩展这段代码，让评论者也可以从本地存储中删除他们的评论。

[https://www.youtube.com/embed/AfVUiQ-my4Q](https://www.youtube.com/embed/AfVUiQ-my4Q)

[下载源代码](https://github.com/justalever/local-storage-comments)

### 标记

首先，我将使用一些虚拟数据。我们将创建一个已经有一条评论的评论提要。提要下面将是一个新的评论表单，一旦提交，将在页面上创建和插入新评论的值。这也将挂钩到本地存储以保存持久化的数据，因此当评论者离开页面并返回时，数据仍然存在。

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    Let's Build with JavaScript - Local Storage Comments
    <link rel="stylesheet" href="styles.css" />
  </head>
  <body>
    <div class="comment-container">
      <h3 class="comment-container-title">Comments</h3>
      <div class="comments">
        <div class="comment flex items-start justify-start">
          <img
            class="comment-avatar"
            src="https://secure.gravatar.com/avatar/d1f5ca0d7e625f334c5186e112b77ebd"
          />
          <div class="flex-1">
            <h3 class="comment-author">Andy Leverenz</h3>
            <p class="comment-body">First comment of some type!</p>
          </div>
        </div>
      </div>
      <div class="comment comment-new flex items-start justify-start">
        <img
          class="comment-avatar"
          src="https://secure.gravatar.com/avatar/d1f5ca0d7e625f334c5186e112b77ebd"
        />
        <div class="flex-1">
          <h3 class="comment-author">Andy Leverenz</h3>
          <form action="#" class="comment-form">
            <textarea
              placeholder="Add a comment"
              class="comment-input"
            ></textarea>
            <input type="submit" class="comment-submit" value="Reply" />
          </form>
        </div>
      </div>
    </div>

    <script src="app.js"></script>
  </body>
</html> 
```

### CSS

我们应该让表单和评论提要更像样。为此，我创建了下面的 CSS。

```
* {
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  font-size: 1rem;
  line-height: 1;
}

.flex {
  display: flex;
}

.flex-1 {
  flex: 1;
}

.items-start {
  align-items: start;
}

.justify-start {
  justify-content: flex-start;
}

.comment-container {
  border-radius: 8px;
  border: 1px solid #e2e2e2;
  margin: 3rem auto;
  max-width: 600px;
  min-height: 200px;
}

.comment-container-title {
  background: #f8f8f8;
  border-bottom: 1px solid #ebebeb;
  border-top-left-radius: 8px;
  border-top-right-radius: 8px;
  color: #2d3748;
  margin-top: 0;
  padding: 1rem 2rem;
}

.comment {
  margin-top: 1rem;
  padding: 1rem 2rem;
}

.comment-new {
  border-top: 1px solid #ebebeb;
  margin: 1rem 0;
  padding-top: 2rem;
}

.comment-avatar {
  border-radius: 50%;
  height: 48px;
  margin-right: 1rem;
  width: 48px;
}

.comment-author {
  font-size: 1rem;
  margin-bottom: 0.5rem;
  margin-top: 0;
}

.comment-body {
  color: #4a5568;
  line-height: 1.4;
  margin: 0;
  padding-right: 1rem;
}

.comment-input {
  border-radius: 8px;
  border: 1px solid #dddd;
  box-sizing: border-box;
  color: #4a5568;
  font-size: 1rem;
  line-height: 1.4;
  padding: 0.8rem;
  width: 100%;
}

.comment-input:focus {
  border-color: #5c6ac4;
  outline: none;
}

.comment-submit {
  background: #5c6ac4;
  border-radius: 4px;
  border: none;
  color: white;
  cursor: pointer;
  font-size: 12px;
  letter-spacing: 0.05rem;
  margin-top: 0.5rem;
  padding: 10px 16px;
  text-transform: uppercase;
}

.comment-submit:hover,
.comment-submit:focus {
  filter: brightness(110%);
} 
```

### JavaScript

最后，我们让这一切工作。即使页面上有一个表单元素，我也想禁用它的默认操作。我们可以通过在`Reply`按钮上添加一个事件监听器来做到这一点。该按钮还将启动将新创建的注释添加到 DOM 本身的过程。

从那里，我们使用 ES6 字符串文字遵循模板，并传入适当的数据。数据可以更加动态，但要做到这一点，我们需要利用远程 API 或数据存储。我通常使用 Ruby on Rails，所以我可能会使用后者，并将其输出到标记中的某个位置，以便以后直接用 JavaScript 获取。

最后，我的代码如下所示。为了更加清楚，我添加了一些注释。

```
const submit = document.querySelector('.comment-submit');
const commentList = document.querySelector('.comments');
const commentInput = document.querySelector('.comment-input');

function template(data) {
  commentList.insertAdjacentHTML("beforeend", `
  <div class="comment flex items-start justify-start">
      <img class="comment-avatar" src="${data.avatar}" />
      <div class="flex-1">
        <h3 class="comment-author">${data.author}</h3>
        <p class="comment-body">${data.comment}</p>
      </div>
    </div>
  </div>`);
}

function appendComment (event) {

  const data = {
    avatar: "https://secure.gravatar.com/avatar/d1f5ca0d7e625f334c5186e112b77ebd",
    comment: commentInput.value,
    author: "Andy Leverenz"
  };

  event.preventDefault();
  // If the value is nothing just return
  if (commentInput.value.length < 1) return;

  // Insert new template into DOM
  template(data);

  // Reset textrea value
  commentInput.value = "";

  // Save the list to localStorage
  localStorage.setItem('commentListing', commentList.innerHTML);
}

submit.addEventListener('click', appendComment, false)

// Check for saved items
const saved = localStorage.getItem('commentListing');

// If there are any saved items, update our list
if (saved) {
  commentList.innerHTML = saved;
} 
```

我再次邀请你进一步扩展这一点。也许你能想出办法从本地存储中删除一条评论。你也许可以像创建它一样调用`localStorage.removeItem('my-item, <string>)`。

### 到目前为止的级数

*   [让我们构建:使用 JavaScript–DIY 下拉菜单和响应菜单](https://web-crunch.com/lets-build-with-javascript-diy-dropdowns-responsive-menus/)
*   [让我们用 JavaScript 构建——带 Cookies 的广播栏](https://web-crunch.com/lets-build-with-javascript-broadcast-bar-cookies/)
*   [让我们用 JavaScript 构建:粘性导航](https://web-crunch.com/lets-build-with-javascript-sticky-nav/)
*   [让我们构建:用 JavaScript 动态标签](https://web-crunch.com/how-to-create-tabs-with-vanilla-javascript)
*   [让我们构建:用 JavaScript–Modals](https://web-crunch.com/lets-build-with-javascript-how-to-code-modal-vanilla-javascript/)
*   [让我们构建:使用 JavaScript–html 5 视频播放器](https://web-crunch.com/lets-build-with-javascript-html5-video-player/)
*   [让我们构建:用 JavaScript——手风琴](https://web-crunch.com/how-to-create-accordion-vanilla-javascript/)
*   [让我们构建:用 JavaScript 骨架屏幕效果](https://web-crunch.com/how-to-create-skeleton-screen-loading-effect)
*   [让我们构建:用 JavaScript——如何编写一个非画布菜单——让我们构建:用 JavaScript](https://web-crunch.com/off-canvas-menu)
*   [让我们构建:用 JavaScript–显示更多–显示更少用 JavaScript 切换](https://dev.to/justalever/how-to-code-a-show-more--show-less-toggle-with-javascript-3c7m)

帖子[如何使用 JavaScript 的本地存储](https://web-crunch.com/how-to-use-local-storage-with-javascript/)首先出现在[的 Web-Crunch](https://web-crunch.com) 上。

### 不要脸的塞！

[![](img/f572d5df905b6366d1201f6c51d1b8c2.png)](http://hellorails.io)

我有一门新课程叫做 [Hello Rails](https://hellorails.io) 。Hello Rails 是一门现代课程，旨在帮助您快速开始使用和理解 Ruby on Rails。如果你是 Ruby 或 Ruby on Rails 的新手，我邀请你去看看这个网站。该课程将与这些构建非常相似，但是是一个超级深入的版本，具有更现实的目标和可交付成果。[报名今天得到通知](https://hellorails.io/)！

在推特上关注 [@hello_rails](https://twitter.com/hello_rails) 和[本人@justalever](https://twitter.com/justalever) 。