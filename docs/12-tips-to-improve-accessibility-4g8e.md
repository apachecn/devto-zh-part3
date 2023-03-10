# 提高可访问性的 12 个技巧

> 原文：<https://dev.to/invot/12-tips-to-improve-accessibility-4g8e>

一个常见的假设是无障碍环境只对残疾人开放。尽管我们应该时刻记住那些有视觉、听觉、身体、语言、认知和神经障碍的人，但无障碍网络应该是为每个人设计的，并使每个人受益。有无残疾的人。网络用户和网络开发者。请记住，人们的需求和偏好千差万别。一个可访问的网站给你的用户以他们需要或想要的任何方式访问数字材料的灵活性。

## 要牢记的概念

**人类都是不同的，多样的**。正如我们在文化背景下庆祝个人的多样性一样，我们也应该设计考虑不同需求和偏好的技术。

**无障碍设计是创新设计**。使技术变得可访问的设计原则——语义标记、直观的信息层次、机器可读的文本——也是促进移动设计和语义网的原则。

技术有潜力成为一个伟大的均衡器，但无法获得的技术只会进一步排斥人们。让我们拥抱技术的潜力，让人们尽可能地独立、自给自足和相互联系。

## 提高可达性的 12 种方法

下面你会发现一些无障碍设计的关键原则。大多数可访问性原则可以非常容易地实现，并且不会影响网站的整体“外观和感觉”。

1.  [提供适当的替代文本](http://webaim.org/techniques/alttext)

    替代文本为网页中的非文本内容(如图片和图像)提供文本替代。对于盲人和依靠屏幕阅读器阅读网站内容的人来说，这尤其有用。

2.  [提供适当的文件结构](http://webaim.org/techniques/semanticstructure)

    标题、列表和其他结构元素为网页提供了意义和结构。它们还可以促进页面内的键盘导航。

3.  [为数据表提供表头](http://webaim.org/techniques/tables)

    表格在线用于布局和组织数据。用于组织表格数据的表应该有适当的表头(即

    element). Data cells should be associated with their appropriate headers, Making it easier for screen reader users to navigate and understand the data table.
4.  [Make sure that users can complete and submit all forms](http://webaim.org/techniques/forms)

    Make sure that every form element (text field, check box, drop-down list, etc.). ) has a label, and make sure that the label uses elements associated with the correct form elements. Also make sure that users can submit the form and recover from any errors, such as failing to fill in all required fields.

5.  [Make sure that links are meaningful out of context](http://webaim.org/techniques/hypertext)

    If the linked text is read by yourself, then every link should be meaningful. Screen reader users can choose to read only the links on the web page. Certain phrases such as "click here" and "more" must be avoided.

6.  [Provide subtitles and/or written records for media](http://webaim.org/techniques/captions)

    Video and live audio must have subtitles and written records. For archived audio, transcription may be sufficient.

7.  Ensure the accessibility of non-HTML content, Including [PDF files](http://webaim.org/techniques/acrobat) , [Microsoft Word](http://webaim.org/techniques/word) documents, [PowerPoint](http://webaim.org/techniques/powerpoint) presentations and [Adobe Flash](http://webaim.org/techniques/flash) content

    In addition to all other principles listed here, PDF documents and other non-HTML content must be accessible as easily as possible. If you can't make it accessible, consider using HTML, or at least provide an accessible alternative. The PDF should also contain a series of tags to make it more accessible. The tagged PDF file looks the same, but it is almost always easier for people who use screen readers to access it.

8.  [Allow users to skip the repeated elements on the page](http://webaim.org/techniques/skipnav)

    You should provide a way to allow users to skip navigation or other elements that are repeated on every page. This is usually achieved by providing a "jump to main content" or "skip navigation" link at the top of the page, which can jump to the main content of the page.

9.  [Don't just rely on color to convey meaning](http://webaim.org/articles/visual/colorblind)

    The use of color can enhance understanding, but don't use color alone to convey information. This information may not be available to the blind and screen reader users.

10.  [Make sure the content is written clearly and easily](http://webaim.org/techniques/writing)

    There are many ways to make your content easier to understand. Write clearly, use clear fonts, and use titles and lists appropriately.

11.  [Make JavaScript accessible](http://webaim.org/techniques/javascript)

    Make sure that JavaScript event handlers are device independent (for example, they don't need to use a mouse), and make sure that your page doesn't depend on JavaScript to run.

12.  [Design according to the standard](https://developer.mozilla.org/en-US/docs/Learn/Accessibility/CSS_and_JavaScript)

    Even if the style sheet is disabled, make sure that every page has complete functions. HTML-compatible and accessible pages are more powerful and provide better search engine optimization. [Cascading Style Sheets (CSS)](http://webaim.org/techniques/css) allow you to separate content from presentation. This provides more flexibility and accessibility to your content.