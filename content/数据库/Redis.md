```json
	{
        "date":"2022.12.09 15:33"，
    }
```

***Redis*** 是典型的 ***NoSQL*** 数据库。

***redis官网***：https://redis.io/download

> ***Redis*** 是一个开源的 ***key-value*** 存储系统。
>
> 和 ***Memcached*** 类似，它支持存储的 ***value*** 类型相对更多，包括 ***string、list、set、zset、sorted set、hash***。
>
> 这些数据类型都支持 ***push/pop、add/remove*** 及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。
>
> 在此基础上，***Redis*** 支持各种不同方式的排序。
>
> 与 ***memcached***一样，为了保证效率，数据都是缓存在内存中。
>
> 区别的是 ***Redis*** 会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件。
>
> 并且在此基础上实现了***master-slave*** （主从）同步。
>
> 单线程 + ***IO*** 多路复用。

## windos常用的服务命令

```markdown
开启服务：redis-server --service-start 
停止服务：redis-server --service-stop 
卸载服务：redis-server --service-uninstall
```

## 常用的五大数据类型

### 键 key

#### 常用命令

```markdown
*
keys *:查看当前库所有key 
exists key :判断某个key是否存在  
type key :查看key的类型 
del key :删除指定的key数据 
unlink key:根据value选择非阻塞删除（仅将key从keyspace元数据中删除，真正的删除会在后续异步操作）
expire key 10 10秒钟 :为给定的key设定过期时间 ttl key 查看还有多少秒过期，-1为永不过期，-2表示已过期
select :命令切换数据库 
dbsize : 查看当前数据库的key的数量 
flushdb : 清空当前库 
flushall : 清空全部库
```

### 字符串 string

***String*** 类型是二进制安全的。意味着 ***Redis*** 的 ***string*** 可以包含任何数据。比如 ***jpg*** 图片或者序列化的对象。

***String*** 类型是 ***Redis*** 最基本的数据类型，一个 ***Redis*** 中字符串 ***value*** 最多可以是 512M。

#### 常用命令

```markdown
set <key><value>：添加键值对

get <key>：查询对应键值

append <key><value>：将给定的 <value> 追加到原值的末尾

strlen <key>：获得值的长度

setnx <key><value>：只有在 key 不存在时，设置 key 的值

incr <key>：将 key 中储存的数字值增 1，只能对数字值操作，如果为空，新增值为 1（具有原子性）

decr <key>：将 key 中储存的数字值减 1，只能对数字值操作，如果为空，新增值为 -1

incrby/decrby <key><步长>：将 key 中储存的数字值增减。自定义步长

mset <key1><value1><key2><value2> ：同时设置一个或多个 key-value 对

mget <key1><key2><key3>...：同时获取一个或多个 value

msetnx <key1><value1><key2><value2>... ：同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在 有一个失败则所有都失败

getrange <key><起始位置><结束位置>：获得值的范围

setrange <key><起始位置><value>：用 <value> 覆写 <key> 所储存的字符串值

setex <key><过期时间><value>：设置键值的同时，设置过期时间，单位秒。

getset <key><value>：以新换旧，设置了新值同时获得旧值。
```

#### 原子性

所谓 **原子** 操作是指不会被线程调度机制打断的操作；

这种操作一旦开始，就一直运行到结束，中间不会有任何 ***context switch*** （切换到另一个线程）。

- 在单线程中， 能够在单条指令中完成的操作都可以认为是”原子操作”，因为中断只能发生于指令之间。
- 在多线程中，不能被其它进程（线程）打断的操作就叫原子操作。

***Redis*** 单命令的原子性主要得益于 ***Redis*** 的单线程。

#### 数据结构

内部结构实现上类似于 ***Java*** 的 ***ArrayList***，采用预分配冗余空间的方式来减少内存的频繁分配.

当字符串长度小于1M时，扩容都是加倍现有的空间，如果超过1M，扩容时只会多扩1M的空间，字符串最大长度是512M

![img](./images/202206241239397.png)

### 列表 list

***Redis*** 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。

它的底层实际是个双向链表，对两端的操作性能很高，通过索引下标的操作中间的节点性能会较差。

[![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241239264.png)](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241239264.png)

#### 常用命令

```markdown
lpush/rpush <key><value1><value2><value3> ....： 从左边/右边插入一个或多个值。 
example：
lpush k1 v1 v2 v3
lrange k1 0 -1
输出：v3 v2 v1
----
rpush k1 v1 v2 v3
rrange k1 0 -1
输出：v1 v2 v3

lpop/rpop <key>：从左边/右边吐出一个值。值在键在，值光键亡。

rpoplpush <key1><key2>：从 <key1> 列表右边吐出一个值，插到 <key2> 列表左边。

lrange <key><start><stop>：按照索引下标获得元素（从左到右）

lrange mylist 0 -1 0：左边第一个，-1右边第一个，（0 -1表示获取所有）

lindex <key><index>：按照索引下标获得元素（从左到右）

llen <key>：获得列表长度

linsert <key> before/after <value><newvalue>：在 <value> 的前面/后面插入 <newvalue> 插入值

lrem <key><n><value>：从左边删除 n 个 value（从左到右）

ltrim <key><start><end>：按照索引截取下标元素（从左到右）

lset<key><index><value>：将列表 key 下标为 index 的值替换成 value
```

#### 数据结构

***List*** 的数据结构为快速链表 ***quickList***。

首先在列表元素较少的情况下会使用一块连续的内存存储，这个结构是 ***ziplist***，也即是压缩列表。

它将所有的元素紧挨着一起存储，分配的是一块连续的内存。

当数据量比较多的时候才会改成 ***quicklist***。

因为普通的链表需要的附加指针空间太大，会比较浪费空间。比如这个列表里存的只是 ***int*** 类型的数据，结构上还需要两个额外的指针 ***prev*** 和 ***next***。

***Redis*** 将链表和 ***ziplist*** 结合起来组成了 ***quicklist***。也就是将多个 ***ziplist*** 使用双向指针串起来使用。这样既满足了快速的插入删除性能，又不会出现太大的空间冗余。

[![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241240221.png)](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241240221.png)

### 集合 Set

***Set*** 对外提供的功能与 ***List*** 类似列表的功能，特殊之处在于 ***Set*** 是可以 **自动排重** 的，当需要存储一个列表数据，又不希望出现重复数据时，***Set*** 是一个很好的选择，并且 ***Set*** 提供了判断某个成员是否在一个 ***Set*** 集合内的重要接口，这个也是 ***List*** 所不能提供的。

***Redis*** 的 ***Set*** 是 ***String*** 类型的无序集合。它底层其实是一个 ***value*** 为 ***null*** 的 ***hash*** 表，所以添加，删除，查找的复杂度都是 ***O(1)***。

一个算法，随着数据的增加，执行时间的长短，如果是 ***O(1)***，数据增加，查找数据的时间不变。

#### 常用命令

```markdown
sadd <key><value1><value2> ..... ：将一个或多个 member 元素加入到集合 key 中，已经存在的 member 元素将被忽略

smembers <key>：取出该集合的所有值。

sismember <key><value>：判断集合 <key> 是否为含有该 <value> 值，有返回 1，没有返回 0

scard<key>：返回该集合的元素个数。

srem <key><value1><value2> ....：删除集合中的某个元素

spop <key>：随机从该集合中吐出一个值

srandmember <key><n>：随机从该集合中取出 n 个值，不会从集合中删除

smove <source><destination>value：把集合中一个值从一个集合移动到另一个集合

sinter <key1><key2>：返回两个集合的交集元素

sunion <key1><key2>：返回两个集合的并集元素

sdiff <key1><key2>：返回两个集合的差集元素（key1 中的，不包含 key2 中的）
```

#### 数据结构

***Set*** 数据结构是字典，字典是用哈希表实现的。

### 哈希 hash 

***Redis hash*** 是一个键值对集合。

***Redis hash*** 是一个 ***String*** 类型的 ***field*** 和 ***value*** 的映射表，***hash*** 特别适合用于存储对象。

#### 常用命令

```markdown
hset <key><field><value>：给 <key> 集合中的 <field> 键赋值 <value>

hget <key1><field>：从 <key1> 集合 <field> 取出 value

hmset <key1><field1><value1><field2><value2>...： 批量设置 hash 的值

hexists <key1><field>：查看哈希表 key 中，给定域 field 是否存在

hkeys <key>：列出该 hash 集合的所有 field

hvals <key>：列出该 hash 集合的所有 value

hincrby <key><field><increment>：为哈希表 key 中的域 field 的值加上增量 1 -1

hsetnx <key><field><value>：将哈希表 key 中的域 field 的值设置为 value ，当且仅当域 field 不存在
```

#### 数据结构

***Hash*** 类型对应的数据结构是两种：***ziplist***（压缩列表），***hashtable***（哈希表）。

当 ***field-value*** 长度较短且个数较少时，使用 ***ziplist***，否则使用 ***hashtable***。

### 有序集合 Zset

***Redis*** 有序集合 ***zset*** 与普通集合 ***set*** 非常相似，是一个没有重复元素的字符串集合。

不同之处是有序集合的每个成员都关联了一个评分（***score***）,这个评分（***score***）被用来按照从最低分到最高分的方式排序集合中的成员。集合的成员是唯一的，但是评分可以是重复的。

因为元素是有序的，所以可以很快的根据评分（***score***）或者次序（***position***）来获取一个范围的元素。

访问有序集合的中间元素也是非常快的，因此能够使用有序集合作为一个没有重复成员的智能列表。

#### 常用命令

```markdown
zadd <key><score1><value1><score2><value2>…：将一个或多个 member 元素及其 score 值加入到有序集 key 当中

zrange <key><start><stop> [WITHSCORES] ：返回有序集 key 中，下标在 <start><stop> 之间的元素

当带 WITHSCORES，可以让分数一起和值返回到结果集

zrangebyscore key min max [withscores] [limit offset count]：返回有序集 key 中，所有 score 值介于 min 和 max 之间（包括等于 min 或 max ）的成员。有序集成员按 score 值递增（从小到大）次序排列。

zrevrangebyscore key max min [withscores] [limit offset count] ：同上，改为从大到小排列

zincrby <key><increment><value>：为元素的 score 加上增量

zrem <key><value>：删除该集合下，指定值的元素

zcount <key><min><max>：统计该集合，分数区间内的元素个数

zrank <key><value>：返回该值在集合中的排名，从 0 开始。
```

#### 数据结构

***SortedSet（zset）***是 ***Redis*** 提供的一个非常特别的数据结构，一方面它等价于 ***Java*** 的数据结构 ***Map<String, Double>***，可以给每一个元素 ***value*** 赋予一个权重 ***score***，另一方面它又类似于 ***TreeSet***，内部的元素会按照权重 ***score*** 进行排序，可以得到每个元素的名次，还可以通过 ***score*** 的范围来获取元素的列表。

***zset*** 底层使用了两个数据结构

- ***hash***，***hash*** 的作用就是关联元素 ***value*** 和权重 ***score***，保障元素 ***value*** 的唯一性，可以通过元素 ***value*** 找到相应的 ***score*** 值
- 跳跃表，跳跃表的目的在于给元素 ***value*** 排序，根据 ***score*** 的范围获取元素列表

## Redis的发布与订阅

***Redis*** 发布订阅（ ***pub/sub*** ）是一种消息通信模式：发送者（ ***pub*** ）发送消息，订阅者（ ***sub*** ）接收消息。

***Redis*** 客户端可以订阅任意数量的频道。

1. 客户端可以订阅频道

[![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241241271.png)](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241241271.png)

1. 当给这个频道发布消息后，消息就会发送给订阅的客户端

[![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241242008.png)](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241242008.png)

```markdown
subscribe channel # 订阅频道
publish channel hello # 频道发送信息
```

## 新数据类型

### Bitmaps

**Redis** 提供了 **Bitmaps** 这个 “数据类型” 可以实现对位的操作：

**Bitmaps** 本身不是一种数据类型， 实际上它就是字符串（**key-value**） ， 但是它可以对字符串的位进行操作。

**Bitmaps** 单独提供了一套命令， 所以在 **Redis** 中使用 **Bitmaps** 和使用字符串的方法不太相同。 可以把 **Bitmaps** 想象成一个以位为单位的数组， 数组的每个单元只能存储 0 和 1， 数组的下标在 **Bitmaps** 中叫做偏移量。

![img](https://img-blog.csdnimg.cn/8a4a79f6f1a5499594baff4fe2451808.png)

#### 常用命令

```markdown
setbit <key> <offset> <value> : 设置键的第offset个位的值（从0算起）
在第一次初始化Bitmaps时，假如偏移量非常大，那么整个初始化过程执行会比较慢，可能会造成Redis的阻塞

getbit <key> <offset> ： 获取键的第offset位的值（从0开始算） 

bitcount <key>（start end）： 获取Bitmaps指定范围bit位值为1的个数
[start]和[end]代表起始和结束字节数

bitop and(or/not/xor）<destkey> key[key1...] ： 复合操作，交并非异或，结果保存在destkey
```

### HyperLogLog

**Redis** **HyperLogLog** 是用来做基数统计的算法，**HyperLogLog** 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的

在 **Redis** 里面，每个 **HyperLogLog** 键只需要花费 12 KB 内存，就可以计算接近 2^64个不同元素的基数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比

但是，因为**HyperLogLog**只会根据输入元素来计算基数，而不会存储元素本身，所以**HyperLogLog**不能像[集合](https://so.csdn.net/so/search?q=集合&spm=1001.2101.3001.7020)那样，返回输入的各个元素。

#### 常用命令

```markdown
pfadd <key> element [element...] : 添加指定元素到hyperloglog中

pfcount <key> [key...] : 返回给定hyperloglog 的基数估算值

pfmerge <destkey> <sourcekey> [sourcekey...] : 将多个hyperloglog 合并为一个hyperloglog

示例：
127.0.0.1:6379> PFADD mykey a b c d e f g h i j 
1
127.0.0.1:6379> PFCOUNT mykey 
10
127.0.0.1:6379> PFADD mykey2 i j z x c v b n m 
1
127.0.0.1:6379> PFMERGE mykey3 mykey mykey2 
OK
127.0.0.1:6379> PFCOUNT mykey3 15
```

### Geospatial

**GEO**，**Geographic**，地理信息的缩写。该类型，就是元素的 2 维坐标，在地图上就是**经纬度**。**redis** 基于该类型，提供了经纬度设置，查询，范围查询，距离查询，经纬度 **Hash** 等常见操作。

#### 常用命令

```markdown
geoadd <key> <longitude> <latitude> <member> [longitude latitude member...] : 添加地理位置（经度，纬度，名称）

geops <key> <member> [member...] : 获得指定地区的坐标值

geodist <key> <member1> <member2> [m|km|ft|mi] : 获取两个位置之间的直线距离 m：米 km：千米 ft：英尺 mi：英里 默认单位为米

georadius <key> <longitude> <latitude> radius m|km|ft|mi
: 以给定的经纬度为中心，找出某一半径内的元素（经度，纬度，距离，单位）

```

## 事务和锁机制

***Redis*** 事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断

***Redis*** 事务的主要作用就是串联多个命令防止别的命令插队。

### *Multi*、*Exec*、*Discard*

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241242519.png)

从输入 ***Multi*** 命令开始，输入的命令都会依次进入命令队列中，但不会执行，直到输入 ***Exec*** 后，***Redis*** 会将之前的命令队列中的命令依次执行。

组队的过程中可以通过 ***Discard*** 来放弃组队。

**组队成功，提交成功**：

<img src="https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241243427.png" alt="img" style="zoom:70%;" />

**放弃组队**：

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241243951.png)

**组队中有命令错误，不会执行：** 当组队中某个命令出现了报告错误，执行时整个的所有队列都会被取消。

<img src="https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241244858.png" alt="img" style="zoom:80%;" />

**组队中不报错，执行时报错：** 当执行时出现错误，队列中其他命令可执行成功

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241244271.png)

### 悲观锁

悲观锁（***Pessimistic Lock***），顾名思义，就是很悲观。即每次去拿数据的时候都认为有其他线程会修改，所以每次在拿数据的时候都会上锁，这样其他线程想要拿到这个数据就会被 ***block*** 直到成功拿到锁。（效率低）

传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前上锁

### 乐观锁

乐观锁（***Optimistic Lock***），顾名思义，就是很乐观。即每次去拿数据的时候都认为其他线程不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间有没有其他线程去更新这个数据，可以使用版本号等机制。

**乐观锁适用于多读的应用类型，这样可以提高吞吐量**。

***Redis*** 就是利用这种 ***check-and-set*** 机制实现事务的。

![image-20220905203508380](C:\Users\K\AppData\Roaming\Typora\typora-user-images\image-20220905203508380.png)

### Watch、unwatch命令

在执行 ***multi*** 之前，先执行 ***watch key1 [key2]\**\*，可以监视一个（或多个 ）\**\*key*** 。如果在事务执行之前这个 ***key*** 被其他命令所改动，那么事务将被打断。

取消 ***WATCH*** 命令对所有 ***key*** 的监视。如果在执行 ***WATCH*** 命令之后，***EXEC*** 命令或 ***DISCARD*** 命令先被执行，那么就不需要再执行 ***UNWATCH*** 。

### 事务三特性

- 单独的隔离操作

  事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。

- 没有隔离级别的概念

  队列中的命令没有提交之前都不会实际被执行，因为事务提交前任何指令都不会被实际执行。

- 不保证原子性

  事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚 

## 持久化

### RDB

在指定的时间间隔内将内存中的数据集快照写入磁盘， 即 ***Snapshot*** 快照，恢复时是将快照文件直接读到内存里。

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241245966.png)

***Redis*** 会单独（***fork***）创建一个子进程来进行持久化。

先将数据写入到一个临时文件中，待持久化过程完成后，再将这个临时文件内容覆盖到 ***dump.rdb***。

整个过程中，主进程是不进行任何 ***IO*** 操作的，这就确保了极高的性能。如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那 ***RDB*** 方式要比 ***AOF*** 方式更加的高效。

**RDB** **的缺点是最后一次持久化后的数据可能丢失。**

#### Fork

- 作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等） 数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程
- 在 ***Linux*** 程序中，***fork()*** 会产生一个和父进程完全相同的子进程，但子进程在此后多会 ***exec*** 系统调用，出于效率考虑，***Linux*** 中引入了 **写时复制技术**
- **一般情况父进程和子进程会共用同一段物理内存**，只有进程空间的各段的内容要发生变化时，才会将父进程的内容复制一份给子进程

#### 配置

**dump 文件名字**

在 ***redis.conf*** 中配置文件名称，默认为 ***dump.rdb***。

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241245089.png)

**dump 保存位置**

***rdb*** 文件的保存路径可以修改。默认为 ***Redis*** 启动时命令行所在的目录下。

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241245951.png)

***stop-writes-on-bgsave-error***

即当 ***redis*** 无法写入磁盘，关闭 ***redis*** 的写入操作。

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241246038.png)

***rdbcompression***

持久化的文件是否进行压缩存储。

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241246917.png)

***rdbchecksum***

完整性的检查，即数据是否完整性、准确性。

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241247735.png)

***save***

表示写操作的次数。（格式：save 秒 写操作次数）

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241247583.png)

#### 优点

- 适合大规模的数据恢复；
- 对数据完整性和一致性要求不高更适合使用；
- 节省磁盘空间；
- 恢复速度快。

#### 缺点

- ***Fork*** 的时候，内存中的数据被克隆了一份，大致 2 倍的膨胀性需要考虑；
- 虽然 ***Redis*** 在 ***fork*** 时使用了**写时拷贝技术**，但是如果数据庞大时还是比较消耗性能；
- 在备份周期在一定间隔时间做一次备份，所以如果 ***Redis*** 意外 ***down*** 掉的话，就会丢失最后一次快照后的所有修改。

### AOF

以日志的形式来记录每个写操作（增量保存），将 ***Redis*** 执行过的所有写指令记录下来（读操作不记录）， 只许追加文件但不可以改写文件，***Redis*** 启动之初会读取该文件重新构建数据，换言之，如果 ***Redis*** 重启就会根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。

**执行流程**

- 客户端的请求写命令会被 ***append*** 追加到 ***AOF*** 缓冲区内；
- ***AOF*** 缓冲区根据 ***AOF*** 持久化策略 `[always,everysec,no]` 将操作 ***sync*** 同步到磁盘的 ***AOF*** 文件中；
- ***AOF*** 文件大小超过重写策略或手动重写时，会对 ***AOF*** 文件 ***Rewrite*** 重写，压缩 ***AOF*** 文件容量；
- ***Redis*** 服务重启时，会重新 ***load*** 加载 ***AOF*** 文件中的写操作达到数据恢复的目的。

***AOF*** 和 ***RDB*** 同时开启时，系统默认读取 ***AOF*** 的数据（数据不会存在丢失）

#### 配置

**AOF 默认不开启**

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241248962.png)

**文件名字**

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241248396.png)

**AOF 同步频率设置**

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241249139.png)

***appendfsync always***

 始终同步，每次 ***Redis*** 的写入都会立刻记入日志；

 性能较差但数据完整性比较好。

***appendfsync everysec***

 每秒同步，每秒记入日志一次，如果宕机，本秒的数据可能丢失。

***appendfsync no***

 ***Redis*** 不主动进行同步，把同步时机交给操作系统。

**Rewrite 压缩**

当 ***AOF*** 文件的大小超过所设定的阈值时，***Redis*** 就会启动 ***AOF*** 文件的内容压缩，只保留可以恢复数据的最小指令集。可以使用命令 ***bgrewriteaof***。

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241249997.png)

#### 优点

- 备份机制更稳健，丢失数据概率更低；
- 可读的日志文本，通过操作 ***AOF*** 稳健，可以处理误操作。

#### 缺点

- 比起 ***RDB*** 占用更多的磁盘空间；
- 恢复备份速度要慢；
- 每次读写都同步的话，有一定的性能压力；
- 存在个别 ***Bug***，造成不能恢复

### 选择

官方推荐两个都启用。

如果对数据不敏感，可以选单独用 ***RDB***。

不建议单独用 ***AOF***，因为可能会出现 ***Bug***。

如果只是做纯内存缓存，可以都不用。

## 主从复制

主机数据更新后根据配置和策略， 自动同步到备机的 ***master/slaver*** 机制，***Master*** 以写为主，***Slaver*** 以读为主。

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241250697.png)

1. 读写分离，性能扩展
2. 容灾快速恢复
3. 一主多从！

### 搭建一主两从

1. 创建文件目录 ： `/opt/etc`
2. 将 ***redis.conf*** 复制到当前目录 ： `cp /etc/redis.conf /opt/etc/`
3. 创建 3 个 ***redis.conf*** 配置文件

```
# redis6379.conf
include /opt/etc/redis.conf
pidfile /var/run/redis_6379.pid
port 6379
dbfilename dump6379.rdb

# redis6380.conf
include /opt/etc/redis.conf
pidfile /var/run/redis_6380.pid
port 6380
dbfilename dump6380.rdb

# redis6381.conf
include /opt/etc/redis.conf
pidfile /var/run/redis_6381.pid
port 6381
dbfilename dump6381.rdb
```

4. 启动 3 台 ***redis*** 服务器

   ![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241250298.png)

5. 查看主机运行情况 ： info replication

   ![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241251598.png)

6. 配从不配主 ： slaveof  <ip><port>  # 成为某个实例的从服务器

   ![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241251672.png)

7. 再次查看主机运行情况

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241252641.png)

成功搭建

### 一主两仆

主机 ***6379***，从机 ***6380*** 和 ***6381***。

1. 假设从机6380挂掉。

   当6380重启后，不再是6379的从机，而是新的master；

   当再次把6380作为6379的从机加入后，从机会把数据从头到尾复制

2. 假设主机6379挂掉。

   6380和6381仍然是6379的从机，不会做任何事情；

   当6379重启后，依然是主服务器

### 薪火相传

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241253497.png)

上一个 ***slave*** 可以是下一个 ***slave*** 的 ***master***，***slave*** 同样可以接收其他 ***slave***的连接和同步请求，那么该 ***slave*** 作为了链条中下一个的 ***master***，可以有效减轻 ***master*** 的写压力，去中心化降低风险。

`slaveof <ip><port>`

中途变更转向：会清除之前的数据，重新建立拷贝最新的。

当某个 ***slave*** 宕机，后面的 ***slave*** 都没法备份。

即当主机挂掉，从机还是从机，但是无法继续写数据。

### 反客为主

当一个 ***master*** 宕机后，后面的 ***slave*** 可以立刻升为 ***master***，其后面的 ***slave*** 不用做任何修改。

`slaveof no one`

### 哨兵模式

**反客为主的自动版**，即能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库。

1. 创建 ***sentinel.conf*** 文件：`/opt/etc/sentinel.conf`

2. 配置哨兵

   ```
   sentinel monitor mymaster 172.16.88.168 6379 1
   
   # mymaster：监控对象起的服务器名称
   # 1：至少有多少个哨兵同意迁移的数量。 
   ```

3. 启动哨兵

   `redis-sentinel  /opt/etc/sentinel.conf `

   ![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241253197.png)

   主机挂掉，会从从机选举产生新的主机

#### 选举规则

- 根据优先级别，***slave-priority/replica-priority***，优先选择优先级靠前的。

  ![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241253826.png)

- 根据偏移量，优先选择偏移量大的。

- 根据 ***runid***，优先选择最小的服务。

#### 复制延时

由于所有的写操作都是先在 ***master*** 上操作，然后同步更新到 ***slave*** 上，所以从 ***master*** 同步到 ***slave*** 从机有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，***slave*** 机器数量的增加也会使这个问题更加严重。

### 复制原理

- ***slave*** 启动成功连接到 ***master*** 后会**主动**发送一个 ***sync*** 命令（同步命令）。
- ***master*** 接到命令启动后台的存盘进程，对数据进行持久化操作，同时收集所有接收到的用于修改数据集命令，在后台进程执行完毕之后，***master*** 将传送整个数据文件（***rdb***）到 ***slave***，以完成一次完全同步。
- 当主服务进行写操作后，和从服务器进行数据同步。
- 全量复制：而 ***slave*** 服务在接收到数据库文件数据后，将其存盘并加载到内存中。
- 增量复制：***master*** 继续将新的所有收集到的修改命令依次传给 ***slave***，完成同步。
- 只要是重新连接 ***master***，一次完全同步（全量复制）将被自动执行。

## 集群

当容量不够，***redis*** 如何进行扩容？

在并发写操作，**redis**如何分摊？

主从模式，薪火相传模式，主机宕机，导致 ***ip*** 地址发生变化，应用程序中配置需要修改对应的主机地址、端口等信息。

解决方法：

- 代理主机（ ***之前*** ）

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241254954.png)

- 无中心化集群配置（ ***redis3.0*** ）

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241255698.png)

***Redis*** 集群实现了对 ***Redis*** 的水平扩容，即启动 ***N*** 个 ***Redis*** 节点，将整个数据库分布存储在这 ***N*** 个节点中，每个节点存储总数据的 ***1/N*** 。

***Redis*** 集群通过分区（***partition***）来提供一定程度的可用性（***availability***），即使集群中有一部分节点失效或者无法进行通讯， 集群也可以继续处理命令请求。

### 搭建 Redis 集群

1. 创建配置文件

   ```markdown
   # 以redis6379.conf为例
   include /opt/etc/redis.conf
   pidfile /var/run/redis_6379.pid # 更改
   port 6379 # 更改
   dbfilename dump6379.rdb # 更改
   cluster-enabled yes # 打开集群模式
   cluster-config-file nodes-6379.conf # 设置节点配置文件名称，需要更改
   cluster-node-timeout 15000 # 设置节点失联事件，超过该时间（ms），集群自动进行主从切换
   ```

   ![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241255522.png)

2. 启动

   ![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241255653.png)

3. 将6个节点合成一个集群

   组合之前请确保所有`redis`实例启动后，`node-xxxx.conf`文件都生成正常

   ![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241256147.png)

   ```markdown
   # 进入redis安装目录
   /opt/redis-6.2.6/src
   
   # 执行
   redis-cli --cluster create --cluster-replicas 1 172.16.88.168:6379 172.16.88.168:6380 172.16.88.168:6381 172.16.88.168:6389 172.16.88.168:6390 172.16.88.168:6391
   ```

4. 采用集群策略连接

   ```markdown
   redis-cli -c -p PORT
   cluster nodes # 命令查看集群信息
   ```

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241257848.png)

### 问题

#### ***redis cluster* 如何分配这六个节点?**

一个集群至少要有三个主节点。

选项 `--cluster-replicas 1`，表示希望为集群中的每个主节点创建一个从节点。

分配原则尽量保证每个主数据库运行在不同的 ***IP*** 地址，每个从库和主库不在一个 ***IP*** 地址上。

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241258001.png)

#### **什么是 *slots*？**

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241259789.png)

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241259691.png)

一个 ***Redis*** 集群包含 ***16384*** 个插槽（***hash slot***）， 数据库中的每个键都属于这 ***16384*** 个插槽的其中一个。

集群使用公式 ***CRC16(key) % 16384*** 来计算键 ***key*** 属于哪个槽， 其中 ***CRC16(key)*** 语句用于计算键 ***key*** 的 ***CRC16*** 校验和 。

集群中的每个节点负责处理一部分插槽。 例如， 如果一个集群可以有主节点， 其中：

- 节点 ***A*** 负责处理 ***0*** 号至 ***5460*** 号插槽。
- 节点 ***B*** 负责处理 ***5461*** 号至 ***10922*** 号插槽。
- 节点 ***C*** 负责处理 ***10923*** 号至 ***16383*** 号插槽。

#### **如何在集群中录入值？**

在 ***redis-cli*** 每次录入、查询键值，***redis*** 都会计算出该 ***key*** 应该送往的插槽，如果不是该客户端对应服务器的插槽，***redis*** 会报错，并告知应前往的 ***redis*** 实例地址和端口。

***redis-cli*** 客户端提供了 ***–c*** 参数实现自动重定向。

例如 ***redis-cli -c –p 6379*** 登入后，再录入、查询键值对可以自动重定向

#### **如何查询集群中的值？**

每个主机只能查询自己范围内部的插槽。

`cluster keyslot `：查询某个 ***key*** 的 ***slot* **。

`cluster countkeysinslot `：查询某个 ***slot*** 是否有值。

`CLUSTER GETKEYSINSLOT `：返回 ***count*** 个 ***slot*** 槽中的键。

#### 故障恢复？

如果主节点下线？从节点能否自动升为主节点？注意：***15*** 秒超时。

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241259357.png)

- 当 ***6379*** 挂掉后，***6389*** 成为新的主机。

主节点恢复后，主从关系会如何？主节点回来变成从机。

- 当 ***6379*** 重启后，***6379*** 成为 ***6389*** 的从机。

如果所有某一段插槽的主从节点都宕掉，***redis*** 服务是否还能继续?

- 如果某一段插槽的主从都挂掉，而 ***cluster-require-full-coverage=yes***，那么 ，整个集群都挂掉。
- 如果某一段插槽的主从都挂掉，而 ***cluster-require-full-coverage=no***，那么，该插槽数据全都不能使用，也无法存储。

`redis.conf` 中的参数 `cluster-require-full-coverage`

### 优点

- 实现扩容；
- 分摊压力；
- 无中心配置相对简单

### 缺点

- 多键操作是不被支持的；
- 多键的 ***Redis*** 事务是不被支持的。***lua*** 脚本不被支持；
- 由于集群方案出现较晚，很多公司已经采用了其他的集群方案，而代理或者客户端分片的方案想要迁移至***redis cluster***，需要整体迁移而不是逐步过渡，复杂度较大。

## 应用问题解决

### 缓存穿透

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241300146.png)

#### 现象

***key*** 对应的数据在数据源并不存在，每次针对此 ***key*** 的请求从缓存获取不到，请求都会压到数据源，从而可能压垮数据源。

比如用一个不存在的用户 ***id*** 获取用户信息，不论缓存还是数据库都没有，若黑客利用此漏洞进行攻击可能压垮数据库。

造成：

1. 应用服务器压力变大。
2. ***redis*** 命中率下降  => 查询数据库 。

#### 如何解决

- **对空值缓存**

  如果一个查询返回的数据为空（不管是数据是否不存在），仍然把这个空结果（***null***）进行缓存，设置空结果的过期时间会很短，最长不超过五分钟。

- **设置可访问的名单（白名单）：**

  使用 ***bitmaps*** 类型定义一个可以访问的名单，名单 ***id*** 作为 ***bitmaps*** 的偏移量，每次访问和 ***bitmap*** 里面的 ***id*** 进行比较，如果访问 ***id*** 不在 ***bitmaps*** 里面，进行拦截，则不允许访问。

- **采用布隆过滤器**

  布隆过滤器（***Bloom Filter***）是1970年由布隆提出的。它实际上是一个很长的二进制向量（位图）和一系列随机映射函数（哈希函数）。

  布隆过滤器可以用于检索一个元素是否在一个集合中。它的优点是空间效率和查询时间都远远超过一般的算法，缺点是有一定的误识别率和删除困难。

  将所有可能存在的数据哈希到一个足够大的 ***bitmaps*** 中，一个一定不存在的数据会被这个 ***bitmaps*** 拦截掉，从而避免了对底层存储系统的查询压力。

- **进行实时监控**

  当发现 ***Redis*** 的命中率开始急速降低，需要排查访问对象和访问的数据，和运维人员配合，可以设置黑名单限制服务。

### 缓存击穿

![img](https://tsuiraku.oss-cn-chengdu.aliyuncs.com/typora/202206241301946.png)

***key*** 对应的数据存在，但在 ***redis*** 中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端***DB*** 加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端 ***DB*** 压垮。

#### 现象

1. 数据库访问压力瞬间增大。
2. ***redis*** 中没有出现大量 ***key*** 过期，***redis*** 正常运行。
3. （即某个经常访问的 ***key*** 过期，突然有大量访问这个数据）

#### 如何解决

- 预先设置热门数据

  在 ***redis*** 高峰访问之前，把一些热门数据提前存入到 ***redis*** 里面，加大这些热门数据 ***key*** 的时长。

- 实时调整

  现场监控哪些数据热门，实时调整 ***key*** 的过期时长。

- 使用锁

### 缓存雪崩

***key*** 对应的数据存在，但在 ***redis*** 中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端***DB*** 加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端 ***DB*** 压垮。

缓存雪崩与缓存击穿的区别在于这里针对很多 ***key*** 缓存，前者则是某一个 ***key***。

#### 现象

1. 数据库压力变大。
2. 即极少的时间段，查询大量 ***key*** 的集中过期情况。

#### 如何解决

- **构建多级缓存架构**

  ***nginx*** 缓存 + ***redis*** 缓存 + 其他缓存（***ehcache***等）

- **使用锁或队列：**

  用加锁或者队列的方式来保证不会有大量的线程对数据库一次性进行读写，从而避免失效时大量的并发请求落到底层存储系统上。不适用高并发情况。

- **设置过期标志更新缓存：**

  记录缓存数据是否过期（设置提前量），如果过期会触发通知另外的线程在后台去更新实际 ***key*** 的缓存。

- **将缓存失效时间分散开：**

  比如我们可以在原有的失效时间基础上增加一个随机值，比如 1～5 分钟随机，这样每一个缓存的过期时间的重复率就会降低，就很难引发集体失效的事件。

### 分布式锁

https://blog.csdn.net/Me_xuan/article/details/124418176

