[TOC]
# Python 爬虫

## 基础

    1、http协议：
        概念：服务器与客户端进行交互的一种形式
        
    2、常用的请求头协议
        - User-Agent：请求载体的身份标识
        - Connection：请求完毕后，是断后连接还是保持连接
    
    3、常用响应头信息：
        - Content-Type：服务器响应回客户端的数据类型
    
    4、https协议：
        - 安全的超文本传输协议
    
    5、加密方式：
        - 对称密钥加密
        - 非对称密钥加密
        - 证书密钥加密


## requests 模块

1、requests 模块：python中原生的一款基于网络请求的模块，功能非常强大，简单便捷，效率极高。

2、作用？：模拟浏览器发送请求。

3、如何使用：
  - 指定url
  - 发起请求
  - 获取相应数据
  - 持久化存储


**4、post传参数爬取** 
```python
# Author: QuanYang Leng
# ProjectName: 爬虫
# FileName: requests爬取肯德基餐厅查询的数据
# CreateTime: 2020/11/10
"""
描述：
"""
import requests
import json

if __name__ == '__main__':
    url = 'http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=keyword'
    kw = input('enter a place:')
    params = {
        'cname':'',
        'pid':'',
        'keyword': kw,
        'pageIndex': '1',
        'pageSize': '10'
    }
    # UA伪装
    headers = {
        'User-Agent': 'Mozilla/5.0 (iPhone; CPU iPhone OS 13_2_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.3 Mobile/15E148 Safari/604.1'
    }

    response = requests.post(url=url,data=params,headers=headers)
    text = response.text
    with open('./网页/'+kw+'.txt','w',encoding='utf-8') as fp:
        fp.write(text)

    print('over!!!')

```

**5、get传参数爬取**
```python
# Author: QuanYang Leng
# ProjectName: 爬虫
# FileName: requests爬取豆瓣电影分类排行数据
# CreateTime: 2020/11/10
"""
描述：
"""
import requests
import json
if __name__ == '__main__':
    url = 'https://movie.douban.com/j/chart/top_list'
    params = {
        'type': '24',
        'interval_id': '100:90',
        'action':'',
        'start': '0',
        'limit': '20'
    }
    # UA伪装
    headers = {
        'User-Agent': 'Mozilla/5.0 (iPhone; CPU iPhone OS 13_2_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.3 Mobile/15E148 Safari/604.1'
    }

    response = requests.get(url=url,params=params,headers=headers)
    # 获取json数据
    list_data = response.json()
    fp = open('./网页/douban.json','w',encoding='utf-8')
    json.dump(list_data,fp=fp,ensure_ascii=False)
    print('over!!!')

```


## 数据解析
  - 聚焦爬虫
    - 正则解析
    - bs4解析
    - xpath解析

> 数据解析的原理概述
> - 解析的局部的文本内容都会在标签之间或者标签对应的属性中进行存储
> - 进行指定标签的定位
> - 标签或者标签对应的属性中存储的数据值进行提取（解析）

> 聚焦爬虫的编码流程：
> - 指定url
> - 发起请求
> - 获取响应数据
> - 数据解析
> - 持久化存储

```python
if __name__ == '__main__':
    url = 'https://pic.qiushibaike.com/system/pictures/12377/123776168/medium/C146H9F3O944GXDB.jpg'
    # text 获取字符串的数据
    # content 获取二进制数据
    # json() 获取对象数据
    img_data = requests.get(url=url).content

    with open('./图片/qiutu.jpg','wb') as fp:
        fp.write(img_data)

```

> 正则表达式的数据解析

```python
import requests
import re
import os

if __name__ == '__main__':
    url = 'https://qiushibaike.com/imgrank/'
    # UA伪装
    header = {
        'User-Agent': 'Mozilla/5.0 (iPhone; CPU iPhone OS 13_2_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.3 Mobile/15E148 Safari/604.1'
    }
    # 使用通用爬虫对url对应的页面进行爬取
    response = requests.get(url=url,headers=header)
    # 手动设定相应数据的编码格式
    response.encoding = 'utf-8'
    page_text = response.text

    # 使用聚焦爬虫将页面中所有的糗图进行解析/提取
    ex = '<a .*?<img src="(.*?)" alt.*?</a>'
    img_list  = re.findall(ex,page_text,re.S)
    # print(page_text)
    # print(img_list)

    # 创建一个文件夹
    if not os.path.exists('./糗事百科'):
        os.mkdir('./糗事百科')
    img_list.pop(0)
    for src in img_list:
        src = 'https:'+src
        img_data = requests.get(url=src,headers=header).content
        img_name = src.split('/')[-1]
        img_path = './糗事百科/'+img_name
        with open(img_path,'wb') as fp:
            fp.write(img_data)
            print(img_name,'下载成功！！！')

```

> bs4数据解析

   - 数据解析的原理：
     - 1、标签定位
     - 2、提取标签，标签属性中存储的数据值
   - bs4数据解析的原理：
     - 1、实例化一个BeautifulSoup对象，并且将页面源码数据加载到该对象中
     - 2、通过调用BeautifulSoup对象中相关的属性或者方法进行标签定位和数据提取
   - 环境安装：
   ```python
   pip install bs4
   pip install lxml
   ```

   - 如何实例化BeautifulSoup对象：
  ```python
  from bs4 import BeautifulSoup
  ```

 - 对象的实例化：
   - 1、将本地的html文档中的数据加载到该对象中
     - fp = open('./test.html','r',encoding='utf-8')
     - soup = BeautifulSoup(fp , 'lxml')
   - 2、将互联网上获取的页面源码加载到该对象中
     - page_text = response.text
     - soup = BeautifulSoup(page_text , 'lxml')

- 提供的用于数据解析的方法和属性：
  - soup.tagName:返回的是文档中第一次出现的tagName对应的标签
  - soup.find():返回文档中第一次出现的tagName对应的标签
    - soup.find('tagName') 等同于 soup.tagName
    - 属性定位：
      - soup.find('div',class_/id/attr='属性值')
  - soup.find_all('tagName'):返回符合要求的所有标签


- select：
  - select('某种选择器（id，class，标签。。。选择器）')，返回的是一个列表。
    - soup.select('.cur')
    - soup.select('#cur')
  - 层级选择器：
    - soup.select('.cur > ul > li > a')[0] ：获取取第一个a标签，> 表示一个层级
    - soup.select('.top-nav > ul span')[0]：空格表示多个层级

- 获取标签之间的文本数据:下面的方法适用于上面所有的方法（soup.tagName,find,find_all(),select())
  - soup.tagName.text/string/get_text()
    - 如：soup.select('.cur > ul > li > a')[0].text
    - 如：soup.select('.cur > ul > li > a')[0].string
    - 如：soup.select('.cur > ul > li > a')[0].get_text()
  - text/get_text():可以获取一个标签中所有的 **文本内容**
  - string：只可以获取该标签下面 **直系的文本内容**
- 获取标签中的属性值
  - soup.a['href']

```python
from bs4 import BeautifulSoup

if __name__ == "__main__":
    # 将本地的html文档中的数据加载到该对象中
    fp = open('./网页/sogou.html','r',encoding='utf-8')
    soup = BeautifulSoup(fp,'lxml')
    # print(soup)
    # print(soup.div) # soup.tagName 返回的是第一次出现的标签
    # print(soup.find('div',class_='user-box').string)
    # print(soup.find('div',id='products-box'))
    # print(soup.find_all('a'))
    # print(soup.select('#products-box'))
    # print(soup.select('.top-nav > ul span')[0].string)
    # li_list = soup.select('.book-mulu > ul > li')
    # title = li.a.string
    print(soup.find('a')['href'])

```

> xpath 解析：最常用且最便捷高效的一种解析方式。通用性。

  - xpath 解析原理：
    - 1、实例化一个etree的对象，且需要将被解析的页面源码数据加载到该对象中
    - 2、调用etree对象中xpath方法结合着xpath表达式实现标签的定位和内容的捕获
  - 环境的安装
```python
pip install lxml
```
  - 如何实例化一个etree对象
    - 1、将本地的html文档中的源码数据加载到etree对象中：
      - etree.parse(filePath)
    - 2、可以将从互联网上获取的源码数据加载到该对象中
      - etree.HTML('page_text')
```python
from lxml import etree
```

- xpath('xpath表达式')
  - / : 表示的是从根节点开始定位。表示的是一个层级。
  - // : 表示的是多个层级。可以表示从任意位置开始定位
  - 定位到某一个class属性的div：
    - xpath('//div[@class="song"]')
    - 通用写法：tag[@attrName="attrValue"]
  - 索引定位：
    - xpath('//div[@class="song"]/p[3]') 定位到class为song的div的子标签p的第三个标签，下标不是从0开始的，从1开始
  - 取文本
    - /text() : 获取的是标签下的直系文本内容
    - //text() : 获取的是标签中非直系的文本内容（即所有的文本内容）
  - 取属性：
    - /@attrName     ==>  img/@src

```python
# 以下三者表示的是同一种结果
tree = etree.parse('test.html')
r = tree.xpath('/html/body/div') # html前面的 / 相当于是 从根部开始
r = tree.xpath('/html//div')
r = tree.xpath('//div')

# 属性定位
tree.xpath('//div[@class="song"]')
# 索引定位
tree.xpath('//div[@class="song"]/p[3]')

# 获取class属性为tang的div下的第五个li标签下的a标签的值
tree.xpath('//div[@class="tang"]//li[5]/a/text()')

# 获取第7个li标签的内容
tree.xpath('//li[7]//text()')

# 获取class属性为tang的div下的所有文本内容
tree.xpath('//div[@class="tang"]//text()')

# 获取img的src属性值
tree.xpath('//div[@class="song"]/img/@src')
```

**实例**
```python
import requests
from lxml import etree

if __name__ == '__main__':
    # 发送请求
    url = 'https://cc.58.com/ershoufang/'
    # UA伪装
    header = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.198 Safari/537.36'
    }
    page_text = requests.get(url=url,headers=header).text
    # 实例化etree对象
    parser = etree.HTMLParser(encoding='utf-8')
    tree = etree.HTML(page_text,parser=parser)
    li_list = tree.xpath('//ul[@class="house-list-wrap"]/li')
    # print(r)
    for li in li_list:
        # ./ 表示的是从当前标签开始，当前的标签是 li
        title = li.xpath('./div[2]/h2/a/text()')[0]
        href = li.xpath('./div[2]/h2/a/@href')[0]
        print(title,'  ',href)
```

**实例2：延时，异常，多层，爬取**
```python
# Author: QuanYang Leng
# ProjectName: 爬虫
# FileName: requests爬取运用xpath之二
# CreateTime: 2020/11/13
"""
描述：解析下载图片数据 http://pic.netbian.com/4kmeinv/
"""
import requests
from lxml import etree
import os
import time

if __name__ == '__main__':
    headers = {
        "User-Agent":'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.198 Safari/537.36'
    }
    url = 'http://pic.netbian.com/4kfengjing/'
    try:
        # 发起请求
        response = requests.get(url=url,headers=headers,timeout=15)
    except requests.exceptions.RequestException as e:
        print(e)
        exit()
    # 手动设定响应数据的编码格式
    # response.encoding = 'utf-8'
    page_text = response.text
    parser = etree.HTMLParser(encoding='utf-8')
    tree = etree.HTML(page_text,parser=parser)
    img_list = tree.xpath('//ul[@class="clearfix"]/li')
    # print(img_list)

    time.sleep(2)

    # 创建一个文件夹
    if not os.path.exists('./picLibs'):
        os.mkdir('./picLibs')
    for img in img_list:
        img_title = img.xpath('./a/img/@alt')[0]+".jpg"
        # 通用解决中文乱码问题
        img_title = img_title.encode('iso-8859-1').decode('gbk')
        img_src = 'http://pic.netbian.com'+img.xpath('./a/img/@src')[0]

        time.sleep(2)
        a_href = 'http://pic.netbian.com/'+img.xpath('./a/@href')[0]

        try:
            img_response = requests.get(url=a_href,headers=headers,timeout=15)
        except requests.exceptions.RequestException as e:
            print('失败')
            print(e)
            continue

        img_page_text = img_response.text
        img_tree = etree.HTML(img_page_text,parser=parser)
        big_img_src = 'http://pic.netbian.com/'+img_tree.xpath('//div[@class="photo-pic"]//img/@src')[0]

        time.sleep(3)

        try:
        # 保存图片
            img_data = requests.get(url=big_img_src,headers=headers,timeout=15).content
            imgName = img_title
            with open('./picLibs/'+imgName,'wb') as fp:
                fp.write(img_data)

            print(img_title,'   保存成功!')
        except requests.exceptions.RequestException as e:
            print('保存失败！')
            print(e)
            continue

        time.sleep(1)


```

**实例3：xpath表达式中可以用运算符**
```python
# Author: QuanYang Leng
# ProjectName: 爬虫
# FileName: request爬取运用xpath之全国城市名称爬取
# CreateTime: 2020/11/13
"""
描述：全国城市名称爬取:https://www.aqistudy.cn/historydata/
"""
import requests
import os
import time
from lxml import etree

if __name__ == "__main__":
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.198 Safari/537.36'
    }
    url = 'https://www.aqistudy.cn/historydata/'
    page_text = requests.get(url=url,headers=headers).text
    parser = etree.HTMLParser(encoding='utf-8')
    tree = etree.HTML(page_text,parser=parser)

    # 解析到热门城市和所有城市对应的a标签
    # //div[@class="bottom"]/ul/li/a
    # //div[@class="bottom"]/ul/div[2]/li/a
    
    # xpath表达式中使用运算符
    all_city = tree.xpath('//div[@class="bottom"]/ul/li/a | //div[@class="bottom"]/ul/div[2]/li/a')
    for c in all_city :
        city_name = c.xpath('./text()')[0]
        print(city_name)


```

**5、爬取简历模板**
```python
# Author: QuanYang Leng
# ProjectName: 爬虫
# FileName: requests爬取xpath之站长素材
# CreateTime: 2020/11/13
"""
描述：下载简历模板：https://sc.chinaz.com/jianli/free.html
"""
import requests
import time
from lxml import etree
import os

# 下载每页简历模板
def downJianli(maxPage,headers):
    global page_text
    if maxPage==1:
        url = 'https://sc.chinaz.com/jianli/free.html'
    else:
        url = 'https://sc.chinaz.com/jianli/free_'+str(maxPage)+'.html'
    try:
        page_text = requests.get(url=url,headers=headers).text
    except:
        print('初始请求失败！')
    # 实例化etree对象
    parser = etree.HTMLParser(encoding='utf-8')
    tree = etree.HTML(page_text,parser=parser)

    # 获取简历模板
    jianli_list = tree.xpath('//div[@id="main"]/div[@class="main_list jl_main"]/div/a')

    # 创建文件夹
    if not os.path.exists('./jianLi'):
        os.mkdir('./jianLi')

    print('这是第',maxPage,'页的模板')
    ind = 1
    for jl in jianli_list:
        # 页面链接
        jianli_href = 'https:'+jl.xpath('./@href')[0]
        # 模板名称
        jianli_name = jl.xpath('./img/@alt')[0].encode('iso-8859-1').decode('utf-8')+'.rar'

        # print(jianli_href,jianli_name)

        try:
            # 请求每一个链接中的页面
            page_response = requests.get(url=jianli_href,headers=headers).text
        except:
            print('第',maxPage,'页中','第',ind,'个模板的详情页请求失败！')
            continue
        tree = etree.HTML(page_response,parser=parser)
        # 获取下载地址
        down_url = tree.xpath('//div[@class="clearfix mt20 downlist"]/ul/li[1]/a[1]/@href')[0]

        ind +=1

        # 创建每一页的文件夹
        if not os.path.exists('./jianLi/'+str(maxPage)):
            os.mkdir('./jianLi/'+str(maxPage))

        try:
            # 下载模板
            muban_data = requests.get(url=down_url,headers=headers).content
            with open('./jianLi/'+str(maxPage)+'/'+jianli_name,'wb') as fp:
                fp.write(muban_data)

            print(jianli_name,'   下载成功')
        except Exception as e:
            print('第',maxPage,'页中','第',ind-1,'个模板的下载失败！')
            print(e)
            continue

# 下载从第几页到第几页的模板
def downPageByPage(start,end,headers):
    for i in range(start,end+1):
        downJianli(i, headers)


if __name__ == "__main__":
    headers = {
        "User-Agent":'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.198 Safari/537.36'
    }
    url = 'https://sc.chinaz.com/jianli/free.html'
    page_text = requests.get(url=url,headers=headers).text
    parser = etree.HTMLParser(encoding='utf-8')
    tree = etree.HTML(page_text,parser=parser)

    # 获取页面数
    max_page = tree.xpath('//div[@class="pagination fr clearfix clear"]/a/b/text()')[-1]
    # print(max_page)

    # 下载从第几页到第几页的模板
    downPageByPage(5,6,headers)



```

> 解决爬虫中lxml.etree.XMLSyntaxError问题

**问题阐述**
在使用lxml中的etree.parse()方法解析HTML页面源码的时候，可能会报错：lxml.etree.XMLSyntaxError。出现这个问题的主要来源是：html代码书写不规范，不符合xml解析器的使用规范。

**解决方案**

1、解析本地的html源码文件，假设其为：XX.html ，根据实际更改：
```python
parser = etree.HTMLParser(encoding="utf-8")
tree = etree.parse('XX.html', parser=parser)
```

2、解析从互联网获取的HTML源码数据
```python
page_text = requests.get(url, headers).text
parser = etree.HTMLParser(encoding="utf-8")
tree = etree.HTML(page_text, parser=parser)
```

## 验证码识别

> 简介

    - 验证码与爬虫之间的关系：反爬机制
    - 识别验证码图片中的数据，用于模拟登录操作。

> 识别验证码的操作

    - 人工肉眼识别（不推荐）
    - 第三方自动识别（推荐）
      - 云打码