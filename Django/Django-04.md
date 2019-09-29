# Django-04

## request对象

Django框架根据Http请求报文自动生成的一个对象，包含了请求各种信息

### path

请求完整路径

### method

GET 1.11版本最大数据量2K

POST参数存在请求体中，文件上传等

请求方法，常用GET，POST

应用场景：前后端分离的底层 判断请求方法 执行对应的业务逻辑

### GET

+ QueryDict类字典结构key-value
+ 一个key可以对应多个值
+ get：如果有多个相同参数，获取最后一个
+ getlist：如果有多个相同参数获取多个

### POST

类似字典的参数，包含了POST请求方式的所有参数

### encoding

编码方式，常用utf-8

### FILES

类似字典的参数，包含了上传的文件 文件上传的时候会使用

页面请求方式必须是post form的属性enctype=multipart/form-data

flask和django通用的 一个专属于django

### COOKIES

类似于字典的参数，包含了上传的文件 获取cookie

### session

类似字典，表示会话

### META

应用反爬虫 REMOTE_ADDR 拉入黑名单

客户端的所有信息ip

子路由：urls.py

```python
urlpatterns = [
    url(r'index', views.index),
    url(r'testRequest', views.testRequest)
]
```



views.py文件

```python
def index(request):
    return HttpResponse('index')


# 当遇到post请求时，会验证csrf 防跨站攻击 解决方法：注释掉，在setting中的中间件
def testRequest(request):
    # 请求资源路径
    # 应用场景：判断某请求是否在某请求列表下
    print(request.path)

    # 获取请求方式
    print(request.method)
    # 请求资源路径：127.0.0.1:8000/req/testRequest/?name=zs&age=18&age=19
    # queryDict 和Dict的区别
    # dict是字典对象，没有urlencode()方法
    # QueryDict对象，you urlencode()方法， 作用是将QueryDict对象转换为url字符串
    # queryDict默认的value值是一个列表
    # dict的默认value值是一个字符串
    # 返回的结构< QueryDict: {'name': ['zs'], 'age': ['18', '19']} >
    #           <QueryDict:{'age':['18'], 'name':['zs']}>

    # get请求参数
    print(request.GET)
    print(request.GET.get('name'))
    # 如果重复，取后面的值
    print(request.GET.get('age'))
    # 想要全部获取，使用getlist
    print(request.GET.getlist('age'))

    # post请求参数
    print(request.POST)
    print(request.POST.get('name'))
    print(request.POST.getlist('age'))
    # 编码
    print(request.encoding)
    # 反爬虫
    print(request.META.get('REMOTE_ADDR'))

    for key in request.META:
        print(key, request.META.get(key))

    return HttpResponse('今天是个好日子')
```

## HttpResponse对象

### HTML响应

#### 基类HttpResponse 

不适用模板 直接HttpResponse()

方法 

+ init： 初始化内容
+ write(xxx)：直接写出文本
+ flush()：冲刷缓冲区
+ set_cookie(key,value)：设置cookie
+ delete_cookie()：删除cookie

#### render转发/渲染

方法的返回值类型也是一个HttpResponse

#### HttpResponseRedirect(重定向)

HttpResponse的子类，响应重定向：可以实现服务器内部跳转

return HttpResponseRedict('路径') 推荐使用反向解析，状态吗302

简写方式：简写redirect方法的返回值类型就是HttpResponseRedirect

### 反向解析

1. 页面中的反向解析

   ```
   基本使用
   {% url 'namespance:name')
   url 位置参数
   {% url 'namespace:name'  value1 value2 %}
   url关键字参数
   {% url 'namespace:name' key1=value1 key2 = value2 %}
   ```

2. python代码中的反向解析

   ```
   基本使用
   reverse('namespace:name')
   位置参数
   reverse('namespace:name', args=(value1, value2 ...))
   reverse('namespace:name', args=[value1, value2 ...])
   关键字参数
   reverse('namespace:name', kwargs={key1:value2, key2:value2 ...})
   ```

### JsonResponse

这个类是HttpResponse的子类，它主要和父类的区别在于：

1. 它的默认值Content-Type被设置为：application/json
2. 第一个参数，data应该是一个字典类型，当safe这个参数设置为：False,那么data可以填入任何能被转换为JSON格式的对象，比如list,tuple,set。默认的safe参数是True.如果你传入的data数据类型不是字典类型，那么他就会抛出异常

子路由 urls.py

### HttpResponse子类

```
HttpResponseRedirect：重定向302
HttpResponsePermanentRedirect：永久重定向301
HttpResponseBadRequest：400
HttpResponseNoFound：404
HttpResponseForbidden：403 csrf防跨站攻击
HttpResponseNotAllowed：405
HttpResponseServerError：500
Http404-Exception：raise主动抛出异常
```

```python
urlpatterns = [
    url(r'^index/', views.index),
    # html响应
    # response
    url(r'^testResponse/', views.testResponse),
    # render
    url(r'testRender/', views.testRender),
    # redirect
    url(r'^testRedirect/', views.testRedirect),
    # 重定向的简写方式
    url(r'testRedirect1/', views.testRedirect1),

    url(r'test/', views.test),
    # python 中的反向解析
    # 基本应用
    url(r'^testReverse/', views.testReverse, name='testReverse'),
    url(r'^testReverseBase/', views.testReverseBase),
    # 位置参数
    url(r'^testLocal/', views.testLocal),
    url(r'^testReverseLocal/(\d{4})/(\d+)/(\d+)/', views.testReverseLocal, name='testReverseLocal' ),
    # 关键字参数
    url(r'^testKey/', views.testKey),
    url(r'^testReverseKey/(?P<year>\d{4})/(?P<month>\d+)/(?P<day>\d+)/', views.testReverseKey, name='testReverseKey'),

    # jsonResponse
    url(r'^testJson', views.testJson),
    # JsonResponse 返回一个对象
    url(r'^testJsonObject', views.testJsonObject),
    # JsonResponse返回一个列表
    url(r'^testJsonList', views.testJsonList)
]
```

视图函数views.py

```python
from django.http import HttpResponse, HttpResponseRedirect, JsonResponse
from django.shortcuts import render, redirect

# Create your views here.
from django.urls import reverse

from RespApp.models import Animal


def testResponse(request):
    response = HttpResponse()
    response.content = '明天有相亲大会，下午开始'
    response.status_code = 405
    response.write('恭祝相亲成功')

    # 字节流 字节缓冲流 字符流 字符缓冲流
    # flush 避免字节丢失
    response.flush()

    return response


def testRender(request):
    response = render(request, 'testRender.html')
    print(type(response))
    return response


# HttpResponseRedirect 是HttpResponseRedirectBase的子类
# HttpResponseRedirectBase 是 HttpResponse的子类
# 状态码 302

# HttpResponsePermanentRedirect 永久重定向
# HttpResponsePermanentRedirect的父类是HttpResponseRedirectBase
# HttpResponseRedirectBase的父类是HttpResponse
def testRedirect(request):
    return HttpResponseRedirect('/resp/index')


def index(request):
    return HttpResponse('欢迎光临珍爱网')


# redirect方法判断了permanent
def testRedirect1(request):
    return redirect('/res/idnex')


def test(request):
    name = '张三'
    context = {
        'name': name
    }
    return render(request, 'index.html', context=context)


def testReverse(request):
    return HttpResponse('用来测试python中的反向解析')


def testReverseBase(request):
    a = reverse('resp:testReverse')
    print(a)
    return redirect(reverse('resp:testReverse'))


def testReverseLocal(request, month, day, yeas):
    return HttpResponse(yeas + '/' + month + '/' + day)


def testLocal(request):
    return redirect(reverse('resp:testReverseLocal', args=[2019, 9, 27]))


def testReverseKey(request, month, day, year):
    return HttpResponse(year + '/' + month + '/' + day)


def testKey(request):
    return redirect(reverse('resp:testReverseKey', kwargs={'year': 2019, 'month': 9, 'day': 27}))


# JsonResponse 继承了HttpResponse
def testJson(request):
    data = {
        'msg': 'ok',
        'status': 200
    }
    return JsonResponse(data)


def testJsonObject(request):
    animal = Animal.objects.first()

    data = {
        'msg': 'ok',
        'status': 200,
        'animal': animal.to_dict()
    }
    # 如果出现中文编码问题，加上json_dumps_params参数
    return JsonResponse(data=data, json_dumps_params={'ensure_ascii': False})


def testJsonList(request):
    animals = Animal.objects.all()
    animal_list = []
    for animal in animals:
        animal_list.append(animal)

    data = {
        'msg': 'ok',
        'status': 200,
        'animal_list': animal_list
    }

    return JsonResponse(data=data, json_dumps_params={'ensure_ascii':False})

```

## 会话技术

Http在web开发中基本都是短连接

请求生命周期从Request开始，到Response结束

会话技术：cookie、Session、token(自定义的session)

### cookie

客户端会话技术，数据都存储在客户端，以key-value进行存储，支持过期时间max_age，默认请求会携带本网站的所有cookie，cookie不能跨域名，不能跨浏览器，cookie默认不支持中文`base64`
cookie是服务器端创建  保存在浏览器端

```
设置cookie应该是服务器 response
获取cookie应该在浏览器 request
删除cookie应该在服务器 response
```

cookie使用：
	

```
设置cookie：response.set_cookie(key,value）
获取cookie：username =request.COOKIES.get("username")
删除cookie：response.delete_cookie("content")
```

可以加盐：加密 获取的时候需要解密

+ 加密：`response.set_signed_cookie('content', uname, "xxxx")`

+ 解密：获取的是加盐之后的数据
  		 	`uname = request.COOKIES.get('content)`
  		 获取的是解密之后数据
  		` uname = request.get_signed_cookie("content", salt="xxxx")`

  通过Response将cookie写到浏览器上，下一次访问，浏览器会根据不同的规则携带cookie过来
  `max_age`:整数，指定cookie过期时间  单位秒
  `expries`:整数，指定过期时间，还支持是一个`datetime`或	`timedelta`，可以指定一个具体日期时间
  max_age和`expries`两个选一个指定
  过期时间的几个关键时间

  + max_age 设置为 0 浏览器关闭失效
  + 设置为None永不过期
  + `expires=timedelta(days=10)` 10天后过期

案例：需求：登录页面输入姓名，在欢迎页面获取登录界面输入的姓名，如果不通过登录页面直接进入欢迎页面，显示欢迎游客，欢迎页面有一个退出按钮，点击之后显示欢迎游客

子路由：urls.py

```python
urlpatterns = [
    # 登录页面
    url(r'^toLogin/', views.toLogin, name='toLogin'),
    # 登录
    url(r'^login/', views.login, name='login'),
    # 欢迎界面
    url(r'^welcome/', views.welcome, name='welcome'),
    # 退出
    url(r'^logout/', views.logout, name='logout'),
    # cookie
    url(r'^testSaltCookie', views.testSaltCookie),
    # cookie 加盐
    url(r'^testSaltCookie1', views.testSaltCookie1, name='testSaltCookie1'),
]
```

视图函数 views.py

```python
def toLogin(request):
    return render(request, 'login.html')


def login(request):
    name = request.POST.get('name')
    print(name)
    response = redirect(reverse('con:welcome'))
    response.set_cookie('name', name)

    return response


def welcome(request):
    name = request.COOKIES.get('name', '游客')

    return render(request, 'welcome.html', context=locals())


def logout(request):
    response = redirect(reverse('con:welcome'))
    response.delete_cookie('name')
    return response


def testSaltCookie(request):
    response = redirect(reverse('con:testSaltCookie1'))

    response.set_signed_cookie('name','zs', salt='xxx')

    return response


def testSaltCookie1(request):
    name = request.get_signed_cookie('name', salt='xxx')
    # name = request.COOKIES.get('name')
    return HttpResponse(name)
```

### session

服务端会话技术，数据存储在服务端，默认存储在内存中，在`django`被持久化到了数据库中，该表叫做`Django_session`,表中有三个字段，分别为`session_key`,`session_data`,`expris_sate`

django中Session的默认过期时间为14天，支持过期，主键是字符串，默认做了数据安全，使用了BASE64

使用base64之后，这个字符串会在最后面添加一个=

在前面添加了一个混淆串

依赖于cookies

Session的使用：

+ 设置session：`request.session['username']=username`
+ 获取session：`usernam=request.session.get('username')`
+ 删除session：
  + `del request.session['username']`，cookie是脏数据，即只删除了数据库中的session表中的数据，浏览器中的cookie依然存在
  + `response.delete_cookie('sessionid')`，session是脏数据，值删除了浏览器中的cookie信息
+ 冲刷：`request.flush()`，使用上面两种删除方法之后使用，将脏数据清除

session常用操作

+ get(key,default=None)根据键获取会话的值
+ clear()：清楚会话
+ flush()：删除当前的会话数据并删除会话cookie
+ `delect request['session_id']`:删除会话
+ `session.session_key`获取session的key
+ `request.session['user']=username`，设置session

数据存储到数据库中会进行base64编码

案例：需求：登录页面输入姓名，在欢迎页面获取登录界面输入的姓名，如果不通过登录页面直接进入欢迎页面，显示欢迎游客，欢迎页面有一个退出按钮，点击之后显示欢迎游客

子路由 urls.py

```python
urlpatterns = [
    # 取登录页面
    url(r'toLoginSession/', views.toLoginSession),
    # 登录
    url(r'loginSession', views.loginSession, name='loginSession'),
    # 欢迎界面
    url(r'welcomeSession', views.welcomeSession, name='welcomeSession'),
    # 退出
    url(r'logoutSession/', views.logoutSession, name='logoutSession')
]
```

视图函数views.py

```python
def toLoginSession(request):
    return render(request, 'loginSession.html')


def loginSession(request):
    name = request.POST.get('name')
    request.session['name'] = name

    return redirect(reverse('session:welcomeSession'))


def welcomeSession(request):
    name = request.session.get('name', '游客')

    return render(request, 'welcomeSession.html', context=locals())


def logoutSession(request):
    # del request.session['name']
    response = redirect(reverse('session:welcomeSession'))
    response.delete_cookie('sessionid')
    request.session.flush()

    # return redirect(reverse('session:welcomeSession'))
    return response
```





