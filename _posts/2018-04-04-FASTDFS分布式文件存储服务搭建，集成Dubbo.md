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

#### 项目搭建，创建项目的步骤就不多说了，相信大家都会。如果这个不会，那就别往下看了。这里就附上项目的结构图

<img src="{{ '/assist/images/1522829240778.jpg' | prepend: site.baseurl }}" width="400" />

按照dubbo服务的角色来分，分为服务提供者和消费者；按照项目结构，另外多了一层api层，即对外暴漏的RPC接口。

首先定义接口

<img src="{{ '/assist/images/1522829654944.jpg' | prepend: site.baseurl }}" width="400" />
  
我这里就先定义了一个接口，提供上传、下载、查询、获取完整访问url接口。

  <pre>
    <code>
      /**
        * 类 名: RemoteStorageService
        * 描 述: 文件存储服务RPC接口
        * 作 者: 张桐
        * 创 建：2018/3/29 上午10:36
        * 版 本：v1.0.0
        *
        * 历 史: (版本) 作者 时间 注释
        */
        public interface RemoteStorageService {

          /**
          * 描 述： 上传文件，默认使用第一个分组
          * 作 者： 张桐
          * 历 史： (版本) 作者 时间 注释
          * @param data 文件的二进制内容
          * @param fileInfo	文件的信息，需要自行传入buzId,fileName,suffixName等其他描述信息
          *                  相关路径会自动设置
          * @return 上传成功后返回文件对象信息，否则返回NULL
          */
          ComFileInfo upload(byte[] data, ComFileInfo fileInfo);

          /**
          * 描 述： 指定分组上传文件
          * 作 者： 张桐
          * 历 史： (版本) 作者 时间 注释
          * @param group 分组，详见枚举
          * @param data 文件的二进制内容
          * @param fileInfo	文件的信息，需要自行传入buzId,fileName,suffixName等其他描述信息
          *                  相关路径会自动设置
          * @return 上传成功后返回文件对象信息，否则返回NULL
          */
          ComFileInfo upload(FastDFSGroup group, byte[] data, ComFileInfo fileInfo);

          /**
          * 描 述： 下载文件
          * 作 者： 张桐
          * 历 史： (版本) 作者 时间 注释
          * @param fileId 文件表ID
          * @return 返回二进制数组
          */
          byte[] download(String fileId);

          /**
          * 描 述： 获取文件访问token
          * 作 者： 张桐
          * 历 史： (版本) 作者 时间 注释
          * @param fileId 文件表ID
          * @return 返回token=?&ts=?的格式，拼接在访问地址的后面
          */
          String getToken(String fileId);

          /**
          * 描 述： 获取文件访问的完整URL，自动拼接token
          * 作 者： 张桐
          * 历 史： (版本) 作者 时间 注释
          *
          * @param fileId 文件表ID
          * @return httpUrl
          */
          String getHttpUrl(String fileId);

          /**
          * 描 述： 删除文件
          * 作 者： 张桐
          * 历 史： (版本) 作者 时间 注释
          * @param fileId 被删除的文件表ID
          * @return 删除成功后返回0，失败后返回错误代码
          */
          int deleteByFileId(String fileId);

          /**
          * 描 述： 删除文件
          * 作 者： 张桐
          * 历 史： (版本) 作者 时间 注释
          * @param buzId 被删除的文件表业务ID
          * @return 删除成功后返回0，失败后返回错误代码
          */
          int deleteByBuzId(String buzId);
        }
    </code>
  </pre>

2、创建服务提供者

  <img src="{{ '/assist/images/1522830786953.jpg' | prepend: site.baseurl }}" width="400" />

  

> *以上为个人做项目，整理出来的，欢迎指正。
