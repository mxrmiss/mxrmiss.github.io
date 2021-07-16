---
title: python爬虫（六）
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_2.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: python
tags: [python, 爬虫]   # 可以是数组
top: false

---



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

