# 为服务对象的使用开发 GEM

> 原文：<https://dev.to/iagosilva/developing-a-gem-for-use-of-service-objects-11b0>

> 服务对象有助于保持控制器和模型的简洁和可读性

### 简单例子

*   以前

```
# app/controllers/post_releases_controller.rb
class PostReleasesController < ApplicationController

  # [...]

  def update
    @post.prepare_to_release                      # <--
    if @post.update(released_at: Date.current)    # <--
      redirect_to root_path
    else
      render :edit
    end
  end

  # [...]

end 
```

*   在...之后

```
# app/controllers/post_releases_controller.rb
class PostReleasesController < ApplicationController

  # [...]

  def update
    if ReleasePost.call(post: @post)    # <--
      redirect_to root_path
    else
      render :edit
    end
  end

  # [...]

end 
```

```
# app/services/release_post.rb
class ReleasePost < ServiceIt::Base
  def perform
    @post.prepare_to_release
    @post.update(released_at: Date.current)
  end
end 
```

非常欢迎反馈！

下面是它的**Github**:【https://github.com/iago-silva/service_it】T2