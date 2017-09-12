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

##前言

最近接到一个需求：导出8月份新注册的用户，截至到9月1日前累计订单量，累计支付金额。

这里涉及到两个表，一个是用户表，一个是订单表。表结构大概如下：

```MySQL
       Table: user
Create Table: CREATE TABLE `user` (
	`uid` bigint(20) NOT NULL DEFAULT '0' COMMENT '用户id',
	`regtime` bigint(20) NOT NULL DEFAULT '0' COMMENT '注册时间',
	PRIMARY KEY (`uid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='用户表'


```
