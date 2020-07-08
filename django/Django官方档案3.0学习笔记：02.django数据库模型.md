@[TOC](django官方文档3.0学习笔记 02])

# 

### 视图的创建

- Django中，视图表现为一个python函数（polls.py）
- 根据用户请求的URL来使用那个视图（urls.py）

### 匹配变量

```python
# urls.py中配置URL
urlpatterns = [
    path('<int:question_id>/', views.detail, name='detail'),
]
```



 ```python
# views.py中引用变量
def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)
 ```

 ### 模板渲染和数据处理

- 模版存放目录（polls/templates/）

- 模版载入配置（传递字典数据context）

  ```python
  def index(request):
      latest_question_list = Question.objects.order_by('-pub_date')[:5]
      template = loader.get_template('polls/index.html')
      context = {
          'latest_question_list': latest_question_list,
      }
  	return HttpResponse(template.render(context, request))
  ```

- 快捷函数重写index()视图

  ```python
  def index(request):
      latest_question_list = Question.objects.order_by('-pub_date')[:5]
      context = {'latest_question_list': latest_question_list}
      return render(request, 'polls/index.html', context)
  ```

### 抛出错误404

```python
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
```

- 快捷函数get_object_or_404( )

  ```python
  question = get_object_or_404(Question, pk=question_id)
  ```

### 模板系统

- 循环调用： {% for  ... in .. %} {% endfor %}

- 变量：{{ xxx }}

- 模板中替换URL

  替换前

  ```python
  <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
  ```

  替换后

  ```python
  <li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
  ```

* URL 名称添加命名空间，视图不唯一，指向具体的应用视图

  无指向

  ```python
  <li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
  ```

  指向具体命名空间

  ```python
  <li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
  ```