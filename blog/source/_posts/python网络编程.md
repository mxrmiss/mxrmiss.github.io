---
title: python网络编程
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_16.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: python
tags: [python, 网络编程, 套接字]   # 可以是数组
top: false
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

### 二、服务器

```python
import socket
import threading


# bind_id留空的意思是本地IP 127.0.0.1
bind_id = ""  
bind_port = 9999

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind((bind_id, bind_port))
server.listen(5)
print('[*] Listening on {}:{}' .format(bind_id, bind_port))

def handle_client(client_socket):
    request = client_socket.recv(1024)
    print('[*] Received: {}' .format(request))
    send_it = "I love you"
    client_socket.send(send_it.encode())
    client_socket.close()


client, address = server.accept()
print("[*] Accepted connection from: {}:{}" .format(client, address))
client_handler_t1 = threading.Thread(target=handle_client, args=(client, ))
client_handler_t1.start()
```

### 实战演练

#### 服务器（采取多线程）

```python
import socket
import threading
# from struct import pack


class ThreadSocket(object):
    def __init__(self, host, port):
        self.host = host
        self.port = port
        self.sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        self.sock.bind((self.host, self.port))
        # 超时收取，超过5秒没收到客户端回应自动断开连接
        # self.sock.setsockopt(socket.SOL_SOCKET, socket.SO_RCVTIMEO, pack('QQ', 5, 0))
        # 超时发送，超过5秒没将数据传送至客户端断开连接
        # self.sock.setsockopt(socket.SOL_SOCKET, socket.SO_SNDTIMEO, pack('QQ', 4, 0))

    def listen(self):
        self.sock.listen(5)
        '''
        在这里插入对Ctrl+C的控制
        '''
        # 循环连接，可以与多个客户端依次链接
        # while True:
        client, address = self.sock.accept()
        print('正在接收客户端信息...\n')
        # 超过30秒停止活动
        # client.settimeout(15)
        t1 = threading.Thread(target=self.handleClientRequest, args=(
            client, address))
        t1.start()

    def handleClientRequest(self, client, address):
        # 采取循环，使每次与客户端连接都可以进行多次对话
        while True:
            try:
                data = client.recv(1024)
                print('显示客户端信息：{}'.format(data))
                if data:
                    client.send(data)
                else:
                    print("Client has disconnected")
                    break
            except:
                client.close()


if __name__ == '__main__':
    server = ThreadSocket('', 9000)
    server.listen()

```



#### 客户端

```python
import socket

target_host = '127.0.0.1'
target_ip = 9000

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client.connect((target_host, target_ip))
send_data = input('输入你想说的：')
client.send(send_data.encode())
response = client.recv(4096)

print(response.decode() + 'love')
```



### 三、[socket常见选项之SO_REUSEADDR,SO_REUSEPORT](https://www.cnblogs.com/HKUI/p/11707170.html)

- 使端口被释放后可以立即被使用
