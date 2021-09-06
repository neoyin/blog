---
title: Hive命令导出数据
date: 2021-05-23 17:10
Author: neoyin
categories: 技术流
tags:
 - hive
permalink: hive-export-csv
---



Hive 导出csv文件 

```sql
Hive -e 'select * from your_Table' | sed 's/[\t]/,/g'  > /home/yourfile.csv
```

可以在`SELECT`之前指定属性`set Hive.cli.print.header=true`，以确保创建标头以及数据并将其复制到文件中。例如：

```sql
Hive -e 'set Hive.cli.print.header=true; select * from your_Table' | sed 's/[\t]/,/g'  > /home/yourfile.csv
```

如果您使用的是Hive 11或更高版本，则可以将`INSERT`语句与`LOCAL`关键字一起使用。

```sql
insert overwrite local directory '/home/carter/staging' row format delimited fields terminated by ',' select * from hugetable;
# 这里会生成大量的随机文件 需要合并 
cat /LocalPath/* > /LocalPath/table.csv

```

或者利用Beeline

```
beeline -u "jdbc:hive2://${ip}" -n anbao -p Algorithm_at_g7 --silent=true --outputformat=csv2 --incremental=true -e 'select geohash,course,road_class,province,city,county from dwd_device.dwd_roadtype_baidu_fixed_geohash_f_p_weekly where geohash_type="bd_7";' >  /data/roadtype_data/db/bd_10_cource.csv
```



##### 参考

1. <https://www.it-swarm.cn/zh/csv/%E5%A6%82%E4%BD%95%E5%B0%86hive%E8%A1%A8%E5%AF%BC%E5%87%BA%E5%88%B0csv%E6%96%87%E4%BB%B6%EF%BC%9F/1073244602/>
2. <https://www.docs4dev.com/docs/zh/apache-hive/3.1.1/reference/LanguageManual_WindowingAndAnalytics.html>

