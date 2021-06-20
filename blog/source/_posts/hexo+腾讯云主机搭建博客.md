---
title: hexo＋腾讯云主机搭建博客
date: 2020-09-14 14:06
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_6.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: blog
tags: [hexo, blog]   # 可以是数组
top: false
---


## hexo＋腾讯云主机搭建博客

- [参考链接1](https://zhuanlan.zhihu.com/p/120322118)
- [参考链接2](https://www.jianshu.com/p/31eb5c754c01)
- [参考链接3](https://zhuanlan.zhihu.com/p/60578464)

+ 说明：不建议用hexo在云主机上搭建博客，感觉多此一举，建议hexo+github， wordpress+云主机（宝塔界面更快哦）

<!--more-->

### 一．Git安装

- 我使用的云主机是centos8系统的，之前也用过ubuntu18系统，但是感觉效果不怎的，可能试的次数比较少吧
- 先检查是否有git

```
git --version
```

- 没有则安装，旧的卸载重装

```
＃卸载旧的git
yum remove git
# 安装依赖库
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel
# 将git下载安装到/usr/local/src目录
cd /usr/local/src
wget http://ftp.ntu.edu.tw/software/scm/git/git-2.26.0.tar.gz
tar -zvxf git-2.26.0.tar.gz
# 进入目录
cd git-2.26.0
# 编译执行
make prefix=/usr/local/git all
# 安装 git 到 /usr/local/git 目录下
make prefix=/usr/local/git install

```

### 二、配置Git环境变量

- 打开环境变量配置文件进行配置

```
vim /etc/profile

#添加以下内容
PATH=$PATH:/usr/local/git/bin   # git 的目录
export PATH

# 使配置的环境变量生效
source /etc/profile
```

- 验证git安装成功

```
git --version
```

### 三、云主机与GitHub交换SSH密匙

- Git初始化

```
# 设置github昵称
git config --global user.name 'mxrmiss'
# 设置github邮箱
git config --global user.email 'heroli520@outlook.com'
```

- git status 无法显示中文

```
git config --global core.quotepath false
```

- 创建ssh密匙

```
ssh-keygen -t rsa -C "GitHub 邮箱"
```

- 输出 id_rsa.pub 内容并复制到GitHub中的setting页面上

```
cat id_rsa.pub
```

[![syONLT.jpg](https://s3.ax1x.com/2021/01/18/syONLT.jpg)](https://imgchr.com/i/syONLT)

- ssh -T [git@github.com](mailto:git@github.com) 输入此命令，显示successful表示成功

### 四、node.js安装

1. node.js下载

```
cd /opt
wget https://npm.taobao.org/mirrors/node/latest-v15.x/node-v15.0.1-linux-x64.tar.xz  # 下载二进制安装包
```

2. node.js部署

```
cd /opt
tar xf node-v10.16.0-linux-x64.tar.xz 
ln -s node-v10.16.0-linux-x64 nodejs
nodejs -> node-v10.16.0-linux-x64
```

3. 版本信息

```
cd /opt/nodejs/bin
./node -v
会看到 v10.16.0
```

4. 创建软连接

```
ln -s /opt/nodejs/bin/node /usr/local/bin/node
ln -s /opt/nodejs/bin/npm  /usr/local/bin/npm

# 验证
node -v
```

### 五、hexo 部署

1. 安装hexo

```
npm install hexo-cli -g 
```

2. 采用软连接的方式将hexo添加入全局变量

```
ln -s /opt/nodejs/lib/node_modules/hexo-cli/bin/hexo /usr/local/bin/hexo
```

3. 将自己GitHub中存储的hexo博客ssh下载到主机中

### 六、使用Nginx部署Hexo站点

1. Nginx安装

```
yum install -y nginx
```

2. 配置Nginx

```
nginx -t
```

3. 使用vim打开nginx.conf文件

- 注意：有时该配置文件会不同于下面显示的，请参考网上其他教程从官网下载

```
vim /etc/nginx/nginx.conf
```

[![s6S4k8.png](https://s3.ax1x.com/2021/01/18/s6S4k8.png)](https://imgchr.com/i/s6S4k8)

4. 启动Nginx

```
systemctl start nginx.service
```

5. 重启Nginx

```
systemctl restart nginx.service
```

### 七、配置本地hexo

- 博客根目录_config下增加

```
deploy:
    type: git  (必须是git)
    repo: root@***(服务器外网ip):#仓库地址
    branch: master   
```

- 进入自己的hexo博客目录
- hexo clean
- hexo g
- hexo d
- 搜索自己的域名便可访问
