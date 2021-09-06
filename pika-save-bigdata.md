---
title: 利用Pika存储查询大数据
date: 2021-05-21 17:10
Author: neoyin
categories: 技术流
tags:
 - bigdata
 - pika
permalink: pika-save-bigdata
---



Pika是一个可持久化的大容量redis存储服务，兼容string、hash、list、zset、set的绝大部分接口，解决redis由于存储数据量巨大而导致内存不够用的容量瓶颈.

Github地址：<https://github.com/OpenAtomFoundation/pika>



##### 安装启动

```shell
 # 先安装相关依赖
 yum install snappy-devel glog-devel
 yum install gcc-c++
# 下载对应版本的编译后的包文件 https://github.com/OpenAtomFoundation/pika/releases/tag/v3.3.6
wget https://github.com/OpenAtomFoundation/pika/releases/download/v3.3.6/pika-linux-x86_64-v3.3.6.tar.bz2
tar jxvf pika-linux-x86_64-v3.3.6.tar.bz2
# 启动服务
./output/bin/pika -c ./conf/pika.conf
```



##### 配置文件说明

```shell
# pika 端口
port : 9221

# pika是多线程的, 该参数能够配置pika的线程数量, 不建议配置值超过部署服务器的CPU核心数量 
thread-num : 1

# 处理命令用户请求命令线程池的大小
thread-pool-size : 8

# sync 主从同步时候从库执行主库传递过来命令的线程数量
sync-thread-num : 6

# sync 处理线程的任务队列大小, 不建议修改
sync-buffer-size : 10

# Pika日志目录, 用于存放INFO, WARNING, ERROR日志以及用于同步的binlog(write2fine)文件
log-path : ./log/

# Pika数据目录
db-path : ./db/

# Pika 底层单个rocksdb单个memtable的大小, 设置越大写入性能越好但会在buffer刷盘时带来更大的IO负载, 请依据使用场景合理配置
[RocksDb-Tuning-Guide](https://github.com/facebook/rocksdb/wiki/RocksDB-Tuning-Guide)
write-buffer-size : 268435456

# Pika 的连接超时时间配置, 单位为秒, 当连接无请求时(进入sleep状态)开始从配置时间倒计时, 当倒计时为0时pika将强行
# 断开该连接, 可以通过合理配置该参数避免可能出现的pika连接数用尽问题, 该参数默认值为60
timeout : 60

# 密码管理员密码, 默认为空, 如果该参数与下方的userpass参数相同(包括同时为空), 则userpass参数将自动失效, 所有用户均为
# 管理员身份不受userblacklist参数的限制
requirepass : password

# 同步验证密码, 用于slave(从库)连接master(主库)请求同步时进行验证, 该参数需要与master(主库)的requirepass一致
masterauth :

# 用户密码, 默认为空, 如果该参数与上方的userpass参数相同(包括同时为空), 则本参数将自动失效, 所有用户均为管理员身份不
# 受userblacklist参数的限制
userpass : userpass
 
# 指令黑名单, 能够限制通过userpass登录的用户, 这些用户将不能使用黑名单中的指令, 指令之间使用","隔开, 默认为空
# 建议将高风险命令配置在该参数中
userblacklist : FLUSHALL, SHUTDOWN, KEYS, CONFIG

# 分为经典模式和分片模式，[classic | sharding]，经典模式中支持多db的配置
instance-mode : classic

# 经典模式下下指定db的数量，使用方式和redis一致
databases : 1

# 分片模式下每一个table中默认的slot数量
default-slot-num：16

# 定义一个副本组又多少个从副本，目前支持的配置选项范围[0, 1, 2, 3, 4], 0代表不开启此功能
replication-num : 0

# 定义在返回客户端之前主副本收到多少个从副本的ACK反馈信息。目前可以配置的选项范围[0, ...replicaiton-num]，0代表不开启此功能。
consensus-level : 0

# Pika的dump文件名称前缀, bgsave后生成的文件将以该前缀命名
dump-prefix : backup-
 
# 守护进程模式  [yes | no]
daemonize : yes
 
# slotmigrate  [yes | no], pika3.0.0暂不支持该参数
#slotmigrate : no

# Pika dump目录设置, bgsave后生成的文件将存放在该目录中
dump-path : /data1/pika9001/dump/

# dump目录过期时间, 单位为天, 默认为0即永不过期
dump-expire: 0

# pidfile Path pid文件目录
pidfile : /data1/pika9001/pid/9001.pid
 
# pika最大连接数配置参数
maxclients : 20000
 
# rocks-db的sst文件体积, sst文件是层级的, 文件越小, 速度越快, 合并代价越低, 但文件数量就会超多, 而文件越大, 速度相对变慢, 合并代价大, 但文件数量会很少, 默认是 20M
target-file-size-base : 20971520

# binlog(write2file)文件保留时间, 7天, 最小为1, 超过7天的文件会被自动清理
expire-logs-days : 7
 
# binlog(write2file)文件最大数量, 200个, 最小为10, 超过200个就开始自动清理, 始终保留200个
expire-logs-nums : 200
 
# root用户连接保证数量：2个, 即时Max Connection用完, 该参数也能确保本地（127.0.0.1）有2个连接可以同来登陆pika
root-connection-num : 2
 
# 慢日志记录时间, 单位为微秒, pika的慢日志记录在pika-ERROR.log中, pika没有类似redis slow log的慢日志提取api
slowlog-log-slower-than : 10000

# slave是否是只读状态(yes/no, 1/0)
# slave-read-only : 0

# Pika db 同步路径配置参数
db-sync-path : ./dbsync/

# 该参数能够控制全量同步时的传输速度, 合理配置该参数能够避免网卡被用尽, 该参数范围为1~125, 意为:1mb~125mb，当该参数
# 被配置为小于0或大于125时, 该参数会被自动配置为125
db-sync-speed : -1

# 指定网卡
# network-interface : eth1

# 同步参数配置, 适用于从库节点(slave), 该参数格式为ip:port, 例如192.168.1.2:6666, 启动后该示例会自动向192.168.1.2的
# 6666端口发送同步请求
# slaveof : master-ip:master-port

# 配置双主或Hub需要的server id, 不使用双主或Hub请忽略该参数
server-id : 1

# 双主配置, 不使用双主请忽略以下配置
double-master-ip :	双主对端Ip
double-master-port :	双主对端Port
double-master-server-id :	双主对端server id
 
# 自动全量compact, 通过配置的参数每天定时触发一次自动全量compact, 特别适合存在多数据结构大量过期、删除、key名称复用的场景
# 参数格式为:"启动时间(小时)-结束时间(小时)/磁盘空余空间百分比", 例如你需要配置一个每天在凌晨3点~4点之间自动compact的任务
# 同时该任务仅仅在磁盘空余空间不低于30%的时候执行, 那么应配置为:03-04/30, 该参数默认为空
compact-cron : 

# 自动全量compact, 该参与与compact-cron的区别为, compact-cron每天仅在指定时间段执行, 而compact-interval则以配置时间为周期
# 循环执行, 例如你需要配置一个每4小时执行一次的自动compact任务, 同时该任务仅仅在磁盘空余空间不低于30%的时候执行, 那么该参
# 数应配置为:4/30, 该参数默认为空
compact-interval :

# 从库实例权重设置, 仅配合哨兵使用,无其它功能, 权重低的slave会优先选举为主库, 该参数默认为0(不参与选举)
slave-priority : 

# 该参数仅适用于pika跨版本同步时不同版本的binlog能够兼容并成功解析, 该参数可配置为[new | old]
# 当该参数被配置为new时, 该实例仅能作为3.0.0及以上版本pika的从库, 与pika2.3.3~2.3.5不兼容
# 当该参数被配置为old时, 该时候仅能作为2.3.3~2.3.5版本pika的从库, 与pika3.0.0及以上版本不兼容
# 该参数默认值为new, 该参数可在没有配置同步关系的时候通过config set动态调整, 一旦配置了同步关系则不可动态修改
# 需要先执行slaveof no one关闭同步配置, 之后即可通过config set动态修改
identify-binlog-type : new

# 主从同步流量控制的的窗口，主从高延迟情形下可以通过提高该参数提高同步性能。默认值9000最大值90000。
sync-window-size : 9000

# 处理客户端连接请求的最大缓存大小，可配置的数值为67108864(64MB) 或 268435456(256MB) 或 536870912(512MB)
# 默认是268435456(256MB)，需要注意的是主从的配置需要一致。
# 单条命令超过此buffer大小，服务端会自动关闭与客户端的连接。
max-conn-rbuf-size : 268435456

###################
#Critical Settings#
#    危险参数      #
###################
# write2file文件体积, 默认为100MB, 一旦启动不可修改,  limited in [1K, 2G]
binlog-file-size : 104857600

# 压缩方式[snappy, zlib, lz4, zstd]默认为snappy, 一旦启动不可修改
# 官方发布的二进制提供默认的snaapy的静态连接。如果需要其他压缩方式请自行下载相应静态库并进行编译。
compression : snappy

# 指定后台flush线程数量, 默认为1, 范围为[1, 4]
max-background-flushes : 1

# 指定后台压缩线程数量, 默认为1, 范围为[1, 4]
max-background-compactions : 1

# DB可以使用的打开文件的数量, 默认为5000
max-cache-files : 5000

# pika实例所拥有的rocksdb实例使用的memtable大小上限，如果rocksdb实际使用超过这个数值，下一次写入会造成刷盘
[Rocksdb-Basic-Tuning](https://github.com/facebook/rocksdb/wiki/Setup-Options-and-Basic-Tuning)
max-write-buffer-size : 10737418240

# 限制命令返回数据的大小，应对类似于keys *等命令，返回值过大将内存耗尽。
max-client-response-size : 1073741824

# pika引擎中层级因子, 用于控制每个层级与上一层级总容量的倍数关系, 默认为10(倍), 允许调整为5(倍) 
max-bytes-for-level-multiplier : 10

# 统计对于key的操作次数，对于操作频繁的一部分key做小规模compaction
# max-cache-statistic-keys 为受监控key的数量，配置为0代表关闭此功能
max-cache-statistic-keys : 0

# 如果开启小规模compaction，如果对于key操作次数超过small-compaction-threshold上限，那么对该key进行compaction
small-compaction-threshold : 5000
```

##### 目录结构

- db目录
> 用于存放pika的所有数据文件，包含5个子目录（5大数据类型）它们是：kv，set，zset，hash，list，从pika3.0.0开始，这些数据结构目录为：hashes，lists，sets，strings，zsets
- log目录
> 用于存放所有日志文件，包含：一般日志、警告日志、错误日志、同步日志（binlog）、同步日志节点信息文件（mainfest）
- dump目录
> 用于存放快照式备份产生的文件
- pid目录
> 用于存放pika的pid文件
- dbsync目录
> 用于主从全量同步时存放全量同步所需的文件

##### `INFO`命令

```tex
127.0.0.1:9221> info [section]
#主库：
# Server
pika_version:2.3.0 -------------------------------------------- pika 版本信息
pika_git_sha:3668a2807a3d047ea43656b58a2130c1566eeb65 --------- git的sha值
pika_build_compile_date: Nov 14 2017 -------------------------- pika的编译日期
os:Linux 2.6.32-2.0.0.8-6 x86_64 ------------------------------ 操作系统信息
arch_bits:64 -------------------------------------------------- 操作系统位数
process_id:12969 ---------------------------------------------- pika pid信息
tcp_port:9001 ------------------------------------------------- pika 端口信息
thread_num:12 ------------------------------------------------- pika 线程数量
sync_thread_num:6 --------------------------------------------- sync线程数量
uptime_in_seconds:3074 ---------------------------------------- pika 运行时间（秒）
uptime_in_days:0 ---------------------------------------------- pika 运行时间（天）
config_file:/data1/pika9001/pika9001.conf --------------------- pika conf文件位置
server_id:1 --------------------------------------------------- pika的server id

# Data
db_size:770439 ------------------------------------------------ db的大小(Byte)
db_size_human:0M ---------------------------------------------- 人类可读的db大小(M)
compression:snappy -------------------------------------------- 压缩方式
used_memory:4248 ---------------------------------------------- 使用的内存大小(Byte)
used_memory_human:0M ------------------------------------------ 人类可读的使用的内存大小(M)
db_memtable_usage:4120 ---------------------------------------- memtable的使用量(Byte)
db_tablereader_usage:128 -------------------------------------- tablereader的使用量(Byte)

# Log
log_size:110174 ----------------------------------------------- binlog的大小(Byte)
log_size_human:0M --------------------------------------------- 人类可读的binlog大小(M)
safety_purge:none --------------------------------------------- 目前可以安全删除的文件名
expire_logs_days:7 -------------------------------------------- 设置binlog过期天数
expire_logs_nums:10 ------------------------------------------- 设置binlog过期数量
binlog_offset:0 388 ------------------------------------------- binlog偏移量(文件号，偏移量)
 
# Clients
connected_clients:2 ------------------------------------------- 当前连接数
 
# Stats
total_connections_received:18 --------------------------------- 总连接次数统计
instantaneous_ops_per_sec:1 ----------------------------------- 当前qps
total_commands_processed:633 ---------------------------------- 请求总计
is_bgsaving:No, , 0 ------------------------------------------- pika 备份信息：是否在备份,备份名称，备份
is_scaning_keyspace:No ---------------------------------------- 是否在执行scan操作
is_compact:No ------------------------------------------------- 是否在执行数据压缩操作
compact_cron: ------------------------------------------------- 定时compact(format: start-end/ratio, eg. 02-04/60)
compact_interval: --------------------------------------------- compact的间隔(format: interval/ratio, eg. 6/60)

# CPU
used_cpu_sys:48.52 -------------------------------------------- Pika进程系统CPU占用时间
used_cpu_user:73.10 ------------------------------------------- Pika进程用户CPU占用时间
used_cpu_sys_children:0.05 ------------------------------------ Pika子进程系统CPU占用时间
used_cpu_user_children:0.05 ----------------------------------- Pika子进程用户CPU占用时间
 
# Replication(MASTER)
role:master --------------------------------------------------- 本实例角色
connected_slaves:1 -------------------------------------------- 当前从库数量
slave0:ip=192.168.1.1,port=57765,state=online,sid=2,lag=0 ----- lag：当前主从binlog相差的字节数（byte），如果有多个从库则依次展示
 
#从库（区别仅在于同步信息的展示）：
# Replication(SLAVE)
role:slave ---------------------------------------------------- 本实例角色
master_host:192.168.1.2 --------------------------------------- 主库IP
master_port:9001 ---------------------------------------------- 主库端口
master_link_status:up ----------------------------------------- 当前同步状态
slave_read_only:1 --------------------------------------------- 从库是否readonly
repl_state: connected ----------------------------------------- 从库同步连接的当前状态
 
# Keyspace（key数量展示，按照数据类型分类展示，默认不更新，仅在执行info keyspace 1的时候刷新该信息）
# Time:2016-04-22 17:08:33 ------------------------------------ 上一次统计的时间
db0 Strings_keys=100004, expires=0, invaild_keys=0
db0 Hashes_keys=2, expires=0, invaild_keys=0
db0 Lists_keys=0, expires=0, invaild_keys=0
db0 Zsets_keys=1, expires=0, invaild_keys=0
db0 Sets_keys=0, expires=0, invaild_keys=0
# keys：当前有效KEY的数量，等同于redis的keys 
# expires：keys中带有expire属性的key的数量，等同于Redis 
# invalid_keys：pika独有，指已经失效（标记删除），但还未被rocksdb彻底物理删除的key，虽然这些key不再会被访问到，但会占用一定磁盘空间，如果发现较大可以通过执行compact来彻底清理

# DoubleMaster(MASTER)
role:master --------------------------------------------------- 双主角色
the peer-master host: ----------------------------------------- 双主对端IP
the peer-master port:0 ---------------------------------------- 双主对端Port
the peer-master server_id:0 ----------------------------------- 双主对端server id
double_master_mode: False ------------------------------------- 是否配置双主模式
repl_state: 0 ------------------------------------------------- 双主连接状态
double_master_recv_info: filenum 0 offset 0 ------------------- 从对端接受的Binlog偏移量
```

##### 其它命令

```
client kill all 删除全部的客户端
bgsave 执行方式和redis一样。但是异步dump完后，数据库保存在dump_path目录下，dump出来的数据库包含dump_prefix和dump时间等信息；
delbackup 删除dump目录下除正在使用（全同步中）的db快照外的其他快照
info keyspace 执行方式是“info keyspace 1”，“info keyspace 0”和”info keyspace“， “info keyspace”和“info keyspace 0”等价；
info keyspace 1： 异步开始一次keyspace的扫描，并返回已完成的上一次keyspace扫描的结果
info keyspace 0: 返回已完成的上一次keyspace扫描的结果
config get/set * config get和config set的用法和redis是一样的，但是选项可能会有所不同，所以配了两个命令
config get *
config set * 用于分别列出config get和config set所能操作的选项
compact 因为pika底层存储引擎是基于rocksdb改造来的，会存在读写和空间放大的问题，除了rocksdb的自动compaction，pika也设置了一个手动compaction的命令，以强制compact整个kespace内的内容，支持对单个数据结构进行compact，语法为：compact [string/hash/set/zset/list/all]
一般keyspace比较大时，执行完compact命令后，占用空间会显著减小，但是耗时比较长，对读写性能也有影响，所以建议在流量不大的情况下执行

```



##### 参考

1. <https://github.com/OpenAtomFoundation/pika/wiki/%E5%AE%89%E8%A3%85%E4%BD%BF%E7%94%A8>
2. <https://github.com/OpenAtomFoundation/pika/wiki/Pika-Best-Practice>

3. <https://github.com/OpenAtomFoundation/pika/wiki>
