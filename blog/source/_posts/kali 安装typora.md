---
title: kali安装typroa
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_8.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: kali
tags: [kali, typroa]   # 可以是数组
top: false
---

## kali 安装typora

### 一、安装

- 官网下载文件解压，并移动到 /opt 文件夹下



### 二、赋权

- 在typora目录的bin文件夹下执行命令

```
./typora
```

- 会报错[7442:0707/173355.682906:FATAL:setuid_sandbox_host.cc(157)] The SUID sandbox helper binary was found, but is not configured correctly. Rather than run without sandboxing I’m aborting now. You need to make sure that /opt/Typora-linux-x64/chrome-sandbox is owned by root and has mode 4755.

- 执行命令

```
sudo chown root chrome-sandbox
sudo chgrp root chrome-sandbox
sudo chmod 4755 chrome-sandbox
```



### 三、添加快捷方式

- vim typora.desktop

```
[Desktop Entry]
Name=Typora
Exec=/opt/Typora-linux-x64/Typora
Type=Application
Icon=/opt/Typora-linux-x64/resources/app/asserts/icon/icon_512x512.png
```

- 将快捷方式复制到/usr/share/applications

```
sudo cp typora.desktop /usr/share/applications/
```

- 关机重启
