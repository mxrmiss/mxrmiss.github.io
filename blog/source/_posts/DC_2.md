---
title: DC_2靶机渗透测试 
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_3.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: 渗透测试
tags: [渗透测试, DC]   # 可以是数组
top: false

---

# DC_2靶机渗透测试

[参考链接1](https://cloud.tencent.com/developer/article/1801074)

[参考链接2](https://www.anquanke.com/post/id/178658)

### 一、信息收集

1. 查询攻击机器IP

![image-20210621214110642](http://cdn.mxrblog.cn/image-20210621214110642.png)

2. 扫描靶机，并查看其开放端口，发现80端口开启，可以利用http服务

![image-20210621214259348](http://cdn.mxrblog.cn/image-20210621214259348.png)



### 二、寻找flag

3. 打开浏览器，搜索目标靶机的80端口，发现主机名为dc-2，并且无法访问

![image-20210621214817866](http://cdn.mxrblog.cn/image-20210621214817866.png)

4. 将192.168.0.131	dc-2     添加到攻击机器的hosts文件中

![image-20210621215059251](http://cdn.mxrblog.cn/image-20210621215059251.png)

5. 再次进入，发现flag1，并且发现网站CMS采用的是wordpress设计

![image-20210621215501914](http://cdn.mxrblog.cn/image-20210621215501914.png)

6. 通过nmap进一步发现wordpress的版本为4.7.10

![image-20210621220137364](http://cdn.mxrblog.cn/image-20210621220137364.png)

7. 搜索wordpress的漏洞，用wpscan —url 192.168.0.131, 发现提示使用域名代替IP地址，于是输入：

```
wpscan --url http://dc-2/ -e u    //枚举用户
```

![image-20210622201359238](http://cdn.mxrblog.cn/image-20210622201359238.png)

- ​	创建密码字典

```
cewl http://dc-2/ > pwd
```

- 暴力破解得到密码，其中admin用户的秘法无法获取，可以获取到jerry和tom的密码

```
wpscan --url http://dc-2 -U admin -P /tmp/pasword.txt
-U 是指定爆破的用户名
-P  使用的密码本
```

![image-20210622202854604](http://cdn.mxrblog.cn/image-20210622202854604.png)

![image-20210622203102094](http://cdn.mxrblog.cn/image-20210622203102094.png)

- tom登陆后发现不了flag，但是登录jerry用户后成功找到flag2

![image-20210622203812083](http://cdn.mxrblog.cn/image-20210622203812083.png)

- 通过提示，发现还有其他方法能找到flag，于是再次用nmap扫描更多的端口，发现ssh端口是7744

![image-20210622210133772](http://cdn.mxrblog.cn/image-20210622210133772.png)

- 使用hydra(九头蛇)：它是一款爆破神器，可以对多种服务的账号和密码进行爆破，包括Web登录、数据库、SSH、FTP等服务，支持Linux、Windows、Mac平台安装，其中Kali Linux中自带Hydra。

    使用方法：hydra -l 主机名字典 -P 密码字典的绝对路径 -t 6 -vV ip地址 ssh

    -l 为要进行密码爆破的目标主机用户名 

    -P为指定字典的绝对路径

    -t 为指定爆破线程

![image-20210622214545927](http://cdn.mxrblog.cn/image-20210622214545927.png)

- 得到登录名tom, 密码parturient，登陆后执行ls，发现flag3，

```
vi flag3.txt
```

![image-20210622220518612](http://cdn.mxrblog.cn/image-20210622220518612.png)

- 关键词： jerry，su，于是可以思考提权的问题，但是经过操作后发现我正处于以一个受限制的bash，也就是rbash，在网上也搜索了许多绕过rbash的方法，这里使用vi编辑器绕过

![image-20210622221713795](http://cdn.mxrblog.cn/image-20210622221713795.png)

```
vi flag3.txt
```



![image-20210622222702211](http://cdn.mxrblog.cn/image-20210622222702211.png)

![image-20210622222754224](http://cdn.mxrblog.cn/image-20210622222754224.png)

![image-20210622233544229](http://cdn.mxrblog.cn/image-20210622233544229.png)

- 进入 /home/jerry , 发现flag4

![image-20210622222953583](http://cdn.mxrblog.cn/image-20210622222953583.png)

- flag4中说了还有最后一个flag

![image-20210622223052601](http://cdn.mxrblog.cn/image-20210622223052601.png)

- 由flag4知道，git提权可以使用，但是使用git提权的前提是可以使用sudo -l 命令，tom账户不能使用该命令，于是切换到jerry账户，切换的时候可以进入 /bin目录下，使用  ./su jerry  进行切换

![image-20210623001139313](http://cdn.mxrblog.cn/image-20210623001139313.png)

```
sudo git -p help config
# 末行模式下书写
!/bin/sh
```

- 进入root目录，发现最后一个flag

![image-20210623001511155](http://cdn.mxrblog.cn/image-20210623001511155.png)

### 三、总结

- wpscan的使用
- cewl生成密码字典
- hydra的使用
- 绕过rbash
- git提权
- 注意ssh端口可能不在22号
- 配置hosts文件
