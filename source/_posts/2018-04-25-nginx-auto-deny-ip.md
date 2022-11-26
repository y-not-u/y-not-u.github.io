---
layout: post
title: 排序恶意请求的IP并在Nginx中Deny
author: Vogan <voganwong@gmail.com>
date: 2018-04-25
---

## 这是一个Shell脚本：

```shell
#!/bin/bash

cat /var/log/nginx/api.access.log| awk '{print $3}' | sort | uniq -c | sort -n | tail -n 100 | grep -v '-'| sed --expression='s/,//g' |awk '{if ($1>300) print "deny " $2";"}' >> /etc/nginx/conf.d/deny.conf
nginx -s reload
```

这里简单的做下解释，用于替换你自己的参数。

- `/var/log/nginx/api.access.log`：你的`access.log`位置，默认为`/var/log/nginx/access.log`
- `awk '{print $3}'`： 注意这里的`$3`，为我日志中配置的第三列`X-Forwarded-For `(用户IP, 代理服务器IP)。如果你没有自定义配置，和没有经过CDN，那么应该是第一列为用户IP，即`$1`
- `| sort | uniq -c | sort -n | tail -n 100`：排序，且只取100个请求量最大的
- `grep -v '-'`：排除`-`有特殊符号的
- `sed --expression='s/,//g'`：去除`,`（因为CDN中可能有多级代理存在）
- `awk '{if ($1>300) print "deny " $2";"}'`：取出请求量大于300的，并进行拼接格式为`deny 1.1.1.1;`
- `>> /etc/nginx/conf.d/deny.conf`：追加写入到你的ngxin.conf能都读取到的文件
- `nginx -s reload`：重启Nginx😎


## CND & Real IP

1. Nginx在CND后，获取real ip：

   正常CND会在Header里留存一个键用来放Client IP，一般为`X-Forwarded-For`。所以在Nginx的log_format中的参数为`$http_x_forwarded_for`，即可在日志中记录真实IP。

2. deny的时候不起作用？

   原因是，Nginx默认拿到的IP不是你认为的Client IP，所以你需要指定

   `real_ip_header X-Forwarded-For;`

   `set_real_ip_from 0.0.0.0/0;`

   注意下面的一句很重要，缺少也不可获取real ip。

   ​
