### Redis

********

#### Redis简介

> Redis（Remote Dictionary Server )，即远程字典服务。
>
> 非关系型数据库(`NoSQL`)。
>
> redis是一个数据库，不过与传统数据库不同，**redis的缓存是存在内存中的**，所以读写速度非常快，因此redis被广泛应用于缓存方向。
>
> 用处：缓存方向、分布式锁；redis提供了多种数据类型支持不同的业务场景；redis支持事务、持久化、LUA脚本、LRU驱动事件、多种集群方案。

##### Redis能干什么？

1. 内存存储、持久化（rdb、aof）
2. 效率高，可以用户高速缓存
3. 发布订阅信息
4. 地图信息分析
5. 计数器、计数器



#### Redis与MySQL的区别

- **类型上**
  - **mysql是关系型数据库**
  - **redis是非关系型数据库**
- **数据存放位置**
  - **mysql用于持久化的存储数据到硬盘，功能强大，但是速度较慢**
  - **redis用于存储使用较为频繁的数据到缓存中，读取速度快**



#### [为什么用redis而不用数据库](https://www.cnblogs.com/chengxuyuanzrc/articles/13707183.html)

**处理高并发的时候，需要用到redis。**

**redis是缓存机制。**

**至于用redis是因为它内嵌了LRU（最近的先访问）。**

**太过于频繁访问数据库，容易造成数据库奔溃。而且数据库数据量大的时候，特别耗时。**

**先访问经常使用的数据，可以避免资源浪费**

##### 为什么用Redis/为什么用缓存

- 高性能：假如用户第一次访问数据库中的某些数据。这个过程会比较慢，因为是从硬盘上读取的。将该用户访问的数据存在缓存中，这样下一次再访问这些数据的时候就可以直接从缓存中获取了。操作缓存就是直接操作内存，所以速度相当快。如果数据库中的对应数据改变的之后，同步改变缓存中相应的数据即可！

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210406201142040.png" alt="image-20210406201142040" style="zoom:40%;" />

- 高并发：直接缓存能够承受的请求是远远大于直接访问数据库的，所以我们可以考虑把数据库中的部分数据转移到缓存中，这样用户的一部分请求会直接到缓存这里而不用经过数据库。

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210921102027185.png" style="zoom:43%">

##### 为什么用redis而不是map/guava做缓存？

缓存分为本地缓存和分布式缓存。

- 以Java为例，使用自带的map或者guava实现的是本地缓存，最主要的特点是轻量以及快速，生命周期随着jvm的销毁而结束，并且在多实例的情况下，每个实例都需要各自保存一份缓存，缓存不具有一致性。
- 使用redis或memcached之类的称为分布式缓存，在多实例的情况下，各实例共用一份缓存数据，缓存具有一致性。缺点是需要保持redis 或memcached服务的高可用，整个程序架构上较为复杂。

> 1. Redis 可以用几十 G 内存来做缓存，Map 不行，一般 JVM 也就分几个 G 数据就够大了
> 2. Redis 的缓存可以持久化，Map 是内存对象，程序一重启数据就没了
> 3. Redis 可以实现分布式的缓存，Map 只能存在创建它的程序里
> 4. Redis 可以处理每秒百万级的并发，是专业的缓存服务，Map 只是一个普通的对象
> 5. Redis 缓存有过期机制，Map 本身无此功能
> 6. Redis 有丰富的 API，Map 就简单太多

#### redis和memcached的区别

- **redis支持更丰富的数据类型(支持更复杂的应用场景)：**Redis不仅仅支持简单的k/v类型的数据，同时还提供list，set，zset，hash等数据结构的存储。memcache支持简单的数据类型，String。
- **Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用，而Memecache把数据全部存在内存之中**。
- **集群模式：**memcached没有原生的集群模式，需要依靠客户端来实现往集群中分片写入数据；但是redis目前是原生支持cluster模式的。
- **Memcached是多线程，非阻塞I0复用的网络模型；Redis使用单线程的多路I0复用模型。**由于 redis 只使用单核，而 memcached 可以使用多核，所以平均每一个核上 redis 在存储小数据时比 memcached 性能更高。而在 100k 以上的数据中，memcached 性能要高于 redis。

![image-20210406215009010](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210406215009010.png)

#### Redis的单线程模型

> redis内部使用文件事件处理器`file event handler`，**这个文件事件处理器是单线程的，所以redis才叫做单线程的模型**。它采用IO多路复用机制同时监听多个socket，根据socket上的事件来选择对应的事件处理器进行分析。
>
> **官方表示：Redis是基于内存操作，CPU不是Redis性能的瓶颈，Redis性能的瓶颈是机器的内存和网络的带宽，既然可以用单线程，就使用单线程了。**

##### 文件事件处理器的4个部分

- 多个socket
- IO多路复用程序
- 文件事件分派器
- 事件处理器(连接应答处理器、命令请求处理器、命令回复处理器)

多个socket可能会并发产生不同的操作，每个操作对应不同的文件事件，但是IO多路复用程序会监听多个socket，会将socket产生的事件放入队列中排队，事件分派器每次从队列中取出一个事件，把该事件交给对应的事件处理器进行处理。

##### 为啥 redis 单线程模型也能效率这么高？

- 纯内存操作
- 核心是基于非阻塞的 IO 多路复用机制
- 单线程反而避免了多线程的频繁上下文切换问题

#### ==redis常见数据结构以及使用场景==

##### String

> 常用命令：set，get，decr，incr，mget等
>
> String数据结构是最简单的`key-value`类型。

##### Hash

> 常用命令：hget，hset，hgetall等
>
> hash是一个string类型的field和value的映射表，特别适合用于存储对象。
>
> 比如：可以使用hash数据结构来存储用户信息，商品信息等。

##### List

> 常用命令：lpush，rpush，lpop，rpop，lrange等

​		list 就是链表，Redis list的应用场景非常多，也是Redis最重要的数据结构之一，比如微博的关注列表，粉丝列表，消息列表等功能都可以用Redis的list结构来实现。

​		Redis list的实现为一个==双向链表==，**即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销**。
另外可以**通过`lrange`命令，就是从某个元素开始读取多少个元素，可以基于list 实现分页查询**，这个很棒的一个功能，基于redis实现简单的高性能分页，可以做类似微博那种下拉不断分页的东西（一页一页的往下走），性能高。

##### Set

> 常用命令：sadd，spop，smembers，sunion等

​		set 对外提供的功能与list类似是一个列表的功能，特殊之处在于**set是可以自动排重**的。

​		当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的。可以基于set轻易实现交集、并集、差集的操作。

​		比如：在微博应用中，可以将一个用户所有的关注人存在一个集合中，将其所有粉丝存在一个集合。

*Redis可以非常方便的实现如共同关注、共同粉丝、共同喜好等功能*。这个过程也就是求交集的过程，具体命令如下：

```java
sinterstore key1 key2 key3  //将交集存在key1内
```

##### Zset

> 常用命令：`zadd`，`zrange`，`zrem`，`zcard`等

`Zset` 与 `set` 不同的是每个元素都会关联一个 double 类型的分数（score）。redis正是通过分数来为集合中的成员进行从小到大的排序。

- `score` 相同：按字典顺序排序
- 有序集合的成员是唯一的, 但分数 `(score)` 却可以重复。

举例：在直播系统中，实时排行信息包含直播间在线用户列表，各种礼物排行榜，弹幕消息（可以理解为按消息维度的消息排行榜）等信息，适合使用Redis中的Zset 结构进行存储。

###### Zset的底层实现

zset的编码有**`ziplist`**和**`skiplist`**两种。

底层分别使用**`ziplist`（压缩链表）**和**`skiplist`（跳跃表）**实现。

**什么时候使用ziplist什么时候使用skiplist？**

```markdown
当 `Zset` 满足以下两个条件的时候，使用 `ziplist`:

> 1. 保存的元素少于128个，
> 2. 保存的所有元素大小都小于64字节。

**不满足这两个条件则使用 `skiplist`。**
```

##### [Ziplist]( (https://www.jianshu.com/p/afaf78aaf615))

ziplist是list键、hash键以及zset键的底层实现之一（3.0之后list键已经不直接用ziplist和linkedlist作为底层实现了，取而代之的是quicklist）。

这些键的常规底层实现如下：

- **list键**：双向链表
- **hash键**：字典dict
- **zset键**：跳跃表skiplist

但是当 `list` 键里包含的元素较少、并且每个元素要么是小整数要么是长度较小的字符串时，redis将会用 `ziplist `作为 `list`键的底层实现。同理 `hash `和 `zset `在这种场景下也会使用`ziplist`。**原因是为了节省内存空间**。

###### 整体布局

`ziplist` 是由**一系列特殊编码的连续内存块组成的顺序存储结构**，类似于数组，`ziplist` 在**内存中是连续存储**的，但是不同于数组，为了节省内存， `ziplist `的每个元素所占的内存大小可以不同（数组中叫元素，`ziplist `叫节点 **entry**，下文都用“节点”），每个节点可以用来存储一个整数或者一个字符串。

![image-20210806163520639](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210806163520639.png)

- `zlbytes`: `ziplist` 的长度（单位: 字节)，是一个32位无符号整数。
- `zltail`: `ziplist`最后一个节点的偏移量，反向遍历 `ziplist `或者 `pop `尾部节点的时候有用。
- `zllen`: `ziplist`的节点（`entry`）个数
- `entry`: 节点
- `zlend`: 值为 `0xFF`，用于标记 `ziplist` 的结尾

`ziplist `每个节点的长度是可以不一样的，而我们面对不同长度的节点又不可能直接 `sizeof(entry)`，那么它是怎么访问下一个节点呢？

**`ziplist `将一些必要的偏移量信息记录在了每一个节点里，使之能跳到上一个节点或下一个节点。**

###### **节点的布局(entry)**

每个节点由三部分组成：`prevlength`、`encoding`、`data`

- `prevlengh`: 记录上一个节点的长度，为了方便反向遍历 `ziplist`
- `encoding`: 当前节点的编码规则，下文会详细说
- `data`: 当前节点的值，可以是数字或字符串

为了节省内存，**根据上一个节点的长度 `prevlength `可以将 `ziplist `节点分为两类：**

![image-20210806164116624](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210806164116624.png)

- `entry `的前8位小于254，则这8位就表示上一个节点的长度。
- `entry `的前8位等于254，则意味着上一个节点的长度无法用8位表示，后面32位才是真实的prevlength。**用254不用255(11111111)作为分界是因为255是zlend的值，它用于判断`ziplist`是否到达尾部。**

**根据当前节点存储的数据类型及长度，可以将 `ziplist `节点分为9类**：

- 其中整数节点分为6类：
  - ![image-20210806164259847](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210806164259847.png)
- 字符串节点分为3类：
  - ![image-20210806164328512](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210806164328512.png)

##### Redis跳跃表

> 跳跃表是一种有序的数据结构，它通过在每个节点中维持多个指向其他节点的指针，从而达到快速访问节点的目的。
>
> 只用在了两个地方。一是**实现有序集合键(`Zset`)**，二是**集群节点中用作内部数据结构**。

###### 什么是跳跃表

对于一个单链表来讲，即便链表中存储的数据是有序的，如果我们要想在其中查找某个数据，也只能从头到尾遍历链表。这样查找效率就会很低，时间复杂度会很高，是 `O(n)`。

如果我们想要提高其查找效率，可以考虑在链表上建索引的方式。

![image-20210515110827579](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210515110827579.png)

我们看出，加来一层索引之后，查找一个结点需要遍的结点个数减少了，也就是说查找效率提高了。

当有大量的数据时，我们可以增加多级索引，其查找效率可以得到明显提升。

**像这种链表加多级索引的结构，就是跳跃表！**

![image-20210515110938679](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210515110938679.png)

###### Redis跳跃表

Redis使用跳跃表作为有序集合键的底层实现之一，如果一个有序集合包含的**元素数量比较多**，又或者有序集合中的元素的**成员是比较长的字符串**时，Redis就会使用跳跃表来作为有序集合键的底层实现。

**为什么元素数量比较多或者成员是比较长的字符串的时候Redis要使用跳跃表来实现？**

> 跳跃表在链表的基础上增加了多级索引以提升查找的效率，但其是一个空间换时间的方案，必然会带来一个问题——索引是占内存的。原始链表中存储的有可能是很大的对象，而索引结点只需要存储关键值和几个指针，并不需要存储对象，因此当节点本身比较大或者元素数量比较多的时候，其优势必然会被放大，而缺点则可以忽略。

###### Redis中跳跃表的实现

Redis中的跳跃表由 **`zskiplistNode`** 和 **`skiplist`** 两个结构定义，其中**`zskiplistNode`**结构用于表示跳跃表结点，而**`skiplist`** 结构用于保存跳跃表结点的相关信息，比如节点的数量, 以及指向表头节点和表尾节点的指针等等。

![image-20210515182308533](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210515182308533.png)

**上图展示了一个跳跃表示例,其中最左边的是 `skiplist` 结构,该结构包含以下属性。**

- **`header:`** 指向跳跃表的表头节点，通过这个指针程序定位表头节点的时间复杂度就为 `O(1)`
- **`tail:`** 指向跳跃表的表尾节点, 通过这个指针程序定位表尾节点的时间复杂度就为 `O(1)`
- **`level`**: 记录目前跳跃表内, 层数最大的那个节点的层数(**表头节点的层数不计算在内**)，通过这个属性可以在 `O(1)` 的时间复杂度内获取层高最好的节点的层数。
- **`length`**: 记录跳跃表的长度,也即是,跳跃表目前包含节点的数量(**表头节点不计算在内**)，通过这个属性，程序可以在 `O(1)`的时间复杂度内返回跳跃表的长度。

**结构右方的是四个 `zskiplistNode`结构,该结构包含以下属性**

- 层**(level)**:

    ```markdown
    - 节点中用L1、L2、L3等字样标记节点的各个层,L1代表第一层,L2代表第二层,以此类推。
    
    - 每个层都带有两个属性:前进指针和跨度。前进指针用于访问位于表尾方向的其他节点, 而跨度则记录了前进指针所指向节点和当前节点的距离(跨度越大、距离越远)。在上图中, 连线上带有数字的箭头就代表前进指针,而那个数字就是跨度。当程序从表头向表尾进行遍历时,访问会沿着层的前进指针进行。
    
    - 每次创建一个新跳跃表节点的时候,程序都根据幂次定律(powerlaw,越大的数出现的概率越小)随机生成一个介于1和32之间的值作为level数组的大小,这个大小就是层的“高度”
    ```

- 后退(**backward**)指针：

    ```markdown
    - 节点中用 BW 字样标记节点的后退指针, 它指向位于当前节点的前一个节点。后退指针在程序从表尾向表头遍历时使用。与前进指针所不同的是每个节点只有一个后退指针，因此每次只能后退一个节点。
    ```

- 分值(**score**):

    ```markdown
    - 各个节点中的1.0、2.0和3.0是节点所保存的分值。在跳跃表中,节点按各自所保存的分值从小到大排列。
    ```

- 成员对象(**oj**):

    ```markdown
    - 各个节点中的o1、o2和o3是节点所保存的成员对象。在同一个跳跃表中, 各个节点保存的成员对象必须是唯一的,但是多个节点保存的分值却可以是相同的:分值相同的节点将按照成员对象在字典序中的大小来进行排序,成员对象较小的节点会排在前面(靠近表头的方向),而成员对象较大的节点则会排在后面(靠近表尾的方向)。
    ```

************

#### Redis底层数据结构

Redis针对不同类型的数据结构底层都进行了优化，会根据不同的数据采用不同的数据结构来进行存储。

##### Redis对象（RedisObject）

Redis中所有的key都是字符串，但是所有的value存储的时候实际上都不是直接采用String、List、Set、SortedSet和Hash这些结构来存储的，而是封装成了RedisObejct对象。相当于Redis就是一个庞大的 `<String, RedisObejct>` 集合。

Redis每次新建一个键值对时都会创建两个RedisObject对象，一个是键的对象，一个是值的对象。

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/822135-20201119161526603-1642044408.png)

- type: 表示value代表的数据类型，取值范围为String、List、Set、SortedSet、Hash等五种类型
- encoding：表示value的编码格式，包括int、embstr、raw、ziplist、linkedList、ht、intset、skipList等等
- refCount：表示value对象引用计数，当refCount值为0的时候则表示可以被回收了
- ptr: 指向底层数据的指针
- lru: 上一次被访问的时间

==其中不同类型的数据结构可能会对应多种不同的编码方式，通过采用不同编码方式的方法可以达到优化内存的效果。另外相同的数据类型同样也可能会有不同的编码方式来优化内存。==

##### 简单动态字符串（SDS）

```c
struct sdshdr{
     //记录buf数组中已使用字节的数量
     //等于 SDS 保存字符串的长度
     int len;
     //记录 buf 数组中未使用字节的数量
     int free;
     //字节数组，用于保存字符串
     char buf[];
}
```

![image-20210901213436983](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210901213436983.png)

![image-20210901213452773](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210901213452773.png)



##### 

#### redis设置过期时间

​		Redis中有个设置时间过期的功能，即**对存储在redis数据库中的值可以设置一个过期时间**。作为一个缓存数据库，这是非常实用的。如我们一般项目中的 token 或者一些登录信息，尤其是短信验证码都是有时间限制的，按照传统的数据库处理方式，一般都是自己判断过期，这样无疑会严重影响项目性能。
​		*我们set key的时候，都可以给一个expire time，就是过期时间，通过过期时间我们可以指定这个 key 可以存活的时间。*
如果假设你设置了一批key 只能存活1个小时，那么接下来1小时后，redis是怎么对这批key进行删除的？

**定期删除+惰性删除**

- **定期删除：**redis默认是每隔100ms就==随机抽取==一些设置了过期时间的key，检查其是否过期，如果过期就删除。注意这里是随机抽取的。**为什么要随机呢？你想一想假如redis 存了几十万个key，每隔100ms就遍历所有的设置过期时间的key的话，就会给CPU带来很大的负载！**
- **惰性删除：**定期删除可能会导致很多过期key到了时间并没有被删除掉。所以就有了惰性删除。假如你的过期key，靠定期删除没有被删除掉，还停留在内存里，除非你的系统去查一下那个key，才会被redis给删除掉。

但是仅仅通过设置过期时间还是有问题的。我们想一下：如果定期删除漏掉了很多过期key，然后你也没及时去查，也就没走惰性删除，此时会怎么样？如果大量过期key堆积在内存里，导致redis内存块耗尽了。怎么解决这个问题呢？**redis 内存淘汰机制。**

#### ==redis内存淘汰机制(数据淘汰策略)==

**redis提供6种数据淘汰策略**  [参考链接](https://www.cnblogs.com/leeego-123/p/12577136.html)

1.**`volatile-lru`：**从**已设置过期时间的数据集**（`server.db[i].expires`）中挑选**最近最少使用的数据淘汰**；

2.**`volatile-ttl`：**从**已设置过期时间的数据集**（`server.db[i].expires`）中挑选**将要过期的数据淘汰**；

3.**`volatile-random`：**从**已设置过期时间的数据集**（`server.db[i].expires`）中**任意选择数据淘汰**；

4.**`allkeys-lru`：**当**内存不足以容纳新写入数据时**，在键空间中，移除**最近最少使用的key（这个是最常用的）**；

5.**`allkeys-random`：**从数据集（`server.db[i].dict`）中**任意选择数据淘汰**；

6**`no-eviction`：**禁止驱逐数据，也就是说当**内存不足以容纳新写入数据时，新写入操作会报错**。这个应该没人使用吧！

4.0版本后增加以下两种：

7.**`volatile-lfu`**：从**已设置过期时间的数据集**（`server.db[i].expires`）中挑选**最不经常使用的数据淘汰**；

8.**`allkeys-lfu`**：当**内存不足以容纳新写入数据时**，在键空间中，**移除最不经常使用的key**。

#### ==redis持久化机制（怎么保证redis挂掉之后再重启数据可以进行恢复）==

​		`redis` 为了内部数据的安全考虑，会把本身的数据以 **文件** 的形式保存到硬盘中一份，在服务器重启之后会自动把硬盘的数据恢复到内存（`redis`）里边。

> 数据保存到硬盘的过程就被称为”持久化“效果。

​		`Redis` 不同于 `Memcached` 的很重要一点就是，**Redis支持持久化，而且支持两种不同的持久化操作。**Redis的一种持久化方式叫 **快照（snapshotting，`RDB`）**，另一种方式是 **只追加文件（append-only file，`AOF`）**。

##### 快照持久化（RDB）

> `Redis` 将某一时刻的快照（备份的数据库数据）保存成一种称为 `RDB` 格式的文件中，这种格式是经过压缩的二进制文件。`Redis` 保存和恢复文件，如图 1 和图 2 所示。
>
> ![img](https://img2018.cnblogs.com/blog/999593/201905/999593-20190509112639580-1693985096.png)

​		`Redis` 可以通过创建快照来获得存储在内存里面的数据在某个时间点上的副本。`Redis` 创建快照之后，可以对快照进行备份，可以将快照复制到其他服务器从而创建具有相同数据的服务器副本（`Redis` 主从结构，主要用来提高 `Redis` 性能），还可以将快照留在原地以便重启服务器的时候使用。保存的快照的名字默认是 `dump.rdb`

​	快照持久化是 `Redis` 默认采用的持久化方式，在 `redis.conf` 配置文件中默认有以下配置：

```bash
# 多久持久化一次
save 900 1 #在900秒（15分钟）之后，如果至少有1个key发生变化，Redis就会自动触发BGSAVE命令创建快照。
save 300 10 #在300秒（5分钟）之后，如果至少有10个key发生变化，Redis就会自动触发BGSAVE命令创建快照。
save 60 10000 #在60秒（1分钟）之后，如果至少有10000个key发生变化，Redis就会自动触发BGSAVE命令创建快照。
```

**`RDB` 其他知识**

```markdown
**
RDB快照持久化数据的优缺点：
　　　1、优点：
　　　　　　（1）采用子线程创建RDB文件，不会对redis服务器性能造成大的影响；
　　　　　　（2）快照生成的RDB文件是一种压缩的二进制文件，可以方便的在网络中传输和保存。通过RDB文件，可以方便的将redis数据恢复到某一历史时刻，可以提高数据安全性，避免宕机等意外对数据的影响。
　　　　　　（3）适合大规模的数据恢复。
　　　　　　（4）如果业务对数据完整性和一致性要求不高，RDB是很好的选择。
　　　2、缺点：
　　　　　　（1）redis文件在时间点A生成，之后产生了新数据，还未到达另一次生成RDB文件的条件，redis服务器崩溃了，那么在时间点A之后的数据会丢失掉，数据一致性不够好，如果可以接受这部分丢失的数据，可以用生成RDB的方式；
　　　　　　（2）快照持久化方法通过调用fork()方法创建子线程。当redis内存的数据量比较大时，创建子线程和生成RDB文件会占用大量的系统资源和处理时间，对 redis处理正常的客户端请求造成较大影响。
　　　　　　（3）数据的完整性和一致性不高，因为RDB可能在最后一次备份时宕机了。
　　　　　　（4）备份时占用内存，因为Redis 在备份时会独立创建一个子进程，将数据写入到一个临时文件（此时内存中的数据是原来的两倍哦），最后再将临时文件替换之前的备份文件。所以Redis 的持久化和数据的恢复要选择在夜深人静的时候执行是比较合理的。
触发RDB快照：
　　　　1、在指定的时间间隔内，执行指定次数的写操作
　　　　2、执行save（阻塞， 只管保存快照，其他的等待） 或者是bgsave （异步）命令
　　　　3、执行flushall 命令，清空数据库所有数据，意义不大
　　　　4、执行shutdown 命令，保证服务器正常关闭且不丢失任何数据，意义...也不大。
通过RDB文件恢复数据：
　　　　将 dump.rdb 文件拷贝到 redis 的安装目录的bin目录下，重启redis服务即可。在实际开发中，一般会考虑到物理机硬盘损坏情况，选择备份dump.rdb 。
```

##### 只追加文件持久化（AOF）

>  `AOF` 是 `redis` 对将所有的写命令保存到一个 `aof` 文件中，根据这些写命令，实现数据的持久化和数据恢复。
>
> ![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/999593-20190509115345285-799871304.png)

与快照持久化相比，`A0F` 持久化的实时性更好，因此已成为主流的持久化方案。默认情况下 Redis 没有开启`AOF(append only file)`方式的持久化，可以通过 `append only` 参数开启：

```bash
appendonly yes
```

​		开启 `AOF` 持久化后每执行一条会更改 `Redis` 中的数据的命令，`Redis` 就会将该命令写入硬盘中的 `AOF` 文件。`AOF` 文件的保存位置和 `RDB` 文件的位置相同，都是通过 `dir` 参数设置的，默认的文件名是`appendonly.aof`。

​		在Redis的配置文件中存在三种不同的 `AOF` 持久化方式，它们分别是：

```bash
appendfsync always #每次有数据修改发生时都会写入AOF文件，这样会严重降低Redis的速度
appendfsync everysec #海秒钟同步一次，显示地将多个写命令同步到硬盘
appendsync no #让操作系统觉得何时进行同步
```

为了兼顾数据和写入性能，用户可以考虑**`appendsync everysec`选项**。

###### `AOF` 重写

1. `Redis` 不断的将写命令保存到 `AOF` 文件中，导致 `AOF` 文件越来越大，当 `AOF` 文件体积过大时，数据恢复的时间也是非常长的，因此，`Redis` 提供了重写或者说压缩 `AOF` 文件的功能。比如对 `key1` 初始值是`0`，调用 `incr` 命令 `100` 次，`key1` 的值变为 `100` ，那么其实直接一句 `set key1 100` 就可以代替之前的 `100`次调用，`AOF` 重写功能就是干这个事情的。重写时，可以调用 `BGREWRITEAOF` 命令重写 `AOF`文件，与新建子线程 `bgsave`命令的工作原理相似。也可以通过配置文件配置什么条件下对 `AOF` 文件重写。
2. 重写的原理：`Redis` 会 `fork` 出一条新进程，读取内存中的数据，并重新写到一个临时文件中。并没有读取旧文件（太大了）。最后替换旧的 `aof` 文件。
3. 重写触发机制：当 `AOF` 文件大小是上次重写后大小的一倍且文件大于 `64M`时触发。这里的“一倍”和“64M” 可以通过配置文件修改。

###### `AOF` 文件生成机制

>  生成过程包括三个步骤：命令追加、文件写入、文件同步。
>
> - `Redis` 打开 `AOF` 持久化功能之后，`Redis` 在执行完一个写命令后，把执行的命令首先追加到 `Redis` 内部的 `aof_buf` 缓冲区末尾，此时缓冲区的记录还没有写到 `appendonly.aof`文件中。
> - 然后，缓冲区的写命令会被写入到  `AOF` 文件，这一过程是文件写入过程。对于操作系统来说，调用`write函数`并不会立刻将数据写入到硬盘，为了将数据真正写入硬盘，还需要调用 `fsync函数`，调用`fsync函数` 即是文件同步的过程，只有经过了文件的同步过程，写命令才真正的被保存到了 `AOF`文件中。`appendfsync` 就是配置同步的频率的选项。

**`AOF` 其他知识**

```markdown
AOF ：
	1 Redis 默认不开启。它的出现是为了弥补RDB的不足（数据的不一致性），所以它采用日志的形式来记录每个写操作，并追加到文件中。　　　 
	2 Redis 重启的会根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。

AOF优缺点：　　
	优点：　　　　
	1、提供了多种同步命令的方式，默认1秒同步一次写命令，做多会丢失1秒内的数据；　　　　
	2、如果AOF文件有错误，比如在写AOF文件时redis崩溃了，redis提供了多种恢复AOF文件的方式，　　　　　　例如使用redis-check-aof工具修正AOF文件（一般都是最后一条写命令有问题，可以手动取出最后一条写命令）；　　　　
	3、AOF文件可读性交强，也可手动操作写命令。　　　　
	4、数据的完整性和一致性更高　　　　
	缺点：　　　　
	1、AOF文件比RDB文件较大；　　　　
	2、redis负载较高时，RDB文件比AOF文件具有更好的性能；　　　　
	3、RDB使用快照的方式持久化整个redis数据，而aof只是追加写命令，因此从理论上来说，RDB比AOF方式更加健壮，另外，官方文档也指出，在某些情况下，AOF的确也存在一些bug，　　　　　　比如使用阻塞命令时，这些bug的场景RDB是不存在的。　　　　
	4、因为AOF记录的内容多，文件会越来越大，数据恢复也会越来越慢。触发AOF快照：　　根据配置文件触发，可以是每次执行触发，可以是每秒触发，可以不同步。根据AOF文件恢复数据：　　正常情况下，将appendonly.aof 文件拷贝到redis的安装目录的bin目录下，重启redis服务即可。但在实际开发中，可能因为某些原因导致appendonly.aof 文件格式异常，　　从而导致数据还原失败，可以通过命令redis-check-aof --fix appendonly.aof 进行修复 。
```

##### Redis4.0对持久化机制的优化

> `Redis 4.0` 开始支持 `RDB` 和 `AOF` 的混合持久化(默认关闭，可以通过配置`aof-use-rdb-preamble`开启)。
>
> 如果打开混合持久化，`AOF` 重写的时候就直接把 `RDB` 的内容写到 `AOF` 文件开头。
>
> - 好处：可以结合 `RDB` 和 `AOF` 的优点，快速加载同时避免丢失过多的数据。
> - 缺点：`AOF` 里面的 `RDB` 部分是压缩格式不再是 `AOF` 格式，可读性较差。

#### redis事务

> 事务提供了一种将多个命令请求打包，然后一次性、按顺序地执行多个命令的机制，并且在事务执行期间，服务器不会中断事务而改去执行其他客户端的命令请求，它会将事务中的所有命令都执行完毕，然后才去处理其他客户端的命令请求。

> Redis 通过MULTI、EXEC、WATCH等命令来实现事务（transaction）功能。

##### ACID性质

> ​		ACID，是指在数据库管理系统（DBMS）中事务所具有的四个特性：原子性（Atomicity）、一致性（Consistency）、隔离性（Isolation，又称独立性）、持久性（Durability）。
>
> ​		在数据库系统中，一个事务是指由一系列数据库操作组成的一个完整的逻辑过程。例如银行转帐，从原账户扣除金额，以及向目标账户添加金额，这两个数据库操作的总和构成一个完整的逻辑过程，不可拆分。这个过程被称为一个事务，具有ACID特性。

###### 原子性

> 整个事务中的所有操作，要么全部完成，要么全部不完成，不可能停滞在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
>
> 比如说，一个事务开始更新100行记录，但是在更新了20行之后（因为某种原因）失败了，那么此时数据库会回滚（撤销）对那20条记录的修改

###### 一致性

> ​		在事务开始之前和事务结束以后，数据库的完整性约束没有被破坏。
>
> ​		事务将数据库从一个一致性状态带入另一个一致性状态。 比如说，在一个银行事务（在描述关系数据库事务的特性时，基本上都是用银行事务来作为描述对象的）中，需要从存储账户扣除款项，然后在支付账户中增加款项。 如果在这个中转的过程发生了失败，那么绝对不能让数据库只执行其中一个账户的操作，因为这样会导致数据处于不一致的状态（这样的话，银行的账目上，借贷就不平衡了）。
>
> ​		如果数据库系统运行中发生故障，有些事物尚未完成就被迫中断了，系统就将此事务中对数据库的所有已经完成的操作全部撤销，滚回到事务开始时的一致状态。

###### 隔离性

> ​		两个事务的执行是互不干扰的，一个事务不可能看到其他事务运行时中间某一时刻的数据。
>
> ​		这个特性是说，直到事务结束时（commit/rollback），其他事务（或者会话）对此事务所操作的数据都不可见（但并不是说其他会话的读取会被阻塞）。 比如说，一个用户正在修改hr.employees表，但是没有提交，那么其他用户在这个修改没有提交之前是看不到这个修改的。

###### 持久性

> ​		在事务完成以后，该事务所对数据库所作的更改便持久的保存在数据库之中，并不会被回滚。
>
> ​		指的是只要事务成功结束，它对数据库所做的更新就必须永久保存下来。即使发生系统崩溃，重新启动数据库系统后，数据库还能恢复到事务成功结束时的状态。
>
> ​		被提交的更改会永久地保存到数据库中（并不是说以后就不可以修改）。 事务提交之后，数据库必须通过“恢复机制”来确保事务更改的数据不会丢失。

**redis具有一致性、隔离性，并且当 `redis` 运行在某种特定的持久化模式下时，事务也具有持久性**

> ***不保证原子性：redis中的一个事务中如果存在命令执行失败，那么其他命令依然会被执行，没有回滚机制。***

#### ==缓存雪崩、缓存穿透、缓存穿透问题解决方案==

##### 缓存雪崩

> 缓存同一时间大面积的失效(比如大量数据到达过期时间)，后面的请求都会落到数据库上，造成数据库短时间内承受大量请求而崩掉。

###### 解决方案

- ### 提高缓存可用性

  - 集群部署：通过集群来提升缓存的可用性，可以利用Redis本身的Redis Cluster或者第三方集群方案如Codis等。
  - 多级缓存：设置多级缓存，第一级缓存失效的基础上，访问二级缓存，每一级缓存的失效时间都不同。

  ### 过期时间

  - 均匀过期：为了避免大量的缓存在同一时间过期，可以把不同的 key 过期时间随机生成，避免过期时间太过集中。
  - 热点数据永不过期。

  ### 熔断降级

  - 服务熔断：当缓存服务器宕机或超时响应时，为了防止整个系统出现雪崩，暂时停止业务服务访问缓存系统。
  - 服务降级：当出现大量缓存失效，而且处在高并发高负荷的情况下，在业务系统内部暂时舍弃对一些非核心的接口和数据的请求，而直接返回一个提前准备好的 fallback（退路）错误处理信息。

![image-20210407095812629](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210407095812629.png)

##### 缓存击穿

>缓存在某个时间点过期的时候，恰好在这个时间点对这个 `Key` 有大量的并发请求过来，这些请求发现缓存中 `key` 已经过期以后，会从后端数据库中加载数据并回设到缓存，这个时候大量并发的请求可能会瞬间把后端数据库压垮。

###### 解决方法

- **使用互斥锁**：查询缓存，发现缓存中不存在，加锁，让其它线程等待，只让一个线程去进行加载数据库的操作并且设置缓存。
- **设置不过期：**一般产生缓存击穿的 `key` 都是热门 `key`。
  - 设置不过期的话就没有过期时间，这样就保证了，不会出现热点 `key` 过期问题，也就是“物理”不过期。
  - 异步更新：把过期时间存在 `key` 对应的 `value `里，如果发现要过期了，通过一个后台的异步线程进行缓存的构建，也就是“逻辑”不过期。



##### 缓存穿透

> 查询缓存和数据库中都不存在的数据，这样每次请求直接打到数据库，就好像缓存不存在一样。

`正常缓存处理流程`

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210407095959413.png" alt="image-20210407095959413" style="zoom:50%;" />

`缓存穿透情况处理流程`

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210407100041672.png" alt="image-20210407100041672" style="zoom:50%;" />

###### 解决方法

​		基本的就是首先做好参数校验，一些不合法的参数请求直接抛出异常信息返回给客户端。比如查询的数据库 `id` 不能小于0、传入的邮箱格式不对的时候直接返回错误消息给客户端等等。

- **缓存无效key：**如果缓存和数据库都查不到某个 `key` 的数据就写一个到 `redis` 中去并设置过期时间，具体命令如下：`SET key value EX 10086`。这种方式可以**解决请求的key 变化不频繁**的情况，如果黑客恶意攻击，每次构建不同的请求key，会导致 `redis` 中缓存大量无效的 `key`。很明显，这种方案并不能从根本上解决此问题。如果非要用这种方式来解决穿透问题的话，尽量**将无效的key的过期时间设置短一点比如1分钟**。设计key：**`表名：列名：主键名：主键值`**。

- **布隆过滤器：**布隆过滤器是一个非常神奇的数据结构，通过它我们可以非常方便地判断一个给定数据是否存在与海量数据中。我们需要的就是判断 `key` 是否合法，有没有感觉布隆过滤器就是我们想要找的那个“人”。具体是这样做的：**把所有可能存在的请求的值都存放在布隆过滤器中，当用户请求过来，我会先判断用户发来的请求的值是否存在于布隆过滤器中。不存在的话，直接返回请求参数错误信息给客户端，存在的话才会走下面的流程。**

  <img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210407100433189.png" alt="image-20210407100433189" style="zoom:50%;" />

###### 布隆过滤器

可以理解为是一个数组和一堆哈希函数的结合体。文邹邹一点的来说就是**`一个很长的二进制向量和一系列随机映射函数，用来检索一个元素是否存在于集合中.`**

**布隆算法**：

​		二进制向量，其实就是只能存储 `0` 和 `1` 的一个数组，每个位置的初始值是0。随机映射函数，用来计算元素得到一个哈希值，然后在数组中这个哈希值对应下标位置的值改为 `1` 。比如定义一个长度为 `10` 的二进制向量：

![布隆过滤器原理解析1](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/4b208a725e37a4430a1831ee3a98b0d61603765824857.png)

用三种映射函数计算字符"周"的哈希值，假如分别得到2、5、8，那么就把二进制向量（就是数组）对应下标位置的值都置为1

![布隆过滤器原理解析2](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/e58235570f4b50404d7f0402ba6429e71603765824857.png)

再搞一个字符"华"来试一下，假如用三种映射函数计算哈希值分别得到2、5、6，那么处理后数组就变成了这样：

![布隆过滤器原理解析3](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/53b236705fe95a68b150577bc01793031603765824857.png)

如果我需要判断字符"煌"是否存在，首先使用同样的3个映射函数分别计算得到哈希值，比如是3、4、5，然后判断数组中对应下标位置的值。**只要有一个为0，那么说明这个字符一定不存在，但是如果全是1呢，只能说可能存在。**

**==为什么是可能？==**

```markdown
**
1. 因为布隆过滤器具有一定的错误率.
2. 那么为什么会有错误率呢? 简单来说,就是哈希碰撞.
`就是两个不同的数据计算出来的哈希值可能是相同的,那比如,我们存储了id为1的商品数据,计算出来的哈希值为5, 这时候请求过来的数据id为105 计算出来的哈希值也是5,那我们只能说105可能存在而不是肯定存在.但是如果是0,那么肯定不存在.哈希碰撞是肯定不会存在.
`
3. 如何降低错误率
	3.1 加大数组长度,尽量避免出现在相同的位置. 
	3.2. 使用适当多的哈希函数来计算多个哈希值
```

$$
k为哈希函数个数，m为布隆过滤器长度，n为插入的元素个数，p为误报率。\\
如何选择适合业务的 k 和 m 值呢\\
m = -\frac{n lnp}{(ln2)^2}\\
k = \frac{m}{n}ln2
$$



> **把数据库所有数据存在布隆过滤器会不会占用特别多的内存而导致查询速度变慢呢?**

不会的,因为原理中我们提到了,它是利用数组下标的方式判断数据是否存在的,而不是将数据实体都存储到过滤器中.所以内存的占用是非常小的.

> 优点：不需要存储key，节省空间。
>
> 缺点：
>
> 1. 有一定的错误率：算法判断 `key` 在集合中时，有一定的概率 `key` 其实不在集合中
>
> 2. 无法删除
>
>    ```markdown
>    **
>    删除数据存在以下两种困境:
>    1. 由于有误判的可能,并不确定数据是否存在数据库里,例如数据包3。
>    2. 当你删除某一个数据包对应位图上的标志后,可能影响其他的数据包,例如上面例子中,如果删除数据包1,也就意味着会将bitmap1,3,6位设置为0,此时数据包2来请求时,会显示不存在,因为3,6两位已经被设置为0。
>    **
>    ```

**布隆过滤器增强版**

```markdown
**
为了解决上面布隆过滤器的问题,出现了一个增强版的布隆过滤器(Counting Bloom Filter),这个过滤器的思路是将布隆过滤器的bitmap更换成数组,当数组某位置被映射一次时就+1,当删除时就-1,这样就避免了普通布隆过滤器删除数据后需要重新计算其余数据包Hash的问题,但是依旧没法避免误判。
**
```

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/2002319-20210127224637851-1288038414.png)

###### 布谷鸟过滤器

- **布谷鸟哈希**

  - 最简单的布谷鸟哈希结构是一维数组结构，会有两个 `hash` 算法将新来的元素映射到数组的两个位置。如果两个位置中有一个位置为空，那么就可以将元素直接放进去。但是如果这两个位置都满了，它就不得不「鸠占鹊巢」，随机踢走一个，然后自己霸占了这个位置。

  - ```java
    p1 = hash1(x) % l
    p2 = hash2(x) % l
    复制代码
    ```

  - 不过布谷鸟哈希算法会帮这些受害者（被挤走的蛋）寻找其它的窝。因为每一个元素都可以放在两个位置，只要任意一个有空位置，就可以塞进去。所以这个伤心的被挤走的蛋会看看自己的另一个位置有没有空，如果空了，自己挪过去也就皆大欢喜了。但是如果这个位置也被别人占了呢？好，那么它会再来一次「鸠占鹊巢」，将受害者的角色转嫁给别人。然后这个新的受害者还会重复这个过程直到所有的蛋都找到了自己的巢为止。

- **布谷鸟哈希的问题**

  - ```markdown
    **
    1. 如果数组太拥挤了，连续踢来踢去几百次还没有停下来，这时候会严重影响插入效率。这时候布谷鸟哈希会设置一个阈值，当连续占巢行为超出了某个阈值，就认为这个数组已经几乎满了。这时候就需要对它进行扩容，重新放置所有元素。
    2. 挤兑循环：比如两个不同的元素，hash 之后的两个位置正好相同，这时候它们一人一个位置没有问题。但是这时候来了第三个元素，它 hash 之后的位置也和它们一样，很明显，这时候会出现挤兑的循环。不过让三个不同的元素经过两次 hash 后位置还一样，这样的概率并不是很高，除非你的 hash 算法太挫了。
     > 布谷鸟哈希算法对待这种挤兑循环的态度就是认为数组太拥挤了，需要扩容（实际上并不是这样）。
    **
    ```

- **优化布谷鸟哈希**

  - ```markdown
    **
    1. 方案之一是增加 hash 函数，让每个元素不止有两个巢，而是三个巢、四个巢。这样可以大大降低碰撞的概率，将空间利用率提高到 95% 左右。
    2. 方案二是在数组的每个位置上挂上多个座位，这样即使两个元素被 hash 在了同一个位置，也不必立即「鸠占鹊巢」，因为这里有多个座位，你可以随意坐一个。除非这多个座位都被占了，才需要进行挤兑。很明显这也会显著降低挤兑次数。这种方案的空间利用率只有 85% 左右，但是查询效率会很高，同一个位置上的多个座位在内存空间上是连续的，可以有效利用 CPU 高速缓存。
    > 所以更加高效的方案是将上面的两个改良方案融合起来，比如使用 4 个 hash 函数，每个位置上放 2 个座位。这样既可以得到时间效率，又可以得到空间效率。这样的组合甚至可以将空间利用率提到高 99%，这是非常了不起的空间效率。
    **
    ```

- **布隆过滤器**

  - 布谷鸟过滤器和布谷鸟哈希结构一样，它也是一维数组，但是不同于布谷鸟哈希的是，布谷鸟哈希会存储整个元素，而布谷鸟过滤器中只会存储元素的指纹信息（几个bit，类似于布隆过滤器）。这里过滤器牺牲了数据的精确性换取了空间效率。正是因为存储的是元素的指纹信息，所以会存在误判率，这点和布隆过滤器如出一辙。

  - 首先布谷鸟过滤器还是只会选用两个 hash 函数，但是每个位置可以放置多个座位。这两个 hash 函数选择的比较特殊，因为过滤器中只能存储指纹信息。当这个位置上的指纹被挤兑之后，它需要计算出另一个对偶位置。而计算这个对偶位置是需要元素本身的，我们来回忆一下前面的哈希位置计算公式。

  - ```java
    fp = fingerprint(x)
    p1 = hash1(x) % l
    p2 = hash2(x) % l
        
    // 我们知道了 p1 和 x 的指纹，是没办法直接计算出 p2 的。
    ```

  - ```markdown
    # 特殊的 hash 函数
    **
    1. 布谷鸟过滤器巧妙的地方就在于设计了一个独特的 hash 函数，使得可以根据 p1 和 元素指纹 直接计算出 p2，而不需要完整的 x 元素。
    fp = fingerprint(x)
    p1 = hash(x)
    p2 = p1 ^ hash(fp)  // 异或
    2. 所以我们根本不需要知道当前的位置是 p1 还是 p2，只需要将当前的位置和 hash(fp) 进行异或计算就可以得到对偶位置。而且只需要确保 hash(fp) != 0 就可以确保 p1 != p2，如此就不会出现自己踢自己导致死循环的问题。
    > 也许你会问为什么这里的 hash 函数不需要对数组的长度取模呢? 实际上是需要的，但是布谷鸟过滤器强制数组的长度必须是 2 的指数，所以对数组的长度取模等价于取 hash 值的最后 n 位。在进行异或运算时，忽略掉低 n 位之外的其它位就行。将计算出来的位置 p 保留低 n 位就是最终的对偶位置。
    **
    ```


**********************

#### 如何解决Redis的并发竞争Key问题(Redis分布式锁)

> 多个系统同时对一个key进行操作，但是最后执行的顺序和我们期望的顺序不一样，这样就导致了结果的不同。

##### 解决方法：分布式锁

分布式锁一般有三种实现方式：1. 数据库乐观锁；2. 基于Redis的分布式锁；3. 基于ZooKeeper的分布式锁。

首先，为了确保分布式锁可用，我们至少要确保锁的实现同时满足以下四个条件：

```markdown
1. 互斥性。在任意时刻，只有一个客户端能持有锁。
2. 不会发生死锁。即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。
3. 具有容错性。只要大部分的Redis节点正常运行，客户端就可以加锁和解锁。
4. 解铃还须系铃人。加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了。
```

##### 单节点的Redis实现分布式锁

- **加锁：**

  - 加锁实际上就是在 `redis` 中，给 `key` 键设置一个值，为避免死锁，并给定一个过期时间。

  - ```markdown
    > SET lock_key random_value NX PX 5000
    
    1. random_value 是客户端生成的唯一的字符串。
    2. NX 代表只在键不存在时，才对键进行设置操作。
    3. PX 5000 设置键的过期时间为5000毫秒。
    ```

- **解锁：**

  - 解锁的过程就是将 `Key` 键删除。但也不能乱删，不能说客户端1的请求将客户端2的锁给删除掉。这时候`random_value`的作用就体现出来。

  - ```markdown
    > 先判断当前锁的字符串是否与传入的值相等，是的话就删除Key，解锁成功。
    if redis.call('get',KEYS[1]) == ARGV[1] then 
       return redis.call('del',KEYS[1]) 
    else
       return 0 
    end
    ```

- **实现：**

  - **加锁**的过程很简单，就是通过 `SET` 指令来设置值，成功则返回；否则就循环等待，在 `timeout` 时间内仍未获取到锁，则获取失败

  - ```java
    public class RedisTool {
    
        private static final String LOCK_SUCCESS = "OK";
        private static final String SET_IF_NOT_EXIST = "NX";
        private static final String SET_WITH_EXPIRE_TIME = "PX";
    
        /**
         * 尝试获取分布式锁
         * @param jedis Redis客户端
         * @param lockKey 锁
         * @param requestId 请求标识
         * @param expireTime 超期时间
         * @return 是否获取成功
         */
        public static boolean tryGetDistributedLock(Jedis jedis, String lockKey, String requestId, int expireTime) {
    
            String result = jedis.set(lockKey, requestId, SET_IF_NOT_EXIST, SET_WITH_EXPIRE_TIME, expireTime);
    
            if (LOCK_SUCCESS.equals(result)) {
                return true;
            }
            return false;
        }
    }
    ```

  - ```markdown
    **
    可以看到，我们加锁就一行代码：
    jedis.set(String key, String value, String nxxx, String expx, int time)
    这个set()方法一共有五个形参：
    1. key，我们使用key来当锁，因为key是唯一的。
    2. value，我们传的是requestId，很多童鞋可能不明白，有key作为锁不就够了吗，为什么还要用到value？原因就是我们在上面讲到可靠性时，分布式锁要满足第四个条件解铃还须系铃人，通过给value赋值为requestId，我们就知道这把锁是哪个请求加的了，在解锁的时候就可以有依据。requestId可以使用UUID.randomUUID().toString()方法生成。
    3. nxxx，这个参数我们填的是NX，意思是SET IF NOT EXIST，即当key不存在时，我们进行set操作；若key已经存在，则不做任何操作；
    4. expx，这个参数我们传的是PX，意思是我们要给这个key加一个过期的设置，具体时间由第五个参数决定。
    5. time，与第四个参数相呼应，代表key的过期时间。
    **
    **
    总的来说，执行上面的set()方法就只会导致两种结果：
    1. 当前没有锁(key不存在)，那么就进行加锁操作，并对锁设置一个有效期，同时value表示加锁的客户端。
    2. 已有锁存在，不做任何操作。
    **
    ```

  - **解锁**

    - ```java
      public class RedisTool {
      
          private static final Long RELEASE_SUCCESS = 1L;
      
          /**
           * 释放分布式锁
           * @param jedis Redis客户端
           * @param lockKey 锁
           * @param requestId 请求标识
           * @return 是否释放成功
           */
          public static boolean releaseDistributedLock(Jedis jedis, String lockKey, String requestId) {
      
              String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end"; // Lua脚本代码: 首先获取锁对应的value值，检查是否与requestId相等，如果相等则删除锁（解锁）
              Object result = jedis.eval(script, Collections.singletonList(lockKey), Collections.singletonList(requestId)); // 将Lua代码传到jedis.eval()方法里, 交给Redis服务端执行
              if (RELEASE_SUCCESS.equals(result)) {
                  return true;
              }
              return false;
          }
      }		
      ```

> 存在问题：锁不具有可重用性。	



​	基于 zookeeper 临时有序节点可以实现的分布式锁。大致思想为：**每个客户端对某个方法加锁时，在zookeeper上的与该方法对应的指定节点的目录下，生成一个唯一的瞬时有序节点。判断是否获取锁的方式很简单，只需要判断有序节点中序号最小的一个。当释放锁的时候，只需将这个瞬时节点删除即可。同时，其可以避免服务宕机导致的锁无法释放，而产生的死锁问题。完成业务流程后，删除对应的子节点释放锁。**
​		在实践中，当然是从以可靠性为主。所以**首推Zookeeper**。

##### `Redisson`中的可重入锁

- 配置获取 `RedissonClient` 客户端的实例，然后`getLock`获取锁的实例，进行操作即可。

  - ```java
    public static void main(String[] args) {
    
        Config config = new Config();
        config.useSingleServer().setAddress("redis://127.0.0.1:6379");
        config.useSingleServer().setPassword("redis1234");
        
        final RedissonClient client = Redisson.create(config);  
        RLock lock = client.getLock("lock1");
        
        try{
            lock.lock();
        }finally{
            lock.unlock();
        }
    }
    ```

- 获取锁实例 。上面代码中 `RLock lock = client.getLock("lock1");` 这句代码就是为了获取锁的实例，然后我们可以看到它返回的是一个`RedissonLock`对象。

  - ```java
    public RLock getLock(String name) {
        return new RedissonLock(connectionManager.getCommandExecutor(), name);
    }
    ```

  - 在`RedissonLock`构造方法中，主要初始化一些属性。

  - ```java
    public RedissonLock(CommandAsyncExecutor commandExecutor, String name) {
        super(commandExecutor, name);
        //命令执行器
        this.commandExecutor = commandExecutor;
        //UUID字符串
        this.id = commandExecutor.getConnectionManager().getId();
        //内部锁过期时间
        this.internalLockLeaseTime = commandExecutor.
                    getConnectionManager().getCfg().getLockWatchdogTimeout();
        this.entryName = id + ":" + name;
    }
    ```

- 加锁

  - 当我们调用`lock`方法，定位到`lockInterruptibly`。在这里，完成了加锁的逻辑。

  - ```java
    public void lockInterruptibly(long leaseTime, TimeUnit unit) throws InterruptedException {
        
        //当前线程ID
        long threadId = Thread.currentThread().getId();
        //尝试获取锁
        Long ttl = tryAcquire(leaseTime, unit, threadId);
        // 如果ttl为空，则证明获取锁成功
        if (ttl == null) {
            return;
        }
        //如果获取锁失败，则订阅到对应这个锁的channel
        RFuture<RedissonLockEntry> future = subscribe(threadId);
        commandExecutor.syncSubscription(future);
    
        try {
            while (true) {
                //再次尝试获取锁
                ttl = tryAcquire(leaseTime, unit, threadId);
                //ttl为空，说明成功获取锁，返回
                if (ttl == null) {
                    break;
                }
                //ttl大于0 则等待ttl时间后继续尝试获取
                if (ttl >= 0) {
                    getEntry(threadId).getLatch().tryAcquire(ttl, TimeUnit.MILLISECONDS);
                } else {
                    getEntry(threadId).getLatch().acquire();
                }
            }
        } finally {
            //取消对channel的订阅
            unsubscribe(future, threadId);
        }
        //get(lockAsync(leaseTime, unit));
    }
    ```

  - 如上代码，就是加锁的全过程。先调用`tryAcquire`来获取锁，如果返回值ttl为空，则证明加锁成功，返回；如果不为空，则证明加锁失败。这时候，它会订阅这个锁的Channel，等待锁释放的消息，然后重新尝试获取锁。流程如下：

  - <img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/13230160-6c08ae68fe9a796f.png" style="zoom:60%">

  - **获取锁**

  - 获取锁的过程是怎样的呢？接下来就要看`tryAcquire`方法。在这里，它有两种处理方式，一种是带有过期时间的锁，一种是不带过期时间的锁。

  - ```java
    private <T> RFuture<Long> tryAcquireAsync(long leaseTime, TimeUnit unit, final long threadId) {
    
        //如果带有过期时间，则按照普通方式获取锁
        if (leaseTime != -1) {
            return tryLockInnerAsync(leaseTime, unit, threadId, RedisCommands.EVAL_LONG);
        }
        
        //先按照30秒的过期时间来执行获取锁的方法
        RFuture<Long> ttlRemainingFuture = tryLockInnerAsync(
            commandExecutor.getConnectionManager().getCfg().getLockWatchdogTimeout(),
            TimeUnit.MILLISECONDS, threadId, RedisCommands.EVAL_LONG);
            
        //如果还持有这个锁，则开启定时任务不断刷新该锁的过期时间
        ttlRemainingFuture.addListener(new FutureListener<Long>() {
            @Override
            public void operationComplete(Future<Long> future) throws Exception {
                if (!future.isSuccess()) {
                    return;
                }
    
                Long ttlRemaining = future.getNow();
                // lock acquired
                if (ttlRemaining == null) {
                    scheduleExpirationRenewal(threadId);
                }
            }
        });
        return ttlRemainingFuture;
    }
    ```

  - 接着往下看，`tryLockInnerAsync`方法是真正执行获取锁的逻辑，它是一段LUA脚本代码。在这里，它使用的是hash数据结构。

  - ```java
    <T> RFuture<T> tryLockInnerAsync(long leaseTime, TimeUnit unit,     
                                long threadId, RedisStrictCommand<T> command) {
    
            //过期时间
            internalLockLeaseTime = unit.toMillis(leaseTime);
    
            return commandExecutor.evalWriteAsync(getName(), LongCodec.INSTANCE, command,
                      //如果锁不存在，则通过hset设置它的值，并设置过期时间
                      "if (redis.call('exists', KEYS[1]) == 0) then " +
                          "redis.call('hset', KEYS[1], ARGV[2], 1); " +
                          "redis.call('pexpire', KEYS[1], ARGV[1]); " +
                          "return nil; " +
                      "end; " +
                      //如果锁已存在，并且锁的是当前线程，则通过hincrby给数值递增1
                      "if (redis.call('hexists', KEYS[1], ARGV[2]) == 1) then " +
                          "redis.call('hincrby', KEYS[1], ARGV[2], 1); " +
                          "redis.call('pexpire', KEYS[1], ARGV[1]); " +
                          "return nil; " +
                      "end; " +
                      //如果锁已存在，但并非本线程，则返回过期时间ttl
                      "return redis.call('pttl', KEYS[1]);",
            Collections.<Object>singletonList(getName()), 
                    internalLockLeaseTime, getLockName(threadId));
        }
    ```

  - 这段LUA代码看起来并不复杂，有三个判断：

    - **通过exists判断，如果锁不存在，则设置值和过期时间，加锁成功**
    - **通过hexists判断，如果锁已存在，并且锁的是当前线程，则证明是重入锁，加锁成功**
    - **如果锁已存在，但锁的不是当前线程，则证明有其他线程持有锁。返回当前锁的过期时间，加锁失败**

  - <img src="https://upload-images.jianshu.io/upload_images/13230160-2046b77640392660.png?imageMogr2/auto-orient/strip|imageView2/2/w/913/format/webp" style="zoom:60%">

  - 加锁成功后，在redis的内存数据中，就有一条hash结构的数据。Key为锁的名称；field为随机字符串+线程ID；值为1。如果同一线程多次调用`lock`方法，值递增1。

  - ```java
    127.0.0.1:6379> hgetall lock1
    1) "b5ae0be4-5623-45a5-8faa-ab7eb167ce87:1"
    2) "1"
    ```

- 解锁

  - 通过调用`unlock`方法来解锁。

  - ```java
    public RFuture<Void> unlockAsync(final long threadId) {
        final RPromise<Void> result = new RedissonPromise<Void>();
        
        //解锁方法
        RFuture<Boolean> future = unlockInnerAsync(threadId);
    
        future.addListener(new FutureListener<Boolean>() {
            @Override
            public void operationComplete(Future<Boolean> future) throws Exception {
                if (!future.isSuccess()) {
                    cancelExpirationRenewal(threadId);
                    result.tryFailure(future.cause());
                    return;
                }
                //获取返回值
                Boolean opStatus = future.getNow();
                //如果返回空，则证明解锁的线程和当前锁不是同一个线程，抛出异常
                if (opStatus == null) {
                    IllegalMonitorStateException cause = 
                        new IllegalMonitorStateException("
                            attempt to unlock lock, not locked by current thread by node id: "
                            + id + " thread-id: " + threadId);
                    result.tryFailure(cause);
                    return;
                }
                //解锁成功，取消刷新过期时间的那个定时任务
                if (opStatus) {
                    cancelExpirationRenewal(null);
                }
                result.trySuccess(null);
            }
        });
    
        return result;
    }
    ```

  - 再看`unlockInnerAsync`方法。这里也是一段LUA脚本代码。

  - ```java
    protected RFuture<Boolean> unlockInnerAsync(long threadId) {
        return commandExecutor.evalWriteAsync(getName(), LongCodec.INSTANCE, EVAL,
        
                //如果锁已经不存在， 发布锁释放的消息
                "if (redis.call('exists', KEYS[1]) == 0) then " +
                    "redis.call('publish', KEYS[2], ARGV[1]); " +
                    "return 1; " +
                "end;" +
                //如果释放锁的线程和已存在锁的线程不是同一个线程，返回null
                "if (redis.call('hexists', KEYS[1], ARGV[3]) == 0) then " +
                    "return nil;" +
                "end; " +
                //通过hincrby递减1的方式，释放一次锁
                //若剩余次数大于0 ，则刷新过期时间
                "local counter = redis.call('hincrby', KEYS[1], ARGV[3], -1); " +
                "if (counter > 0) then " +
                    "redis.call('pexpire', KEYS[1], ARGV[2]); " +
                    "return 0; " +
                //否则证明锁已经释放，删除key并发布锁释放的消息
                "else " +
                    "redis.call('del', KEYS[1]); " +
                    "redis.call('publish', KEYS[2], ARGV[1]); " +
                    "return 1; "+
                "end; " +
                "return nil;",
        Arrays.<Object>asList(getName(), getChannelName()), 
            LockPubSub.unlockMessage, internalLockLeaseTime, getLockName(threadId));
    
    }
    ```

  - 如上代码，就是释放锁的逻辑。同样的，它也是有三个判断：

    - **如果锁已经不存在，通过publish发布锁释放的消息，解锁成功**
    - **如果解锁的线程和当前锁的线程不是同一个，解锁失败，抛出异常**
    - **通过hincrby递减1，先释放一次锁。若剩余次数还大于0，则证明当前锁是重入锁，刷新过期时间；若剩余次数小于0，删除key并发布锁释放的消息，解锁成功**

  - <img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/13230160-34c1a3f8ec81a652.png" style="zoom:60%">



##### 多个Redis实例的分布式锁

http://redis.cn/topics/distlock.html

#### 如何保证缓存与数据库双写时的数据一致性

>  一般情况下我们都是这样使用缓存的：先读缓存，缓存没有的话，就读数据库，然后取出数据后放入缓存，同时返回响应。这种方式很明显会存在缓存和数据库的数据不一致的情况。

​		你只要用缓存，就可能会涉及到缓存与数据库双存储双写，你只要是双写，就一定会有数据一致性的问题，那么你如何解决一致性问题？
​		一般来说，就是**如果你的系统不是严格要求缓存+数据库必须一致性的话，缓存可以稍微的跟数据库偶尔有不一致的情况，最好不要做这个方案，*读请求和写请求串行化，串到一个内存队列里去，这样就可以保证一定不会出现不一致的情况*。**

​		串行化之后，就会导致系统的吞吐量会大幅度的降低，用比正常情况下多几倍的机器去支撑线上的一个请求。

##### Redis与数据库一致性问题分析

三种更新策略：

1. 先更新数据库，再更新缓存
2. 先删除缓存，再更新数据库
3. 先更新数据库，再删除缓存

- ### **先更新数据库，再更新缓存**

  - 反对

  - **原因一（线程安全角度）**

    - 同时有请求A和请求B进行更新操作，那么会出现：
    - （1）线程A更新了数据库
      （2）线程B更新了数据库
      （3）线程B更新了缓存
      （4）线程A更新了缓存
    - 这就出现请求A更新缓存应该比请求B更新缓存早才对，但是因为网络等原因，B却比A更早更新了缓存。这就导致了脏数据，因此不考虑。

  - **原因二（业务场景角度）**

    - （1）如果你是一个写数据库场景比较多，而读数据场景比较少的业务需求，采用这种方案就会导致，数据压根还没读到，缓存就被频繁的更新，浪费性能。

      （2）如果你写入数据库的值，并不是直接写入缓存的，而是要经过一系列复杂的计算再写入缓存。那么，每次写入数据库后，都再次计算写入缓存的值，无疑是浪费性能的。显然，删除缓存更为适合。

- ### **先删缓存，再更新数据库**

  - 该方案会导致不一致的原因是。同时有一个请求A进行更新操作，另一个请求B进行查询操作。那么会出现如下情形：

    - （1）请求A进行写操作，删除缓存
      （2）请求B查询发现缓存不存在
      （3）请求B去数据库查询得到旧值
      （4）请求B将旧值写入缓存
      （5）请求A将新值写入数据库

    - 上述情况就会导致不一致的情形出现。而且，如果不采用给缓存设置过期时间策略，该数据永远都是脏数据。

    - 如何解决呢？**采用延时双删策略**

      - > ```java
        > public void write(String key,Object data){
        >  
        >     redis.delKey(key);
        >     db.updateData(data);
        >  
        >     Thread.sleep(1000);
        >     redis.delKey(key);
        >  
        > }
        > ```
        >
        > （1）先淘汰缓存
        > （2）再写数据库（这两步和原来一样）
        > （3）休眠1秒，再次淘汰缓存
        >
        > 那么，**这个1秒怎么确定的，具体该休眠多久呢？**
        >
        > 针对上面的情形，读者应该自行评估自己的项目的读数据业务逻辑的耗时。然后写数据的休眠时间则在读数据业务逻辑的耗时基础上，加几百ms即可。这么做的目的，就是确保读请求结束，写请求可以删除读请求造成的缓存脏数据。

- ### **先更新数据库，再删缓存**

  - 老外提出了一个缓存更新套路，名为《Cache-Aside pattern》。其中就指出

    - 更新：应用程序先从cache取数据，没有得到，则从数据库中取数据，成功后，放到缓存中。
    - 命中：应用程序从cache中取数据，取到后返回。
    - 失效：先把数据存到数据库中，成功后，再让缓存失效。

  - **这种情况不存在并发问题么？**

    不是的。假设这会有两个请求，一个请求A做查询操作，一个请求B做更新操作，那么会有如下情形产生

  - （1）缓存刚好失效
    （2）请求A查询数据库，得一个旧值
    （3）请求B将新值写入数据库
    （4）请求B删除缓存
    （5）请求A将查到的旧值写入缓存

    ok，如果发生上述情况，确实是会发生脏数据。

    **然而，发生这种情况的概率又有多少呢？**

    发生上述情况有一个先天性条件，就是步骤（3）的写数据库操作比步骤（2）的读数据库操作耗时更短，才有可能使得步骤（4）先于步骤（5）。可是，大家想想，数据库的读操作的速度远快于写操作的（不然做读写分离干嘛，做读写分离的意义就是因为读操作比较快，耗资源少），因此步骤（3）耗时比步骤（2）更短，这一情形很难出现。

    **[详细](https://blog.csdn.net/diweikang/article/details/94406186)**

##### [Cache Aside Pattern](https://doocs.github.io/advanced-java/#/./docs/high-concurrency/redis-consistence?id=cache-aside-pattern)(**先更新数据库，再删缓存**)

最经典的缓存+数据库读写的模式，就是 **`Cache Aside Pattern`**。

- 读的时候，先读缓存，缓存没有的话，就读数据库，然后取出数据后放入缓存，同时返回响应。
- 更新的时候，**先更新数据库，然后再删除缓存**。

###### **为什么是删除缓存，而不是更新缓存？**

原因很简单，很多时候，在复杂点的缓存场景，缓存不单单是数据库中直接取出来的值。

比如可能更新了某个表的一个字段，然后其对应的缓存，是需要查询另外两个表的数据并进行运算，才能计算出缓存最新的值的。

另外更新缓存的代价有时候是很高的。是不是说，每次修改数据库的时候，都一定要将其对应的缓存更新一份？也许有的场景是这样，但是对于**比较复杂的缓存数据计算的场景**，就不是这样了。如果你频繁修改一个缓存涉及的多个表，缓存也频繁更新。但是问题在于，**这个缓存到底会不会被频繁访问到？**

举个例子，一个缓存涉及的表的字段，在 1 分钟内就修改了 20 次，或者是 100 次，那么缓存更新 20 次、100 次；但是这个缓存在 1 分钟内只被读取了 1 次，有**大量的冷数据**。实际上，如果你只是删除缓存的话，那么在 1 分钟内，这个缓存不过就重新计算一次而已，开销大幅度降低。**用到缓存才去算缓存。**

其实删除缓存，而不是更新缓存，就是一个 lazy 计算的思想，不要每次都重新做复杂的计算，不管它会不会用到，而是让它到需要被使用的时候再重新计算。像 mybatis，hibernate，都有懒加载思想。查询一个部门，部门带了一个员工的 list，没有必要说每次查询部门，都把里面的 1000 个员工的数据也同时查出来啊。80% 的情况，查这个部门，就只是要访问这个部门的信息就可以了。先查部门，同时要访问里面的员工，那么这个时候只有在你要访问里面的员工的时候，才会去数据库里面查询 1000 个员工。

**********

#### Redis缓存与本地缓存

1. 本地缓存无法提供丰富的数据结构，redis可以。
2. 本地缓存需要自己实现过期功能，实现不好可能导致极其严重的后果，而redis经过大量的流量验证，比较安全。
3. redis可以写磁盘，持久化，本地缓存不可以或者说很麻烦要考虑的东西太多。
4. 使用本地缓存极有可能导致严重的线程安全问题，并发考虑严重。
5. 读写速度，不考虑并发问题，本地缓存自然是最快的。



****************

#### Redis主从复制

##### 什么是Redis的主从复制？

**概念：**

主从复制，是指将一台`Redis`服务器的数据，复制到其他的`Redis`服务器。前者称为主节点（`Master/Leader`）, 后者称为从节点（`Slave/Follower`)，数**据的复制是单向的**！==只能由主节点复制到从节点==（主节点以写为主、从节点以读为主）。

==默认情况下，每台Redis服务器都是主节点==，一个主节点可以有0个或者多个从节点，但每个从节点只能有一个主节点。

**作用：**

- **数据冗余：**主从复制实现了数据的热备份，是持久化之外的一种数据冗余的方式。
- **故障恢复**：当主节点故障时，从节点可以暂时替代主节点提供服务，是一种服务冗余的方式
- **负载均衡：**在主从复制的基础上，配合读写分离，由主节点进行写操作，从节点进行读操作，分担服务器的负载；尤其是在读多写少的场景下，通过多个从节点分担负载，提高并发量。
- **高可用基石：**主从复制还是哨兵和集群能够实施的基础。

**为什么使用集群？**

- 单台服务器难以负载大量的请求
- 单台服务器故障率高，系统崩坏概率大
- 单台服务器内存容量有限。

**环境配置**

只配置从库，不配置主库。

```bash
127.0.0.1:6379> info replication # 查看当前库的信息
# Replication
role:master # 角色
connected_slaves:0 # 没有从机
master_replid:f633aa4125b26de00c8637f7f008a12328d54283
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```

默认情况下，每台 Redis 服务器都是主节点 ;我们一般情况下只用配置从机就好了！

使用 `SLAVEOF host port` 就可以为从机配置主机了。

**细节**

1. 从机只能读，不能写，主机可读可写但是多用于写。

   ```bash
    127.0.0.1:6381> set name sakura # 从机6381写入失败
   (error) READONLY You can't write against a read only replica.
   
   127.0.0.1:6380> set name sakura # 从机6380写入失败
   (error) READONLY You can't write against a read only replica.
   
   127.0.0.1:6379> set name sakura # 主机6379写入成功
   OK
   127.0.0.1:6379> get name
   "sakura"
   ```

2. 当主机断电宕机后，默认情况下从机的角色不会发生变化 ，集群中只是失去了写操作，当主机恢复以后，又会连接上从机恢复原状。

3. 当从机断电宕机后，若不是使用配置文件配置的从机，再次启动后自己会作为主机，无法获取之前主机的数据的，若此时重新配置称为从机，又可以获取到主机的所有数据。这里就要提到一个同步原理。

4. 第二条中提到，默认情况下，主机故障后，不会出现新的主机，有两种方式可以产生新的主机：

   ```markdown
   - 从机手动执行命令slaveof no one,这样执行以后从机会独立出来成为一个主机
   - 使用哨兵模式（自动选举）
   ```


##### 复制原理

`Slave `启动成功连接到 `Master `后会发送一个 `sync `同步命令。

`Master `接到命令，启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进执行完毕后，==Master将传送整个数据文件到 Slave==，并完成一次完全同步。

- ==全量复制：==`Slave `服务在接收到数据库文件数据后，将其存盘并加载到内存中。
- ==增量复制：==`Master `继续将新的所有收集到的修改命令依次传给`Slave`，完成同步。

但是只要是重新连接 `Master`，一次完全同步（全量复制）将被自动执行！全部的数据一定可以在从机中看到。

##### 哨兵模式

当主服务器宕机后，需要手动(`slaveof no one`)**把一台从服务器切换为主服务器**，这就需要人工干预，费事费力，还会造成一段时间内服务不可用。这不是一种推荐的方式，更多时候，我们优先考虑 **哨兵模式**。

<img src="https://img-blog.csdnimg.cn/20200924220203813.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpc2VuMDEwNzAxMDc=,size_16,color_FFFFFF,t_70#pic_center" style="zoom:88%">

这里的哨兵有两个作用：

- 通过发送命令，让 `Redis` 服务器监控其运行状态，包括主服务器和从服务器。
- 当哨兵监测到 `Master `宕机，会自动将 `Slave` 切换成 `Master`，然后通过**发布订阅模式**通知其他的从服务器，修改配置文件，让它们切换主机。

然而一个哨兵进程对 `Redis `服务器进行监控，可能会出现问题，为此，我们可以使用多个哨兵进行监控。各个哨兵之间还会进行监控，这样就形成了多哨兵模式。

![img](https://img-blog.csdnimg.cn/20200924220217342.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpc2VuMDEwNzAxMDc=,size_16,color_FFFFFF,t_70#pic_center)

​	假设主服务器宕机，哨兵1先检测到这个结果，系统并不会马上进行 `failover `过程，仅仅是哨兵1主观的认为主服务器不可用，这个现象成为**主观下线**。当后面的哨兵也检测到主服务器不可用，并且数量达到一定值时，那么哨兵之间就会进行一次投票，投票的结果由一个哨兵发起，进行 **`failover[故障转移]`** 操作。切换成功后，就会通过**发布订阅模式**，让各个哨兵把自己监控的从服务器实现切换主机，这个过程称为**客观下线**。

如果 `79` 主机再次上线，只能当 `80` 的从机。

**优点：**

- 哨兵集群，基于主从复制模式，所有主从复制的优点，它都有
- 主从可以切换，故障可以转移，系统的可用性更好
- 哨兵模式是主从模式的升级，手动到自动，更加健壮

**缺点：**

- Redis不好在线扩容，集群容量一旦达到上限，在线扩容就十分麻烦
- 实现哨兵模式的配置其实是很麻烦的，里面有很多配置项

###### 哨兵模式的全部配置

完整的哨兵模式配置文件 `sentinel.conf`

```bash
# Example sentinel.conf
 
# 哨兵sentinel实例运行的端口 默认26379
port 26379
 
# 哨兵sentinel的工作目录
dir /tmp
 
# 哨兵sentinel监控的redis主节点的 ip port 
# master-name  可以自己命名的主节点名字 只能由字母A-z、数字0-9 、这三个字符".-_"组成。
# quorum 当这些quorum个数sentinel哨兵认为master主节点失联 那么这时 客观上认为主节点失联了
# sentinel monitor <master-name> <ip> <redis-port> <quorum>
sentinel monitor mymaster 127.0.0.1 6379 1
 
# 当在Redis实例中开启了requirepass foobared 授权密码 这样所有连接Redis实例的客户端都要提供密码
# 设置哨兵sentinel 连接主从的密码 注意必须为主从设置一样的验证密码
# sentinel auth-pass <master-name> <password>
sentinel auth-pass mymaster MySUPER--secret-0123passw0rd
 
 
# 指定多少毫秒之后 主节点没有应答哨兵sentinel 此时 哨兵主观上认为主节点下线 默认30秒
# sentinel down-after-milliseconds <master-name> <milliseconds>
sentinel down-after-milliseconds mymaster 30000
 
# 这个配置项指定了在发生failover主备切换时最多可以有多少个slave同时对新的master进行 同步，
这个数字越小，完成failover所需的时间就越长，
但是如果这个数字越大，就意味着越 多的slave因为replication而不可用。
可以通过将这个值设为 1 来保证每次只有一个slave 处于不能处理命令请求的状态。
# sentinel parallel-syncs <master-name> <numslaves>
sentinel parallel-syncs mymaster 1
 

# 故障转移的超时时间 failover-timeout 可以用在以下这些方面： 
#1. 同一个sentinel对同一个master两次failover之间的间隔时间。
#2. 当一个slave从一个错误的master那里同步数据开始计算时间。直到slave被纠正为向正确的master那里同步数据时。
#3.当想要取消一个正在进行的failover所需要的时间。  
#4.当进行failover时，配置所有slaves指向新的master所需的最大时间。不过，即使过了这个超时，slaves依然会被正确配置为指向master，但是就不按parallel-syncs所配置的规则来了
# 默认三分钟
# sentinel failover-timeout <master-name> <milliseconds>
sentinel failover-timeout mymaster 180000
 
# SCRIPTS EXECUTION
 
#配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。
#对于脚本的运行结果有以下规则：
#若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10
#若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。
#如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
#一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被一个SIGKILL信号终止，之后重新执行。
 
#通知型脚本:当sentinel有任何警告级别的事件发生时（比如说redis实例的主观失效和客观失效等等），将会去调用这个脚本，
#这时这个脚本应该通过邮件，SMS等方式去通知系统管理员关于系统不正常运行的信息。调用该脚本时，将传给脚本两个参数，
#一个是事件的类型，
#一个是事件的描述。
#如果sentinel.conf配置文件中配置了这个脚本路径，那么必须保证这个脚本存在于这个路径，并且是可执行的，否则sentinel无法正常启动成功。
#通知脚本
# sentinel notification-script <master-name> <script-path>
  sentinel notification-script mymaster /var/redis/notify.sh
 
# 客户端重新配置主节点参数脚本
# 当一个master由于failover而发生改变时，这个脚本将会被调用，通知相关的客户端关于master地址已经发生改变的信息。
# 以下参数将会在调用脚本时传给脚本:
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
# 目前<state>总是“failover”,
# <role>是“leader”或者“observer”中的一个。 
# 参数 from-ip, from-port, to-ip, to-port是用来和旧的master和新的master(即旧的slave)通信的
# 这个脚本应该是通用的，能被多次调用，不是针对性的。
# sentinel client-reconfig-script <master-name> <script-path>
sentinel client-reconfig-script mymaster /var/redis/reconfig.sh
```

#### 哈希一致性

​	要了解一致性哈希，首先我们必须了解传统的哈希及其在大规模分布式系统中的局限性。简单地说，哈希就是一个键值对存储，在给定键的情况下，可以非常高效地找到所关联的值。假设我们要根据其邮政编码查找城市中的街道名称。一种最简单的实现方式是将此信息以哈希字典的形式进行存储 `<Zip Code，Street Name>`。

​	当数据太大而无法存储在一个节点或机器上时，问题变得更加有趣，系统中需要多个这样的节点或机器来存储它。比如，使用多个 Web 缓存中间件的系统。**那如何确定哪个 key 存储在哪个节点上？针对该问题，最简单的解决方案是使用哈希取模来确定。** 给定一个 key，先对 key 进行哈希运算，将其除以系统中的节点数，然后将该 key 放入该节点。同样，在获取 key 时，对 key 进行哈希运算，再除以节点数，然后转到该节点并获取值。上述过程对应的哈希算法定义如下：

```bash
node_number=hash(key) % N # 其中 N 为节点数。
```

下图描绘了多节点系统中的传统的哈希取模算法，基于该算法可以实现简单的负载均衡。

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/bVbA7as">

##### 1、传统哈希取模算法的局限性

假设我们有一个哈希取模的方法 `hash` ，对**semlinker、kakuqo 和 test** 3 个键进行哈希运算并取余。假设得到下面的结果:

```bash
node_number=hash("semlinker") % 3 -> 1
node_number=hash("kakuqo") % 3 -> 2
node_number=hash("test") % 3 -> 0
```

基于这个结果，我们可以创建这样的表格

|    键     | 哈希值 | 节点编号 |
| :-------: | :----: | :------: |
| semlinker |  2650  |    1     |
|  kakuqo   |  1847  |    2     |
|   test    |  1356  |    0     |

###### 1.1、节点减少的场景

**在分布式多节点系统中，出现故障很常见。任何节点都可能在没有任何事先通知的情况下挂掉，针对这种情况我们期望系统只是出现性能降低，正常的功能不会受到影响。** 对于原始示例，当节点出现故障时会发生什么？原始示例中有的 3 个节点，假设其中 1 个节点出现故障，这时节点数发生了变化，节点个数从 3 减少为 2，此时表格的状态发生了变化：

![image-20210925205511217](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210925205511217.png)

很明显节点的减少会导致键与节点的映射关系发生变化，**这个变化对于新的键来说并不会产生任何影响，但对于已有的键来说，将导致节点映射错误**，以 “semlinker” 为例，变化前系统有 3 个节点，该键对应的节点编号为 1，当出现故障时，节点数减少为 2 个，此时该键对应的节点编号为 0。

###### 1.2、节点增加的场景

**在分布式多节点系统中，对于某些场景比如节日大促，就需要对服务节点进行扩容，以应对突发的流量。** 对于原始示例，当增加节点会发生什么？原始示例中有的 3 个节点，假设进行扩容临时增加了 1 个节点，这时节点数发生了变化，节点个数从 3 增加为 4 个，此时表格的状态发生了变化：

![image-20210925205633638](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210925205633638.png)

很明显节点的增加也会导致键与节点的映射关系发生变化，**这个变化对于新的键来说并不会产生任何影响，但对于已有的键来说，将导致节点映射错误**，同样以 “semlinker” 为例，变化前系统有 3 个节点，该键对应的节点编号为 1，当增加节点时，节点数增加为 4 个，此时该键对应的节点编号为 2。

当集群中节点的数量发生变化时，之前的映射规则就可能发生变化。如果集群中每个机器提供的服务没有差别，这不会有什么影响。**但对于分布式缓存这样的系统而言，映射规则失效就意味着之前缓存的失效，若同一时刻出现大量的缓存失效，则可能会出现 “缓存雪崩”，这将会造成灾难性的后果。**

**要解决此问题，我们必须在其余节点上重新分配所有现有键，这可能是非常昂贵的操作，并且可能对正在运行的系统产生不利影响。当然除了重新分配所有现有键的方案之外，还有另一种更好的方案即使用一致性哈希算法。**

##### 2、一致性哈希算法

一致性哈希算法在 1997 年由麻省理工学院提出，是一种特殊的哈希算法，在移除或者添加一个服务器时，能够尽可能小地改变已存在的服务请求与处理请求服务器之间的映射关系。

###### 2.1、一致性哈希算法的优点

- 可扩展性。一致性哈希算法保证了增加或减少服务器时，数据存储的改变最少，相比传统哈希算法大大节省了数据移动的开销 。

- 更好地适应数据的快速增长。采用一致性哈希算法分布数据，当数据不断增长时，部分虚拟节点中可能包含很多数据、造成数据在虚拟节点上分布不均衡，此时可以将包含数据多的虚拟节点分裂，这种分裂仅仅是将原有的虚拟节点一分为二、不需要对全部的数据进行重新哈希和划分。

  虚拟节点分裂后，如果物理服务器的负载仍然不均衡，只需在服务器之间调整部分虚拟节点的存储分布。这样可以随数据的增长而动态的扩展物理服务器的数量，且代价远比传统哈希算法重新分布所有数据要小很多。

###### 2.2、 一致性哈希算法与哈希算法的关系

一致性哈希算法是在哈希算法基础上提出的，在动态变化的分布式环境中，哈希算法应该满足的几个条件：平衡性、单调性和分散性。

- 平衡性：是指 hash 的结果应该平均分配到各个节点，这样从算法上解决了负载均衡问题。
- 单调性：是指在新增或者删减节点时，不影响系统正常运行。
- 分散性：是指数据应该分散地存放在分布式集群中的各个节点（节点自己可以有备份），不必每个节点都存储所有的数据

##### 3、一致性哈希算法的原理

一致性哈希算法通过一个叫作一致性哈希环的数据结构实现。这个环的起点是 $0$，终点是 $2^{32} - 1$，并且起点与终点连接，故这个环的整数分布范围是 $[0, 2^{32}-1]$，如下图所示：

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/bVbA7aA">

###### 3.1、将对象放置到哈希环

假设我们有 "semlinker"、"kakuqo"、"lolo"、"fer" 四个对象，分别简写为 o1、o2、o3 和 o4，然后使用哈希函数计算这个对象的 hash 值，值的范围是 $[0, 2^{32}-1]$：

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/bVbA7aB)

图中对象的映射关系如下：

```bash
hash(o1) = k1; hash(o2) = k2;
hash(o3) = k3; hash(o4) = k4;
```

###### 3.2、将服务器放置到哈希环

接着使用同样的哈希函数，我们将服务器也放置到哈希环上，可以选择服务器的 IP 或主机名作为键进行哈希，这样每台服务器就能确定其在哈希环上的位置。这里假设我们有 3 台缓存服务器，分别为 cs1、cs2 和 cs3：

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/bVbA7aG)

图中服务器的映射关系如下：

```bash
hash(cs1) = t1; hash(cs2) = t2; hash(cs3) = t3; # Cache Server
```

###### 3.3、为对象选择服务器

**将对象和服务器都放置到同一个哈希环后，在哈希环上顺时针查找距离这个对象的 hash 值最近的机器，即是这个对象所属的机器。** 以 o2 对象为例，顺序针找到最近的机器是 cs2，故服务器 cs2 会缓存 o2 对象。而服务器 cs1 则缓存 o1，o3 对象，服务器 cs3 则缓存 o4 对象。

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/bVbA7aH)

###### 3.4、服务器增加的情况

假设由于业务需要，我们需要增加一台服务器 cs4，经过同样的 hash 运算，该服务器最终落于 t1 和 t2 服务器之间，具体如下图所示：

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/bVbA7aI)

对于上述的情况，只有 t1 和 t2 服务器之间的对象需要重新分配。在以上示例中只有 o3 对象需要重新分配，即它被重新到 cs4 服务器。在前面我们已经分析过，如果使用简单的取模方法，当新添加服务器时可能会导致大部分缓存失效，而使用一致性哈希算法后，这种情况得到了较大的改善，因为只有少部分对象需要重新分配。

###### 3.5、服务器减少的情况

假设 cs3 服务器出现故障导致服务下线，这时原本存储于 cs3 服务器的对象 o4，需要被重新分配至 cs2 服务器，其它对象仍存储在原有的机器上。

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/bVbA7aJ)

###### 3.6、虚拟节点

到这里一致性哈希的基本原理已经介绍完了，但对于新增服务器的情况还存在一些问题。新增的服务器 cs4 只分担了 cs1 服务器的负载，服务器 cs2 和 cs3 并没有因为 cs4 服务器的加入而减少负载压力。如果 cs4 服务器的性能与原有服务器的性能一致甚至可能更高，那么这种结果并不是我们所期望的。

**针对这个问题，我们可以通过引入虚拟节点来解决负载不均衡的问题。即将每台物理服务器虚拟为一组虚拟服务器，将虚拟服务器放置到哈希环上，如果要确定对象的服务器，需先确定对象的虚拟服务器，再由虚拟服务器确定物理服务器。**

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/bVbA7aK)

4、一致性哈希算法的代码实现

这里我们只介绍不带虚拟节点的一致性哈希算法实现：

```java
import java.util.SortedMap;
import java.util.TreeMap;

public class ConsistentHashingWithoutVirtualNode {
    //待添加入Hash环的服务器列表
    private static String[] servers = {"192.168.0.1:8888", "192.168.0.2:8888", 
      "192.168.0.3:8888"};

    //key表示服务器的hash值，value表示服务器
    private static SortedMap<Integer, String> sortedMap = new TreeMap<Integer, String>();

    //程序初始化，将所有的服务器放入sortedMap中
    static {
        for (int i = 0; i < servers.length; i++) {
            int hash = getHash(servers[i]);
            System.out.println("[" + servers[i] + "]加入集合中, 其Hash值为" + hash);
            sortedMap.put(hash, servers[i]);
        }
    }

    //得到应当路由到的结点
    private static String getServer(String key) {
        //得到该key的hash值
        int hash = getHash(key);
        //得到大于该Hash值的所有Map
        SortedMap<Integer, String> subMap = sortedMap.tailMap(hash);
        if (subMap.isEmpty()) {
            //如果没有比该key的hash值大的，则从第一个node开始
            Integer i = sortedMap.firstKey();
            //返回对应的服务器
            return sortedMap.get(i);
        } else {
            //第一个Key就是顺时针过去离node最近的那个结点
            Integer i = subMap.firstKey();
            //返回对应的服务器
            return subMap.get(i);
        }
    }

    //使用FNV1_32_HASH算法计算服务器的Hash值
    private static int getHash(String str) {
        final int p = 16777619;
        int hash = (int) 2166136261L;
        for (int i = 0; i < str.length(); i++)
            hash = (hash ^ str.charAt(i)) * p;
        hash += hash << 13;
        hash ^= hash >> 7;
        hash += hash << 3;
        hash ^= hash >> 17;
        hash += hash << 5;

        // 如果算出来的值为负数则取其绝对值
        if (hash < 0)
            hash = Math.abs(hash);
        return hash;
    }

    public static void main(String[] args) {
        String[] keys = {"semlinker", "kakuqo", "fer"};
        for (int i = 0; i < keys.length; i++)
            System.out.println("[" + keys[i] + "]的hash值为" + getHash(keys[i])
                    + ", 被路由到结点[" + getServer(keys[i]) + "]");
    }

}
```

以上代码成功运行后，在控制台会输出以下结果：

```java
[192.168.0.1:8888]加入集合中, 其Hash值为1326271016
[192.168.0.2:8888]加入集合中, 其Hash值为1132535844
[192.168.0.3:8888]加入集合中, 其Hash值为115798597

[semlinker]的hash值为1549041406, 被路由到结点[192.168.0.3:8888]
[kakuqo]的hash值为463104755, 被路由到结点[192.168.0.2:8888]
[fer]的hash值为1677150790, 被路由到结点[192.168.0.3:8888]
```

https://blog.csdn.net/suifeng629/article/details/81567777

#### Redis实现消息队列

##### 1、使用Redis的列表类型 (`LPUSH` + `BRPOP`)

  	在 `Redis` 中，`List` 类型是按照插入顺序排序的字符串链表。和数据结构中的普通链表一样，我们可以在其头部 `(left)` 和尾部 `(right)` 添加新的元素。在插入时，如果该键并不存在，`Redis` 将为该键创建一个新的链表。与此相反，如果链表中所有的元素均被移除，那么该键也将会被从数据库中删除。`List` 中可以包含的最大元素数量是4294967295。

​		从元素插入和删除的效率视角来看，如果我们是在链表的两头插入或删除元素，这将会是非常高效的操作，即使链表中已经存储了百万条记录，该操作也可以在常量时间内完成。然而需要说明的是，如果元素插入或删除操作是作用于链表中间，那将会是非常低效的。相信对于有良好数据结构基础的开发者而言，这一点并不难理解。(类似于 `java` 的 `ArrayList` )。

> 1 消费生产者：**开启5个线程生产消息**

```java
public class MessageProducer extends Thread {
    public static final String MESSAGE_KEY = "message:queue";
    private volatile int count;

    public void putMessage(String message) {
        Jedis jedis = JedisPoolUtils.getJedis();
        Long size = jedis.lpush(MESSAGE_KEY, message);
        System.out.println(Thread.currentThread().getName() + " put message,size=" + size + ",count=" + count);
        count++;
    }

    @Override
    public synchronized void run() {
        for (int i = 0; i < 5; i++) {
            putMessage("message" + count);
        }
    }

    public static void main(String[] args) {
        MessageProducer messageProducer = new MessageProducer();
        Thread t1 = new Thread(messageProducer, "thread1");
        Thread t2 = new Thread(messageProducer, "thread2");
        Thread t3 = new Thread(messageProducer, "thread3");
        Thread t4 = new Thread(messageProducer, "thread4");
        Thread t5 = new Thread(messageProducer, "thread5");
        t1.start();
        t2.start();
        t3.start();
        t4.start();
        t5.start();
    }
}
```

> 2 消息消费者：**开启两个线程消费消息**

```java
public class MessageConsumer implements Runnable {
    public static final String MESSAGE_KEY = "message:queue";
    private volatile int count;

    public void consumerMessage() {
        Jedis jedis = JedisPoolUtils.getJedis();
        String message = jedis.rpop(MESSAGE_KEY);
        System.out.println(Thread.currentThread().getName() + " consumer message,message=" + message + ",count=" + count);
        count++;
    }

    @Override
    public void run() {
        while (true) {
            consumerMessage();
        }
    }

    public static void main(String[] args) {
        MessageConsumer messageConsumer = new MessageConsumer();
        Thread t1 = new Thread(messageConsumer, "thread6");
        Thread t2 = new Thread(messageConsumer, "thread7");
        t1.start();
        t2.start();
    }
}
```

> 发现问题

但上述例子中消息消费者有一个问题存在，即需要不停的调用 `rpop` 方法查看 `List` 中是否有待处理消息。每调用一次都会发起一次连接，这会造成不必要的浪费。也许你会使用 `Thread.sleep()` 等方法让消费者线程隔一段时间再消费，但这样做有两个问题：

  1）、如果生产者速度大于消费者消费速度，消息队列长度会一直增大，时间久了会占用大量内存空间。

  2）、如果睡眠时间过长，这样不能处理一些时效性的消息，睡眠时间过短，也会在连接上造成比较大的开销。

> 解决方法：==**`brpop` 和 `blpop`可以实现阻塞读取**==
>
> 　　`brpop` 命令可以接收多个键，其完整的命令格式为 `BRPOP key [key ...] timeout`, 如: `brpop key1 0`。意义是同时检测多个键，如果所有键都没有元素则阻塞，如果其中一个有元素则从该键中弹出该元素(==会按照 key 的顺序进行读取，可以实现具有优先级的队列==)。

##### 2、发布/订阅模式

> 　除了实现任务队列外，`redis` 还提供了一组命令可以让开发者实现"发布/订阅"( `publish/subscribe` )模式。"发布/订阅"模式同样可以实现进程间的消息传递，其原理如下:
>
> - "发布/订阅"模式包含两种角色，分别是发布者和订阅者；
> - 订阅者可以订阅一个或者多个频道(channel)；
> - 而发布者可以向指定的频道(channel)发送消息，所有订阅此频道的订阅者都会收到此消息。

- 发布消息：`publish channel message`

  - 例如：

  - ```java
    127.0.0.1:6379> publish channel:1 hi
    (integer) 0
    ```

  - 这样消息就发出去了。返回值表示接收这条消息的订阅者数量。发出去的消息不会被持久化，也就是有客户端订阅 `channel:1` 后只能接收到后续发布到该频道的消息，之前的就接收不到了。

- 订阅频道：`subscribe channel1 [channel2 ...]` 

  - > 不会收到订阅之前就发布到该频道的消息

  - 例如：

  - ```java
    127.0.0.1:6379> subscribe channel:1
    Reading messages... (press Ctrl-C to quit)
    1) "subscribe"
    2) "channel:1"
    3) (integer) 1
    ```

  - 执行上面命令客户端会进入订阅状态，处于此状态下客户端不能使用除 `subscribe`、`unsubscribe`、`psubscribe` 和 `punsubscribe` 这四个属于"发布/订阅"之外的命令，否则会报错。

进入订阅状态后客户端可能收到 3 种类型的回复。每种类型的回复都包含 3 个值，第一个值是消息的类型，根据消类型的不同，第二个和第三个参数的含义可能不同。

消息类型的取值可能是以下 3 个:

- `subscribe`。   表示订阅成功的反馈信息。第二个值是订阅成功的频道名称，第三个是当前客户端订阅的频道数量。
- `message`。        表示接收到的消息，第二个值表示产生消息的频道名称，第三个值是消息的内容。
- `unsubscribe`。表示成功取消订阅某个频道。第二个值是对应的频道名称，第三个值是当前客户端订阅的频道数量，当此值为0时客户端会退出订阅状态，之后就可以执行其他非"发布/订阅"模式的命令了。

#### Jedis和Lettuce的区别

- `jedis` 和 `Lettuce` 都是 `Redis` 的客户端，它们都可以连接Redis服务器，但是在SpringBoot2.0之后默认都是使用的 `Lettuce` 这个客户端连接 `Redis` 服务器。因为当使用 `Jedis` 客户端连接 `Redis` 服务器的时候，每个线程都要拿自己创建的 `Jedis` 实例去连接 `Redis` 客户端，当有很多个线程的时候，不仅开销大需要反复的创建关闭一个 `Jedis` 连接，而且也是线程不安全的，一个线程通过 `Jedis` 实例更改 `Redis` 服务器中的数据之后会影响另一个线程；
- 但是如果使用 `Lettuce` 这个客户端连接 `Redis` 服务器的时候，就不会出现上面的情况，`Lettuce` 底层使用的是 `Netty`，当有多个线程都需要连接 `Redis` 服务器的时候，可以保证只创建一个 `Lettuce` 连接，使所有的线程共享这一个 `Lettuce` 连接，这样可以减少创建关闭一个 `Lettuce` 连接时候的开销；而且这种方式也是线程安全的，不会出现一个线程通过 `Lettuce` 更改 `Redis` 服务器中的数据之后而影响另一个线程的情况；

##### 为什么 Jedis 不是线程安全的？

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/357783-20200928171248293-715372143.png)

Jedis类中有 `RedisInputStream` 和 `RedisOutputStream` 两个属性，而发送命令和获取返回值都是使用这两个成员变量，显然，这很容易引发多线程问题。