# flask-05

### pagination

简介：分页器

需要想要的页码，每一页显示多少数据

- 原生代码

```python
student_list = Student.query.limit(page).offset((page_per-1)*page)
```

- 封装

```python
# 分页封装
@blue.route('/getPage/')
def getPage():
    # BaseQuery.paginate()参数：page:一页的个数 per_page页码数 error_out：是否抛出异常
    songs = Song.query.paginate(page=4, per_page=3).items
    for song in songs:
        print(song.name)

    return '分页成功'
```

pagination的属性：

- items：转化为列表类型
- pages：获取总页数
- prev_num：上一页的页码
- has_prev：是否有上一页
- has_next：是否有下一页
- iter_pages：遍历页数

### 逻辑运算

- 逻辑与：and_

```python
song = Song.query.filter(and_(Song.id == 1, Song.name == '凡人歌'))[0]
print(type(song))
print(song.id, song.name)
```

- 逻辑或：or_

```python
song = Song.query.filter(or_(Song.id == 1, Song.name == '凡人歌'))[0]
print(type(song))
print(song.id, song.name)
```

- 逻辑非：not_

```python
songs = Song.query.filter(not_(Song.name == '凡人歌'))
for song in songs:
    print(song.id, song.name)
```

- 在指定的条件内：in

```python
songs = Song.query.filter(Song.id.in_([1, 2, 3, 4]))
    for song in songs:
        print(song.id, song.name)
    return 'getLogin'
```

注意：and\_，or\_，导包时，导入\_operator、operator或者`sqlalchemy`都可以，not\_只能是`sqlalchemy`

### 数据定义

字段类型

- Integer
- String
- Date
- Boolean

约束

- primary_key：主键
- `autoincrement`：主键自增长
- unique：唯一
- default：默认
- index：索引
- not null：不为空
- `ForgeignKey`：外键

约束是用来约束级联数据获取

`db.Column(db.Iteger, db.ForeignKey(xxx.id))`

使用relationship实现级联数据获取

声明级联数据：backref='表名'

lazy=True

### 模型关系：

参数介绍:
 		 1.`relationship`函数
                    `sqlalchemy`对关系之间提供的一种便利的调用方式，关联不同的表；
          2.`backref`参数
                    对关系提供反向引用的声明，在Parent类上声明新属性的简单方法，之后可以在`Child.person`来获取这个对象的`person`；
          3.`lazy`参数
                    （1）`'select'`（默认值）
                        `SQLAlchemy `会在使用一个标准 select 语句时一次性加载数据；
                    （2）`'joined'`
                        让 `SQLAlchemy `当父级使用 JOIN 语句是，在相同的查询中加载关系；
                    （3）`'subquery'`
                        类似 'joined' ，但是 `SQLAlchemy` 会使用子查询；
                    （4）`'dynamic'`：
                        `SQLAlchemy` 会返回一个查询对象，在加载这些条目时才进行加载数据，大批量数据查询处理时推荐使用。
          4.`ForeignKey`参数
                    代表一种关联字段，将两张表进行关联的方式，表示一个person的外键，设定上必须要能在父表中找到对应的id值

一对多

```python
class Parent(db.Model):
    __table_args__ = {'mysql_engine': 'InnoDB', 'mysql_charset': 'utf8'}

    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    name = db.Column(db.String(20))

    # relationship，告诉ORM，Parent类需要被连接到User，可以通过主表查询从表，backref,让从表查询主表
    childen = db.relationship('Child', backref='parent', lazy=True)


class Child(db.Model):
    __table_args__ = {'mysql_engine': 'InnoDB', 'mysql_charset': 'utf8'}

    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    name = db.Column(db.String(20))

    parent_id = db.Column(db.Integer, db.ForeignKey('parent.id'))

```

模型的运用

```python
# 一对多模型
@blue.route('/addParent/')
def addParent():
    parent = Parent()
    parent.name = '张三'

    child = Child()
    child.name = '张四'

    child1 = Child()
    child1.name = '王五'

    child_list = [child, child1]

    parent.childen = child_list

    db.session.add(parent)
    db.session.commit()

    return '添加成功'


# 查询
# 主查从  给你一个parent 然后查询child的孩子
@blue.route('/getChild/')
def getChild():
    childs = Child.query.filter(Parent.id == 1)
    for child in childs:
        print(child.id, child.name)

    return '查询成功'


# 从查主
@blue.route('/getParent/')
def getParent():
    parent = Parent.query.filter(Parent.id == 1)[0]

    print(parent.name)

    return '查询成功'


# relationship函数是sqlalchemy对关系之间提供的一种便利的调用方式, backref参数则对关系提供反向引用的声明。
# 假如没有relationship，我们只能像下面这样调用关系数据如果
# 不加relationship 查询 给主表数据 然后查询从表数据
@blue.route('/getChild1/')
def getChild1():
    parent1 = Parent1.query.filter(Parent1.id == 1)[0]
    print(parent1.id)
    childs = Child1.query.filter(Child1.parent_id == parent1.id)
    for child in childs:
        print(child.name)

    return '查询成功'


# 加relationship，可以直接通过主表查询字表
@blue.route('/getChild2/')
def getChild2():
    parent = Parent1.query.filter(Parent1.id == 1)[0]
    childs = parent.children
    for child in childs:
        print(child.name)

    return '查询成功'

# 不加backref， 不能通过从表查询主表
@blue.route('/getParent1/')
def getParent1():
    child = Child1.query.filter(Child1.id == 1)[0]
    print(child.parent.name)
    return '查询成功'
```

一对一：

```python
# 一对一
class User(db.Model):
    __table_args__ = {'mysql_engine': 'InnoDB', 'mysql_charset': 'utf8'}

    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    name = db.Column(db.String(20))
    # uselist=False 是在模型执行的时候，会验证 从表中是否有重复的数据
    address = db.relationship('Address', backref='parent', lazy=True, uselist=False)


class Address(db.Model):
    __table_args__ = {'mysql_engine': 'InnoDB', 'mysql_charset': 'utf8'}

    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    name = db.Column(db.String(20))

    user_id = db.Column(db.Integer, db.ForeignKey('user.id'))
```

多对多

```python
class Movie(db.Model):
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    name = db.Column(db.String(32))


class Collection(db.Model):
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)

    u_id = db.Column(db.ForeignKey(User1.id))
    m_id = db.Column(db.ForeignKey(Movie.id))

    num = db.Column(db.Integer)
```

```python
# 多对多
# 第一次插入到数据库，第二次在原来的基础上加1
@blue.route('/addCollection/')
def addCollection():
    u_id = int(request.args.get('u_id'))
    m_id = int(request.args.get('m_id'))

    collection = Collection.query.filter(Collection.u_id == u_id).filter(Collection.m_id == m_id)

    if collection.count() > 0:
        collection = collection[0]
        collection.num = collection.num + 1

    else:
        collection = Collection()
        collection.u_id = u_id
        collection.m_id = m_id
        collection.num = 1

    db.session.add(collection)
    db.session.commit()

    return '添加成功'
```

### flask-bootstrap

- 插件安装
  `pip install  flask-bootstrap`
- ext中初始化
  `Bootstrap（app=app）`
    	
  bootstrap案例--bootstrap模板   {% extends ‘bootstrap/base.html’%}

```python
# flask-bootstrap
@blue.route('/bootstrapDemo/')
def bootstrapDemo():
    page = int(request.args.get('page', 1))
    per_page = request.args.get('per_page', 4)

    pagination = Movie1.query.paginate(page=page, per_page=per_page)
    print(type(pagination.items))

    return render_template('bootstrapDemo.html', pagination=pagination, page=page)
```

### flask-debugtoolbar

- 辅助调试插件

- 安装
  `pip install flask-debugtoolbar`

- 初始化  ext     

  ```
  app.debug = True (最新版本需要添加)
  debugtoolbar = DebugToolBarExtension()
  debugtoolbar.init_app(app=app)
  ```

