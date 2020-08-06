# Spider01

## 爬虫概念

通过一个程序，根据url进行爬取网页，获取有用信息

使用程序模拟浏览器向浏览器发送请求，获取相应信息

## 爬虫的核心

爬取网页：爬取整个网页，包含了网页中得到的内容

解析数据：将网页中得到的数据进行解析

难点：爬虫与反爬虫之间的博弈

## 爬虫的用途

+ 数据分析/人工数据集
+ 社交软件冷启动
+ 舆情监控
+ 竞争对手监控

## 爬虫语言分类

+ php:多进程和多线程支持不好
+ java:目前java爬虫需求岗位旺盛，python爬虫的主要竞争对手，代码臃肿，代码量大，重构成本高，而爬虫需要经常修改，不好用
+ c\c++ :学习成本高，性能和效率高，停留在研究层面，市场需求量小，体现程序员能力
+ python:语法简介优美，对新手友好，学习成本低、支持的模块非常多，有scrapy非常强大的爬虫框架

## 爬虫分类

+ 通用爬虫
  + 百度，360，Google，搜狗等搜索引擎
  + 功能：访问网站->抓取数据->数据存储->数据处理->提供检索服务
  + robots协议：一个约定俗成的协议，添加robots.txt文件，来说明那些网站的内容不可以抓取，起不到限制作用
  + 网站排名(SEO):根据pagerank算法值进行排名，百度竞价排名，钱多就往前排
  + 缺点：抓取到的数据大多无用，不能根据用户的需求来精准获取数据
+ 聚焦爬虫
  + 根据需求实现爬虫程序，抓取需要的数据
  + 原理：
    + 网页都有自己唯一的url
    + 网页都是html组成
    + 传输协议都是http\https
  + 设计思路：
    + 确定要爬取的url
    + 模拟浏览器通过http协议访问url，获取从服务器返回的html代码
    + 解析html字符串(根据一定规则提取需要的数据

## 反爬手段

### User-Agent

User-Agent中文名为用户代理，简称UA，它是一个特殊字符串头，使得服务器能够识别客户使用的操作系统及版本、CPU类型、浏览器及版本、浏览器渲染、浏览器语言、浏览器插件等

### 代理

西次代理、快代理

代理级别：

透明代理：使用透名代理，对方服务器可以知道你使用了代理，并且知道你真的真是IP

匿名代理：使用匿名代理，对方服务器知道你使用了代理，但不知道你的真是IP

高匿名代理：使用高匿名代理，对方服务器不知道你使用了代理，更不知道你的真实IP

### 验证码访问

打码平台：云打码平台，超级鹰

### 动态加载网页 

网站返回的是JS数据 并不是网页的真是数据

selenium驱动真实的浏览器发送求情

### 数据加密

分析js代码

## Http协议

### http和https的区别：

http:明文传输，端口号为80，http协议(超文本传输协议)，是一种发布和接受HTML页面的方法

https:加密传输，端口号443，简单讲就是HTTP的安全版，在HTTP下加入SSL层，HTTPS=HTTP+SSL

SSL：安全   套阶层，主要用于web的安全传输协议，在传输层对网页连接进行加密，保障Internet上数据传输的安全



### SSL

Source Socket Layer

安全认证 关于CA，12306网站证书是自己的

安全认证requests 安全认证urllib

注意：如果报错SSL，那么解决方案是：import urllib.request import ssl

ssl._create_deafual_hhtps_context = ssl.\_create\_unverified_context

### 常见的服务器端口号

+ ftp : 21
+ ssh : 22
+ mysql : 3306
+ oracle : 1521
+ MongoDB : 27017
+ redis : 6379

### http工作原理

url组成：协议 主机 端口号 路径 参数 锚点

上网原理：http请求和响应

请求行：请求头和请求体

响应行：响应头和响应体

请求头详情：

Accept
				Accept-Encoding

​				Accept-Language

​				Cache-Control

​				Connection

​				Cookie

​				Host

​				Upgrade-Insecure-Requests    http是否升级为https

​				User-Agent 

​				X-Requested-With             是否是ajax请求

​				Referer                      上一级路径

响应头详解
				Connection

​				Content-Encoding

​				Content-Type

​				Date

​				Expires

​				Server

​				Transfer-Encoding 内容是否分包传输
​			常见HTTP状态码
​				200
​					请求成功
​				404
​					未找到资源
​				500
​					服务器内部错误

## urllib库使用

urllib.request.urlopen()模拟浏览器向服务器发送请求

request 服务器返回的数据

response的数据类型是HttpResponse

字节------>字符串：解码decode

字符串----->字节：编码encode

read() 字节形式读取二进制 扩展：read(5)返回前五个字节

readline():读取一行

readlines():一行一行读取整个内容

getcode():得到状态码

geturl():获取url

gettheaders():获取headers

urllib.request.urlretrieve():请求网页 请求图片 请求视频

```python
import urllib.request

url = 'https://www.baidu.com'

response = urllib.request.urlopen(url=url)

# 以二进制的格式来获取网站源码
# print(response.read())

# 读取前五个字节
# print(response.read(5))

# 编码 编写二进制的格式  字符串=====》二进制
# 解码 编写成字符串格式  二进制======》 字符串
# 以字符串的格式来读取网站源码
# print(response.read().decode('utf-8'))

# readline
# 以二进制的格式一行一行读取
# a = response.readline()
# print(a)

# a = response.readlines()
# print(a)

# a = response.readline(5)
# print(a)

# 获取状态码
print(response.getcode())

# 获取url
print(response.geturl())

# 获取请求头信息
print(response.getheaders())
```

```python

```

## 请求对象的定制

request = urllib.Request()

```python
# 默认情况下urllib.request.urlopen方法。访问服务器携带的是python的ua
# 很多网站会检测ua的真实性 如果是一个爬虫程序 那么将刽给你返回数据
url = 'http://www.baidu.com'

# response = urllib.request.urlopen(url=url)
headers = {
    'User-Agent':'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'

}

request = urllib.request.Request(url=url, headers=headers)

response = urllib.request.urlopen(request)

print(response.getcode())
```

## 编解码

get请求方法，urllib.parse.quote()

```python
# 编码的基本使用
# s = '华夏'
#
# name = urllib.parse.quote(s)
# print(name)


# 默认情况下 浏览器会自动编解码
# pycharm不会自动编解码
url = 'https://www.baidu.com/s?wd='

wd = '华夏'
# 将中文进行编码
wd = urllib.request.quote(wd)
# url进行拼接
url = url + wd
# print(url)
response = urllib.request.urlopen(url=url)

t = response.read().decode('utf-8')

print(t)
```

```python
# url headers data参数
url = 'http://www.baidu.com/s?wd='

# 编码
data = '1906班'
data = urllib.parse.quote(data)

# url拼接
url = url + data

# ua
headers = {
    'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'

}

# 请求对象的定制
request = urllib.request.Request(url=url, headers=headers)

# 模拟浏览器向服务器发送请求
response = urllib.request.urlopen(request)

# 读服务器响应的页面源码
p_source = response.read().decode('utf-8')
print(p_source)
```

get请求，多参数 urllib.request.urlencode()

```python
url = 'http://www.baidu.com/s?'

data = {
    'wd': '韩美娟',
    'sex': '不知道'
}

data = urllib.parse.urlencode(data)

url = url + data

headers = {
    'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'

}

request = urllib.request.Request(url=url, headers=headers)

response = urllib.request.urlopen(request)

print(response.read().decode('utf-8'))

```

post请求

```python
url = 'https://fanyi.baidu.com/sug'

data = {
    'kw': 'style'
}

headers = {
    'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'
}

# post请求必须进行编码.
data = urllib.parse.urlencode(data).encode('utf-8')

request = urllib.request.Request(url=url, headers=headers, data=data)

response = urllib.request.urlopen(request)

content = response.read().decode('utf-8')

import json

obj = json.loads(content)
s = json.dumps(obj, ensure_ascii=False)
print(s)
```

get和post的区别：

1：get请求方式的参数必须编码，参数是拼接到url后面，编码之后不需要调用encode方法
2：post请求方式的参数必须编码，参数是放在请求对象定制的方法中，编码之后需要调用encode方法

## ajax的get请求

爬取豆瓣电影前十页

```python
import urllib.request
import urllib.parse

for i in range(0, 200, 20):
    url = 'https://movie.douban.com/j/chart/top_list?type=5&interval_id=100%3A90&action=&'
    data = {
        'start': i,
        'limit': 20
    }

    data = urllib.parse.urlencode(data)

    url = url + data

    headers = {
        'User-Agent': 'Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_8; en-us) AppleWebKit/534.50 (KHTML, like Gecko) Version/5.1 Safari/534.50'
    }

    request = urllib.request.Request(url=url, headers=headers)

    response = urllib.request.urlopen(request)

    content = response.read().decode('utf-8')

    name = 'douban' + str(int(i/20)+1) + '.json'

    # print(response.geturl())

    with open(name, 'w', encoding='utf-8')as fp:
        fp.write(content)
```

