# Flask-02

## Flask中的路由

```python
import uuid

from flask import Blueprint


blue = Blueprint('first', __name__)


@blue.route('/index/')
def index():
    return 'index'

# 指定路由参数，格式为<类型:变量名>
# 默认类型为字符串
@blue.route('/getstudents/<username>/')
def getstudents(username):
    print(username)
    print(type(username))
    return '学生姓名为%s' % username

# path:也是字符串，但是保留/
@blue.route('/getstudents1/<path:username>/')
def getstudents1(username):
    print(username)
    print(type(username))
    return '学生姓名为%s' % username

# int类型
@blue.route('/makemoney/<int:money>/')
def makemoney(money):
    print(money)
    print(type(money))
    return '金额为%d' % money

# float类型
@blue.route('/weight/<float:weight>/')
def weight(weight):
    print(weight)
    print(type(weight))
    return '学生体重为%.2f' % weight

# 随机获得一个uuid值
@blue.route(('/getuu/'))
def getuu():
    uu = uuid.uuid4()
    print(uu)
    print(type(uu))
    return str(uu)

# uuid类型
@blue.route('/getuuid/<uuid:uuid>')
def getuuid(uuid):

    print(uuid)
    print(type(uuid))
    return str(uuid)

# 以给定的选项进行访问
@blue.route('/getany/<any(string,int):any>/')
def getany(any):
    print(any)
    t = type(any)
    return '得到的是%s' % t
```

注意：访问时，所给的路径值必须符合路由参数的类型，不然访问不了

## postman和请求方式

postman：模拟请求工具

安装：`https://blog.csdn.net/Shyllin/article/details/80257755`

```python
# 请求方式又叫http 方式
# get(获取) post(添加) delete(删除) put(修改全部属性) patch(修改的是部分属性)
@blue.route('/toLogin/')
def toLogin():
    return render_template('login.html')


# 路由默认只支持 get head options请求方式 不支持post delete
# 如果想让路由支持post delete put patch，可以在rout方法中添加一个参数
# 这个参数为method=[]
# method列表的元素大小写都可以
@blue.route('/login/', methods=['post', 'delete'])
def login():
    return '欢迎光临'
```

```python
# postman 请求模拟工具
@blue.route('/testPostman/', methods=['post', 'patch'])
def testPostman():
    return '你的梦想是什么'
```



### 反向解析

```python
# 反向解析
@blue.route('/testReverse/')
def testReverse():
    return 'testReverse'


@blue.route('/testReverse1/')
def testReverse():
    # 获取testReverse的请求路径
    # 反向解析获取的是请求资源路径
    # 反向解析的语法 url_for(蓝图名.方法名)
    s = url_for('first.testReverse')
    print(s)

    s1 = url_for('first.testPostman')
    print(s1)

    # 应用场景
    # 1、redirect('/index/')  重定向到index请求
    #   尽量不要使用硬编码 redirect(url_for('first.testPostman'))
    # 2、页面中不要写硬编码 form action='/login/'
    #    form action = url_for('first.login')
    return 'testReverse1'
```

## request

request是一个内置对象，不需要创建就可以直接使用的对象

属性：

+ method：获取请求方法
+ base_url：获取请求路径
+ host_url：获取主机ip地址
+ url：获取地址，包括请求参数
+ remote_addr：请求的客户端地址
+ files：文件上传
+ headers：获取请求头
+ path：路由中的路径
+ cookie：请求头中的cookie
+ agrs：获取get请求方式的参数
+ form：获取post请求方式的参数

```python
# request
@blue.route('/testRequest/', methods=['get', 'post'])
def testRequest():
    # mysql 3306 mongodb 27017 oracle 1521 redis 6379
    # http 80 http 443
    # ftp 21 ssh 22
    # 获取请求方式
    print(request.method)

    # 去掉请求参数的路径
    # http://127.0.0.1:5000/testRequest/
    print(request.base_url)

    # 主机的路径不带请求资源路径
    # http://127.0.0.1:5000/
    print(request.host_url)

    # 完整的url路径 (请求资源路径，请求参数)
    # http://127.0.0.1:5000/testRequest/?nm=zs
    print(request.url)

    # 主机地址 应用场景 反爬虫
    print(request.remote_addr)

    # 应用场景 文件上传
    print(request.files)

    # 请求头
    print(request.headers)

    # 请求资源路径  应用场景 购物车
    print(request.path)

    # 获取请求的cookies
    print(request.cookies)

    # http://127.0.0.1:5000/testRequest?name=zs
    # 怎么获取name的值
    name = request.args.get('name')
    print(name)
    # 只返回第一个符合的请求参数
    age = request.args.get('age')
    print(age)
    # 多个相同的请求参数，以列表的形式返回
    age1 = request.args.getlist('age')
    print(age1)

    # 获取post请求
    name2 = request.form.get('name')
    print(name2)
    age2 = request.form.get('age')
    print(age2)
    age3 = request.form.getlist('age')
    print(age3)

    return 'testRequest'
```

## response

视图函数的返回值类型

字符串：普通字符串，render_templates

response：`make_response`、`redirect`、`Response`

```python
# response 视图函数的返回值类型
@blue.route('/testResponse/')
def testReponse():
    return '君子世无双'


# 视图函数返回一个模板
@blue.route('/testResponse2/')
def testResponse2():
    s = render_template('testResponse2.html')
    print(type(s))
    return s


# 视图函数返回一个make_response()
@blue.route('/testResponse3/')
def testRespons3():
    r = make_response('<h1>举头望明月</h1>')
    print(type(r))
    return r


# 视图函数返回一个redirect()
@blue.route('/index/')
def index():
    return 'welcome to 北京'


@blue.route('/testResponse4/')
def testResponse4():
    r = redirect('/index/')
    print(type(r))
    return r


# 视图函数返回Response对象
@blue.route('/testResponse5/')
def testResponse5():
    r = Response('低头思故乡')
    return r
```

## 异常和捕获异常

abort：直接抛出，显示错误状态码，种植程序运行

@blue.errorhandler(错误码)：异常捕获，可以根据状态或Exception进行捕获

函数中包含一个参数，参数用来接收异常信息

```python
# 异常
@blue.route('/testAbort/')
def testAbort():
    abort(404)
    return 'testAbort'


@blue.errorhandler(404)
def testAbort1(Exception):
    return '系统正在升级，请稍后再试....'
```

## 会话技术

+ 请求过程Request开始，到Response结束
+ 连接都是短连接
+ 延长交互的生命周期
+ 将关键数据记录下来
+ Cookie是保存在浏览器/客户端的状态管理
+ Session是服务器端的状态管理技术

### cookie

+ 客户端会话技术

+ 所有数据存储在客户端

+ 以key-value进行数据存储

+ 服务器不做任何存储

+ 特性

  + 支持过期时间
    + max_age：以毫秒为单位
    + expries：以日期值的形式
  + 根据域名进行cookie存储
  + 不能跨网站，Response进行操作

+ cookie的登录和使用

  ```txt
  设置cookie
  	response.set_cookie('username',username)
  获取cookie，第二个参数是默认值
  	username = request.cookie.get('username','游客')   
  删除cookie
  	response.delete_cookie('username')
  ```

  ```python
  # cookie
  @blue.route('/tologincookie/')
  def tologincookie():
      return render_template('logincookie.html')
  
  
  @blue.route('/logincookie/', methods=['post'])
  def logincookie():
      name = request.form.get('name')
  
      response = redirect(url_for('first.welcomecookie'))
  
      response.set_cookie('name', name)
  
      return response
  
  
  @blue.route('/welcomecookie/')
  def welcomecookie():
      # 获取cookie中的name
      name = request.cookies.get('name', '游客')
  
      return render_template('welcomecookie.html', name=name)
  
  
  @blue.route('/logoutcookie/')
  def logoutcookie():
      response = redirect(url_for('first.welcomecookie'))
      response.delete_cookie('name')
      return response
  ```

