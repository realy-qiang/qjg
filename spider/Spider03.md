# Spider03

## 正则表达式

### 单字修饰符

+ . ：匹配除换行符以外的任意字符
+ []：庸才匹配一组字符，可单独列出，也可给出范围，[abc]表示匹配'a'、'b'和'c',[1-9a-zA-Z]表示匹配数字和字母
+ \d：匹配任意数字
+ \D：匹配任意非数字
+ \w：匹配任意数字字母下划线
+ \W：匹配任意非数字字母下划线
+ \s：匹配任意空白字符，等同于[\\r\\t\\n\\f]
+ \S：匹配任意非空白字符

### 数量修饰符

+ *：表示1个或多个
+ +：表示0个和多个
+ ?：表示0个和1个
+ {m}：表示m个
+ {m,}：表示最少m个
+ {m,n}：表示m到n个

### 边界修饰符

+ ^：以什么开头，'^abc'，以abc开头
+ $：以什么结尾，'abc$'，以abc结尾
+ (?<=....)：前向界定
+ (?=....)：后向界定
+ 以上两个一起使用，匹配在什么之间且不包含本身，比如'(?<=//\*).+?(?=/\*/)'，匹配/\*和\*/之间的字符串

### 分组修饰符

+ ()：匹配括号内的表达式，也表示一个组（.*）:(.*)
+ \1 \2匹配法：匹配第1，2个分组内容，\1  \2

### 贪婪与非贪婪匹配

贪婪模式/非贪婪模式

 贪婪模式：在整个表达式匹配成功的前提下，尽可能多的匹配 ( * )；

非贪婪模式：在整个表达式匹配成功的前提下，尽可能少的匹配 ( ? )；

 Python里数量词默认是贪婪的。

示例一 ： 源字符串：abbbc

​				使用贪婪的数量词的正则表达式 ab* ，匹配结果： abbb。 

   * 决定了尽可能多匹配 b，所以a后面所有的 b 都出现了。
   * 数量词的正则表达式ab*?，匹配结果： a。 
   *   即使前面有 *，但是 ? 决定了尽可能少匹配 b，所以没有 b。

示例二 ： 源字符串：aa<div>test1</div>bb<div>test2</div>cc

使用贪婪的数量词的正则表达式：<div>.*</div>

匹配结果：<div>test1</div>bb<div>test2</div>

这里采用的是贪婪模式。在匹配到第一个“</div>”时已经可以使整个表达式匹配成功，但是由于采用的是贪婪模式，所以仍然要向右尝试匹配，查看是否还有更长的可以成功匹配的子串。匹配到第二个“</div>”后，向右再没有可以成功匹配的子串，匹配结束，匹配结果为“<div>test1</div>bb<div>test2</div>”

  使用非贪婪的数量词的正则表达式：<div>.*?</div> 匹配结果：<div>test1</div>

正则表达式二采用的是非贪婪模式，在匹配到第一个“</div>”时使整个表达式匹配成功，由于采用的是非贪婪模式，所以结束匹配，不再向右尝试，匹配结果为“<div>test1</div>”

### 模式修饰符regular expression

+ re.S  单行模式
+ re.M  多行模式
+ re.I 忽略大小写

### 使用步骤：

re.complie()

+ compile 函数用于编译正则表达式，生成一个正则表达式Pattern对象
+ Pattern对象 = re.compile(正则表达式)
+ Pattern对象》find all（html）

findall()：在字符串中找到正则表达式所匹配的所有子串，并返回一个列表，如果没有找到匹配的，则返回空列表。

```python
import re

content = '''
    <div class="thumb">
        <a href="/article/119749308" target="_blank">
        <img src="//pic.qiushibaike.com/system/pictures/11974/119749308/medium/app119749308.jpg" alt="糗事#119749308" class="illustration" width="100%" height="auto">
        </a>
    </div>
    <div class="thumb">
        <a href="/article/119750010" target="_blank">
        <img src="//pic.qiushibaike.com/system/pictures/11975/119750010/medium/app119750010.jpg" alt="糗事#119750010" class="illustration" width="100%" height="auto">
        </a>
    </div>
    <div class="thumb">
        <a href="/article/121859652" target="_blank">
        <img src="//pic.qiushibaike.com/system/pictures/12185/121859652/medium/LZB48T44DNIAY1CV.jpg" alt="糗事#121859652" class="illustration" width="100%" height="auto">
        </a>
    </div>
'''

# pattern = re.compile(r'//.*?\.jpg')
pattern = re.compile(r'<div class="thumb">.*?<img src="(.*?)" alt="(.*?)"', re.S)

src_list = pattern.findall(content)

for src in src_list:
    print(src)
```

案例：站长素材图片下载

```python
import os
import re
import urllib.request


def create_request(page):
    if page == 1:
        url = 'http://sc.chinaz.com/tupian/meinvtupian.html'
    else:
        url = 'http://sc.chinaz.com/tupian/meinvtupian_' + str(page) + '.html'

    headers = {
        'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'
    }

    request = urllib.request.Request(url=url, headers=headers)

    return request


def get_content(request):
    response = urllib.request.urlopen(request)

    content = response.read().decode('utf-8')
    return content


def download(content):
    patterns = re.compile('<a target="_blank".*?>.*?<img src2="(.*?)" alt="(.*?)"', re.S)
    src_list = patterns.findall(content)
    for src in src_list:
        urllib.request.urlretrieve(src[0], filename='picture/' + src[1] + '.jpg')


if __name__ == '__main__':
    start_page = int(input('请输入起始页码：'))
    end_page = int(input('请输入结束页码：'))

    for page in range(start_page, end_page):
        request = create_request(page)
        content = get_content(request)
        download(content)
```

## xpath

xpath使用：

1.安装lxml库      pip install lxml -i https://pypi.douban.com/simple

2.导入lxml.etree  from lxml import etree

3.etree.parse()   解析本地文件   html_tree = etree.parse('XX.html')	

4.etree.HTML()    服务器响应文件     html_tree = etree.HTML(response.read().decode('utf-8')	

5.html_tree.xpath(xpath路径)

注意：提前安装xpath插件

### 基本语法

+ 路径查找
  + //：所有子孙节点，不考虑层级关系
  + /：子节点
+ 谓词查询
  + //div[@div]：查找div标签
  + //div[@class="name"]：查找class为name的div
+ 属性查询
  + //@class：得到class的值
+ 模糊查询
  + //div[contains(@id, "he")]   ：包含
  + //div[starts-with(@id, "he")] ：以什么开头
+ 内容查询
  + //div/h1/text()：获取h1的内容
+ 逻辑运算
  + //div[@id="head" and @class="s_down"]
  + //title | //price

应用案例： 1.站长素材图片抓取并且下载

```python
import os
import re
import urllib.request
from lxml import etree


def create_request(page):
    if page == 1:
        url = 'http://www.mmonly.cc/tag/swyh/'
    else:
        url = 'http://www.mmonly.cc/tag/swyh/' + str(page) + '.html'

    headers = {
        'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'
    }

    request = urllib.request.Request(url=url, headers=headers)

    return request


def get_content(request):
    response = urllib.request.urlopen(request)

    content = response.read().decode('gb18030')
    return content


def download(content):
    tree = etree.HTML(content)
    src_list = tree.xpath('//div[@class="item_t"]/div[@class="img"]/div[@class="ABox"]/a[@target="_blank"]/img/@src')
    alt_list = tree.xpath('//div[@class="item_t"]/div[@class="img"]/div[@class="ABox"]/a[@target="_blank"]/img/@alt')

    for src in src_list:
        i = src_list.index(src)
        alt = re.findall('>(.*?)<', alt_list[i])

        if not alt:
            alt = alt_list[i]
        else:
            alt = alt[0]
        name = 'picture/'+alt.replace('/', '_')+"."+src.split('.')[-1]

        urllib.request.urlretrieve(src, filename=name)


if __name__ == '__main__':
    start_page = int(input('请输入起始页码：'))
    end_page = int(input('请输入结束页码：'))

    for page in range(start_page, end_page+1):
        request = create_request(page)
        content = get_content(request)
        download(content)
```

## JsonPath

jsonpath的安装及使用方式：

pip安装： pip install jsonpath

jsonpath的使用：

+ obj = json.load(open('json文件', 'r', encoding='utf-8'))

+ ret = jsonpath.jsonpath(obj, 'jsonpath语法')

json对象的转换

+ json.loads()：是将字符串转化为python对象
+ json.dumps()：将python对象转化为json格式的字符串
+ json.load()：读取json格式的文本，转化为python对象，json.load(open(a.json))
+ json.dump()：将python对象写入到文本中

应用案例：智联招聘（薪水，公司名称，职位需求）

```python
import os
import re
import urllib.request
import jsonpath
import json


def create_request(page):
    if page == 1:
        url = 'https://fe-api.zhaopin.com/c/i/sou?pageSize=90&cityId=538&workExperience=-1&education=-1&companyType=-1&employmentType=-1&jobWelfareTag=-1&kw=python&kt=3&_v=0.75487501&x-zp-page-request-id=156b66f7cbe3410b8550efa31923059e-1571906793754-47490&x-zp-client-id=894bdba7-27a8-4454-b09e-d83c86b4298c'

    else:
        url = 'https://fe-api.zhaopin.com/c/i/sou?start='+str((page-1)*90)+'&pageSize=90&cityId=538&workExperience=-1&education=-1&companyType=-1&employmentType=-1&jobWelfareTag=-1&kw=python&kt=3&_v=0.75487501&x-zp-page-request-id=156b66f7cbe3410b8550efa31923059e-1571906793754-47490&x-zp-client-id=894bdba7-27a8-4454-b09e-d83c86b4298c'

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
    name = 'zhilian_'+str(page)+'.json'
    with open(name, 'w', encoding='utf-8') as fp:
        fp.write(content)

    obj = json.load(open(name, 'r', encoding='utf-8'))

    jobName_list = jsonpath.jsonpath(obj, '$.data.results[*].jobName')
    company_list = jsonpath.jsonpath(obj, '$.data.results[*].company.name')
    salary_list = jsonpath.jsonpath(obj, '$.data.results[*].salary')

    message_list = []

    for i in range(len(jobName_list)):
        message_dict = {}
        message_dict['jobName'] = jobName_list[i]
        message_dict['company'] = company_list[i]
        message_dict['salary'] = salary_list[i]

        message_list.append(message_dict)

    for message in message_list:

        with open('jobInfo.txt', 'a+', encoding='utf-8') as fp:
            fp.write(str(message)+'\n')


if __name__ == '__main__':
    start_page = int(input('请输入起始页码：'))
    end_page = int(input('请输入结束页码：'))

    for page in range(start_page, end_page+1):
        request = create_request(page)
        content = get_content(request)
        download(page, content)
```

boss直聘

```python
import urllib.request
from lxml import etree


def create_request(page):
    url = 'https://www.zhipin.com/c101020100/?query=python&page=' + str(page)

    headers = {
        'authorit': 'www.zhipin.co',
        'method': 'GET',
        'path': '/c101020100/?query=python&page=' + str(page),
        'scheme': 'https',
        'accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3',
        # 'accept-encoding': 'gzip, deflate, br',
        'accept-language': 'zh-CN,zh;q=0.9',
        'cookie': 'lastCity=101020100; sid=sem_pz_bdpc_dasou_title; __c=1571914614; __g=sem_pz_bdpc_dasou_title; _uab_collina=157191461393105957330304; __l=l=%2Fwww.zhipin.com%2F%3Fsid%3Dsem_pz_bdpc_dasou_title&r=https%3A%2F%2Fsp0.baidu.com%2F9q9JcDHa2gU2pMbgoY3K%2Fadrc.php%3Ft%3D06KL00c00fDIFkY0luu-0KZEgs766e7I0000079yiNC00000rEIcSM.THdBULP1doZA8QMu1x60UWdBmy-bIfK15Hb4Pj7bnjDYnj0snWP9mHc0IHYsnj-KfbDsnRuKrDwDfYDLPHFAPbRdf1Rkf10vwDRznfK95gTqFhdWpyfqn1c4n1DvPWbkriusThqbpyfqnHm0uHdCIZwsT1CEQLILIz4lpA-spy38mvqVQ1q1pyfqTvNVgLKlgvFbTAPxuA71ULNxIA-YUAR0mLFW5HnYrjcY%26tpl%3Dtpl_11534_19968_16032%26l%3D1514755672%26attach%3Dlocation%253D%2526linkName%253D%2525E6%2525A0%252587%2525E5%252587%252586%2525E5%2525A4%2525B4%2525E9%252583%2525A8-%2525E6%2525A0%252587%2525E9%2525A2%252598-%2525E4%2525B8%2525BB%2525E6%2525A0%252587%2525E9%2525A2%252598%2526linkText%253DBOSS%2525E7%25259B%2525B4%2525E8%252581%252598%2525E2%252580%252594%2525E2%252580%252594%2525E6%252589%2525BE%2525E5%2525B7%2525A5%2525E4%2525BD%25259C%2525EF%2525BC%25258C%2525E6%252588%252591%2525E8%2525A6%252581%2525E8%2525B7%25259F%2525E8%252580%252581%2525E6%25259D%2525BF%2525E8%2525B0%252588%2525EF%2525BC%252581%2526xp%253Did(%252522m3293166919_canvas%252522)%25252FDIV%25255B1%25255D%25252FDIV%25255B1%25255D%25252FDIV%25255B1%25255D%25252FDIV%25255B1%25255D%25252FDIV%25255B1%25255D%25252FH2%25255B1%25255D%25252FA%25255B1%25255D%2526linkType%253D%2526checksum%253D136%26ie%3Dutf-8%26f%3D3%26tn%3Dbaidu%26wd%3Dboss%25E7%259B%25B4%25E8%2581%2598%25E5%25AE%2598%25E7%25BD%2591%26oq%3D%2525E9%25259B%2525AA%2525E4%2525B8%2525AD%2525E6%252582%25258D%2525E5%252588%252580%2525E8%2525A1%25258C%26rqlang%3Dcn%26inputT%3D11865%26prefixsug%3Dboss%26rsp%3D0&g=%2Fwww.zhipin.com%2F%3Fsid%3Dsem_pz_bdpc_dasou_title&friend_source=0&friend_source=0; Hm_lvt_194df3105ad7148dcf2b98a91b5e727a=1571914614,1571917038,1571917193,1571917209; __a=53524998.1571914614..1571914614.28.1.28.28; __zp_stoken__=7fe2MoG9NJtPX8ZTzrIPo9EgATZtslb6ivo7qUd3%2FcQlQNlYOKNfsnMpj1h5KbI2pkO%2Bl85VJoyVOeSZBYUT3mi2pQ%3D%3D; Hm_lpvt_194df3105ad7148dcf2b98a91b5e727a=1571921391',        'upgrade-insecure-requests': 1,
        'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'
    }

    request = urllib.request.Request(url=url, headers=headers)

    return request


def get_content(request):
    response = urllib.request.urlopen(request)

    content = response.read().decode('utf-8')

    return content


def down_load(page, content):
    tree = etree.HTML(content)
    jobName_list = tree.xpath('//div[@class="job-primary"]/div/h3/a/div/text()')
    salary_list = tree.xpath('//div[@class="job-primary"]/div/h3/a/span/text()')
    company_list = tree.xpath('//div[@class="job-primary"]/div[@class="info-company"]/div/h3/a/text()')
    jobInfo_list = []
    for i in range(len(jobName_list)):
        jobInfo_dict = {}
        jobInfo_dict['jobName'] = jobName_list[i]
        jobInfo_dict['salary'] = salary_list[i]
        jobInfo_dict['company'] = company_list[i]
        jobInfo_list.append(jobInfo_dict)

    name = 'boss_' + str(page) + '.html'
    name1 = 'boss_' + str(page) + '.json'

    with open(name, 'w', encoding='utf-8') as fp:
        fp.write(content)

    with open(name1, 'w', encoding='utf-8') as fp:
        fp.write(str(jobInfo_list))


if __name__ == '__main__':
    start_page = int(input('请输入起始页码：'))
    end_page = int(input('请输入结束页码：'))

    for page in range(start_page, end_page + 1):
        request = create_request(page)
        content = get_content(request)
        download = down_load(page, content)
```

股票信息提取

```python
import os
import re
import urllib.request
import jsonpath
import json


def create_request(page):
    # if page == 1:
    url = 'http://74.push2.eastmoney.com/api/qt/clist/get?cb=jQuery1124016683962969814892_1571928325279&pn=1&pz=20&po=1&np=' + str(
        page) + '&ut=bd1d9ddb04089700cf9c27f6f7426281&fltt=2&invt=2&fid=f26&fs=b:BK0707&fields=f1,f2,f3,f4,f5,f6,f7,f8,f9,f10,f12,f13,f14,f15,f16,f17,f18,f20,f21,f23,f24,f25,f26,f22,f11,f62,f128,f136,f115,f152&_=1571928325280'

    # else:
    #     url = 'https://fe-api.zhaopin.com/c/i/sou?start='+str((page-1)*90)+'&pageSize=90&cityId=538&workExperience=-1&education=-1&companyType=-1&employmentType=-1&jobWelfareTag=-1&kw=python&kt=3&_v=0.75487501&x-zp-page-request-id=156b66f7cbe3410b8550efa31923059e-1571906793754-47490&x-zp-client-id=894bdba7-27a8-4454-b09e-d83c86b4298c'

    headers = {
        'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'
    }

    request = urllib.request.Request(url=url, headers=headers)

    return request


def get_content(request):
    response = urllib.request.urlopen(request)

    content = response.read().decode('utf-8')
    content = content.split('(')[1].split(')')[0]


    return content


def download(page, content):
    name = 'gupiao_' + str(page) + '.json'
    # with open(name, 'w', encoding='utf-8') as fp:
    #     fp.write(content)
    #
    # obj = json.load(open(name, 'r', encoding='utf-8'))
    #
    # name_list = jsonpath.jsonpath(obj, '$.data.diff[*].f14')
    # latest_price_list = jsonpath.jsonpath(obj, '$.data.diff[*].f2')
    # quote_change_list = jsonpath.jsonpath(obj, '$.data.diff[*].f3')
    # rise_and_fall_list = jsonpath.jsonpath(obj, '$.data.diff[*].f4')
    # volume_list = jsonpath.jsonpath(obj, '$.data.diff[*].f5')
    # turnover_list = jsonpath.jsonpath(obj, '$.data.diff[*].f6')
    # amplitude_list = jsonpath.jsonpath(obj, '$.data.diff[*].f7')
    # highest_list = jsonpath.jsonpath(obj, '$.data.diff[*].f15')
    # lowest_list = jsonpath.jsonpath(obj, '$.data.diff[*].f16')
    # open_today_list = jsonpath.jsonpath(obj, '$.data.diff[*].f17')
    # receive_yesterday_list = jsonpath.jsonpath(obj, '$.data.diff[*].f18')
    # turnover_rate_list = jsonpath.jsonpath(obj, '$.data.diff[*].f8')
    # market_earning_ratio_list = jsonpath.jsonpath(obj, '$.data.diff[*].f9')
    # market_net_ratio_list = jsonpath.jsonpath(obj, '$.data.diff[*].f23')
    #
    # message_list = []
    #
    # for i in range(len(name_list)):
    #     message_dict = {}
    #     message_dict['名称'] = name_list[i]
    #     message_dict['最新价'] = latest_price_list[i]
    #     message_dict['涨跌幅'] = str(quote_change_list[i]) + '%'
    #     message_dict['涨跌额'] = rise_and_fall_list[i]
    #     message_dict['成交量'] = volume_list[i]
    #     message_dict['成交额'] = turnover_list[i]
    #     message_dict['振幅'] = str(amplitude_list[i]) + '%'
    #     message_dict['最高'] = highest_list[i]
    #     message_dict['最低'] = lowest_list[i]
    #     message_dict['今开'] = open_today_list[i]
    #     message_dict['昨收'] = receive_yesterday_list[i]
    #     message_dict['换手率'] = str(turnover_rate_list[i]) + '%'
    #     message_dict['市盈率'] = market_earning_ratio_list[i]
    #     message_dict['市净率'] = market_net_ratio_list[i]
    #
    #     message_list.append(message_dict)
    #
    # # print(message_list)
    #
    # for message in message_list:
    #     with open('gupiao.txt', 'a+', encoding='utf-8') as fp:
    #         fp.write(str(message) + '\n')


if __name__ == '__main__':
    start_page = int(input('请输入起始页码：'))
    end_page = int(input('请输入结束页码：'))

    for page in range(start_page, end_page + 1):
        request = create_request(page)
        content = get_content(request)
        download(page, content)
```

## BeautifulSoup

### 简介

1.BeautifulSoup简称：bs4

2.什么是BeatifulSoup？

BeautifulSoup，和lxml一样，是一个html的解析器，主要功能也是解析和提取数据

3.优缺点？

​		缺点：效率没有lxml的效率高	

​		优点：接口设计人性化，使用方便

### 安装和创建

+ 安装：pip install bs4
+ 导入：from bs4 import BeautifulSoup
+ 创建对象
  + 服务器响应的文件生成对象
    + soup = BeautifulSoup(response.read().decode(), 'lxml')
  + 本地文件生成对象
    + soup = BeautifulSoup(open('1.html'), 'lxml')
+ 注意：默认打开文件的编码格式gbk所以需要指定打开编码格式

### 节点定位

+ 根据标签名查找节点

  + soup.a 【注】只能找到第一个a
  + soup.a.name
  + soup.a.attrs

+ 函数

  + find(返回一个对象)

    + find('a')：只找到第一个a标签
    + find('a', title='名字')
    + find('a', class_='名字')

  + find_all(返回一个列表)

    + find_all('a')  查找到所有的a
    + find_all(['a', 'span'])  返回所有的a和span
    + find_all('a', limit=2)  只找前两个a

  + select(根据选择器得到节点对象)【推荐】

    + .element：eg:p
    + .class：eg:.firstname
    + #id： eg:#firstname
    + 属性选择器
      +  [attribute]：li = soup.select('li[class]')
      +  [attribute=value]：li = soup.select('li[class="hengheng1"]')
    + 层级选择器
      +  element element：div p
                              element>element
                                  div>p
                              element,element
                                  div,p 
                                  		eg:soup = soup.select('a,span')

  + 获取子孙节点：

    + contents：返回的是一个列表，print(soup.body.contents)

    + descendants：返回的是一个生成器

      for a in soup.body.descendants:
      					print(a)

  ### 节点信息

  + 获取节点内容：适用于标签中嵌套标签的结构
            obj.string
            obj.get_text()【推荐】

  + 节点的属性，tag.name 获取标签名

    ​          ag = find('li)
    ​          print(tag.name)

    ​    tag.attrs将属性值作为一个字典返回

  + 获取节点属性
            obj.attrs.get('title')【常用】
            obj.get('title')
            obj['title']

### 节点类型

bs4.BeautifulSoup 根节点类型
	bs4.element.NavigableString 连接类型  可执行的字符串
	bs4.element.Tag 节点类型  
	bs4.element.Comment 注释类型
		eg:
			if type(aobj.string) == bs4.element.Comment:
                print('这个是注释内容')
             else:
                print('这不是注释')

中华英才网-旧版

```python
import urllib.request
from bs4 import BeautifulSoup


def create_request(page):
    url = 'http://www.chinahr.com/sou/?orderField=relate&keyword=python&city=34,398;36,400;25,292;27,312;25,291&page=' + str(
        page)
    headers = {
        'user-agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'
    }

    request = urllib.request.Request(url=url, headers=headers)

    return request


def get_content(request):
    response = urllib.request.urlopen(request)

    content = response.read().decode('utf-8')

    return content


def down_load(page, content):
    name = 'yingcai/yingcai_' + str(page) + '.html'
    with open(name, 'w', encoding='utf-8') as fp:
        fp.write(content)

    soup = BeautifulSoup(content, 'lxml')
    name_list = soup.select('div[class="jobList"] li[class="l1"] span[class="e1"]')
    company_list = soup.select('div[class="jobList"] li[class="l1"] span[class="e3 cutWord"]')
    salary_list = soup.select('div[class="jobList"] li[class="l2"] span[class="e2"]')

    jobInfo_list = []

    for i in range(len(name_list)):
        jobInfo_dict = {}
        jobInfo_dict['name'] = name_list[i].get_text()
        jobInfo_dict['company'] = company_list[i].get_text()
        jobInfo_dict['salary'] = salary_list[i].get_text()

        jobInfo_list.append(jobInfo_dict)

    with open('yingcai/yingcai.txt', 'a+', encoding='utf-8') as fp:
        fp.write(str(jobInfo_list).replace('\\n', '') + '\n')

    # name = 'tenxun_' + str(page) + '.html'

    # with open(name, 'w', encoding='utf-8') as fp:
    #     fp.write(content)


if __name__ == '__main__':
    start_page = int(input('请输入起始页码：'))
    end_page = int(input('请输入结束页码：'))

    for page in range(start_page, end_page + 1):
        request = create_request(page)
        content = get_content(request)
        download = down_load(page, content)
```

腾讯公司招聘需求抓取

```python
import json
import urllib.request

import jsonpath


def create_request(page):
    url = 'https://careers.tencent.com/tencentcareer/api/post/Query?timestamp=1571990645574&countryId=&cityId=&bgIds=&productId=&categoryId=&parentCategoryId=&attrId=&keyword=python&pageIndex=' + str(page) + '&pageSize=10&language=zh-cn&area=cn'

    headers = {
        'user-agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'
    }

    request = urllib.request.Request(url=url, headers=headers)

    return request


def get_content(request):
    response = urllib.request.urlopen(request)

    content = response.read().decode('utf-8')

    return content


def down_load(page, content):
    name = 'tenxun_' + str(page) + '.json'

    with open(name, 'w', encoding='utf-8') as fp:
        fp.write(content)

    obj = json.load(open(name, 'r', encoding='utf-8'))
    RecruitPostName_list = jsonpath.jsonpath(obj, '$.Data.Posts[*].RecruitPostName')
    LocationName_list = jsonpath.jsonpath(obj, '$.Data.Posts[*].LocationName')
    BGName_list = jsonpath.jsonpath(obj, '$.Data.Posts[*].BGName')
    Responsibility_list = jsonpath.jsonpath(obj, '$.Data.Posts[*].Responsibility')

    jobsInfo_list = []

    for i in range(len(RecruitPostName_list)):
        jobInfo_dict = {}
        jobInfo_dict['RecruitPostName'] = RecruitPostName_list[i]
        jobInfo_dict['LocationName'] = LocationName_list[i]
        jobInfo_dict['BGName'] = BGName_list[i]
        jobInfo_dict['Responsibility'] = Responsibility_list[i]

        jobsInfo_list.append(jobInfo_dict)

    with open('tenxun.txt', 'a+', encoding='utf-8') as fp:
        fp.write(str(jobsInfo_list)+'\n')



if __name__ == '__main__':
    start_page = int(input('请输入起始页码：'))
    end_page = int(input('请输入结束页码：'))

    for page in range(start_page, end_page+1):
        request = create_request(page)
        content = get_content(request)
        download = down_load(page, content)

```

