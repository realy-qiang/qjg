## 一、ORM 对象-关系映射

### 1.概述

> ORM全称是：Object Relational Mapping(对象关系映射)。
>
> 其主要作用是在编程中把面向对象的概念跟数据库中表的概念对应起来。

例如：定义一个类，那就对应一张表，这个类的实例，就对应着表中的一条记录。

面向对象编程把所有实体看成对象（object），关系型数据库则是采用实体之间的关系（relation）连接数据。

**ORM的优点：**

* 数据模型都在一个地方定义,更容易更新和维护,也利于重用代码。
* ORM 有现成的工具,很多功能都可以自动完成,比如数据预处理、事务等等。
* 它迫使你使用 MVC 架构,ORM 就是天然的 Model,最终使代码更清晰。
* 基于 ORM 的业务代码比较简单,代码量少,语义性好,容易理解。
* 不需要编写性能不佳的 SQL。

Python下常用的ORM有：**Django-ORM、SQLAlchemy、Peewee**等。

### 2.实例：

这里使用SQLAlchemy来操作数据库

```python
import datetime

from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy import Column, String, Integer, Float, Date, Enum
from sqlalchemy.ext.declarative import declarative_base

# 建立数据库的连接
engine = create_engine('mysql+pymysql://root:abc123@localhost:3306/tornado')

Base = declarative_base(bind=engine)  # 创建模型的基础类
Session = sessionmaker(bind=engine)  # 创建会话


class User(Base):
    '''User模型'''

    __tablename__ = 'user'  # 该模型对应的表名
    id = Column(Integer, primary_key=True, autoincrement=True)
    name = Column(String(20), unique=True)
    # sex = Column(Enum('男', '女'), default='保密')
    birthday = Column(Date, default=datetime.date(1990, 1, 1))
    city = Column(String(10), default='上海')


Base.metadata.create_all()  # 创建表结构

# 定义的每一个对象，对应数据库的每一行数据
bob = User(name='bob', birthday=datetime.date(1990, 3, 21), city='上海')
jack = User(name='jack', birthday=datetime.date(1995, 9, 12))
stack = User(name='stack', birthday=datetime.date(1998, 5, 14), city='北京')
rose = User(name='rose',  birthday=datetime.date(1994, 3, 9), city='深圳')
henry = User(name='henry', birthday=datetime.date(1992, 3, 17), city='北京')
jerry = User(name='jerry', birthday=datetime.date(1987, 7, 28), city='上海')
hepburn = User(name='hepburn', birthday=datetime.date(1974, 2, 5), city='深圳')
ella = User(name='ella', birthday=datetime.date(1999, 5, 26), city='北京')

# 定义与数据库的会话
session = Session()

# 增加数据
session.add(bob)  # 在session中记录操作
session.add_all([bob, jack, stack, rose, henry, jerry, hepburn, ella])
session.commit()  # 提交到数据库执行

# 删除数据
session.delete(ella)
session.commit()

# 修改数据
bob.city = '重庆'
session.commit()

# 查询数据
q = session.query(User)  # 先定义表的查询对象
result = q.filter(User.id >= 5).order_by('birthday')

for user in result.all():
    print(user.id, user.name, user.birthday, user.city)

# 使用filter、filter_by(参数不需要带类名，=代替==)按条件查询
# 使用filter 进行范围查询，并对结果进行order_by排序
result2 = q.filter(User.id >= 5).order_by('birthday')
for user in result.all():
    print(user.id, user.name, user.birthday, user.city)

# 根据查询结果进行更新
q.filter(User.id == 1).update({'city': '上海'}, synchronize_session=False)
q.filter(User.id == 1).update({'city': '魔都' + user.city}, synchronize_session=False)
session.commit()

# 按数量取出数据：limit/offset
result = q.limit(3).offset(4)

# 检查是否存在
exists = q.filter_by(name='张旭').exists()
result3 = session.query(exists).scalar()
print(result)  # 懒加载 惰性加载 -> 惰性求值

# 计数
q.filter(User.id > 3).count()

# 分组 group_by
result = session.query(User.city).group_by('city')
for user in result:
    print(user.city)

```



## 二、Tornado的模板系统

> 模板系统是为了更快速、更方便的生产大量的页面而设计的一套程序。
>
> 借助模板系统，我们可以先写好页面大概的样子，然后预留好数据的位置，再然后将我们需要的数据,
> 按照既定规则拼到模板中的指定位置，然后渲染出完整页面。
>
> 现代的模板系统已经相当成熟,甚至可以通过 if...else 、 for 等语句在模板中写出简单的逻辑控制。

### 1.模版与静态文件的路径配置

定义 app 时,在 Application 中定义, 可以是相对路径, 也可以是绝对路径

```python
app = Application(
	templates_path = 'templates', # HTML模版文件夹名字
	static_path = 'statics' # 静态文件夹名字
)
```

```python
def make_app():
    routes = [
        (r"/", MainHandler),
        (r"/block", BlockHandler),
    ]

    # 获取模版目录和静态文件目录的绝对路径
    base_dir = os.path.dirname(os.path.abspath(__file__))
    template_dir = os.path.join(base_dir, 'templates')
    static_dir = os.path.join(base_dir, 'jingtai')

    return tornado.web.Application(routes, template_path=template_dir, static_path=static_dir)
```

### 2.模板中的变量

* 在模板中,**变量和表达式**使用 `{{ ... }} `包围,可以写入任何的 Python 表达式或者变量;

```html
<!DOCTYPE html>
<html lang='en'>
    <head>
        <meta charset='UTF-8'>
        <title>Templates</title>
    </head>
    <body>
        <h1>Hello World</h1>
        <hr />
        <div>你好 {{ name }},欢迎回来!</div>
        <div>猜一猜,3 x 2 等于几啊?</div>
        <div>我是不会告诉你等于 {{ 3 * 2 }}的</div>
    </body>
</html>
```

### 3.从 Python 程序中传递参数

```python
class MainHandler(tornado.web.RequestHandler):
    def get(self):
        # get请求方式，第一个参数：接受通过url中直接传入的参数的变量，第二个参数如果没有出入使用默认的值
        name = self.get_argument('name', 'admin')
        self.render('index.html', name=name)
```

### 4.模板中的 `if...else` 结构

* 模板中的控制语句使用 `{% ... %}` 包围,如下所示

```html
<h1 style="text-align: center;">Hello World</h1>
        {% if sex == '男' %}
        <div>你好 {{ name }}，欢迎回来先生！</div>
        {% elif sex == '女' %}
        <div>你好 {{ name }}，欢迎回来美女！</div>
        {% else %}
        <div>你好 {{ name }}，性别不明，不能入内？</div>
        {% end %}

        <hr />
```



### 5.模板中的 `for` 循环

* Python 程序中

  使用一个可迭代或者说可遍历的数据类型

```python
    def get(self):
        abc = self.get_argument('arg', 'hello!树哥')
        name = self.get_argument('name', 'admin')
        sex = self.get_argument('sex', '保密')
        menu = ['红烧肉', '水果沙拉', '糖醋排骨', '松花小肚', '波士顿龙虾', '三文鱼刺身']
        self.render('index.html', xyz=abc, name=name, sex=sex, menu=menu)
```

* 页面中:
  

```html
 您今天的菜谱请过目：
        <ol>
            {% for item in menu %}
            <li>{{ item }}</li>
            {% end %}
        </ol>
```



### 6.静态文件

* 参照第 1 小结添加静态文件的路径配置
* 所有的静态文件在使用的时候，都要以`'/static/'` 为固定URL前缀作为引导，然后文件路径拼接到后面。

![1568538217854](images/1568538217854.png)

* 在模板中使用静态文件时,静态文件的路径:

```html
<img class="avatar" src="/static/img/coder.jpg"/>
```

将所需静态文件的上两级目录名换成static进行访问

```python
# 获取模版目录和静态文件目录的绝对路径
    base_dir = os.path.dirname(os.path.abspath(__file__))
    template_dir = os.path.join(base_dir, 'templates')
    # 这里的静态文件目录是和Python文件同级的存放静态文件的目录名
    static_dir = os.path.join(base_dir, 'jingtai')
```



### 7.模板的继承

网站中,大多数页面都是相似的结构和风格,没有必要在所有页面中把相同的样式重复的写很多遍。

Tornado 为我们提供了了模板的继承机制,只需要写好父模板,然后让其他模板继承即可。

* 父模板文件名经常定义为` "base.html"`

```html
<!-- 内容区域 -->
<div class="content">
    <!--关键字block不能省略，然后使用一个变量名，containt，让两个模板产生联系-->
    {% block contain %}
    这里什么内容都没有
    {% end %}
</div>
```

* 子模板:

```html
{% extends "base.html" %}


{% block contain %}

<h1>{{ title }}</h1>
<p>
    {{ content }}
</p>

{% end %}
```

python中传入内容

```python
class BlockHandler(tornado.web.RequestHandler):
    def get(self):
        title = '题龙阳县青草湖'
        content = '''
                    西风吹老洞庭波，
                    一夜湘君白发多。
                    醉后不知天在水，
                    满船清梦压星河。
                '''
        self.render('article.html', title=title, content=content)
```

练习

```python
import tornado.ioloop
import tornado.web
from tornado.options import parse_command_line, define, options

from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy import Column, String, Integer, Float, Date, Enum, Boolean
from sqlalchemy.ext.declarative import declarative_base

import datetime
import os

# 建立数据库的连接
engine = create_engine('mysql+pymysql://root:abc123@localhost:3306/tornado')

Base = declarative_base(bind=engine)  # 创建模型的基础类
Session = sessionmaker(bind=engine)  # 创建会话


class User(Base):
    '''User模型'''

    __tablename__ = 'xiyou'  # 该模型对应的表名
    __table_args__ = {'mysql_engine': 'InnoDB', 'mysql_charset': 'utf8'}
    id = Column(Integer, primary_key=True, autoincrement=True)
    name = Column(String(20), unique=True)
    sex = Column(Enum('男', '女', '保密'), default='保密')
    city = Column(String(10), default='上海')
    description = Column(String(50), default='这家伙很懒，什么都没有留下...')
    birthday = Column(Date, default=datetime.date(1990, 1, 1))
    only_child = Column(Boolean(), default=True)


Base.metadata.create_all()  # 创建表结构

# 定义的每一个对象，对应数据库的每一行数据
user1 = User(name='孙悟空', sex='男', city='花果山', description='前期无敌，后期弱鸡', birthday=datetime.date(1990, 3, 21),
             only_child=False)
user2 = User(name='猪八戒', sex='男', city='花果山', description='散伙散伙，分家分家...', birthday=datetime.date(1994, 6, 2),
             only_child=False)
user3 = User(name='紫霞仙子', sex='女', city='盘丝洞', description='我的意中人，会身披战甲，脚踩七色祥云...', birthday=datetime.date(1992, 7, 1))
user4 = User(name='铁扇公主', sex='女', city='芭蕉洞', description='扇子很厉害...', birthday=datetime.date(1993, 10, 11),
             only_child=False)
user5 = User(name='牛魔王', sex='男', city='摩云洞', description='碧水精金兽很牛', birthday=datetime.date(1994, 3, 25))
user6 = User(name='沙僧', sex='男', city='流沙河', description='大师兄，师父被妖怪抓走了', birthday=datetime.date(1990, 5, 31))
user7 = User(name='唐僧', sex='男', city='长安', description='悟空，你为什么枉杀好人', birthday=datetime.date(1992, 9, 18),
             only_child=False)
user8 = User(name='女儿国国王', sex='女', city='女儿国', description='痴情一片...', birthday=datetime.date(1990, 9, 21))

# 定义与数据库的会话
session = Session()

# 增加数据
# session.add_all([user1, user2, user3, user4, user5, user6, user7, user8])
# session.commit()  # 提交到数据库执行

define("host", default='localhost', help="主机地址", type=str)
define("port", default=8000, help="主机端口", type=int)

q = session.query(User)


class QueryHandler(tornado.web.RequestHandler):
    def get(self):
        id = self.get_argument('id')
        result = q.filter_by(id=id).one()
        # print(result.id)
        self.write('id:%s,姓名:%s,性别:%s,居住地:%s,描述:%s,生日:%s,是否为独生子女:%s' % (
            result.id, result.name, result.sex, result.city, result.description, result.birthday, result.only_child))


class UpdateHandler(tornado.web.RequestHandler):
    def get(self):
        self.render('index1.html')

    def post(self):
        id = self.get_argument('id')
        name = self.get_argument('name')
        city = self.get_argument('city')

        desc = self.get_argument('desc')

        user = q.filter_by(id=id)
        print(user)
        user.update({'name': name, 'city': city, 'description': desc}, synchronize_session=False)
        session.commit()

        self.render('main.html')


class MainHandler(tornado.web.RequestHandler):
    def get(self):
        self.render('main.html')


class AllHandler(tornado.web.RequestHandler):
    def get(self):
        users = q.filter_by()

        self.render('article.html', menu=users)


class OneHandler(tornado.web.RequestHandler):
    def get(self):
        pass

    def post(self):
        name = self.get_argument('name')
        user = q.filter_by(name=name).one
        self.render('one.html', user=user)


def make_app():
    routes = [
        (r'/', MainHandler),
        (r"/query", QueryHandler),
        (r"/update", UpdateHandler),
        (r'/all', AllHandler),
        (r'/one', OneHandler)
    ]

    # 获取模版目录和静态文件目录的绝对路径
    base_dir = os.path.dirname(os.path.abspath(__file__))
    template_dir = os.path.join(base_dir, 'templates')
    static_dir = os.path.join(base_dir, 'jingtai')

    return tornado.web.Application(routes, template_path=template_dir, static_path=static_dir)


if __name__ == "__main__":
    parse_command_line()

    app = make_app()
   print('server running on %s:%s' % (options.host, options.port))
    app.listen(options.port, options.host)

    tornado.ioloop.IOLoop.instance().start()
```



