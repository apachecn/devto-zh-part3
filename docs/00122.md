# 如何在 Rails 中定义几乎全局变量和几乎顶层方法

> 原文：<https://dev.to/hanachin/how-to-define-almost-global-variable-and-almost-top-level-method-in-rails-2nb1>

```
# app/models/application_container.rb
require 'singleton'

class ApplicationContainer
  include Singleton
end 
```

```
# app/controllers/application_controler.rb
class ApplicationController < ActionController::Base
  delegate_missing_to :container

  cattr_accessor :container, default: ApplicationContainer.instance
end 
```

```
# app/models/application_record.rb
class ApplicationRecord < ActiveRecord::Base
  self.abstract_class = true

  delegate_missing_to :container

  cattr_accessor :container, default: ApplicationContainer.instance
end 
```