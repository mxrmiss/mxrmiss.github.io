---
title: python爬虫（二）
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_2.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: python
tags: [python, 爬虫]   # 可以是数组
top: false
---



# 爬虫第二天

### 有关于ajax
#### 爬取百度翻译的内容

```python
import requests
import json

if __name__ == '__main__':
    #获取url
    post_url = 'https://fanyi.baidu.com/sug'
    #进行UA伪装
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.149 Safari/537.36'
    }
    #post请求的参数处理（同get请求一致）
    word = input('输入你想翻译的东西:')
    data = {
        'kw': word
    }
    #发送请求并生成响应
    response = requests.post(url=post_url, data=data, headers=headers)
    #获取响应数据:
    #json()方法返回的是obj （如果确认响应数据是json类型的）
    dic_obj = response.json()

    #持久化存储
    filename = word+'.json'
    with open(filename, 'w', encoding='utf-8') as fp:
        json.dump(dic_obj, fp=fp, ensure_ascii=False)

    #将jaon格式转化成dict格式
    with open(filename, 'r', encoding='utf-8') as fp:
        great = json.load(fp)

    #将字典格式化输出(此字典中存在字典与列表的嵌套现象)
    for k, v in great.items():
        if type(v) == list:
            for i in v:
                print(i)
        else:
            print(k)
            print(v)

    print('end...')

```

### 爬取豆瓣电影信息

```python
##此处代码示例为失败的例子，未考虑到由ajax得到的页面依旧采用了ajax

import requests
import json

if __name__ == '__main__':
    url = 'https://movie.douban.com/j/chart/top_list'
    param = {
        'type': ' 24',
        'interval_id': ' 100:90',
        'action': '',
        'start': ' 1',
        'limit': ' 20',
    }
    headers = {
        'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.149 Safari/537.36'
    }

    response = requests.get(url=url, params=param, headers=headers)

    list_data = response.json()

    with open('./douban.json', 'w', encoding='utf-8') as fp:
        json.dump(list_data, fp, ensure_ascii=False)

    with open('./douban.json', 'r', encoding='utf-8') as fp:
        great = json.load(fp)

    print(great)
```