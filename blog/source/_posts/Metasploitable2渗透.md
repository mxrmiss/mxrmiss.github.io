# Metasploitable2渗透

- 扫描主机位置, 查看开放端口

![image-20210710123742004](http://cdn.mxrblog.cn/image-20210710123742004.png)

```
┌──(kali㉿kali)-[~]
└─$ sudo nmap -sV 192.168.1.6 --top-ports 10 -A
Starting Nmap 7.91 ( https://nmap.org ) at 2021-07-09 14:58 EDT
Nmap scan report for 192.168.1.6
Host is up (0.00075s latency).

PORT     STATE  SERVICE       VERSION
21/tcp   open   ftp           vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 192.168.1.8
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp   open   ssh           OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
23/tcp   open   telnet        Linux telnetd
25/tcp   open   smtp          Postfix smtpd
|_smtp-commands: metasploitable.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, 
|_ssl-date: 2021-07-09T18:59:03+00:00; -2s from scanner time.
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|     SSL2_RC2_128_CBC_WITH_MD5
|_    SSL2_RC4_128_WITH_MD5
80/tcp   open   http          Apache httpd 2.2.8 ((Ubuntu) DAV/2)
|_http-server-header: Apache/2.2.8 (Ubuntu) DAV/2
|_http-title: Metasploitable2 - Linux
110/tcp  closed pop3
139/tcp  open   netbios-ssn   Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
443/tcp  closed https
445/tcp  open   netbios-ssn   Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
3389/tcp closed ms-wbt-server
MAC Address: 00:0C:29:1B:98:84 (VMware)
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6
OS details: Linux 2.6.9 - 2.6.33
Network Distance: 1 hop
Service Info: Host:  metasploitable.localdomain; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 1h19m57s, deviation: 2h18m33s, median: -2s
|_nbstat: NetBIOS name: METASPLOITABLE, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: metasploitable
|   NetBIOS computer name: 
|   Domain name: localdomain
|   FQDN: metasploitable.localdomain
|_  System time: 2021-07-09T14:58:55-04:00
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)

TRACEROUTE
HOP RTT     ADDRESS
1   0.75 ms 192.168.1.6

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.21 seconds
                                 
```

- 查看web

![image-20210710124202310](http://cdn.mxrblog.cn/image-20210710124202310.png)

- 使用samba3.0漏洞

![image-20210710130933449](http://cdn.mxrblog.cn/image-20210710130933449.png)

![image-20210710131242678](http://cdn.mxrblog.cn/image-20210710131242678.png)

- 使用vsftpd 2.3.4漏洞

![image-20210710132229878](http://cdn.mxrblog.cn/image-20210710132229878.png)

![image-20210710132248507](http://cdn.mxrblog.cn/image-20210710132248507.png)

![image-20210710132309804](http://cdn.mxrblog.cn/image-20210710132309804.png)

