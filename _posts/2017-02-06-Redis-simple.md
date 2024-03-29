---
layout: post
title: "纯真数据库数据导出和二分查找IP实现"
categories: python-script
tags: chunzhen geoip
author: zhangxx
---

{:toc}




# Redis 安装

```
docker pull sameersbn/redis:latest

docker run --name redis -d --restart=always \
  --publish 1122:6379 \
  --env 'REDIS_PASSWORD=112233' \
  --volume /srv/docker/redis:/var/lib/redis \
  sameersbn/redis:latest
  // 紧接着进入 docker exec -it <container_id> 注释 /etc/redis/redis.conf 中的 bind 0.0.0.0 
```

# Redis 客户端连接
## Redis 连接

redis提供两个类Redis和StrictRedis用于实现Redis的命令，StrictRedis用于实现大部分官方的命令，并使用官方的语法和命令，Redis是StrictRedis的子类，用于向后兼容旧版本的redis-py。

redis连接实例是线程安全的，可以直接将redis连接实例设置为一个全局变量，直接使用。如果需要另一个Redis实例（or Redis数据库）时，就需要重新创建redis连接实例来获取一个新的连接。同理，python的redis没有实现select命令。

```
import redis 

# r = redis.StrictRedis(host='192.168.0.110', password='112233', port=1122, db=0)
r = redis.Redis(host='192.168.0.110', password='112233', port=1122, db=0)
data = {'a':[2*i for i in range(10)]}
r.set("d1": data)
print(r.get('d1'))
print(r.keys())
r.delete('d1')
## r.flushdb()  #清空数据

p = r.pipeline()        ##--创建一个管道; 
## 下面命令支持链式操作，返回的还是自己; 
p.set("sid", 22)
p.set('hello','redis')
p.set("h1", 'iostream')
p.sadd('cate','aaaaa')
p.sadd('cate','bbbbb')
p.incr('num') ## 自增1
p.execute()

r.smembers('cate') ##查看成员; sadd 的作用结果
```

## Redis 连接池
redis-py使用connection pool来管理对一个redis server的所有连接，避免每次建立、释放连接的开销。默认，每个Redis实例都会维护一个自己的连接池。
可以直接建立一个连接池，然后作为参数Redis，这样就可以实现多个Redis实例共享一个连接池

```
import redis   # 导入redis模块，通过python操作redis 也可以直接在redis主机的服务端操作缓存数据库

connect_pool = redis.ConnectionPool(host='192.168.0.110', port=1122, password='112233', decode_responses=True, db=0)
r = redis.Redis(connection_pool=connect_pool)
r.set('gender', 'male')     # key是"gender" value是"male" 将键值对存入redis缓存
print(r.get('gender'))      # gender 取出键male对应的值
# set(name, value, ex=None, px=None, nx=False, xx=False)
## ex, px 设置过期时间为妙和毫秒
## nx，如果设置为True，则只有name不存在时，当前set操作才执行 （新建）
## xx，如果设置为True，则只有name存在时，当前set操作才执行 （修改）
# setnx(name, value)
## 设置值，只有name不存在时，执行设置操作（添加）
# 其他命令参考
# http://www.jianshu.com/p/2639549bedc8
```

## Redis 高级使用; 以及Mysql线程池等
- 这里是我后面在19年初修改和升级的内容，现在工作中一直应用

- [redis-cache](https://github.com/xx-scan/xx-scan/blob/master/apps/ops/libs/websdk/cache.py)
> 关于真实的脚本使用参考 我的另外一个开源项目
- 这个项目里面包含了 syslog-ng-python, 包含了 es, 包含了 redis-cache等。
- [nginx_modsecurity_log_es](https://github.com/xx-zhang/nginx_modsecurity_log_es)