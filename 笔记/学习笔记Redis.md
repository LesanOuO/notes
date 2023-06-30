---
title: "学习笔记Redis"
date: 2022-05-20T18:45:30+08:00
draft: false
tags: ['Redis']
categories: ['学习笔记']
---

## Redis简介
Redis 是完全开源的，遵守 BSD 协议，是一个高性能的 key-value 数据库。

Redis 与其他 key - value 缓存产品有以下三个特点：

- Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
- Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
- Redis支持数据的备份，即master-slave模式的数据备份。

### Redis数据结构

1. String（字符串）

string 是 redis 最基本的类型，你可以理解成与 Memcached 一模一样的类型，一个 key 对应一个 value。

string 类型是二进制安全的。意思是 redis 的 string 可以包含任何数据。比如jpg图片或者序列化的对象。

string 类型是 Redis 最基本的数据类型，string 类型的值最大能存储 512MB。

```bash
127.0.0.1:6379> set name Lesan
OK
127.0.0.1:6379> get name
"Lesan"
```

2. Hash（哈希）

Redis hash 是一个键值(key=>value)对集合。

Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。

```bash
127.0.0.1:6379> hmset lesan field1 "Hello" field2 "World"
OK
127.0.0.1:6379> hget lesan field1
"Hello"
127.0.0.1:6379> hget lesan field2
"World"
```

3. List（列表）

Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。

```bash
127.0.0.1:6379> del lesan
(integer) 1
127.0.0.1:6379> lpush lesan redis
(integer) 1
127.0.0.1:6379> lpush lesan mongodb
(integer) 2
127.0.0.1:6379> lpush lesan mysql
(integer) 3
127.0.0.1:6379> lrange lesan 0 10
1) "mysql"
2) "mongodb"
3) "redis"
```

4. Set（集合）

Redis 的 Set 是 string 类型的无序集合。

集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。

```bash
127.0.0.1:6379> sadd lesan redis
(integer) 1
127.0.0.1:6379> sadd lesan redis
(integer) 0
127.0.0.1:6379> sadd lesan mongodb
(integer) 1
127.0.0.1:6379> sadd lesan mysql
(integer) 1
127.0.0.1:6379> smembers lesan
1) "redis"
2) "mysql"
3) "mongodb"
```

5. zset（有序集合）

Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。
不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。

zset的成员是唯一的,但分数(score)却可以重复。

```bash
127.0.0.1:6379> zadd lesan 0 redis
(integer) 1
127.0.0.1:6379> zadd lesan 0 mongodb
(integer) 1
127.0.0.1:6379> zadd lesan 0 mysql
(integer) 1
127.0.0.1:6379> zadd lesan 0 mysql
(integer) 0
127.0.0.1:6379> zrangebyscore lesan 0 100
1) "mongodb"
2) "mysql"
3) "redis"
```

6. Stream

Redis Stream 主要用于消息队列（MQ，Message Queue），Redis 本身是有一个 Redis 发布订阅 (pub/sub) 来实现消息队列的功能，但它有个缺点就是消息无法持久化，如果出现网络断开、Redis 宕机等，消息就会被丢弃。

而 Redis Stream 提供了消息的持久化和主备复制功能，可以让任何客户端访问任何时刻的数据，并且能记住每一个客户端的访问位置，还能保证消息不丢失。

### Redis 命令
1. key

Redis 键命令用于管理 redis 的键。

- `DEL KEY_NAME` 用于删除已存在的键。不存在的 key 会被忽略
- `DUMP KEY_NAME` 用于序列化给定 key ，并返回被序列化的值
- `EXISTS KEY_NAME` 用于检查给定 key 是否存在
- `Expire KEY_NAME TIME_IN_SECONDS` 用于设置 key 的过期时间，key 过期后将不再可用。单位以秒计
- `Expireat KEY_NAME TIME_IN_UNIX_TIMESTAMP` 用于以 UNIX 时间戳(unix timestamp)格式设置 key 的过期时间。key 过期后将不再可用
- `PEXPIRE key milliseconds` 以毫秒为单位设置 key 的生存时间
- `PEXPIREAT KEY_NAME TIME_IN_MILLISECONDS_IN_UNIX_TIMESTAMP` 设置 key 过期时间的时间戳(unix timestamp) 以毫秒计
- `KEYS PATTERN` 用于查找所有符合给定模式 pattern 的 key 
- `MOVE KEY_NAME DESTINATION_DATABASE` 用于将当前数据库的 key 移动到给定的数据库 db 当中
- `PERSIST KEY_NAME` 用于移除给定 key 的过期时间，使得 key 永不过期
- `PTTL KEY_NAME` 以毫秒为单位返回 key 的剩余过期时间
- `TTL KEY_NAME` 以秒为单位返回 key 的剩余过期时间
- `RANDOMKEY` 从当前数据库中随机返回一个 key
- `RENAME OLD_KEY_NAME NEW_KEY_NAME` 用于修改 key 的名称 
- `RENAMENX OLD_KEY_NAME NEW_KEY_NAME` 用于在新的 key 不存在时修改 key 的名称 
- `SCAN cursor [MATCH pattern] [COUNT count]` 用于迭代数据库中的数据库键
- `TYPE KEY_NAME` 用于返回 key 所储存的值的类型

> 更多命令请参考[菜鸟教程](https://www.runoob.com/redis/redis-commands.html)

## Redis 应用场景

1. 缓存

作为`Key-Value`形态的内存数据库，Redis 最先会被想到的应用场景便是作为数据缓存。而使用 Redis 缓存数据非常简单，只需要通过string类型将序列化后的对象存起来即可，不过也有一些需要注意的地方：

- 必须保证不同对象的 key 不会重复，并且使 key 尽量短，一般使用类名（表名）加主键拼接而成。
- 选择一个优秀的序列化方式也很重要，目的是提高序列化的效率和减少内存占用。
- 缓存内容与数据库的一致性，这里一般有两种做法：
    1. 只在数据库查询后将对象放入缓存，如果对象发生了修改或删除操作，直接清除对应缓存（或设为过期）。
    2. 在数据库新增和查询后将对象放入缓存，修改后更新缓存，删除后清除对应缓存（或设为过期）。

2. 数据共享分布式

String 类型，因为 Redis 是分布式的独立服务，可以在多个应用之间共享
例如：分布式Session

3. 分布式锁

在分布式环境下，单体锁已不在适用，Redis中string的set命令增加了一些参数：

- `EX`：设置键的过期时间（单位为秒）

- `PX`：设置键的过期时间（单位为毫秒）

- `NX`：只在键不存在时，才对键进行设置操作。 SET key value NX 效果等同于 SETNX key value 。

- `XX`：只在键已经存在时，才对键进行设置操作。

由于这个操作是原子性的，可以简单地以此实现一个分布式的锁，例如：
```
　　set lock_key locked NX EX 1 
```
如果这个操作返回`false`，说明 key 的添加不成功，也就是当前有人在占用这把锁。而如果返回`true`，则说明得了锁，便可以继续进行操作，并且在操作后通过`del`命令释放掉锁。并且即使程序因为某些原因并没有释放锁，由于设置了过期时间，该锁也会在 1 秒后自动释放，不会影响到其他程序的运行。

**推荐使用 redisson 第三方库实现分布式锁**

4. 全局ID

int类型，incrby，利用原子性
```
incrby userid 1000
```
分库分表的场景，一次性拿一段

5. 计数器

int类型，incr方法

例如：文章的阅读量、微博点赞数、允许一定的延迟，先写入Redis再定时同步到数据库

计数功能应该是最适合 Redis 的使用场景之一了，因为它高频率读写的特征可以完全发挥 Redis 作为内存数据库的高效。在 Redis 的数据结构中，string、hash和sorted set都提供了incr方法用于原子性的自增操作，下面举例说明一下它们各自的使用场景：

- 如果应用需要显示每天的注册用户数，便可以使用string作为计数器，设定一个名为REGISTERED_COUNT_TODAY的 key，并在初始化时给它设置一个到凌晨 0 点的过期时间，每当用户注册成功后便使用incr命令使该 key 增长 1，同时当每天凌晨 0 点后，这个计数器都会因为 key 过期使值清零。
- 每条微博都有点赞数、评论数、转发数和浏览数四条属性，这时用hash进行计数会更好，将该计数器的 key 设为weibo:weibo_id，hash的 field 为like_number、comment_number、forward_number和view_number，在对应操作后通过hincrby使hash 中的 field 自增。
- 如果应用有一个发帖排行榜的功能，便选择sorted set吧，将集合的 key 设为POST_RANK。当用户发帖后，使用zincrby将该用户 id 的 score 增长 1。sorted set会重新进行排序，用户所在排行榜的位置也就会得到实时的更新。

6. 限流

int类型，incr方法

以访问者的ip和其他信息作为key，访问一次增加一次计数，超过次数则返回false

7. 位统计

String类型的bitcount，字符是以8位二进制存储的
```
set k1 a
setbit k1 6 1
setbit k1 7 0
get k1
```
其中6 7 代表的a的二进制位的修改 a->01100001 b->01100010 因为bit非常节省空间，可以用来做大数据量的统计

8. 时间轴

`list`作为双向链表，不光可以作为队列使用。如果将它用作栈便可以成为一个公用的时间轴。当用户发完微博后，都通过`lpush`将它存放在一个 key 为`LATEST_WEIBO`的`list`中，之后便可以通过`lrange`取出当前最新的微博。

9. 消息队列

Redis 中list的数据结构实现是双向链表，所以可以非常便捷的应用于消息队列（生产者 / 消费者模型）。消息的生产者只需要通过lpush将消息放入 list，消费者便可以通过rpop取出该消息，并且可以保证消息的有序性。如果需要实现带有优先级的消息队列也可以选择sorted set。而pub/sub功能也可以用作发布者 / 订阅者模型的消息。无论使用何种方式，由于 Redis 拥有持久化功能，也不需要担心由于服务器故障导致消息丢失的情况。

List提供了两个阻塞的弹出操作：blpop/brpop，可以设置超时时间

- blpop：blpop key1 timeout 移除并获取列表的第一个元素，如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
- brpop：brpop key1 timeout 移除并获取列表的最后一个元素，如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。

上面的操作。其实就是java的阻塞队列。学习的东西越多。学习成本越低

- 队列：先进先除：rpush blpop，左头右尾，右边进入队列，左边出队列
- 栈：先进后出：rpush brpop

10. 抽奖

利用set结构的无序性,通过 Spop（ Redis Spop 命令用于移除集合中的指定 key 的一个或多个随机元素，移除后会返回移除的元素 ） 随机获得值

11. 点赞、签到、打卡

假如微博ID是t1001，用户ID是u3001

用 like:t1001 来维护 t1001 这条微博的所有点赞用户

- 点赞了这条微博：sadd like:t1001 u3001
- 取消点赞：srem like:t1001 u3001
- 是否点赞：sismember like:t1001 u3001
- 点赞的所有用户：smembers like:t1001
- 点赞数：scard like:t1001

是不是比数据库简单多了。

12. 好友关系、用户关注、推荐模型

这个场景最开始是是一篇介绍微博 Redis 应用的 PPT 中看到的，其中提到微博的 Redis 主要是用在在计数和好友关系两方面上，当时对好友关系方面的用法不太了解，后来看到《Redis 设计与实现》中介绍到作者最开始去使用 Redis 便是希望能通过set解决传统数据库无法快速计算集合中交集这个功能。后来联想到微博当前的业务场景，确实能够以这种方式实现，所以姑且猜测一下：

对于一个用户 A，将它的关注和粉丝的用户 id 都存放在两个 set 中：

- A:follow：存放 A 所有关注的用户 id
- A:follower：存放 A 所有粉丝的用户 id

那么通过`sinter`命令便可以根据A:follow和A:follower的交集得到与 A 互相关注的用户。当 A 进入另一个用户 B 的主页后，A:follow和B:follow的交集便是 A 和 B 的共同专注，A:follow和B:follower的交集便是 A 关注的人也关注了 B；通过`sdiff`命令便可得到差集，就可以得到用户间可能认识的人

13. 排行榜

使用`sorted set`(有序set)和一个计算热度的算法便可以轻松打造一个热度排行榜，`zrevrangebyscore`可以得到以分数倒序排列的序列，`zrank`可以得到一个成员在该排行榜的位置（是分数正序排列时的位置，如果要获取倒序排列时的位置需要用`zcard`-`zrank`）。

id 为 6001 的新闻点击数加1：`zincrby hotNews:20190926 1 n6001`

获取今天点击最多的15条：`zrevrange hotNews:20190926 0 15 withscores`


> 更多应用案例可以查看[本篇文章](https://blog.csdn.net/agonie201218/article/details/123640871)、或[本篇文章](https://blog.csdn.net/Legend_Young/article/details/104825982)

## Redis集群部署

### 主从复制

部署简单，分为一主一从，或一主N从。数据分布是在所有节点通过replication复制全量的数据。如果主节点挂掉，需要手动把其中的一个从节点设置为主节点

**实践：**

只需要在从库中执行`slaveof ip port`

### 哨兵模式

稍微比第一种复杂点，引入哨兵，此集群的原理还是主从复制。但是此集群中必须至少3个sentinel节点，来对一主两从的节点进行监控。因为sentinel里面存在一个Leader选举机制。必须是单数。此时sentinel(哨兵)其实就是一个Redis的特殊实例。此时的三个sentinel实例又组成了一个集群，两两互相监控，且这三个sentinel实例又分别都监控了所有的Redis节点。当一个主节点（Master）挂掉时，此集群方式会通过配置自动由对应的从节点（slave）变为主节点。如果一个主节点下有N个从节点，则进行选举机制来确定哪一个从节点变为主节点。此时所有节点的数据也都是全量的

### 分片模式

此集群是Redis从3.0版本开始支持，自带的一种集群方式。它的原理使用了分布的思想，其数据会均分到所有的主节点上。且有一个虚拟槽的概念。此部署方式，当数据量过大时，会让服务器均摊压力。在各个主节点上分配的数据都不是全量的。是分片存储的。目前此种部署方式在生产环境的较多


> 参考自：
> https://blog.csdn.net/u014659211/article/details/119805443
> https://blog.csdn.net/qq_42815754/article/details/82912130
