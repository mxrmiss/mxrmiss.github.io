### Nikto简介

基于perl语言开发的web页面扫描器。其特点扫描全面，速度快。

### nikto常用命令



|                        |                                         |
| :--------------------- | :-------------------------------------: |
| \-upodate              |             升级，更新插件              |
| \-host                 |               扫描目标UR                |
| \-id username:password |              http认证接口               |
| \-list-plugins         |           列出所有可用的插件            |
| \-evasion              | IDS/IPS逃避技术（实例演示里有详细信息） |
| \-port                 |           指定端口（默认80）            |
| -ssl                   |                 使用SSL                 |
| \-useproxy             |              使用http代理               |
| \-vhost  域名          |       当一个IP拥有多个网站时 使用       |



### nikto交互参数（扫描过程中使用）

|      |                  |
| :--- | :--------------: |
| 空格 | 报告当前扫描状态 |
| v    |   显示详细信息   |
| d    |   显示调试信息   |
| e    | 显示http错误信息 |
| p    |   显示扫描进度   |
| r    |  显示重定向信息  |
| c    |    显示cookie    |
| a    | 显示身份认证过程 |
| q    |     退出程序     |
| N    |  扫描下一个目标  |
| P    |     暂停扫描     |



### 实例演示

**1.扫描单个地址时**

nikto   -host  http://192.168.3

       ![](https://img-blog.csdnimg.cn/20200508150110280.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtbGlfbmc=,size_16,color_FFFFFF,t_70)

**2.扫描多个地址时**

nikto  -host   url.txt

![](https://img-blog.csdnimg.cn/20200508150130650.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtbGlfbmc=,size_16,color_FFFFFF,t_70)

**3.扫描https网站**

nikto   -host   www.baidu.com   -ssl   -port 443

![](https://img-blog.csdnimg.cn/20200508150147671.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtbGlfbmc=,size_16,color_FFFFFF,t_70)

**4.使用代理进行扫描**

nikto   -host  https://www.baidu.com -ssl   -useproxy  http;//127.0.0.1:8080（没有无法演示，若以后有条件补上）。

**5.使用LibWhisker绕过IDS的检测（10个参数 1-8、A、B）**

              1 随机URI编码（非utf-8）

              2 自选择路径（/. /）

              3 过早结束的URL

              4 使用长随机字符串

              5 使用假参数

              6 使用tab作为命令的分隔符

              7 更改URL的大小写

              8 使用windows的命令分隔符"\\"

              A 使用回车0x0d作为请请求分隔符

              B 使用二进制0x0b作为请请求分隔符      

niketo -host www.baidu.com -ssl  -port 443    -evasion 1358

![](https://img-blog.csdnimg.cn/20200508151222711.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtbGlfbmc=,size_16,color_FFFFFF,t_70)

### 修改配置信息

**1.将user-agent:nikto改为其他浏览器标识，避免被识别出来。** 

 vi /etc/nikto.conf

![](https://img-blog.csdnimg.cn/20200508151959421.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtbGlfbmc=,size_16,color_FFFFFF,t_70)

**2.如果是基于表单的认证，必须要使用cookie，将原来的cookie注释，按照原来的格式写入你的cookie。** 

STATIC-COOKIE = "cooke1=cookie value1"; "cookie2=value2"； 

![](https://img-blog.csdnimg.cn/20200508151928619.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtbGlfbmc=,size_16,color_FFFFFF,t_70)

