@[TOC](4.表单处理精简代码)

### 表单 <form>元素

```python
<h1>{{ question.question_text }}</h1>

{% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}

<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
{% for choice in question.choice_set.all %}
    <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}">
    <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br>
{% endfor %}
<input type="submit" value="Vote">
</form>
```

- 模板实现遍历一组当选按钮，value 属性对应各个Choice的ID,name为choice 选择单选按钮提交表单时, 发送一个POST数据choice=# , # 表示表单ID
- 设置表单的action访问URL
- 设置method="post" 改变服务器端的数据。
- forloop.counter表示for标签已经循环多少次
- 表单验证{%csrf_token%} 防止跨站点请求伪造

### 视图函数引用表单模板

```python
def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except (KeyError, Choice.DoesNotExist):
        return render(request, 'polls/detail.html', {
            'question': question,
            'error_message': "You didn't select a choice.",
        })
    else:
        selected_choice.votes += 1
        selected_choice.save()
        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
```

- request.POST 是一个类字典对象,通过关键子的名字获取提交数据,放回字符串形式
- request.POST['choice']返回Choice的ID,如果没有返回, 引发一个KeyError.代码异常处理重新现实Question 表单和错误信息
-  重定向:HttpResponseRedirect( )方法,只接受一个参数(用户重定向的URL)
- reverse( ) 函数指定要跳转的具体的视图URL

### 使用通用视图,减少代码

> 步骤:
>
> 1. 转换URLconf
> 2. 删除一些旧的、不再需要的视图.
> 3. 给予Django的通用视图引入新的视图

#### 改良URLconf

```python
from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.IndexView.as_view(), name='index'),
    path('<int:pk>/', views.DetailView.as_view(), name='detail'),
    path('<int:pk>/results/', views.ResultsView.as_view(), name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

> DetailView 要求 路径字符串中匹配模式的名称已经由<question_id> 改为<pk>

#### 改良视图

```python
from django.http import HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse
from django.views import generic

from .models import Choice, Question


class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        """Return the last five published questions."""
        return Question.objects.order_by('-pub_date')[:5]


class DetailView(generic.DetailView):
    model = Question
    template_name = 'polls/detail.html'


class ResultsView(generic.DetailView):
    model = Question
    template_name = 'polls/results.html'


def vote(request, question_id):
    ... # same as above, no changes needed.
```

1. 通用视图: ListView 和 DetailView
2. ListView 视图特性
   *  现实一个对象列表
   *  默认模板**<app name>/<model name>_list.html**
   * template_name='指定自定义模板'
   * ListView 默认自动生成的context变量为question_list. 提供覆盖context_object_name ='指定变量名'

1. DetailView视图特性

- DetailView: 现实一个特定类型对象的详细页面, 要求从URL中捕获名为“pk”的主键值,所以我们为通用视图把question_id 改成pk

- DetailView question变量自动提供默认context变量

  

