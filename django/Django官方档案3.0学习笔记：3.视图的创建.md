@[TOC](django官方文档3.0学习笔记 02])

# Django应用01 02 

---

## Django 安装创建项目

1. 安装
2. 创建项目
3. 运行服务器
4. 新建应用
5. 新建视图
6. path函数关联route和view

### 1. 安装

```shell
$ python3 install django[==指定版本]

# 查询版本 
$ python -m django --version
```

### 2. 创建项目

```shell
$ django-admin startproject projectName
## 生成文件 __init__.py
##        settings.py # 项目的配置文件
##        urls.py   # URL 声明，就像你网站的“目录”
##        asgi.py   # 运行在 ASGI 兼容 ..入口
##        wsgi.py   # 运行在 WSGI 兼容 ..入口
```

### 3. 运行服务器

```shell
# 运行服务器,设置监听端口 不设置默认8000端口
$ python manage.py runserver [ip]:[port]
# 0:8000 表示 0.0.0.0:8000
```

### 4. 创建应用

```shell
$ python manage.py startapp appname
##    __init__.py
##    admin.py     # 后台管理
##    apps.py      # 
##    migrations/  # 存储迁移文件
##        __init__.py
##    models.py
##    tests.py
##    urls.py
##    views.py
```

### 5. 创建视图和路由关联

- 创建视图 

  ```python
  ## appName/views.py文件中新建函数
  from django.http import HttpResponse
  
  
  def index(request):
      return HttpResponse("Hello, world. You're at the polls index.")
  ```

- urls.py配置 ,path(route,view)函数

  ```python
  urlpatterns = [
      path('polls/', include('polls.urls')),
      path('admin/', admin.site.urls),
  ]
  ## path() 函数关联route和view 旧版本实用url函数
  # 必选参数:route 路由 匹配url的准则
  # 必选参数:view 关联视图函数 
  # 可选参数:name 对URL取名django任意地方引用URL
  # 可选参数:kwargs 给视图函数传递参数
  # include() 允许引用其它应用的 URLconfs(urls.py文件)
  ```

## Django 数据库

---

1. Django配置数据库配置 setting.py 中DATABASES 和INSTALLED_APPS

2. 创建数据库模型

3. 激活数据库模型

4. 交互式命令操作

5. Django管理界面,根据模型创建全自动后台界面

### 1. 配置文件配置

- DATABASES 配置

  ```python
  DATABASES = {
      'default': {
          'ENGINE': 'django.db.backends.sqlite3',
          'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
      }
  }
  
  ## django 默认值为sqlite3数据库
  # ENGINE--参数可选
  # sqlite3数据库 'django.db.backends.sqlite3'，
  # postgresql数据库 'django.db.backends.postgresql'，
  # mysql数据库 'django.db.backends.mysql'，
  # oracle数据库 'django.db.backends.oracle'
  # NAME -- 数据库名称
  # sqlite3 NAME指定数据库文件绝对路径, 默认值 os.path.join(BASE_DIR,'db.sqlite3') 存放项目根目录
  # 其他数据库需配置 USER PASSWORD HOST 
  ```

  

- INSTALLED_APPS自带应用和自定义应用

  ```python
  INSTALLED_APPS = [
      'django.contrib.admin',
      'django.contrib.auth',
      'django.contrib.contenttypes',
      'django.contrib.sessions',
      'django.contrib.messages',
      'django.contrib.staticfiles',
      'rest_framework' 
  ]
  ## 默认应用
  # django.contrib.admin -- 管理员站点， 你很快就会使用它。
  # django.contrib.auth -- 认证授权系统。
  # django.contrib.contenttypes -- 内容类型框架。
  # django.contrib.sessions -- 会话框架。
  # django.contrib.messages -- 消息框架。
  # django.contrib.staticfiles -- 管理静态文件的框架。
  # 可添加自定义点式路径
  ```

  ```shell
  # 为INSTALLED_APPS中的应用创建数据表和 makemigrations生成的.000x_initial文件
  $ python manage.py migrate
  ```

  

### 2. 创建数据库模型

```python
from django.db import models


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

### 3. 激活数据库模型

```shell
## 为模型的改变生成迁移文件, 在应用的migration/目录下生成000x_initial.py文件
$ python manage.py makemigrations [appname]
## 查看指定的迁移文件,以可读的方式
$ python manage.py sqlmigrate appname 000x
# 为INSTALLED_APPS中的应用创建数据表和应用数据库迁移
$ python manage.py migrate
```

### 4. 交互式命令行

```shell
# 进入交互式命令
$ python manage.py shell
```

### 5. Django后台管理

Django 全自动地根据模型创建后台界面。

```shell
# 创建管理员用户  设置用户名 邮件 密码
$ python manage.py createsuperuser

# 启动开发服务器
$ python manage.py runserver
```

```python
# admin.py给数据库模型注册一个一个后台接口
polls/admin.py¶
from django.contrib import admin
from .models import Question

admin.site.register(Question)
```

## Django 视图进阶

---

1.  视图的创建
2. urls配置 URLconf
3. 视图渲染模板

### 1. 视图的创建

- Django中，视图表现为一个python函数（polls.py）
- 根据用户请求的URL来使用那个视图（urls.py）
- 

### 2. urls配置 

- 匹配变量 

  ```python
  # urls.py中配置 route设置变量
  urlpatterns = [
      path('<int:question_id>/', views.detail, name='detail'),
  ]
  ```

  ```python
  # views.py中引用变量
  def detail(request, question_id):
      return HttpResponse("You're looking at question %s." % question_id)
  ```

### 3. 模板渲染和数据处理

- 渲染template.render() 函数

  ```python
  # 模板存放目录（appName/templates/appName/index.html）  模板命名空间: 防止和另一个应用中的某个模板文件重名，把这些模板放入一个和 自身 应用重名的子文件夹里。
  # template = loader.get_template('polls/index.html') # 导入模板文件
  # 可通过template.render(context, request) # 传入上下文字典映射为一个python对象传入
  
  def index(request):
      latest_question_list = Question.objects.order_by('-pub_date')[:5]
      template = loader.get_template('appname/index.html')
      context = {
          'latest_question_list': latest_question_list,
      }
  	return HttpResponse(template.render(context, request))
  ```

- rander()函数渲染模板 

  ```python
  # 直接使用render函数渲染模板文件,返回一个HttpResponse对象
  # render(request, 'appname/index.html', context)
  def index(request):
      latest_question_list = Question.objects.order_by('-pub_date')[:5]
      context = {'latest_question_list': latest_question_list}
      return render(request, 'polls/index.html', context)
  ```

### 4. 抛出错误404

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

### 5. 模板系统

- 循环调用： {% for  ... in .. %} {% endfor %}

- 变量：{{ xxx }}

- 模板中替换URL

  替换前

  ```html
  <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
  ```

  替换后

  ```html
  <li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
  ```

* URL 名称添加命名空间，视图不唯一，指向具体的应用视图

  无指向

  ```html
  <li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
  ```

  指向具体命名空间

  ```html
  <li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
  ```

