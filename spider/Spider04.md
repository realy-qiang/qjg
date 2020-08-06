# Spider

## selenium

### 简介

+ Selenium是一个用于web应用程序测试的工具
+ selenium测试直接运行在浏览器,就像真正的用户在操作一样
+ 支持通过各种driver驱动真实浏览器完成测试
+ selenium也是支持无界面浏览器的操作的

优点:

模拟浏览器功能,自动执行页面中的js代码,实现动态加载

### 安装

操作谷歌浏览器驱动下载地址:http://chromedriver.storage.googleapis.com/index.html 

谷歌驱动和谷歌浏览器版本之间的映射表:http://blog.csdn.net/huilan_same/article/details/51896672

查看谷歌浏览器版本:谷歌浏览器右上角-->帮助-->关于

pip install selenium

### 使用步骤

+ 导入:from selenium import webdriver

+ 创建谷歌浏览器操作对象

  path = 谷歌浏览器驱动文件路径

  browser = webdriver.Chrome(path)

+ 访问网址

  url = 要访问的网址

  browser.get(url)

### 元素定位

元素定位：自动化要做的就是模拟鼠标和键盘来操作来操作这些元素，点击、输入等

等。操作这些元素前首先要找到它们，WebDriver提供很多定位元素的方法

方法：

+ find_element_by_id    eg:button = browser.find_element_by_id('su')
+ find_elements_by_name   eg:name = browser.find_element_by_name('wd')
+ find_elements_by_xpath    eg:xpath1 = browser.find_elements_by_xpath('//input[@id="su"]')
+ find_elements_by_tag_name   eg:names = browser.find_elements_by_tag_name('input')
+ find_elements_by_css_selector    eg:my_input = browser.find_elements_by_css_selector('#kw')[0]
+ find_elements_by_link_text    eg:browser.find_element_by_link_text("新闻")

```python
from selenium import webdriver

path = '/home/qjg/PycharmProjects/Spider/day05/chromedriver'

browser = webdriver.Chrome(path)

url = 'http://www.baidu.com'

browser.get(url=url)

# kw = browser.find_element_by_id('kw')
#
# print(kw)

# 标签属性
# name = browser.find_elements_by_name('wd')
# print(name)

# xpath格式
# xname = browser.find_elements_by_xpath('//input[@id="kw"]')
# print(xname)

# bs4格式
# bname = browser.find_elements_by_css_selector('#kw')
# print(bname)

# 标签名
# tname = browser.find_element_by_tag_name('input')
# print(tname)

lname = browser.find_elements_by_link_text('地图')
print(lname)

browser.quit()
```



### 访问元素信息

+ 获取元素的信息    .get_attribute('class')
+ 获取文本信息    .text
+ 获取id   .id
+ 获取标签名 　 .tag_name

```python
from selenium import webdriver

path = '/home/qjg/PycharmProjects/Spider/day05/chromedriver'

browser = webdriver.Chrome(path)

url = 'http://www.baidu.com'

browser.get(url=url)

su = browser.find_element_by_id('su')

v = su.get_attribute('value')
print(v)

print(su.text)
print(su.id)
print(su.tag_name)


browser.quit()
```



### 交互

+ 点击:click()

+ 输入:send_keys()

+ 后退操作:browser.back()

+ 前进操作: browser.forword()

+ 模拟js滚动:

  js = 'document.body.scrollTop=100000'

  js = 'document.documentElement.scrollTop=100000'

  browser.execute_script(js)执行代码

+ 获取网页源码:page_source

+ 退出  browser.quit()

```python
import time

from selenium import webdriver

path = '/home/qjg/PycharmProjects/Spider/day05/chromedriver'

browser = webdriver.Chrome(path)

url = 'https://book.douban.com/tag/%E8%A8%80%E6%83%85'

browser.get(url)

time.sleep(2)

js = 'document.documentElement.scrollTop=100000'
browser.execute_script(js)

next = browser.find_element_by_css_selector('.next')[0]
next.click()

js = 'document.documentElement.scrollTop=100000'
browser.execute_script(js)

time.sleep(3)

browser.quit()
```

## Phantomjs

### 简介

是一个无界面的浏览器

支持页面元素查找，js的执行等

由于不进行css和gui渲染，运行效率要比真实的浏览器要快很多

### 使用步骤

获取PhantomJS.exe文件路径path

browser = webdriver.PhantomJS(path)

browser.get(url)

 扩展：保存屏幕快照:browser.save_screenshot('baidu.png')

```python
import time

from selenium import webdriver

path = '/home/qjg/PycharmProjects/Spider/day05/phantomjs'

browser = webdriver.PhantomJS(path)

url = 'https://www.baidu.com'

browser.get(url=url)

browser.save_screenshot('baidu.png')
time.sleep(2)


t = browser.find_element_by_id('kw')
t.send_keys('刘亦菲')
browser.save_screenshot('baidu1.png')

b = browser.find_element_by_id('su')

b.click()
browser.save_screenshot('baidu2.png')

js='document.documentElement.scrollTop=100000'
browser.execute_script(js)
browser.save_screenshot('baidu3.png')
```



## Chrome handless

### 系统要求

Chrome 

+ Unix\Linux 系统需要 chrome >= 59 
+  Windows 系统需要 chrome >= 60

Python3.6 

Selenium==3.4.* 

ChromeDriver==2.31

### 配置

```python
	from selenium import webdriver
	from selenium.webdriver.chrome.options import Options

    chrome_options = Options()
    chrome_options.add_argument('--headless')
    chrome_options.add_argument('--disable-gpu')

    path = r'C:\Program Files (x86)\Google\Chrome\Application\chrome.exe'
    chrome_options.binary_location = path

	browser = webdriver.Chrome(chrome_options=chrome_options)

	browser.get('http://www.baidu.com/')
```

### 配置封装

```python
from selenium import webdriver
          #这个是浏览器自带的  不需要我们再做额外的操作
          from selenium.webdriver.chrome.options import Options

          def share_browser():
              #初始化
              chrome_options = Options()
              chrome_options.add_argument('--headless')
              chrome_options.add_argument('--disable-gpu')
              #浏览器的安装路径    打开文件位置
              #这个路径是你谷歌浏览器的路径
              path = r'C:\Program Files (x86)\Google\Chrome\Application\chrome.exe'
              chrome_options.binary_location = path

              browser = webdriver.Chrome(chrome_options=chrome_options)

              return  browser
  封装调用：
          from handless import share_browser

          browser = share_browser()

          browser.get('http://www.baidu.com/')

          browser.save_screenshot('handless1.png')
```

## requests

### 文档

+ 官方文档   http://cn.python-requests.org/zh_CN/latest/
+ 快速上手   http://cn.python-requests.org/zh_CN/latest/user/quickstart.html

### 安装

pip install requests

### requests的属性和类型

类型:models.Response

属性:

+ r.text:获取网页源码,响应的是字符串
+ r.encoding :访问或定制编码方式
+ r.url:获取请求的url
+ r.content:获取网页源码,响应的是二进制
+ r.status_code:获取响应的状态码
+ r.headers:响应头信息

### get请求

requests.get()

定制参数

+ 参数使用params传递
+ 参数无需urlencode编码
+ 不需要请求对象的定制
+ 请求资源路径中？可加可不加

```python
import requests

# get请求的?可以加也可以不加
# url = 'https://www.baidu.com/s?'
url = 'https://www.baidu.com/s'

data = {
    'wd': '陈冠希'
}

headers = {
    'user-agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36',
}

response = requests.get(url=url, params=data, headers=headers)

content = response.text
print(response.url)
print(content)
```

### post

```python
import requests

url = 'https://fanyi.baidu.com/sug'

data = {
    'kw': 'abandon'
}

headers = {
    'user-agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36',
}

response = requests.post(url=url, data=data, headers=headers)

content = response.text
import json

obj = json.loads(content)

s = json.dumps(obj, ensure_ascii=False)
print(s)
```

### get与post的区别

+ get请求的参数名字是params post请求的参数的名字是data
+ 请求资源路径后面不加?
+ 不需要手动编码
+ 不需要做请求对象的定制 

### 代理

```python
import requests

url = 'http://httpbin.org/ip'

headers = {
    'user-agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36',
}

proxies = {
    'http': '183.166.102.92:9999'
    # 'http': '60.205.188.24:3128'
}

response = requests.get(url=url, headers=headers, proxies=proxies)

content = response.text

print(content)
```

### cookie定制

设置session

```python
import requests


url_get = 'http://www.jokeji.cn/user/c'

url = 'http://www.jokeji.cn/User/MemberCenter.asp'

headers = {
    'user-agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36',
}

response = requests.get(url=url, headers=headers)

content = response.text

print(content)
```

古诗文网

```python
import requests
from bs4 import BeautifulSoup
from Shibie import Recoginitier


headers = {
    'user-agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36',
}

url_login = 'https://so.gushiwen.org/user/login.aspx?from=http://so.gushiwen.org/user/collect.aspx'

session = requests.session()

response = requests.get(url=url_login, headers=headers)

content = response.text
soup = BeautifulSoup(content, 'lxml')

viewsstate = soup.select('#__VIEWSTATE')[0].attrs.get('value')

viewstategenerator = soup.select('#__VIEWSTATEGENERATOR')[0].attrs.get('value')

code = soup.select('#imgCode')[0].attrs.get('src')

code_url = 'https://so.gushiwen.org/' + code

# urllib.request.urlretrieve(url=code_url, filename='code.jpg')

response_code = session.get(url=code_url)

content_code = response_code.content

with open('code.jpg', 'wb') as fp:
    fp.write(content_code)

file_path = 'code.jpg'
s = Recoginitier()

codeName = s.esay_recoginition(file_path)
url = 'https://so.gushiwen.org/user/login.aspx?from=http%3a%2f%2fso.gushiwen.org%2fuser%2fcollect.aspx'
#
data = {
    '__VIEWSTATE': viewsstate,
    '__VIEWSTATEGENERATOR': viewstategenerator,
    'from': 'http://so.gushiwen.org/user/collect.aspx',
    'email': '1092684502@qq.com',
    'pwd': '1092684502',
    'code': codeName,
    'denglu': '登录'
}

response_post = session.post(url=url, headers=headers, data=data)

content_post = response_post.text

with open('gushiwen.html', 'w', encoding='utf-8') as fp:
    fp.write(content_post)
```

案例

14k壁纸

```python
import urllib.request
import requests

from bs4 import BeautifulSoup


def get_content(page):
    if page == 1:
        url = 'http://pic.netbian.com/4kmeinv/'
    else:
        url = 'http://pic.netbian.com/4kmeinv/index_' + str(page) + '.html'

    headers = {
        'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36',
        'Referer': 'http://pic.netbian.com/4kmeinv/',
    }

    response = requests.get(url=url, headers=headers)
    response.encoding = 'gbk'
    content = response.text

    return content


def down_load(content):
    soup = BeautifulSoup(content, 'lxml')

    img_list = soup.select('.slist>.clearfix img')
    for img in img_list:
        src = 'http://pic.netbian.com' + img.get('src')
        alt = img.get('alt')

        filename = './4k/' + alt + '.jpg'
        urllib.request.urlretrieve(url=src, filename=filename)


if __name__ == '__main__':
    start_page = int(input('请输入起始页码:'))
    end_page = int(input('请输入结束页码:'))

    for page in range(start_page, end_page + 1):
        content = get_content(page)
        download = down_load(content)
```



