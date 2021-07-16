---
title: python爬虫技术
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_2.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: python
tags: [python, 爬虫]   # 可以是数组
top: false
---

# 爬虫第一天

- urllib太过古老，因此不加以深入学习

### requests
requests模块：python中原生的一款基于网络请求的模块，功能强大，简单便捷，效率高。
作用：模拟浏览器发送请求。

如何使用：（requests模块的编码流程）
    -指定url
    -发起请求（get或post）
    -获取响应数据
    -持久化存储（数据库或本地存储）

环境安装：
    -pip或pycharm安装
    -anaconda自带（方便已装）
    
<!--more-->

### **实战编码：**

    - 需求：爬取拉勾网首页的数据
        示例：

```python
import requests

if __name__ == '__main__':
    #step 1:指定url
    url = "https://www.lagou.com/jobs/6889986.html?show=96e52fa1d2134ad483eac5662bcc9fc6"

    #step_2:发起请求
    #get会返回一个响应对象
    response = requests.get(url=url)

    #step_3:获取相应数据(.text返回的是字符串形式的响应数据)
    page_text = response.text
    print(page_text)

    #step_4:持久化存储
    with open('./lagou.html', 'w', encoding = 'utf-8') as fp:
        fp.write(page_text)
    print('爬取数据结束')
```
- 以上代码极有可能会被网站捕获，被识别出为爬虫，所以需要实施反爬虫策略

- 示例： 
  UA:User-Agent (请求载体的身份标识)
  UA检测：门户网站的服务器会检测对应的载体身份标识，如果检测到请求的载体身份标识为某一款浏览器，
  说明该请求是一个正常的请求。但是，如果监测到请求的载体身份标识不是基于某一款浏览器的，则表示
  该请求为不正常的请求（爬虫），则服务器端就很有可能拒绝这次请求。
  UA伪装:将对应的请求载体身份标识伪装成一款浏览器

```python
import requests

if __name__ == '__main__':
    # UA伪装：将对应的User-Agent封装到一个字典中
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.149 Safari/537.36'
    }

    url = 'https://www.sogou.com/web?'

    #处理url携带的参数：封装到字典中（参数为问号后面的）
    kw = input('enter a word:')
    param = {
        'query': kw
    }

    # 需求的响应返回的是一个对象，对象应实例化
    response = requests.get(url=url, params=param, headers=headers)

    # 确认相应数据是txt类型的
    page_text = response.text
    fileName = kw+'.html'
    with open(fileName, 'w', encoding='utf-8') as fp:
        fp.write(page_text)
    print('保存成功。。。')

```





