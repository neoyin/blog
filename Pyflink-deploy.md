---
title: Pyflink环境中项目部署
date: 2020-03-07 17:10
Author: neoyin
categories: 技术流
tags:
 - docker
 - flink
 - python
 - pyflink
permalink: pyflink-deploy
---

---



通过以上文章基本搞定了整个流程的开发，接下来需要做的就是部署项目了

我们的pyflink是一个docker的小集群. 我们的代码需要第三方的依赖 不可能在集群中预先安装好.

还好 1.10版本提供了支持 可以看一下源文件 `table_environment.py`



##### 第三方依赖

如下:

```python
@since("1.10.0")
def set_python_requirements(self, requirements_file_path, requirements_cache_dir=None):

```

```
requirements_file_path需要一个依赖文件 requirements.txt 文件中为第三方依赖如numpy==1.16.5
```

> 在代码中加入此方法 则启动时会自动下载相关依赖
> 前提是pyflink环境需要网络可下载 
> requirements_cache_dir 为缓存目录 

##### 自定义文件
上面为第三方依赖的处理,

但是我们也会依赖我们自己的代码文件 如下是一个简单的python文件 里面就是简单的打印了一条字符串

```python
from thr_fun import TE
TE()
```

如果单独部署肯定会报错 
需要在部署的时候指定依赖文件 

```python
bin/flink run -m localhost:8081 -py $path/deploy_kfk_demo.py -pyfs $path_third/thr_fun.py
或者
bin/flink run -m localhost:8081 -pym deploy_kfk_demo -pyfs $path_third
deploy_kfk_demo为执行入口
```

官方文档见 https://ci.apache.org/projects/flink/flink-docs-release-1.10/zh/ops/cli.html

https://ci.apache.org/projects/flink/flink-docs-release-1.10/dev/table/functions/udfs.html