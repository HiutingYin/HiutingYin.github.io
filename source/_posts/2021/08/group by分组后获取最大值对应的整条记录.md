---
title: group by分组后获取最大值对应的整条记录
author: HiuTingYin
date: 2021-08-25 18:58:00 +0800
categories: [日常问题合集, MySQL]
tags: MySQL
---

在mysql中如果使用 `group by`进行分组获取某一列的最大值，我们可以直接使用`max`进行获取。  
但是如果我们需要最大值对应的其余列值，那么我们需要取得整行的数据。
<!-- more -->

### 问题描述

在活动开发过程中，需要展示 各英雄 点赞数贡献值最大的玩家信息。

`thumbs`表中包含所有玩家对英雄的点赞信息，每个玩家有其对应的点赞英雄 hero，对应英雄点赞数thumb，点赞记录更新时间 utime

|  id   | uid  |  hero   | thumb  | utime    |
|  ----  | ----  |----  |----  |----  |
| 1  | 1 |英雄1|10|1629459033|
| 2  | 2 |英雄1|10|1629459034|
| 3  | 3 |英雄1|2|1629891033|
| 4  | 1 |英雄2|5|1629891033|
| 5  | 2 |英雄2|10|1629891033|

`users` 表中包含玩家的基础信息

 |uid|nickname  |
|  ---- |  ---- |
|1 |测试1|
|2 |测试2|
|3 |测试3|

第一想法就是通过 group by 和 max 来获取对应信息。但是需注意 **取每组最大的数据** 和 **取每组最大一条记录** 是两个概念。 
前者可以直接通过group by 分组，max()即可得到。后者如果通过前者的方法获取，只能得到max()值，对应的其他字段非max()值这条记录的对应数据。

### 解决方法
使用 `JOIN` 和 `IN` 语句
步骤一：通过`group by` 和 `max()` 查询thumbs 表中对应hero的最高点赞数
>注意：因为有可能存在多个玩家同时拥有最高点赞数，所以在这个查询中不包含玩家uid信息
```mysql
select hero,max(thumb),utime from thumbs  group by hero order by thumb desc , utime asc;
```

步骤二：把users表和thumbs表连接，在这张临时表中，查询uid和点赞数的关系
```mysql
SELECT
     hero,thumb,t.uid,u.nickname
FROM
    thumbs as t 
left join  	users as u on t.uid = u.uid
WHERE
    (t.hero,thumb,t.utime) in 
(
select hero,max(thumb),utime from thumbs  group by hero order by thumb desc , utime asc
)
;
```

结果：

 |hero|thumb|uid  |  nickname  |
|  ---- |  ---- |  ----  | ----  |
|英雄1 |10|1|测试1|
|英雄2|10|2|测试2|
