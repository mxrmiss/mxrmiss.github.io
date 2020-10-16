---
title: 网络编程
date: 2020/10/16 
comments: ture
categories:
- python
tags:
- 网络编程
---



# python网络编程

### 一、客户端

#### TCP客户端

<!--more-->

```python
import socket

# 建立变量：目标主机和目标端口
host = ***
port = ***

# 建立一个socket对象
client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 连接客户端
# connect内的是一个元组，代表的是一个具体的地址
client.connect((host, port))

# 发送数据
client.send("hello, world!")

# 接受数据
# 最大接受1024字节数据
response = client.recv(1024)

print(response)

# 关闭套接字
client.close()
```

#### UDP客户端

```python
import socket

# 建立变量：目标主机和目标端口
host = ***
port = ***

# 建立一个socket对象
client = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

# 连接客户端
# connect内的是一个元组，代表的是一个具体的地址
client.connect((host, port))

# 发送数据
client.sendto("hello, world!", (host, port))

# 接收数据
# 最大接受1024字节数据
data, addr = client.recvfrom(1024)

print(data)

# 关闭套接字
client.close()
```



