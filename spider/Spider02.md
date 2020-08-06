# Spider02

## 贴吧源代码

```python
def create_request(page):
    url = 'https://tieba.baidu.com/f?kw=%E5%89%91%E6%9D%A5&ie=utf-8&pn='

    data = str((page - 1) * 50)

    url = url + data

    headers = {
        'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'
    }

    request = urllib.request.Request(url=url, headers=headers)

    return request


def get_content(request):
    response = urllib.request.urlopen(request)

    content = response.read().decode('utf-8')

    return content


def download(page, content):
    name = 'tiaba_' + str(page) + '.html'
    with open(name, 'w', encoding='utf-8') as fp:
        fp.write(content)


if __name__ == '__main__':
    start_page = int(input('请输入起始页码：'))
    end_page = int(input('请输入结束页码：'))

    for page in range(start_page, end_page+1):
        request = create_request(page)
        content = get_content(request)
        download(page, content)

```

## URLError/HTTPError

简介:

1.HTTPError类是URLError类的子类

2.导入的包urllib.error.HTTPError    urllib.error.URLError

3.http错误：http错误是针对浏览器无法连接到服务器而增加出来的错误提示。引导并告诉浏览者该页是哪里出了问题。

4.通过urllib发送请求的时候，有可能会发送失败，这个时候如果想让你的代码更加的健壮，可以通过try-except进行捕获异常，异常有两类，URLError\HTTPError

```python
import urllib.request
import urllib.error

# url = 'https://blog.csdn.net/m0_37907797/article/details/102661778'

url = 'https://blog.csdn11111.net/m0_37907797/article/details/102661778'

headers = {
    # 'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3',
    # 'Accept-Encoding': 'gzip, deflate, br',
    # 'Accept-Language': 'zh-CN,zh;q=0.9',
    # 'Cache-Control': 'max-age=0',
    # 'Connection': 'keep-alive',
    # 'Cookie': 'uuid_tt_dd=10_19284691370-1530006813444-566189; smidV2=2018091619443662be2b30145de89bbb07f3f93a3167b80002b53e7acc61420; _ga=GA1.2.1823123463.1543288103; dc_session_id=10_1550457613466.265727; acw_tc=2760821d15710446036596250e10a1a7c89c3593e79928b22b3e3e2bc98b89; Hm_lvt_e5ef47b9f471504959267fd614d579cd=1571329184; Hm_ct_e5ef47b9f471504959267fd614d579cd=6525*1*10_19284691370-1530006813444-566189; __yadk_uid=r0LSXrcNYgymXooFiLaCGt1ahSCSxMCb; Hm_lvt_6bcd52f51e9b3dce32bec4a3997715ac=1571329199,1571329223,1571713144,1571799968; acw_sc__v2=5dafc3b3bc5fad549cbdea513e330fbbbee00e25; firstDie=1; SESSION=396bc85c-556b-42bd-890c-c20adaaa1e47; UserName=weixin_42565646; UserInfo=d34ab5352bfa4f21b1eb68cdacd74768; UserToken=d34ab5352bfa4f21b1eb68cdacd74768; UserNick=weixin_42565646; AU=7A5; UN=weixin_42565646; BT=1571800370777; p_uid=U000000; dc_tos=pzt4xf; Hm_lpvt_6bcd52f51e9b3dce32bec4a3997715ac=1571800372; Hm_ct_6bcd52f51e9b3dce32bec4a3997715ac=1788*1*PC_VC!6525*1*10_19284691370-1530006813444-566189!5744*1*weixin_42565646; announcement=%257B%2522isLogin%2522%253Atrue%252C%2522announcementUrl%2522%253A%2522https%253A%252F%252Fblogdev.blog.csdn.net%252Farticle%252Fdetails%252F102605809%2522%252C%2522announcementCount%2522%253A0%252C%2522announcementExpire%2522%253A3600000%257D',
    # 'Host': 'blog.csdn.net',
    # 'Referer': 'https://passport.csdn.net/login?code=public',
    # 'Sec-Fetch-Mode': 'navigate',
    # 'Sec-Fetch-Site': 'same-origin',
    # 'Sec-Fetch-User': '?1',
    # 'Upgrade-Insecure-Requests': '1',
    'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'
}

try:
    request = urllib.request.Request(url=url, headers=headers)
    response = urllib.request.urlopen(request)

    content = response.read()

    print(content)

except urllib.error.HTTPError:
    print(1111111)
except urllib.error.URLError:
    print(222222222)
```

## cookie登录

### 人人网登录

```python
import urllib.request
import urllib.parse

url = 'http://www.renren.com/305523888/profile'
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.120 Safari/537.36',
    'Cookie': 'anonymid=jix3nuu4-498h3n; _de=BF83005E46A2ACDF72FFEFECAA50653A696BF75400CE19CC; ln_uact=595165358@qq.com; ln_hurl=http://hdn.xnimg.cn/photos/hdn521/20170509/0940/main_5crY_aee9000088781986.jpg; _r01_=1; jebe_key=b8a3f973-563c-4e6a-ac8f-99deef080f20%7Ccfcd208495d565ef66e7dff9f98764da%7C1565664618046%7C0%7C1565664616181; wp_fold=0; depovince=SH; jebecookies=dd7a7840-774a-44e8-89ab-36942629e3af|||||; JSESSIONID=abc61vSCzpF2xhumYp33w; ick_login=f9f873c2-206c-4c17-9cb9-ec4c7e3044d9; p=b178623a64102cc30833baf87c1c33f28; first_login_flag=1; t=ec055348320811e3b0b3345d15afe18c8; societyguester=ec055348320811e3b0b3345d15afe18c8; id=305523888; xnsid=8bff41c; loginfrom=syshome; jebe_key=b8a3f973-563c-4e6a-ac8f-99deef080f20%7Cdca572dcc866b00768c874af75fd79ec%7C1571811553654%7C1%7C1571811557213'
}
request = urllib.request.Request(url=url, headers=headers)
response = urllib.request.urlopen(request)
content = response.read().decode('utf-8')
print(content)

```

### 微博登录

```python
import urllib.request

url = 'https://weibo.cn/6451491586/info'

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.120 Safari/537.36',
    'cookie': 'SCF=Ahi2Sm3XHpcYIJvIsbJd8AnqkyO8t5RFmHXn8yHeTOMYgumvEqFGsgNbZbD6BmzlV7GA-B8sNWcbTcHeVmF3eNc.; _T_WM=661e0af7353e4ce48f5c9cfa8368bce5; SUB=_2A25wq4T0DeRhGeBK7lMV-S_JwzqIHXVQVyy8rDV6PUJbkdAKLXTakW1NR6e0UGDjQ3X7s7TSUa_J_IHy8f7dJeXJ; SUHB=0idbj3TaBzD_2G; SSOLoginState=1571812516',
}

request = urllib.request.Request(url=url, headers=headers)
response = urllib.request.urlopen(request)

content = response.read().decode('utf-8')

print(content)
```

登录时用户名缓存问题：通过清楚浏览器缓存来解决

```python
import urllib.request
import urllib.parse

url = 'https://passport.weibo.cn/sso/login'


headers={
		'User-Agent':'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/34.0.1847.137 Safari/537.36 LBBROWSER'
		}


data = {
    'username': '18642820892',
    'password': 'lijing1150501',
    'savestate': '1',
    'r': 'https://weibo.cn/',
    'ec': '0',
    'pagerefer': 'https://weibo.cn/pub/?vt=',
    'entry': 'mweibo',
    'wentry': '',
    'loginfrom': '',
    'client_id': '',
    'code': '',
    'qq': '',
    'mainpageflag': '1',
    'hff': '',
    'hfp':'',
}

data = urllib.parse.urlencode(data).encode('utf-8')

request = urllib.request.Request(url=url,headers=headers, data=data)

response = urllib.request.urlopen(request)

content = response.read().decode('utf-8')

print(content)
```

### Handle处理器

指定高级请求头

urllib.request.urlopen(url):不能指定请求头

urllib.request.Resquest(url=url, headers=headers, data=data)：指定简单请求头

urllib.request.HTTPHandler():可以进行动态cookie和代理的设置

```python
import urllib.request
url = 'http://www.baidu.com'
headers = {
        'User - Agent': 'Mozilla / 5.0(Windows NT 10.0;Win64;x64) AppleWebKit / 537.36(KHTML, likeGecko) Chrome / 74.0.3729.169Safari / 537.36'
    }
request = urllib.request.Request(url=url,headers=headers)

handler = urllib.request.HTTPHandler()

opener = urllib.request.build_opener(handler)

response = opener.open(request)

print(response.read().decode('utf-8'))
```

## 代理服务器

代理服务器是一种重要的服务器安全功能，它的主要工作在系统互联(OSI)模型的会话层，从而起到防火墙的作用

翻墙：指的是绕过响应的IP封锁，内容过滤，域名劫持，流量限制等

代理的常用功能：

+ 突破自身IP访问限制，访问国外站点
+ 访问一些单位或团体的内部资源
  + 某个大学的FTP，使用教育网内地址段免费代理服务器，就可以对教育网开放的各类FTP下载上传，以及各类资料查询共享等服务(前提是该代理地址在该资源允许访问范围之内)
+ 提高范文速度
  + 服务器都设置一个较大的硬盘缓冲区，当有外界的信息通过时，同时也将其保存到缓冲区中，当其他用户再访问相同的信息时， 则直接由缓冲区中取出信息，传给用户，以提高访问速度
+ 隐藏真是IP
  + 上网者也可以通过这种方法隐藏自己的IP，免受攻击

代码配置：

创建request对象

创建ProxyHandler

用handler创建opener对象

使用opener。open函数发送请求

### 代理

```python
import urllib.request

url = 'http://www.baidu.com/s?wd=ip'

headers = {
    'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'
}

request = urllib.request.Request(url=url, headers=headers)

proxy = {
    'http': '140.143.142.218:1080'
}

handler = urllib.request.ProxyHandler(proxies=proxy)

opener = urllib.request.build_opener(handler)

response = opener.open(request)

content = response.read().decode('utf-8')

with open('baidu.html', 'w', encoding='utf-8') as fp:
    fp.write(content)
```

收费代理

```python
import urllib.request

# 在快代理购买ip后，会有个提取ip链接，通过该连接得到ip
ip_url = 'http://kps.kdlapi.com/api/getkps/?orderid=987181651846636&num=1&pt=1&sep=1'

response = urllib.request.urlopen(url=ip_url)

content_ip = response.read().decode('utf-8')

url = 'http://www.baidu.com/s?wd=ip'

# 得到ip后，将该IP设置为代理IP
headers = {
    'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'
}

request = urllib.request.Request(url=url, headers=headers)

proxy = {
    'http': content_ip
}

handler = urllib.request.ProxyHandler(proxies=proxy)

opener = urllib.request.build_opener(handler)

response = opener.open(request)

content = response.read().decodeI('utf-8')

with open('baudi1.html', 'w', encoding='utf-8') as fp:
    fp.write(content)

```



代理池

```python
import urllib.request
import random

url = 'http://www.baidu.com/s?wd=ip'

headers = {
    'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'
}

request = urllib.request.Request(url=url, headers=headers)

proxy = [
    {'http': '140.143.142.218:1080'},
    {'http': '125.110.102.230:9000'},
    {'http': '183.129.207.81:12655'},
    {'http': '118.187.58.35:53281'},
    {'http': '117.30.113.217:9999'},
]

proxy = random.choice(proxy)

handler = urllib.request.ProxyHandler(proxies=proxy)

opener = urllib.request.build_opener(handler)

response = opener.open(request)

content = response.read().decode('utf-8')

print(content)

```

## cookie库

动态获取cooike

cookie库通过handler登录会自动保存登陆之后的cookie

cookie库配置

创建一个CookieJar对象

使用cookiejar对象创建一个handler对象

使用handler对象创键一个opener

5通过cookie登录

handler会自动保存登录之后的cookie

```python
import urllib.request
import urllib.parse
import http.cookiejar


# 动态cookie需要先登录
url_post = 'http://www.quanshuwang.com/login.php?do=submit'
data = {
    'username': 'action',
    'password': 'action',
    'action': 'login',
}
data = urllib.parse.urlencode(data).encode('utf-8')

headers = {
    'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'
}

request_post = urllib.request.Request(url=url_post, data=data, headers=headers)

cookiejar = http.cookiejar.CookieJar()
handle = urllib.request.HTTPCookieProcessor(cookiejar=cookiejar)
opener = urllib.request.build_opener(handle)
response = opener.open(request_post)

url_get = 'http://www.quanshuwang.com/modules/article/bookcase.php'
request_get = urllib.request.Request(url=url_get, headers=headers)
response = urllib.request.urlopen(request_get)

content = response.read().decode('utf-8')
print(content)
```

