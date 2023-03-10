# 教我们的机器人新语言

> 原文：<https://dev.to/dinkydani21/teaching-new-languages-to-our-bot-3gn9>

我们一直在对[别管我](https://leavemealone.xyz)进行改进，但有一件事自发布以来一直保持不变，那就是*退订机器人*。

如果你不熟悉“别管我”的工作方式，我们会扫描你的收件箱，显示你收到的所有订阅的列表。然后，我们会让您点击一个按钮，选择取消订阅。

*退订机器人*是系统的一部分，当用户点击那个漂亮的退订开关时，它会代表用户执行实际的退订。

[![Teaching new languages to our bot](img/1dce2ed999caceee917c5f71e74988c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DAyxFgJm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.leavemealone.xyz/conteimg/2019/06/unsubscribe-new.gif)

退订器本质上非常简单，它跟踪一个 URL 并读取输出，如果需要的话执行一些操作，并最终确定退订是否成功。如果失败了，我们告诉用户，他们可以手动干预。

然而，当我们构建它时，我们做了一些非常天真的假设，随着我们的发展，很明显这些肯定需要发展。

这篇文章将处理我们最大的抱怨之一，让我们看看你是否能从简短的描述中找出它。

* * *

执行退订时，可能会发生许多事情，由退订者决定结果是否成功。它有许多硬编码的基本短语来帮助它理解正在发生的事情。

例如，如果它看到下面的话，它知道事情进展顺利；

```
[
  "success",
  "successfully",
  "unsubscribed",
  "you have been removed"
] 
```

或者如果它看到一个带有文本`Confirm`的按钮，它就知道要点击它，等等。

如果你的母语不是英语，那么我想表达的意思就很明显了。因为我是英国人，所以当我们把它发布到现实世界时，我当然很少考虑退订者会遇到的语言，遗憾的是，我只教了我的孩子英语。

然而，与我不同的是，它可以向世界学习。

* * *

免责声明——我是一名网络开发人员，不是数据科学家。如果我用错了下面的单词或者说了其他愚蠢的话，请纠正我，我会做一些修改。

谢天谢地，当退订者出生时，我决定收集它的失败数据是一个好主意，以防我们将来需要它们。虽然我当时并不知道，但这可能是我做过的最好的决定。

取消订阅后(无论成功与否),我们向用户提供这个屏幕；

<figure>[![Teaching new languages to our bot](img/70e73a5bb54889bc6014a608e4c4d890.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F3wgS1UU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.leavemealone.xyz/conteimg/2019/05/Screen-Shot-2019-05-27-at-14.29.43.png) 

<figcaption>【别管我】反馈画面</figcaption>

</figure>

这使我们能够收集到底发生了什么的信息。有了这些丰富的数据，退订者肯定能从中学习到一些东西。

* * *

不幸的是，在我们开始之前，我们遇到了一个问题，我们一直将这些数据保存为图像，而我们真正需要的是文本(是的，过去的我是一个白痴)。所以首先我们需要把文本拿出来。

为了从图像中抓取文本，我们将使用 OCR 软件 [Tesseract](https://tesseract.projectnaptha.com/) 。当文本较大时，宇宙魔方似乎工作得更好，所以我们也将使用流行的图像处理器 [Sharp](https://github.com/lovell/sharp) 放大图像。最后，宇宙魔方不会为我们检测语言，所以这将是 [langdetect](https://github.com/newmsz/node-language-detection) 的工作。

```
const Tesseract = require('tesseract.js');
const sharp = require('sharp');
const fs = require('fs');
const langdetect = require('langdetect');

// read all image filenames from path
const path = __dirname + '/images';
const images = fs.readdirSync(path).filter(p => p.endsWith('.png'));

let languages = {};

const { TesseractWorker } = Tesseract;
const worker = new TesseractWorker();

(async () => {
  // process images one at a time
  for (let i = 0; i < images.length; i = i + 1) {
    const image = images[i];
    await new Promise(resolve => {
      // resize image
      sharp(`${path}/${image}`)
        .resize(1200, 900)
        .toFile('output.png')
        .then(() => {
          worker
            .recognize(`output.png`)
            .progress(message => {})
            .catch(err => {})
            .then(result => {
              // result.words stores the metadata
              // dertermined anbout each bit of text
              if (result.words.length) {
                // use the first identified language
                // and add to the count
                const language = langdetect.detectOne(result.text);
                console.log(language);
                const currentCount = languages[language] || 0;
                languages = {
                  ...languages,
                  [language]: currentCount + 1
                };
              }
              fs.writeFileSync(
                'languages.json',
                JSON.stringify(languages, null, 2)
              );
              resolve();
            })
            .finally(resultOrError => {});
        });
    });
  }
})(); 
```

在长时间处理我们的图像后，我们对存在的语言有了一个很好的了解。

```
{
  en: 6438,
  fr: 1153,
  it: 503,
  nl: 346,
  ...
} 
```

但是我们真正想要的是关键词。

从英语语料库中我们可以看出，最常用的短语是那些表示退订状态的短语；“退订成功”、“不便之处，敬请原谅”、“您的邮件已被删除”等。我们可以假设这在其他语言中也是一样的，所以即使我们不能理解它们，我们也可以相对确定其准确性。应用程序中提供的用户反馈也对我们有所帮助。

我们在语言处理术语中寻找的是最高出现的 **n-grams** 。一个 n-gram 仅仅是一个由 *n* 项组成的连续序列，在我们的例子中,“取消订阅成功”是一个二元模型，我们想要确定这种情况是否经常发生。频率越高，n-gram 就越与动作相关...大概吧。

通过稍微修改我们的宇宙魔方脚本，我们可以将所有文本存储到语言文件中，以备后用(这被称为*语料库*)。

```
let language;
if (result.words.length) {
  // use the first identified language
  // and add to the count
  const language = langdetect.detectOne(result.text);
  const currentCount = languages[language] || 0;
  languages = {
    ...languages,
    [language]: currentCount + 1
  };
}
// add text to the corpus
fs.appendFileSync(`${__dirname}/corpus/${language}.txt`, result.text, 'utf8'); 
```

在所有的图像被处理后，我们有相当多的不同语言的线条。

```
$ wc -l ./corpus/*
    138 ./corpus/af.txt
     87 ./corpus/cs.txt
    492 ./corpus/da.txt
   4958 ./corpus/de.txt
 277388 ./corpus/en.txt
   1507 ./corpus/es.txt
    325 ./corpus/et.txt
    130 ./corpus/fi.txt
   5553 ./corpus/fr.txt
     71 ./corpus/hr.txt
    215 ./corpus/hu.txt
    169 ./corpus/id.txt
   2602 ./corpus/it.txt
     17 ./corpus/lt.txt
      7 ./corpus/lv.txt
   1342 ./corpus/nl.txt
    393 ./corpus/no.txt
    755 ./corpus/pl.txt
   2377 ./corpus/pt.txt
   3411 ./corpus/ro.txt
    258 ./corpus/sk.txt
    153 ./corpus/sl.txt
    902 ./corpus/so.txt
     19 ./corpus/sq.txt
    292 ./corpus/sv.txt
     53 ./corpus/sw.txt
     94 ./corpus/tl.txt
    743 ./corpus/tr.txt
    129 ./corpus/vi.txt
 304580 total 
```

所以让我们在新的语料库上运行一个 *n-gram 频率分析*。为此，我们使用自然语言处理库 [natural](https://github.com/NaturalNode/natural) 。

```
const natural = require('natural');
const fs = require('fs');

const LANG = 'fr';

const text = fs.readFileSync(`${LANG}.txt`).toString();

// find ngrams of 5 words 
const ngrams = natural.NGrams.ngrams(text, 5);
// count ngram occurrences
let out = ngrams.reduce((o, ng, i) => {
  const str = ng.join('  ');
  if (o[str]) {
    return {
      ...o,
      [str]: o[str] + 1
    };
  }
  return { ...o, [str]: 1 };
}, {})

// sort ngrams by count
out = Object.keys(out).sort((a, b) => {
  return out[a] - out[b];
}).reduce((o, key) => {
  return { ...o, [key]: out[key] };
}, {})

// write sorted ngrams out to a file
fs.writeFileSync(`${LANG}.analysis.js`, JSON.stringify(out, null, 2)); 
```

以法语为例，我们可以看到一些出现频率最高的短语相当不错；

```
[
  "désabonnement réussi", // unsubscribed successfully
  "vous ne recevrez plus", // you will not receive any more
  ...
] 
```

不幸的是，这里也有很多垃圾。退订后经常看到的屏幕是 Mailchimp 的“你为什么退订”页面，如下所示。

[![Teaching new languages to our bot](img/81890cbd6f205dee19b3d55726170077.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DKzyd5zY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.leavemealone.xyz/conteimg/2019/06/unsub-success-1.png)

所以我们从像这样的屏幕上得到很多短语，这些短语并不是很有用。

```
[
  ...
  // I never subscribed to this list
  "je ne suis jamais abonné à cette liste", 
  // if you have a moment
  "Si vous avez un moment",
  // why you unsubscribed
  "pourquoi vous vous êtes désabonnement"
  ...
] 
```

### 结论

尽管这是一个有趣的实验，但这比仅仅将我们的英语短语翻译成不同语言的母语术语要好吗？

大概不会。

在大多数语言中，我们没有足够的数据来进行像样的分析，即使在那些流行到足以获得足够多短语的语言中，我仍然必须手动干预来翻译它们并确保它们是有效的。

那么下一步是什么？

我们将继续努力增加多语言支持，但很可能我们必须正确翻译短语，而不是像这样依赖自动翻译。

在最新版本的“别管我”中，我们将为用户提供各种奖励，比如推荐他们的朋友。古伊列梅·里索最近的这条推文让我想到，也许用户会愿意提供他们自己的翻译，以换取足够体面的报酬。

> ![unknown tweet media content](img/84fda49b1557a3fa89e9d9e5ee980c3e.png)![Guilherme Rizzo from CSS Scan profile image](img/a659a4cef3d6a15131ffab9265639af5.png)古伊列梅里佐来自 CSS 扫描@ gvrizzo![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)你好，世界！정석원刚刚把 CSS Scan 翻译成了🇰🇷韩语， [@Habbe](https://twitter.com/Habbe) 翻译成了🇸🇪瑞典语！
> 
> 📊我创建了一个简单的谷歌表单，任何人都可以将 CSS 扫描翻译成他们自己的语言——我们会在我们的网站上感谢你👉[docs.google.com/spreadsheets/d…](https://t.co/sRDLWlVM0t)T21
> 👉CSS 扫描[getcssscan.com](https://t.co/qmNzkkZtzS)20:44pm-01 2019 年 6 月[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1134923628305272832)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1134923628305272832)3[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1134923628305272832)34

感谢您读到这里！[订阅我们的博客](https://blog.leavemealone.xyz),看看我们未来的发展方向，听听其他“别管我”的决定和改进！

* * *

特别感谢我的法国朋友[巴兹](https://twitter.com/BasileSamel)和[安妮-劳雷](https://twitter.com/@anthilemoon)再次检查我的短语是否有意义，并教我一些法语脏话！