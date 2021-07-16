---
title: python（五）
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_2.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: python
tags: [python, 爬虫]   # 可以是数组
top: false

---



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

* BOT_NAME = 爬虫的名字

* ROBOTSTXT_OBEY = 网络君子协议君子协议（不遵守）， 布尔

* CONCURRENT_REQUESTS = 爬虫的并发量

* DOWOLOAD_DELAY = 下载延迟，一般是1.5或2，小项目不用管

* COOKLES_ENABLED = cookiede的使用，布尔

* DEFAULT_REQUEST_HEADERS = 默认请求头

* ITEM_PIPELINES = 管道文件

  

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

```python
from scrapy import cmdline

cmdline.execute("scrapy crawl xiushiSpider".split())
# cmdline.execute(["scrapy", 'crawl', 'xiushiSpider'])
```

