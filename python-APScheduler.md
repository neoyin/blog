---
title: Python中任务调度模块APScheduler
date: 2021-03-04 17:10
Author: neoyin
categories: 技术流
tags:
 - python
 - APScheduler
permalink: python-apscheduler
---



> APScheduler是基于Quartz的一个Python定时任务框架。提供了基于日期、固定时间间隔以及crontab类型的任务，并且可以持久化任务。
> 在线文档：https://apscheduler.readthedocs.io/en/latest/userguide.html

##### 安装APScheduler

`pip install apscheduler`

##### 基本架构

1. 触发器 triggers：设定触发任务的条件

   > 描述一个任务何时被触发，按日期或按时间间隔或按 cronjob 表达式三种方式触发

2. 任务存储器 job stores：存放任务，可以放内存(默认)或数据库

   > 注：调度器之间不能共享任务存储器

3. 执行器 executors：用于执行任务，可设定执行模式

   > 将指定的作业提交到线程池或者进程池中运行，任务完成通知调度器触发相应的事件。

4. 调度器 schedulers：将上方三个组件作为参数，创建调度器实例执行。

   > 协调三个组件的运行。

##### 调度器组件(schedulers)

- `BlockingScheduler`阻塞式调度器

> 调度程序是进程中唯一运行的进程，调用start函数会阻塞当前线程，不能立即返回

```python
from apscheduler.schedulers.blocking import BlockingScheduler
import time

scheduler = BlockingScheduler()

def job1():
    print "%s: 执行任务"  % time.asctime()

scheduler.add_job(job1, 'interval', seconds=3)
scheduler.start()
```

- `BackgroundScheduler`后台调度器

  > 当前线程不会阻塞，调度器后台执行

```python
from apscheduler.schedulers.background import BackgroundScheduler
import time

scheduler = BackgroundScheduler()
 
def job1():
    print "%s: 执行任务"  % time.asctime()

scheduler.add_job(job1, 'interval', seconds=3)
scheduler.start()
time.sleep(10)
```

- `AsyncIOScheduler`AsyncIO调度器

  > 适用于使用了asyncio的情况

- `GeventScheduler`Gevent调度器

  > 使用了Gevent的情况

- `TornadoScheduler`Tornado调度器

  > 适用于构建Tornado应用

- `TwistedScheduler` Twisted调度器

  > 适用于构建Twisted应用

- `QtScheduler` Qt调度器

  > 适用于构建Qt应用

##### 触发器组件(trigger)

1. `date`：只在某个时间点执行一次，具体日期

   > `run_date(datetime|str)`
   >
   > ```python
   > Copyscheduler.add_job(my_job, 'date', run_date=datetime(2019, 7, 12, 15, 30, 5), args=[])
   > scheduler.add_job(my_job, 'date', run_date="2019-07-12", args=[])
   > ```
   >
   > `timezone` 指定时区

2. `interval`：每隔一段时间允许一次，时间间隔

   > `weeks=0 | days=0 | hours=0 | minutes=0 | seconds=0, start_date=None, end_date=None, timezone=None`
   >
   > ```python
   > Copyscheduler.add_job(my_job, 'interval', hours=2)
   > scheduler.add_job(my_job, 'interval', hours=2, start_date='2017-9-8 21:30:00', end_date='2018-06-15 21:30:00)
   > ```

3. `cron`：任务的运行周期

  > `(year=None, month=None, day=None, week=None, day_of_week=None, hour=None, minute=None, second=None, start_date=None, end_date=None, timezone=None)`
  >
  > 除了week和 day_of_week，它们的默认值是`*`
  >
  > 例如`day=1, minute=20`，这就等于`year='*', month='*', day=1, week='*', day_of_week='*', hour='*', minute=20, second=0`，工作将在每个月的第一天以每小时20分钟的时间执行

4. 表达式

  | 表达式 | 参数类型 | 描述                                     |
  | ------ | -------- | ---------------------------------------- |
  | *      | 所有     | 通配符。例：`minutes=*`即每分钟触发      |
  | */a    | 所有     | 可被a整除的通配符。                      |
  | a-b    | 所有     | 范围a-b触发                              |
  | a-b/c  | 所有     | 范围a-b，且可被c整除时触发               |
  | xth y  | 日       | 第几个星期几触发。x为第几个，y为星期几   |
  | last x | 日       | 一个月中，最后个星期几触发               |
  | last   | 日       | 一个月最后一天触发                       |
  | x,y,z  | 所有     | 组合表达式，可以组合确定值或上方的表达式 |

> 注：当设置的时间间隔小于，任务的执行时间，线程会阻塞住，等待执行完了才能执行下一个任务，可以设置`max_instance`指定一个任务同一时刻有多少个实例在运行，默认为1



##### 参考

1. <https://www.cnblogs.com/mangM/p/11187015.html>
2. <https://www.cnblogs.com/gdjlc/p/11432526.html>
3. <https://www.biaodianfu.com/python-schedule.html>

