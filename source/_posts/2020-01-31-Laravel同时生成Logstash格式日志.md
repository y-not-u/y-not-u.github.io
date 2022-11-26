---
layout: post
title: Laravel同时生成Logstash格式日志
date: 2020-01-31 22:39:15
tags: laravel
---

如果你需要将Laravel的日志倒入ELK进行统计查看，就会发现默认的日志格式并不能被Logstash认识，你需要进行各种正则判断，参数转换。如果你使用的是Laravel5.6以上版本，那么恭喜，有非常优雅的方案通过配置即可完成。

<!--more-->

这里主要用到`channels`的方案，通过再自定义话配置一种日志记录方式来实现。

## 思路

使用`stack` channel搭配，生成默认的日志和一份json形式的日志。

josn形式的日志通过**monolog**提供的方法实现。

## 代码

File: `config/logging.php`

```php
'channels' => [
  'stack' => [
      'driver' => 'stack',
      'channels' => ['daliy', 'logstash'], // channel的组合
  ],
  // ...
  'logstash' => [
      'driver' => 'monolog',
      'level' => 'debug',
      'handler' => Monolog\Handler\RotatingFileHandler::class,
      'formatter' => Monolog\Formatter\LogstashFormatter::class,
      'with' => [
          'filename' => storage_path('jsonlogs/laravel.log'), // 自定义文件存放路径
          'maxFiles' => 14 // 自定义最大文件保存数目
      ],
      'formatter_with' => [
          'applicationName' => env('APP_NAME'), // 自定义应用名称
      ]
  ],
]
```

这里看到使用`handler` 和`formatter`来搭配进行日志的处理和格式化。

其中Monolog默认库里提供可选用的有很多，具体看[文档](https://github.com/Seldaek/monolog/blob/master/doc/02-handlers-formatters-processors.md)。

另外需要注意的则是`with` 和`formatter_with`两个参数，这是为`handler`和`formatter`分别实例化提供必要参数的方式，具体提供哪些必要参数可以查看Class的代码。

这样，就可以使用**Filebeat**来检测`jsonlogs/*.log`并上传到ELK中分析。