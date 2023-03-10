# 多方法调用期望的优雅匹配器

> 原文：<https://dev.to/arturmartsinkovskyi/elegant-matcher-for-multiple-method-call-expectations-33o6>

最近，我在研究一些测试代码时，发现了一个令人讨厌的重复出现的模式，它有些笨拙，令人不满意。我讲这样一个案例:

```
 expect(user.uid).to eq('hpqeqa12asdq1')
  expect(user.country).to eq('Germany')
  expect(user.street_number).to eq('9')
  expect(user.street_address).to eq('Somestrasse')
  expect(user.balance(from: 1.week.ago)).to eq(42000) 
```

当你看着那堵代码墙时，你会看到一些重复出现的模式在你眼前闪耀，阻碍你看到本质。每次都是`expect(user.***).to eq(...`。这个代码块没有给表达式带来多少价值，只是样板文件。虽然，有人可能会说这样的测试用例不符合编写良好的规范的含义，因为它们确实一次测试了多个东西，但是这并不是一个糟糕代码的例子。它检查最有可能在一个步骤中自动插入的值，因此单独测试它们是浪费时间和更多的样板代码，不会使理解假定的断言更容易掌握。我一直在思考如何让这样一个表达式更加数据驱动，而不是充斥着噪音。这是我得出的结果:

```
### Implementation ###

def expect_responses(object, **fields)
  fields.reduce(true) do |acc, (field, expectation)|
    acc && if expectation.is_a?(WithArguments)
              object.send(
                field, 
                *expectation.arguments
              ) == expectation.expected_result
           else
             object.send(field) == expectation
           end
  end
end

WithArguments = Struct.new(:arguments, :expected_result)

def with_arguments(*args, to_have_result:)
  WithArguments.new(args, to_have_result)
end

### Example ###

expect_responses(
  user,
  uid: "hpqeqa12asdq1",
  country: "Germany",
  street_number: '9',
  street_address: 'Somestrasse',
  balance: with_arguments({from: 1.week_ago}, to_have_result: 42000)
) # => true 
```

这个看起来更干净，而且我还添加了一种向方法传递参数的方式。为了让这篇文章更容易理解，这段代码返回一个布尔值，并使用简单的比较，在真正的 RSpec 代码中，它将运行在 expectations 上，并使用匹配器来正确地比较复合数据结构。我试图模仿 RSpec DSL 的常见风格，但我不确定这个复合断言的命名是否合适，但如果你对这个主题或这篇文章的任何部分有更好的建议，请在下面留下评论。你认为这样的补充值得吗？你会用它吗？为什么？为什么不呢？