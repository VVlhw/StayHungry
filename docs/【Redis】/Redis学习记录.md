# VVlhw学Redis

> 前言

最近也着手写了两个项目，都是商城主题的。一个是微服务、一个是功能较完善的单体。正如狂神说的一样，现在能写这些已经是司空见惯了，要想进大厂还得继续学，**狂神金句：如果学不死就往死里学**

这次学了redis，收获了很多，起码在之前的项目中是没有使用到redis缓存的，而这对于真实环境中却是十分需要

**本文打算针对狂神的redis教程进行画重点、概括，方便自己以后面试快速回忆**

?> 狂神redis教程（很好的老师，鸡汤也很浓）：https://www.bilibili.com/video/BV1S54y1R7SB   详细资料：参考狂神的PDF文件

 



## NoSQL历程、对比、CAP、BASE理论

> 是什么

NoSQL = Not Only SQL （不仅仅是SQL）

> 区别我们常用的关系型数据库

`记忆：表对数据结构、SQL对没SQL、性能高低、扩展难易、事务的区别、数据类型是多样型`

关系数据库管理系统（Relational Database Management System：*RDBMS*）

**一、关系型数据库**

关系型数据库最典型的数据结构是表，由二维表及其之间的联系所组成的一个数据组织

优点：

1、易于维护：都是使用表结构，格式一致；

2、使用方便：SQL语言通用，可用于复杂查询；

3、复杂操作：支持SQL，可用于一个表以及多个表之间非常复杂的查询。

缺点：

1、读写性能比较差，尤其是海量数据的高效率读写；

2、固定的表结构，灵活度稍欠；

3、高并发读写需求，传统关系型数据库来说，<mark>硬盘I/O是一个很大的瓶颈</mark>。

---



**二、非关系型数据库**

非关系型数据库<mark>严格上不是一种数据库，应该是一种数据结构化存储方法的集合，可以是文档或者键值对等。</mark>

优点：

1、格式灵活：存储数据的格式可以是key,value形式、文档形式、图片形式等等，文档形式、图片形式等等，使用灵活，应用场景广泛，而关系型数据库则只支持基础类型。

2、速度快：nosql<mark>可以使用硬盘或者随机存储器作为载体，而关系型数据库只能使用硬盘</mark>；

3、高扩展性；

4、成本低：nosql数据库部署简单，基本都是开源软件。

缺点：

1、<mark>不提供sql支持 </mark>，学习和使用成本较高；

2、~~无事务处理~~；redis有事务，只是整个事务不像MySQL保证原子性

3、数据结构相对复杂，复杂查询方面稍欠。

------------------------------------------------
版权声明：本文为CSDN博主「aaronthon」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/aaronthon/article/details/81714528

> 为什么要用NoSQL，发展历程，归根到底还是数据量大

`单机MySQL`：读写混合、一个机器内存不够

`Memcached（缓存） + MySQL + 垂直拆分 （读写分离）`

`分库分表 + 水平拆分 + MySQL集群`

但现在数据量爆发增长，量大，文件内容也大，数据类型也多，格式还各种各样，表非常难设计（就算设计好了，又有新变化。。），关系型已经不够用，而Nosql 可以很好的处理以上的情况

> NoSQL的分类（常见）

**1、文档型：MongoDB**

**2、key-value型：Redis**

**3、列式数据库：HBase**

**4、图形数据库：Neo4J**

> 扩展

分布式系统中<mark>CAP理论和BASE理论  </mark>

`CAP理论`：一个分布式系统最多只能同时满足以下**三项中的两项（视场景取舍某一项）**：

- 一致性（Consistency）【某更新，其他能否看到】、
- 可用性（Availability）【停机时间来评判】、
- 分区容错性（Partition tolerance）【某地方出故障仍能对外提供服务的能力】

?> 注：一致性又分：弱一致性、最终一致性、严格一致性

`BASE理论`：对CAP理论的延伸，BASE是指

- 基本可用（Basically Available）【允许损失部分可用性，即保证核心可用】、
- 软状态（ Soft State）【允许不同节点间副本同步的**延时**就是软状态的体现】、
- 最终一致性（ Eventual Consistency）【所有数据副本经过一定时间后，最终能够达到一致的状态】

参考博客：https://www.jianshu.com/p/2a2d8556dad7



`异地多活`：其实我觉得就是CAP理论中的分区容错性，每个数据有相应的副本在其他地方，即使某个地方出问题了，也能继续对外提供服务

参考博客：https://www.jianshu.com/p/d0fc8b378ecf



## Redis

> 是什么

Redis（Remote Dictionary Server )，即远程字典服务 ! C语言写的

> 特性

**1、多样的数据类型**

**2、持久化**

**3、集群**

**4、事务**

> windows、linux安装、redis-benchmark性能测试参考狂神PDF

> 基本知识

Redis**默认有16个数据库，默认用第0号**

**默认端口6379（粉丝效应）**

```bash
select 3  # 切换3号数据库
DBSIZE  # 查看当前DB大小！Key的个数
keys *  # 查看数据库所有的key
flushdb # 清除当前数据库
FLUSHALL # 清除全部数据库的内容
```

查看redis是否启动完成：`ps -ef | grep redis`

## 五大数据类型

### Redis-key

```bash
keys *   # 查看所有的key
EXISTS key名字  # 判断当前的key是否存在
move key名字 到某个数据库号  # 移动数据key名到相应的数据库
EXPIRE key名字 几秒过期  # 设置key的过期时间，单位是秒
ttl key名字  # 查看当前key的剩余时间，-1永不过期 -2已过期（或不存在）
type key名字  # 查看当前key的一个类型！
```



### String

```bash
set key1 v1   # 设置值
get key1    # 获得值
del key1  # 删除
APPEND key1 "hello"  # 追加字符串，如果当前key不存在，就相当于set
STRLEN key1  # 获取字符串的长度！
#################################################
incr views  # 自增1 浏览量变为1
decr views  # 自减1  浏览量-1
INCRBY views 10  # 可以设置步长，指定增量！
DECRBY views 5 # 可以设置步长，指定减量！
#################################################
GETRANGE key1 0 3   # 截取字符串 [0,3]
GETRANGE key1 0 -1  # 获取全部的字符串 和 get key1是一样的
SETRANGE key2 1 xx  # 替换指定位置开始的字符串！
#################################################
# setex (set with expire)  # 设置过期时间
# setnx (set if not exist)  # 不存在在设置 （在分布式锁中会常常使用！）
setex key3 30 "hello" # 设置key3 的值为 hello,30秒后过期
setnx mykey "redis"  # 如果mykey 不存在，创建mykey
#################################################
mset k1 v1 k2 v2 k3 v3  # 同时设置多个值
mget k1 k2 k3  # 同时获取多个值
msetnx k1 v1 k4 v4  # msetnx 是一个原子性的操作，要么一起成功，要么一起失败！
#################################################
# 对象
set user:1 {name:zhangsan,age:3} # 设置一个user:1 对象 值为 json字符来保存一个象！
# 这里的key是一个巧妙的设计： user:{id}:{filed} , 如此设计在Redis中是完全OK了！
mset user:1:name zhangsan user:1:age 2
#################################################
getset db redis  # 先get然后在set。如果不存在值，则返回 nil
```

### List

所有的list命令都是`用l开头的，Redis不区分大小命令`

```bash
LPUSH list one  # 将一个值或者多个值，插入到列表头部 （左）
Rpush list two  # 将一个值或者多个值，插入到列表位部 （右）
Lpushx list one # list存在才能放
Rpushx list two # 同上
#################################################
Lpop list  # 移除list的第一个元素
Rpop list  # 移除list的最后一个元素
BLpop list 等待时间 # 用于实现轻量消息队列，没东西则阻塞等待，等待时间0代表无限等待
RLpop list 等待时间 # 同上，多参数的话，依次检查多个列表，弹出第一个非空列表
#################################################
lindex list 0
Llen list  # 返回列表的长度
LRANGE list 0 -1  # 获取list中值！从上到下就是从左到右、从下标0开始
lrem list 1 one  # 移除list集合中【指定个数】的value，精确匹配，两边删最高效，因为链表
ltrim mylist 1 2  # 通过下标截取指定的长度，list已经被改变了，截断了只剩下截取的元素！
#################################################
rpoplpush l1 l2  # 移除l1列表的最后一个元素，将他移动到新的l2列表中！
lset list 0 item  # 如果存在，更新当前下标的值，【列表or下标不存在则报错】
LINSERT mylist before "world" "other"  # 插入到某个value的前面
LINSERT mylist after world new # 插入到某个value的后面，可以不双引号
```

### Set

所有的set命令都是`用s开头的，Redis不区分大小命令`

```bash
sadd myset "hello"  # set集合中添加
spop myset  # 【随机】删除一些set集合中的元素！因为数据结构，不是有序集合
smove myset myset2 "kuangshen" # 将一个指定的值，移动到另外一个set集合！
srem myset hello  # 移除set集合中的指定元素
#################################################
SMEMBERS myset   # 查看指定set的所有值
SISMEMBER myset hello   # 判断某一个值是不是在set集合中！
scard myset  # 获取set集合中的内容元素个数！
SRANDMEMBER myset [可指定个数]  # 随机抽选出一个元素，可指定
#################################################
SDIFF key1 key2   # 差集
SINTER key1 key2  # 交集  共同好友就可以这样实现
SUNION key1 key2  # 并集
```

### Zset

有序集合

```bash
zadd myset 1 one   # 添加一个值
zadd myset 2 two 3 three  # 添加多个值
zrem salary xiaohong  # 移除有序集合中的指定元素
#################################################
ZRANGE myset 0 -1
zcard salary  # 获取有序集合中的个数
zcount myset 1 3  # 获取指定区间的成员数量！
#################################################
ZRANGEBYSCORE salary -inf +inf  # 根据key来排序，只能从小到大！
ZREVRANGE salary 0 -1 # 从大到小进行排序！
ZRANGEBYSCORE salary -inf +inf withscores # 显示全部的用户并且附带value值
```



### Hash

只是key-value上这个value是一个map，**更适合存对象**

```bash
hset myhash name kuangshen  # set一个具体 key-vlaue
hmset myhash field1 hello field2 world  # set多个 key-vlaue，不用加{}
hsetnx myhash field4 hello  # 如果不存在则可以设置,【存在则不能设置】
hdel myhash field1  # 删除hash指定key字段！对应的value值也就消失了！
HINCRBY myhash field3 1
#################################################
hget myhash name  # 获取一个字段值
hmget myhash field1 field2  # 获取多个字段值
hgetall myhash  # 获取全部的数据
hlen myhash  # 获取hash表的字段数量！
HEXISTS myhash field1  # 判断hash中指定字段是否存在！
hkeys myhash  # 只获得所有field
hvals myhash  # 只获得所有value
```

## 三种特殊数据类型

> Geospatial 地理位置

**应用：朋友的定位，附近的人，打车距离计算**

> Hyperloglog 基数统计，即不重复数的个数

应用：如果我们用set来统计不重复数的个数，其实放进去的那个内容只是用作了判断是否相同，意义并不大，这个内容一旦所占内存高，或者内容数量多，就很浪费内存。

**因此引入了这个占用内存很少，能存2^64 不同的元素的技术（一般也达不到这么多）**

```bash
PFadd mykey a b c d e f g h i j  # 创建第一组元素 mykey
PFCOUNT mykey  # 统计 mykey 元素的基数数量
PFMERGE mykey3 mykey mykey2  # 合并两组 mykey mykey2 => mykey3 并集
```

**有0.81% 错误率**，`如果允许容错，那么建议使用 Hyperloglog ！`
如果不允许容错，就使用 set 或者自己的数据类型即可！

> Bitmap 位图，适合只有两种状态的存储，占空间少

```bash
setbit test 0 1 # 设置0下标为 1状态 / 0状态
getbit test 0	# 获取0下标的状态
bitcount test	# 统计该test所有1状态的个数
```

## 事务

**Redis事务不像MySQL，redis没有隔离级别的概念！**

Redis`单条命令`保证原子性的，但是<mark>整个redis事务不保证原子性（区别MySQL）！  </mark>

所有的命令在事务中，**命令被序列化**，并没有直接被执行！只有**发起执行命令Exec的时候才会按顺序执行！**

```bash
multi   # 开启事务
# ....一系列命令
DISCARD  # 取消事务后，命令都不会被执行！
exec  # 执行事务
```

!> 编译型异常（代码有问题！ 命令有错！） ，事务中所有的命令都不会被执行！

!> 运行时异常（1/0）， 如果事务队列中存在语法性，那么执行命令的时候，其他命令是可以正常执行的，错误命令抛出异常！

## watch实现乐观锁

?> `watch key名`，测试过watch之后，某个线程改了又改回来原来的值，发现确实不行，确实watch是实现了乐观锁，内部应该有版本号，并不是根据key的value值来判断（ABA问题）



## SpringBoot整合

一开始是Jedis，即Java操作Redis，详细参考狂神的PDF

!> 在 SpringBoot2.x 之后，原来使用的jedis 被替换为了` lettuce`

jedis : 采用的直连，多个线程操作的话，是不安全的，如果想要避免不安全的，使用 jedis pool 连接池！ 更像 BIO 模式

lettuce : 采用netty，实例可以再多个线程中进行共享，不存在线程不安全的情况！可以减少线程数据了，更像 NIO 模式



?> 开发使用狂神提供的配置类、工具类即可



## 配置问题

> 连接远程redis

1. 开放端口

2. 在redis的配置文件`redis.conf`中，找到`bind localhost`注释掉。

3. `ps -ef | grep redis` 验证（看后面的`*`），`*`就可以看出此时是允许所有的ip连接登录到这台redis服务上

4. 设置参数`protected-mode` 为 no，关闭redis的保护模式

5. 设置密码，因为不设就都能访问了，requirepass [password]

6. 本机连接的方式：redis-cli -h host -p port -a password



> 更多配置内容参考狂神PDF



## 持久化

**redis是内存数据库，内存断电即失**，它有如下两种方式持久化

- RDB（Redis DataBase）
- AOF（Append Only File）



**默认就是RDB方式，一般不用修改**

rdb保存的文件是`dump.rdb` ，都是在我们的配置文件中快照中进行配置的。

![](images\Snipaste_2020-07-24_16-23-41.png)

> 触发机制

1、save的规则满足的情况下，会自动触发rdb规则

2、执行 flushall 命令，也会触发我们的rdb规则！

3、退出redis，也会产生 rdb 文件！

备份就自动生成一个 dump.rdb

恢复的话：`config get dir`命令输出的路径中有dump.rdb文件即可

> 优缺点

优点：

1、适合大规模的数据恢复！

2、对数据的完整性要不高！

缺点：

1、需要一定的时间间隔进程操作！如果redis意外宕机了，这个**最后一次修改数据就没有的了**！

2、fork进程的时候，会占用一定的内容空间！！



?> RDB、AOF分割线----------------



**记录每一次的写操作，即通过重新执行所有的写操作来恢复**

Aof保存的是 `appendonly.aof` 文件

默认是不开启的，在配置文件打开

修复aof文件，redis 给我们提供了一个工具 `redis-check-aof --fix`

![](images/Snipaste_2020-07-24_17-11-12.png)

> 优缺点

优点：

1、每一次修改都同步，**文件的完整会更加好**！

2、每秒同步一次，可能会丢失一秒的数据

3、从不同步，效率最高的！

缺点：

1、相对于数据文件来说，aof远远大于 rdb，修复的速度也比 rdb慢！

2、Aof 运行效率也要比 rdb 慢，所以我们redis默认的配置就是rdb持久化！

> 扩展

只做缓存，如果你只希望你的数据在服务器运行的时候存在，你也可以不使用任何持久化

RDB、AOF**都开启的话，优先使用AOF**，因为AOF数据完整性更好



## 发布订阅

通过 `PUBLISH 、SUBSCRIBE 和 PSUBSCRIBE` 等命令实现发布和订阅功能

具体参考狂神PDF

使用场景：

1、实时消息系统！

2、事实聊天！（频道当做聊天室，将信息回显给所有人即可！）

3、订阅，关注系统都是可以的！

稍微复杂的场景我们就会使用 消息中间件 MQ 



## 主从复制、哨兵模式

主从复制，是指将一台Redis服务器的数据，复制到其他的Redis服务器。前者称为主节点
(master/leader)，后者称为从节点(slave/follower)；

数据的复制是单向的，只能由主节点到从节点。
Master以写为主，Slave 以读为主。
**默认情况下，每台Redis服务器都是主节点**



?> 主要就是为了分担一台Redis服务器的压力，读写分离，作为写的那方需要将最新数据复制给从机。
而哨兵模式就是能保证这多台redis服务器的高可用性，
比如主机断了，能通过投票选举某个从机当老大，并且原来的小弟跟着他；
还有就是断的恢复后能自动跟上当前老大。
这在以前都是得要手动，哨兵模式（Sentinel）的出现使得能够自动化。

!> 哨兵模式本质就是一个独立的进程，发请求监控着多个redis实例。哨兵可能出问题，所以有多哨兵模式（也监控着其他哨兵），有问题了，就通过自带的发布订阅去通知来修改相应的配置文件



!> Slave 启动成功连接到 master 后会发送一个sync同步命令，这里涉及全量复制、增量复制两个概念，其实区别就是前者是全部内容同步，后者是主机更新，就发来新增加的更新内容同步



## 缓存穿透、击穿、雪崩

> 缓存穿透（查不到）

用户想要查询一个数据，发现redis内存数据库没有，也就是缓存没有命中，于是向持久层数据库查询。发现也没有，于是本次**查询失败。当用户很多的时候，缓存都没有命中，于是都去请求了持久层数据库**。这会给持久层数据库造成很大的压力，这时候就相当于出现了缓存穿透。



?> 解决：`布隆过滤器`、缓存空对象（浪费缓存空间）

> 缓存击穿（量太大，人品不好，刚要查，缓存过期了）

**当这个key在失效的瞬间，持续的大并发就穿破缓存，直接请求数据库，就像在一个屏障上凿开了一个洞**。当某个key在过期的瞬间，有大量的请求并发访问，这类数据一般是热点数据，由于缓存过期，会同时访问数据库来查询最新数据，并且回写缓存，会导使数据库瞬间压力过大。



?> 解决：热点数据永不过期（不现实）、加互斥锁（只有一个线程能查，从而减少数据库压力）

> 缓存雪崩（缓存集中过期失效）

对于热点数据，我们可能提前将其放入缓存，等时间一过期，这个压力就落在了 缓存服务器上、数据库上。**因为又要忙着从数据库查询，又要写入缓存，很容易导致缓存服务器宕机，一旦宕机，数据库压力就更大了**



?> 解决：redis高可用、限流降级、数据预热（设置不同过期时间，让缓存失效的时间点尽量均匀）

## 应用场景

**String：**

- 计数器，如粉丝数
- 短信验证码、session key，可以方便地设置过期时间
- 把对象缓存存储，一般使用Hash类型，更方便
- setnx实现分布式锁



**List：**

- 利用LRANGE可以很方便的实现list内容分页的功能。
- 同方向的Push、pop做栈
- 利用BLpop / BRpop命令，不同方向的做轻量级消息队列、~~阻塞队列~~ https://segmentfault.com/a/1190000012244418 、

**Set：**

- 共同好友

- 需要去重的列表，比如该页面有多少人看（一个ip算一次，就涉及去重）

- set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的




**Zset：**

- 排序，TopN

- 做带权重的队列




**Hash：**

- 存放结构化数据，如对象，比String类型好用




**其他：**

1、内存存储、持久化，内存中是断电即失、所以说持久化很重要（rdb、aof）
2、效率高，可以用于高速缓存
3、自身的发布订阅。复杂用消息队列MQ：RocketMQ、RabbitMQ、Kafka
4、地图信息分析，使用Geospatial类型



## 遇到的问题

> 安装报各种结构体找不到的错：
>
> `Redis server.c:5103:19: server.c:5174:31: error: ‘struct redisServer’ has no member named ‘server_cp`

原因是最新版本6.0.5与gcc版本不兼容，参考博客：https://blog.csdn.net/kawayiyy123/article/details/107110946/



> make test 有两个err：
> `err: PEXPIRE/PSETEX/PEXPIREAT can set sub-second expires in tests/unit/expire.tcl
> Expected 'somevalue {}' to equal or match '{} {}'
> err: pending querybuf: check size of pending_querybuf after set a big value in tests/unit/pendingq`

第一个错误改改参数，参考博客：https://blog.csdn.net/w433668/article/details/82315468

第二错误是自己服务器内存不足（学生机：怪我咯），关了一些docker容器就可以了



## 资料

官网：https://redis.io/

中文网：http://www.redis.cn/

阿里云的这群疯子：https://developer.aliyun.com/article/653511 。看完真的很激动，佩服他们，梁汉伟加油~