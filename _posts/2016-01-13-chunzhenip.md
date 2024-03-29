---
layout: post
title: "纯真数据库数据导出和二分查找IP实现"
categories: python-script
tags: chunzhen geoip
author: zhangxx
---

{:toc}


## 创建文本
> 这里只是提供一个思路，后面导入到 mongo/mysql/es 实际上差别不大。

```python
# import os
import numpy as np
# BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
# os.environ.setdefault("new_dir", BASE_DIR)
# from db import Connection, MPP_CONFIG

def write_data_to_db_sql():
    import re
    all_sql = """"""
    txt = """"""
    src =  open("data/ip.csv", "r+", encoding='utf-8').readlines()
    insert_sql = """insert into cz_ip(start_ip, end_ip, location) values('{start_ip}','{end_ip}','{location}');\t\n"""
    index = 0
    for line in src:
        m = re.match("""^(\d+\.\d+\.\d+\.\d+)\s+(\d+\.\d+\.\d+\.\d+)\s+(.*)\\n$""", line)
        if m:
            start_ip = m.group(1)
            ip_len = sum(np.array([int(x) for x in start_ip.split('.')]) * np.array([10**(3 * (3-i)) for i in range(4)]))
            params = {
                "start_ip": start_ip,
                "end_ip": m.group(2),
                "location": m.group(3),
                "ip_len":ip_len
            }
            all_sql += insert_sql.format(**params)
            index += 1
            txt += """{start_ip}@@@{end_ip}@@@{location}@@@{ip_len}###\t\n""".format(**params)
    return all_sql, txt


def write_to_local_txt():
    all_sql, txt = write_data_to_db_sql()
    with open("data/ip.txt", "w+", encoding="utf-8") as f:
        f.write(txt)
        f.close()
    ## 这个 mysql 不去执行
    with open("data/insert_sql.sql", "w+", encoding="utf-8") as f2:
        f2.write(all_sql)
        f2.close()
    print("write_Done")

write_to_local_txt()
Create_Table_SQL = """DROP TABLE IF EXISTS `cz_ip`;
    CREATE TABLE `cz_ip` (

      `start_ip` varchar(20) NOT NULL,
      `end_ip` varchar(20) NOT NULL,
      `location` varchar(150),
       `ip_len`  bigint(15),
      PRIMARY KEY (`ip_len`)
    ) ENGINE=InnoDB DEFAULT CHARSET=gbk;
    load data local infile 'A://main.py/ips/data/ip.txt' into table cz_ip 
fields terminated by '@@@'lines terminated by '###\t\n'(start_ip, end_ip, location, ip_len);
    """
```


## SQL 

```sql
DROP TABLE IF EXISTS `cz_ip`;
    CREATE TABLE `cz_ip` (

      `start_ip` varchar(20) NOT NULL,
      `end_ip` varchar(20) NOT NULL,
      `location` varchar(150),
       `ip_len`  bigint(15),
      PRIMARY KEY (`ip_len`)
    ) ENGINE=InnoDB DEFAULT CHARSET=gbk;
    load data local infile 'A://main.py/ips/data/ip.txt' into table cz_ip 
fields terminated by '@@@'lines terminated by '###\t\n'(start_ip, end_ip, location, ip_len)
```

## 二分查找 python

```python
def binnary(x, l):
    """
    返回参数在顺寻 l 中的位置; 返回前面一个值
   :param x: 确切的值
    :param l: 要对比的列表
    :return: 返回 L 中升序恰好不大于 x 的值 的 索引。
    """
    low = 0
    high = len(l) - 1
    mid = int((low + high) / 2)
    while (low <= high):
        if l[mid] < x:
            low = mid + 1
        elif l[mid] > x:
            high = mid - 1
        else:
            break
        mid = int((low + high) / 2)
    return  mid

import pymysql

MY_CONFIG = {
    'host': 'localhost',
    'port': 3306,
    'user': 'root',
    'password': '112233..',
    'db': 'chunzhen_ip',
    'charset': 'utf8',
    'cursorclass': pymysql.cursors.DictCursor,
}
import numpy as np
import pandas as pd


def BinarySearchIP(ip):

    gaim_ip_len = sum(np.array([int(x) for x in ip.split('.')]) * np.array([10 ** (3 * (3 - i)) for i in range(4)]))
    connection = pymysql.connect(**MY_CONFIG)
    corsor = connection.cursor()
    corsor.execute("""select location, ip_len from cz_ip;""")
    res = corsor.fetchall()
    corsor.close()
    connection.close()
    index = binnary(gaim_ip_len, [x["ip_len"] for x in res])

    return res[index]["location"]

# print(BinarySearchIP('172.10.1.125'))
print(BinarySearchIP('139.108.85.252'))
```

## 升级版本 4G内存查询只要11ms

> 优化查询索引

```python 
def BinarySearchIP(ip):
    gaim_ip_len = sum(np.array([int(x) for x in ip.split('.')]) * np.array([10 ** (3 * (3 - i)) for i in range(4)]))
    ############ 分为两段三段再二分
    second_duan = int(gaim_ip_len / (10**6)) * (10**6)
    mo_duan = second_duan + 255255
    array = [x["ip_len"] for x in get_data("""select ip_len from cz_ip where ip_len >= {second_duan} and ip_len <= {mo_duan};""".format(second_duan=second_duan, mo_duan=mo_duan))]
    if len(array) > 0:
        index = binnary(gaim_ip_len, array)
    else:
        params = {"first_duan": int(gaim_ip_len/ (10**9)) * 10**9, "mo_duan": int(gaim_ip_len/ (10**9) + 1) * 10**9}
        array = [x["ip_len"] for x in get_data("""select ip_len from cz_ip where ip_len >= {first_duan} and ip_len <= {mo_duan};""".format(**params))]
        index = binnary(gaim_ip_len, array)

    return get_data("""select * from cz_ip where ip_len={}""".format(array[index]))


class IPSearchHandler(tornado.web.RequestHandler):
    def get(self):
        ip = self.get_argument("ip", None)
        res = BinarySearchIP(ip)
        respon_json = tornado.escape.json_encode({"res": res})
        self.write(respon_json)
```

## 后记
- 纯真数据库是开源的, 可以自己下载对应的IP文本进行处理和测试