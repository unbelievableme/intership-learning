### 底层数据结构
k/v数据结构
为什么读写快


### 基本数据类型
- String
    - set
    - get
    - incr/incrby
    - decr/decrby
    - setnx
- List
    - lpush
    - lpop
    - rpush
    - rpop
    - lset
- Set
    - sadd
    - smembers
    - sunion
    - sdiff
- Hash
    - hset
    - hget
    - hkeys
    - hlen
    - hexists
- 使用场景

### Redis与Memcached
- 支持的数据类型
- 持久化
- 分布式

### redis的淘汰策略
- lru
- 设置淘汰时间expire

### 持久化
- RDB
    - 优点:生成的rdb文件不会更改,方便备份，恢复快
    - 缺点:会丢失好几分钟的数据
    - 适用场景:数据量大,对数据一致性没有太高的要求
- AOF
    - 优点:丢失数据量少
    - 缺点:写入速度慢，文件大，恢复慢
    - AOF重写,fork子进程,将内存数据写入新的aof文件中,
    - 适用场景:对数据一致性要求较高
    
- 数据备份

### redis集群
- 集群策略
    - redis cluster(crc16->slot->node)
        - 横向扩展增加master，提高写并发
        - 纵向扩展增加slaver，提高读并发
    

- 集群原理
    - 一致性hash算法
    - redis cluster自带的hash槽