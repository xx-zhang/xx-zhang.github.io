---
layout: post
title:  "WAF 基础入门须知，给刚入职人员"
categories: WAF 
tags: waf
author: zhangxx
---

{:toc}


# [了解WAF攻击的过程(需了解124)](http://192.168.0.188/actanble/waf-phaser1/blob/master/workstat/work-env/waf_cook.md)

## 1， OWASP-Top10-2017-v1.3 
- WAF最新标准和相关隐患的介绍
- [中国owasp](http://www.owasp.org.cn/owasp-project)
- [世界OWASP](https://www.owasp.org/index.php/Main_Page)

## 2,  [靶场DVWA](http://172.10.1.129:9070/login.php)
> 了解常见的攻击漏洞; 攻击的方法看页面的 help 按钮即可
- 用户名/密码 admin/password

## 3, 最全靶场 `WebGoat` 
- `WebGoat`和`Bwapp`是面向开发人员的靶场环境，旨在改变一些编码习惯。
- 这是个`jar`包, 安装了Java之后直接点击就能
- 运行 `java -jar webgoat-server-8.0.0.VERSION.jar [--server.port=8080] [--server.address=localhost]`
- 网上关于[本地WebGoat7.1](http://172.10.1.127:8080/WebGoat/)版本的介绍较多。这里给一个8.0的jar包

## 4, WAF 渗透工具
### Burpsuite
- `Burpsuite+Pro+1.6`
- 就是获取网页的包进行修改重发的工具。
- 后面有个文档会介绍

## 5, OWASP测试指南(中文）
- 测试指南是相关的生产环境中waf应该达到的标准。
- 对应的过审WAF(waf标准.pdf)

## 6, 深入尝试新的漏洞和解决方案
- [vulhub](https://github.com/vulhub/vulhub)

## 7, WAF部署和测试【跳过】
- [WAF文档介绍](http://192.168.0.188/actanble/waf-doc/tree/master)
- [WAF开发](http://192.168.0.188/actanble/waf-phaser1/tree/master)
- [WAF管理](http://172.10.1.127:5721/waf/webmg/)