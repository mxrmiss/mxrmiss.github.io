---
title: DC_1靶机渗透测试 
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_3.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: 渗透测试
tags: [渗透测试, DC]   # 可以是数组
top: false
---

# DC_1靶机渗透测试

- [参考链接1]([萌新教程丨VulnHubDC系列之DC1靶机渗透测试 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/135342104))
- [参考链接2]([Vulnhub靶机渗透测试——DC-1 - 简书 (jianshu.com)](https://www.jianshu.com/p/86b3c598a4c4))
- [参考链接3]([(4条消息) Vulnhub-DC1靶机渗透_adminuil的博客-CSDN博客](https://blog.csdn.net/adminuil/article/details/101269264))
- [参考链接4]([Linux suid 提权 - Junsec - 博客园 (cnblogs.com)](https://www.cnblogs.com/junsec/p/11652723.html#:~:text=Linux suid 提权. SUID ( S et owner,在 Linux%2FUnix中，当一个程序运行的时候， 程序将从登录用户处继承权限。. SUID被定义为给予一个用户临时的（程序%2F文件）所有者的权限来运行一个程序%2F文件 。. 用户在执行程序%2F文件%2F命令的时候，将获取文件所有者的权限以及所有者的UID和GID。. 查找suid文件. %3F))

## 一、DC_1的准备

1.  在官网上下载DC_1镜像文件到电脑1上并且解压到虚拟机上，我使用的是VMware
2. 将DC_1与攻击机器配置在同一网段上

[![R9rCFS.png](https://z3.ax1x.com/2021/06/18/R9rCFS.png)](https://imgtu.com/i/R9rCFS)

- 以管理员权限**更改设置**

![DC_1_2](http://cdn.mxrblog.cn/DC_1_2.jpg)

- 查看物理电脑正在使用的网卡，将虚拟机网卡设置为一致

![DC1_3](http://cdn.mxrblog.cn/DC1_3.png)



## 二、信息收集

### 1. nmap

- 先使用nmap侦测DC1的IP地址

```
nmap -sP 192.168.0.0/24       //查询这个网段里面的活的IP
```

![image-20210619095954498](http://cdn.mxrblog.cn/image-20210619095954498.png)

### 2. arp-scan -l

- 再使用sudo arp-scan -l 探测，可知DC_1的IP是192.168.0.155

![image-20210619100321088](http://cdn.mxrblog.cn/image-20210619100321088.png)

### 3. 查看DC_1的开放端口

- 使用nmap -sV -A 192.168.0.155 可以发现DC_1开放了80端口

![image-20210619100832836](http://cdn.mxrblog.cn/image-20210619100832836.png)

### 4. 登陆192.168.0.155:80

- 发现网站下面有powered by Drupal，可以推断出网站的CMS是Drupal

![image-20210619101204599](http://cdn.mxrblog.cn/image-20210619101204599.png)

## 二、寻找漏洞

- 由于我们已经知晓网站的CMS是Drupal，于是我们可以使用msf探测Drupal的漏洞，并加以利用

![image-20210619101755241](http://cdn.mxrblog.cn/image-20210619101755241.png)

- 使用漏洞4，并发现必须注明RHOSTS

![image-20210619102053400](http://cdn.mxrblog.cn/image-20210619102053400.png)

- 注明Rhosts， 并且运行（exploit和run运行皆可）

![image-20210619102607700](http://cdn.mxrblog.cn/image-20210619102607700.png)



## 三、寻找flag

- 执行ls， 并且发现flag1

![image-20210619102900990](http://cdn.mxrblog.cn/image-20210619102900990.png)

- 查看flag1的信息， 发现1在提示我们查看CMS的配置文件

![image-20210619103026632](http://cdn.mxrblog.cn/image-20210619103026632.png)

- 通过网上搜素，发现Drupal的配置文件在 /var/www/sites/default 下，切换至该目录下，发现配置文件settings.php，查看内容发现flag2，并且提示不能使用字典暴力破解，通过观察配置文件，我们找到了MySQL的登录名以及密码

![image-20210619103714950](http://cdn.mxrblog.cn/image-20210619103714950.png)

- 登录mysql，发现无法只用mysql命令

![image-20210619104328728](http://cdn.mxrblog.cn/image-20210619104328728.png)

- 由于mfs终端下的局限性，可使用的命令较少，获取DC_1的shell并设置一个交互式shell用来回显MySQL指令

```
shell     //进入shell
python -c "import pty;pty.spawn('/bin/bash')"   //设置交互式shell
```

![image-20210619104807264](http://cdn.mxrblog.cn/image-20210619104807264.png)

- 再次登录MySQL

![image-20210619105030783](http://cdn.mxrblog.cn/image-20210619105030783.png)

- 寻找储存有用信息的表

![image-20210619105436601](http://cdn.mxrblog.cn/image-20210619105436601.png)

- 发现表users

![image-20210619105516728](http://cdn.mxrblog.cn/image-20210619105516728.png)

- 查看表users的内容， 发现管理员以及经过加密后的密码，通过网上搜索，得知该CMS的加密方式文件在 /var/www/scripts/password-hash.sh

![image-20210619105726822](http://cdn.mxrblog.cn/image-20210619105726822.png)

- 经过Google得知，使用  ./scripts/password-hash.sh   %^@#&  可以加密，获取加密后的密码

![image-20210619110656291](http://cdn.mxrblog.cn/image-20210619110656291.png)

- 再次进入数据库并且更改管理员的密码

![image-20210619111111061](http://cdn.mxrblog.cn/image-20210619111111061.png)

- 利用更改后的账号密码登录网站，寻找后发现flag3，flag3的信息中提到了passwd和shadow,很明显就是`/etc/passwd和/etc/shadow`，还提到了`find、perms、-exec`，这几个是提权用的。

![image-20210619111628491](http://cdn.mxrblog.cn/image-20210619111628491.png)

- 查看 /etc/passwd，可以找到flag4的位置

![image-20210619113107387](http://cdn.mxrblog.cn/image-20210619113107387.png)

- 查看flag4的内容，发现在root下有flag，需要提权

![image-20210619113333252](http://cdn.mxrblog.cn/image-20210619113333252.png)

- 通过find命令查看有哪些使用root权限执行的文件

```
find / -perm -u=s -type f 2>/dev/null
```

![image-20210619114815699](http://cdn.mxrblog.cn/image-20210619114815699.png)

- 发现find可以利用，并且获取root权限

![image-20210619115449178](http://cdn.mxrblog.cn/image-20210619115449178.png)

- 进入root目录，发现并打开最后一个flag

![image-20210619115723615](http://cdn.mxrblog.cn/image-20210619115723615.png)

+ **另外还可以使用find + netcat的方式获取root权限**

- *对于靶机shell，发送自己的shell*

![image-20210619120620378](http://cdn.mxrblog.cn/image-20210619120620378.png)

- *对于攻击机器，监听靶机上建立特意开放的端口号*

![image-20210619120759548](http://cdn.mxrblog.cn/image-20210619120759548.png)



## 知识点

### [Linux suid 提权]([Linux suid 提权 - Junsec - 博客园 (cnblogs.com)](https://www.cnblogs.com/junsec/p/11652723.html#:~:text=Linux suid 提权. SUID ( S et owner,在 Linux%2FUnix中，当一个程序运行的时候， 程序将从登录用户处继承权限。. SUID被定义为给予一个用户临时的（程序%2F文件）所有者的权限来运行一个程序%2F文件 。. 用户在执行程序%2F文件%2F命令的时候，将获取文件所有者的权限以及所有者的UID和GID。. 查找suid文件. %3F))

　**SUID** (**S**et owner **U**ser **ID** up on execution) 是给予文件的一个特殊类型的文件权限。在 Linux/Unix中，当一个程序运行的时候， 程序将从登录用户处继承权限。SUID被定义为给予一个用户临时的（程序/文件）所有者的权限来运行一个程序/文件**。用户在执行程序/文件/命令的时候，将获取文件所有者的权限以及所有者的UID和GID。**

- 查找suid文件

```
find / -perm -u=s -type f 2> /dev/null
/表示从文件系统的顶部（根）开始并找到每个目录
-perm 表示搜索随后的权限``-u = s表示查找root用户拥有的文件
type表示我们正在寻找的文件类型
f 表示常规文件，而不是目录或特殊文件
2表示该进程的第二个文件描述符，即stderr（标准错误）
>表示重定向
/ dev / null是一个特殊的文件系统对象，它将丢弃写入其中的所有内容。
```

- find提权

```
find -exec /bin/sh \;
```

