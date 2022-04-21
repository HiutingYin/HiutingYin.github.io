---
title: MySQL关闭严格模式
date: 2022-03-22 15:14:59
tags: MySQL
categories: [日常问题合集, MySQL]
---

mysql日期时间设置默认0000-00-00 00:00:00的时候会报错。后面发现是mysql的严格模式被开启导致的。

### 严格模式 
[mysql中有严格模式和非严格模式](https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html#sql-mode-strict)，顾名思义，严格模式就是比非严格模式更为严格，主要就是对数据的要求更为严格，比如数据的类型，长度，格式等。

比如一个整型字段我们写入一个字符串类型的数据，在非严格模式下MySQL不会报错，同样如果定义了char或varchar类型的字段，当写入或更新的数据超过了定义的长度也不会报错
。还有一些关于null的数据问题，也可能是因为严格模式造成的。

首先通过以下命令，查询当前是否处于严格模式，如果查询结果中存在 **STRICT_TRANS_TABLES** ，则表示开启了严格模式
```mysql
SELECT @@GLOBAL.sql_mode;       //查看MySQL的全局模式
SELECT @@SESSION.sql_mode;      //查看MySQL的当前连接的模式，如果当前连接模式与全局模式不一致，关闭连接后再连接就一致了
```

### 开启严格模式
1.修改配置文件  
找到mysql安装目录下的my.cnf（windows系统则是my.ini)文件  
在sql_mode中加入STRICT_TRANS_TABLES则表示开启严格模式，如没有加入则表示非严格模式，修改后重启mysql即可

2.在查询中设置  
先查看一下当前你的sql_mode，然后复制查询结果，把STRICT_TRANS_TABLES加到开头就可以了  
执行以下两个语句中的一个，根据自身情况修改（推荐了解一下这些变量，都挺容易记的，记住了就可以按需使用了）  

然后我们遇到的时间判断问题，主要是因为sql_mode中有 **NO_ZERO_DATE和NO_ZERO_IN_DATE**。把这两个变量去掉就好了。