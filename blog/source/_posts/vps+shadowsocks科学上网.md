# vps+shadowsocks科学上网

- [参考链接]([关于 VPS 和翻墙的那些事 - StarryVoid - Blog](https://blog.starryvoid.com/archives/76.html))
- [bbr加速参考链接]([一键安装最新内核并开启 BBR 脚本 | 秋水逸冰 (teddysun.com)](https://teddysun.com/489.html))

## 一、购买VPS云主机

- 科学上网需要一台海外的vps，我使用的是vultr的日本vps，对于其他vps不作比较和评论
- 登陆后修改密码，更新系统并且安装vim,我的系统是Centos 7x64

## 二、安装shadowsocks

```
yum -y install epel-release
yum -y install snapd
systemctl enable --now snapd.socket
ln -s /var/lib/snapd/snap /snap
source /etc/profile
snap install core
snap install shadowsocks-libev --edge
```

## 三、配置shadowsocks文件

- shadowsocks配置文件需要手动创建
- 配置文件目录 

```
/var/snap/shadowsocks-libev/common/etc/shadowsocks-libev/config.json
```

- 执行命令

```
vim /var/snap/shadowsocks-libev/common/etc/shadowsocks-libev/config.json
```

- 复制并参考以下配置文件：

```
{
     "server":["::0","0.0.0.0"],
     "server_port":8388,
     "encryption_method":"chacha20-ietf-poly1305",  //加密方式在网上搜索最新最安全的加密方式
     "password":"your_password", //密码换成自己的
     "mode":"tcp_only",  //限制处理协议，禁用 UDP 提高安全性
     "fast_open":false  //TCP 快速转发，禁用提高安全性
 }
```

## 四、防火墙配置

- 由于我使用的是centos 7，参考如下配置

```
firewall-cmd --add-service=8388/tcp --per
firewall-cmd --reload
```

## 五、开机自启

- 一切准备就绪时设置服务器上shadowsocks开机自启

```
systemctl start snap.shadowsocks-libev.ss-server-daemon.service

systemctl enable snap.shadowsocks-libev.ss-server-daemon.service
```

- 查看shadowsocks运行状态

```
systemctl status snap.shadowsocks-libev.ss-server-daemon.service
```

- 结果

```
snap.shadowsocks-libev.ss-server-daemon.service - Service for snap application shadowsocks-libev.ss-server-daemon
   Loaded: loaded (/etc/systemd/system/snap.shadowsocks-libev.ss-server-daemon.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2021-02-05 16:11:12 UTC; 2 weeks 0 days ago
 Main PID: 918 (ss-server)
   CGroup: /system.slice/snap.shadowsocks-libev.ss-server-daemon.service
           └─918 /snap/shadowsocks-libev/751/bin/ss-server
```

## 六、配置ss客户端

### win10安装

- github上自己撸出来ss客户端并且下载
- 在win10上不同位置解压安装包运行可能有得能成功或失败
- 在win10上启动后填写成如下：

[![yI0iFS.png](https://s3.ax1x.com/2021/02/20/yI0iFS.png)](https://imgchr.com/i/yI0iFS)

- 服务器地址改成自己的服务器地址，填写和服务器端ss相同的加密方式，注意填写代理端口
- 在任务栏中找到ss的小飞机图标，可能被放在了隐藏栏，注意找一下，右击小飞机图标，选择系统代理，使用全局模式，注意，这里不使用PAC模式，比较卡，具体俩个模式有什么区别自行百度
- 注意：在全局模式下win10自带的outlook邮箱或者软件商店等不能使用，建议下载网易邮箱大师，亲测全局模式下可用，如果在使用PC其他软件无法上网的时候可以尝试一下关掉全局代理
- 大功告成

### linux安装

- 在GitHub上找到压缩包并解压安装，图形版的ss客户端我没有试过，但是我安装过终端下的ss，并且还要搭配终端下的代理软件proxychains，虽然成功打开了foxfire并且在youtube上浏览了一番，但是无法在终端利用proxychains打开google-chrome，于是最终放弃，具体怎么做可以具体百度，不具体描述

## 七、安装代理插件

- 可能我们不想使用小飞机的全局模式，因为在访问国内网站时会变得慢，这时候我们可以使用浏览器代理插件SwitchyOmega

- 建议在microsoft edge上安装插件，另下载google-chrome进行科学上网，当然，foxfire（火狐浏览器）自带该插件功能
- 该插件功能请自行摸索

## 八、bbr加速

- 听说bbr加速可以让自己的海外vps充分发挥自己的带宽，虽然我觉得没啥区别，但是我还是安装了一下

```
wget --no-check-certificate -O /opt/bbr.sh https://github.com/teddysun/across/raw/master/bbr.sh
chmod 755 /opt/bbr.sh
/opt/bbr.sh
```

- 安装完成后按照脚本提示重启即可
- 进行验证：

```
sysctl net.ipv4.tcp_available_congestion_control
返回值
net.ipv4.tcp_available_congestion_control = bbr cubic reno
或
net.ipv4.tcp_available_congestion_control = reno cubic bbr

sysctl net.ipv4.tcp_congestion_control
返回值
net.ipv4.tcp_congestion_control = bbr

sysctl net.core.default_qdisc
返回值
net.core.default_qdisc = fq

lsmod | grep bbr
返回值
tcp_bbr                20480  3
//这个返回值可能没有，我的vps就是这样
```

## 九、虚拟机走物理机代理

- 在linux系统上安装ss客户端有许多弊端，一旦安装了proxychains可能会导致无法科学上网甚至无法正常上网，并且也无法通过命令行打开google-chrome，并且xfce桌面系统很精简（奇葩），不能进行代理的手动配置，如果需要必须将桌面系统变成gnome
- 我们将linux安装在虚拟机上，通过对物理机的防火墙进行配置：

[![yI6bnJ.png](https://s3.ax1x.com/2021/02/20/yI6bnJ.png)](https://imgchr.com/i/yI6bnJ)

- 打开防火墙，点击入站规则，新建规则，建立TCP端口规则，开放端口1080
- 设置虚拟机为桥接模式
- 打开虚拟机，如果使用的linux桌面系统不支持对代理进行手动配置，可以使用foxfire进行浏览器的代理配置

### 终端走代理

- 使用终端走代理可以使用proxychains
- 安装过程：

```
git clone https://github.com/rofl0r/proxychains-ng
cd proxychains-ng
./configure --prefix=/usr --sysconfdir=/etc

## 以下命令注意自己的权限是否够
make
make install
make install-config

cd
rm -rf proxychains-ng
```

- 更改proxychains配置文件

```
vim /etc/proxychains.conf
```

- dynamic_chain这行代码前面原本有注释的符号去掉，让它生效
- 跳转到文件的最尾端，删除上面已有的socks4一行，新添一行如图所示，注意ip地址是物理机的地址，后面的端口号是物理机防火墙上开放的端口号

[![yIgkMF.png](https://s3.ax1x.com/2021/02/20/yIgkMF.png)](https://imgchr.com/i/yIgkMF)

- 在任务栏中右击小飞机图标，点击‘允许其他设备接入’
- proxychains + 命令 即可进行终端的科学上网