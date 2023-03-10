# 按关系排序时，acts-as-tag able-on 导致 SQL 错误

> 原文：<https://dev.to/drbragg/acts-as-taggable-on-causing-sql-errors-when-ordered-by-relation-3bc2>

我在使用 tagged_with 和 any 标志时遇到了一个奇怪的问题。

我的模型看起来像这样:

```
class Document < ApplicationRecord
  ...
  belongs_to :phase, optional: true
  ...

  scope :search_by_drug, ->(drug) { tagged_with(drug, on: :drug) if drug.present? }
  scope :search_by_area, ->(area) { tagged_with(area, on: :area) if area.present? }
  scope :search_by_type, ->(types) { tagged_with(types, on: :doc_type, any: true) if types.present? }
  scope :search_by_tags, ->(tags) { tagged_with(tags, on: :tags, any: true) if tags.present? }
  ...

  acts_as_taggable_on :tags, :drug, :area, :doc_type
  ...
end 
```

在我的控制器中，这是我查询的地方:

```
class DocumentsController < ApplicationController
  include Pagy::Backend
  ...

  # GET /documents
  def index
    @pagy, @documents = pagy(
      Document.includes(:phase, :taggings)
              .search_by_drug(params[:drug])
              .search_by_area(params[:area])
              .search_by_type(params[:types])
              .search_by_tags(params[:tags])
              .order(sort_order),
      items: 5
    )
  end
  ...

  private

  def sort_by
    return 'phases.name' if params[:sort] == 'phase'

    @sort_by = params[:sort] || 'date'
  end

  def order
    @order = params[:order] || 'asc'
  end

  def sort_order
    "#{sort_by}  #{order}"
  end
  ...
end 
```

当我使用标签或类型进行搜索，并尝试对 phase 列进行排序时，会出现如下所示的错误:

`ERROR: for SELECT DISTINCT, ORDER BY expressions must appear in select list
LINE 1: ...) AND "taggings"."context" = 'doc_type') ORDER BY phases.nam...`。

我做了很多搜索，但我能想到的最接近的问题是 github 上的一个旧问题，但它被关闭了，因为(据他们说)他们已经[合并了一个 PR](https://github.com/mbleigh/acts-as-taggable-on/pull/570) 修复了这个问题，他们的问题中没有什么新的东西。

有人能给我指一下正确的方向吗？