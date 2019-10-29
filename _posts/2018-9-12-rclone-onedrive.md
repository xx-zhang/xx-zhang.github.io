---
layout: post
title:  "rclone 挂在onedrive教程"
categories: rclone
tags: rclone
author: zhangxx
---


{:toc}


# [Rclone 挂载 Onedrive教程](https://github.com/xx-scan/ubsec/blob/master/rclone.md)

- [在centos-7上使用rclone挂载onedrive网盘](https://www.centos.bz/2019/02/%E5%9C%A8centos-7%E4%B8%8A%E4%BD%BF%E7%94%A8rclone%E6%8C%82%E8%BD%BDonedrive%E7%BD%91%E7%9B%98/)
- [Rclone使用教程 - 挂载管理Google Drive/Dropbox等网盘](https://www.moewah.com/archives/876.html)
- [Rclone使用教程](https://www.superbin.cc/technology/544.html)

## 认证产生token 
- `rclone authorize "onedrive"`
- 将结果保存后面要放到一个result里面去。

## 安装
```
#!/bin/bash 

wget https://downloads.rclone.org/rclone-current-linux-amd64.zip && unzip rclone-current-linux-amd64.zip -d /usr/local/

ln -sn /usr/local/rclone- /usr/bin/rclone 
 
```

## 基本命令

```ini 
### 文件上传
rclone copy /home/backup gdrive:backup # 本地路径 配置名字:谷歌文件夹名字
### 文件下载
rclone copy gdrive:backup /home/backup
### 列表
rclone ls gdrive:backup
rclone lsl gdrive:backup # 比上面多一个显示上传时间
rclone lsd gdrive:backup # 只显示文件夹
### 新建文件夹
rclone mkdir gdrive:backup
### 挂载
rclone mount gdrive:mm /root/mm &
### 卸载
fusermount -u  /root/mm
 
#### 其他 ####
#### https://softlns.github.io/2016/11/28/rclone-guide/
rclone config - 以控制会话的形式添加rclone的配置，配置保存在.rclone.conf文件中。
rclone copy - 将文件从源复制到目的地址，跳过已复制完成的。
rclone sync - 将源数据同步到目的地址，只更新目的地址的数据。   –dry-run标志来检查要复制、删除的数据
rclone move - 将源数据移动到目的地址。
rclone delete - 删除指定路径下的文件内容。
rclone purge - 清空指定路径下所有文件数据。
rclone mkdir - 创建一个新目录。
rclone rmdir - 删除空目录。
rclone check - 检查源和目的地址数据是否匹配。
rclone ls - 列出指定路径下所有的文件以及文件大小和路径。
rclone lsd - 列出指定路径下所有的目录/容器/桶。
rclone lsl - 列出指定路径下所有文件以及修改时间、文件大小和路径。
rclone md5sum - 为指定路径下的所有文件产生一个md5sum文件。
rclone sha1sum - 为指定路径下的所有文件产生一个sha1sum文件。
rclone size - 获取指定路径下，文件内容的总大小。.
rclone version - 查看当前版本。
rclone cleanup - 清空remote。
rclone dedupe - 交互式查找重复文件，进行删除/重命名操作。
#### 其他 ####
```

## 挂载
```bash
# yum install -y nload htop fuse p7zip-full
 
#::挂载为磁盘
# rclone mount DriveName:Folder LocalFolder --copy-links --no-gzip-encoding --no-check-certificate --allow-other --allow-non-empty --umask 000
 
#::卸载磁盘
# fusermount -qzu LocalFolder
```


## Supervisord 监听mount
```
[program:onedrive]
command=rclone mount onedrive:/ /mnt/guest/ --copy-links --no-gzip-encoding --no-check-certificate --allow-other --allow-non-empty --umask 000
process_name=%(program_name)s
numprocs=1
user=root
autostart=true
autorestart=true
#autorestart=unexpected
startsecs=5
startretries=3
priority=2
redirect_stderr=true
stdout_logfile=/var/log/%(program_name)s.log
stdout_logfile_maxbytes=20MB
stdout_logfile_backups=10

stopasgroup=true
killasgroup=true
```

