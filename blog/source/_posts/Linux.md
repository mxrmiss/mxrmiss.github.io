---
title: Linux
date: 2020/5/3 9:08:28
comments: ture
categories:
- linux
tags:
- Linux
---

[本笔记的友情链接](https://book.apeland.cn/details/189/)

## 常用目录介绍

1. boot    存放启动文件
2. dev     存放设备文件
3. etc     存放配置文件
4. home    普通用户家目录，以/home/$username的方式存放
5. media   移动存储自动挂载目录,以 /media/$device_name的方式来存放
6. mnt     手动挂载目录
7. opt     三方软件安装目录
8. proc    内存系统文件
9. root    管理员家目录
10. run     里面的东西是系统运行时需要的, 不能随便删除. 但是重启的时候应该抛弃. 下次系统运行时重新生成
11. srv     服务相关数据
12. sys     系统文件
13. tmp     临时文件夹
14. usr     存放库文件、文档、命令、用户数据等
15. var     日志存放lib     库文件
<!--more-->
- -echo相当于python中的print，即输出、打印的意思

## linux基本命令
### 命令[命令选项][参数]
​    {必选项}， [可选项]

1）清屏命令：clear      快捷键  ctrl+l
2）帮助命令：man
3）进入文件夹命令: cd  【change dir，cd理解为改变目录的意思】
4）列出当前目录内容命令：ls   [-a -l -d ] 【list】
5）显示主机名：hostname
6）显示日期时间：date [-s %F]
7）显示日历：cal
8）计算器：bc
9）重启命令：reboot     shutdown -r      init 6
10）注销命令：logout
11）关机命令：halt      shutdown -h         init 0
12) 显示当前操作系统和机器的信息: uname
13) 显示当前路径: pwd 【定位自己的位置】

-多个命令选项可以用一个 — 拼凑在一起，若命令产生了冲突，则以排最后的为主
-命令选项写全称时，前面加 — —



- 从命令行启动图形界面的默认图片查看器

  ​	运行命令：xdg-open filename.png

- 在命令行打开一个图片:

   1. apt install fim

   2. fim filename.png

      + fim是fbi的升级版

        [有关于fim的操作链接](http://www.itpro.net.cn/2019/12/linux)

- apt 与apt-get 的区别：apt-get 是 apt 的子集

- 更新安装包：apt upgrade



### 安装内核头文件

- 输入命令：apt-get install linux-headers-$(uname -r)或者直接敲apt-get install linux-headers-在这时候你按键盘上的tab键，找你本系统的头文件安装即可



### 关于Anaconda的安装

- 在清华镜像站下载Anaconda

- 安装后修改环境变量

  - sudo vim /etc/profile

  - 向里面写入  export PATH=$PATH:/home/balacksheep/anaconda3/bin:$PATH

       						%%blacksheep为用户名，应选择自己的路							径配置环境变量

- 安装后命令行使用时前面有base，代表安装成功，不需要去掉base，对使用无影响

- 更换anaconda的源，以清华源举例：

  - sudo vim ~/.condarc

  - 向里面写入：

    

    ```
    channels:
    https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
    https://mirrors.ustc.edu.cn/anaconda/cloud/conda-forge/
    https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
    defaults
    show_channel_urls: true
    ```
  
  


### 关于qq的安装问题

- 在kali系统中，输入wine，系统会自动提示你wine未安装，此时按照提示给的步骤来就行了
- 关于字体问题：qq安装好了后会发现有些字体变成了乱码，此时可以安装一些字体来解决

```
apt-get install xfonts-intl-chinese
apt-get install ttf-wqy-microhei
```

- 关于QQ用wine装好后找不到的问题
  + wine 默认被配置成home文件下的隐藏文件，cd .wine即可调出
  + 输入~/.wine/drive_c/Program Files (x86)/Tencent/QQ/Bin
  + 你会发现里面优QQ.exe文件，输入wine QQ 或wine QQ.exe即可调出QQ程序
  + 为了方便，可以把QQ/Bin 放到home文件下



### 关于vmware 虚拟机安装

1. 官网下载虚拟机最新版本
2. chmod -x 下载的文件
3. sudo bash 下载的文件
4. 之后就可以在电脑中找到虚拟机，并进行下一步的安装操作



### man 中文手册下载

1. Debian / Ubuntu安装

```
sudo apt update
sudo apt install manpages-zh
```

2. Arch Linux:

```
pacman -Syu
pacman -S man-pages-zh_cn man-pages-zh_tw
```

3. Red Hat / CentOS:

```
yum update
yum install man-pages-zh-CN
```

4. Fedora:

```
dnf update
dnf install man-pages-zh-CN
```



### vim命令

1. :set number 显示行号
2. :set 取消行号
3. xG:跳转到指定的第x行，G移动到文件末尾行
4. dd ：剪切一行， yy：复制一行
5. D：剪切一个字符，y：复制一个字符
6. p：粘贴
7. dxw：删除光标右边x个字符
8. dxh：删除光标左边x个字符
9. :x 删除一个字符
10. /string :查找字符 n:向上查找，N：向下查找
11. :范围 s/oldword/newword/g  字符替换 %s全文查找，g为global，全局替换

- vim规则总结

（1）一般是操作字符+被操作的量

（2）$ 为结尾， 0 为开头

- vim简单配置：

  

  ```c
  "===================
  "适合自己用的vimrc配置文件
  "===================
  
  "设置编码
  set fileencodings=utf-8,ucs-bom,gb18030,gbk,gb2312,cp936
  set termencoding=utf-8
  set encoding=utf-8
  
  "保存.vimrc文件时自动重启加载，即让此文件立即生效
  autocmd BufWritePost $MYVIMRC source $MYVIMRC
  
  "语法高亮
  syntax on
  
  "设置ruler会在右下角显示光标所在的行号和列号,不方便查看,改成设置状态栏显示内容
  "set ruler
  
  "设置状态行显示的内容. %F: 显示当前文件的完整路径. %r: 如果readonly,会显示[RO]
  "%B: 显示光标下字符的编码值,十六进制. %l:光标所在的行号. %v:光标所在的虚拟列号.
  "%P: 显示当前内容在整个文件中的百分比. %H和%M是strftime()函数的参数,获取时间.
  set statusline=%F%r\ [HEX=%B][%l,%v,%P]\ %{strftime(\"%H:%M\")}
  
  "显示行号
  set nu "等同于 set number
  
  "突出显示当前行
  set cursorline "等同于 set cul
  
  "突出显示当前列
  set cursorcolumn "等同于 set cuc
  
  "共享剪贴板  
  set clipboard+=unnamed 
  
  "从不备份  
  set nobackup
  
  "自动保存
  set autowrite
  
  "隐藏工具栏
  "set guioptions-=T
  "隐藏菜单栏
  "set guioptions-=m
  
  "高亮显示所有搜索到的内容.后面用map映射快捷键来方便关闭当前搜索的高亮.
  "set hlsearch
  
  "光标立刻跳转到搜索到内容
  "set incsearch
  
  "搜索到最后匹配的位置后,再次搜索不回到第一个匹配处
  "set nowrapscan
  
  "去掉输入错误时的提示声音
  set noeb
  
  " 默认按下Esc后,需要等待1秒才生效,设置Esc超时时间为100ms,尽快生效
  set ttimeout
  set ttimeoutlen=100
  
  "在处理未保存或只读文件的时候，弹出确认
  set confirm
  
  "让Backspace键可以往前删除字符.
  "Debian系统自带的vim版本会加载一个debian.vim文件,默认已经设置这一项,
  "可以正常使用Backspace键.如果使用自己编译的vim版本,并自行配置.vimrc文件,
  "可能就没有设置这一项,导致Backspace键用不了,或者时灵时不灵.所以主动配置.
  "使回格键（backspace）正常处理indent, eol, start等
  set backspace=indent,eol,start
  
  "允许backspace和光标键跨越行边界
  "set whichwrap+=<,>,h,l
  
  "去掉有关vi一致性模式,避免操作习惯上的局限.
  set nocompatible
  
  "FIXME 在MS-DOS控制台打开vim时,控制台使用鼠标右键来复制粘贴,设置
  "全鼠标模式,鼠标右键被映射为visual mode,不能用来复制粘贴,不方便.
  "但是如果不设置鼠标模式,会无法使用鼠标滚轮来滚动界面.经过验证,发现
  "可以设成普通模式mouse=n来使用鼠标滚轮,也能使用鼠标右键复制粘贴.
  " mouse=c/mouse=i模式都不能用鼠标滚轮. Linux下还是要设成 mouse=a
  set mouse=n
  "set selection=exclusive
  "set selectmode=mouse,key
  
  "高亮显示括号匹配
  set showmatch
  
  "设置Tab长度为4空格
  set tabstop=4
  "设置自动缩进长度为4空格
  set shiftwidth=4
  "自动缩进,这个导致从外面拷贝多行以空格开头的内容时,会有多的缩进,先不设置
  "set autoindent
  "不要用空格代替制表符
  set noexpandtab
  "输入tab制表符时，自动替换成空格
  "set expandtab
  "设置softtabstop有一个好处是可以用Backspace键来一次删除4个空格.
  "softtabstop的值为负数,会使用shiftwidth的值,两者保持一致,方便统一缩进.
  "set softtabstop=4
  
  
  "显示空格和tab键
  set listchars=tab:>-,trail:-
  
  "1=启动显示状态行, 2=总是显示状态行.设置总是显示状态行,方便看到当前文件名
  set laststatus=2
  
  "自动补全
  :inoremap ( ()<ESC>i
  :inoremap ) <c-r>=ClosePair(')')<CR>
  :inoremap { {<CR>}<ESC>O
  :inoremap } <c-r>=ClosePair('}')<CR>
  :inoremap [ []<ESC>i
  :inoremap ] <c-r>=ClosePair(']')<CR>
  :inoremap " ""<ESC>i
  :inoremap ' ''<ESC>i
  function! ClosePair(char)
      if getline('.')[col('.') - 1] == a:char
          return "\<Right>"
      else
          return a:char
      endif
  endfunction 
  "打开文件类型检测,并载入文件类型插件,为特定文件类型载入相关缩进文
  filetype plugin indent on
  " 设置自动补全的选项. longest表示只自动补全最大匹配的部分,剩余部分通过
  " CTRL-P/CTRL-N来选择匹配项进行补全. menu表示弹出可补全的内容列表.
  " 如果有多个匹配,longest选项不会自动选中并完整补全,要多按一次CTRL-P,比较
  " 麻烦,不做设置,保持默认设置,vim默认没有设置longest.
  "set completeopt=longest,menu "启用这句才会开启自动补全
  
  
  "=============显示中文帮助
  if version >= 603
      set helplang=cn
      set encoding=utf-8
  endif
  
  "=============新建.c,.h,.sh,.java文件，自动插入文件头 
  autocmd BufNewFile *.cpp,*.[ch],*.sh,*.java exec ":call SetTitle()" 
  ""定义函数SetTitle，自动插入文件头 
  func SetTitle() 
      "如果文件类型为.sh文件 
      if &filetype == 'sh' 
          call setline(1,"\############################") 
          call append(line("."), "\# File Name: ".expand("%")) 
          call append(line(".")+1, "\# Author: Li Ziqiang") 
          call append(line(".")+2, "\# mail: 2296557984@qq.com") 
          call append(line(".")+3, "\# Created Time: ".strftime("%c"))
          call append(line(".")+4, "\############################") 
          call append(line(".")+5, "\#!/bin/bash") 
          call append(line(".")+6, "") 
      else 
          call setline(1, "/******************************") 
          call append(line("."), "    > File Name: ".expand("%")) 
          call append(line(".")+1, "    > Author:Li Ziqiang") 
          call append(line(".")+2, "    > Mail: 2296557984@qq.com ") 
          call append(line(".")+3, "    > Created Time: ".strftime("%c")) 
          call append(line(".")+4, " *****************************/") 
          call append(line(".")+5, "")
      endif
  
      "新建文件后，自动定位到文件末尾
      autocmd BufNewFile * normal G
  endfunc 
  
  ```

  