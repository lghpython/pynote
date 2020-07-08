### Django ORM数据持久化

#### ORM: 对象关系映射

- 数据库文件自动生成模型数据命令

  ```shell
  $ python manage.py dbfilename > appname/models.py
  ```

ORM 的CRUD操作

```shell
## 进入交互式环境
$ python manage.py shell 
# 导入模型
>>> from polls.models import Subject

## Create 新增
>>> subject1 = Subject(name='Python全栈开发', intro='当下最热门的学科', is_hot=True)
>>> subject1.save()
>>> subject2 = Subject(name='全栈软件测试', intro='学习自动化测试的学科', is_hot=False)
>>> subject2.save()
>>> subject3 = Subject(name='JavaEE分布式开发', intro='基于Java语言的服务器应用开发', is_hot=True)

## Remove 删除
>>> subject = Subject.objects.get(no=2)
>>> subject.delete()

## Update 更新
>>> subject = Subject.objects.get(no=1)
>>> subject.name = 'Python全栈+ 人工智能'
>>> subject.save()
## Retrieve 读取
# 读取所有
>>> Subject.objects.all()
# 筛选读取
>>> Subject.objects.filter(condition1).filter(condition2)
# 单个
>>> Subject.objects.get(pk=1)
>>> Subject.objects.filter(condition1).first()
>>> Subject.objects.filter(condition1).last()
# 排序 负号为降序
>>> Subject.object.order_by('no')
>>> Subject.object.order_by('-no') 
# 分页
# 按编号从小到大查询前3个学科
>>> Subject.objects.order_by('no')[:3]
# 计数
# 查询一共有多少个学科
>>> Subject.objects.count()
# 高级查询
# 查询编号为1的学科的老师
>>> Teacher.objects.filter(subject__no=1)
# 反向查询 类名小写_set
>>> Subject.objects.get(pk=1).teacher_set.all() 
# 查询学科名称有“全栈”二字的学科的老师
>>> Teacher.objects.filter(subject__name__contains='全栈') 
```



