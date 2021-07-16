---
title: sqlmap
date: 2021-6-26
swiper: false # 将改文章放入轮播图中
swiperImg: '' # 该文章在轮播图中的图片，可以是本地目录下图片也可以是http://xxx图片
img: 'http://cdn.mxrblog.cn/ct_4.jpg' # 该文章图片，可以是本地目录下图片也可以是http://xxx图片
categories: sql
tags: [sql, sqlmap]   # 可以是数组
top: false

---

## sqlmap basics

#### continuously  updating ……

- Format

```
sqlmap -u "required_url" parameter1, parameter2, parameter3 ...
```

- Basic parameters

```
-dbs		//all databases
-tables		//all tables
-columns	//all columns
-dump		//all items of columns
-D [DBNAME]		//specified database
-T [TBLNAME]	//specified table
-C [COLNAME]	//specified column
-random-agent	//construct random users
-proxy [PROXY]://[url]:[port] -threads 10	//proxy(customize the thread)
–dbms=DBMS			//specified DBMS
-p	[TESTPARAMETER]		//specified parameters of a test
–level=[LEVEL]		//level of execution (1-5)
–risk=[RISK]		//risk of execution (0-3)
```

