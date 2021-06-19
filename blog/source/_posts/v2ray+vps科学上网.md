

# v2ray+vps科学上网

## [服务端]([V2ray多合一脚本，支持VMESS+websocket+TLS+Nginx、VLESS+TCP+XTLS、VLESS+TCP+TLS等组合 - V2ray XTLS黑科技 (v2xtls.org)](https://v2xtls.org/v2ray多合一脚本，支持vmesswebsockettlsnginx、vlesstcpxtls、vlesstcptls等组合/))

```
配置：
搬瓦工vps
centos-7-x86_64
一个可用的并且指向该vps的域名
```

- v2ray VLESS+TPC+XTLS一键脚本

```
bash <(curl -sL https://s.hijk.art/v2ray.sh)
```

[![2940pj.jpg](https://z3.ax1x.com/2021/05/26/2940pj.jpg)](https://imgtu.com/i/2940pj)

```
VMESS，即最普通的V2ray服务器，没有伪装，也不是VLESS
VMESS+KCP，传输协议使用mKCP，VPS线路不好时可能有奇效
VMESS+TCP+TLS，带伪装的V2ray，不能过CDN中转
VMESS+WS+TLS，即最通用的V2ray伪装方式，能过CDN中转，推荐使用
VLESS+KCP，传输协议使用mKCP
VLESS+TCP+TLS，通用的VLESS版本，不能过CDN中转，但比VMESS+TCP+TLS方式性能更好
VLESS+WS+TLS，基于websocket的V2ray伪装VLESS版本，能过CDN中转，有过CDN情况下推荐使用
VLESS+TCP+XTLS，目前最强悍的VLESS+XTLS组合，强力推荐使用（但是支持的客户端少一些）
trojan，轻量级的伪装协议
trojan+XTLS，trojan加强版，使用XTLS技术提升性能
```

- 建议选配置4，一路默认就好，在最后完成时将vmess链接和其他配置信息复制保存，以便在客户端连接时方便使用

## 客户端

- GitHub上搜索并且下载v2ray客户端，建议先下载windows版本进行链接测试，再下载Linux或其他版本
- windows客户端下为图形化界面，点击左下方·的导入或者新建，进入新页面后按照要求填写即可（最好使用导入选项，将之前在服务器上保存的配置信息中的链接粘贴到里面即可），双击链接即可科学上网
- Linux客户端建议直接使用终端下的客户端，进入GitHub下载相应版本后解压到合适的目录下，然后配置其中的config.json文件,可以将之前windows客户端的config.json导出，然后替换掉Linux客户端原有的那个文件，配置好之后记得把vpoint_vmess_feedom.json文件删除。最后一步需要到系统的网络设置下手动配置代理，socks和http代理的配置在config.json文件里面都有