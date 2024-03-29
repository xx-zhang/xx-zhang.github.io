---
layout: post
title:  "部署WAF引擎出现的问题和解决方案"
categories: WAF 
tags: docker waf syslog
author: zhangxx
---

* content
{:toc}


>  部署Nginx/Openresty/Syslog/Lua 等会出现的问题和解决方案

## 安装syslog-ng问题

> configure: error: Cannot find eventlog version >= 0.2: is pkg-config in path?
- [解决方案](https://blog.csdn.net/awenluck/article/details/40618703)

- 1， yum -y install glib2-devel
- 2，export PKG_CONFIG_PATH=/usr/local/eventlog/lib/pkgconfig
- 3，./configure--prefix=/usr/local/syslog-ng --with-libol=/usr/local/libol && make&& make install

# 安装过程和配置

## 客户端主机(WAF产生日志机器)

- 172.10.1.129

`docker run -it -p 514:514 -p 601:601 -v /var/log/waf_logs/:/var/log/waf_logs/ --name sn balabit/syslog-ng:latest -edv`

### syslog-ng/conf.d/waf_client.conf

```
source access_log { file("/var/log/waf_logs/nginx/access.log");  };
source modsec_audit_log { file("/var/log/waf_logs/modsec_audit.log");;  };

destination d_centserver1 { network("172.10.1.127" transport("tcp") port(30051)); };
destination d_centserver2 { network("172.10.1.127" transport("tcp") port(30052)); };

log { source(access_log); destination(d_centserver1); };
log { source(modsec_audit_log); destination(d_centserver2); };
```

## 服务端主机(WAF存储日志得机器)

- 172.10.1.127

### Install

- **服务端开启两个TCP端口允许外部所有主机使用这个日志服务, 客户端在建立链接**

`docker run -it --net=host -v /opt/log/:/opt/log/ --name sns balabit/syslog-ng:latest -edv`

#### syslog-ng/conf.d/waf_server.conf

```
source waf_server_accesslog { network(
        ip("0.0.0.0")
        port(30051)
        flags(syslog-protocol)
        transport("tcp")
    ); };

source waf_server_modsec { network(
        ip("0.0.0.0")
        port(30052)
        flags(syslog-protocol)
        transport("tcp")
    ); };

destination accesslog_path { file("/opt/log/access.log"); };
destination modseclog_path { file("/opt/log/modsec_audit.log"); };

log { source(waf_server_accesslog); destination(accesslog_path); };
log { source(waf_server_modsec); destination(modseclog_path); };

```

### 产生出来的日志存在缺陷
- 日志的开头都会包含时间戳

----------------------------------------------------

## 安装 NginxLuaWaf

```yaml
# docker cp VeryNginx verynginx:/root/
# 前置需要的工具大全
yum install -y pcre pcre-devel openssl openssl-devel  perl gcc
cd verynginx && python install.py install 
ln -s /opt/verynginx/openresty/nginx/sbin/nginx /usr/sbin/nginx
# nginx -V ## 查看版本
```

### 部署成功的镜像
- `docker push registry.cn-beijing.aliyuncs.com/anglecv/verynginx`

------------------------------------------------------
## 测试机 VBoxManage 容量不足
- `VBoxManage list hdds`
- `VBoxManage modifyhd 006b81b5-4d05-4c4f-8df0-20b5ab6f9d93 - -resize 5210`
- `bin/centos_vm_readme.md` 分区和硬盘处理的文件
- 恢复原来的20G文件， 出现的问题: 
- - 无法进行本地映射。 `proxy`
  - 无法进行容器隔离(原来的4G)

------------------------------------------------------

## Readmine 插件
```

# 首先第一个大插件
git clone https://github.com/alphanodes/additionals

## www.easyredmine.com
# 任务插件
http://github.com/jperelli/Redmine-Periodic-Task/

# redmine_ckeditor
http://github.com/a-ono/redmine_ckeditor

## Issues 插件
https://github.com/Loriowar/redmine_issues_tree

# 产品插件

# 问题插件
## http://www.redminecrm.com/projects/questions
## http://redmineup.com/pages/plugins/agile
## 
## 清凉BOX
https://github.com/paginagmbh/redmine_lightbox2

# 工作时间插件
## https://github.com/tkusukawa/redmine_work_time/releases
https://github.com/tkusukawa/redmine_work_time

# redmine_banner
https://github.com/akiko-pusu/redmine_banner
```
------------------------------------------------------

## 安装 ` cryptography` 出错
`apt-get -y  install build-essential libssl-dev  libffi-dev python-dev`


```bash 
wget ftp://sourceware.org/pub/libffi/libffi-3.0.11.tar.gz
tar zxvf libffi-3.0.11.tar.gz
cd libffi-3.0.11/　　　　
./configure && make && make install
# （这里需要注意一定要在sudo下进行安装，如果直接make install的话不会安装好）    
```

## 重新编辑
- 本文章的所有内容，已经被 xx-docker 里卖弄的 logdev 和 es 取代。

