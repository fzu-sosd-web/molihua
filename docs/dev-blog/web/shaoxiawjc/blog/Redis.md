# Redis



# NoSql概述

> 问题：
>
> 在单个MySQL的年代
>
> 1. 数据量太大，过去的一台机器的MySQL无法支撑
> 2. 数据的索引太多，机器也放不下
> 3. 访问量（读写混合），一个服务器承受不了



## memcached缓存+mysql+垂直拆分（读写分离）

## 垂直拆分

多个服务器，部分用于写入数据并同步到其他服务器，其他服务器用于读数据

![image-20240121113753662](./markdown-img/Redis.assets/image-20240121113753662.png)



>  问题：
>
> ​	网站80%都是在读，如果每次都去查询数据库会特别麻烦耗时

因此，为了减轻数据的压力，我们可以使用缓存来保证效率

![image-20240121114205233](./markdown-img/Redis.assets/image-20240121114205233.png)



## 分布分表+水平拆分（MySQL集群）

数据库的本质==>(读，写)

MyISAM：表锁，查询一次锁住整个表，高并发下出现严重的锁问题

Innodb：行锁





![image-20240121114958138](./markdown-img/Redis.assets/image-20240121114958138.png)

> 问题：
>
> ​	数量量开始继续变多，变复杂
>
> ​	关系型数据库不够用（存储json，图片，大博客）
>
> ​	需要新的数据库



一个基本的互联网项目

![image-20240121121401002](./markdown-img/Redis.assets/image-20240121121401002.png)



## 为什么要用NoSQL

用户的个人信息，地理位置，社交网络，用户日志等等

因此需要NoSQL

## 什么是NoSQL

==Not Only SQL==泛指非关系型数据库

关系型数据库：

​	表格类型，行和列

用户的个人信息，地理位置，社交网络，用户日志这些数据的存储不需要一个固定的格式！



## NoSQL特点

1、方便扩展（数据之间没用关系，很好扩展）

2、大数据高性能（redis每秒写8万，读11万）

3、数据类型多样（不需要设计数据库）

4、NoSQL和RDBMS（传统的关系型数据库）

```markdown
RDBMS
- 结构化组织
- SQL语言
- 数据和关系都存在单独的表里
- 数据定义语言
- 严格的一致性ACID
- 基础的事务
...
```

```markdown
NoSQL
- 不仅仅是数据
- 没用固定的查询语言
- 键值对，列，文档，图形等数据类型存储
- 最终一致性
- CAP定理 和 BASE
- 高性能，高可用，高可扩展
...
```



## NoSQL的四大分类

**K-V键值对**

* Redis
* Tair
* memecached

**文档型数据库（bson格式）**

* MongoDB
  * 基于分布式文件存储的数据库，c++编写的，主要用来处理大量的文档
  * 介于关系型数据库和非关系型数据库的中间的产品，MongoDB是非关系型数据库中功能最丰富，最像关系型数据库的
* conthdb

**列存储数据库**

* HBase
* 分布式文件系统

**图形关系数据库**

不是存储图片的，是存储关系的数据库，比如社交关系，广告推荐

* infogrid
* Neo4j



对比

![image-20240121140055885](./markdown-img/Redis.assets/image-20240121140055885.png)



# Redis入门

## 概述

> 什么是redis

Redis （Remote Dictionary Service），即远程字典服务，有c语言编写开源的，K-V键值对数据库，提供多种语言的api



> Redis能干嘛

1. 内存存储，持久化（RDB,AOF）
2. 效率高，可以用于高速缓存
3. 发布订阅系统
4. 地图信息分析
5. 计时器，计数器（浏览量）
6. ....



## Windows下安装Redis

Redis的Windows版的官网：https://github.com/microsoftarchive/redis/releases/tag/win-3.2.100

下载解压

开启服务![image-20240121142701973](./markdown-img/Redis.assets/image-20240121142701973.png)

![image-20240121142733773](./markdown-img/Redis.assets/image-20240121142733773.png)

Redis推荐我们使用Linux去开发，不建议使用Windows



## Linux下安装Redis

1、下载安装包

2、解压安装包！

![image-20240123200758047](./markdown-img/Redis.assets/image-20240123200758047.png)

3、进入解压后的文件，可以看到Redis的配置文件

4、基本的环境安装

先安装gcc

```shell
yum install gcc-c++

make
make
make install
```

![image-20240123203848964](./markdown-img/Redis.assets/image-20240123203848964.png)

![image-20240123203924654](./markdown-img/Redis.assets/image-20240123203924654.png)

5、Reid默认的安装路径，都在/usr/local/bin下

![image-20240123204056370](./markdown-img/Redis.assets/image-20240123204056370.png)

6、将Redis配置文件复制到当前目录下

```shell
 cp /opt/redis-7.2.4/redis.conf sxconfig
```

7、Redis默认不是后台启动的，我们需要修改配置文件

8、启动服务

```shell
redis-server sxconfig/redis.conf
```

9、测试

![image-20240123205937730](./markdown-img/Redis.assets/image-20240123205937730.png)

10、查看进程是否开启

```shell
ps -ef|grep redis
```



11、关闭

![image-20240123210207958](./markdown-img/Redis.assets/image-20240123210207958.png)

## 测试性能

redis-benchmark

官方自带的测试

![image-20240123210634277](./markdown-img/Redis.assets/image-20240123210634277.png)

简单测试

```shell
# 测试100个并发连接 100000 请求
redis-benchmark -h localhost -p 6379 -c 100 -n 100000
```

 

## 基础知识

redis默认有16个数据库

![image-20240123213824644](./markdown-img/Redis.assets/image-20240123213824644.png)

切换数据库

```shell
select 3 # 切换到第3个数据库
DBSIZE  # 查看数据库的大小
keys * # 查看当前数据库所有的key
flushdb # 清除当前数据库
flushall # 清除所有数据库
set key value # 添加一个键值对
get key # 得到指定key的v
exists key # 判断key是否存在，存在返回1，不存在返回0 
move key 数据库序号 # 从指定数据库移除key
expire key n # n秒后key-value过期 
ttl key # 查看过期剩余时间
type key # 查看v类型
```

> Redis是单线程的

Redis是很快的，基于内存操作，CPU不是性能瓶颈，Redis的性能瓶颈是根据机器的内存和网络带宽

Redis是c语言写的

为什么单线程这么快？

误区1：高线程的服务器一定是多线程的？

误区2：多线程（CPU）一定比单线程的效率高？

核心：Redis是将所有的数据全部放在内存里的，所有使用单线程直接去操作是最快的，多线程反而会有上下文切换。多次读写都是再一个CPU上的



# 五大数据类型

## String(字符串类型)

```shell
append key value # 再指定key的v里追加value，返回最终的字符串长度,如果当前key不存在就相当于set key value
strlen key # 获取字符串长度
incr key # 让key的v+1
incrby key n #  步长为n
decr key # -1
decrby key n # 步长为n
getrange key n1 n2 # 截取字符串的n1到n2部分（闭区间）,以0开始，0到-1截取所有字符串
setrange key n value # 从下标n开始，把value替换指定开始的
setex key value time（set with expire） # 设置过期时间
setnx key value（set if not exist） # 如果不存在才设置，设置成功返回1，设置失败返回0，再分布式锁中会经常使用
mset k1 v1 k2 v2 ... # 批量设置k-v键值对
mget k1 k2 k3 ... # 批量拿到v
msetnx k1 v1 k2 v2 ... # 批量如果不存在设置，如果有一个不存在就都不执行，保证原子性

# 对象
# 这里的key是一个巧妙的设计：user:{id}:{filed} 这么设计在Redis是完全可以的，比如set article:{id}:num
set user:1 {name:zhangsan,age:3} # 设置一个对象
mset user:1:name zhangsan user:1:age 30 # 同上
mget user:1:name user:2:age # 获取对象的信息

getset key redis # 组合命令，先执行查询，返回查询的值，在设置新的值
```

使用场景

* 计数器
* 统计多单位的数量 uid:9525644:follow 0
* 粉丝数
* 对象缓存存储



## List

基本的数据类型，列表

在Redis里list可以变成栈，队列等等

所有的list命令都是以l开头的

 ```shell
 127.0.0.1:6379> LPUSH list one
 (integer) 1
 127.0.0.1:6379> LPUSH list er
 (integer) 2
 127.0.0.1:6379> LPUSH list threee
 (integer) 3
 127.0.0.1:6379> lrange list 0 -1
 1) "threee"
 2) "er"
 3) "one"
 127.0.0.1:6379> lrange list 0 1
 1) "threee"
 2) "er" 
 127.0.0.1:6379> lrange list 0 -1
 1) "threee"
 2) "er"
 3) "one"
 # 我们不难发现，list的顺序是倒序
 # lpush 将一个值或多个值放到列表的头部 
 # rpush 将一个或多个值放到列表的尾部
 # lrange 获取list指定闭区间的值
 -------------------------------------
 127.0.0.1:6379> lpop list
 "threee"
 127.0.0.1:6379> rpop list
 "rightv"
 127.0.0.1:6379> lrange list 0 -1
 1) "er"
 2) "one"
 # lpop 移除列表的最左边元素
 # rpop 移除列表的最右边的元素
 --------------------------------------
 127.0.0.1:6379> LINDEX list 1
 "one"
 127.0.0.1:6379> lindex list 0
 "er"
 # lindex 通过下标获取值
 --------------------------------------
 127.0.0.1:6379> llen list
 (integer) 2
 # llen 获取列表的长度
 ---------------------------------------
 127.0.0.1:6379> lrange list 0 -1
 1) "three"
 2) "three"
 3) "two"
 4) "one"
 127.0.0.1:6379> lrem list 1 one
 (integer) 1
 127.0.0.1:6379> lrange list 0 -1
 1) "three"
 2) "three"
 3) "two"
 127.0.0.1:6379> lrem list 1 three
 (integer) 1
 127.0.0.1:6379> lrange list 0 -1
 1) "three"
 2) "two"
 127.0.0.1:6379> lpush list three
 (integer) 3
 127.0.0.1:6379> lrem list 2 three
 (integer) 2
 127.0.0.1:6379> lrange list 0 -1
 1) "two"
 # lrem 列表key 移除的数量 v 移除列表里指定数量的v，精确匹配
 ----------------------------------------
  127.0.0.1:6379> lrange mylist 0 -1
 1) "hello0"
 2) "hello1"
 3) "hello2"
 4) "hello3"
 5) "hello4"
 127.0.0.1:6379> ltrim mylist 1 3
 OK
 127.0.0.1:6379> lrange mylist 0 -1
 1) "hello1"
 2) "hello2"
 3) "hello3"
 # ltrim 截取指定区间，会改变原来的list
 ----------------------------------------
 127.0.0.1:6379> rpush mylist hello0
 (integer) 1
 127.0.0.1:6379> rpush mylist hello1
 (integer) 2
 127.0.0.1:6379> rpush mylist hello2
 (integer) 3
 127.0.0.1:6379> rpoplpush mylist myotherlist
 "hello2"
 127.0.0.1:6379> 
 127.0.0.1:6379> lrange mylist 0 -1
 1) "hello0"
 2) "hello1"
 127.0.0.1:63
 # rpoplpush 截取原来list最右边的元素并添加到目标列表的最左边
 ---------------------------------------
 127.0.0.1:6379> EXISTS list
 (integer) 1
 127.0.0.1:6379> FLUSHALL
 OK
 127.0.0.1:6379> lset list 0 v1
 (error) ERR no such key
 127.0.0.1:6379> lpush list 0 666
 (integer) 2
 127.0.0.1:6379> lrange list 0 -1
 1) "666"
 2) "0"
 127.0.0.1:6379> lset list 1 999
 OK
 127.0.0.1:6379> lrange list 0 -1
 1) "666"
 2) "999"
 127.0.0.1:6379> lset list 100 333
 (error) ERR index out of range
 # exists 判断这个列表是否存在
 # lset 对指定列表的指定下表的值进行修改，如果不存在就报错
 ----------------------------------------
 127.0.0.1:6379> FLUSHALL
 OK
 127.0.0.1:6379> rpush list hello
 (integer) 1
 127.0.0.1:6379> rpush list world
 (integer) 2
 127.0.0.1:6379> lrange list 0 -1
 1) "hello"
 2) "world"
 127.0.0.1:6379> LINSERT list before "world" v1
 (integer) 3
 127.0.0.1:6379> lrange list 0 -1
 1) "hello"
 2) "v1"
 3) "world"
 127.0.0.1:6379> linsert list after hello v0
 (integer) 4
 127.0.0.1:6379> lrange list 0 -1
 1) "hello"
 2) "v0"
 3) "v1"
 4) "world"
 127.0.0.1:6379> rpush list hello
 (integer) 5
 127.0.0.1:6379> lrange list 0 -1
 1) "hello"
 2) "v0"
 3) "v1"
 4) "world"
 5) "hello"
 127.0.0.1:6379> linsert list after hello v0
 (integer) 6
 127.0.0.1:6379> lrange list 0 -1
 1) "hello"
 2) "v0"
 3) "v0"
 4) "v1"
 5) "world"
 6) "hello"
 # linsert key before|after v v1 在指定的list里的指定v的前或后面插入v1，指针对从左往右数第一个v
 ----------------------------------------
 ```

> 小结

* 本质是一个链表link
* 如果key不存在创建新的链表
* 如果key存在就新增内容
* 在俩边插入或改动值，效率最高，越往里面效率越低





## Set（集合）

值不能重复,无序

命令基本都是以s开头

```bash
127.0.0.1:6379> sadd myset "hello"
(integer) 1
127.0.0.1:6379> sadd myset "shaoxia"
(integer) 1
127.0.0.1:6379> sadd myset "hello"
(integer) 0
127.0.0.1:6379> smembers myset
1) "hello"
2) "shaoxia"
127.0.0.1:6379> SISMEMBER myset  "hello"
(integer) 1
# sadd 在指定集合里添加剂元素，不存在就添加并且返回1，已存在就返回0
# smembers 查看集合的所有元素
# sismember 判断一个元素是否存在在指定集合里
-----------------------------------------
127.0.0.1:6379> scard myset
(integer) 2
127.0.0.1:6379> SREM myset hello
(integer) 1
127.0.0.1:6379> SMEMBERS myset
1) "shaoxia"
# scard 查看集合的元素个数
# SREM 移除某个元素
----------------------------------------
127.0.0.1:6379> SMEMBERS myset
1) "shaoxia"
2) "hello"
3) "hello1"
4) "hello2"
127.0.0.1:6379> SRANDMEMBER myset
"hello2"
127.0.0.1:6379> SRANDMEMBER myset
"hello2"
127.0.0.1:6379> SRANDMEMBER myset
"hello1"
127.0.0.1:6379> SRANDMEMBER myset
"hello1"
127.0.0.1:6379> SRANDMEMBER myset
"hello"
127.0.0.1:6379> SRANDMEMBER myset 2
1) "shaoxia"
2) "hello2"
127.0.0.1:6379> SRANDMEMBER myset 2
1) "hello1"
2) "hello2"
# srandmember 随机抽取指定个数的元素
-----------------------------------------
127.0.0.1:6379> SMEMBERS myset
1) "shaoxia"
2) "hello"
3) "hello1"
4) "hello2"
127.0.0.1:6379> spop myset
"hello"
127.0.0.1:6379> spop myset
"hello1"
127.0.0.1:6379> SMEMBERS myset
1) "shaoxia"
2) "hello2"
# spop 随机移除一个元素
-----------------------------------------
127.0.0.1:6379> SMEMBERS myset
1) "shaoxia"
2) "hello2"
127.0.0.1:6379> sadd myset2 uiweihwioiowio
(integer) 1
127.0.0.1:6379> SMOVE myset myset2 hello2
(integer) 1
127.0.0.1:6379> SMEMBERS myset
1) "shaoxia"
127.0.0.1:6379> SMEMBERS myset2
1) "uiweihwioiowio"
2) "hello2"
# smove 从一个集合里移动一个指定元素到另一个集合里
----------------------------------------
127.0.0.1:6379> sadd key1 a
(integer) 1
127.0.0.1:6379> sadd key1 b
(integer) 1
127.0.0.1:6379> sadd key1 c
(integer) 1
127.0.0.1:6379> sadd key2 c
(integer) 1
127.0.0.1:6379> sadd key2 e
(integer) 1
127.0.0.1:6379> sadd key2 f
(integer) 1
127.0.0.1:6379> SMEMBERS key1
1) "a"
2) "b"
3) "c"
127.0.0.1:6379> SMEMBERS key2
1) "c"
2) "e"
3) "f"
127.0.0.1:6379> sdiff key1 key2
1) "a"
2) "b"
127.0.0.1:6379> sinter key1 key2
1) "c"
127.0.0.1:6379> SUNION key1 key2
1) "a"
2) "b"
3) "c"
4) "e"
5) "f"
# sdiff key1 key2 去key1在key2里的差集
# sinter 取交集
# sunion 取并集
# 应用：二者用户的共同关注，共同好友（交集）
```

## Hash（哈希）

k-map

map集合，key-Map集合k-< k-v >

以h开头的

```shell
127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> hset myh f1 v1
(integer) 1
127.0.0.1:6379> hget myh f1 
"v1"
127.0.0.1:6379> hmset myh f2 v2 f3 v3
OK
127.0.0.1:6379> hmget myh f1 f2 f3
1) "v1"
2) "v2"
3) "v3"
127.0.0.1:6379> hgetall myh
1) "f1"
2) "v1"
3) "f2"
4) "v2"
5) "f3"
6) "v3"
# hset key f v 设置值
# hget 获取值
# hmset 批量设置
# hmget 批量获取
# hgetall 获取全部map
----------------------------------------
127.0.0.1:6379> hdel myh f3 
(integer) 1
127.0.0.1:6379> hgetall myh
1) "f1"
2) "v1"
3) "f2"
4) "v2"
127.0.0.1:6379> hgetall myh
1) "f1"
2) "v1"
3) "f2"
4) "v2"
127.0.0.1:6379> hlen myh
(integer) 2
# hdel 删除指定哈希的指定字段
# hlen 查看哈希长度
-----------------------------------------
127.0.0.1:6379> hlen myh
(integer) 2
127.0.0.1:6379> HEXISTS myh f3
(integer) 0
127.0.0.1:6379> HEXISTS myh f2
(integer) 1
# hexists 判断指定字段是否存在
-----------------------------------------
127.0.0.1:6379> hkeys myh
1) "f1"
2) "f2"
127.0.0.1:6379> hvals myh
1) "v1"
2) "v2"
# hkeys 查看所有的key
# hvals 查看所有val
-----------------------------------------
127.0.0.1:6379> hset myh f3 5
(integer) 1
127.0.0.1:6379> hincrby myh f3 2
(integer) 7
# incrby 设置自增
-----------------------------------------
127.0.0.1:6379> hsetnx myh f4 v4
(integer) 1
127.0.0.1:6379> hsetnx myh f4 dsjjklbsikbniksbikol
(integer) 0
# hsetnx 如果不存在设置
-----------------------------------------
```

哈希的应用

存储变更数据，经常变动的信息！

更适合对象的存储 user:1 name shaoxia

## Zset （有序集合）

比set多了一个值

zset key score val

```bash
127.0.0.1:6379> zadd myset 1 one
(integer) 1
127.0.0.1:6379> zadd myset 2 two 3 three
(integer) 2
127.0.0.1:6379> zrange myset 0 -1
1) "one"
2) "two"
3) "three"
# zadd 添加
# zrange 查看
-----------------------------------------
127.0.0.1:6379> zadd salary 2500 zs
(integer) 1
127.0.0.1:6379> zadd salary 500000 wjc
(integer) 1
127.0.0.1:6379> zadd salary 6 shaoxia
(integer) 1
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf
1) "shaoxia"
2) "zs"
3) "wjc"
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf withscores
1) "shaoxia"
2) "6"
3) "zs"
4) "2500"
5) "wjc"
6) "500000"
127.0.0.1:6379> ZRANGEBYSCORE salary -inf 3000 withscores
1) "shaoxia"
2) "6"
3) "zs"
4) "2500"
# ZRANGEBYSCORE 通过score在指定区间进行排序，withscores可以指定显示scores 
-----------------------------------------
127.0.0.1:6379> ZREM salary shaoxia
(integer) 1
127.0.0.1:6379> zcard salary
(integer) 2
127.0.0.1:6379> ZREVRANGE salary 0 -1
1) "wjc"
2) "zs"
# zrem 移除
# zcard 获取个数
# ZREVRANGE 从大到小进行排序
-----------------------------------------
127.0.0.1:6379> zadd ms 1  v1
(integer) 1
127.0.0.1:6379> zadd ms 2 v2
(integer) 1
127.0.0.1:6379> zadd ms 3 v3
(integer) 1
127.0.0.1:6379> ZCOUNT ms 1 3
(integer) 3
127.0.0.1:6379> ZCOUNT ms 1 2
(integer) 2
# zcount 在score的闭区间计数有几个val
----------------------------------------

```

应用场景

成绩，工资

重要消息，通过权重进行排序

排行榜



# 三种特殊类型

## geospatial 地理位置

定位，附近的人，打车距离

3.2版本就有了

[城市经纬度查询-国内城市经度纬度在线查询工具 (jsons.cn)](http://www.jsons.cn/lngcode/)

只有6个命令

> geoadd

```shell
# geoadd 添加地理位置
# 两级无法添加，我们一般会下载城市数据，直接通过Java程序一次性输入
# 参数 key 维度 经度 名称
127.0.0.1:6379> geoadd chian:city 116.40 39.90 Beijing
(integer) 1
127.0.0.1:6379> geoadd chian:city 121.47 31.23 Shanghai
(integer) 1
127.0.0.1:6379> geoadd chian:city 106.50 29.53 Chongqin
(integer) 1
127.0.0.1:6379> geoadd chian:city 114.05 22.54 Shengzhen
(integer) 1
127.0.0.1:6379> geoadd chian:city 120.16 30.24 Hangzhou
(integer) 1
127.0.0.1:6379> geoadd chian:city 108.96 34.26 XiAn
(integer) 1
# ERR invalid longitude,latitude pair 无效的经纬度
```

> geopos

```shell
127.0.0.1:6379> GEOPOS chian:city Beijing
1) 1) "116.39999896287918091"
   2) "39.90000009167092543"
获取指定的经纬度
```

> GEODIST

单位

* **m** 表示单位为米。
* **km** 表示单位为千米。
* **mi** 表示单位为英里。
* **ft** 表示单位为英尺。

```shell
127.0.0.1:6379> GEODIST chian:city Beijing Shanghai
"1067378.7564"
```

> geohash

该命令将返回11个字符的Geohash字符串，所以没有精度Geohash，损失相比，使用内部52位表示。返回的geohashes具有以下特性：

1. 他们可以缩短从右边的字符。它将失去精度，但仍将指向同一地区。
2. 它可以在 `geohash.org` 网站使用，网址 `http://geohash.org/<geohash-string>`。查询例子：http://geohash.org/sqdtr74hyu0.
3. 与类似的前缀字符串是附近，但相反的是不正确的，这是可能的，用不同的前缀字符串附近。

```shell
127.0.0.1:6379> geohash chian:city Beijing Shanghai
1) "wx4fbxxfke0"
2) "wtw3sj5zbj0"
```

> georadius

以给定的经纬度为中心， 返回键包含的位置元素当中， 与中心的距离不超过给定最大距离的所有位置元素。

范围可以使用以下其中一个单位：

- **m** 表示单位为米。
- **km** 表示单位为千米。
- **mi** 表示单位为英里。
- **ft** 表示单位为英尺。

在给定以下可选项时， 命令会返回额外的信息：

- `WITHDIST`: 在返回位置元素的同时， 将位置元素与中心之间的距离也一并返回。 距离的单位和用户给定的范围单位保持一致。
- `WITHCOORD`: 将位置元素的经度和维度也一并返回。
- `WITHHASH`: 以 52 位有符号整数的形式， 返回位置元素经过原始 geohash 编码的有序集合分值。 这个选项主要用于底层应用或者调试， 实际中的作用并不大。

命令默认返回未排序的位置元素。 通过以下两个参数， 用户可以指定被返回位置元素的排序方式：

- `ASC`: 根据中心的位置， 按照从近到远的方式返回位置元素。
- `DESC`: 根据中心的位置， 按照从远到近的方式返回位置元素。

可以做附近的人，通过半径定位

```shell
# GEORADIUS key 经纬度 距离 单位 选项
127.0.0.1:6379> GEORADIUS chian:city 110 30 1000 km 
1) "Chongqin"
2) "XiAn"
3) "Shengzhen"
4) "Hangzhou"
127.0.0.1:6379> GEORADIUS chian:city 110 30 1000 km withdist
1) 1) "Chongqin"
   2) "341.9374"
2) 1) "XiAn"
   2) "483.8340"
3) 1) "Shengzhen"
   2) "922.6257"
4) 1) "Hangzhou"
   2) "977.5143"
127.0.0.1:6379> GEORADIUS chian:city 110 30 1000 km withdist asc
1) 1) "Chongqin"
   2) "341.9374"
2) 1) "XiAn"
   2) "483.8340"
3) 1) "Shengzhen"
   2) "922.6257"
4) 1) "Hangzhou"
   2) "977.5143"
127.0.0.1:6379> GEORADIUS chian:city 110 30 1000 km withdist desc
1) 1) "Hangzhou"
   2) "977.5143"
2) 1) "Shengzhen"
   2) "922.6257"
3) 1) "XiAn"
   2) "483.8340"
4) 1) "Chongqin"
   2) "341.9374"
# 只拿2个
127.0.0.1:6379> GEORADIUS chian:city 110 30 1000 km withdist withcoord count 2
1) 1) "Chongqin"
   2) "341.9374"
   3) 1) "106.49999767541885376"
      2) "29.52999957900659211"
2) 1) "XiAn"
   2) "483.8340"
   3) 1) "108.96000176668167114"
      2) "34.25999964418929977"

```

> GEORADIUSBYMEMBER 以用户为圆心找

选项和georadius差不多

```bash
127.0.0.1:6379> GEORADIUSBYMEMBER chian:city Beijing 1000 km
1) "Beijing"
2) "XiAn"
```

> geo的底层实现原理就是zset

我们可以使用zset来操作

```shell
127.0.0.1:6379> zrange chian:city 0 -1
1) "Chongqin"
2) "XiAn"
3) "Shengzhen"
4) "Hangzhou"
5) "Shanghai"
6) "Beijing"
127.0.0.1:6379> zrem chian:city XiAn
(integer) 1
127.0.0.1:6379> zrange chian:city 0 -1
1) "Chongqin"
2) "Shengzhen"
3) "Hangzhou"
4) "Shanghai"
5) "Beijing"
```



## hyperloglog

> 什么是基数

A{1,3,5,7,} B{1,3,5,7,8}

基数（一个集合里不重复的元素）

> 简介

redis 2.8.9更新的数据结构

只需要12kb

做基数统计的算法，网页的UV（一个人访问网站多次，还是统计为一次）

传统1方式，set保存用户的id，然后就可以统计set里的元素数量作为标准

这个方式如果保存大量的用户id，就会比较麻烦！我们的用户是为了计数 ，使用set会占很多内存

有0.81%的错误率

可以接受

```shell
127.0.0.1:6379> pfadd key1 a b c d e f g
(integer) 1
127.0.0.1:6379> PFCOUNT key1 # 统计基数
(integer) 7
127.0.0.1:6379> pfadd key2 a l p o e
(integer) 1
127.0.0.1:6379> PFMERGE key1 key2 # 合并
OK
127.0.0.1:6379> PFCOUNT key1
(integer) 10
# pfmerge destkey source ... 
```

## bitmaps

> 位存储

统计疫情感染人数：001001001，是否感染用01表示

登录和未登录，活跃和不活跃，打卡和不打卡....

只有俩个状态的，都可以使用bitmap

```shell
127.0.0.1:6379> setbit sign 0 1
(integer) 0
127.0.0.1:6379> setbit sign 1 0
(integer) 0
127.0.0.1:6379> setbit sign 2 1
(integer) 0
127.0.0.1:6379> getbit sign 0
(integer) 1
127.0.0.1:6379> getbit sign 1
(integer) 0
127.0.0.1:6379> BITCOUNT sign
(integer) 2
# setbit key offset status 设置bit
# getbit key offset 获取状态
# bitcount key 计数1
```





# 事务

Redis单条命令是保证原子性的，但是事务是不保证原子性

> 事务的本质

一组命令的集合！一个事务中的所有命令都会被序列化，在事务的执行过程中，会按照顺序执行。

一次性，顺序性，排他性 执行一系列的命令

Redis事务没有隔离级别的概念

所有的命令在事务里，并没有被直接执行！只有发起执行命令的时候才会被执行！Exec 

* 开启事务(multi)
* 命令入队
* 执行事务（exec）

Redis可以实现乐观锁

> 正常执行事务

```shell
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> set k1 v1
QUEUED
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> set k3 v3
QUEUED
127.0.0.1:6379(TX)> EXEC
1) OK
2) OK
3) OK
```

> 中途出现错误

1、编译错误，代码有问题，那么事务里的所有命令都不会被执行

```shell
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> set k1 v1
QUEUED
127.0.0.1:6379(TX)> getset k3 # 错误处
(error) ERR wrong number of arguments for 'getset' command
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> exec
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379> get k1
(nil)
```

2、运行时异常（1/0），该命令不能执行，其他命令可以执行

```shell
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> set k1 v1
QUEUED
127.0.0.1:6379(TX)> INCR k1
QUEUED
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> get k2
QUEUED
127.0.0.1:6379(TX)> exec
1) OK
2) (error) ERR value is not an integer or out of range
3) OK
4) "v2"
127.0.0.1:6379> get k2
"v2"
```



> 放弃事务

```shell
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> set k4 v4
QUEUED
127.0.0.1:6379(TX)> DISCARD
OK
127.0.0.1:6379> get k4
(nil)
```



> 监控 Watch

悲观锁：

* 无论什么都会加锁

乐观锁：

* 不加锁！更新数据的时候取判断一下，再次期间是否有人修改过这个数据
* 取出来获取version
* 更新的时候比较version

正常执行成功

```shell
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set spend 0
OK
127.0.0.1:6379> WATCH money # 监视 money
OK
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> DECRBY money 20
QUEUED
127.0.0.1:6379(TX)> INCRBY spend 20
QUEUED
127.0.0.1:6379(TX)> EXEC
1) (integer) 80
2) (integer) 20

```

==模拟多个线程==

在原来的命令里

```shell
 127.0.0.1:6379> WATCH money
OK
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> DECRBY money 50
QUEUED
127.0.0.1:6379(TX)> INCRBY spend 50
QUEUED
```

新建一个会话

执行

```shell
127.0.0.1:6379> get money
"70"
127.0.0.1:6379> INCRBY money 100
(integer) 170
```

原来的会话里

```shell
127.0.0.1:6379(TX)> exec
(nil)
```

发现执行失败

```shell
127.0.0.1:6379> UNWATCH # 发现事务执行失败，先解锁
OK
127.0.0.1:6379> WATCH money # 再开启监视
OK
127.0.0.1:6379> MULTI 
OK
127.0.0.1:6379(TX)> DECRBY money 1
QUEUED
127.0.0.1:6379(TX)> INCRBY spend 1
QUEUED
127.0.0.1:6379(TX)> exec
1) (integer) 169
2) (integer) 31

```

如果执行失败，就获取最新的值就行了





# Jedis

要使用Java来操作redis

> 什么是jedis 

是Redis官方推荐的java连接开发工具！使用java操作Redis中间件！

> 测试

1、导入对应的依赖

```xml
<!--jedis-->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>5.1.0</version>
</dependency>
<!--fastjson-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>2.0.42</version>
</dependency>
```

2、编码测试

* 连接数据库
* 操作命令
* 断开连接

```java
public static void main(String[] args) {
		// 1、new 一个jedis对象
		Jedis jedis = new Jedis("127.0.0.1",6379);
		// jedis的命令都是Redis的命令
		System.out.println(jedis.ping());
	}
```

```java
System.out.println("清空所有数据库"+jedis.flushAll());
		System.out.println("清空当前数据库"+jedis.flushDB());
		System.out.println("判断name是否存在"+jedis.exists("name"));
		System.out.println("set一个name"+jedis.set("name","shaoxia"));
		System.out.println("获得name"+jedis.get("name"));
		System.out.println("set一个password"+jedis.set("password","123456"));
		System.out.println("获得password"+jedis.get("password"));
		System.out.println("all keys"+jedis.keys("*"));
		System.out.println("the type of name is "+jedis.type("name"));
		System.out.println("the type of password is "+jedis.type("password"));
		System.out.println("random key is "+jedis.randomKey());
		System.out.println("rename name to username "+jedis.rename("name", "username"));
		System.out.println("select the db 0"+jedis.select(0));
		System.out.println("the db size is "+jedis.dbSize());
		System.out.println(jedis.flushAll());
```

其他代码就不写了。累死了

> 事务



```java
public static void main(String[] args) {
		Jedis jedis = new Jedis("127.0.0.1",6379);
		jedis.flushDB();
		System.out.println(jedis.ping());
		JSONObject jsonObject = new JSONObject();
		jsonObject.put("name","shaoxia");
		jsonObject.put("userId",102301323);
		String result = jsonObject.toJSONString();
		// 开启事务
		Transaction transaction = jedis.multi();
		try {
			transaction.set("user",result);
			int i = 1/0;
			transaction.exec();
		} catch (Exception e) {
			// 放弃事务
			transaction.discard();
			e.printStackTrace();
		}finally {
			System.out.println(jedis.get("user"));
		}

		jedis.close(); // 关闭连接
	}
```



# 整合spring boot

在springboot2.x之后，原来的jedis被替换为了lettuce



jedis：采用的直连，多个线程操作的化是不安全的，如果想要避免不安全的使用jedis pool连接池



lettuce：采用netty，实例可以在多个线程里进行共享，不存在线程不安全的情况，可以减少线程数量，更像nio模式



> 测试

1、导入环境

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```



2、配置

```yaml
# 配置Redis
spring:
  redis:
    host: 127.0.0.1
    port: 6379
```

![image-20240125141629932](./markdown-img/Redis.assets/image-20240125141629932.png)

3、测试

```java
@Autowired
    private RedisTemplate redisTemplate;

    @Test
    void contextLoads() {
       // opsForValue 操作字符串
       // opsForList 操作列表
       // 获取连接，一般很少用
//     RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
//     connection.flushAll();
    }
```

关于对象的保存

实际开发里，我们一般把对象序列化为json，同时所有的对象要求序列化

我们需要在自己的RedisConfig里配置自定义序列化方式

自己的配置

```java
/**
 * 固定的Redis配置
 * */
@Bean
@ConditionalOnSingleCandidate(RedisConnectionFactory.class)
public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
    // 为了开发方便，我们使用RedisTemplate<String, Object>
    RedisTemplate<String, Object> template = new RedisTemplate();
    template.setConnectionFactory(redisConnectionFactory);
    // 序列化配置
    // Jackson2JsonRedisSerializer序列化对象
    Jackson2JsonRedisSerializer jsonRedisSerializer = new Jackson2JsonRedisSerializer<>(Object.class);
    // ObjectMapper是Jackson中处理json的主要类
    ObjectMapper om = new ObjectMapper();
    // 设置可见性
    // PropertyAccessor.ALL表示对所有字段生效
    // JsonAutoDetect.Visibility.ANY表示对所有属性生效，即使是private
    om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
    om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
    jsonRedisSerializer.setObjectMapper(om);
    // String的序列化
    StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

    // key采用string的序列化方式
    template.setKeySerializer(stringRedisSerializer);
    // hash的key采用string的序列化方式
    template.setHashKeySerializer(stringRedisSerializer);
    // value的序列化方式采用jsonRedisSerializer
    template.setValueSerializer(jsonRedisSerializer);
    //  hash的value同上
    template.setHashValueSerializer(jsonRedisSerializer);

    template.afterPropertiesSet();
    return template;
}
```

常用类

```java
@Component
public class RedisUtils {

    @Autowired
    private RedisTemplate redisTemplate;


    // =============================common============================
    /**
     * 指定缓存失效时间
     * @param key  键
     * @param time 时间(秒)
     */
    public boolean expire(String key, long time) {
        try {
            if (time > 0) {
                redisTemplate.expire(key, time, TimeUnit.SECONDS);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 根据key 获取过期时间
     * @param key 键 不能为null
     * @return 时间(秒) 返回0代表为永久有效
     */
    public long getExpire(String key) {
        return redisTemplate.getExpire(key, TimeUnit.SECONDS);
    }


    /**
     * 判断key是否存在
     * @param key 键
     * @return true 存在 false不存在
     */
    public boolean hasKey(String key) {
        try {
            return redisTemplate.hasKey(key);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 删除缓存
     * @param key 可以传一个值 或多个
     */
//    @SuppressWarnings("unchecked")
    public void del(String... key) {
        if (key != null && key.length > 0) {
            if (key.length == 1) {
                redisTemplate.delete(key[0]);
            } else {
                redisTemplate.delete(Arrays.asList(key));
//                redisTemplate.delete(CollectionUtils.arrayToList(key));
            }
        }
    }

    /**
     * 获取并删除缓存
     * @param key 键
     * @return 值
     */
    public Object getAndDelete(String key) {
        try{
            return key == null ? null : get(key);
        }finally {
            del(key);
        }
    }

    // ============================String=============================

    /**
     * 普通缓存获取
     * @param key 键
     * @return 值
     */
    public  Object get(String key) {
        return key == null ? null : redisTemplate.opsForValue().get(key);
    }

    /**
     * 普通缓存放入
     * @param key   键
     * @param value 值
     * @return true成功 false失败
     */

    public boolean set(String key, Object value) {
        try {
            redisTemplate.opsForValue().set(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 普通缓存放入并设置时间
     * @param key   键
     * @param value 值
     * @param time  时间(秒) time要大于0 如果time小于等于0 将设置无限期
     * @return true成功 false 失败
     */

    public boolean set(String key, Object value, long time) {
        try {
            if (time > 0) {
                redisTemplate.opsForValue().set(key, value, time, TimeUnit.SECONDS);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 递增
     * @param key   键
     * @param delta 要增加几(大于0)
     */
    public long incr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递增因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, delta);
    }


    /**
     * 递减
     * @param key   键
     * @param delta 要减少几(小于0)
     */
    public long decr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递减因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, -delta);
    }


    // ================================Map=================================

    /**
     * HashGet
     * @param key  键 不能为null
     * @param item 项 不能为null
     */
    public Object hget(String key, String item) {
        return redisTemplate.opsForHash().get(key, item);
    }

    /**
     * 获取hashKey对应的所有键值
     * @param key 键
     * @return 对应的多个键值
     */
    public Map<Object, Object> hmget(String key) {
        return redisTemplate.opsForHash().entries(key);
    }

    /**
     * HashSet
     * @param key 键
     * @param map 对应多个键值
     */
    public boolean hmset(String key, Map<String, Object> map) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * HashSet 并设置时间
     * @param key  键
     * @param map  对应多个键值
     * @param time 时间(秒)
     * @return true成功 false失败
     */
    public boolean hmset(String key, Map<String, Object> map, long time) {
        try {
            if (time > 0) {
                redisTemplate.opsForHash().putAll(key, map);
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 向一张hash表中放入数据,如果不存在将创建
     *
     * @param key   键
     * @param item  项
     * @param value 值
     * @return true 成功 false失败
     */
    public boolean hset(String key, String item, Object value) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 向一张hash表中放入数据,如果不存在将创建
     *
     * @param key   键
     * @param item  项
     * @param value 值
     * @param time  时间(秒) 注意:如果已存在的hash表有时间,这里将会替换原有的时间
     * @return true 成功 false失败
     */
    public boolean hset(String key, String item, Object value, long time) {
        try {
            if (time > 0) {
                redisTemplate.opsForHash().put(key, item, value);
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 删除hash表中的值
     *
     * @param key  键 不能为null
     * @param item 项 可以使多个 不能为null
     */
    public void hdel(String key, Object... item) {
        redisTemplate.opsForHash().delete(key, item);
    }


    /**
     * 判断hash表中是否有该项的值
     *
     * @param key  键 不能为null
     * @param item 项 不能为null
     * @return true 存在 false不存在
     */
    public boolean hHasKey(String key, String item) {
        return redisTemplate.opsForHash().hasKey(key, item);
    }


    /**
     * hash递增 如果不存在,就会创建一个 并把新增后的值返回
     *
     * @param key  键
     * @param item 项
     * @param by   要增加几(大于0)
     */
    public double hincr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, by);
    }


    /**
     * hash递减
     *
     * @param key  键
     * @param item 项
     * @param by   要减少记(小于0)
     */
    public double hdecr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, -by);
    }


    // ============================set=============================

    /**
     * 根据key获取Set中的所有值
     * @param key 键
     */
    public Set<Object> sGet(String key) {
        try {
            return redisTemplate.opsForSet().members(key);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 根据value从一个set中查询,是否存在
     *
     * @param key   键
     * @param value 值
     * @return true 存在 false不存在
     */
    public boolean sHasKey(String key, Object value) {
        try {
            return redisTemplate.opsForSet().isMember(key, value);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 将数据放入set缓存
     *
     * @param key    键
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSet(String key, Object... values) {
        try {
            return redisTemplate.opsForSet().add(key, values);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 将set数据放入缓存
     *
     * @param key    键
     * @param time   时间(秒)
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSetAndTime(String key, long time, Object... values) {
        try {
            Long count = (long)values.length;
            if (time > 0) {
                count = redisTemplate.opsForSet().add(key, values);
                expire(key, time);
            }
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 获取set缓存的长度
     *
     * @param key 键
     */
    public long sGetSetSize(String key) {
        try {
            return redisTemplate.opsForSet().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 移除值为value的
     *
     * @param key    键
     * @param values 值 可以是多个
     * @return 移除的个数
     */

    public long setRemove(String key, Object... values) {
        try {
            return redisTemplate.opsForSet().remove(key, values);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    // ===============================list=================================

    /**
     * 获取list缓存的内容
     *
     * @param key   键
     * @param start 开始
     * @param end   结束 0 到 -1代表所有值
     */
    public List<Object> lGet(String key, long start, long end) {
        try {
            return redisTemplate.opsForList().range(key, start, end);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 获取list缓存的长度
     *
     * @param key 键
     */
    public  long lGetListSize(String key) {
        try {
            return redisTemplate.opsForList().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 通过索引 获取list中的值
     *
     * @param key   键
     * @param index 索引 index>=0时， 0 表头，1 第二个元素，依次类推；index<0时，-1，表尾，-2倒数第二个元素，依次类推
     */
    public Object lGetIndex(String key, long index) {
        try {
            return redisTemplate.opsForList().index(key, index);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     */
    public boolean lSet(String key, Object value) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 将list放入缓存
     * @param key   键
     * @param value 值
     * @param time  时间(秒)
     */
    public boolean lSet(String key, Object value, long time) {
        try {
            if (time > 0) {
                redisTemplate.opsForList().rightPush(key, value);
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }

    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     * @return true 存放成功 false存放失败
     */
    public boolean lSet(String key, List<Object> value) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }

    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     * @param time  时间(秒)
     * @return true 存放成功 false存放失败
     */
    public boolean lSet(String key, List<Object> value, long time) {
        try {
            if (time > 0) {
                redisTemplate.opsForList().rightPushAll(key, value);
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 根据索引修改list中的某条数据
     *
     * @param key   键
     * @param index 索引
     * @param value 值
     * @return true 存放成功 false存放失败
     */

    public boolean lUpdateIndex(String key, long index, Object value) {
        try {
            redisTemplate.opsForList().set(key, index, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 移除N个值为value
     *
     * @param key   键
     * @param count 移除多少个
     * @param value 值
     * @return 移除的个数
     */

    public long lRemove(String key, long count, Object value) {
        try {
            return redisTemplate.opsForList().remove(key, count, value);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }
}
```





# Redis配置文件分析

> 单位

unite对单位不敏感

![image-20240125153153227](./markdown-img/Redis.assets/image-20240125153153227.png)

> 包含

![image-20240125153244505](./markdown-img/Redis.assets/image-20240125153244505.png)

可以包含多个配置文件

> 网路

```shell
bind 127.0.0.1 -::1 # 绑定的IP
protected-mode yes # 保护模式
port 6379 # 端口设置
```

> 通用 GENERAL

```shell
daemonize yes # 是否开启守护进程，后台运行
pidfile /var/run/redis_6379.pid # 如果我们开启了以守护进程方式运行，就会指定一个pid文件

# 日志
# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
# nothing (nothing is logged)
loglevel notice
logfile "" # 日志的生成的文件名

databases 16 # 数据库的数量
always-show-logo no # 是否显示logo
```



> 快照

在规定的时间内，进行了多少次操作则会持久化到文件  .rdb  .aof

redis 是内存数据库，数据断电即失

```shell
# save 3600 1 300 100 60 10000
在 3600/300/60 秒内，至少进行了1/100/10000次操作，就进行一次持久化
stop-writes-on-bgsave-error yes # 持久化失败了是否继续工作
rdbcompression yes # 是否压缩rdb文件，需要消耗一些cpu资源
rdbchecksum yes # 保存rdb时是否校验rdb文件
dir ./ # rdb文件保存的目录
```



> REPLICATION 复制



> 安全

1、设置密码

* 命令行设置

```shell
config set requirepass 密码 # 设置密码
config get requirepass # 获取密码
auth 密码 # 验证密码
```

* 配置文件设置

![image-20240125165436686](./markdown-img/Redis.assets/image-20240125165436686.png)



> CLIENTS 客户端限制

```shell
# maxclients 10000 # 客户端的最大数量限制

```

> MEMORY MANAGEMENT 内存管理

```shell
maxmemory <bytes> # 最大内存管理
maxmemory-policy noeviction # 内存达到上限后的处理策略
	- 移除一些过期的key
	- 报错
	- ....
```



> APPEND ONLY MODE aof配置

```shell
appendonly no # 默认不开启aof，默认的rdb模式，大部分情况下rdb已经够用了
appendfilename "appendonly.aof" # 持久化文件的名字

# appendfsync always    # 每次修改都写入
appendfsync everysec	# 每秒执行一次
# appendfsync no    # 不执行

```





# Redis持久化

## RDB

在Redis中，RDB（Redis DataBase）是一种持久化的机制，它允许将当前内存中的数据保存到硬盘上的一个二进制文件中。RDB文件是一个快照（snapshot）文件，记录了某个时间点上整个数据库的状态。这个文件通常具有`.rdb`的扩展名

==默认的文件是dump.rdb==

> dump.rdb产生规则

1、save指令下的条件满足后，会自动产生

2、执行了flushdb指令后，会自动产生

3、关闭redis服务后，也会自动产生

> 如何恢复rdb文件

1、只需要把rdb文件放到Redis的启动目录就可以了。Redis会自动监测

2、如果这个目录下存在dump.rdb，Redis就会自动扫描

```shell
127.0.0.1:6379> config get dir
1) "dir"
2) "/usr/local/bin"
```

大部分情况下默认配置就足够了

优点：

1、适合大规模的数据恢复！dump.rdb

2、对数据的完整性要求不高！

缺点：

1、需要一定的时间间隔！如果Redis意外宕机，那么最后一次的修改数据就没有了

2、fork进程的时候会占用一定的内存空间

![image-20240125191200734](./markdown-img/Redis.assets/image-20240125191200734.png)



## AOF


AOF（Append Only File）是Redis中的一种持久化机制，用于将写命令追加到一个文件中，以记录数据库状态的变化。AOF文件包含了使数据库状态从空到当前状态的所有写命令。它是一个文本文件，每条命令以Redis协议的格式存储。

以下是AOF的基本原理：

1. **记录写命令：** 当Redis接收到写命令（例如SET、INCR等）时，不仅会在内存中执行该命令，还会将该命令追加到AOF文件的末尾。
2. **同步策略：** Redis提供了不同的AOF同步策略，决定何时将AOF缓冲区的内容同步到磁盘。有三种主要的同步方式：
   - `always`：每个写命令都同步到磁盘。最安全但性能较差。
   - `everysec`：每秒同步一次，将AOF缓冲区的内容写入磁盘。折衷方案，提供了不错的性能和持久性。
   - `no`：完全依赖操作系统的缓冲机制，不主动进行同步。
3. **重写机制：** 为了避免AOF文件过大，Redis提供了AOF重写机制。这是一个后台任务，它通过分析当前数据库状态，生成一个新的AOF文件，只包含重要的写命令，从而减小文件体积。
4. **启动时加载AOF：** 当Redis启动时，可以选择根据AOF文件的内容将数据库还原到最新状态。这是通过读取AOF文件中的写命令来实现的。

AOF持久化的优点包括：

- 更可靠的数据持久性，因为AOF文件记录了每个写命令。
- 在发生故障时，AOF文件通常比RDB文件更容易修复。
- 提供了对数据更细粒度的持久化控制。

然而，AOF持久化也可能导致AOF文件较大，因此定期进行AOF重写是一个优化策略。同时，AOF同步操作可能对性能产生一定影响，因此需要根据实际需求进行配置。

保存的文件是appendonly.aof



默认的配置aof是关闭的，要去配置文件里开启

如果aof文件有错误，这个时候Redis服务是无法启动的，我们需要修复他

修复文件``redis-check-aof``

```shell
redis-check-aof --fix aop文件
```

重写规则

```shell
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb #  如果文件大于64mb，就再写一份
```



# Redis的订阅和发布

![image-20240126115305393](./markdown-img/Redis.assets/image-20240126115305393.png)

![image-20240126120336424](./markdown-img/Redis.assets/image-20240126120336424.png)

> 测试

订阅端：

```shell
127.0.0.1:6379> subscribe shaoxia  # 1、订阅一个频道
1) "subscribe"
2) "shaoxia"
3) (integer) 1
# 2、等待接收消息
1) "message"  # 4、接受消息
2) "shaoxia"
3) "hello,world"
```

发布端：

```shell
127.0.0.1:6379> publish shaoxia "hello,world"  # 3、发布者发布消息
(integer) 1
```



场景

1. 实时消息系统
2. 实时聊天系统
3. 订阅和关注系统

复杂一点：

会使用消息中间件MQ



# Redis的主从复制

主--仆

## 概念

**Redis主从复制（Replication）**是一种数据同步机制，用于在多个Redis服务器之间保持数据一致性。在主从复制中，一个Redis服务器充当主节点，而其他Redis服务器则作为从节点。主节点负责处理写操作，从节点则复制主节点的数据，并在需要时提供读取服务。

![image-20240126121514736](./markdown-img/Redis.assets/image-20240126121514736.png)

读写分离，大部分情况下都是再进行读操作，就可以使用，减轻压力



































