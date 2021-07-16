---
title: python爬虫（三)
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_2.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: python
tags: [python, 爬虫]   # 可以是数组
top: false
---



# 爬虫学习第三天

### 爬取国家药监总局部分信息

```python
import requests
import json

if __name__ == '__main__':
    url = 'http://125.35.6.84:81/xk/itownet/portalAction.do?method=getXkzsList'
    url_2 = 'http://125.35.6.84:81/xk/itownet/portalAction.do?method=getXkzsById'
    headers = {
        'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.149 Safari/537.36'
    }
    #爬取的页数
    page_ = input("请输入您想爬取的信息页数：")
    for page in range(1, int(page_)+1):
        data = {
            'on': 'true',
            'page': page,
            'pageSize': '15',
            'productName': '',
            'conditionType': '1',
            'applyname': '',
            'applysn': '',
        }
        #id_list have many ids
        id_list = []
        message = []

        json_list = requests.post(url=url, data=data, headers=headers).json()
        #id_a is a dict
        for id_a in json_list["list"]:
            id_list.append(id_a["ID"])
            #id_b是id号
        for id_b in id_list:
                data_2 = {
                    "id": str(id_b)
                }
                detail_message = requests.post(url=url_2, data=data_2, headers=headers).json()
                print(detail_message)
                message.append(detail_message)
    with open('./药物信息.json', 'w', encoding='utf-8') as fp:
        json.dump(message, fp, ensure_ascii=False)
    print('end...')

'''此代码爬取时间可能有些长，也有可能会出现Timeouterror错误，多试几次可能就可以了'''
'''出现这种情况的原因是对方服务器拒绝请求（反爬虫），，，后续继续学习反反爬虫技术'''
```

### 数据解析概述

- 聚焦爬虫：爬取页面中指定的页面内容
      -编码流程
          1、指定url
          2、发起请求
          3、获取相应数据
          4、持久化存储
          
  -数据解析分类：
          -正则
          -bs4
          -xpath
          
  -数据解析原理概述
          -解析的局部的文本内容都会在标签之间或者标签对应的属性中进行存储
          -1、进行指定标签的定位
          -2、标签或者标签对应属性中存储的数据值进行提取（解析）

### 图片的爬取代码

```python
import requests
#此处url为图片地址，而非链接【地址比链接短】
url = 'https://tse4-mm.cn.bing.net/th/id/OIP.vLPcBX_5hWArkIbn_PqvYgHaGL?w=233&h=195&c=7&o=5&dpr=1.25&pid=1.7'
headers = {
        'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.149 Safari/537.36'
}
#图片以二进制的形式存储，所以以.content形式接收，以jpg格式存
data_pict = requests.get(url=url, headers=headers).content
with open('./beauty_pict.jpg', 'wb') as fp:
    fp.write(data_pict)
```
