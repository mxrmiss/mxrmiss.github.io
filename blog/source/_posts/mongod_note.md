---
title: MongoDB
date: 2020/5/3 9:08:28
comments: ture
categories:
- mongodb
tags:
- mongodb
---

#关于MongoDB
    -安装
    -配置环境变量
        C:\Program Files\MongoDB\Server\4.2\bin
    -在C盘中创建一个文件夹 data
    -在data中创建1一个文件夹db
    
    -打开cmd命令行窗口
        -输入 mongod启动mongodb服务器
        -32位注意：
            启动服务器时，需要输入如下内容：
                mongod --storageEngine=mmapv1
            **注意！此内容32位用户只需在第一次使用mongodb时输入
        
        -  mongod --dbpath数据库路径 --port 端口号        
     
<!--more-->
    -再打开一个cmd窗口
        -输入mongo 连接mongodb， 出现 > 便为成功
        
    -数据库（database）
        -数据库的服务器
            -服务器用来保存数据
            -mongod 用来启动服务器
            
        -数据库的客户端
            -客户端用来操作服务器， 对数据进行增删改查的操作
            -mongo 用来启动服务器
            
        -将MongoDB设置为系统服务，可以自动在后台启动，不需要每次都手动启动
            1、在C盘根目录创建data（前面步骤已创建）
                在data下创建db和log文件夹
            2、创建配置文件
                在目录 C:\Program Files\MongoDB\Server\4.2下添加一个配置