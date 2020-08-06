# Spider-05

## Scrapy

### 简介

Scrapy是一个为了爬取网站数据，提取结构性数据而编写的应用框架。 可以应用在包括

数据挖掘，信息处理或存储历史数据等一系列的程序中。



### 安装

pip install scrapy

安装中出错

building 'twisted.test.raiser' extension

error: Microsoft Visual C++ 14.0 is required. Get it with "Microsoft Visual C++ 			 

Build Tools": http://landinghub.visualstudio.com/visual-cpp-build-tools



解决方案

http://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted 下载twisted对应版本的whl文

件（如我的Twisted-17.5.0-cp36-cp36m-win_amd64.whl），cp后面是            

python版本，amd64代表64位，运行命令：

pip install C:\Users\...\Twisted-17.5.0-cp36-cp36m-win_amd64.whl

pip install Scrapy

如果再报错   win32

解决方法：pip install pypiwin32

再报错：使用anaconda

使用步骤：

+ 打开anaconda
+ 点击environments
+ 点击not installed
+ 输入scrapy
+ apply
+ 在pycharm中选择anaconda的环境

### scrapy项目的创建以及运行

创建项目: scrapy startproject 项目名

项目的组成

spiders

  \_\_init\_\_.py

自定义的爬虫文件.py       ---》由我们自己创建，是实现爬虫核心功能的文

\_\_init\_\_.py                  

 items.py                     ---》定义数据结构的地方，是一个继承自scrapy.Item的类

middlewares.py               ---》中间件   代理

pipelines.py				  ---》管道文件，里面只有一个类，用于处理下载数据的后续处理
										默认是300优先级，值越小优先级越高（1-1000）

settings.py				  ---》配置文件  比如：是否遵守robots协议，User-Agent定义等

#### 创建爬虫文件

跳转到spiders文件夹   cd 目录名字/目录名字/spiders

scrapy genspider 爬虫名字 网页的域名

爬虫文件的基本组成：

+ 继承scrapy.Spider类
+ name = 'qiubai'       ---》  运行爬虫文件时使用的名字
+ allowed_domains       ---》 爬虫允许的域名，在爬取的时候，如果不是此域名之下的url，会被过滤掉
+ start_urls			 ---》 声明了爬虫的起始地址，可以写多个url，一般是一个
+ parse(self, response) ---》解析数据的回调函数
  + response.text
  + response.body ---》响应的是二进制文件
  + response.xpath()-》xpath方法的返回值类型是selector对象
+ extract()             ---》提取的是selector对象的是data
+ extract_first()       ---》提取的是selector列表中的第一个数据

#### 运行爬虫文件

scrapy crawl 爬虫名

注意 应该在spider文件夹内执行

### 导出文件

运行时在 scrapy crawl 爬虫名后面加上

+ -o name.json   json文件
+ -o name.xml   xml文件
+ -o name.csv    excel格式的文件

### scrapy框架的组成

引擎 :自动运行，无需关注，会自动组织所有的请求对象，分发给下载器

下载器:从引擎处获取到请求对象后，请求数据

spiders:Spider类定义了如何爬取某个(或某些)网站。包括了爬取的动作(例如:是否跟进链接)以及如何从网页的内

容中提取结构化数据(爬取item)。 换句话说，Spider就是您定义爬取的动作及分析某个网页(或者是有些网页)的地

方。

调度器:有自己的调度规则，无需关注

管道（Item pipeline）:最终处理数据的管道，会预留接口供我们处理数据

当Item在Spider中被收集之后，它将会被传递到Item Pipeline，一些组件会按照一定的顺序执行对Item的处理。

每个item pipeline组件(有时称之为“Item Pipeline”)是实现了简单方法的Python类。他们接收到Item并通过它

执行一些行为，同时也决定此Item是否继续通过pipeline，或是被丢弃而不再进行处理。

以下是item pipeline的一些典型应用：

+ 清理HTML数据
+ 取的数据(检查item包含某些字段)
+ 查重(并丢弃)
+ 结果保存到数据库中

### 工作原理

![scrapty原理](/home/qjg/桌面/资料/Spider/day06/doc/scrapy╘¡└φ.png)

![scrapy原理 英文版](/home/qjg/桌面/资料/Spider/day06/doc/scrapy╘¡└φ_╙ó╬─.png)

zhanzhang.py

```python
# -*- coding: utf-8 -*-
import scrapy

from ..items import ZhanzhangItem


class ZhangzhanSpider(scrapy.Spider):
    name = 'zhangzhan'
    allowed_domains = ['http://sc.chinaz.com/tupian/chengshijingguantupian.html']
    start_urls = []
    start_page = int(input('请输入起始页码'))
    end_page = int(input('请输入结束页码:'))

    for page in range(start_page, end_page + 1):
        if page == 1:
            url = 'http://sc.chinaz.com/tupian/chengshijingguantupian.html'
            start_urls.append(url)
        else:
            url = 'http://sc.chinaz.com/tupian/chengshijingguantupian_' + str(page) + '.html'
            start_urls.append(url)

        print(start_urls)

    def parse(self, response):

        print(response.url)
        img_list = response.xpath('//div[@id="container"]//a/img')

        images_list = []
        for img in img_list:
            src = img.xpath('./@src2').extract_first()
            alt = img.xpath('./@alt').extract_first()
            # 不要使用以下方式去创建对象 因为数据结构不固定  容易发生数据安全问题
            # sky = {}
            # sky['src']=src
            # sky['alt']=alt
            # sky['name']='zs'
            images = ZhanzhangItem(src=src, alt=alt)
            images_list.append(images)

        return images_list
```

items.py

```python
# -*- coding: utf-8 -*-

# Define here the models for your scraped items
#
# See documentation in:
# https://docs.scrapy.org/en/latest/topics/items.html

import scrapy


class ZhanzhangItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    src = scrapy.Field()
    alt = scrapy.Field()
```

pipelines.py

```python
# -*- coding: utf-8 -*-

# Define your item pipelines here
#
# Don't forget to add your pipeline to the ITEM_PIPELINES setting
# See: https://docs.scrapy.org/en/latest/topics/item-pipeline.html
import urllib.request


class ZhanzhangPipeline(object):
    # def __init__(self):
    #     print('========start==========')

    # 在爬虫文件开始时 就对文件进行写入
    def open_spider(self, spider):
        # print('=========start===========')

        self.fp = open('zz.json', 'a+', encoding='utf-8')

        print('===============start====================')

    def process_item(self, item, spider):
        # with open('zz.json', 'a', encoding='utf-8') as fp:
        #     fp.write(str(item))

        self.fp.write(str(item))


        return item

    # 在爬虫文件结束时进行关闭
    def close_spider(self, spider):
        self.fp.close()


class ZhanzhangDownLoadPipeline(object):
    def process_item(self, item, spider):
        src = item['src']
        alt = item['alt']

        print(src)
        print(alt)

        filename = './zhanzhang/' + alt + '.jpg'
        urllib.request.urlretrieve(url=src, filename=filename)
```

多个下载任务需要写多个class

这是需要在setting进行设置

300代表权重 范围在1~1000值越小权重越重

```python
ITEM_PIPELINES = {
    'zhanzhang.pipelines.ZhanzhangPipeline': 300,
    'zhanzhang.pipelines.ZhanzhangDownLoadPipeline': 301
}
```

### scrapy shell

1. Scrapy终端，是一个交互终端，供您在未启动spider的情况下尝试及调试您的爬取代

码。 其本意是用来测试提取数据的代码，不过您可以将其作为正常的Python终端，在

上面测试任何的Python代码。

该终端是用来测试XPath或CSS表达式，查看他们的工作方式及从爬取的网页中提取的

数据。 在编写您的spider时，该终端提供了交互性测试您的表达式代码的功能，免去了

每次修改后运行spider的麻烦。

一旦熟悉了Scrapy终端后，您会发现其在开发和调试spider时发挥的巨大作用。

2. 安装ipython          pip install ipython

3. 简介：如果您安装了 IPython ，Scrapy终端将使用 IPython (替代标准Python终端)。 IPython 终端与其他相比更为强大，提供智能的自动补全，高亮输出，及其他特性。

4. 应用：

   scrapy shell www.baidu.com

   scrapy shell http://www.baidu.com

   scrapy shell "http://www.baidu.com"

   scrapy shell "www.baidu.com"

   在不同的操作系统中使用方式不同,以上四种适用于大多数操作系统

5. response对象

   + response.body
   + response.text
   +  response.url
   + response.status

6. response的解析

   + response.xpath()使用xpath路径查询特定元素，返回一个selector列表对象

   + response.css():使用css_selector查询元素，返回一个selector列表对象
   + 获取内容 ：response.css('#su::text').extract_first()
   + 获取属性 ：response.css('#su::attr(“value”)').extract_first()

7. selector对象（通过xpath方法调用返回的是seletor列表）

   + extract():使用xpath请求到的对象是一个selector对象，需要进一步使用extract()方法拆包，转换为unicode字符串

   + extract_first():返回第一个解析到的值，如果列表为空，此种方法也不会报错，会返回一个空值

   + xpath()

   + css()

   注意：每一个selector对象可以再次的去使用xpath或者css方法

8. item对象 

   + dict(itemobj):可以使用dict方法直接将item对象转换成字典对象
   +  Item(dicobj):可以使用字典对象创建一个Item对象

