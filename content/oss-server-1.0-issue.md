---
title: "oss-server 1.0 版本发布 小型对象存储系统"
date: 2018-06-19T10:01:33+08:00
tags:
- softnews
categories:  "softnews" 
---
oss-server 1.0正式发布了,oss-server是针对项目开发时提供的小型对象存储系统,开发者在针对文件上传时业务剥离,同时方便文件迁移，为满足单个项目，多个系统的情况下，提供统一的oss服务 

本次更新：

1、重构界面,调整登录页、首页等页面

2、引入sqlite3嵌入式数据库，辅助存储相关系统相关信息

3、增加主面板功能、基本信息、权限管理(开发者管理、应用管理)等功能

4、修改上传相关接口,增加开发者权限验证,上传需提供开发者appid及appsecret

5、完善oss-server帮助文档,在线访问地址：http://oss-server.mydoc.io/

6、引入druid、mybatis-plus等数据库技术中间件

![](index.png)

![](basic.png)



关于更多oss-server的信息,请仔细阅读帮助文档：http://oss-server.mydoc.io/

如果您有任何问题,或者建议,欢迎提issue

欢迎大家拍砖~~~ 

如果项目对您有帮助,请前往项目地址给个Star ！！！！



**相关链接**

- oss-server 的详细介绍：[点击查看](https://gitee.com/xiaoym/oss-server)
- oss-server 的下载地址：[点击下载](https://gitee.com/xiaoym/oss-server/releases)