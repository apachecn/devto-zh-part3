# Ruby & Rails 的结构化 RSpec

> 原文：<https://dev.to/edwinthinks/structured-rspec-for-ruby--rails-1icb>

### 简介

在我早期的软件开发中，我很少关注为我的产品应用程序构建测试的实践。当然，在部署新代码期间，我的血压、压力和焦虑水平达到了新的峰值。我不测试的原因我相信在社区中是普遍的。认为“写测试时间太长”。我已经意识到，这种感觉可能是由于缺乏结构和驱动测试/规范创建的底层方法造成的。

在这篇文章中，我分享了我为 Ruby & Ruby On Rails 项目创建规格/测试的思考过程和方法(/w 示例)。我的方法深受我以前的导师和更好的规范的影响。我希望你(读者)在阅读完这篇文章后，能够将测试/规范创建添加到他们的日常工作流程中。

让我们在部署新代码时感觉良好:)

### 你打算`describe`什么是你的`subject`？

如果不使用`subject`或`describe`，很难确切知道规范作者的意图。我利用规范中的`describe`来跟踪我正在测试的代码/方法的部分，并利用`subject`来定义它。下面是一个描述某个`Animal`对象和一个名为`#speak` :
的实例方法的规范示例

```
describe AnimalSpec do
  describe '#speak' do
    # Any call to `subject` will execute & return the output of the code block.
    # We now know that the author intends to test the output of #speak on varying
    # instances of `Animal` 
    subject { Animal.new.speak }

    it "should return an animal noise" do
      # The code beneath then does something more like: 
      # expect(Animal.new.speak).to eq('an animal noise')
      expect(subject).to eq('an animal noise')
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这让东西保持整洁，更重要的是*专注于*。也就是说，我们在这里测试不同的`Animal`实例的输出。虽然这个测试还没那么有趣。让我们利用下一节中的`let`来匹配不同的`context`，并最终测试`subject`的各种输出

### [T3 让我们用`let`来设置`context`](#lets-use-raw-let-endraw-to-setup-the-raw-context-endraw-)

为了扩展我们的规范以覆盖不同的`Animal`实例和它们对应的`#speak`输出，我们将利用`let`和`context`。`let`方法允许我们修改方法的输出值。而`context`是用来用文字描述`subject`所处的情况。下面是一个具有相同`Animal`规格的示例，但是这次通过引入`animal_type`和`angry`参数添加了输出的方差:

```
describe AnimalSpec do
  describe '#speak' do
    subject { Animal.new(animal_type: animal_type, angry: angry).speak }

    context "when the animal_type is 'duck'" do
      # We utilize `let` to change the inputs of our subject to match
      # the situation we are describing in our context.
      let(:animal_type) { 'duck' }

      context "and the animal is not angry" do
        # Moreover, you can utilize `let` to continue to describe the
        # context further. 
        let(:angry) { false } 

        it "should return 'quack'" do
          expect(subject).to eq('quack')
        end
      end

      context "and the animal is angry" do
        let(:angry) { true }

        it "should return 'QUACK!'" do
          expect(subject).to eq('QUACK!')
        end
      end
    end

    context "when the animal_type is 'dog'" do
      let(:animal_type) { 'dog' }

      context "and the animal is not angry" do
        let(:angry) { false } 

        it "should return 'woof'" do
          expect(subject).to eq('woof')
        end
      end

      context "and the animal is angry" do
        let(:angry) { true }

        it "should return 'WOOF!'" do
          expect(subject).to eq('WOOF!')
        end
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`let`和`context`，我们可以更清楚地显示我们的情况和我们`Animal`实例的变化特征。

### 将其应用于 Ruby On Rails 项目

既然我们已经介绍了普通代码的基本构建规范。现在让我们更深入一点，在使用 Rails 时可能会遇到的一些常见情况中实践这一点。

#### 模型验证

假设您有一个`User`数据库模型，它需要一个有效的唯一电子邮件和一个密码。我们可以首先创建一个表示“验证”的`describe`块和一个在执行`#valid?`后返回`#errors`的主题。在这种方法中，我在最外层的上下文中定义了用户的有效属性，并用`let`将它们修改成无效状态，我们可以测试这种状态是否会产生预期的错误:

```
 describe User do
  describe 'validations' do
    # I want to execute the `#valid?` operation to populate the errors and
    # then the errors for the following conditions. 
    subject { user.valid?; user.errors }

    let(:user) { User.new(user_attrs) }

    # Note - `let` (and `subject`) also accepts code blocks
    let(:user_attrs) do
      {
        email: email,
        password: password
      }
    end
    let(:email) { 'fake_valid_email@example.com' }
    let(:password) { 'fake_password' }

    context 'when the user_attributes are valid' do
      # No need to use `let` here as the outermost is already defining 
      # the valid attributes.
      it 'should have no errors' do
        expect(subject).to be_empty
      end        
    end

    context 'when the email is nil' do
      # This overwrites the value in the outer context.
      let(:email) { nil }

      # Be descriptive about what your are expecting to be returned
      # by your `subject`
      it "should have an error that says email can't be blank" do
        # `subject` is the errors hash as defined above. So we can 
        # dig in and see what errors are included.
        expect(subject[:email]).to include("can't be blank")
      end
    end

    context 'when the email is in an invalid format' do
      let(:email) { 'not_a_email' }

      it "should have an error that says email is invalid" do
        expect(subject[:email]).to include("is invalid")
      end
    end

    context 'when the email has been taken already' do
      # To trigger the expected uniqueness error we create User prior to calling
      # `subject` with the same email
      before do
        User.create!(email: email, password: password)
        # or also `User.create!(user_attrs)` works
      end

      it 'should have an error that says has already been taken' do
        expect(subject[:email]).to include('has already been taken')
      end
    end

    context 'when the password is nil' do
      let(:password) { nil }

      it "should have a error that says password can't be blank" do
        expect(subject[:password]).to include("can't be blank")
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这种方法为我们提供了处理各种错误状态的指导方针，这些错误状态是`User`的一个实例可能处于的状态。这些想法可以扩展到更大的概念或情况，只需改变将它配置成不同状态的`subject`和`let`。

#### 副作用或工作排队

偶尔你可能会遇到这样的情况，你并不关心你的`subject`的返回值，而是关心它的副作用。例如，假设您在`User`上有一个名为`#reset_password`的方法，其中您只关心`User`的实例现在通过一个变更事件重置了它们的密码。这里我们利用了`-> { code }` (lambda)语法，让我们对执行代码的效果而不是输出更感兴趣:

```
describe User do
  describe '#reset_password' do
    # The subject is now a lambda which you can trigger via `subject.call`
    # It will become more apparent why we want to do this in the next sections.
    subject { -> { user.reset_password } }

    let(:user) { User.create!(password: original_password) }
    let(:original_password) { 'original_password' }

    it "should change the password (method 1)" do
      # Check that the password is initially set to what you expect
      expect(user.password).to eq(original_password)
      subject.call
      # Use reload to ensure you get the latest copy of the `User` record
      expect(user.reload.password).not_to eq(original_password)
    end

    # Or asserting that the change happened via `change` matcher
    it "should change the password (method 2)" do
      expect { subject.call }.to change(user.reload.password)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，假设我们想要在`#reset_password`上添加一些额外的功能，使我们能够指定密码重置指令应该通过电子邮件发送给该用户。让我们将该参数设为`send_instructions`，并利用 mocking 来检查负责发送电子邮件的作业或类是否已被调用。让我们称这个工作为`PasswordResetSender` :

```
describe User do
  describe '#reset_password' do
    subject { -> { user.reset_password(send_instructions: send_instructions } }

    # Let's mock out the class that would be triggering so we can see that it
    # was called were we expect it to be.
    before do
      allow(PasswordResetSender).to receive(:deliver)
    end

    context "when send_instructions is set to true" do
      let(:send_instructions) { true }

      it "should trigger a sending the password reset instructions" do
        subject.call
        # Check that the class responsible for sending the password instruction 
        # had been sent.
        expect(PasswordResetSender).to have_received(:deliver)
      end
    end

    context "when send_instructions is set to false" do
     let(:send_instructions) { false }

      it "should not trigger a sending the password reset instructions" do
        subject.call
        # Vice a versa, we want to ensure sending the password instruction does
        # not occur.
        expect(PasswordResetSender).not_to have_received(:deliver)
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在本文中，我介绍了将 RSpec 应用于 Ruby 或 Ruby On Rails 项目的思维过程和方法。我希望这对于将测试/规范添加到您的常规工作流程中有所启发和启发。

这只是众多提供如何有效应用 RSpec 的技巧的资源中的一个。我最喜欢的灵感来源之一是 [BetterSpecs](http://www.betterspecs.org/) 。

如果您有任何问题或其他情况想知道如何有效地找出答案，请随时联系我:)。