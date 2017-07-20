# Redis
* `redis-cli --raw`  命令会在shell中显示中文，而不是16进制的数字。
* `select database` --选用数据库
### Redis 配置
配置文件是 `\redis\redis.conf`,可以通过 `config` 命令查看或设置配置项；

* Redis CONFIG 命令格式：     
  > redis 127.0.0.1：6379> CONFIG GET CONFIG_SETTION_NAME

**编辑配置**
修改`redis.conf` 文件或使用 **CONFIG set** 修改配置：      
  命令：`config set CONFIG_SETTION_NAME NEW_CONFIG_VALUE`


**配置参数说明**
redis.conf 配置项说明：
1. `daemonize no`   --是否以守护线程启动
2. `pidfile /var/run/redis.pid`   --守护线程号
3. `port 6379`   -- 服务端口号
4. `bind 127.0.0.1`  --绑定主机地址
5. `timeout 300`   --客户端闲置时长
6. `loglevel verbose`  -- 日志级别： debug verbose notice warning
7. `logfile stdout`    -- 日志记录方式，可以记录入文件中；
8. `database 16`     -- 设置数据库的数量
9. `save <seconds> <changes>`   -- 数据同步到数据文件策略
10. `rdbcompression yes`  -- 数据文件本地存储策略——是否压缩
11. ` dbfilename dump.rdb`  -- 指定数据文件名称；
12. `dir ./`    -- 数据文件存放路径
13. `slaveof <masterip> <masterport>`  -- 本机为`slave` 时，`master`服务配置
14. ` masterauth <master-password>`   -- master 机器的密码
15. `requirepass foobared`   -- 客户端连服务器的密码
16. ` maxclients 128`   -- 最大连接数
17. ` maxmemory <bytes>`  -- Redis 最大内存大小
18. `appendonly no`  -- 数据写入磁盘策略，在文件末尾写入
19. `appendfilename appendonly.aof`  --更新输入写入磁盘的文件名
20. `appendfsync everysec` --- 数据写入文件的策略条件三个可选值：    
    `no`         
    `always`        
    `everysec`         
21. ` vm-enabled no`   -- 是否启用 Redis VM 机制
22. `vm-swap-file /tmp/redis.swap`   --VM 内存文件路径，不可多个Redis实例共享
23. `vm-max-memory 0`  -- VM 将大于这里值得数据存入虚拟内存
24. `vm-page-size 32`  -- 一个对象可以保存在多个page上，一个page不能被多个对象共享；
25. `vm-pages 134217728`  -- page数量
26. `vm-max-threads 4`   -- 访问swap文件线程数
27. ` glueoutputbuf yes`   -- 是否合并小包
28. `hash-max-zipmap-entries 64  hash-max-zipmap-value 512`    --超过临界值，换哈希算法
29. `activerehashing yes`   --是否重置哈希
30. `include /path/to/local.conf`  --制定包含其他的配置文件，同一主机多个Redis实例之间使用同一配置文件，各实例又可以拥有特定配置；

























redis-cli
