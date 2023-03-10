# RSpec 嵌套主题

> 原文：<https://dev.to/chilcutt/rspec-nested-subjects-593l>

[RSpec 主题功能](https://relishapp.com/rspec/rspec-core/v/3-8/docs/subject/explicit-subject)对于简化您的规范设置和直奔主题非常有用。我非常喜欢使用一个明确的主题，因为它有助于在你写规格说明书的时候描述什么功能正在被测试。最近，我了解到当使用一个命名主题时，可以嵌套你的主题。

<figure>

[![programming](img/9289e4abca6b07623e24d64613991fc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6zJemXg1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/12000/1%2AofMfrqFMfpaTH_RMvI_RQA.jpeg)

<figcaption>Photo by [Clément H](https://unsplash.com/photos/95YRwf6CNw8?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

* * *

`subject`语法允许你使用[一行语法](https://relishapp.com/rspec/rspec-core/v/3-8/docs/subject/one-liner-syntax)来编写一个测试，它可以真正清理一个规范文件。让我们举一个 Rails 模型的简单例子:

```
describe Car do
  let(:car) { Car.new }

  it "is a vehicle" do
    expect(car).to be_a(Vehicle)
  end
end 
```

对于这个微不足道的案例，您会注意到我们的期望与测试案例的描述完全相符。这可能是`subject` :
的一个很好的用例

```
describe Car do
  subject { Car.new }

  it { is_expected.to be_a(Vehicle) }
end 
```

上面将测试相同的功能，并在规范运行时留下非常相似的规范输出。

[![failures](img/59b061480bde7eeb1ff46d8f94670f99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OsUPs57g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3624/1%2A8CMdd5YkCsObQJMDTy9B_A.png) 

<figure>

<figcaption>故障看起来略有不同，但却说明了同一点。</figcaption>

</figure>

您也可以为测试的不同块重新定义您的`subject`。让我们为我想要测试的实例方法设置`subject`:

```
describe Car do
  subject { Car.new }

  it { is_expected.to be_a(Vehicle) }

  describe "#parked?" do
    subject { Car.new.parked? }

    it { is_expected.to eq(false) }
  end
end 
```

毒品。通过重新定义`subject`,我们可以在任何需要的地方使用单行语法。

最后一个技巧是我们可以添加一个名字到初始的`subject`中，这样我们就可以在规范中引用它:

```
describe Car do
  subject(:car) { Car.new }

  it { is_expected.to be_a(Vehicle) }

  describe "#parked?" do
    subject { car.parked? }

    it { is_expected.to eq(false) }
  end
end 
```

如果封闭块中的`subject`有一些复杂的设置:
，我发现这特别有用

```
describe Car do
  let(:driver) { Person.new }
  let(:front_seat) { Person.new }
  subject(:car) { Car.new(driver: driver, front_seat: front_seat) }

  it { is_expected.to be_a(Vehicle) }

  describe "#parked?" do
    subject { car.parked? }

    it { is_expected.to eq(false) }
  end
end 
```

现在，我们可以利用任何早期的设置，同时仍然在每个上下文中拥有不同主题的好处。

* * *

*向[扎克·马里斯卡尔](https://twitter.com/zackmariscal)大声欢呼，感谢他帮助我编辑和改进这篇文章以及我的写作。*