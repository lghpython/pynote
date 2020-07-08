CBV和 FBV

function base views 基于函数视图

class base views 基于类的视图

```python
## CBV

# urls.py 配置
urlpattern=[
 url(r'login2/',views.LoginView.as_view()),
]
# views.py 新建视图类
from django.views import View,render, HttpResponse

class LoginView(View) ;
	def get(self, request):
    return render()
  def post(self,request):
   	username= request.POST.get('username')
    password = request.POST.get('pwd')
    return HttpResponse("CBV")
  
## FBV 
# urls.py 配置
urlpattern=[
 url(r'login1/',views.login),
]
# views.py 新建视图函数
def login(request):
  return HttpResponse("FBV")
```



