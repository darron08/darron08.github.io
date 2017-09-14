---
layout: post
title:  "MySQL left join on where"
date:   2017-09-12
categories: MySQL
tags:  MySQL
author: Tse
---

* content
{:toc}

## 前言

最近接到一个需求：导出8月份新注册的用户（无论是否有下单），截至到9月1日前累计订单量，累计支付金额。

这里涉及到两个表，一个是用户表，一个是订单表。表结构大概如下：

```MySQL

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

```
一开始我很快就写了一条SQL：


select uid, count(1), sum(fee) from 
(select a.uid, d.dealid, d.fee, from `user` as a left join deal as d on a.uid = d.uid
WHERE a.regtime>= unix_timestamp('2017-08-01 00:00:00') and a.regtime< unix_timestamp('2017-09-01 00:00:00') and
d.state= 4 and d.gentime<unix_timestamp('2017-09-01 00:00:00')) as t
group by uid;



但其实这样写是有问题的，因为这样会把没有下单的用户的记录去除掉。

