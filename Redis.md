# Redis

> [Redis 教程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/redis/redis-tutorial.html)

​	REmote DIctionary Server(Redis) 是一个由 Salvatore Sanfilippo 写的 key-value 存储系统，是跨平台的非关系型数据库。

## 安装步骤

### Linux

1. 解压tar包：`tar zxvf redis.xxx.tar.gz`。
2. 进入解压完成的redis文件夹内执行`make`命令。
3. 继续执行`make install`命令
4. redis安装完成，默认安装地址 `/usr/local/bin`。

## 使用

### 库

* `select <db>`：切换数据库
* `dbsize`：查看当前数据库键值对的数量
* `flushdb`：清空当前库
* `flushall`：清空全部库

### 键值对

* `keys*`：查看当前库所有的键

* `exists <key>`：判断某个键值对是否存在

* `type <key>`：查看键所指向的值的数据类型

* `del <key>`：即刻删除键值对

* `unlink <key>`：异步删除键值对

* `expire <key> <expireTime>`：设置过期时间

* `ttl <key>`：查看键过期状态
  * -1：永不过期
  * -2：已过期

### String


* `set <key> <value>`：设置一个键值对
* `setnx <key> <value>`：当key不存在时，才能设置一个键值对。
* `setex <key> <expireTime> <value>` ：设置键值对的同时设置过期时间。
* `mset <key1> <value> <key2> <value2> ...`：同时设置多个键值对
* `msetnx <key1> <value> <key2> <value2> ...`：同时设置多个键值对，当且仅当所给定的键都不存在。
* `setrange <key> <index> <value>`：从index位置(索引从0开始)用value覆写key所存储的字符串值


* ​	`get <key>`：得到键对应的值。


* `mget <key1> <key2> ...`：同时得到多个指定key的值。


* `getrange <key> <start> <end>`：得到指定键的值的指定范围的数据。


* `getset <key> <value>`：设置新值的同时获取旧值。


* `append <key> <value>`：将value值追加到键指向的值末尾。


* `strlen <key>`：得到键对应值的长度。


* `incr <key>`：对存储在指定key的数值执行原子的加1操作。


* `decr <key>`：对存储在指定key的数值执行原子的减1操作。


* `incrby <key> <stepValue>`：将数字值增加指定值。


* `decrby <key> <stepValue>`：将数字值减少指定值。

### List

* `lpush/rpush <key> <value1> <value2> ...`：从左/右插入一个或多个值。
* `lpop/rpop <key>`：从左/右边弹出一个值，若列表中无值存在时，键消亡。
* `rpoplpush <key1> <key2>`：从<key1>列表中弹出一个值至<key2>列表左边。
* `lrange <key> <start> <end>`：从左开始按照下标获取元素。
  * start为0代表左边第一个
  * end为-1代表右边第一个
* `lindex <key> <index>`：获取列表中指定下标的元素（从左到右）。
* `llen <key>`：获取列表的长度
* `linsert <key> before/after <value> <newValue>`：在值的后方或前方插入新值。
* `lrem/rrem <key> <n> <value>`：从左/右移除n个value。
* `lset <key> <index> <value>`：将列表中下标为index的元素替换为value。 

### Set

* `sadd <key> <value1> <value2> ...`：将一个或多个元素放入集合key中，已存在的元素将被忽略。
* `smembers <key>`：取出集合中的所有元素。
* `sismember <key> <value>`：检查value是否存在于集合中，存在为1，不存在为0。
* `scard <key>`：返回该集合的元素个数
* `srem <key> <value1> <value2> ...`移除集合中的元素。
* `spop <key>`：随机从集合中弹出一个值。
* `srandmember <key> <n>`：随即从集合中取出n个值。
* `smove <key1> <key2> <value>`：将值从一个集合移至另一个集合。
* `sinner <key1> <key2>`：取两个集合的交集元素。
* `sunion <key1> <key2>`：取两个集合的并集元素。
* `sdiff <key1> <key2>`：取两个集合的差集元素。

### Hash

* `hset <key> <field> <value>`：在集合中设置一个键值对
* `hget <key> <filed>`：在集合中取出一个键值对
* `hmset <key> <filed1> <value1> <filed2> <value2> `：在集合中批量设置键值对
* `hexists <key> <filed>`：查看集合中是否存在指定的键
* `hkeys <key>`：列出该集合中所有的键。
* `hvals <key>`：列出集合中所有的值。
* `hincby <key> <filed> <increment>`：给集合中指定键的值(数字类型)增加指定值
* `hsetnx <key> <field> <value>`：当且仅当集合中给定的键不存在时，在集合中设置一个新的键值对。

### Zset

* `zadd <key> <score1> <value1> <score2> <value2> ...`：在集合中添加带评分的元素。
* `zrange <key> <start> <end> [withscores]`：返回集合中下标在start和end之间的元素，`[withscores]`代表同时返回评分。 
* `zrangebyscore <key> <min> <max> [withscores] [limit offset count]`：从小到大返回指定评分区间的元素，`[withscores]`代表同时返回评分。 
* `zrevrangebyscore <key> <max> <min> [withscores] [limit offset count]`：从大到小返回指定评分区间的元素，`[withscores]`代表同时返回评分。 
* `zincrby <key> <increment> <value>`：为集合中的指定元素增加指定的评分。
* `zrem <key> <value>`：删除指定集合中指定的元素。
* `zcount <key> <min> <max>`：查看指定集合中指定评分区间元素的数量。
* `zrank <key> <value>`：返回指定集合中指定元素的排名。

## 数据结构与对象

### 简单动态字符串

​	String是Redis最基本的类型。它是二进制安全的，即它可以存储任何数据。一个字符串型的数据的大小最多为512M。

#### 与C字符串比较

* 杜绝缓冲区溢出：C字符串拼接时，如未给字符串分配足够的空间时，可能拼接后的数据会溢出到其他数据所在的空间中。SDS的空间分配策略完全杜绝了发生缓冲区溢出的可能性，当SDS API 需要对SDS进行修改时，API会先检查SDS的孔家你是否满足修改所需的要求，不满足的话，API会将SDS的空间扩展至执行修改所需的大小，然后才执行实际的修改操作。
* 每次增长或缩短一个C字符串，程序都需要对这个C字符串进行一次内存重分配操作。如果忘了这一步就会产生缓冲区溢出或内存泄漏。内存重分配操作通常是一个比较耗时的工作。Redis作为数据库，频繁进行内存重分配操作可能会对性能造成影响。SDS通过未使用空间解除了字符串长度和底层数组长度之间的关联。数组里面可以包含未使用的字节，这些字节的数量由SDS的free属性记录。

---

通过未使用空间，SDS实现了空间预分配和惰性空间释放。

#### 空间预分配



#### 底层实现

​	String的数据结构为简单动态字符串（Simple Dynamic String，缩写SDS）。是可以修改的字符串。采用预分配策略的方式来减少内存的频繁分配。

* 具有属性

  * free：空余空间
  * length：当前长度
  * arr[]：内容

* 预分配策略

  * 当长度小于1M时，free = lengt。

  * 当长度大于等于1M时，free稳定等于1M。

* 惰性删除

### 哈希-Hash

​	存储键值对映射关系。

#### 底层实现

​	底层使用压缩列表和哈希表实现，当长度较短时使用压缩列表，否则使用哈希表。

### 列表-List

​	简单的字符串列表，按照插入顺序排序。可以添加一个元素到列表的头部或者尾部。

#### 底层实现

​	元素较少的情况下会使用一块连续的内存，这个结构是压缩列表（ziplist）。

​	元素较多的情况下是节点为压缩列表的快速链表（quickList）。

### 集合-Set

​	元素不会重复的字符串列表。	

#### 底层实现

​	底层是Hash，哈希的所有value都指向同一个内部值。

### 有序集合-Zset

​	没有重复元素的字符串集合，其中每个元素都关联了评分，这个评分用于从低到高排序集合中的元素。

#### 底层实现

​	Zset底层使用两种数据结构：

* Hash：目的用于关联元素和元素的权重。
* 跳表：目的在于给元素排序，根据score的范围获取元素列表。

## 配置文件

1. Units：配置大小单位，只支持字节，不支持bit。
2. INCLUDES：引入其他配置文件
3. NETWORK：网络相关配置
   1. bind：配置访问请求，不写即无限制接收任何IP地址的访问。
   2. protected-mode：保护模式，关闭即可支持远程访问。
   3. port：端口号。
   4. tcp-backlog：backlog是一个连接队列，队列总和=完成三次握手队列+正在完成三次握手队列。
      1. 高并发环境下需要一个高tcp-backlog值避免慢客户端连接问题。
      2. Linux内核会将这个值减少至/proc/sys/net/core/somaxconn的值（128），所以需要确认增加/proc/sys/net/core/somaxconn和/proc/sys/net/ipv4/tcp_max_syn_backlog（128）两个值来达到想要的效果。
   5. timeout：控制客户端维持的时长，0代表永不关闭。
   6. tcp-keepalive：心跳检测间隔，如果未检测到客户端的心跳，则断开连接。
4. GENERAL：通用配置
   1. daemonize：设置为yes后可以后台启动。
   2. pidfile：存放pidfile文件的位置。
   3. loglevel：设置日志级别。
   4. logfile：设置日志的输出路径。
   5. database：设定库的数量，默认16，默认数据库为0。可以使用`SELECT <dbid>`命令在连接上指定数据库id。
5. SECURITY：安全配置。
   1. requirepass：设置密码。
6. LIMITS：限制配置。
   1. maxclients：设置客户端连接数量，默认10000。如果达到了此限制，Redis会拒绝新的请求。
   2. maxmemory：设置可以使用的内存容量。一旦达到上限，Redis会试图移除内部数据。移除规则可以通过`maxmemory-policy`指定。
   3. maxmemory-policy：内存满时移除键值对的规则。
      1. volatile-lru：使用LRU算法移除设置了过期时间的键值对。
      2. allkeys-lru：使用LRU算法移除键值对。
      3. volatile-random：随机移除设置了过期时间的键值对。
      4. allkeys-random：随机移除键值对。
      5. volatile-ttl：移除近期要过期的键值对。
      6. noeviction：没有键值对会被移除，只是返回错误信息。
   4. maxmemory-samples：LRU和最小TTL算法都不算是精确的算法，这个参数设置LRU算法和最小TTL算法一次检查的键值对。

## 发布和订阅

​	Redis的发布订阅是一种消息通信模式，发送者发送消息，订阅者接收消息。

## 事务

​	Redis事务是一个单独的隔离操作：事务中的所有命令都会被序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。

​	Redis事务的主要作用就是串联多个命令防止别的命令插队。

1. Multi：开启事务，对后续命令进行组队
2. Exec：执行队伍中的命令
3. discard：类似MySQL中事务的回滚，取消组队命令产生的影响。

### 事务的三特性

1. 单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行过程中，不会被其他客户端发送的命令所打断。
2. 没有隔离级别的概念：事务提交前任何指令都不会被实际执行。
3. 不保证原子性：事务中一条命令的执行错误不会影响其他命令的执行。

### 监视Key

1. `watch key1 ...`：监视一个或多个key，事务执行前这些key被改动，那么事务就被打断。

## 持久化

### AOF

​	以日志的形式来记录每个写操作，将Redis执行过的所有指令记录下来，只许追加文件但不可以改写文件，redis启动之初会读取该文件重建数据。

#### 工作过程

1. 客户端的请求写命令会被append追加到AOF缓冲区。
2. AOF缓冲区根据AOF持久化策略将操作sync同步到磁盘的AOF文件中。
3. AOF文件大小超出重写策略或手动重写时，会对AOF文件重写，压缩AOF文件大小。

#### 修复损坏的AOF文件

​	通过/usr/local/bin/redis-check-aof --fix appendonly.aof进行修复。

### RDB

​	保存数据库的键值对来记录数据库的状态。

​	在指定的时间间隔内将内存中的数据集快照写入磁盘，恢复时将快照文件读入内存中。

#### 工作过程

​	Redis单独创建一个子进程来进行持久化，会先将数据写入到一个文件中，待持久化过程结束后，在用这个临时文件替换上次持久化的文件。整个过程中，主进程是不会进行任何IO操作的，这就确保了极高的性能，如果要进行大规模的数据恢复，且对于数据恢复的完整性不是很敏感，那么RDB的方式要比AOF方式更加的高效。

​	缺点：最后一次持久化的数据可能会丢失。

## 主从复制

​	主机数据更新后根据配置和策略，自动同步到备机的机制。

### 原理



### 优势

1. 读写分离
2. 容灾快速恢复

### 薪火相传

​	一个从服务器的主服务器可以是另一个从服务器。

### 反客为主

​	从服务器在主服务器宕机的情况下成为主服务器。

​	使用`slaveof no one`将从机变成主机。

## 哨兵模式

​	可以监控主机是否故障，如果故障了根据投票数自动将从库转换为主库。

​	选举条件依次为：

1. 优先级`repliac-priority`靠前的。
2. 偏移量最大的。
3. runid最小的。

### 步骤   

1. `新建sentinel.conf`配置文件。
2. 配置文件中书写`sentinel monitor <master_name> <address> <port> 1` 。

## 集群