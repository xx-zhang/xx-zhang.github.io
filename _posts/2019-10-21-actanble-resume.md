---
layout: post
title:  "个人简历"
categories: resume
tags: resume
author: zhangxx
---

# 个人简历

2017-2019的时间里， 我参与了 web应用防火墙的，应急响应处置平台等的开发，都是承担着核心开发业务。个人学习能力非常强，理解 `Devsecops` 的概念，也一直在这条路上越走越宽。
我擅长web后端，擅长服务端开发，网络服务搭建，计算环境防护，安全环境测试（主要是web应用测试）等。后面我会在工作经历中再详细说明对应的内容。

---

# 联系方式
- 手机：13429****** 
- Email：actanble@gmail.com 
- 微信号：actanble

---

# 个人信息

 - 湖北/男/1993 
 - 本科/湖北文理学院数学专业
 - 工作年限：3年
 - 技术博客：[https://xx-zhang.github.io](https://xx-zhang.github.io)
 - Github：[个人](http://github.com/xx-zhang)  [Docker技术](http://github.com/xx-docker) [安全技术](http://github.com/xx-sec) [渗透测试](http://github.com/xx-scan)
 - 期望职位：网络安全工程师 安全开发工程师
 - 期望薪资：税前月薪10k~12K
 - 期望城市：武汉

---

# 工作经历

## 黑白调（北京）家居用品有限公司（ 2016年6月 ~ 2017年4月 ）

### 数据采集处理
整理和脚本收集每天来自数据仓库，e店宝等工具的关于同行和自己的各个店铺关于方可流量的来源，销售占比等数据。
- [示例1 直通车关键词推广](https://blog.csdn.net/actanble/article/details/52235644)

### 用户画像
通过aliyun后台提供的各种因素，例如年龄，月消费，访问时段，访问频率，访问偏好等标签，对访客用户的特征进行
聚类，接着对不同的访客对应的访客品质投放对应的广告。

## 武汉楚云安网络科技有限公司 （ 2017年5月 ~ 2019年10月 ）
以下两个项目的所有的后台，防护引擎，日志收集，安装部署，版本更新等所有的内容
都是由本人单独负责和完成。也就是说，除了界面的编写之外都是我一人承担，其中本人
也承担了解决前台的问题，比如 cookie 获取和释放，xmlrequest 带请求头下载等技术问题。

### 应急响应处置平台 
在这个平台中，联立管理了IPS和漏扫两个工具，进行安全事件的处置监督。
其中具体的之数据采集到的数据见演示。简要说明如下。
    内网安全中，ips负责防护，漏扫负责本身的漏洞隐患，将两者的数据集中展现，能够快速方便进行应急响应处置。
这个大约开发了一年多，当然除了应急响应处置平台，还有信息安全管理平台的系统安全管理平台，管理的是对接入的数个安全设备进行综合管理，通过平台本身可以下发策略给对应的安全管理部件（就是漏扫和IPS设备）。


### Web应用防火墙 
选择引擎 -> 制定引擎规则 -> 设计数据字典 -> 开发后台 -> 日志采集 -> 版本更新 -> 安装部署。
以上每个步骤都是本人单独完成，耗时七八个月。
- 由于其中内容较多，难点也较多这里简单说几个，如果需要可以指定对应的环节跟我联系。
   - 选择引擎，当前开源openwaf/modsecurity两者比较火，选择modsecruity+tengine 
   - 制定规则，需要一定的waf防护经验和符合规则规范的编写逻辑，其中限制请求头长度，限制响应状态码，限制响应关键字较复杂一点。
   - 设计字典，数据采集，资源管理，认证模块，版本升级，处置监督，规则模块等数据库设计。
   - 日志采集 文本采集到数据库集中管理和展示。
   - 后台开发 管理waf引擎状态，通过界面交互实现规则集变动，制定对应的防护策略等。
   - 版本升级，打补丁，规则升级。
   - 安装部署，docker 部署，双机热备。
- 上面都是简单介绍，如果要详细知道，我会携带演示界面进行展示，另外解答每个过程的难点和问题。

### 其他项目

#### 漏洞知识库
做出类似 cnnvd,cve,seebug 等类似的漏洞知识库，在其他公司的漏洞库的基础上，增加了我们自己产品发现的漏洞，以及自己的处理方案。当前已经处理和收集解析了将近三千多个个漏洞。

#### 信息收集
基于ivre开发，类似钟馗之眼，shodan的工具，主要是监控防护的主机，是否有异常端口开放，是否有错误解析等。

---

# 项目和作品
> 下面两个项目，见本人带的项目演示。笔记本展示
- Web应用防护墙开发
- 应急响应平台开发

## 开源项目

- [docker-modsecurity全网最完整的WAF打包引擎](https://github.com/xx-docker/install_tengine_with_mosecurity)
   - 原项目, 根项目等都是本人。readme.md 即可看到。
- [xx-scan并发扫描器](https://github.com/xx-scan/xx-scan)
   - 当前版本是前后端分离，准备增加任务调度和es的版本，老版本v0.1在release
- [mongo收集waf日志](https://github.com/xx-sec/tengine-mosecruity-logdev)
   - 日志收集工具，txt->mongo->sql(es)
- [自研sql批量插入,半sql半ORM智能化工具](https://github.com/xx-scan/xsqlmb)
   - 增删改查的批量操作，可以根据字段传入进行活动。所以是半ORM.
- [全网最新openvas打包Docker-Openvas](https://github.com/xx-scan/docker-openvas-based-ub1804)

## 技术文章
- 当前博客可以查看 [https://xx-zhang.github.io](https://xx-zhang.github.io)
- 更老的博客文章  [https://blog.csdn.net/actanble](https://blog.csdn.net/actanble)

由于一直在公司开发，面对的都是真实环境中的难点和项目，所以很少技术相关的文章。但是本人的项目环境中都有对应的说明和解读，如果要深入了解，我携带的电脑上可以展示。


# 技能清单

以下均为我熟练使用的技能

- Web开发：Python
- Web框架：Django/Tornado
- CMDB：Celery/Apscheduler/Ansible
- 渗透工具：Appscan/AWVS/Sqlmap/XSStrike/kali2*/Msf等
- 安全工具：Suricata/Openvas/VUls/Modsecrity/T-Pot
- 前端框架：Bootstrap/Jquery/Vuejs
- 数据库相关：MySQL/PgSQL/ElasticSearch/SQLite
- 版本管理、文档和自动化部署工具：Git
- 单元测试：Python-Unit
- 虚拟化技术: Docker, KVM, VitualBox

## 技能关键字(熟练度)

- python(364)            python/django 开发
- web(219)               http/https wen-api后台端
- pentest(200)           渗透
- sercurity(200)         安全防护
- waf/ips/pot/siem(200)  安全防护工具
- docker(211)            Docker使用; 唯一部署方案
- mysql(186)             关系数据库的使用和管理
- linux(159)             ub1804/centos7/debian9等环境开发
- celery(12)             异步任务
- service(12)            服务端中间件
- scheduler(12)          任务调度
- framework(12)          框架/后台框架/渗透框架/安全防护框架
- db(8)                  redis/momgo/es 等
- api(8)                 编写和使用API，二次开发API等。
- orm(7)                 ORM->DB 数据字典和数据管理

---

# 致谢
感谢您花时间阅读我的简历，期待能有机会和您共事。

