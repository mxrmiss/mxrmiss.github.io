---
title: python爬虫实战
date: 2020/5/3 9:08:28
comments: ture
categories:
- 爬虫
- python
tags:
- 爬虫
- python
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

# 爬虫第四天
### 爬取百度图片（星空图片）

```python
import requests
import re
import os
import time

if __name__ == '__main__':
    #创建一个文件夹，存放图片
    if not os.path.exists('./star_sky'):
        os.mkdir('./star_sky')
        
    url = 'https://cn.bing.com/images/search?q=%E6%98%9F%E7%A9%BA%E5%9B%BE%E7%89%87&qpvt=%e6%98%9f%e7%a9%ba%e5%9b%be%e7%89%87&form=IGRE&first=1&cw=1117&ch=714'
    headers = {
            'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.149 Safari/537.36'
    }
    data_pict = requests.get(url=url, headers=headers).text

    #正则匹配
    ex = '<a class=.*?murl&quot;:&quot;(.*?)&quot;.*?>'
    star_list = re.findall(ex, data_pict, re.S)
    for i in star_list:
        url_2 = i
        #请求到了图片的二进制数据
        data_2_pict = requests.get(url=url_2, headers=headers).content
        #为图片设置名称
        img_name = url_2.split("/")[-1]
        img_path = './star_sky/' + img_name
        with open(img_path, 'wb') as fp:
            fp.write(data_2_pict)
        print(img_name + '...successful')
        time.sleep(0.1)

****************************************未完，，待更新********************************************
```
### xpath

- xpath解析：最常用且最便捷高效的一种解析方式，具有通用性
      -xpath解析原理：
          -1、实例化一个etree对象，且需要将被解析的页面源码数据加载到该对象中
          -2、调用etree对象中的xpath方法结合着xpath表达式实现标签的定位和内容的捕获
      -如何实例化一个etree对象
          *from lxml import etree
          -1、将 本地的html文档中的源代码数据加载到etree对象中：
              etree.parse(filepath)
          -2、可以将从互联网上获取的数据加载到该对象中
              etree.HTML('page_text')###page_text就是获取的响应数据
          - xpath('xpath表达式')
          -xpath表达式：
                  - / ：表示的是从根目录结点开始定位，表示的是一个层级
                          - // : 表示的是多个层级或从任意位置开始定位（前提是//前面没有结点）
                          -属性定位：tag[@attrName="attrValue"]
                          -索引定位：p[n] 索引是从1开始的
                          -取文本：
              - /text() 获取的是标签中直系的文本内容
              - //text() 标签中非直系的文本内容（所有的文本内容）
              -[0] 可以去除列表框
                  -取属性
              -/@attrName
              

### xpath代码实战示例：

```python
#爬取妹子图片，总共有俩千多张
import requests
from lxml import etree
import os

if __name__ == '__main__':
    ii = 1
    if not os.path.exists('./girl'):
        os.mkdir('./girl')
    url_list = [i for i in range(1, 50)]
    url_list[1] = ''

    for page in url_list:
        url = 'https://www.lanvshen.com/zhongguo/' + str(page) +'.html'
        headers = {
            'User-Agent' : 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.149 Safari/537.36'
        }
        get_text = requests.get(url=url, headers=headers)
        tree = etree.HTML(get_text.text)
        pict_ = tree.xpath('//div[@class="hezi"]//li')
        for it_ in pict_:
            img_url = it_.xpath('./a/img/@src')[0]
            img_name = str(ii) + '.jpg'
            img = requests.get(url=img_url, headers=headers).content
            img_path = './girl/' + img_name
            with open(img_path, 'wb') as fp:
                fp.write(img)
            print(img_name + 'sccuessful...')
            ii = ii + 1

##此代码存在许多不足之处，所爬取的图片都只是像素低的缩略图，并且下载速度较慢
##后期将学习爬取高清图片，并进行分布式爬取

```



### ip代理

1. 代理的原理：在请求目的网站之前，先请求代理服务器，然后让代理服务器去请求目的网站，
   代理服务器拿到目的网站的数据后，再转发给我们的代码
2. [这个网站可以方便的查看http请求的一些参数](http://httpbin.org) 
3. 在代码中(requests模块)使用代理：

```python
	import requests

	url = 'http://httpbin.org'
	headers = {
		'User-Anget': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.149 Safari/537.36'
	}

	#设置代理
	proxy = {
		'http': '123.54.194.96:38661'
	}
	get_text = requerts.get(url=url, headers=headers, proxies=proxy).text
	print(get_text)
```

4. 网络代理有付费和不需要付费的，不需要付费的不稳定，常常会失效
5. 代理的作用是防止我们在爬取服务器的数据时被反爬虫而被封禁导致我们
   的 ip 不能用而采取的措施，选择代理应选择隐蔽性高，不透明的

### cookie 

* [cookie与session的差异](http://blog.sina.com.cn/s/blog_628571e601019uis.html)

* [cookie技术深入](http://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=200538817&idx=2&sn=1147d48f2320fa37e82a7daef4f31c11#rd )			 

  - Cookie是什么？	 

  Cookie 是一小段文本信息，伴随着用户请求和页面在 Web 服务器和浏览器之间传递。
  Cookie 包含每次用户访问站点时 Web 应用程序都可以读取的信息。

  - 为什么需要Cookie？ 

    因为HTTP协议是无状态的，对于一个浏览器发出的多次请求，WEB服务器无法区分 是不是来源于同一个浏览器。
    所以，需要额外的数据用于维护会话。Cookie 正是这样的一段随HTTP请求一起被传递的额外数据。

  - Cookie能做什么？ 

    Cookie只是一段文本，所以它只能保存字符串。而且浏览器对它有大小限制以及 它会随着每次请求被发送到服务器，所以应该保证它
    不要太大。Cookie的内容也是明文保存的，有些浏览器提供界面修改，所以， 不适合保存重要的或者涉及隐私的内容。

  - Cookie 的限制

    大多数浏览器支持最大为 4096 字节的 Cookie。由于这限制了 Cookie 的大小，最好用 Cookie 来存储少量数据，
    或者存储用户 ID 之类的标识符。用户 ID 随后便可用于标识用户，以及从数据库或其他数据源中读取用户信息。 
    浏览器还限制站点可以在用户计算机上存储的 Cookie 的数量。大多数浏览器只允许每个站点存储 20 个 Cookie；
    如果试图存储更多 Cookie，则最旧的 Cookie 便会被丢弃。有些浏览器还会对它们将接受的来自所有站点的 Cookie
    总数作出绝对限制，通常为 300 个。

![Cookie示意图](C:/Users/Acer/Desktop/笔记/爬虫_day4_files/1.jpg)	

- 处理cookie
  如果想要在多次请求中共享cookie，那么应该使用session

### 处理不信任的SSL证书

```python
resp = resquests.get(url=url, vrrify=False).content

```

### 多线程

* 多线程之生产者与消费者模型

- 代码示例一：(Lock版)

```python
	"""
	关于多线程生产者消费者模型
	"""
	import threading
	import time
	import random

	gMoney = 1000
	gLock = threading.Lock()
	gTotalTimes = 10
	gTimes = 0


	# 生产线程Producer
	class Producer(threading.Thread):
		def run(self):
			global gMoney
			global gTimes
			while True:
				money = random.randint(100, 1000)
				gLock.acquire()
				if gTimes >= gTotalTimes:
					gLock.release()
					break
				gMoney += money
				print('{}生产了{}元钱，剩余{}元钱'
					  .format(threading.current_thread(), money, gMoney))
				gTimes += 1
				gLock.release()
				time.sleep(0.5)


	# 消费线程Consumer
	class Consumer(threading.Thread):
		def run(self):
			global gMoney
			while True:
				money = random.randint(100, 1000)
				gLock.acquire()
				if gMoney >= money:
					gMoney -= money
					print('{}消费者消费了{}元钱，剩余{}元钱'
						  .format(threading.current_thread(), money, gMoney))
				# 若剩余金额不足以消费，则退出循环
				else:
					if gTimes >= gTotalTimes:
						gLock.release()
						break
					print('{}消费者准备消费{}元钱，剩余{}元钱，不足！'
						  .format(threading.current_thread(), money, gMoney))
				gLock.release()
				time.sleep(0.5)


	def mainIt():
		# 设置3个消费线程
		for x in range(3):
			t = Consumer(name='消费者线程{}'.format(x))
			t.start()
			# 设置5个生产线程

		for x in range(5):
			t = Producer(name='生产者线程{}'.format(x))
			t.start()


	if __name__ == '__main__':
		mainIt()

```

- 代码示例二：(condition版)

```python
 
	import threading
	import random
	import time

	gMoney = 1000
	gCondition = threading.Condition()  # 等同于threading.Lock
	gTimes = 0
	gTotalTimes = 5
	

	class Producer(threading.Thread):
		def run(self):
			global gMoney
			global gCondition
			global gTimes
			while True:
				money = random.randint(100, 1000)
				gCondition.acquire()  # 加锁（全局变量改变前（gMoney））
				if gTimes >= gTotalTimes:
					gCondition.release()  # 解锁
					print("当前生产者总共生产了%s次" % gTimes)
					break
				gMoney += money
				print("%s当前存入%s元钱，剩余%s元线" % (threading.current_thread(), money, gMoney))
				gTimes += 1
				gCondition.notify_all()  # 通知正在等待的线程（wait）
				gCondition.release()
				time.sleep(0.5)


	class Cosumer(threading.Thread):
		def run(self):
			global gMoney
			while True:
				money = random.randint(100, 1000)
				gCondition.acquire()
				while gMoney < money:
					if gTimes >= gTotalTimes:
						gCondition.release()
						return
					print("%s准备消费%d元钱，剩余%d元钱，不足" % (threading.current_thread(), money, gMoney))
					gCondition.wait()  # 等待状态  （获取锁） 直到生产者把钱加上 （有钱了再去排队消费）
				gMoney -= money
				print("%s消费了%d元钱，剩余%d元钱" % (threading.current_thread(), money, gMoney))
				gCondition.release()
				time.sleep(0.5)


	def main():
		for x in range(3):
			t = Cosumer(name="消费者线程%s" % x)
			t.start()
		for y in range(5):
			t1 = Producer(name="生产者线程%s" % y)
			t1.start()


	if __name__ == '__main__':
		main()

```

- 多线程Lock与condition方法总结：
  1. 在上述的例子中，lock方法虽然实现了多线程的使用，但是当消费者资金不足时，程序在当前线程会重新whlie True，
     这就导致了该线程需要再次上锁和解锁，这就导致了CPU的浪费
  2. 当我们使用condition方法时，每当消费者资金不足，该线程无需重新while True，也不需要再次的上锁和解锁，
     使用wait()，使当资金不足时的消费者线程在wait()暂停并解锁，等待条件满足时再次从wait()处开始执行，这样就
     减少了循环过程中因上锁解锁而导致的CPu资源浪费

* Queue线程安全队列
  在线程中，访问一些全局变量，枷锁是一个经常的过程，如果你是想把某个数据存储到某个队列中，那么python内置了一个线程安全的模块
  叫做queue模块。python中的queue模块中提供了同步的、线程安全的模块，其中队列Queue为先进先出，实现了原子操作，即要么不做，要
  么做完。可使用队列来实现线程的同步。相关函数如下：
  作用：
  　　　解耦：使程序直接实现松耦合，修改一个函数，不会有串联关系。
  　　　提高处理效率：ＦＩＦＯ　＝　现进先出，ＬＩＦＯ　＝　后入先出。

1. Queue.Queue(maxsize=0)   FIFO， 如果maxsize小于1就表示队列长度无限

2. Queue.LifoQueue(maxsize=0)   LIFO， 如果maxsize小于1就表示队列长度无限

3. Queue.qsize()   返回队列的大小

4. Queue.empty()   如果队列为空，返回True,反之False

5. Queue.full()   如果队列满了，返回True,反之False

6. Queue.get([block[, timeout]])   读队列，取出数据 ，没有数据将会等待timeout等待时间

7. Queue.put(item, [block[, timeout]])   写队列，放入数据，timeout等待时间

8. Queue.queue.clear()   清空队列

9. class queue.PriorityQueue(maxsize=0) 存储数据时可设置优先级的队列，优先级设置数越小等级越高

10. Queue.get(timeout = 1)如果1秒后没取到数据就退出

11. Queue.get_nowait() 取数据，如果没数据抛queue.Empty异常

12. Queue.task_done()后续调用告诉队列，任务的处理是完整的。

```python
	from queue import Queue
	import time
	import threading


	# 在队列中放入数值
	def set_value(q):
		index = 0
		while True:
			q.put(index)
			index += 1
			time.sleep(0.8)


	# 取出队列中的数值
	def get_value(q):
		while True:
			print(q.get())


	def main():
		# 产生一个队列
		q = Queue(1)
		t1 = threading.Thread(target=set_value, args=[q])
		t2 = threading.Thread(target=get_value, args=[q])

		t1.start()
		t2.start()


	if __name__ == '__main__':
		main()

```

* 生产者与消费者模型

- 代码示例三：(Queue版)

```python
	import threading,time
	#python2  from Queue import Queue
	#python3
	import queue
	 
	class Producer(threading.Thread):
		def run(self):
			global queue
			count = 0
			while True:
				#qsize返回queue内部长度
				if queue.qsize() < 1000:
					for i in range(100):
						count += 1
						msg = '生成产量'+str(count)
						#put是往queue中放入
						queue.put(msg)
						print(msg)
				time.sleep(0.5)
	class Consumer(threading.Thread):
		def run(self):
			global queue
			while True:
				if queue.qsize() > 100:
					for i in range(3):
						#get从queue中取出一个值
						msg = self.name + "消费了"+queue.get()
						print(msg)
				time.sleep(1)
	if __name__ == '__main__':
		queue = queue.Queue()
		for i in range(500):
			queue.put("初始产品"+str(i))
		for i in range(2):
			p = Producer()
			p.start()
		for i in range(5):
			c = Consumer()
			c.start()
			
```

### 爬虫之多线程

* 多线程爬虫框架代码

```python
	import requests
	from lxml import etree
	import os
	import re
	from queue import Queue
	import threading

	""" 
	我们引入了threading这个包，我们将使用这个包中的
	Thread类，并在我们自定义的类中继承这个Thread类
	"""


	# 生产者线程
	class Procuder(threading.Thread):
		headers = {
				'User-Agent': '... 浏览器...'
		}

		""" 重写继承于父类的__init__方法 """
		""" 我们继承了Thread这个类，在调用类中的方法时可能需要许多参数，我们用*args和**kwargs来接收"""
		def __init__(self, page_queue, img_queue, *args, **kwargs):
			super(Procuder, self).__init__(*args, **kwargs)
			self.page_queue = page_queue
			self.img_queue = img_queue

		""" 重写继承于父类的run方法，改成我们想要的方法"""
		""" 从存放每页的url的队列中逐一挑出一个url并传入parse_page中
			获取每页的url
		"""
		def run(self):
			while True:
				if self.page_queue.empty():
					break
				url = self.page_queue.get()
				self.parse_page(url)

		def parse_page(self, url):
			""" 
			.......
			
			获取每页的响应数据，并从中提取出每张图片的
			url和name，并放入队列中
			"""


	# 消费者线程
	class Consumer(threading.Thread):
		def __init__(self, page_queue, img_queue, *args, **kwargs):
			super(Procuder, self).__init__(*args, **kwargs)
			self.page_queue = page_queue
			self.img_queue = img_queue

		def run(self):
			while True:
				if self.img_queue.empty() and self.page_queue.empty():
					break
					"""
					......
					
					利用从队列中获取的每张图片的url和name，进行图片的获取和永久储存
					"""

	def main():
		""" 设置页数和图片的url队列"""
		page_queue = Queue()
		img_queue = Queue()

		# 将每页的url放到页数队列中
		for x in range(1, 101):
			url = 'http://........ %d' % x
			page_queue.put(url)

		# 设置5个生产者线程
		for x in range(5):
			t = Procuder(page_queue, img_queue)
			t.start()

		# 设置5个消费者线程
		for x in range(5):
			t = Consumer(page_queue, img_queue)
			t.start()


	if __name__ == '__main__':
		main()

```

* 多线程爬虫实战代码

```python
	import requests
	import re
	from lxml import etree
	import threading
	import os
	import queue
	from urllib import request

	# 生产图片的url和name
	class ProimgUrl(threading.Thread):
		def __init__(self, page_queue, img_queue, headers, *args, **kwargs):
			super().__init__(*args, **kwargs)
			self.page_queue = page_queue
			self.img_queue = img_queue
			self.headers = headers

		def run(self):
			while True:
				if self.page_queue.empty():
					break
				else:
					url = self.page_queue.get()
					self.parse_page(url)

		def parse_page(self, url):
			img_get = requests.get(url=url, headers=self.headers).text
			tree = etree.HTML(img_get)
			img_it = tree.xpath('//div[@class="page-content text-center"]//a')
			for it in img_it:
				img_url = it.xpath('./img[@class!="gif"]/@data-original')[0]
				alt = it.xpath('./img/@alt')[0]
				alt = re.sub(r'[\?？.!！，。\/\*]', '', alt)
				# 利用splitext截取图片的后缀名
				suffix = os.path.splitext(img_url)[1]
				img_name = alt + suffix
				self.img_queue.put((img_url, img_name))


	# 获取图片并储存
	class ConimgName(threading.Thread):
		def __init__(self, page_queue, img_queue, headers, *args, **kwargs):
			super().__init__(*args, **kwargs)
			self.page_queue = page_queue
			self.img_queue = img_queue
			self.headers = headers

		def run(self):
			while True:
				if self.img_queue.empty() and self.page_queue.empty():
					break
				else:
					img_url, img_name = self.img_queue.get()
					# 用urlli包下的request.urlretrieve模块，可以更方便的获取和存储图片
					request.urlretrieve(img_url, './smile/'+img_name)
					print(img_name + ' 下载成功...')

	def main():
		if not os.path.exists('./smile'):
			os.mkdir('./smile')
		headers = {
			'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) '
						  'Chrome/80.0.3987.149 Safari/537.36 '
		}
		page_queue = queue.Queue(100)
		img_queue = queue.Queue(1000)

		for x in range(1, 10):
			url = 'https://www.doutula.com/photo/list/?page=' + str(x)
			page_queue.put(url)

		for i in range(5):
			t = ProimgUrl(page_queue, img_queue, headers)
			t.start()

		for i in range(5):
			t = ConimgName(page_queue, img_queue, headers)
			t.start()


	if __name__ == '__main__':
		main()

```

* 多线程爬虫的一些坑：多线程爬虫爬取的内容是无序的



# 爬虫第五天

## scrapy爬虫框架

[关于xpath选择器selector的网址](https://zhuanlan.zhihu.com/p/25443389)



![scrapy框架示意图](https://img-blog.csdnimg.cn/20200328091921480.png)

1. 创建项目

   ​	scrapy startproject #^%#^(项目名字)

   

2. 创建爬虫

   ​	scrapy genspider 爬虫名字 网络域名

   注意：

    * 爬虫名字不要和项目名字一样
    * 网站域名是允许爬虫采集的域名
      - baidu.com

![创建项目与配置环境后各部分组件](https://img-blog.csdn.net/20180704211604860?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNDcyNzY1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

item文件是存储数据的

* 在setting文件（程序启动被首先检查的文件）

  - BOT_NAME = 爬虫的名字

  - ROBOTSTXT_OBEY = 网络君子协议君子协议（不遵守）， 布尔

  - CONCURRENT_REQUESTS = 爬虫的并发量

  - DOWOLOAD_DELAY = 下载延迟，一般是1.5或2，小项目不用管

  - COOKLES_ENABLED = cookiede的使用，布尔

  - DEFAULT_REQUEST_HEADERS = 默认请求头

  - ITEM_PIPELINES = 管道文件

    

    ### setting文件详解

    ```python
    # -*- coding: utf-8 -*-
     
    # Scrapy settings for TestSpider project
    #
    # For simplicity, this file contains only settings considered important or
    # commonly used. You can find more settings consulting the documentation:
    #
    #     https://doc.scrapy.org/en/latest/topics/settings.html
    #     https://doc.scrapy.org/en/latest/topics/downloader-middleware.html
    #     https://doc.scrapy.org/en/latest/topics/spider-middleware.html
     
    BOT_NAME = 'TestSpider'
     
    SPIDER_MODULES = ['TestSpider.spiders']
    NEWSPIDER_MODULE = 'TestSpider.spiders'
     
     
    # Crawl responsibly by identifying yourself (and your website) on the user-agent
    #USER_AGENT = 'TestSpider (+http://www.yourdomain.com)'
     
    USER_AGENT = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:61.0) Gecko/20100101 Firefox/61.0'
     
    # Obey robots.txt rules
    # Scrapy框架默认遵守 robots.txt 协议规则，robots规定了一个网站中，哪些地址可以请求，哪些地址不能请求。
    # 默认是True，设置为False不遵守这个协议。
    ROBOTSTXT_OBEY = False
     
     
    # Configure maximum concurrent requests performed by Scrapy (default: 16)
    # 配置scrapy的请求连接数，默认会同时并发16个请求。
    # CONCURRENT_REQUESTS = 10
     
    # Configure a delay for requests for the same website (default: 0)
    # See https://doc.scrapy.org/en/latest/topics/settings.html#download-delay
    # See also autothrottle settings and docs
     
    # 下载延时，请求和请求之间的间隔，降低爬取速度，default: 0
    # DOWNLOAD_DELAY = 3
     
     
    # CONCURRENT_REQUESTS_PER_DOMAIN：针对网站(主域名)设置的最大请求并发数。
    # CONCURRENT_REQUESTS_PER_IP：某一个IP的最大请求并发数。
    # The download delay setting will honor only one of:
    # CONCURRENT_REQUESTS_PER_DOMAIN = 16
    # CONCURRENT_REQUESTS_PER_IP = 16
     
     
    # Disable cookies (enabled by default)
    # 是否启用Cookie的配置，默认是可以使用Cookie的。主要是针对一些网站是禁用Cookie的。
    # COOKIES_ENABLED = False
     
    # Disable Telnet Console (enabled by default)
    #TELNETCONSOLE_ENABLED = False
     
     
    # Override the default request headers:
     
    # 配置默认的请求头Headers.
    # DEFAULT_REQUEST_HEADERS = {
    #   'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
    #   'Accept-Language': 'en',
    # }
     
     
    # Enable or disable spider middlewares
    # See https://doc.scrapy.org/en/latest/topics/spider-middleware.html
     
    # 配置自定义爬虫中间件，scrapy也默认启用了一些爬虫中间件，可以在这个配置中关闭。
    # SPIDER_MIDDLEWARES = {
    #    'TestSpider.middlewares.TestspiderSpiderMiddleware': 543,
    # }
     
     
    # 下载中间件，配置自定义的中间件或者取消Scrapy默认启用的中间件。
    # Enable or disable downloader middlewares
    # See https://doc.scrapy.org/en/latest/topics/downloader-middleware.html
    # DOWNLOADER_MIDDLEWARES = {
    #    'TestSpider.middlewares.TestspiderDownloaderMiddleware': 543,
    # }
     
     
    # Enable or disable extensions
    # See https://doc.scrapy.org/en/latest/topics/extensions.html
    # EXTENSIONS = {
    #    'scrapy.extensions.telnet.TelnetConsole': None,
    # }
     
     
    # Configure item pipelines
    # See https://doc.scrapy.org/en/latest/topics/item-pipeline.html
     
    # 配置自定义的PIPELINES，或者取消PIPELINES默认启用的中间件。
    # ITEM_PIPELINES = {
    #    'TestSpider.pipelines.TestspiderPipeline': 300,
    # }
     
     
    # 限速配置
    # Enable and configure the AutoThrottle extension (disabled by default)
    # See https://doc.scrapy.org/en/latest/topics/autothrottle.html
     
    # 是否开启自动限速
    # AUTOTHROTTLE_ENABLED = True
     
     
    # The initial download delay
    # 配置初始url的下载延时
    # AUTOTHROTTLE_START_DELAY = 5
     
     
    # The maximum download delay to be set in case of high latencies
    # 配置最大请求时间
    # AUTOTHROTTLE_MAX_DELAY = 60
     
     
    # 配置请求和请求之间的下载间隔，单位是秒
    # The average number of requests Scrapy should be sending in parallel to
    # each remote server
    # AUTOTHROTTLE_TARGET_CONCURRENCY = 1.0
     
     
    # Enable showing throttling stats for every response received:
    # AUTOTHROTTLE_DEBUG = False
     
     
    # 关于Http缓存的配置，默认是不启用。
    # 对于同一个页面的请求进行数据的缓存，如果后续还有相同的请求，直接从缓存中进行获取。
    # Enable and configure HTTP caching (disabled by default)
    # See https://doc.scrapy.org/en/latest/topics/downloader-middleware.html#httpcache-middleware-settings
    #HTTPCACHE_ENABLED = True
    #HTTPCACHE_EXPIRATION_SECS = 0
    #HTTPCACHE_DIR = 'httpcache'
    #HTTPCACHE_IGNORE_HTTP_CODES = []
    #HTTPCACHE_STORAGE = 'scrapy.extensions.httpcache.FilesystemCacheStorage'
    ```

    

    ### scrapy爬虫之spider类（示例 一：爬取糗事百科）

    - 创建爬虫项目：scrapy startproject xiushi
    - 创建爬虫文件:   scrapy genSpider xiushiSpider  qiushidabaike.com

    #### item 部分

    ```python
    # -*- coding: utf-8 -*-
    
    # Define here the models for your scraped items
    #
    # See documentation in:
    # https://doc.scrapy.org/en/latest/topics/items.html
    
    import scrapy
    
    
    class XiushiItem(scrapy.Item):
        # define the fields for your item here like:
        # name = scrapy.Field()
        title = scrapy.Field()
        text = scrapy.Field()
    
    ```

    #### setting 部分

    ```python
    # -*- coding: utf-8 -*-
    
    # Scrapy settings for xiushi project
    #
    # For simplicity, this file contains only settings considered important or
    # commonly used. You can find more settings consulting the documentation:
    #
    #     https://doc.scrapy.org/en/latest/topics/settings.html
    #     https://doc.scrapy.org/en/latest/topics/downloader-middleware.html
    #     https://doc.scrapy.org/en/latest/topics/spider-middleware.html
    
    BOT_NAME = 'xiushi'
    
    SPIDER_MODULES = ['xiushi.spiders']
    NEWSPIDER_MODULE = 'xiushi.spiders'
    
    # Crawl responsibly by identifying yourself (and your website) on the user-agent
    # USER_AGENT = 'xiushi (+http://www.yourdomain.com)'
    
    # Obey robots.txt rules
    ROBOTSTXT_OBEY = False
    
    # Configure maximum concurrent requests performed by Scrapy (default: 16)
    # CONCURRENT_REQUESTS = 32
    
    # Configure a delay for requests for the same website (default: 0)
    # See https://doc.scrapy.org/en/latest/topics/settings.html#download-delay
    # See also autothrottle settings and docs
    DOWNLOAD_DELAY = 1
    # The download delay setting will honor only one of:
    # CONCURRENT_REQUESTS_PER_DOMAIN = 16
    # CONCURRENT_REQUESTS_PER_IP = 16
    
    # Disable cookies (enabled by default)
    # COOKIES_ENABLED = False
    
    # Disable Telnet Console (enabled by default)
    # TELNETCONSOLE_ENABLED = False
    
    # Override the default request headers:
    DEFAULT_REQUEST_HEADERS = {
        'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
        'Accept-Language': 'en',
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) '
                      'Chrome/80.0.3987.163 Safari/537.36 '
    }
    
    # Enable or disable spider middlewares
    # See https://doc.scrapy.org/en/latest/topics/spider-middleware.html
    # SPIDER_MIDDLEWARES = {
    #    'xiushi.middlewares.XiushiSpiderMiddleware': 543,
    # }
    
    # Enable or disable downloader middlewares
    # See https://doc.scrapy.org/en/latest/topics/downloader-middleware.html
    # DOWNLOADER_MIDDLEWARES = {
    #    'xiushi.middlewares.XiushiDownloaderMiddleware': 543,
    # }
    
    # Enable or disable extensions
    # See https://doc.scrapy.org/en/latest/topics/extensions.html
    # EXTENSIONS = {
    #    'scrapy.extensions.telnet.TelnetConsole': None,
    # }
    
    # Configure item pipelines
    # See https://doc.scrapy.org/en/latest/topics/item-pipeline.html
    ITEM_PIPELINES = {
       'xiushi.pipelines.XiushiPipeline': 300,
    }
    
    # Enable and configure the AutoThrottle extension (disabled by default)
    # See https://doc.scrapy.org/en/latest/topics/autothrottle.html
    # AUTOTHROTTLE_ENABLED = True
    # The initial download delay
    # AUTOTHROTTLE_START_DELAY = 5
    # The maximum download delay to be set in case of high latencies
    # AUTOTHROTTLE_MAX_DELAY = 60
    # The average number of requests Scrapy should be sending in parallel to
    # each remote server
    # AUTOTHROTTLE_TARGET_CONCURRENCY = 1.0
    # Enable showing throttling stats for every response received:
    # AUTOTHROTTLE_DEBUG = False
    
    # Enable and configure HTTP caching (disabled by default)
    # See https://doc.scrapy.org/en/latest/topics/downloader-middleware.html#httpcache-middleware-settings
    # HTTPCACHE_ENABLED = True
    # HTTPCACHE_EXPIRATION_SECS = 0
    # HTTPCACHE_DIR = 'httpcache'
    # HTTPCACHE_IGNORE_HTTP_CODES = []
    # HTTPCACHE_STORAGE = 'scrapy.extensions.httpcache.FilesystemCacheStorage'
    
    ```

    #### spider 部分

    ```python
    # -*- coding: utf-8 -*-
    import scrapy
    from xiushi.items import XiushiItem
    
    
    class XiushispiderSpider(scrapy.Spider):
        name = 'xiushiSpider'
        allowed_domains = ['qiushidabaike.com']
        start_urls = ['http://qiushidabaike.com/index_1.html']
        base_domain = 'http://qiushidabaike.com'
    
        def parse(self, response):
            duanzi = response.xpath('//div[@class="main-left fl"]//dl')
            for it in duanzi:
                title = it.xpath('./dt/span/a/text()').get().strip()
                text = it.xpath('./dd[@class="content"]//text()').getall()
                text = "".join(text).strip()
                item = XiushiItem(title=title, text=text)
                yield item
            # 进行翻页操作
            next_url = response.xpath('//div[@class="page"]//a[@class="next"]/@href').get()
            if not next_url:
                return
            else:
                yield scrapy.Request(self.base_domain+next_url, callback=self.parse)
    
    ```

    #### pipelines 部分

    ```python
    # -*- coding: utf-8 -*-
    
    # Define your item pipelines here
    #
    # Don't forget to add your pipeline to the ITEM_PIPELINES setting
    # See: https://doc.scrapy.org/en/latest/topics/item-pipeline.html
    """
    # 第一种方法，没进行json优化，但是看起来简单易操作
    import json
    
    
    class XiushiPipeline(object):
        def __init__(self):
            self.fp = open('duanzi.json', 'w', encoding='utf-8')
    
        def open_spider(self, spider):
            print('爬虫开始了...')
    
        def process_item(self, item, spider):
            item_json = json.dumps(dict(item),  ensure_ascii=False)
            self.fp.write(item_json+'\n')
            return item
    
        def close_spider(self, spider):
            self.fp.close()
            print("爬虫结束了...")
    """
    # 方案二，json优化，结果是每个字典存储在列表中
    #        好处是满足json规则，坏处是数据量较大时比较耗内存
    # ''' 自我感觉这种方法不咋地 '''
    # from scrapy.exporters import JsonItemExporter
    #
    #
    # class XiushiPipeline(object):
    #     def __init__(self):
    #         # 注意此时以二进制形式写入
    #         self.fp = open('duanzi.json', 'wb')
    #         self.exporter = JsonItemExporter(self.fp, ensure_ascii=False, encoding='utf-8')
    #         self.exporter.start_exporting()
    #
    #     def open_spider(self, spider):
    #         print('爬虫开始了...')
    #
    #     def process_item(self, item, spider):
    #         self.exporter.export_item(item)
    #         return item
    #
    #     def close_spider(self, spider):
    #         self.exporter.finish_exporting()
    #         self.fp.close()
    #         print("爬虫结束了...")
    
    '''方案三'''
    from scrapy.exporters import JsonLinesItemExporter
    
    
    class XiushiPipeline(object):
        def __init__(self):
            # 注意此时以二进制形式写入
            self.fp = open('duanzi.json', 'wb')
            self.exporter = JsonLinesItemExporter(self.fp, ensure_ascii=False, encoding='utf-8')
    
        def open_spider(self, spider):
            print('爬虫开始了...')
    
        def process_item(self, item, spider):
            self.exporter.export_item(item)
            return item
    
        def close_spider(self, spider):
            self.fp.close()
            print("爬虫结束了...")
    
    ```

    * 可以在总文件中创建.py文件，代替爬虫开始命令 scrapy crawl xiushiSpider (注意应在爬虫目录下执行该命令)

      * ```python
        from scrapy import cmdline
        
        cmdline.execute("scrapy crawl xiushiSpider".split())
        # cmdline.execute(["scrapy", 'crawl', 'xiushiSpider'])
        ```

        

# 爬虫第六天

### scrapy 框架之spider类（示例二.爬取优美散文）

- item部分

```python
# -*- coding: utf-8 -*-

# Define here the models for your scraped items
#
# See documentation in:
# https://doc.scrapy.org/en/latest/topics/items.html

import scrapy


class ProseItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    title = scrapy.Field()
    text = scrapy.Field()

```



- settings部分

```python
# -*- coding: utf-8 -*-

# Scrapy settings for prose project
#
# For simplicity, this file contains only settings considered important or
# commonly used. You can find more settings consulting the documentation:
#
#     https://doc.scrapy.org/en/latest/topics/settings.html
#     https://doc.scrapy.org/en/latest/topics/downloader-middleware.html
#     https://doc.scrapy.org/en/latest/topics/spider-middleware.html

BOT_NAME = 'prose'

SPIDER_MODULES = ['prose.spiders']
NEWSPIDER_MODULE = 'prose.spiders'

# Crawl responsibly by identifying yourself (and your website) on the user-agent
# USER_AGENT = 'prose (+http://www.yourdomain.com)'

# Obey robots.txt rules
ROBOTSTXT_OBEY = False

# Configure maximum concurrent requests performed by Scrapy (default: 16)
# CONCURRENT_REQUESTS = 32

# Configure a delay for requests for the same website (default: 0)
# See https://doc.scrapy.org/en/latest/topics/settings.html#download-delay
# See also autothrottle settings and docs
DOWNLOAD_DELAY = 1
# The download delay setting will honor only one of:
# CONCURRENT_REQUESTS_PER_DOMAIN = 16
# CONCURRENT_REQUESTS_PER_IP = 16

# Disable cookies (enabled by default)
# COOKIES_ENABLED = False

# Disable Telnet Console (enabled by default)
# TELNETCONSOLE_ENABLED = False

# Override the default request headers:
DEFAULT_REQUEST_HEADERS = {
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
    'Accept-Language': 'en',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) '
                  'Chrome/80.0.3987.163 Safari/537.36 '
}

# Enable or disable spider middlewares
# See https://doc.scrapy.org/en/latest/topics/spider-middleware.html
# SPIDER_MIDDLEWARES = {
#    'prose.middlewares.ProseSpiderMiddleware': 543,
# }

# Enable or disable downloader middlewares
# See https://doc.scrapy.org/en/latest/topics/downloader-middleware.html
# DOWNLOADER_MIDDLEWARES = {
#    'prose.middlewares.ProseDownloaderMiddleware': 543,
# }

# Enable or disable extensions
# See https://doc.scrapy.org/en/latest/topics/extensions.html
# EXTENSIONS = {
#    'scrapy.extensions.telnet.TelnetConsole': None,
# }

# Configure item pipelines
# See https://doc.scrapy.org/en/latest/topics/item-pipeline.html
ITEM_PIPELINES = {
   'prose.pipelines.ProsePipeline': 300,
}

# Enable and configure the AutoThrottle extension (disabled by default)
# See https://doc.scrapy.org/en/latest/topics/autothrottle.html
# AUTOTHROTTLE_ENABLED = True
# The initial download delay
# AUTOTHROTTLE_START_DELAY = 5
# The maximum download delay to be set in case of high latencies
# AUTOTHROTTLE_MAX_DELAY = 60
# The average number of requests Scrapy should be sending in parallel to
# each remote server
# AUTOTHROTTLE_TARGET_CONCURRENCY = 1.0
# Enable showing throttling stats for every response received:
# AUTOTHROTTLE_DEBUG = False

# Enable and configure HTTP caching (disabled by default)
# See https://doc.scrapy.org/en/latest/topics/downloader-middleware.html#httpcache-middleware-settings
# HTTPCACHE_ENABLED = True
# HTTPCACHE_EXPIRATION_SECS = 0
# HTTPCACHE_DIR = 'httpcache'
# HTTPCACHE_IGNORE_HTTP_CODES = []
# HTTPCACHE_STORAGE = 'scrapy.extensions.httpcache.FilesystemCacheStorage'

```

- proseSpider部分

```python
# -*- coding: utf-8 -*-
import scrapy
from prose.items import ProseItem


class ProsespiderSpider(scrapy.Spider):
    name = 'proseSpider'
    allowed_domains = ['duwenzhang.com']
    start_urls = ['http://www.duwenzhang.com/wenzhang/shenghuosuibi/']
    base_url = 'http://www.duwenzhang.com/wenzhang/shenghuosuibi/'

    def parse(self, response):
        # xpath 选择不含有某一属性的标签
        url_list = response.xpath('//center//tr[2]//td[(@valign) and not(@height)]/table')
        for it in url_list:
            title = it.xpath('.//tr[2]//td[@height="26"]/b/a/text()').getall()
            text = it.xpath('.//tr[4]/td[@style="padding-left:3px"]/text()').getall()
            text = "".join(text).strip()
            item = ProseItem(title=title, text=text)
            yield item
        # //a[contains(text(), "百度搜索")]  【可以部分检索】
        # //a[text()="文字内容"] 【需要填全】 xpath定位指定文本标签
        next_url = response.xpath('//tr//a[text()="下一页"]/@href').get()
        if not next_url:
            return
        else:
            yield scrapy.Request(self.base_url+next_url, callback=self.parse)


```



- piplines 部分

```python
# -*- coding: utf-8 -*-

# Define your item pipelines here
#
# Don't forget to add your pipeline to the ITEM_PIPELINES setting
# See: https://doc.scrapy.org/en/latest/topics/item-pipeline.html

import json
from scrapy.exporters import JsonLinesItemExporter


class ProsePipeline(object):
    def __init__(self):
        self.fp = open('prose.json', 'wb')
        self.exporter = JsonLinesItemExporter(self.fp, ensure_ascii=False,
                                              encoding='utf-8')

    def open_spider(self, spider):
        print('爬虫开始了...')

    def process_item(self, item, spider):
        self.exporter.export_item(item)
        return item

    def close_spider(self, spider):
        self.fp.close()
        print('爬虫结束了....')
```



