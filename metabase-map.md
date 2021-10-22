---
title: Metabase配置地图信息
date: 2021-10-06 17:10
Author: neoyin
categories: 技术流
tags:
 - metabase
 - map
permalink: metabase-map

---

####Metabase地图展示

Metabase可添加地图数据 

可以在设置中添加地图贴片服务器URL 来实现中国区域更好的展示

目前用到了高德和谷歌的地址如下：

| 地图平台 | 坐标系 | 地图类型 |                                                          url |
| -------: | -----: | -------: | -----------------------------------------------------------: |
|     高德 |  GCJ02 |     街道 | http://wprd01.is.autonavi.com/appmaptile?x={x}&y={y}&z={z}&lang=zh_cn&size=1&scl=1&style=7&ltype=0 |
|     高德 |  GCJ02 |     卫星 | http://wprd01.is.autonavi.com/appmaptile?x={x}&y={y}&z={z}&lang=zh_cn&size=1&scl=1&style=6&ltype=0 |
|     谷歌 |  WGS84 |     街道 | http://mt0.google.cn/vt/lyrs=m&hl=zh-CN&gl=cn&x={x}&y={y}&z={z} |
|     谷歌 |  WGS84 |     卫星 | http://mt0.google.cn/vt/lyrs=s&hl=zh-CN&gl=cn&x={x}&y={y}&z={z} |

也可以添加自定义的地图 需要添加GeoJson数据

利用阿里云数据：

全国数据：

https://geo.datav.aliyun.com/areas/bound/100000_full.json

可以把一些数据下载下来

```shell
# 省份数据
wget http://datavmap-public.oss-cn-hangzhou.aliyuncs.com/areas/csv/100000_province.json
cat 100000_province.json | grep -Eo \"adcode\"\:\"\(\[0-9\]{6}\)\", > provinces.txt
sed 's/\"adcode\"\:\"/https:\/\/geo\.datav\.aliyun\.com\/areas\/bound\//g' provinces.txt > tmp.txt
sed 's/\"\,/\_full\.json/g' tmp.txt > provinces.txt && rm tmp.txt
wget -i provinces.txt

```

这样就得到了各省份数据 ，也可以直接使用

在metadata设置中添加数据地址即可应用



###### 参考

1. <https://www.metabase.com/learn/visualization/maps#pin-map>

2. <https://datav.aliyun.com/tools/atlas/index.html>
3. https://malagis.com/batch-capture-national-geojson-data.html



