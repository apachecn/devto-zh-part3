# 使用 Smalltext 对 Ruby 中的短字符串进行 ML 分类

> 原文：<https://dev.to/modsognir/ml-classification-on-short-strings-in-ruby-using-smalltext-5faa>

我有一个 ruby 应用程序，可以从互联网上获取各种博客和论坛帖子(就像一个游戏开发跟踪器一样)，并且需要一种方法来对它们进行分类。一些简单的规则让我做到了 75%——包括单词“补丁”或“补丁注释”吗？然后，它可能是一个补丁说明或公告后-但这并不完全可靠。例如，报告补丁程序错误的帖子可能会被表述为“由于补丁程序 9.0.0.2076，库存为空”，或者帖子可能会宣布补丁程序部署，但不包括任何注释。

最近我找到了解决我大部分问题的 gem [smalltext](https://github.com/arjunmenon/smalltext) 。它分类很快，训练起来也很简单。

我构建了一个训练脚本，将训练数据硬编码成类别，然后训练并保存一个模型。

#### lib/train_classifier.rb

```
require 'smalltext'

data = {
  patch_notes: [
    "PC Client Update - 12/7/2018",
    "v6.31 Patch Notes",
  ],

  news: [
    "$1,000,000 Winter Royale + Tournament Updates",
    "Pop-Up Cups Update 12/5/2018",
  ],
}

s = Smalltext::Classifier.new
data.each do |classification, titles|
  titles.each do |title|
    s.add_item(classification.to_s, title)
  end
end

s.train
s.save_model("classifications.model") 
```

随着时间的推移，我用明确的例子和错误分类的帖子更新了这个训练脚本，以改进模型。

#### app/服务/分类器. rb

```
class Classifier
  def self.classify(text, threshold = 0.99)

    # Load the classifier and the trained model
    @classifier ||
      ((@classifier = Smalltext::Classifier.new) && @classifier.load_model('classifications.model'))

    classified = @classifier.classify(text).to_h
    # { "patch_notes" => 0.9998900049414214 }

    # Return the first category that meets the threshold
    result = classified.detect {|k,v| v >= threshold }
    result.present? ? result.first : nil
  end
end 
```

这个类为我的应用程序提供了一个分类器的入口点。我只关心合理确定的分类，所以我构建这个来只输出一个高于某个阈值的分类。在我的应用程序中，我要么得到一个类别，要么没有，这使我更容易处理。

```
> Classifier.classify("V7.00 PATCH NOTES")

sentence: V7.00 PATCH NOTES
classification: [["patch_notes", 0.9998900049414214]]

=> "patch_notes" 
```