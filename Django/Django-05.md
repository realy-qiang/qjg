# Django-05

## 模型迁移

### Model=>DB(模型到数据库)

  迁移步骤

+ 生成迁移文件  python manage.py makemigrations
+ 执行迁移文件  python manage.py migrate

迁移文件的生成

+ 根据models文件生成对应的迁移文件
+ 根据models和已有迁移文件差别 生成新的迁移文件

迁移原理

+ 先去迁移记录查找，哪些文件未迁移过：app_label + 迁移文件名字
+ 执行未迁移的文件
+ 执行完毕，记录执行过的迁移文件

指定迁移的app  

+ python manage.py makemigrations app
+ python manage.py migrate    

重新迁移

+ 删除迁移文件：migrations所涉及的迁移文件
+ 删除相关的表
+ 删除数据库中django-migrations表的相关记录

### DB=>Model(数据到模型)

反向生成到指定得app下

+ python manager.py inspectdb > App/models.py

元信息中包含一个属性  managed=False   不支持迁移

如果自己的模型不想被迁移系统管理，也可以使用 managed=False进行声明

## 模型关系

### 一对一

应用场景：

+ 用于功能表的拆分
+ 扩展新功能

一对一模型首先要确定只从关系，谁声明关系谁就是从表

底层实现：使用外键实现，对外建添加唯一约束

创建模型：

```python
class Student(models.Model):
    s_name = models.CharField(max_length=32)

    class Meta:
        db_table = 'student'


class IdCard(models.Model):
    i_cardNo = models.IntegerField()
    i_student = models.OneToOneField(Student, null=True, blank=True)

    class Meta:
        db_table = 'idcard'
```

添加数据

```python
# 增加主表数据
def addStudent(request):
    student = Student()
    student.s_name = '张三'
    student.save()

    return HttpResponse('添加成功')

# 增加从表数据
def addIdCard(request):
    idcard = IdCard()
    idcard.i_cardNo = 1905
    idcard.save()

    return HttpResponse('添加成功')

```

数据绑定：

```python
def bind(request):
    student = Student.objects.last()
    idcard = IdCard.objects.last()

    idcard.i_student = student
    idcard.save()

    return HttpResponse('绑定成功')
```

注意：再添加一个主表数据，然后绑定可以，但是再添加一个从表数据，然后进行绑定，则会失败，因为此时绑定外键字段不唯一

删除数据

+ 默认是级联删除，因为有一个约束 on_delete，默认值为CASCADE
+ 修改on_delete=models.SET_NULL，此时删除主表数据，会将从表中数据相关字段设置为null
+ 修改on_delete=models.SET_PROTECT, 当存在级联数据的时候，删除主表数据，会抛出异常,主表不存在级联数据的时候，可以删除,开发中为了防止误操作，我们通常会设置为此模式

```python
ef deleteIdCart(request):
    idcard = IdCard.objects.last()
    idcard.delete()

    return HttpResponse('删除成功')


def deleteCascade(request):
    student = Student.objects.last()
    student.delete()

    return HttpResponse('删除成功')


def deleteNull(request):
    student = Student.objects.last()
    student.delete()

    return HttpResponse('删除成功')

def deleteProtect(request):
    student = Student.objects.last()
    student.delete()

    return HttpResponse('删除成功')
```



查询数据：

```python
# 从获取主 显性属性 该显性属性会返回一个对象
def getIdCard(request):
    student = Student.objects.last()
    print(student.idcard.i_cardNo)

    return HttpResponse('查询成功')

# 主查从   获得主表的对象之后 该对象 有一个属性 是隐形属性
def getStudent(request):
    idcard = IdCard.objects.last()
    print(idcard.i_student.s_name)

    return HttpResponse('查询成功')
```

### 一对多

增加模型

```python
class Dept(models.Model):
    d_name = models.CharField(max_length=32)

    class Met:
        db_table = 'dept'


class Emp(models.Model):
    e_name = models.CharField(max_length=32)
    # e_dept = models.ForeignKey(Dept, null=True, blank=True, on_delete=models.CASCADE)
    # e_dept = models.ForeignKey(Dept, null=True, blank=True, on_delete=models.SET_NULL)
    e_dept = models.ForeignKey(Dept, null=True, blank=True, on_delete=models.PROTECT)

    class Meta:
        db_table = 'emp'
```

子路由

```python
urlpatterns = [
    # 一对多
    # 添加
    #   添加主表数据
    url(r'^addDept/', views.addDept),
    #   添加从表数据
    url(r'^addEmp/', views.addEmp),
    #   绑定
    url(r'bind', views.bind),
    # 删除
    #   删除子表数据
    url(r'd^eleteEmp', views.deleteEmp),
    #   删除主表数据
    #       删除主表数据 对应的字表数据全部删除
    url(r'^deleteCascade/', views.deleteCascade),
    #       删除主表数据 对应的从表数据设置为空
    url(r'^deleteNull/', views.deleteNull),
    #       删除主表数据 报错
    url(r'^deleteProtect/', views.deleteProtect),
    # 查询
    # 主表查从表
    url(r'^getEmps', views.getEmps),
    # 从表查主表
    url(r'getDept/', views.getDept),
]
```



添加数据：

```python
def addDept(request):
    dept = Dept()
    dept.d_name = '开发部'
    dept.save()

    return HttpResponse('添加成功')


def addEmp(request):
    emp = Emp()
    emp.e_name = '袁隆平'
    emp.save()

    return HttpResponse('添加成功')
```

数据绑定

```python
def bind(request):
    dept = Dept.objects.last()
    emp = Emp.objects.last()

    dept.e_dept = emp
    emp.save()
    return HttpResponse('绑定成功')
```

这个可以多次绑定，因为一对多，外键不需要唯一

删除数据

默认级联删除，on_delete=models.CASCADE

删除主表数据，从表设置为NULL：on_delete=models.SET_NULL

删除主表数据，直接报错：on_delete=models.PROTECT

查询数据

主表查询从表：使用隐形属性dept.emp_set

从表查询主表使用显性属性emp.e_dept

```python
def getEmps(request):
    dept = Dept.objects.last()
    emps = dept.emp_set.all()
    for emp in emps:
        print(emp.id, emp.e_name)

    return HttpResponse('查询成功')


def getDept(request):
    emp = Emp.objects.last()
    print(emp.e_dept.id)

    return HttpResponse('查询成功')
```

### 多对多

+ 产生表的时候会产生单独的关系表
+ 关系表中存储关联表的主键，通过多个外键实现的，多个外键联合唯一
+ 会产生额外的关系表
  + 表中使用多个外键实现
  + 外键对应关系表的主键
+ 注意：关系表中外键的联合唯一

创建模型

```python
class Custom(models.Model):
    c_name = models.CharField(max_length=32)

    class Meta:
        db_table = 'custom'


class Goods(models.Model):
    g_name = models.CharField(max_length=32)
    g_custom = models.ManyToManyField(Custom)

    class Meta:
        db_table = 'goods'
```

子路由

```python
urlpatterns = [
    # 多对多
    #   添加
    #       添加主表数据
    url(r'^addCustom/', views.addCustom),
    #       添加从表数据
    url(r'^addGoods/', views.addGoods),
    #       添加关系表数据
    url(r'^addRelation/', views.addRelation),
    #   删除
    #       删除关系表
    url(r'deleteRelation/', views.deleteRelation),
    #       删除主表
    #           删除custom
    url(r'^deleteCustom/', views.deleteCustom),
    #           删除goods
    url(r'^deleteGoods/', views.deleteGoods),
    #   查询
    #       主表查从表
    url(r'^getGodds/', views.getGoods),
    #       从表查主表
]
```

添加数据：

添加关系表数据

方法：

+ 从 -- 主   从对象.属性.add(主对象)
+ 主 -- 从   主对象.从模型名_set.add(从对象)

注意：首先必须有数据才可以插入 该数据必须是查询出来的



```python
# 添加主表数据
def addCustom(request):
    custom = Custom()
    custom.c_name = '郭美美'
    custom.save()

    return HttpResponse('添加成功')

# 添加从表数据
def addGoods(request):
    goods = Goods()
    goods.g_name = '红牛'
    goods.save()

    return HttpResponse('添加成功')

# 添加关系表数据
def addRelation(request):
    custom = Custom.objects.last()
    goods = Goods.objects.last()

    # custom.goods_set.add(goods)
    goods.g_custom.add(custom)
    return HttpResponse('添加成功')
```

删除数据

```python
# 删除关系表数据
def deleteRelation(request):
    custom = Custom.objects.last()
    goods = Goods.objects.last()

    # custom.goods_set.remove(goods)
    goods.g_custom.remove(custom)

    return HttpResponse('删除成功')


def deleteCustom(request):
    custom = Custom.objects.last()
    custom.delete()

    return HttpResponse('删除成功')


def deleteGoods(request):
    custom = Custom.objects.last()
    custom.delete()

    return HttpResponse('删除成功')
```

查询数据：

```python
def getGoods(request):
    custom = Custom.objects.last()
    good_list = custom.good_set.all()
    for goods in good_list:
        print((goods.g_name, goods.id))

    return HttpResponse('查询成功')
```

