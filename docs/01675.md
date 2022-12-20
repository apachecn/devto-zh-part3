# 基于夹具的 Rails 接口测试方法

> 原文：<https://dev.to/evilmartians/a-fixture-based-approach-to-interface-testing-in-rails-2cd4>

# 问题

在过去的几个月里，我的团队一直在做一个用 Ruby on Rails 编写的大项目。我们的应用程序的关键组件是集成到许多外部 API，如不同的[易贝服务](https://developer.ebay.com/docs)，邮政服务，物流运营商，翻译服务等。我们使用的所有接口都是复杂而丰富的。它们中的大多数都在不断改进:新版本不时发布，而旧版本则不再受支持。一般来说，对于如此复杂的项目，您不应该期望完全或 100%一致的文档覆盖。

当我们从外部服务转向位于应用程序前端和后端之间的“内部”服务时，整个情况变得更加复杂。不久前，我们决定从 RESTful API 切换到 GraphQL，我们作为开发人员的生活变得更加快乐。但是没有蜜蜂就没有蜂蜜。GraphQL 接口的优点是其灵活性的负面影响。API 变得越灵活，我们不得不考虑和通过测试覆盖的边缘情况就越多。

这就是为什么在后端我们依赖我们自己的测试，试图覆盖尽可能多的极限情况。有时规格是程序如何运行的唯一证据。随着时间的推移，即使是特写的作者也会忘记细节。但是当一个规范必须被同事阅读时，不管他们是代码评审员还是团队的新成员，这都是一个不好的信号。这就是为什么测试的可读性很重要并且值得努力的原因。

但是测试的可读性意味着什么呢？我相信它不仅应该恰当地涵盖边缘案例，还应该讲述一个关于我们期望的故事。它必须一步一步来，不要给读者过多的细节。理想情况下，它应该展示我们期望的大画面，并且这个声明必须与我们如何对期望建模分开。

跳过前面，我希望我的测试看起来像这样:

```
RSpec.describe ProductTranslator, ".call" do
  subject { described_class.call(listing) }

  before do
    stub_fixture "#{__dir__}/#{edge_case}/stubs.yml"
    seed_fixture "#{__dir__}/#{edge_case}/database.yml", listing_id: 42
  end

  let(:listing) { Listing.find(42) }
  let(:target)  { load_fixture "#{__dir__}/output.yml" }

  it "updates the listing with translated specifics" do
    expect { subject }.to change { listing.reload.specifics }.to target
  end
end 
```

在这篇文章的剩余部分，我将试着展示我们基于 fixture 的方法来使测试变得可理解，以及我们为这个目标使用的小工具。因为我们使用基于 Rails 的应用程序，所以我引用了其生态系统中的特定工具( [RSpec](https://relishapp.com/rspec/rspec-rails/docs) 、 [FactoryBot](https://github.com/thoughtbot/factory_bot) 和[数据库清理器](https://github.com/DatabaseCleaner/database_cleaner))。但是这篇文章的整体思想是与框架无关的:**使用 fixtures 为规范准备尽可能多的上下文**。

# Web API 作为测试主体

在进一步讨论之前，让我们将 API 作为测试的一个特定主题来看一下。

从消费者的角度来看，所有的公共 web 界面都是相似的。按照其业务逻辑，应用程序准备一个请求并将其发送给服务器。服务器用数据进行响应，这些数据必须根据响应的内容以某种方式进行处理。大多数接口使用基于文本的格式(JSON、XML、GraphQL)进行数据序列化。

这里我们应该指定消费者逻辑的两个部分:

*   请求准备的正确性取决于应用程序的内部状态，
*   响应处理的正确性取决于应用程序的响应和内部状态。

更具体地说，我们使用三种不同的规格:

*   请求生成器的单元测试，
*   响应处理程序的单元测试，
*   对发出请求的整个服务进行点对点测试。

有时指定未记录的 API 也是有意义的。目标不是测试远程服务器，而是确保我们对其行为的预期的正确性。为了简单起见，我把这个任务放在一边。那些对相应技术感兴趣的人可以查看我的同事谢尔盖·多尔加诺夫的《血液契约》。

当测试我们自己的应用程序时，我们必须存根远程接口，因为它们超出了我们的控制。这是我多年来学到的一个重要教训:使用“体内”获得的真实反应进行存根！学习这一课对我来说不容易。很多时候，当我使用文档中的“体外”例子时，会导致几个小时的调试。经验法则是“基于真实数据的存根 API”

总而言之，我们的测试环境包括:

*   DB 种子和存根来提供应用程序的内部状态，
*   外部 API 的客户端存根，
*   请求/响应的真实示例(可能因各种边缘情况而异)。

# 夹具取台

首先，我们来看一个例子。这是一个类的规范，该类使用 [GoogleTranslateDiff](https://evilmartians.com/chronicles/spend-less-on-google-translate) 客户端到 [Google Translate API](https://cloud.google.com/translate/docs/) 来翻译类似`{ name: "Color", value: "Red" }`的嵌套数据。虽然我们将 HTTPS 请求和响应隐藏在客户端接口之后，但是这个包装器足够薄，可以接受纯文本和区域设置，并返回纯文本。

```
# spec/services/product_translator/_spec.rb

RSpec.describe ProductTranslator, ".call" do
  subject { described_class.call(listing) }

  before do
    # Stub the client to remote API
    allow(GoogleTranslateDiff)
      .to receive_message_chain(:translate)
      .with do |text, from:, to:|
        case [text, from, to]
        when %w[Color en ru] then "Цвет"
        when %w[Black en ru] then "Чёрный"
        when %w[Brand en ru] then "Марка"
        when ["<span class='notranslate'>Apple</span>", "en", "ru"]
          "<span class='notranslate'>Apple</span>"
        else raise
        end
      end
  end

  # Seed the necessary objects in the database
  let(:listing) { FactoryBot.create :listing, source: product, locale: target }

  let(:product) do
    FactoryBot.create :product, :ready, locale: :en, specifics: input
  end

  # Prepare data structures for input/output
  let(:input) do
    [{ name: "Color", value: "Black" }, { name: "Brand", value: "##Apple##" }]
  end

  let(:output) do
    [{ name: "Цвет", value: "Чёрный" }, { name: "Марка", value: "Apple" }]
  end

  context "when target locale differs from the product's one" do
    let(:target) { "ru" }

    it "updates the listing with translated specifics" do
      expect { subject }.to change { listing.reload.specifics }.to output
    end
  end

  context "when target locale is the same as the product's one" do
    let(:target) { "en" }

    it "updates the listing with the original specifics from the product" do
      expect(GoogleTranslateDiff).not_to receive(:new)
      expect { subject }.not_to change { listng.reload.attributes }
    end
  end
end 
```

如您所见，当有太多细节时，即使是简单的测试也会变得难以阅读。

在复杂的情况下(当您应该改变内部状态和响应以涵盖所有的极端情况时)，整个规范变成了意大利面条。但是有好消息。正如我提到的，请求和响应都是以某种方式进行文本格式化的(JSON、XML、GraphQL 等)。).通常，我们的客户将这些数据反序列化为 JSON 兼容的基本 Ruby 对象，而不是特定于应用程序的类。

这就是为什么我们可以轻松地将它们提取到[夹具](https://en.wikipedia.org/wiki/Test_fixture)中，就像这样:

```
# spec/services/product_translator/output.yml
---
- name:  Цвет
  value: Чёрный
- name:  Марка
  value: Apple 
```

现在我们可以稍微简化一下我们的规范:

```
let(:output) { YAML.load_file "#{__dir__}/output.yml" } 
```

规范变得更好了，但并没有好多少。我们还有树桩和种子在里面。更重要的是，在夹具中隐藏了一些结构之后，模型期望变成了分布在夹具和测试之间，并且失去了可观察性。例如，当阅读规范时，不清楚为什么我们用“颜色”、“黑色”、“品牌”等来存根客户端。他们扮演什么角色？

如果能把所有具体的部分，包括存根和种子，都移到 fixture 层，让规范只是一个线索，不是更好吗？如果我们能够将方法存根、数据库种子和数据序列化的所有细节都转移到 fixtures 中，会怎么样呢？

但是我们可以！见见 [Fixturama](https://github.com/nepalez/fixturama) ，一种“类固醇夹具”宝石是从我们的项目中提取出来的，已经在生产中大量使用了几个月。您可以在自述文件中查看用法的详细信息。这里我将展示它的使用如何改进我们的规范。

在下一节中，我将向您展示 gem 支持的几种简单技术。

# 类固醇固定物

要向您的套件添加助手，只需添加依赖项:

```
require "fixturama/rspec" 
```

## 拔打

现在我们可以从存根开始。在 YAML 文件中，你可以定义一组固执己见的关键字:

```
# spec/services/product_translator/stubs.yml
---
- class: GoogleTranslateDiff
  chain:
    - new
    - translate
  arguments:
    - Color
    - :from: en
      :to: ru
  actions:
    - return: Цвет

- class: GoogleTranslateDiff
  chain:
    - new
    - translate
  arguments:
    - Brand
    - :from: en
      :to: ru
  actions:
    - return: Марка

- class: GoogleTranslateDiff
  chain:
    - new
    - translate
  arguments:
    - Black
    - :from: en
      :to: ru
  actions:
    - return: Чёрный

- class: GoogleTranslateDiff
  chain:
    - new
    - translate
  arguments:
    - "<span  class='notranslate'>Apple</span>"
    - :from: en
      :to: ru
  actions:
    - return: "<span  class='notranslate'>Apple</span>"

- class: GoogleTranslateDiff
  chain:
    - new
    - translate
  actions:
    - raise: StandardError 
```

虽然 fixture 变得相当冗长(因为我们为参数的每个变化定义了单独的动作)，但规范本身现在很简单:

```
before { stub_fixture "#{__dir__}/stubs.yml" } 
```

在某些情况下，您将在一个夹具中收集所有的短截线，或者您可以在不同的规格之间分割它们(对于各种边缘情况)。

*您可以在这里自定义更多选项，为方法的连续调用设置不同的返回，等等。在 [Github](https://github.com/nepalez/fixturama) 上查看更多示例。*

## 播种

移走存根后，我们可以对数据库对象做同样的事情。这里的`fixturama`为[工厂机器人](https://github.com/thoughtbot/factory_bot)提供了一层薄薄的包装。另一个固执己见的语法:

```
# ./database.yml
---
- type: product
  traits:
    - ready
  params:
    id: 1
    locale: en
    specifics:
      - name:  Color
        value: Black
      - name:  Brand
        value: '##Apple##'

- type: listing
  params:
    id: <%= listing_id %>
    source_id: 1 # bound to the product above
    locale: <%= target %> 
```

在一个规范中，运行种子的方式是:

```
before { seed_fixture "database.yml", listing_id: 42, target: "ru" }

let(:listing) { Listing.find(42) } 
```

现在我们已经删除了规范中的所有细节。我们需要告诉读者的唯一一件事是:*“有一个清单与我们在 fixture 中使用的 id 相同。”*

如果没有 fixtures，数据库准备将如下所示:

```
let(:listing) { create :listing, source: product, locale: "ru" }
let(:product) do
  create :product, :ready, locale: "en", specifics: [
    { "name" => "Color", "value" => "Black" },
    { "name" => "Brand", "value" => "##Apple##" },
  ]
end 
```

它可能看起来比 fixture 短(除非你需要巨大的 jsonb 结构在里面)。但是请记住目标:我想把所有的细节都移出规格。如果我只移动存根和结果，而将数据库对象留在规范中，那么我最终会在测试和它的 fixtures 之间分离设置。这种方法会让读者更难理解发生了什么。

使用“基于夹具”的方法，我们甚至不需要查看规格。我们可以将`database.yml`和`seeds.yml`与他们提供的`output.yml`进行比较。规范本身只是为这些设备提供了一个线索。

请注意，我们使用了所有 fixturama 助手都支持的 [ERB](https://ruby-doc.org/stdlib-2.6.3/libdoc/erb/rdoc/ERB.html) 绑定。您可以以完全相同的方式在种子、存根和负载中使用它。

## 陷阱和变通办法

现在我必须停下来告诉你一个陷阱。为了在 seed fixture 中引用不同的对象，我们使用了具体的 id。让我们看一个来自另一个规范的更详细的例子:

```
---
- type: product
  traits:
    - ready
  params:
    id: 1
    locale: en

- type: listing
  traits:
    - published
  params:
    id: <%= listing_id %>
    source_id: 1
    locale: <%= target %> 
```

在这里，我只需要一份用英语描述的现成产品的公布清单。为了将它们绑定在一起，我对产品的 id 进行了硬编码，但是这可能会给不可靠的、不确定的测试带来问题。

当您将显式 id 与数据库默认生成的 id 组合在一起时，会出现此问题。孤立地看，每个规范都可以很好地工作，但是以随机的顺序运行它们会导致冲突。当然，您可以使用数据库清理器的[截断策略，但是这会导致巨大的性能损失。](https://github.com/DatabaseCleaner/database_cleaner#supported-databases-libraries-and-strategies)

现在我们有了一个更好的解决方案，既干净又优雅。诀窍是[将 id 的 DB 序列的偏移](https://github.com/nepalez/fixturama/blob/master/lib/fixturama/config.rb)相加，以便它们从一个大的数(比如说，`10'000'000`)开始迭代。现在，您可以在夹具中安全地使用较小的 id；默认情况下，数据库永远不会生成相同的记录标识符。

在`fixturama`中，您可以使用特殊设置:

```
# ./rails_helper.rb
RSpec.configure do |config|
  config.before(:suite) { Fixturama.start_ids_from(10_000_000) }
end 
```

## 序列化

我们剩下的只是稍微改进一下序列化。还记得最初提取夹具的方式:

```
let(:output) { YAML.load_file "#{__dir__}/output.yml" } 
```

使用`fixturama`你可以使用加载器做同样的事情:

```
let(:output) { load_fixture "#{__dir__}/output.yml" } 
```

这不是一个很大的改进，但是您不需要显式地指定格式。YAML 和 JSON 格式的文件都会自动序列化；否则，内容将作为纯文本加载。

您现在也可以通过 ERB 绑定值；在许多情况下，这个选项是有用的。

# 测试组织

正如你可能已经注意到的，在上面所有的例子中，我使用了`__dir__`世界来引用当前的文件夹，而不是像`spec/fixtures`一样将设备放入特殊的文件夹中。保持夹具接近规范使得理解和修改更加容易。

通常，我为每个规范创建一个文件夹，并像这样组织文件:

```
specs
  services
    translation
      _spec.rb
      database.yml
      stubs.yml
      output.yml 
```

对于许多边缘情况，您可以更深入:

```
specs
  services
    translation
      _spec.rb
      valid_input
        database.yml
        stubs.yml
        output.yml
      invalid_input
        database.yml
        stubs.yml
        output.yml 
```

现在，您只需要复制粘贴一个子文件夹并修改它的内容，就可以覆盖一个新的 edge 案例了。

# 最后一个例子

让我们看看最后一个例子，在所有这些修改之后，一个规范看起来是什么样的:

```
require "fixturama/rspec"

RSpec.describe ProductTranslator, ".call" do
  subject { described_class.call(listing) }

  before do
    stub_fixture "#{__dir__}/#{edge_case}/stubs.yml"
    seed_fixture "#{__dir__}/#{edge_case}/database.yml", listing_id: 42
  end

  let(:listing) { Listing.find(42) }
  let(:output)  { load_fixture "#{__dir__}/#{edge_case}/output.yml" }

  context "when target locale differs from the product's one" do
    let(:edge_case) { "different_locales" }

    it "updates the listing with translated specifics" do
      expect { subject }.to change { listing.reload.specifics }.to output
    end
  end

  context "when target locale is the same as the product's one" do
    let(:edge_case) { "same_locales" }

    it "updates the listing with the original specifics from the product" do
      expect(GoogleTranslateDiff).not_to receive(:new)
      expect { subject }.not_to change { listng.reload.attributes }
    end
  end
end 
```

我在这里建议的最后一个变化是将期望提取到[共享示例](https://relishapp.com/rspec/rspec-core/docs/example-groups/shared-examples) :

```
require "fixturama/rspec"

RSpec.describe ProductTranslator, ".call" do
  subject { described_class.call(listing) }

  # DEFINITIONS

  before do
    stub_fixture "#{__dir__}/#{edge_case}/stubs.yml"
    seed_fixture "#{__dir__}/#{edge_case}/database.yml", listing_id: 42
  end

  let(:listing) { Listing.find(42) }
  let(:output)  { load_fixture "#{__dir__}/#{edge_case}/output.yml" }

  # EXAMPLES

  shared_examples "translating specifics" do
    it "updates the listing with translated specifics" do
      expect { subject }.to change { listing.reload.specifics }.to output
    end
  end

  shared_examples "skipping translation" do
    it "doesn't call the remote API" do
      expect(GoogleTranslateDiff).not_to receive(:new)
      subject
    end

    it "updates the listing with the original specifics from the product" do
      expect { subject }.not_to change { listng.reload.attributes }
    end
  end

  # CONTEXTS

  context "when target locale differs from the product's one" do
    let(:edge_case) { "different_locales" }
    include_examples "translating specifics"
  end

  context "when target locale is the same as the product's one" do
    let(:edge_case) { "same_locales" }
    include_examples "skipping translation"
  end
end 
```

规格如何准备，以及示例如何建模的所有细节都被隔离在 fixtures 中，并相应地共享示例。

有了这一改变，我们可以通过以下小步骤轻松涵盖新的边缘案例:

*   添加带有相应装置的新文件夹，
*   向规范添加新的上下文，确保选择一个文件夹和预期的行为。

现在，即使覆盖了几十个极限情况，整个测试仍然是可理解和可追踪的。

# 进一步改进

还有更多的改进和优化。我将在这里停下来，只参考弗拉基米尔·德门捷耶夫(又名[帕尔坎](https://dev.to/palkan_tula))的《宝石[测试教授](https://github.com/palkan/test-prof)，在那里你可以找到许多想法。

例如，如果您需要相同的数据库对象用于所有的边缘情况，您可以通过将`before`更改为`before_all` :
来加快准备速度

```
before_all { stub_fixture "#{__dir__}/database.yml" } 
```

这一次，您将有一个耗时的任务，即只创建一次数据库对象。总而言之，我强烈推荐看看宝石并在你的眼镜中使用它，不管是不是基于夹具的。

# 有些限制

我在这篇文章开始时讨论了特定于 API 的问题，但后来探讨了 fixturama 如何成为许多规范的良好解决方案，而与公共接口无关。有时候这是真的；我们使用 gem 测试数据映射器、一些服务对象和与任何外部 API 无关的[策略对象](https://github.com/tram-rb/tram-policy)。不幸的是，YAML 或 JSON 序列化的结构不能涵盖所有必要的情况。它的适用性基于 API 请求和响应的典型格式(文本、JSON、YAML、GraphQL)。

另一个限制来自于测试可能的不规则性。当您必须用相同的结构(seed、stub、准备输入和预期输出)覆盖许多极限情况时，这种方法很有效。当你想用不同的准备步骤测试不同的方法时，它就没那么有用了。

第三个限制与装置的冗长有关。当您从测试中提取大的结构时，它工作得很好，并且当您可以通过比较复杂的`input.yml`和`output.yml`来“编程行为”时，它是合适的，而不必关心规范的具体细节。但是如果你的结构很简单(比如输入上的一个短字符串和输出上的 boolean)，它就变成了一个多余的东西。这就是为什么在我们真正的测试套件中，我们将基于夹具的规格与更传统的规格相结合。

尽管如此，我仍然建议您尝试用 [Fixturama](https://github.com/nepalez/fixturama) 来简化规范，并使您与 API 的交互对您和您的团队来说更加安全、可预测和可理解。

我希望您能尝试一下，并留下您的反馈，告诉我们哪些地方可以做得更好，以及如何通过额外的设置来进一步简化您的测试用例。