---
title: Supervisor 管理进程服务重启报警
date: 2020-11-25 13:59
Author: Neo
categories: 技术流
tags:
 - linux
 - supervisor
permalink: linux-supervisord
---



#### Supervisor Event

Event 是在 Supervisor 3.0 引入的一个高级特性，如果只简单使用 Supervisor 管理进程，则不需要了解 Event。但如果希望监控 Supervisor 管理的进程的各种状态（如: 启动、退出、失败、退出状态码 …）并支持告警，才需要了解Event。

该机制主要通过一个 event listener 订阅 event 通知实现。当被 Supervisor 管理的进程有特定行为的时候，supervisor 就会自动发出对应类型的 event。即使没有配置 listener，这些 event 也是会发的；如果配置了 listener 并监听该类型的 event，那么这个 listener 就会接收到该 event。 event listener 需要自己实现，并像 program 一样，作为 superviosr 的子进程运行。



#### Event Types

| Event                  | Description                                                  |
| ---------------------- | ------------------------------------------------------------ |
| PROCESS_STATE          | 进程状态发生改变                                             |
| PROCESS_STATE_STARTING | 进程状态从其他状态转换为正在启动(Supervisord的配置项中有startsecs配置项，是指程序启动时需要程序至少稳定运行x秒才认为程序运行正常，在这x秒中程序状态为正在启动) |
| PROCESS_STATE_RUNNING  | 进程从正在启动状态转换为正在运行状态                         |
| PROCESS_STATE_BACKOFF  | 进程从正在启动状态转换为启动失败状态，Supervisor 正在重启该进程 |
| PROCESS_STATE_STOPPING | 进程从正在运行状态或正在启动状态转换为正在停止状态           |
| PROCESS_STATE_EXITED   | 进程从正在运行状态转换为退出状态，expected 退出码，如果是 0 表示进程异常退出，1 表示进程正常退出。 |
| PROCESS_STATE_STOPPED  | 进程从正在停止状态转换为已停止状态                           |
| PROCESS_STATE_FATAL    | 进程从启动失败状态(BACKOFF)转换为失败状态(FATAL). 意味着 startretries 尝试次数已达上限，Supervisor 已放弃重启该进程。 |
| PROCESS_LOG            | 进程产生日志输出，被管理的进程需配置，stdout_events_enabled=true or stderr_events_enabled=true 这个事件通知才会生效。 |
| PROCESS_LOG_STDOUT     | 进程产生标准输出，被管理的进程需配置，stdout_events_enabled=true |
| PROCESS_LOG_STDERR     | 进程产生错误输出，被管理的进程需配置，stderr_events_enabled=true |



这里以其中一个类型为例`PROCESS_STATE_EXITED`，顾名思义，当被管理的子进程退出的时候，就会产生该 event。（关于进程状态请参考 http://supervisord.org/subprocess.html#process-states ）

当 supervisord 发送一个 event 到 listener 时，会先发送一个“header”过去，类似这样

```python
ver:3.0 server:supervisor serial:21 pool:listener poolserial:10 eventname:PROCESS_COMMUNICATION_STDOUT len:54

```

header 中每项的含义：

| Key        | Description                                                  |
| ---------- | ------------------------------------------------------------ |
| var        | event 协议类型，目前 3.0                                     |
| server     | supervisor 的标识符，对应配置文件中[supervisord]块的 identifier |
| serial     | event 的序列号                                               |
| pool       | listener 的 pool 的名字，如果 listener 只启动了一个进程，也就没有 pool 的概念了 |
| poolserial | eventpool 给发送到我这个 pool 过来的 event 编的号，有点绕，只要知道与上边的 serial 不同就行了 |
| eventname  | event 类型名称                                               |
| len        | header 后面的 payload 部分的长度，又称`PAYLOAD_LENGTH`       |

对于不同的 event type，header 的结构都是一样的，而 payload 的数据结果与类型相关。
`PROCESS_STATE_EXITED`的 payload 结构如下：

```
processname:cat groupname:cat from_state:RUNNING expected:0 pid:2766Copy
```

该 payload 中每项的含义：

| Key         | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| processname | 进程名 cat [program:cat]                                     |
| groupname   | 进程组名                                                     |
| from_state  | 进程在退出前是什么状态                                       |
| expected    | 默认情况下 exitcodes=0,2，当退出码为 0 或 2 时，是 expected 的，此时该值为 1；其它的退出码，也就是 unexpected 了，该值为 0 |
| pid         | 退出的进程的 pid                                             |



#### Event Listener

编写 listener 之前，先了解一下 listener states 以及 listener 与 event 的通信协议。

##### Event Listener States

一个 listener 进程可能会处于以下三种状态：

| Name         | Description                           |
| ------------ | ------------------------------------- |
| ACKNOWLEDGED | 确认，相当于注册上了这个 listener     |
| READY        | 就绪，event 可以被发送到这个 listener |
| BUSY         | 忙碌，event 不能被发送到这个 listener |

当一个 listener 启动后，会进入`ACKNOWLEDGED`状态。然后它会向自己的 stdout 写一个`READY\n`，进入`READY`状态。supervisor 向`READY`状态的 listener 发一个 event 后，listener 进入`BUSY`状态。listener 返回`OK`或`FAIL`后，回到`ACKNOWLEDGED`状态。

##### Event Listener Notification Protocol

1. listener 处于`READY`时，当 supervisord 产生的 event 在 listener 的配置的可接受的 events 中时，supervisord 就会把该 event 发送给该 listener，并将其状态置为`BUSY`。
2. listener 先处理`header`获取`len`并据其读取`payload`，处理`payload`中的数据，这时候如果有相同类型的 event 产生，supersivor 会将该 event 发给相同 listener pool 中的其他 listener。
3. listerner 处理完数据后，要向自己的 stdout 中写一条消息以告诉 supervisor 处理结果，例如`RESULT 2\nOK`或`RESULT 4\nFAIL`
4. supervisor 收到 listener 返回的结果，若为`OK`就认为处理成功；若为`FAIL`，认为 event 处理失败，会把那个 event 再次放入缓存队列并稍后再次发送。不管收到`OK`还是`FAIL`，这个 listener 都会被置为`ACKNOWLEDGED`状态。
5. listener 被置为`ACKNOWLEDGED`状态后，这个 listener 进程可以退出并稍后自启（配置中`autorestart=true`的话），也可以继续运行。如果要继续运行，则它必须立即向自己的 stdout 中发送`READY`以让 supervisor 将其状态置为`READY`。

##### Writing an Event Listener

官方demo 

```python
import sys

def write_stdout(s):
    sys.stdout.write(s)
    sys.stdout.flush()

def write_stderr(s):
    sys.stderr.write(s)
    sys.stderr.flush()

def main():
    while 1:
        write_stdout('READY\n') # transition from ACKNOWLEDGED to READY
        line = sys.stdin.readline()  # read header line from stdin
        write_stderr(line) # print it out to stderr
        headers = dict([ x.split(':') for x in line.split() ])
        data = sys.stdin.read(int(headers['len'])) # read the event payload
        write_stderr(data) # print the event payload to stderr
        write_stdout('RESULT 2\nOK') # transition from READY to ACKNOWLEDGED

if __name__ == '__main__':
    main()
    import sys
```

监控进程退出并发报警邮件的 listener:（改自 [superlance](https://pypi.python.org/pypi/superlance) 中的 crashmail.py）

```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

# A event listener meant to be subscribed to PROCESS_STATE_CHANGE
# events.  It will send mail when processes that are children of
# supervisord transition unexpectedly to the EXITED state.

import os
import socket
import sys
from supervisor import childutils


def usage():
    print doc
    sys.exit(255)


class CrashMail:
    def __init__(self, programs, any, email, sendmail, optionalheader):

        self.programs = programs
        self.any = any
        self.email = email
        self.sendmail = sendmail
        self.optionalheader = optionalheader
        self.stdin = sys.stdin
        self.stdout = sys.stdout
        self.stderr = sys.stderr

    def runforever(self, test=False):
        # 死循环, 处理完 event 不退出继续处理下一个
        while 1:
            # 使用 self.stdin, self.stdout, self.stderr 代替 sys.* 以便单元测试
            headers, payload = childutils.listener.wait(self.stdin, self.stdout)

            if test:
                self.stderr.write(str(headers) + '\n')
                self.stderr.write(payload + '\n')
                self.stderr.flush()

            if not headers['eventname'] == 'PROCESS_STATE_EXITED':
                # 如果不是 PROCESS_STATE_EXITED 类型的 event, 不处理, 直接向 stdout 写入"RESULT\nOK"
                childutils.listener.ok(self.stdout)
                continue

            # 解析 payload, 这里我们只用这个 pheaders.
            # pdata 在 PROCESS_LOG_STDERR 和 PROCESS_COMMUNICATION_STDOUT 等类型的 event 中才有
            pheaders, pdata = childutils.eventdata(payload + '\n')

            # 过滤掉 expected 的 event, 仅处理 unexpected 的
            # 当 program 的退出码为对应配置中的 exitcodes 值时, expected=1; 否则为0
            if int(pheaders['expected']):
                childutils.listener.ok(self.stdout)
                continue

            hostname = socket.gethostname()
            ip = socket.gethostbyname(hostname)
            # 构造报警内容
            msg = "Host: %s(%s)\nProcess: %s\nPID: %s\nEXITED unexpectedly from state: %s" % \
                  (hostname, ip, pheaders['processname'], pheaders['pid'], pheaders['from_state'])

            subject = ' %s crashed at %s' % (pheaders['processname'],
                                             childutils.get_asctime())
            if self.optionalheader:
                subject = '[' + self.optionalheader + ']' + subject

            self.stderr.write('unexpected exit, mailing\n')
            self.stderr.flush()

            self.mail(self.email, subject, msg)

            # 向 stdout 写入"RESULT\nOK"，并进入下一次循环
            childutils.listener.ok(self.stdout)

    # 发送邮件, 可以用自己的, 也可以抽出来作为一个 module 复用
    def mail(self, email, subject, msg):
        body = 'To: %s\n' % self.email
        body += 'Subject: %s\n' % subject
        body += '\n'
        body += msg
        m = os.popen(self.sendmail, 'w')
        m.write(body)
        m.close()
        self.stderr.write('Mailed:\n\n%s' % body)
        self.mailed = body


def main(argv=sys.argv):
    # 参数解析
    import getopt
    short_args = "hp:ao:s:m:"
    long_args = [
        "help",
        "program=",
        "any",
        "optionalheader="
        "sendmail_program=",
        "email=",
    ]
    arguments = argv[1:]
    try:
        opts, args = getopt.getopt(arguments, short_args, long_args)
    except:
        usage()

    programs = []
    any = False
    sendmail = '/usr/sbin/sendmail -t -i'
    email = None
    optionalheader = None

    for option, value in opts:

        if option in ('-h', '--help'):
            usage()

        if option in ('-p', '--program'):
            programs.append(value)

        if option in ('-a', '--any'):
            any = True

        if option in ('-s', '--sendmail_program'):
            sendmail = value

        if option in ('-m', '--email'):
            email = value

        if option in ('-o', '--optionalheader'):
            optionalheader = value

    # listener 必须交由 supervisor 管理, 自己运行是不行的
    if not 'SUPERVISOR_SERVER_URL' in os.environ:
        sys.stderr.write('crashmail must be run as a supervisor event '
                         'listener\n')
        sys.stderr.flush()
        return

    prog = CrashMail(programs, any, email, sendmail, optionalheader)
    prog.runforever(test=True)


if __name__ == '__main__':
    main()


## Usage
doc = """\
crashmail.py [-p processname] [-a] [-o string] [-m mail_address]
             [-s sendmail] URL

Options:

-p -- specify a supervisor process_name.  Send mail when this process
      transitions to the EXITED state unexpectedly. If this process is
      part of a group, it can be specified using the
      'process_name:group_name' syntax.

-a -- Send mail when any child of the supervisord transitions
      unexpectedly to the EXITED state unexpectedly.  Overrides any -p
      parameters passed in the same crashmail process invocation.

-o -- Specify a parameter used as a prefix in the mail subject header.

-s -- the sendmail command to use to send email
      (e.g. "/usr/sbin/sendmail -t -i").  Must be a command which accepts
      header and message data on stdin and sends mail.  Default is
      "/usr/sbin/sendmail -t -i".

-m -- specify an email address.  The script will send mail to this
      address when crashmail detects a process crash.  If no email
      address is specified, email will not be sent.

The -p option may be specified more than once, allowing for
specification of multiple processes.  Specifying -a overrides any
selection of -p.

A sample invocation:

crashmail.py -p program1 -p group1:program2 -m dev@example.com

"""
```

#### 坑 & 爬坑

虽然 eventlistener 在配置文件中的地位上和 program 是相同的，但是待遇那可是不一样的。在使用的过程中我就遇到了两个坑，现记录如下。

##### 1 号坑

**1 号坑属性：**
如果修改修改一个 program 的配置，使用`supervisorctl update`即可载入最新的配置并按需重启对应的 program，在终端里的表现如下：

```
$ supervisorctl update
TestCrashMail: stopped
TestCrashMail: updated process group
$Copy
```

但是对于一个 eventlistener，当修改完对应的配置后，使用`supervisorctl update`是毫无反应的：

```
supervisorctl updateCopy
```

`restart`一下这个 listener？图样图森破，`start`、`restart`、`stop`这三个命令都是不会载入最新的配置文件的！

瞅了全部的`supervisorctl`的命令，发现只有一个能让 listener “满血复活”的——`reload`。但是这个命令是重启 supervisord 的，被其管理的所有程序当然会全部重启，如果你只有一两个程序是用 supervisord 管理的，还能忍受；但是如果像我这样有几十个程序的，显然这么做不合适。那怎么办？

**爬坑攻略：**
首先该方法只适用于配置分离的情况（至于把 eventlistener 直接写到 supervisord 的配置文件中是否有这个坑还不确定）。

比如我的 supervisord.conf 文件中有如下配置：

```
[include]
files = /opt/supervisor/conf.d/*.confCopy
```

`/opt/supervisor/conf.d/`文件夹下有一个 listener 的配置`crashmail.conf`，那么通过以下命令即可顺利爬出坑：

```
$ mv crashmail.conf crashmail.bak
$ supervisorctl update
crashmail: stopped
crashmail: removed process group
$ mv crashmail.bak crashmail.conf
$ supervisorctl update
crashmail: added process groupCopy
```

##### 2 号坑

坑前提示：只有始终运行的 listener 会遇到该坑；运行一次后退出并自动重启的 listener 无视此坑。

**2 号坑属性：**
一般配置 program 的时候，我们会配置`redirect_stderr=true`选项以使`stderr`重定向到`stdout`中，这样可以少一个 log 文件，方便查看。
但是如果 listener 中配置`redirect_stderr=true`就可能会出问题——取决于你的 listener 在处理的过程中会不会向`stderr`中有输出。
具体现象就是该 listener 只能处理一个 event，然后就卡在那里了，如果你重启了该 listener，它还会接收到上次的那个 event，进行处理后如此循环。

**爬坑攻略：**
对于 eventlistener，不要配置`redirect_stderr=true`。supervisor 的 event 通信协议比较特殊，需要从`stdout`中的输出来判断 listener 的状态（详见上文中的详解），所以`stderr`重定向到`stdout`的输出可能会干扰 supervisor 对 listener 状态的判断。

**2015-12-24 更新**：3.2.0 版本中已经把`[eventlistener:x]`中的`redirect_stderr=true`设置禁用了，如果设置了的话会在启动 supervisord 时出错：

```
$ supervisord -c /etc/supervisord.conf
Error: [eventlistener:listener-crashmail] section sets redirect_stderr=true but this is not allowed because it will interfere with the eventlistener protocol
For help, use /usr/local/bin/supervisord -hCopy
```





#### 参考

- http://supervisord.org/events.html
- https://www.haoyizebo.com/posts/7f55f825/
- https://www.cnblogs.com/linxiyue/p/8121982.html
- https://blog.csdn.net/fgf00/article/details/104579788