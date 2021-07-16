---
title: python爬虫（四）
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_2.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: python
tags: [python, 爬虫]   # 可以是数组
top: false

---



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

![Cookie示意图](C:\Users\Acer\Desktop\笔记\爬虫_day4_files\1.jpg)	

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

