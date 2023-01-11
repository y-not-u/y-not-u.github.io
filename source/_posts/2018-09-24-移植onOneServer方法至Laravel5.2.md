---
title: 移植onOneServer方法至Laravel5.2
tags: 
- laravel
---

> 如果您的应用程序在多个服务器上运行，则你可能会想限制计划任务仅在单个服务器上执行。 例如，您有一个计划任务，每个星期五晚上都会生成一个新报告。 如果在三台服务器上运行，则将在所有三台服务器上运行并生成三次报告。 这样不好！
>
> 为了让计划任务只在一台服务器上运行，在定义计划任务时可使用 `onOneServer` 方法。原理是获取到该任务的第一台服务器将对该任务加上原子锁，以防止其他服务器同时运行相同的任务。

<!--more-->

这是laravel-china文档上的说明。

`onOneServer()`这个方法，在5.6才有，但是在我们的服务分布式部署情况下，却是十分需要的。

## 原理

非常简单，第一个执行的节点对该任务加上原子锁。

## 实现

如何能够达到原子锁，那么最好使用redis。

在Laravel中的核心命令是: `Redis::add()`，

转换为Redis核心命令为：`setnx`，

该命令意义为：如果不存在，那么就SET。

该命令是原子性的，也就是说当第一个任务SET了值之后，后边其他相同的任务都会无法执行。

## 代码

1. 对Event类添加`onOneServer`方法及参数。

    `vendor/laravel/framework/src/Illuminate/Console/Scheduling/Event.php`

    ```php
    class Event
    {
        // ...
        // 判断参数
        public $onOneServer = false;

        // 设置函数
        public function onOneServer()
        {
            $this->onOneServer = true;

            return $this;
        }
    }

    ```

2. 运行时执行真正操作

    `vendor/laravel/framework/src/Illuminate/Console/Scheduling/ScheduleRunCommand.php`

    ```php
    /**
     * Execute the console command.
     *
     * @return void
     */
    public function fire()
    {
        // ...
        foreach ($events as $event) {
            if (! $event->filtersPass($this->laravel)) {
                continue;
            }

            if ($event->onOneServer) { // 是否只能运行在单个节点上
                $this->runSingleServerEvent($event);
            } else {
                $this->runEvent($event);
            }

            ++$eventsRan;
        }
        // ...
    }

    /**
     * 将事件进行上锁处理
     *
     * @param Event $event
     * @return void
     * @author vogan <voganwong@gmail.com>
     */
    protected function runSingleServerEvent($event)
    {
        if ($this->schedule->serverShouldRun($event)) {
            $this->runEvent($event);
        } else {
            $this->line('<info>Skipping command (has already run on another server):</info> '.$event->getSummaryForDisplay());
        }
    }
    ```

    `vendor/laravel/framework/src/Illuminate/Console/Scheduling/Schedule.php`

    ```php
    public function serverShouldRun($event)
    {
        // 创建一个锁，返回是否成功
        return $this->schedulingMutex->create($event);
    }
    ```

    `vendor/laravel/framework/src/Illuminate/Console/Scheduling/SchedulingMutex.php`

    ```php
    /**
     * 对事件创建任务锁
     *
     * @param Event $event
     * @return bool
     * @author vogan <voganwong@gmail.com>
     */
    public function create($event)
    {
        // 最终调用的即是cache->add()方法，
        // key名为锁的名称，value为true，
        // 另外设置一个超时时间，防止任务执行失败，造成了一个死锁不释放。
        return $this->cache->add(
            $event->mutexName(), true, $event->expiresAt
        );
    }
    ```
