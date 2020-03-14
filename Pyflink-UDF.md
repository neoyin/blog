---
title: Pyflink中使用UDF
date: 2020-03-07 17:10
Author: neoyin
categories: 技术流
tags:
 - docker
 - flink
 - python
 - pyflink
permalink: pyflink-udf
---

---

Pyflink1.10 已经支持python lambda udf的开发 也可以调用javaudf 和 ScalarFunction

python udf比较简单



### Python UDF

如下: input_types 为输入 注意此处为复数时是数组 result_type 为输出 在这里进行一个简单的print.

```python
@udf(input_types=DataTypes.STRING(), result_type=DataTypes.STRING(), name="pr")
def pr(str):
    print(str)
    return str
```

需要先进行注册后调用 

```python
st_env.register_function('pr',pr) 
st_env.from_path('source') \
.window(Session.with_gap('20.minutes').on('rowtime').alias('w'))\
.group_by('w,carnum')\
.select('carnum,cast(time.count ,int) as count, pr(carnum) as t')\
.insert_into('mySink')
```

### Java UDF

java udf 必须继承 ScalarFunction 并且有无参构造器 且方法名必须为eval

```java
public class HashCode extends ScalarFunction {
	
	private int factor = 12;
  
  public int eval(String s) {
    return s.hashCode() * factor;
  }
}
```


注册如下 调用如python udf 
```table_env.register_java_function("hashCode", "my.java.function.HashCode")```


需要将文件编译打包后放入pyflink环境中 $pyflink_home/lib 下

### Scala UDF

scala udf 方法申明跟JAVA类似 方法注册跟Python类似

直接在python文件中写代码 定义一个class 继承ScalarFunction 方法名为eval 

```scala
class Add(ScalarFunction):
	def eval(self, i, j):
	return i + j

add = udf(Add(), [DataTypes.BIGINT(), DataTypes.BIGINT()], DataTypes.BIGINT())
调用如 table_env.register_function("add", add)
```

其它见官方文档 https://ci.apache.org/projects/flink/flink-docs-release-1.10/dev/table/functions/udfs.html

官方测试用例 https://github.com/apache/flink/blob/42f49482a3217287abba5e8903cf50791acf287a/flink-python/pyflink/table/tests/test_udtf.py