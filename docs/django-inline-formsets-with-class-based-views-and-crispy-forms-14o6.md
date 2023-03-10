# Django 内嵌表单集，具有基于类的视图和清晰的表单

> 原文：<https://dev.to/zxenia/django-inline-formsets-with-class-based-views-and-crispy-forms-14o6>

最近，我在我的一个 Django 项目中使用了内联表单集，我非常喜欢它的工作方式。我决定分享我集成基于类的视图[内联表单集](https://docs.djangoproject.com/en/2.1/topics/forms/modelforms/#inline-formsets)、[脆表单](https://django-crispy-forms.readthedocs.io/en/latest/)和[django-dynamic-formset](https://github.com/elo80ka/django-dynamic-formset)jQuery 插件的例子。当我研究这个话题时，我没有找到很多例子，Django 文档在这个问题上也不是很全面，所以我把这个帖子放在一起，给那些想尝试这个解决方案的人和我未来的自己。

首先，**为什么要使用内联表单集** :
来允许用户通过被引用对象的创建/更新视图创建和更新相关的外键对象，所有这些都在一个页面上完成。

假设我们有一个集合，可以有多种语言的标题，但我们不知道用户将提供多少标题翻译。我们希望允许用户添加尽可能多的标题，只需单击“添加”按钮，在集合创建表单中添加一个新行。这是我们模特的样子。

*models.py:*

```
from django.db import models
from django.contrib.auth.models import User

class Collection(models.Model):
    subject = models.CharField(max_length=300, blank=True)
    owner = models.CharField(max_length=300, blank=True)
    note = models.TextField(blank=True)
    created_by = models.ForeignKey(User,
        related_name="collections", blank=True, null=True,
        on_delete=models.SET_NULL)

    def __str__(self):
        return str(self.id)

class CollectionTitle(models.Model):
    """
    A Class for Collection titles.

    """
    collection = models.ForeignKey(Collection,
        related_name="has_titles", on_delete=models.CASCADE)
    name = models.CharField(max_length=500, verbose_name="Title")
    language = models.CharField(max_length=3) 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们为 CollectionTitle 创建一个表单和一个包含父模型集合和 FK 相关模型 CollectionTitle 的表单集(使用 [inlineformset_factory](https://docs.djangoproject.com/en/2.1/ref/forms/models/#inlineformset-factory) )。

*forms . py*T2】

```
from django import forms
from .models import *
from django.forms.models import inlineformset_factory

class CollectionTitleForm(forms.ModelForm):

    class Meta:
        model = CollectionTitle
        exclude = ()

CollectionTitleFormSet = inlineformset_factory(
    Collection, CollectionTitle, form=CollectionTitleForm,
    fields=['name', 'language'], extra=1, can_delete=True
    ) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将这个表单集添加到 CollectionCreate 视图中。

*views.py:*

```
from .models import *
from .forms import *
from django.views.generic.edit import CreateView, UpdateView
from django.urls import reverse_lazy
from django.db import transaction

class CollectionCreate(CreateView):
    model = Collection
    template_name = 'mycollections/collection_create.html'
    form_class = CollectionForm
    success_url = None

    def get_context_data(self, **kwargs):
        data = super(CollectionCreate, self).get_context_data(**kwargs)
        if self.request.POST:
            data['titles'] = CollectionTitleFormSet(self.request.POST)
        else:
            data['titles'] = CollectionTitleFormSet()
        return data

    def form_valid(self, form):
        context = self.get_context_data()
        titles = context['titles']
        with transaction.atomic():
            form.instance.created_by = self.request.user
            self.object = form.save()
            if titles.is_valid():
                titles.instance = self.object
                titles.save()
        return super(CollectionCreate, self).form_valid(form)

    def get_success_url(self):
        return reverse_lazy('mycollections:collection_detail', kwargs={'pk': self.object.pk}) 
```

Enter fullscreen mode Exit fullscreen mode

除了在 *get_context_data()* 中应该传递实例对象之外，CollectionUpdate 视图看起来类似。

*views.py:*

```
def get_context_data(self, **kwargs):
        data = super(CollectionUpdate, self).get_context_data(**kwargs)
        if self.request.POST:
            data['titles'] = CollectionTitleFormSet(self.request.POST, instance=self.object)
        else:
            data['titles'] = CollectionTitleFormSet(instance=self.object)
        return data 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们必须用表单集创建 CollectionForm，并将其呈现为其中的字段。这并不简单，因为 crispy-forms 不像 Div 或 HTML 那样有 formset 的 layout 对象。
[最佳方案](https://stackoverflow.com/a/22053952/7101197)(万分感谢！)就是创建一个自定义的脆皮布局对象。

*custom _ layout _ object . py:*

```
from crispy_forms.layout import LayoutObject, TEMPLATE_PACK
from django.shortcuts import render
from django.template.loader import render_to_string

class Formset(LayoutObject):
    template = "mycollections/formset.html"

    def __init__(self, formset_name_in_context, template=None):
        self.formset_name_in_context = formset_name_in_context
        self.fields = []
        if template:
            self.template = template

    def render(self, form, form_style, context, template_pack=TEMPLATE_PACK):
        formset = context[self.formset_name_in_context]
        return render_to_string(self.template, {'formset': formset}) 
```

Enter fullscreen mode Exit fullscreen mode

下一步是添加一个模板来呈现表单集。准确地说，我想呈现的是:对于每个新标题-一行与字段“名称”和“语言”和一个按钮“删除”删除行(并在更新集合时删除数据库中的数据)，和一个按钮行下-为新标题添加另一行。
我正在使用[django-dynamic-formset](https://github.com/elo80ka/django-dynamic-formset)jQuery 插件来动态添加更多的行。
我建议使用*前缀* ( [docs](https://github.com/elo80ka/django-dynamic-formset/blob/master/docs/usage.rst#formset-options) )，以便为所有内联表单集情况提供一个表单集模板(例如，当您在一个表单中添加几个内联表单集时)。Formset 前缀是被引用类的 *related_name* 。所以在我的例子中是“has_titles”。

*formset.html:*

```
 {% load crispy_forms_tags %}
<table>
{{ formset.management_form|crispy }}

    {% for form in formset.forms %}
            <tr class="{% cycle 'row1' 'row2' %} formset_row-{{ formset.prefix }}">
                {% for field in form.visible_fields %}
                <td>
                    {# Include the hidden fields in the form #}
                    {% if forloop.first %}
                        {% for hidden in form.hidden_fields %}
                            {{ hidden }}
                        {% endfor %}
                    {% endif %}
                    {{ field.errors.as_ul }}
                    {{ field|as_crispy_field }}
                </td>
                {% endfor %}
            </tr>
    {% endfor %}

</table>
<br>
<script src="//ajax.googleapis.com/ajax/libs/jquery/2.1.3/jquery.min.js">
</script>
<script src="{% static 'mycollections/libraries/django-dynamic-formset/jquery.formset.js' %}">
</script>
<script type="text/javascript">
    $('.formset_row-{{ formset.prefix }}').formset({
        addText: 'add another',
        deleteText: 'remove',
        prefix: '{{ formset.prefix }}',
    });
</script> 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以通过同时使用现有的布局对象和自定义 Formset 对象来为 CollectionForm 构造自己的表单布局。

*forms.py:*

```
from crispy_forms.helper import FormHelper
from crispy_forms.layout import Layout, Field, Fieldset, Div, HTML, ButtonHolder, Submit
from .custom_layout_object import *

class CollectionForm(forms.ModelForm):

    class Meta:
        model = Collection
        exclude = ['created_by', ]

    def __init__(self, *args, **kwargs):
        super(CollectionForm, self).__init__(*args, **kwargs)
        self.helper = FormHelper()
        self.helper.form_tag = True
        self.helper.form_class = 'form-horizontal'
        self.helper.label_class = 'col-md-3 create-label'
        self.helper.field_class = 'col-md-9'
        self.helper.layout = Layout(
            Div(
                Field('subject'),
                Field('owner'),
                Fieldset('Add titles',
                    Formset('titles')),
                Field('note'),
                HTML("<br>"),
                ButtonHolder(Submit('submit', 'save')),
                )
            ) 
```

Enter fullscreen mode Exit fullscreen mode

*collection _ create . html:*

```
{% extends "mycollections/base.html" %}
{% load crispy_forms_tags %}
{% block content %}
<div class="container">
    <div class="card">
        <div class="card-header">
            Create collection
        </div>
        <div class="card-body">
             {% crispy form %}
        </div>
    </div>
</div>
{% endblock content %} 
```

Enter fullscreen mode Exit fullscreen mode

现在一切就绪，我们只需点击一次保存按钮，就可以在一个页面上以一种形式创建一个集合及其标题。

[![inline-formsets](img/56a50b293fe6d0f9e2f4293af417d46f.png "Inline formsets")](https://res.cloudinary.com/practicaldev/image/fetch/s--zCtBMru1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/olhglncci00mhx9w508t.png)

这个帖子的源码是[这里](https://github.com/zxenia/example-inline-formsets)。

感谢[这篇很棒的博文](https://medium.com/@adandan01/django-inline-formsets-example-mybook-420cc4b6225d)，它帮助我整理了内联表单集解决方案。