---
layout: post
title:  "Postgresql-mysql转换"
categories: db
tags: pgsql mysql  
author: zhangxx
---

{:toc}

## docker_file_dbs 安装 
`docker_db/docker-compose.yml `
```
version: '0'
services:
  postgres:
    image: postgres:latest
    environment:
      - POSTGRES_USER=root
      - POSTGRES_PASSWORD=112233..
      - POSTGRES_DB=qydldb
    volumes:
      - /var/lib/pgsqldata:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  redis:
    image: redis:latest
    volumes:
      - /var/lib/redisdata:/data
      - /var/log/redis:/var/log/redis
    ports:
      - "6379:6379"

  mysql:
    image: mysql
    volumes:
        - "/var/lib/mysqldata:/var/lib/mysql"
    ports:
      - "3306:3306"
    environment:
        MYSQL_ROOT_PASSWORD: 112233..
        MYSQL_DATABASE: qydldb
        MYSQL_USER: admin005
        MYSQL_PASSWORD: myadmin@826
    command:
      - "service mysql restart"

  dj_site:
    build: .
    command: "/bin/bash", "/home/django/start.sh"
    environment:
      - SETTING_PATH=/home/django/website
    volumes:
      - nginx-site.conf:/etc/nginx/sites-available/default
      - supervisor.conf /etc/supervisor/conf.d/
      - website:/home/django/website

volumes:

```
## Mysql -> pgsql 
>  **将 mysql 数据库导出到 pgsql 上来**
### 前提条件
- `本地有mysql客户端和一些mysql库，方便python2链接`
- `pip install py-mysql2pgsql`
-  `py-mysql2pgsql -v -f mysql2pgsql.yml`

*mysql2pgsql.yml*
```
mysql:
 hostname: localhost
 port: 3306
 # socket: /tmp/mysql.sock
 username: root
 password: 112233..
 database: test65
 compress: false
destination:
 # if file is given, output goes to file, else postgres
 file:
 postgres:
  hostname: localhost
  port: 5432
  username: postgres
  password: 112233..
  database: chunzhen_ip
  
# if supress_data is true, only the schema definition will be exported/migrated, and not the data
supress_data: false

# if supress_ddl is true, only the data will be exported/imported, and not the schema
supress_ddl: false

# if force_truncate is true, forces a table truncate before table loading
force_truncate: false

# if timezone is true, forces to append/convert to UTC tzinfo mysql data
timezone: false

# if index_prefix is given, indexes will be created whith a name prefixed with index_prefix
index_prefix:
```


## PGSQL 查询纯真IP数据库第一个网段
```
select t1.x*强调内容*, count(t1.x) as cc from 
   (select x :: integer, line from 
       (SELECT regexp_split_to_table(start_ip, '\.') as x, row_number() over() as line from cz_ip) as t 
      where mod(t.line, 4)=1) as t1 
     group by t1.x 
     order by t1.x desc;


```
## 参考

- [pgsql 分区](http://blog.csdn.net/chendaoqiu/article/details/45537743)
- [Mysql 导出到 pgsql](https://github.com/philipsoutham/py-mysql2pgsql)
- [mysql/pgsql 区别](http://blog.csdn.net/u012843873/article/details/60745287)
- [知乎: mysql/pgsql两者优势](https://www.zhihu.com/question/20010554)