# Flask-03

### session

- 服务端会话技术
- 所有数据存储在服务器中
- 默认存在服务器的内存中
  - django默认做了数据持久化(存在数据库中)
- 存储结构也是key-value形式

注：单纯的使用session是会报错的，需要使用在\_\_init\_\_方法中配置

- session登录和使用

  ```txt
  设置
  	session['username']=username
  获取
  	session.get('username')
  删除
  	resp.delete_cookie('session')
  	session.pop('username')
  ```

```python
# Session
@blue.route('/tologinsession/')
def tologinsession():
    return render_template('loginsession.html')


@blue.route('/loginsession/', methods=['post'])
def loginsession():
    name = request.form.get('name')
    session['name'] = name
    return redirect(url_for('first.welcomesession'))


@blue.route('/welcomesession/')
def welcomesession():
    name = session.get('name', '游客')
    return render_template('welcomecookie.html', name=name)


@blue.route('/logoutsession/')
def logoutsession():
    response = redirect(url_for('first.welcomesession'))
    response.delete_cookie('session')
    # session.pop('name')
    return response
```

注意：使用session会话技术，实现必须给session设置一个唯一秘钥

原因：

会话(Session)数据存储于服务器上. 会话是指一个客户在一个web服务上从登录到注销之间的时间段. 会话中需要存储的会话数据, 储存在服务器上的一个临时目录中.

每个会话都被分配了一个会话ID, 会话数据以Cookie的形式存储, 并且服务器会把会话数据进行加密. 为了进行这样的加密, Flask应用需要定义一个配置: **SECRET_KEY**.

在`manager.py`中作如下设置

```python
app.secret_key = 'any random string'
# 也可以这样设置
# app.config["SECRET_KEY"] = "Flask的秘钥字符串"
```

### session持久化问题

- django中对session做了持久化,存储在数据库中
- 可以修改到redis中,flask没有对默认session做任何处理,默认存储在内存中
- flask-session可以实现session的数据持久化
- 各种位置更推荐使用redis,缓存在磁盘上的时候,管理磁盘文件使用lru,最近最少使用原则

实现方案

1. 安装插件:flask-session

   ```bash
   pip install flask-session
   ```

2. 初始化session对象

   - 配置init中的app.config['SESSION_TYPE']

   ```python
   app.config['SESSION_TYPE'] = 'redis'
   # redis数据库中生成的cookie设置一个前缀
   app.config['SESSION_KEY_PREFIX'] = 'flask'
   ```

   - 初始化

   ```python
   # Session(app=app)
   # 另外一种初始化方法
   s = Session()
   s.init_app(app=app)
   ```

   - 安装redis

   ```bash
   pip install redis
   ```

   - 查看redis内容

   ```
   开启redis客户端服务
   redis-cli
   查看所有的key
   key *
   得到key 对应的值
   get key
   查看过期时间
   ttlttl flask74f38cc0-d167-44f2-a1a6-cfecf65d49fd
   ```

   flask的redis中session的生存时间为31天,django的中的生存时间是14天

## Templates

### 简介

MVC中的view,MVT的Templates

主要用来做数据展示的

模板处理分为2个阶段

- 加载
- 渲染

jinja2模板引擎

- 本质上是一个html
- 支持特定的模板语法
- flask作者开发的一个现代化设计和友好的python模板语言,模仿的django的模板引擎
- 优点:
  - 速度快,被广泛使用
  - html设计和后端python分离
  - 减少python复杂度
  - 非常灵活
  - 提供了控制,继承等高级功能

### 模板

- 静态html,前后端分离
- 模板语言动态生成
- {{ 变量名 }}:接受一个变量

#### 结构标签

- block
- extend:继承
- include:包含,将一个指定的模板包含进来

父标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>


    {% block  ext_css%}

    {% endblock %}

</head>
<body>
    
    {% block header %}
    
    {% endblock %}


    {% block content %}
    
    {% endblock %}

    {% block footer %}
    
    {% endblock %}

    {% block ext_js %}
    
    {% endblock %}
    

</body>
</html>
```

子标签

```html
{% extends 'base.html' %}
{% block header %}
    hello world
{% endblock %}
```

多级继承

注意:多级继承是,如果使用的是同一个模块,后面的会覆盖前面的,如果不想要覆盖,则添加super()

```html
{% extends 'base-a.html' %}
{% block header %}
    {{ super() }}
    hello python
{% endblock %}
```

#### 宏定义(macro)

可以再模板中定义,调用函数

- 无参
- 有参数
- 外文件中的宏定义调用需要导入也可以include

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    什么样的节奏最呀最摇摆
    {# 无参的macro #}
    {% macro say() %}
        一经发现,严惩不贷
    {% endmacro %}

    {{ say() }}
    <hr />
    {# 有参的macro #}
    {% macro getUser(name,age) %}
        {{ name }}的年龄是{{ age }}
    {% endmacro %}

    {{ getUser('张三',18) }}

    <hr />
    {% from  'testMacro1.html' import getName %}

    {{ getName() }}

</body>
</html>
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    {% macro getName() %}
        陌上花开,可缓缓归矣
    {% endmacro %}


</body>
</html>
```

python中的代码

```python
# 结构标签
@blue.route('/testMacro/')
def testmacro():
    return render_template('testMacro.html')
```

#### 循环控制

- for循环
  - for...in
  - loop循环信息
    - 索引 loop.index,正向索引,从1开始
    - loop.index0:正向索引,从0开始
    - loop.revindex:反向索引到1结束
    - loop.revindex:反向索引到0结束
    - loop.first:第一个值
    - loop.last:最后一个值
- if判断
  - if...elif...else

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    说好不哭
    <hr />
    <ul>
        {% for score in score_list %}
             <li>{{ score }}</li>
        {% endfor %}
    </ul>
    <hr />
    <ul>
        {% for score in score_list %}
            {% if loop.first %}
                <li style="color:green">{{ score }}</li>
                {% elif loop.last %}
                    <li style="color:red">{{ score }}</li>
                {% else %}
                    <li style="color:pink">{{ score }}</li>
            {% endif %}
        {% endfor %}
    </ul>
    <hr />
    {% for score in score_list %}
        {{ loop.index }}
    {% endfor %}
    <hr />
    {% for score in score_list %}
        {{ loop.index0 }}
    {% endfor %}
    <hr />
    {% for score in score_list %}
        {{ loop.revindex }}
    {% endfor %}
    <hr />
    {% for score in score_list %}
        {{ loop.revindex0 }}
    {% endfor %}
    <hr />
</body>
</html>
```

#### 过滤器

- {{ 变量名|过滤器|过滤器 }}
- 过滤器没有数量限定,可以进行无限次过滤
- lower:将所有字母改为小写
- upper:将所有字母改为大写
- title:首字母大写
- trim:去掉前后的空格
- reverse:将字符串倒转
- striptages:渲染之前将值中的标签去掉
- safe:让标签生效

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    今天天气真晴朗
    <hr />
    {{ code|upper }}
    <hr />
    {{ code|upper|lower|title }}
    <hr>
    {{ code1|trim|reverse }}
    <hr>
    {{ code2 }}
    <hr>
    {{ code2|striptags }}
    <hr>
    {{ code2|safe }}
</body>
</html>
```

#### model

- 数据交互的封装

- Flask默认没有提供任何数据库的操作

- flask中可以自己选择数据,用原生语句实现功能

- 原生sql语句的缺点:

  - 代码利用率低,条件复杂代码语句过长,有很多相似语句
  - 一些sql是在业务逻辑中拼出来的,修改需要了解业务逻辑
  - 直接写SQL语句容易忽视SQL问题

- 也可以选择ORM

  - SQLAlchemy
  - MongoEngine
  - 将对象的操作转换为原生SQL
  - 优点:
    - 易用性,可以有效的减少重复SQL
    - 性能损耗少
    - 设计灵活,可以轻松实现复杂查询
    - 移植性好

- flask中并没有提供默认ORM

  - ORM:对象关系映射
  - 通过操作对象,实现对数据的操作

- flask-sqlalchemy

  - 使用步骤:1、安装`pip insatll flask-sqlalchemy`

  - 2、app.config配置SQLALCHEMY——DATABASE_URI

    ```
     dialect+driver://username:password@host:port/database
     数据库+驱动://用户:密码@主机:端口/数据库
     数据库选择：mysql：需要全部配置
     sqlite：轻量级数据库，配置简单
     sqlite:///xxxx (sqlite.db)
    ```

  - 3、创建SQLALCHEMY对象

    - 第一种方式：db=SQLAlcheny(app=app)
    - 第二种方式：

    ```
    db=SQLAlchemy()  # 这句话一般放在models中，因为需要db来调用
    db.init_app(app=app)  # 这句话在init中
    ```

  - 执行view中db.create_all()

    注意：必须添加主键，primary_key=True,自增：autoincrement=True

    运行时会出现警告：`'SQLALCHEMY_TRACK_MODIFICATIONS adds significant overhead and '`，在init中配置`config,app.config[‘SQLALCHEMY_TRAKE_MODIFICATIONS’]=False`

- 使用

  - 定义模型
    - 继承Sqlalchemy对象中红的model
  - 定义字段
    - 主键：一定要添加
    - 字段语法：db.Column(db.类型()，约束)，如果类型有长度一定要进行指定
  - 创建：db.create_all()
  - 删除：db.drop_all()
  - 修改表名：\_\_tablename\_\_="表名"
  - 添加数据：创建对象,字段赋值，添加：db.session.add()，提交：db.session.commit()
  - 查询数据：类名.query.all()，得到一个所有字段的列表

models.py

```python
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()


class Animal(db.Model):
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    name = db.Column(db.String(32))
    color = db.Column(db.String(32))

    __tablename__ = 'animal1'
```

\_\_init\_\_.py

```python
from flask import Flask

from App.models import db


def create_app():
    app = Flask(__name__)
    app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://root:abc123@localhost:3306/flaskday03'
    app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = False
    db.init_app(app=app)
    return app
```



view.py

```python
@blue.route('/createTable/')
def createTable():
    db.create_all()
    return '创建成功'


@blue.route('/dropTable/')
def dropTable():
    db.drop_all()
    return '删除成功'


@blue.route('/addanimal/')
def addanimal():
    a = Animal()
    a.name = 'hen'
    a.color = 'yellow'

    db.session.add(a)
    db.session.commit()

    return '添加成功'


@blue.route('/findall/')
def findall():
    animal_list = Animal.query.all()

    for animal in animal_list:
        print(animal)

    return '查询成功'
```

