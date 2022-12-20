# RSpecの有 _ 内容吗？と有内容吗？は違う

> 原文：<https://dev.to/risacan/rspec-havecontent-hascontent-5191>

# `have_content?`和`has_content?`在 RSpec 中是不同的

RSpecのsystem specで `have_content?` と `has_content?` を間違えていて、ドハマりしました。 💥💥

# Conclusion

`has_content?`和`have_content?`是两回事！ 用于条件分支时要注意！

# environment

```
🌸 ruby -v
ruby 2.5.1p57 (2018-03-29 revision 63029) [x86_64-darwin17]
🌸 bin/rails -v
Rails 5.2.0
🌸 be rspec -v
RSpec 3.7
  - rspec-core 3.7.1
  - rspec-expectations 3.7.0
  - rspec-mocks 3.7.0
  - rspec-rails 3.7.2
  - rspec-support 3.7.1 
```

# 问题的代码

在 system spec 内写了这样的代码。

```
~略~

visit user_group_path(user_group)

10.times do 
  break if have_content?("愛島セシル")

  sleep 1
  visit user_group_path(user_group)
end

expect(page).to have_content("愛島 セシル")

~略~ 
```

正在进行异步处理的页面。
处理结束后更新页面将反映结果。
是在出现目标字符串之前更新页面，确认该字符串存在的代码。
用`have_content?("愛島セシル")`确认字符串的存在，如果存在字符串则 break，如果没有字符串则等待 1 秒后更新页面。
在下一个处理`expect(page).to have_content("愛島 セシル")`中正在测试页面上是否有“爱岛塞西尔”这个字符串。

这个代码在 CI 上掉了好几次。

# Conclusion

Ruby 的`nil`和`false`为 false，其他为 true。
这次的情况下，根据`have_content?("愛島 セシル")`，返回了`#<RSpec::Matchers::BuiltIn::Has:0x00007f8ac84536e8 @args=["愛島 セシル"], @block=nil, @method_name=:have_content?>`的对象。
也就是说，`if have_content?("愛島 セシル")`为 true，不管“爱岛塞西尔”的描绘如何，都退出了循环！
刷新页面的代码一次也没读过

# 详情

RSpecの仕様で
`have_なんとか` と書くと、内部で `なんとか?` が呼び出されます。
[https://github . com/rspec/rspec-expectations/blob/3-8-maintenance/lib/rspec/matchers/built _ in/has . Rb # l71](https://github.com/rspec/rspec-expectations/blob/3-8-maintenance/lib/rspec/matchers/built_in/has.rb#L71)

```
def predicate
          # On 1.9, there appears to be a bug where String#match can return `false`
          # rather than the match data object. Changing to Regex#match appears to
          # work around this bug. For an example of this bug, see:
          # <https://travis-ci.org/rspec/rspec-expectations/jobs/27549635>
          @predicate ||= :"has_#{Matchers::HAS_REGEX.match(@method_name.to_s).captures.first}?"
        end 
```

# 有 _ 内容？

```
pry > have_content?("愛島 セシル")
=> #<RSpec::Matchers::BuiltIn::Has:0x00007f8ac84536e8 @args=["愛島 セシル"], @block=nil, @method_name=:have_content?> 
```

# 已经 _ 内容？

```
pry > has_content?("愛島 セシル")
=> true

# has_text? と一緒
pry > has_text?("愛島 セシル")
=> true 
```