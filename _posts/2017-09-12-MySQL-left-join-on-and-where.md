---
layout: post
title:  "MySQL left join后on与where的区别"
date:   2017-09-12
categories: MySQL
tags:  MySQL
author: darron
---

* content
{:toc}


最近接到一个需求：导出8月份新注册的用户（无论是否有下单），截至到9月1日前累计订单量，累计支付金额。

这里涉及到两个表，一个是用户表，一个是订单表。表结构大概如下：


	Table: user
Create Table: CREATE TABLE `user` (
	`uid` bigint(20) NOT NULL DEFAULT '0' COMMENT '用户id',
	`regtime` bigint(20) NOT NULL DEFAULT '0' COMMENT '注册时间',
	PRIMARY KEY (`uid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='用户表'

	
	Table: deal
Create table: CREATE TABLE `deal` (
	`dealid` bigint(20) NOT NULL DEFAULT '0' COMMENT '订单号',
	`uid` bigint(20) NOT NULL DEFAULT '0' COMMENT '订单用户uid',
	`fee` bigint(20) NOT NULL DEFAULT '0' COMMENT '费用',
	`state` int(10) NOT NULL DEFAULT '0' COMMENT '订单状态',
	`gentime` bigint(20) NOT NULL DEFAULT '0' COMMENT '下单时间',
	PRIMARY KEY (`dealid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='订单表'


一开始我很快就写了一条SQL：

```
SELECT uid, COUNT(dealid), SUM(fee) FROM 
(SELECT a.uid, d.dealid, d.fee, FROM user AS a LEFT JOIN deal AS d ON a.uid = d.uid
WHERE a.regtime>= UNIX_TIMESTAMP('2017-08-01 00:00:00') AND a.regtime< UNIX_TIMESTAMP('2017-09-01 00:00:00') AND
d.state= 4 AND d.gentime<UNIX_TIMESTAMP('2017-09-01 00:00:00')) AS t
GROUP BY uid;
```

但其实这样写是有问题的，因为这样会把没有下单的用户的记录去除掉。问题在于优先级，on的优先级高于where。

首先明确两个概念：

1. LEFT JOIN 关键字会从左表那里返回所有的行，即使右表没有匹配的行。
2. 数据库在通过连接两张或多张表来返回记录时，都会生成一张中间的临时表，然后再将这张临时表返回给用户。

在left join下，两者的区别：

1. on是在生成临时表的时候使用的条件，不管on的条件是否起到作用，都会返回左表的行。
2. where则是在生成临时表之后使用的条件，此时已经不管是否使用了left join了，只要条件不为真的行，全部过滤掉。

所以，其实子语句 select a.uid, d.dealid, d.fee, from user as a left join deal as d on a.uid = d.uid WHERE a.regtime>= unix_timestamp('2017-08-01 00:00:00') and a.regtime< unix_timestamp('2017-09-01 00:00:00') and
d.state= 4 and d.gentime<unix_timestamp('2017-09-01 00:00:00') 是会先根据 from user as a left join deal as d on a.uid = d.uid 生成一个临时表，这个临时表肯定会包含user表的所有行。然后，根据where的条件 a.regtime>= unix_timestamp('2017-08-01 00:00:00') and a.regtime< unix_timestamp('2017-09-01 00:00:00') 把注册时间不符的用户记录删除掉，而且因为没有下过单的用户关于deal表的记录会是NULL，所以在where条件 d.state= 4 and d.gentime<unix_timestamp('2017-09-01 00:00:00') 的作用下，会把不符合条件的记录（当然就包括NULL）删除掉，于是我们看到最后的结果是只能得到8月份新注册且有下单的用户，截至到9月1日前累计订单量，累计支付金额，这并不是我们想要的结果。

正确的SQL语句应该是：

```
select uid, count(dealid), sum(fee) from 
(
	select a.uid, d.dealid, d.fee from user as a left join deal as d on 
	a.uid = d.uid and d.state= 4
	and d.gentime<unix_timestamp('2017-09-01 00:00:00') 
	WHERE a.regtime>= unix_timestamp('2017-08-01 00:00:00')
	and a.regtime< unix_timestamp('2017-09-01 00:00:00')
) as t group by uid;
```
上面这条语句，我在JOIN后面加多了两个针对关联表deal的条件，d.state=4 and d.gentime<unix_timestamp('2017-09-01 00:00:00')。

ON与where的使用一定要注意场所：

1. **ON后面的筛选条件主要是针对的是关联表。**
2. 对于主表的筛选条件应放在where后面。
3. 对于关联表我们要区分对待。如果是要条件查询后才连接应该把查询件放置于ON后。如果是想在连接完毕后才筛选就应把条件放置于where后面。
4. 对于关联表我们其实可以先做子查询再做join，SQL如下：

```
select aa.uid ,count(dd.dealid), sum(dd.fee) from
(
	SELECT uid from user
	WHERE regtime> unix_timestamp('2017-04-1')
	and regtime < unix_timestamp('2017-05-1')
) as aa left join (
	select uid, dealid, fee from deal
	where state= 4 and gentime< unix_timestamp('2017-07-01')
) as dd on aa.uid= dd.uid group by aa.uid;
```