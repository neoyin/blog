---
title: Python中重试模块retrying
date: 2021-03-01 17:10
Author: neoyin
categories: 技术流
tags:
 - python
 - retrying
permalink: python-retrying
---



利用Python Requests做网络请求时，经常有超时的问题，需要在超时的时候做一些重试机制。Python有一个重试的 retrying模块 



模块安装如下:

```python
pip install retrying
```

>  retrying是一个 Python的重试包，可以用来自动重试一些可能运行失败的程序段。retrying提供一个装饰器函数retry，被装饰的函数就会在运行失败的条件下重新执行，默认只要一直报错就会不断重试。

```python
# 示例如下

import random
from retrying import retry
@retry
def do_something_unreliable():
    if random.randint(0, 10) > 1:
        raise IOError("Broken sauce, everything is hosed!!!111one")
    else:
        return "Awesome sauce!"
print(do_something_unreliable())
```



- stop_max_attempt_number：用来设定最大的尝试次数，超过该次数就停止重试

-  stop_max_delay：比如设置成10000，那么从被装饰的函数开始执行的时间点开始，到函数成功运行结束或者失败报错中止的时间点，只要这段时间超过10秒，函数就不会再执行了

- wait_fixed：设置在两次retrying之间的停留时间

- wait_random_min和wait_random_max：用随机的方式产生两次retrying之间的停留时间

- wait_exponential_multiplier和wait_exponential_max：以指数的形式产生两次retrying之间的停留时间，产生的值为2^previous_attempt_number * wait_exponential_multiplier，previous_attempt_number是前面已经retry的次数，如果产生的这个值超过了wait_exponential_max的大小，那么之后两个retrying之间的停留值都为wait_exponential_max。这个设计迎合了exponential backoff算法，可以减轻阻塞的情况。

- 我们可以指定要在出现哪些异常的时候再去retry，这个要用retry_on_exception传入一个函数对象：

  在执行read_a_file函数的过程中，如果报出异常，那么这个异常会以形参exception传入retry_if_io_error函数中，如果exception是IOError那么就进行retry，如果不是就停止运行并抛出异常。示例如下：

  ```python
  def retry_if_io_error(exception):
      return isinstance(exception, IOError)
  @retry(retry_on_exception=retry_if_io_error)
  def read_a_file():
      with open("file", "r") as f:
          return f.read()
  ```

  > 这种方法只能判断单一的异常，而且扩展性不够高
  >
  > 我们可以指定要在得到哪些结果的时候去retry，这个要用retry_on_result传入一个函数对象：

  ```python
  # 通过返回值判断是否重试
      def retry_if_result_none(result):
          """Return True if we should retry (in this case when result is None), False otherwise"""
          # return result is None
          if result =="111":
              return True
  
      @retry(stop_max_attempt_number=5,retry_on_result=retry_if_result_none)
      def might_return_none():
          print("Retry forever ignoring Exceptions with no wait if return value is None")
          return "111"
  
      might_return_none()
  ```



##### 参考:

1. <https://www.cnblogs.com/mangM/p/11207202.html>

2. <https://github.com/rholder/retrying>

