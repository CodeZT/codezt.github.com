---
layout: post
title:  FASTDFS分布式文件存储服务搭建，集成Dubbo
date:   2018-04-04 14:00:00 +0800
categories: JAVA 系统设计 微服务 分布式存储
tags: JAVA 系统设计 微服务 分布式存储
comments: true
---

* content
{:toc}


为什么要用FASTDFS，就不多说了；首先来看一下使用FASTDFS的准备工作

> * 首先安装Fastdfs服务，网上一大堆教程，这里给出我用的安装环境，参考此文章(https://my.oschina.net/LevelCoder/blog/1648818)
(https://blog.csdn.net/m0_37797991/article/details/73381739)
> * 因公司原先使用硬存的方式存储文件，这就造成了一大堆问题，首先是性能，文件多了，存取不便；第二就是不易于管理，没有统一的管理接口。
> * 现搭建一个分布式文件存储服务，使用Spring Boot、Dubbo、FastDFS、Redis等框架进行整合。

### 下面说一下搭建步骤

#### 一、项目搭建，创建项目的步骤就不多说了，相信大家都会。如果这个不会，那就别往下看了。这里就附上项目的结构图

<img src="{{ '/assist/images/1522829240778.jpg' | prepend: site.baseurl }}" width="310" />

按照dubbo服务的角色来分，分为服务提供者和消费者；按照项目结构，另外多了一层api层，即对外暴漏的RPC接口。

1、首先定义接口

<img src="{{ '/assist/images/1522829654944.jpg' | prepend: site.baseurl }}" width="310" />
  
我这里就先定义了一个接口，提供上传、下载、查询、获取完整访问url接口。


> *以上为个人做项目，整理出来的，欢迎指正。
