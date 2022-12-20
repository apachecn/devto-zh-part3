# 发展到因果等级

> 原文：<https://dev.to/daviducolo/dev-to-karma-rank-13kc>

你好

只是为了好玩，我实现了一个非常简单的算法，允许根据实际的开发到 v0 API 计算开发到网站的“因果”等级。

这是一个简单的 ruby 代码(你必须安装 rest-client gem )

```
class DevToKarma 
  API_URL = 'https://dev.to/api'

  def initialize(username)
    @username = username
  end

  attr_reader :username

  def posts
    page = 1
    result = []

    loop do
      response = RestClient.get "#{API_URL}/articles?username=#{username}&page=#{page}"
      posts = JSON.parse(response.body)
      result << posts

      page = page + 1
      break if posts.size == 0 
    end

    result.flatten
  end

  def user
    response = RestClient.get "#{API_URL}/users/by_username?url=#{username}"
    JSON.parse(response.body)
  end

  def karma
    joined_at_timestamp = Time.parse(user.dig('joined_at')).to_i 

    posts_count = posts.size
    total_comments_count = posts.map { |r| r['comments_count'] }.sum
    total_positive_reactions_count = posts.map { |r| r['positive_reactions_count'] }.sum

    karma = ( posts_count + total_comments_count + total_positive_reactions_count ).to_f / ( joined_at_timestamp / 1000 / 1000 ) * 100

    karma.to_i
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

示例

```
 DevToKarma.new('daviduco').karma # My own karma
  => 23 
```

Enter fullscreen mode Exit fullscreen mode

```
 DevToKarma.new('ben').karma # Ben Halper karma
  => 3529 
```

Enter fullscreen mode Exit fullscreen mode

显然这是一个“愚蠢的”和小的数学解决方案。许多其他要考虑的因素和变量都被忽略了。但这是一个开始！