发表于 2019-02-22 | 更新于: 2019-02-22 | 分类于 [TOOLS](https://franci4.github.io/categories/TOOLS/) ， [WEB应用程序](https://franci4.github.io/categories/TOOLS/WEB%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F/) ， [WEB爬行](https://franci4.github.io/categories/TOOLS/WEB%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F/WEB%E7%88%AC%E8%A1%8C/) | 阅读次数:

字数统计: 2.4k | 阅读时长 ≈ 10

DIRB是一个Web内容扫描程序。它查找现有（和/或隐藏）Web对象。它基本上是通过对Web服务器启动基于字典的攻击并分析响应来实现的。

DIRB附带了一组预先配置的攻击单词列表，方便使用，但您可以使用自定义单词列表。DIRB有时也可以用作经典的CGI扫描仪，但记住的是内容扫描程序而不是漏洞扫描程序。

DIRB的主要目的是帮助进行专业的Web应用程序审计。特别是在安全相关的测试中。它涵盖了经典Web漏洞扫描程序未涵盖的一些漏洞。DIRB查找其他通用CGI扫描仪无法查找的特定Web对象。它不会搜索漏洞，也不会查找可能存在漏洞的Web内容。

```plain
root@kali:~# dirb

-----------------
DIRB v2.22
By The Dark Raver
-----------------

dirb <url_base> [<wordlist_file(s)>] [options]

========================= NOTES =========================
 <url_base> : Base URL to scan. (Use -resume for session resuming)
 <wordlist_file(s)> : List of wordfiles. (wordfile1,wordfile2,wordfile3...)

======================== HOTKEYS ========================
 'n' -> Go to next directory.
 'q' -> Stop scan. (Saving state for resume)
 'r' -> Remaining scan stats.

======================== OPTIONS ========================
 -a <agent_string> : Specify your custom USER_AGENT.
 -b : Use path as is.
 -c <cookie_string> : Set a cookie for the HTTP request.
 -E <certificate> : path to the client certificate.
 -f : Fine tunning of NOT_FOUND (404) detection.
 -H <header_string> : Add a custom header to the HTTP request.
 -i : Use case-insensitive search.
 -l : Print "Location" header when found.
 -N <nf_code>: Ignore responses with this HTTP code.
 -o <output_file> : Save output to disk.
 -p <proxy[:port]> : Use this proxy. (Default port is 1080)
 -P <proxy_username:proxy_password> : Proxy Authentication.
 -r : Don't search recursively.
 -R : Interactive recursion. (Asks for each directory)
 -S : Silent Mode. Don't show tested words. (For dumb terminals)
 -t : Don't force an ending '/' on URLs.
 -u <username:password> : HTTP Authentication.
 -v : Show also NOT_FOUND pages.
 -w : Don't stop on WARNING messages.
 -X <extensions> / -x <exts_file> : Append each word with this extensions.
 -z <millisecs> : Add a milliseconds delay to not cause excessive Flood.

======================== EXAMPLES =======================
 dirb http://url/directory/ (Simple Test)
 dirb http://url/ -X .html (Test files with '.html' extension)
 dirb http://url/ /usr/share/dirb/wordlists/vulns/apache.txt (Test with apache.txt wordlist)
 dirb https://secure_url/ (Simple Test with SSL)
```

攻击中，common.txt被设置为目录遍历的默认单词列表  
使用字典文件（/usr/share/wordlists/dirb/common.txt）扫描Web服务器（[http://192.168.126.130/）以获取目录：](http://192.168.126.130/%EF%BC%89%E4%BB%A5%E8%8E%B7%E5%8F%96%E7%9B%AE%E5%BD%95%EF%BC%9A)  
dirb 目标 字典路径  

```plain
root@kali:/# dirb http://192.168.126.130 /usr/share/wordlists/dirb/common.txt

-----------------
DIRB v2.22
By The Dark Raver
-----------------

START_TIME: Fri Feb 22 16:33:46 2019
URL_BASE: http://192.168.126.130/
WORDLIST_FILES: /usr/share/wordlists/dirb/common.txt

-----------------

GENERATED WORDS: 4612
```

在很多情况下我们需要在目标服务器上提取特定扩展的目录，然后我们可以使用dirb扫描的-X参数。此参数接受文件扩展名，然后在目标服务器或计算机上搜索给定的扩展文件。  
dirb 目标 -X .php  
上面的命令将提取与php扩展相关的所有目录路径，如下图所示  

```plain
root@kali:/# dirb http://192.168.126.130 -X .php

-----------------
DIRB v2.22
By The Dark Raver
-----------------

START_TIME: Fri Feb 22 17:12:16 2019
URL_BASE: http://192.168.126.130/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
EXTENSIONS_LIST: (.php) | (.php) [NUM = 1]

-----------------

GENERATED WORDS: 4612

---- Scanning URL: http://192.168.126.130/ ----

-----------------
END_TIME: Fri Feb 22 17:12:24 2019
DOWNLOADED: 4612 - FOUND: 0
```

出于记录维护，更好的可读性和未来参考的目的，我们将dirb扫描的输出保存到文件中。为此，我们将使用dirb扫描的参数-o，我们可以在文本文件中保存dirb扫描的输出。  
dirb 目标 -o /位置/自定义名称.txt  
上面的命令将在枚举目录的桌面上生成.txt文件。  

```plain
root@kali:/# dirb http://192.168.126.130 -o /root/123.txt

-----------------
DIRB v2.22
By The Dark Raver
-----------------

OUTPUT_FILE: /root/123.txt
START_TIME: Fri Feb 22 17:15:22 2019
URL_BASE: http://192.168.126.130/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612
```

`root@kali:~# cat /root/123.txt`  
现在我们已经成功执行了命令，现在让我们遍历该位置以确保输出是否已保存在文件上。在这种情况下，我们的输出位置是/root/123.txt。

Status-Code元素是一个3位整数，其中Status-Code的第一个数字定义了响应类，后两个数字没有任何分类角色。在这次攻击中，我们在代码302上使用-N参数，如下所示。  
dirb 目标 -N 302  
您可以从给定的屏幕截图中了解到dirb扫描忽略的是NOT FOUND代码，即302。  

```plain
root@kali:~# dirb http://192.168.126.130 -N 302

-----------------
DIRB v2.22
By The Dark Raver
-----------------
```

正常dirb扫描期间，一些页面会生成警告; dirb扫描会跳过遇到任何警告的目录。  
在进行非常深入和冗长的扫描时，我们希望dirb扫描不要避免这些警告并进行深入扫描，因此我们使用dirb扫描的-w参数。  
dirb 目标 -w  
您可以观察到突出显示的目录/ dev / shell即使在默认扫描中缺少警告消息之后也会被枚举  

```plain
root@kali:~# dirb http://192.168.126.130 -w

-----------------
DIRB v2.22
By The Dark Raver
-----------------
```

在不同场景下工作时，我们遇到的某些环境无法处理由dirb扫描创建的洪水，因此在这些环境中，我们将扫描延迟一段时间非常重要。使用dirb扫描的-z参数可以轻松完成此操作。在此参数中，时间以毫秒为单位提供。就像我们给出的例子中所示，我们给dirb延迟了100秒。  
dirb 目标 -z 100  

```plain
root@kali:~# dirb http://192.168.126.130 -z 100

-----------------
DIRB v2.22
By The Dark Raver
-----------------

START_TIME: Fri Feb 22 18:11:47 2019
URL_BASE: http://192.168.126.130/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
SPEED_DELAY: 100 milliseconds
```

默认情况下，dirb扫描以递归方式扫描目录。这意味着它扫描目录，然后遍历该目录以扫描更多子目录。但在某些情况下，如果时间不够，我们会将dirb设置为不递归扫描。这可以使用-r参数来实现。  

```plain
root@kali:~# dirb http://192.168.126.130 -r

-----------------
DIRB v2.22
By The Dark Raver
-----------------

START_TIME: Fri Feb 22 18:13:13 2019
URL_BASE: http://192.168.126.130/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
OPTION: Not Recursive
```

404错误是HTTP状态代码，表示您尝试在网站上访问的页面无法在其服务器上找到。404 Not Found错误消息经常由各个网站自定义。在某些情况下，我们也需要找到404页面，默认情况下dirb会跳过。要查找这些页面，我们将使用-v参数。  
dirb 目标 -v  
从下面中你可以观察到它还提取了所有那些与404错误相关的目录。  

```plain
root@kali:~# dirb http://192.168.126.130 -v

-----------------
DIRB v2.22
By The Dark Raver
-----------------

START_TIME: Fri Feb 22 18:14:17 2019
URL_BASE: http://192.168.126.130/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
OPTION: Show Not Existent Pages
```

通过将-X参数与具有特定扩展名的目标URL（例如.php）一起使用，它枚举所有扩展名为.php的文件或目录，但使用带有特定扩展名的-H参数，例如.php以及目标URL它将枚举用php命名的所有文件或目录，如下面给出的图像所示。  
dirb 目标 -H .PHP  

```plain
root@kali:~# dirb http://192.168.126.130 -H .php

-----------------
DIRB v2.22
By The Dark Raver
-----------------

START_TIME: Fri Feb 22 18:16:36 2019
URL_BASE: http://192.168.126.130/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
ADDED_HEADERS:
--
.php
--
```

HTTP身份验证/身份验证机制都基于使用401状态代码和WWW- Authenticate 响应头。最广泛使用的 HTTP身份验证 机制是 Basic。客户端将用户名和密码作为未加密的base64编码文本发送。

因此，为了在dirb的帮助下绕过这种身份验证，我们使用了以下命令：  
dirb 目标 -u test:test  
结果，它显示了测试的状态码200：目标URL上的测试和授权凭证。  

```plain
root@kali:~# dirb http://192.168.126.130/admin/admin.asp -u test:test

-----------------
DIRB v2.22
By The Dark Raver
-----------------

START_TIME: Fri Feb 22 18:22:47 2019
URL_BASE: http://192.168.126.130/admin/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
AUTHORIZATION: test:test
```

使用-p选项可以将代理URL用于所有请求，默认情况下它适用于端口1080.正如您所看到的，在Web浏览器中探索目标网络IP时，它会显示“访问禁止错误”，这意味着此网页是在一些代理后面运行。

我们可以使用DIRB来测试特定类型的Web技术中的特定易受攻击对象。每种Web技术都有不同的漏洞。他们并非都是一样的。DIRB可以帮助我们查找特定技术特定的特定易受攻击对象。

在Kali，DIRB有特定的单词列表来搜索这些易受攻击的经常隐藏的对象。您可以在以下位置找到它们  

```plain
root@kali:/# cd /usr/share/dirb/wordlists/vulns/
root@kali:/usr/share/dirb/wordlists/vulns# ls
apache.txt             fatwire.txt    jboss.txt    sap.txt         weblogic.txt
axis.txt               frontpage.txt  jersey.txt   sharepoint.txt  websphere.txt
cgis.txt               hpsmh.txt      jrun.txt     sunas.txt
coldfusion.txt         hyperion.txt   netware.txt  tests.txt
domino.txt             iis.txt        oracle.txt   tomcat.txt
fatwire_pagenames.txt  iplanet.txt    ror.txt      vignette.txt
```

正如您在上面所看到的，每个要测试的特定漏洞都有许多文件列表。如果您的Web服务器是Apache，并且您想测试它，请使用apache.txt

现在，DIRB将使用该Apache列表来查找已知的易受攻击对象。如果它找到任何，那么您可以采取下一步并寻找漏洞，以利用它在Exploit-DB，Metasploit，SecurityFocus或Web上的任何其他许多漏洞利用源。当然，您必须使用与网站中使用的技术相适应的词汇表。例如，如果它是Windows IIS服务器，则应使用iis.txt wordlist。