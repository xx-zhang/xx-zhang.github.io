---
layout: post
title:  "相关WAF部署使用到日志处理等相关集成工具"
categories: WAF
tags: waf
author: zhangxx
---

* content
{:toc}

# 描述

> Centos7 系统 sshd + openresty1.13 + modsec-v3 + crs3.0+supervisor

# 执行
`docker run -itd --name=waf -p 30022:6922 -p 30080:6980 registry.cn-beijing.aliyuncs.com/actanble/openresty-modsec-crs3-ssh-p69 supervisord -n`

# 扩展
- 如果自己要扩展 就把 `-v /opt/nginx/:/etc/nginx -v /var/log/waf-log/:/var/log/` 添加扩展
- 注意使用映射前`/opt/nginx/`一定要有文件。

# 查看部署日志
- `docker logs <container_name>`

## 安装 Nginx(OpenResty) + Modsec3.02 版本
## 2018-8-21 记录
### 安装失败; 回到3.0版本
- 参考这个 Dockerfile 修改其中的Nginx为openresty
- DOckerfile 见 https://github.com/xx-zhang/install_tengine_with_mosecurity/blob/master/online/Dockerfile

### 将开源的检测规则放在我们的 nginx 里面

```
RUN cd nginx_waf && git clone https://github.com/SpiderLabs/owasp-modsecurity-crs.git \
&& cd owasp-modsecurity-crs/ \
&& cp -R owasp-modsecurity-crs/rules/ /etc/nginx/  \
&& cp crs-setup.conf.example /etc/nginx/crs-setup.conf
### waf配置文件加载配置。
RUN echo "\n\n#加载规则配置文件\nInclude crs-setup.conf\n#加载所有规则\nInclude rules/*.conf\n#禁用某个规则方法\n#SecRuleRemoveById 911250\n" >> /etc/nginx/modsecurity.conf
```

### 将本地的 nginx.conf 放进去; 添加

```
COPY nginx.conf /etc/nginx/

COPY entrypoint.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/entrypoint.sh
ENTRYPOINT ["entrypoint.sh"]
CMD ["/usr/sbin/nginx"]
```

## 出现的问题

 1, 缺少 `redhat的ld-opt` 
- 下载`yum -y install redhat-rpm-config`

 2, `error: perl module ExtUtils::Embed is required` 扩展包异常。
- `yum install perl perl-devel perl-ExtUtils-Embed`

 3, 缺少`google`分析工具 `google-perftools`
- `git clone https://github.com/gperftools/gperftools && cd gperftools && sh autogen.sh && ./configure && make && make install`

 4, nginx 启动缺少 `libprofiler.so`
- `ldd /usr/sbin/nginx`, `find / -type f -name "libprofiler.so*"`
- `ln -sv /usr/local/lib/libprofiler.so.0.4.17 /lib64/libprofiler.so.0`

 4.1, nginx 找到自己的mod模块
- `find / -type f -name "ngx_http_modsecurity_module.so*"`

 5, nginx 启动失败; `mod模块问题：ctl:requestBodyProcessor=URLENCODED`
- [URLENCODE失败](https://github.com/SpiderLabs/owasp-modsecurity-crs/issues/1120)
- [mod更新内容](https://coreruleset.org/)

 6, 替换 crs3.03-3.10版本都会出现问题; 901里面的便编码；dos里面的语法。
- 修改910350编码为json, 注释dos核心规则
- 912110 附近两条修改为
- 914100 太多了。


## 设置相关
- 请求中太多内容 [请求中有图片二进制](https://github.com/SpiderLabs/ModSecurity-nginx/issues/115)
```
SecResponseBodyLimit 1
SecResponseBodyMimeType text/plain text/html
```

### SSH 服务
- `yum install openssh-server -y`
- `vi /etc/ssh/ssh_config` 修改端口 
- `ssh-keygen -t dsa -f /etc/ssh/ssh_host_ed25519_key`
- `ssh-keygen -t rsa -b 2048 -f /etc/ssh/ssh_host_rsa_key`
- `ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key`


## 部署结束

- `-p 6922, -p 6980`
- `docker run -itd --name=waf -p 30022:6922 -p 30080:6980 registry.cn-beijing.aliyuncs.com/actanble/openresty-modsec-crs3-ssh-p69 supervisord -n`



