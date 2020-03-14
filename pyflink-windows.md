---
title: Pyflink中windows
date: 2020-03-02 17:10
Author: neoyin
categories: 技术流
tags:
 - docker
 - flink
 - python
 - pyflink
permalink: pyflink-windows
---

---

Pyflink目前只有table api 如下源代码文件列表

```python
table
    __init__.py
    catalog.py
    descriptors.py
    environment_settings.py
    sinks.py
    sources.py
    sql_dialect.py
    table.py
    table_config.py
    table_environment.py
    table_schema.py
    types.py
    udf.py
    window.py
```

只能如hive 表一样对数据进行SQL操作 不能如JAVA一样写trigger 和 evictor

常用的如下

##### Tumble

```python
# Tumbling Processing-time Window (assuming a processing-time attribute "proctime")
.window(Tumble.over("10.minutes").on("proctime").alias("w"))

# Tumbling Row-count Window (assuming a processing-time attribute "proctime")
.window(Tumble.over("10.rows").on("proctime").alias("w"));
```

##### Slide

```python
# Sliding Processing-time window (assuming a processing-time attribute "proctime")
.window(Slide.over("10.minutes").every("5.minutes").on("proctime").alias("w"))

# Sliding Row-count window (assuming a processing-time attribute "proctime")
.window(Slide.over("10.rows").every("5.rows").on("proctime").alias("w"))
```

##### Session

```python
# Session Event-time Window
.window(Session.with_gap("10.minutes").on("rowtime").alias("w"))

# Session Processing-time Window (assuming a processing-time attribute "proctime")
.window(Session.with_gap("10.minutes").on("proctime").alias("w"))
```

##### Over

```python
# Unbounded Processing-time over window (assuming a processing-time attribute "proctime")
.over_window(Over.partition_by("a").order_by("proctime").preceding("unbounded_range").alias("w"))

# Unbounded Event-time Row-count over window (assuming an event-time attribute "rowtime")
.over_window(Over.partition_by("a").order_by("rowtime").preceding("unbounded_row").alias("w"))

# Bounded Processing-time over window (assuming a processing-time attribute "proctime")
.over_window(Over.partition_by("a").order_by("proctime").preceding("1.minutes").alias("w"))

# Bounded Event-time Row-count over window (assuming an event-time attribute "rowtime")
.over_window(Over.partition_by("a").order_by("rowtime").preceding("10.rows").alias("w"))
```

更多可以在官方文档中查看  https://ci.apache.org/projects/flink/flink-docs-stable/dev/table/tableApi.html#group-windows

也可以在官方github中查看其测试unit  https://github.com/apache/flink/blob/42f49482a3217287abba5e8903cf50791acf287a/flink-python/pyflink/table/tests/test_window.py

