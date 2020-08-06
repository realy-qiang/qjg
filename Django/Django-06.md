# Django-06

## 模型继承

+ 默认一个模型在数据库中映射一张表

+ 如果模型存在继承的时候，父模型产生表映射

+ 子模型对应的表会通过外键和夫表产生关联

+ 从表外键引用主表的主键

注意：不能说从表夫外键引用主表的主键就一定的模型继承，因为一对一，一对多都会引用主表的主键

+ 关系型数据库的性能

  + 数据量越大性能月底

  + 关系越多越复杂性能越低

### 抽象模型

在父类的model的元信息中添加abstract=True

抽象模型不会在数据库中产生表

子模型拥有父模型中的所有字段

```python
class Animal(models.Model):
    name = models.CharField(max_length=32)

    class Meta:
        db_table = 'animal'
        abstract = True


class Dog(Animal):
    color = models.CharField(max_length=32)

    class Meta:
        db_table = 'dog'


class Cat(Animal):
    tail = models.IntegerField(default=9)

    class Meta:
        db_table = 'cat'
```

## 静态资源

静态资源和模板的区别：

1. 模板的路径不可以直接访问 必须通过取请求来访问

   static资源不可以直接访问

2. 模板的语法不可以在静态资源在书写

注意：

1. 使用的时候注意配置资源的位置

   STATICFILES_DIRS

   使用{% load static %}

   {% static '相对路径' %}

2. 全栈工程师 要求会templates，开发工程师 前后端分离

## 文件上传

要求客户端必须使用POST请求

指定`enctype='multiplepart/form-data'`

原生代码：使用`django`也使用flask

从`request.FILES`中获取到上传上来的文件打开一个文件，从上传上来的文件进行读取，向打开的文件中写入，必须是二进制格式来写入

注意：每次写入记得flush

实现步骤：

1. 表单的请求方式是POST

2. 添加表单属性enctype='multiplepart/form-data'，二进制传输

3. input的type属性为file

4. 获取表单中的file值，request.FILES.get获取的是文件的名字

5. with open 打开一个路径 然后以web的模式使用

   for in xxx.chunks()

   ​	fp.wirte()

   ​	fp.flush()

   ​	fp.close()

```python
def upload(request):
    if request.method == 'GET':
        return render(request, 'upload.html')
    if request.method == 'POST':
        name = request.FILES.get('icon')

        with open('/home/qjg/PycharmProjects/DjangoDay06/Day06/static/upload/pic.jpg', 'wb') as fp:
            for part in name.chunks():
                fp.write(part)
                fp.flush()
                fp.close()

        return HttpResponse('上传成功')
```

Django内置

1. 创建模型并且指定ImageField属性，注意依赖于pillow,使用pip进行下载，pip install pillow

   ```python
   class User(models.Model):
       u_icon = models.ImageField(upload_to='%Y/%m/%d/ICONS')
   
       class Meta:
           db_table = 'user'
   ```

2. settings中指定MEDIA_ROOT

   ```
   MEDIA_ROOT = os.path.join(BASE_DIR, 'static/uploadDjango')
   ```

   注意：

   1. media_root后面的数据类型不是一个列表
   2. 会自动创建文件夹，该文件夹的路径是MEDIA_ROOT+Imagefield的upload的值
   3. 重复添加同一张图片，也会成功，文件的额名字是原文件名+唯一串
   4. 数据库icon的值是upload_to+文件名

   bug:Linux系统下文件夹的第一级子目录下最多存储65535个文件

   解决办法：

   支持时间格式化，通过时间格式化来划分多级目录

 实现步骤：

1. 表单的提交方式必须是post
2. 添加表单的属性enctype = mutipart/form-data
3. 在settings中设置MEDIA_ROOT = os.path.join(BASE_DIR,'XXX')
4. 创建模型  模型的属性是imagefield 
5. 注意imagefield依赖于pillow
6. imagefield的约束是upload_to 该属性值和MEDIA_ROOT会进行拼接
7. 实例化对象 然后save保存即可

```python
@csrf_exempt
def uploadDjango(request):
    if request.method == 'GET':
        return render(request, 'uploadDjango.html')
    if request.method == 'POST':
        icon = request.FILES.get('icon')
        user = User()
        user.u_icon = icon
        user.save()
    return HttpResponse('上传成功')

# 从数据库中的到图片
def getImage(request):
    user = User.objects.last()

    icon = user.u_icon
    context = {
        'icon': '/static/uploadDjango/' + str(icon)
    }

    return render(request, 'getImage.html', context=context)
```

## 缓存

目的：

+ 缓解服务器的读写压力
+ 提升服务器的响应速度
+ 提升用户体验
+ 将执行的操作数据存储下来，在一定时间内，再次获取数据的时候，直接从缓存中获取

比较理想的方案是，使用内存级缓存

Django内置缓存框架：存储中间是数据的一种介质

原则：

+ 较少的代码
+ 对缓存后端封装一致性操作
+ 有点类似于ORM的感觉
+ 可扩展
+ 应该存在通用基类

Django内置缓存实现

1. 使用系统封装的，装饰器封装在视图函数上

   ```python
   @cache_page(30)
   def testCache1(request):
       time.sleep(5)
   
       return HttpResponse('测试装饰器缓存')
   ```

   注意：@cache_page()不需要写timeout,直接设定时间

2. 基于数据库：真实数据库中创建缓存表

   1. 创建缓存表,python manager createcachetable 表名

   2. 在setting中配置缓存信息

      ```python
      CACHES = {
          'default': {
              'BACKEND': 'django.core.cache.backends.db.DatabaseCache',
              # 表名
              'LOCATION': 'cache_table',
              # 缓存时间，以set方法为主
              'TIMEOUT': 60,
              # 指明数据库
              'KEY_PREFIX': 'Django06',
          }
      ```

      ```python
      def testCache2(request):
          value = cache.get('ip')
          if value:
              return HttpResponse(value)
          else:
              ip = request.META.get('REMOTE_ADD')
              cache.set('ip', ip)
              return HttpResponse('测试数据库缓存')
      ```

3. 基于redis:内存级数据库

   使用redis实现django-cahce的扩展

   操作缓存的API没有发生任何变更

   变更的就是连接缓存的配置

   常见的有两个实现

   `django-redis`：`pip install django_redis`

   `django-redis-cache`：`pip install django_redis_cache`

   配置：

   ```python
   CACHES = {
       'default': {
           'BACKEND': 'django_redis.cache.RedisCache',
           'LOCATION': 'redis://127.0.0.1:6379/1',
           'OPTIONS': {
               'CLIENT_CLASS': 'django_redis.client.DefaultClient'
           }
       }
   }
   ```

   ```python
   def testCache3(request):
       value = cache.get('ip')
       if value:
           return HttpResponse(value)
       else:
           ip = request.META.get('REMOTE_ADDR')
           cache.set('ip', ip)
           return HttpResponse('测试内存级数据库缓存')
   ```

   

   查看redis缓存

   + select 1
   + keys *
   + get :1:news
   + 查看过期时间  tts  :1:news

### 多缓存

写多套配置，定义不同的名字

存入缓存的时候，获取不同的缓存对象

想使用那个缓存就创建那个缓存的实例对象

装饰器缓存：可以使用装饰器 指定想要的数据库，@cache_page(30, cache='cache_name')

数据库缓存
cache = caches['cache_name']

多缓存配置

```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.db.DatabaseCache',
        'LOCATION': 'cache_table',
        'TIMEOUT': 60 * 5,
        'KEY_PREFIX': 'DjangoDay06'

    },

    'redis_backend': {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/1",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
        }
    }
}
```

```python
def testCache5(request):
    # 使用数据库缓存
    # cache = caches['default']
    # 使用内存级数据库缓存-redis缓存
    cache = caches['redis_backend']

    value = cache.get('ip')
    if value:
        return HttpResponse(value)
    else:
        ip = request.META.get('REMOTE_ADDR')
        cache.set('ip', ip)
    return HttpResponse('实现多级缓存')
```

## 富文本

富文本:Rich Text Format（RTF），是有微软开发的跨平台文档格式，大多数的文字处理软件都能读取和保存RTF文档，其实就是可以添加样式的文档，和HTML有很多相似的地方，写论坛，博客时使用的一种带样式的文本插件

插件使用方式

+ 安装插件：`pip install django-tinymce`

+ 在instatlled_app中添加tinymce

+ 初始化：在settings中注册tinymce应用， 设置默认的配置文件

  ```python
  TINYMCE_DEFAULT_CONFIG = {
      'theme':'advanced',
      'width':800,
      'height':600,
  }
  ```

+ 创建模型

  ```python
  class Blog(models.Model):
      sBlog = HTMLField()
  
      class Meta:
          db_table = 'blog'
  ```

+ 使用

  + 在自己的页面中使用

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
      <script type="text/javascript" src="/static/tiny_mce/tiny_mce.js"></script>
      <script type="text/javascript">
          tinyMCE.init({
              "mode": "textareas",
              "theme": "advanced",
              "width": 800,
              "height": 600
          })
      </script>
  </head>
  <body>
  <form action="{% url 'final:testRTF' %}">
      <textarea name="blog"></textarea>
      <button>提交</button>
  </form>
  </body>
  </html>
  ```

## thefuck

一个终端指令修复工具，当指令在输入错误的时候，我们可以通过fuck进行弥补可以提供指令修复方案
enter：确定
control+c：取消
↑↓ ：调整
使用

```
sudo apt update
sudo apt install python3-dev python3-pip
sudo pip3 install thefuck
```

更新环境变量

```
vim ~/.bashrc
eval "$(thefuck --alias fuck)"
source ~/.bashrc
```

## 中间件

是一个轻量级的，底层的插入，可以介入到Django的请求和响应过程(面向切面编程)

中间件的本质就是一个python类

面向切面编程(Aspect Oriented Programming)，简称AOP。AOP的主要实现目的是针对业务处理过程中的切面进行提取，它所面对的是处理过程中的某个步骤和阶段，以获得逻辑过程中个部分之间低耦合的隔离效果

django内置的一个底层插件，从属于面向切面编程AOP

+ 在不修改源代码的情况下，动态添加一些业务逻辑处理
+ 中间件的典型实现：装饰器，中间件就是使用类装饰器实现的

面向切面编程：

切点：

1. process_request

   process_request(self, request)：在执行视图前被调用，每一个请求上都会调用，不主动进行返回或者返回HttpResponse对象

2. process_view

   process_view(self, request, view_func, view_args, view_kwargs)：调用视图之前执行，每个请求都会调用，不主动进行返回或者返回一个HttpResponse对象

3. process_template_response

   process_template_response(self, request, response)：视图函数刚好执行完后进行调用，每个请求都会调用，不主动进行返回或者返回一个HttpResponse对象

4. process_response

   process_response(self, request, response)：所有响应返回浏览器之前调用，每个请求都会调用，不主动进行返回或者返回一个HttpResponse对象

5. process_exception

   process_exception(self, request, exception)：当视图函数抛出异常时调用，不主动进行返回或者返回一个HttpResponse对象

切面：切点处切开可以获得的数据



实现步骤：

1. 在工程目录下创建middleware目录

2. 目录中创建一个python文件

3. 在python文件中导入中间件的基类

   from django.utils.deprecation MiddlewareMiXin

4. 在类中根据功能需求，创建切入需求类，重写切入点方法

5. 启用中间件，在settings中进行配置，MIDDLEWARE中添加

   middleware.文件名.类名

应用：

白名单和黑名单，当某一段业务逻辑发生了错误  那么就会执行process_exception方法

```python
class ConnAOP(MiddlewareMixin):
    def process_request(self, request):
        # print('才知道')
        # print('连接数据库')
        # 白名单
        # ip = request.META.get('REMOTE_ADDR')
        #
        # if ip == '127.0.0.1':
        #     if random.randrange(100) > 40:
        #         return HttpResponse('恭喜你抢到了')

        # 黑名单
        ip = request.META.get('REMOTE_ADDR')
        if ip == '127.0.0.1':
            return HttpResponse('被抢光了')
#         print('听说国外记者专门拍摄阅兵时士兵眨眼瞬间')
#
# 
# class ExceptionAOP(MiddlewareMixin):
#
#     def process_exception(self, request, exception):
#
#         return redirect(reverse('middle:index'))
#
#     def process_request(self, request):
#         print('我们不眨眼')
```

注意：中间件的执行顺序

中间件注册的时候是一个列表，如果我们没有在切点处直接返回，中间件会一次执行

如果我们直接进行了返回，后续中间件就不会在执行了

## 分页器

分页是为了提升永辉体验，并减小服务器的负担而开发的

分页：

真分页：每一次点击下一页或者上一页 都会向数据库发送请求，并且返回数据

假分页：一次性读取所有数据 然后在内存中进行分页

企业级开发中常用真分页



原生实现：

```python
def getPage(request):
    page = int(request.GET.get('page', 1))
    per_page = request.GET.get('per_page', 2)
    # animals = Animal.objects.all()[0:1]
    animals = Animal.objects.all()[(page - 1) * per_page, page * per_page]
    for animal in animals:
        print(animal.name, animal.color)
    return HttpResponse('查询成功')
```

封装实现：

Paginator(分页工具)

+ 对象的创建：Paginator(数据集，每一页数据的数量)

  pagin = Paginator(animals, per_page)

+ 属性：

  + count：对象总数
  + num_pages：页面总数
  + page_range：页码列表，从1开始

+ 方法：

  + page(整数)：获取一个page对象，该方法的返回值类型是Page

+ 常见错误：

  + InvalidPage：page()传递无效页码
  + PageNotAnInteger:page()传递的不是整数
  + Empty：page()传递的值有效，但是没有数据

Page(具体哪一页)

+ 对象获得：通过Paginator的Page方法

+ 属性：
  + object_list：当前页面上所有的数据对象
  + number：当前的页码值
  + paginator：当前page关联的Paginator对象
+ 方法：
  + has_next()：判断是否有下一页
  + has_previous()：判断是否有上一页
  + has_other_pages()：判断是否有上一页或者下一页
  + next_page_number()：返回下一页的页码
  + previous_page_number()：返回上一页的页码
  + len()：返回当前页的数据的个数

应用场景：paginator对象 适用于页码的遍历 

page对象 适用于是否有上一页或者下一页 上一页页码 下一页页码

```python
def getPageDjango(request):
    page = int(request.GET.get('page', 1))
    per_page = request.GET.get('per_page', 2)
    animals = Animal.objects.all()
    pagin = Paginator(animals, per_page)

    p = pagin.page(page)
    context = {
        'p': p,
        'pagin': pagin,
        'page': page
    }
    return render(request, 'getPageDjango.html', context=context)
```

html中的使用

```html
{% if p.has_previous %}
            <a href="{% url 'page:getPageDjango' %}?page={{ p.previous_page_number }}">上一页</a>
            {% else %}
            <a href="#">上一页</a>
        {% endif %}

        {% for foo in pagin.page_range%}
            {% if foo == page %}
                <a href="{% url 'page:getPageDjango' %}?page={{ foo }}" style="color: red">{{ foo }}</a>
                {% else %}
                <a href="{% url 'page:getPageDjango' %}?page={{ foo }}">{{ foo }}</a>
            {% endif %}


        {% endfor %}

        {% if p.has_next %}
            <a href="{% url 'page:getPageDjango' %}?page={{ p.next_page_number }}">下一页</a>
            {% else %}
            <a href="#">下一页</a>
        {% endif %}

    </ul>
```



