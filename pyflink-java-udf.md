---
title: Pyflink使用JAVAUDF
date: 2020-03-20 17:10
Author: neoyin
categories: 技术流
tags:
 - docker
 - flink
 - python
 - pyflink
permalink: pyflink-java-udf

---


## 

虽然pyflink里支持python的UDF 但是有一定的局限性,比如groupby聚合算子,没法使用python 所以需要开发一下javaudf 

源码定义如下 只能是`ScalarFunction` `TableFunction` `AggregateFunction`

```python
def register_java_function(self, name, function_class_name):
"""
Registers a java user defined function under a unique name. Replaces already existing
user-defined functions under this name. The acceptable function type contains
**ScalarFunction**, **TableFunction** and **AggregateFunction**.
```


我们需要一个聚合算子 所以需要继承 AggregateFunction

新建一个JAVA工程 地址 https://git.chinawayltd.com/rdp/pyflink-adapter

```java
package com.g7.rdp.pyflink.adapter;
import org.apache.flink.table.functions.AggregateFunction;
public class ConcatAggregateFunction extends AggregateFunction<String,StrConcatAcc> {
    private static final long serialVersionUID = -2678065132752935739L;
    private static final String DELIMITER = ",";
    public void accumulate(StrConcatAcc acc, String value) throws Exception {
        if (value != null) {
            acc.list.add(value);
        }
    }
    public void merge(StrConcatAcc acc, Iterable<StrConcatAcc> its) throws Exception {
        for (StrConcatAcc otherAcc : its) {
            Iterable<String> accList = otherAcc.list.get();
            if (accList != null) {
                for (String value : accList) {
                        acc.list.add(value);
                }
            }
        }
    }
    @Override
    public StrConcatAcc createAccumulator() {
        return new StrConcatAcc();
    }
    @Override
    public String getValue(StrConcatAcc acc) {
        try {
            Iterable<String> accList = acc.list.get();
            if (accList == null || !accList.iterator().hasNext()) {
                return null;
            } else {
                StringBuilder builder = new StringBuilder();
                boolean isFirst = true;
                for (String value : accList) {
                    if (!isFirst) {
                        builder.append(DELIMITER);
                    }
                    builder.append(value);
                    isFirst = false;
                }
                return builder.toString();
            }
        } catch (Exception e) {
            throw new RuntimeException(e.getMessage(), e);
        }
    }
}
```

>  对于datastream bounded over aggregate操作，要求实现restract方法，该方法接收ACC,T等参数，返回void；对于datastream session window grouping aggregate以及dataset grouping aggregate操作，要求实现merge方法，该方法接收ACC,java.lang.Iterable<T>两个参数，返回void；对于dataset grouping aggregate操作，要求实现resetAccumulator方法，该方法接收ACC参数，返回void



开发完成后将项目打成jar包 放入 $pyflink_home/lib 下

代码中加入 `register_java_function('methond','classpath')`  python中定义调用method 和java class路径即可

https://segmentfault.com/a/1190000018109879

https://www.alibabacloud.com/help/zh/doc-detail/69553.htm

