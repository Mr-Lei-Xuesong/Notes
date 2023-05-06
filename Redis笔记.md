# NoSQL数据库简介

> NoSQL(NoSQL = **Not Only SQL** )，意即“不仅仅是SQL”，泛指**非关系型的数据库**。 
>
> NoSQL 不依赖业务逻辑方式存储，而以简单的key-value模式存储。因此大大的增加了数据库的扩展能力

* 不遵循SQL标准
* 不支持ACID

* 远超于SQL的性能

## 适用场景

* 对数据高并发的读写
* 海量数据的读写
* 对数据高可扩展性的

## 不适用场景

* 需要事务支持
* 基于sql的结构化查询存储，处理复杂的关系,需要即席查询

## 行列式存储数据库

1. 行式数据库

   ![](.\img\104.jpg)

2. 列式数据库

   ![](.\img\105.jpg)

## Redis概述

* Redis是一个**开源**的**key-value**存储系统
* 和Memcached类似，它支持存储的value类型相对更多，包括**string**(字符串)、**list**(链表)、**set**(集合)、**zset**(sorted set --有序集合)和**hash**（哈希类型）
* 这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是**原子性**的
* 在此基础上，Redis支持各种不同方式的**排序**
* 与memcached一样，为了保证效率，数据都是**缓存在内存**中
* 区别的是Redis会**周期性**的把更新的**数据写入磁盘**或者把修改操作写入追加的记录文件
* 并且在此基础上实现了**master-slave(主从)**同步

# 常用五大数据类型

## Redis键(Key)

* 查看当前库所有的key
  * `keys *`
* 判断某个key是否存在
  * `exists key`
* 查看key的类型
  * `type key`
* 删除指定key的数据
  * `del key`
* 删除指定key的数据(非阻塞)
  * `unlink key`
* 为给定key设置过期时间，秒/单位
  * `expire key time`
* 查看key还有多久过期，**-1表示永不过期，-2表示已过期**
  * `ttl key`

* 切换数据库，从0-15
  * `select db`

* 查看当前数据的key的数量
  * `dbsize`

* 清空当前库
  * `flushdb`
* 通杀全部库
  * `flushall`

## 字符串(String)

> String类型**是二进制安全的**，意味着Redis的String可以包含任何数据，包括jpg图片或者序列化的对象，一个Redis中字符串value最多可以是**512M**

### 常用命令

* 添加键值对
  * `set <key> <value>`
* 查询对应键值
  * `get <key>`
* 将给定值追加到末尾
  * `append <key> <value>`
* 获取值的长度
  * `strlen <key>`
* 只有key不存在时，添加键值对
  * `setnx <key> <value>`
* 将key中存储的数字值增1
  * `incr <key>`
* 将key中存储的数字值减1
  * `decr <key>`
* 根据步长增加值
  * `incrby <key> <step>`
* 根据步长减少值
  * `decrby <key> <step>`
* 同时设置一个或多个键值对
  * `mset <key1> <value1> <key2> <value2>...`
* 同时获取一个或多个value
  * `mget <key1> <key2>...`
* 同时设置一个或多个键值对，当key中有一个已存在则都失败
  * `msetnx <key1> <value1> <key2> <value2>...`
* 获取值的范围
  * `getrange <key> <起始位置> <结束位置>`
* 覆盖所存储的字符串值，从<起始位置>开始**(索引从0开始)**
  * `setrange <key> <起始位置> <value>`

* 设置键值对同时，设置过期时间，单位/秒
  * `setex <key> <过期时间> <value>`
* 设置新值同时获取旧值
  * `getset <key> <value>`

### 数据结构

> String的数据结构为简单动态字符串(Simple Dynamic String,缩写SDS)。是可以修改的字符串，内部结构实现上类似于Java的ArrayList，采用预分配冗余空间的方式来减少内存的频繁分配
>
> 当字符串长度小于1M时，扩容都是加倍现有的空间，如果超过1M，扩容时一次只会多扩1M的空间。需要注意的是字符串最大长度为512M



![](.\img\107.jpg)



## 列表(List)

> 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。它的底层实际是个**双向链表**，对两端的操作性能很高，通过索引下标的操作中间的节点性能会较差

![](.\img\106.jpg)

### 常用命令

* 从左边插入一个或多个值
  * `lpush <key1> <value1> <key2> <value2>...`
* 从右边插入一个或多个值
  * `rpush <key1> <value1> <key2> <value2>...`
* 从左边吐出一个值，**值在键在，值空键亡**
  * `lpop <key>`
* 从右边吐出一个值
  * `rpop <key>`

* 从<key1>列表右边吐出一个值，插到<key2>列表左边
  * `rpoplpush <key1> <key2>`
* 按照索引下标获得元素(从左到右)，**(0 -1 表示获取所有)**
  * `lrange <key> <start> <stop>`
* 按照索引下标获得元素(从左到右)
  * `lindex <key> <index>`
* 获得列表长度
  * `llen <key>`
* 在<value>的后面插入新的<value>值
  * `linsert <key> before[after] <value> <newValue>`
* 从左边删除<n>个<value>(从左到右)
  * `lrem <key> <n> <value>`

* 将列表下标为<index>的值替换成<value>
  * `lset <key> <index> <value>`

### 数据结构

> List的数据结构为快速链表**quickList**
>
> 首先在列表元素较少的情况下会使用一块连续的内存存储，这个结构是ziplist，也即是压缩列表
>
> 它将所有的元素紧挨着一起存储，分配的是一块连续的内存。当数据量比较多的时候才会改成quicklist
>
> Redis将链表和ziplist结合起来组成了quicklist。也就是将多个ziplist使用双向指针串起来使用。这样既满足了快速的插入删除性能，又不会出现太大的空间冗余

## 集合(Set)

> Set对外提供的功能与list类似是一个列表的功能，特殊之处在于set是可以**自动排重**的，当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择
>
> Set是string类型的无序集合。**它底层其实是一个value为null的hash表**，所以添加，删除，查找的**复杂度都是O(1)**

### 常用命令

* 将一个或多个元素添加到集合中，已经存在的元素将被忽略
  * `sadd <key> <value1> <value2>...`

* 取出该集合的所有值
  * `smembers <key>`
* 判断集合<key>是否含有该<value>值，**有返回1，没有返回0**
  * `sismember <key> <value>`
* 返回该集合的元素个数
  * `scard <key>`
* 删除集合中的一个或多个元素
  * `srem <key> <value1> <value2>...`
* 随机从该集合吐出一个值
  * `spop <key>`
* 随机从该集合中取出n个值；不会从集合中删除
  * `srandmember <key> <n>`
* 把集合中一个值从一个集合移动到另一个集合
  * `smove <source> <target> <value>`
* 返回两个集合的**交集**元素
  * `sinter <key1> <key2>`
* 返回两个集合的**并集**元素
  * `sunion <key1> <key2>`
* 返回两个集合的**差集**元素（key1中，不包含key2中的）
  * `sdiff <key1> <key2>`

### 数据结构

> Set数据结构是dict字典，字典是用哈希表实现的

## 哈希(Hash)

> hash 是一个键值对集合
>
> hash是一个string类型的field和value的映射表，hash特别适合用于存储对象

### 常用命令

* 给<key>集合中的<filed>键赋值<value>
  * `hset <key> <filed> <value>`
* 从<key>集合<filed>取出value
  * `hget <key> <filed>`
* 批量设置hash的值
  * `hmset <key1> <filed1> <value1> <filed2> <value2>...`
* 查看哈希表key中，给定域filed是否存在。**返回1代表存在；0代表不存在**
  * `hexists <key> <filed>`
* 列出该hash集合的所有filed
  * `hkeys <key>`
* 列出该hash集合的所有value
  * `hvals <key>`
* 为哈希表key中的域filed的值加上增量n
  * `hincrby <key> <filed> <n>`
* 将哈希表key中的域filed的值设置为value，当且仅当域filed不存在。**返回1代表成功，0代表失败**
  * `hsetnx <key> <filed> <value>`

### 数据结构

> Hash类型对应的数据结构是两种：ziplist（压缩列表），hashtable（哈希表）。当field-value长度较短且个数较少时，使用ziplist，否则使用hashtable

## 有序集合(Zset)

> 有序集合zset与普通集合set非常相似，是一个没有重复元素的字符串集合
>
> 不同之处是有序集合的每个成员都关联了一个**评分（score）**,这个评分（score）被用来按照从最低分到最高分的方式排序集合中的成员。集合的成员是唯一的，但是评分可以是重复了
>
> 因为元素是有序的, 所以也可以很快的根据评分（score）或者次序（position）来获取一个范围的元素。
>
> 访问有序集合的中间元素也是非常快的,因此你能够使用有序集合作为一个没有重复成员的智能列表 

### 常用命令

* 将一个或多个元素及其score值加入到有序集key中
  * `zadd <key> <score1> <value1> <score2> <value2>...`
* 返回有序集key中，下标在start，stop之间的元素；带withscores，可以让分数一起和值返回到结果集
  * `zrange <key> <start> <stop> [withscores]`

* 返回有序集key中，所有score值介于min和max之间(包括等于min或max)的成员。有序集成员按score值递增(从小到大)排序
  * `zrangebyscore <key> <min> <max> [withscores]`
* 同上，改为从大到小排序
  * `zrevrangebyscore <key> <max> <min> [withscores]`
* 为元素的score加上增量n
  * `zincrby <key> <n> <value>`

* 删除该集合下，指定值的元素
  * `zrem <key> <value>`
* 统计该集合，分数区间内的元素个数
  * `zcount <key> <min> <max>`
* 返回该值在集合中的排名，从0开始
  * `zrank <key> <value>`

### 数据结构

> SortedSet(zset)是Redis提供的一个非常特别的数据结构，一方面它等价于Java的数据结构Map<String, Double>，可以给每一个元素value赋予一个权重score，另一方面它又类似于TreeSet，内部的元素会按照权重score进行排序，可以得到每个元素的名次，还可以通过score的范围来获取元素的列表。
>
> zset底层使用了两个数据结构
>
> 1. hash，hash的作用就是关联元素value和权重score，保障元素value的唯一性，可以通过元素value找到相应的score值
> 2. 跳跃表，跳跃表的目的在于给元素value排序，根据score的范围获取元素列表。

# 发布和订阅

> Redis 发布订阅 (pub/sub) 是一种消息通信模式：发送者 (pub) 发送消息，订阅者 (sub) 接收消息
>
> Redis 客户端可以订阅任意数量的频道

* 客户端可以订阅频道

  ![](.\img\108.jpg)

* 当给这个频道发布消息后，消息就会发送给订阅的客户端

  ![](.\img\109.jpg)

## 命令行实现

1. 打开一个客户端订阅 channel1

   `subscribe channel1`

2. 打开另一个客户端，给channel1发布消息

   `publish channel1 hello`

# 新数据类型

## Bitmaps

### 简介

> 现代计算机用二进制（位） 作为信息的基础单位， 1个字节等于8位， 例如“abc”字符串是由3个字节组成， 但实际在计算机存储时将其用二进制表示， “abc”分别对应的ASCII码分别是97、 98、 99， 对应的二进制分别是01100001、 01100010和01100011。合理地使用操作位能够有效地提高内存使用率和开发效率
>
> Redis提供了Bitmaps这个“数据类型”可以实现对位的操作：
>
> 1. Bitmaps本身不是一种数据类型， 实际上它就是字符串（key-value） ， 但是它可以对字符串的位进行操作
> 2. Bitmaps单独提供了一套命令， 所以在Redis中使用Bitmaps和使用字符串的方法不太相同。 可以把Bitmaps想象成一个以位为单位的数组， 数组的每个单元只能存储0和1， 数组的下标在Bitmaps中叫做偏移量

### 命令

* 设置Bitmaps中某个偏移量的值(0或1)
  * `setbit <key> <offset> <value>`
  * offset：偏移量从0开始

* 获取键的第offset位的值(从0开始算)
  * `getbit <key> <offset>`

* 统计值被设置为1的个数
  * `bitcount <key> [start] [end]`
* 复合操作，做多个Bitmaps的and(交集)、or(并集)、not(非)、xor(异或)操作并将结果保存在<destkey>中
  * `bitop and[or/not/xor]<destkey> <key> [key...]`

## HyperLogLog

### 简介

> HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的。
>
> 在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。
>
> 但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素

### 命令

* 添加指定元素到HyperLogLog；成功返回1，失败返回0
  * `pfadd <key> <element> [element...]`
* 计算HyperLogLog的基数
  * `pfcount <key>`

* 将一个或多个HyperLogLog合并后的结果存储在另一个HyperLogLog中
  * `pfmerge <destkey> <sorcekey> [sourcekey...]`

## Geospatial

### 简介

> GEO，Geographic，地理信息的缩写。该类型，就是元素的2维坐标，在地图上就是经纬度。redis基于该类型，提供了经纬度设置，查询，范围查询，距离查询，经纬度Hash等常见操作

### 命令

* 添加地理位置(经度、维度、名称)
  * `geoadd <key> <longitude> <latitude> <member> [longitude latitude member...]`
  * 有效的经度从 -180 度到 180 度。有效的纬度从 -85.05112878 度到 85.05112878 度
  * 当坐标位置超出指定范围时，该命令将会返回一个错误。
  * 已经添加的数据，是无法再次往里面添加的。
* 获取指定地区的坐标值
  * `geopos <key> <member> [member...]`
* 获取两个位置之间的直线距离
  * `geodist <key> <member1> <member2> [单位]`
  * m 表示单位为米[默认值]。
  * km 表示单位为千米。
  * mi 表示单位为英里。
  * ft 表示单位为英尺。

* 以给定的经纬度为中心，找出某一半径内的元素
  * `georadius <key> <longitude> <latitude> <radius> [单位]`

# Jedis客户端

* 引入依赖

  ```xml
  <dependency>
      <groupId>redis.clients</groupId>
      <artifactId>jedis</artifactId>
      <version>3.2.0</version>
  </dependency>
  ```

* 创建Jedis客户端

  ```java
  Jedis jedis=new Jedis("host");
  ```

# SpringBoot整合Redis

* 引入依赖

  ```xml
  <dependency>
  	<groupId>org.springframework.boot</groupId>
  	<artifactId>spring-boot-starter-data-redis</artifactId>
  </dependency>
  
  <!-- spring2.X集成redis所需common-pool2-->
  <dependency>
  	<groupId>org.apache.commons</groupId>
  	<artifactId>commons-pool2</artifactId>
  	<version>2.6.0</version>
  </dependency>
  
  ```

* 配置文件

  ```properties
  #Redis服务器地址
  spring.redis.host=192.168.140.136
  #Redis服务器连接端口
  spring.redis.port=6379
  #Redis数据库索引（默认为0）
  spring.redis.database= 0
  #连接超时时间（毫秒）
  spring.redis.timeout=1800000
  #连接池最大连接数（使用负值表示没有限制）
  spring.redis.lettuce.pool.max-active=20
  #最大阻塞等待时间(负数表示没限制)
  spring.redis.lettuce.pool.max-wait=-1
  #连接池中的最大空闲连接
  spring.redis.lettuce.pool.max-idle=5
  #连接池中的最小空闲连接
  spring.redis.lettuce.pool.min-idle=0
  ```

* 添加Redis配置类

  ```java
  @EnableCaching
  @Configuration
  public class RedisConfig extends CachingConfigurerSupport {
  
      @Bean
      public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
          RedisTemplate<String, Object> template = new RedisTemplate<>();
          RedisSerializer<String> redisSerializer = new StringRedisSerializer();
          Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
          ObjectMapper om = new ObjectMapper();
          om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
          om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
          jackson2JsonRedisSerializer.setObjectMapper(om);
          template.setConnectionFactory(factory);
  //key序列化方式
          template.setKeySerializer(redisSerializer);
  //value序列化
          template.setValueSerializer(jackson2JsonRedisSerializer);
  //value hashmap序列化
          template.setHashValueSerializer(jackson2JsonRedisSerializer);
          return template;
      }
  
      @Bean
      public CacheManager cacheManager(RedisConnectionFactory factory) {
          RedisSerializer<String> redisSerializer = new StringRedisSerializer();
          Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
  //解决查询缓存转换异常的问题
          ObjectMapper om = new ObjectMapper();
          om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
          om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
          jackson2JsonRedisSerializer.setObjectMapper(om);
  // 配置序列化（解决乱码的问题）,过期时间600秒
          RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
                  .entryTtl(Duration.ofSeconds(600))
                  .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer))
                  .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))
                  .disableCachingNullValues();
          RedisCacheManager cacheManager = RedisCacheManager.builder(factory)
                  .cacheDefaults(config)
                  .build();
          return cacheManager;
      }
  }
  
  ```

# 事务

> Redis事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。
>
> Redis事务的主要作用就是**串联多个命令**防止别的命令插队。

## multi、exec、discard

> 从输入multi命令开始，输入的命令都会依次进入命令队列中，但不会执行，直到输入exec后，Redis会将之前的命令队列中的命令依次执行

![](.\img\110.jpg)

## 事务的错误处理

1. 组队中某个命令出现了报错，执行时整个的所有队列都会被取消

   <img src=".\img\111.jpg" style="zoom:150%;" />

2. **组队成功后，执行时只有报错的命令不会被执行，而其他命令都会执行，不会回滚**

   <img src=".\img\112.jpg" style="zoom:150%;" />

## 事务冲突的问题

### 悲观锁

> **悲观锁(Pessimistic Lock)**, 顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。**传统的关系型数据库里边就用到了很多这种锁机制**，比如**行锁**，**表锁**等，**读锁**，**写锁**等，都是在做操作之前先上锁

### 乐观锁

> **乐观锁(Optimistic Lock),** 顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。**乐观锁适用于多读的应用类型，这样可以提高吞吐量**。Redis就是利用这种check-and-set机制实现事务的

### 监听键变化

* 在执行multi之前，先执行监听key，如果在事务执行之前这些key被其他命令所改动，那么事务将被打断
  * `watch <key> [key...]`
* 取消watch命令对所有key的监听
  * `unwatch`

## 事务三特性

1. 单独的隔离操作
   * 事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断
2. 没有隔离级别的概念
   * 队列中的命令没有提交之前都不会实际被执行，因为事务提交前任何指令都不会被实际执行

3. 不保证原子性
   * 事务中如果有一条命令执行失败，其后的命令任然会被执行，没有回滚

# 持久化

## RDB(Redis DataBase)

1. 介绍

   > 在指定的**时间间隔**内将内存中的数据集**快照**写入磁盘(**dump.rdb**)， 也就是行话讲的Snapshot快照，它恢复时是将快照文件直接读到内存里

2. 备份如何执行的

   > Redis会单独创建（fork）一个子进程来进行持久化，会**先**将数据**写**入到 一个**临时文件**中，待持久化过程都结束了，再用这个**临时文件替换上次持久化好的文件**。 整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能 如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。**RDB的缺点是最后一次持久化后的数据可能丢失**。

3. Fork

   > 1. Fork的作用是复制一个与当前进程**一样的进程**。新进程的所有数据（变量、环境变量、程序计数器等） 数值都和原进程一致，但是是一个全新的进程，并**作为原进程的子进程**
   > 2. 在Linux程序中，Fork()会产生一个和父进程完全相同的子进程，但子进程在此后多会exec系统调用，出于效率考虑，Linux中引入了“**写时复制技术**”
   > 3. **一般情况父进程和子进程会共用同一段物理内存**，只有进程空间的各段的内容要发生变化时，才会将父进程的内容复制一份给子进程

4. RDB持久化流程

   ![](.\img\113.jpg)

5. redis.conf配置文件

   ```xml
   # rdb文件的保存路径，也可以修改
   dir ./
   # 触发rdb快照策略 [save 秒钟 写操作次数]
   save 3600 1
   save 30 10
   save 60 10000
   # 当Redis无法写入磁盘的话，直接关掉Redis的写操作  推荐yes
   stop-writes-on-bgsave-error yes
   # 对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，Redis会采用LZF算法进行压缩
   rdbcompression yes
   # 检查完整性
   rdbchecksum yes
   ```

6. RDB恢复

   > dump.rdb文件，启动Redis,备份数据会直接加载

7. 优势

   > 适合大规模的数据恢复
   >
   > 对数据完整性和一致性要求不高更合适使用
   >
   > 节省磁盘空间
   >
   > 恢复速度快

8. 劣势

   > Fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑
   >
   > 虽然Redis在Frok时使用了写时拷贝技术，但是如果数据庞大时还是比较消耗性能
   >
   > 在备份周期在一定间隔时间做一次备份，所以如果Redis意外挂掉的话，就会丢失最后一次快照后的所有修改

9. 总结

   ![](.\img\114.jpg)

   * RDB是一个非常紧凑的文件
   * RDB在保存RDB文件时父进程唯一需要做的就是Fork出一个子进程，接下来的工作全部由子进程来做，父进程不需要再做其他IO操作，所以RDB持久化方式可以最大化Redis的性能
   * 与AOF相比，在恢复大的数据集的时候，RDB方式会更快一些
   * 数据丢失风险大
   * RDB需要经常Fork子进程来保存数据集到硬盘上，当数据集比较大的时候，fork的过程时非常耗时的，可能导致Redis在一些毫秒级不能相应客户端请求

## AOF(Append Only File)

1. 介绍

   > **以日志的形式来记录每个写操作（增量保存）**，将Redis执行过的所有写指令记录下来(**读操作不记录**)， **只许追加文件但不可以改写文件**，redis启动之初会读取该文件重新构建数据，换言之，redis 重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作

2. 持久化流程

   1. 客户端的请求写命令会被append追加到AOF缓冲区内
   2. AOF缓冲区根据AOF持久化策略[always,everysec,no]将操作sync同步到磁盘的AOF文件中
   3. AOF文件大小超过重写策略或手动重写时，会对AOF文件rewrite重写，压缩AOF文件容量
   4. Redis服务重启时，会重新加载AOF文件中的写操作达到数据恢复的目的

   ![](.\img\115.jpg)

3. AOF默认不开启
   * **在redis.conf配置文件中，将appendonly no改为yes开启**
   * AOF文件的保存路径，同RDB的路径一致

4. **AOF和RDB同时开启，Redis默认读取AOF的数据**

5. AOF启动、恢复、修复

   * **AOF的备份机制和性能虽然和RDB不同, 但是备份和恢复的操作同RDB一样，都是拷贝备份文件，需要恢复时再拷贝到Redis工作目录下，启动系统即加载**

   1. 正常恢复
      * 将aof文件保存到对应目录
      * 重启redis自动加载
   2. 异常恢复
      * **如遇到AOF文件损坏，通过`redis-check-aof --fix appendonly.aof`进行恢复**
      * 重启redis自动加载

6. AOF同步频率设置

   1. appendfsync always
      * **始终同步，每次Redis的写入都会立刻记入日志**；性能较差但数据完整性比较好
   2. appendfsync everysec
      * **每秒同步，每秒记入日志一次，如果宕机，本秒的数据可能丢失**
   3. appendfsync no
      * redis不主动进行同步，把**同步时机交给操作系统**

7. Rewrite压缩

   > AOF采用文件追加方式，文件会越来越大为避免出现此种情况，新增了重写机制, 当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩， **只保留可以恢复数据的最小指令集**

   1. 重写原理

      > AOF文件持续增长而过大时，会Fork出一条新进程来将文件重写(也是先写临时文件最后再rename)，**redis4.0版本后的重写，是指把rdb 的快照，以二级制的形式附在新的aof头部，作为已有的历史数据，替换掉原来的流水账操作**

      * 如果 no-appendfsync-on-rewrite=yes ,不写入aof文件只写入缓存，用户请求不会阻塞，但是在这段时间如果宕机会丢失这段时间的缓存数据。（降低数据安全性，提高性能）
      * 如果 no-appendfsync-on-rewrite=no, 还是会把数据往磁盘里刷，但是遇到重写操作，可能会发生阻塞。（数据安全，但是性能降低）

   2. 触发机制，何时重写

      * Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的**一倍(128M)**且文件大于64M时触发
      * 重写虽然可以节约大量磁盘空间，减少恢复时间。但是每次重写还是有一定的负担的，因此设定Redis要满足一定条件才会进行重写
      * auto-aof-rewrite-percentage：设置重写的基准值，文件达到100%时开始重写（文件是原来重写后文件的2倍时触发）
      * auto-aof-rewrite-min-size：设置重写的基准值，最小文件64MB。达到这个值开始重写

   3. 重写流程

      1. bgrewriteaof触发重写，判断是否当前有bgsave或bgrewriteaof在运行，如果有，则等待该命令结束后再继续执行

      2. 主进程fork出子进程执行重写操作，保证主进程不会阻塞

      3. 子进程遍历redis内存中数据到临时文件，客户端的写请求同时写入aof_buf缓冲区和aof_rewrite_buf重写缓冲区保证原AOF文件完整以及新AOF文件生成期间的新的数据修改动作不会丢失

      4. 子进程写完新的AOF文件后，向主进程发信号，父进程更新统计信息；主进程把aof_rewrite_buf中的数据写入到新的AOF文件

      5. 使用新的AOF文件覆盖旧的AOF文件，完成AOF重写

         ![](D:\Notes\img\116.jpg)



8. 优势

   ![](D:\Notes\img\117.jpg)

   * 备份机制更稳健，丢失数据概率更低
   * 可读的日志文本，通过操作AOF稳健，可以处理误操作

9. 劣势

   * 比起RDB占用更多的磁盘空间
   * 恢复备份速度要慢
   * 每次读写都同步的话，有一定的性能压力
   * 存在个别Bug，造成恢复不能

10. 总结

    <img src="D:\Notes\img\118.jpg" style="zoom:150%;" />

## 总结

> 官方推荐两个都启用
>
> 如果对数据不敏感，可以选单独用RDB。
>
> 不建议单独用 AOF，因为可能会出现Bug。
>
> 如果只是做纯内存缓存，可以都不用

1. RDB持久化方式能够在指定的时间间隔能对你的数据进行快照存储
2. AOF持久化方式记录每次对服务器写的操作,当服务器重启的时候会重新执行这些命令来恢复原始的数据,AOF命令以redis协议追加保存每次写的操作到文件末尾
3. Redis还能对AOF文件进行后台重写,使得AOF文件的体积不至于过大
4. 只做缓存：如果你只希望你的数据在服务器运行的时候存在,你也可以不使用任何持久化方式
5. 同时开启两种持久化方式
   1. 在这种情况下,当redis重启的时候会优先载入AOF文件来恢复原始的数据, 因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整
   2. RDB的数据不实时，同时使用两者时服务器重启也只会找AOF文件。那要不要只使用AOF呢？
   3. 建议不要，因为RDB更适合用于备份数据库(AOF在不断变化不好备份)， 快速重启，而且不会有AOF可能潜在的bug，留着作为一个万一的手段

# 主从复制

> 主机数据更新后根据配置和策略， 自动同步到备机的**master/slaver机制，Master以写为主，Slave以读为主**
>
> 读写分离，性能扩展
>
> 容灾快速恢复

<img src=".\img\119.jpg" style="zoom: 200%;" />

## 搭建主从复制

1. 启动三台Redis服务器
2. 进入客户端查看状态`info replication`
3. **配从(库)不配主(库)**，在从服务器上关联主服务器
   * `slaveof host port`

## 三种模式

1. 一主二从

   > 当从服务器挂掉后，再次启动会主动向主服务器发送同步数据消息，主服务器收到消息后，会先进行持久化操作，再把RDB文件发送给从服务器进行同步
   >
   > 当主服务器挂掉后，从服务器**原地待命**，等待主服务器重新启动

2. 薪火相传

   > **上一个Slave可以是下一个slave的Master**，Slave同样可以接收其他 Slaves的连接和同步请求，那么该Slave作为了链条中下一个的master, 可以有效减轻master的写压力,去中心化降低风险
   >
   > 中途变更转向:会清除之前的数据，重新建立拷贝最新的
   >
   > 风险是一旦某个slave宕机，后面的slave都没法备份
   >
   > 主机挂了，从机还是从机，无法写数据了

3. 反客为主

   > 当一个master宕机后，后面的slave可以立刻升为master，其后面的slave不用做任何修改
   >
   > 用`salveof no one`将从机变为主机

## 复制原理

> 1. Slave启动成功连接到Master后会发送一个sync命令
> 2. Master接到命令后启动后台存盘进程，同时手上所有接收到的用于修改数据集命令，在后台进程完毕之后，Master将传送整个数据文件到Salve ，以完成一次完全同步
>
> * 全量复制：而Slave服务在接收到数据库文件数据后，将其存盘并加载到内存中
> * 增量复制：Master继续将新的所有手机到的修改命令依次传给Salve,完成同步
> * 但是只要是重新连接Master，一次完全同步(全量复制)将被自动执行

<img src=".\img\120.jpg" style="zoom:200%;" />

## 哨兵模式

> **反客为主的自动版**，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库

<img src=".\img\121.jpg" style="zoom:200%;" />

1. 调整为一主二从模式

2. 配置哨兵

   ```
   sentinel monitor mymaster 127.0.0.1 6379 1
   ```

   * 其中mymster为监控对象起的服务器名称，**1为至少有多少个哨兵同意迁移的数量**

3. 开启哨兵模式：`redis-sentinel /usr/local/redis/sentinel.conf`

4. 当主机挂掉，从机选举中产生新的主机；根据**replica-priority**值越小优先级越高
5. 原主机重启后变为从机

### 复制延时

> 由于所有的写操作都是先在Master上操作，然后同步更新到Slave上，所以从Master同步到Slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，Slave机器数量的增加也会使这个问题更加严重

### 故障恢复

<img src=".\img\122.jpg" style="zoom:150%;" />

# 集群

> Redis 集群实现了对Redis的水平扩容，即启动N个Redis节点，将整个数据库分布存储在这N个节点中，每个节点存储总数据的1/N
>
> Redis 集群通过分区（partition）来提供一定程度的可用性（availability）： 即使集群中有一部分节点失效或者无法进行通讯， 集群也可以继续处理命令请求
>
> Redis集群采用P2P模式，是完全去中心化的，不存在中心节点或者代理节点
>
> Redis集群是没有统一的入口的，客户端（client）连接集群的时候连接集群中的任意节点（node）即可，集群内部的节点是相互通信的（PING-PONG机制），每个节点都是一个redis实例
>
> 为了实现集群的高可用，即判断节点是否健康（能否正常使用），redis-cluster有这么一个投票容错机制：如果集群中超过半数的节点投票认为某个节点挂了，那么这个节点就挂了（fail）。这是判断节点是否挂了的方法
>
> **16384 slots covered**

## 集群搭建

> **Redis集群至少需要3个节点，因为投票容错机制要求超过半数节点认为某个节点挂了该节点才是挂了，所以2个节点无法构成集群。要保证集群的高可用，需要每个节点都有从节点，也就是备份节点，所以Redis集群至少需要6台服务器**

1. 修改redis.conf文件(六台服务器都要修改)

   ```properties
   # 打开集群模式
   cluster-enabled yes
   # 设定节点配置文件名
   cluster-config-file nodes-6379.conf
   # 设定节点失联时间，超过该时间（毫秒），集群自动进行主从切换
   cluster-node-timeout 15000
   ```

2. 分别启动六台服务器

3. 创建Redis集群

   1. 进入redis/src目录下

   2. 执行`redis-cli --cluster create --cluster-replicas 1 192.168.124.130:6379 192.168.124.131:6379 192.168.124.132:6379 192.168.124.133:6379 192.168.124.134:6379 192.168.124.135:6379 `

      * **-replicas 1 采用最简单的方式配置集群，一台主机，一台从机，正好三组**

      ![](.\img\123.jpg)

4. 进入客户端`redis-cli -c`

5. 查看集群状态`cluster nodes`

   ![](.\img\124.jpg)

## Solts

> 一个Redis集群包含16384个插槽(hash slot)，数据库中的每个键都属于这16384个插槽的其中一个
>
> 集群使用公式 CRC16(key) % 16384 来计算键 key 属于哪个槽， 其中 CRC16(key) 语句用于计算键 key 的 CRC16 校验和

* 在集群中录入值

  > Redis每次录入、查询键值，redis都会计算出该key应该送往的插槽，如果不是该客户端对应服务器的插槽，redis会报错，并告知应前往的redis实例地址和端口。
  >
  > redis-cli客户端提供了 –c 参数实现自动重定向

* 通过**{}**来定义组的概念，从而使key中{}内相同内容的键值对放到一个slot中去
  
  * `mset k1{cust} v1 k2{cust} v2 ...`
* 查询集群中的值
  
  * `cluster keyslot <key>`
* 返回count个slot槽中的键
  
  * `cluster countkeysinslot number`

## 故障恢复

> 如果某个主节点下线，从节点自动升为主节点
>
> 原主节点上线后变为从节点

## 集群的Jedis

> 即使连接的不是主机，集群会自动切换主机内存。主机写，从机读
>
> 无中心化主从集群，无论从那台主机写的数据，其他主机上都能读到数据

```java
public class JedisClusterTest {
    public static void main(String[] args) {
        //创建对象
        HostAndPort hostAndPort = new HostAndPort("192.168.124.130", 6379);
        JedisCluster jedisCluster = new JedisCluster(hostAndPort);
        //元素操作
        jedisCluster.set("k1", "v1");

        String k1 = jedisCluster.get("k1");
        System.out.println(k1);

        jedisCluster.close();
    }
}
```

## 集群的优缺点

* 优点
  * 实现扩容
  * 分摊压力
  * 务中心配置相对简单
* 缺点
  * 多键操作是不被支持的
  * 多键的Redis事务是不被支持的。lua脚本不被支持
  * 由于集群方案出现较晚，很多公司已经采用了其他的集群方法，而代理或者客户端分片的方案想要迁移至RedisCluster，需要整体迁移而不是逐步过渡，复杂度较大

# 应用问题解决

## 缓存穿透

* 问题描述

  > key对应的数据在数据源并不存在，每次针对此key的请求从缓存获取不到，请求都会压到数据源，从而可能压垮数据源。比如用一个不存在的用户id获取用户信息，不论缓存还是数据库都没有，若黑客利用此漏洞进行攻击可能压垮数据库。

  ![](.\img\125.jpg)

* 解决方案

  > 一个一定不存在缓存及查询不到的数据，由于缓存是不命中时被动写的，并且出于容错考虑，如果从存储层查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到存储层去查询，失去了缓存的意义

  1. **对空值缓存：**如果一个查询返回的数据为空（不管是数据是否不存在），我们仍然把这个空结果（null）进行缓存，设置空结果的过期时间会很短，最长不超过五分钟
  2. **设置可访问的名单(白名单)**：使用bitmaps类型定义一个可以访问的名单，名单id作为bitmaps的偏移量，每次访问和bitmap里面的id进行比较，如果访问id不在bitmaps里面，进行拦截，不允许访问
  3. **采用布隆过滤器**：(布隆过滤器（Bloom Filter）是1970年由布隆提出的。它实际上是一个很长的二进制向量(位图)和一系列随机映射函数（哈希函数）；布隆过滤器可以用于检索一个元素是否在一个集合中。它的优点是空间效率和查询时间都远远超过一般的算法，缺点是有一定的误识别率和删除困难。将所有可能存在的数据哈希到一个足够大的bitmaps中，一个一定不存在的数据会被 这个bitmaps拦截掉，从而避免了对底层存储系统的查询压力
  4. **进行实时监控**：当发现Redis的命中率开始急速降低，需要排查访问对象和访问的数据，和运维人员配合，可以设置黑名单限制服务

## 缓存击穿

* 问题描述

  > key对应的数据存在，但在redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮

  ![](.\img\126.jpg)

* 解决方案

  > key可能会在某些时间点被超高并发地访问，是一种非常“热点”的数据。这个时候，需要考虑一个问题：缓存被“击穿”的问题

  1. **预先设置热门数据**：在Redis高峰访问之前，把一些热门数据提前存入到Redis里面，加大这些热门数据key的时长

  2. **实时调整**：现场监控那些数据热门，实时调整key的过期时长

  3. **使用锁**：

     1. 在缓存失效的时候(判断拿出来的值为空)，不是立即去load db
     2. 先使用缓存工具的某些带成功操作返回值的操作(比如Redis的setnx)去set一个mutex key
     3. 当操作返回成功时，再进行load db的操作，并回设缓存，最后删除mutex key
     4. 当操作返回失败，证明有线程在load db，当前线程睡眠一段时间再重试整个get缓存的方法

     <img src=".\img\127.jpg" style="zoom:150%;" />

## 缓存雪崩

* 问题描述

  > key对应的数据存在，但在redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮
  >
  > **缓存雪崩与缓存击穿的区别在于这里针对很多key缓存，前者则是某一个key正常访问**

  ![](.\img\128.jpg)

* 解决方案
  1. **构建多级缓存架构**：nginx缓存+redis缓存+其他缓存
  2. **使用锁或队列**：用加锁或者队列的方式保证来保证不会有大量的线程对数据库一次性进行读写，从而避免失效时大量的并发请求落到底层存储系统上。不适用高并发情况
  3. **设置过期标志更新缓存**：记录缓存数据是否过期（设置提前量），如果过期会触发通知另外的线程在后台去更新实际key的缓存
  4. **将缓存失效时间分散开**：可以在原有的失效时间基础上增加一个随机值，比如1-5分钟随机，这样每一个缓存的过期时间的重复率就会降低，就很难引发集体失效的事件

## 分布式锁

> 随着业务发展的需要，原单体单机部署的系统被演化成分布式集群系统后，由于分布式系统多线程、多进程并且分布在不同机器上，这将使原单机部署情况下的并发控制锁策略失效，单纯的Java API并不能提供分布式锁的能力。为了解决这个问题就需要一种跨JVM的互斥机制来控制共享资源的访问，这就是分布式锁要解决的问题

* 设置锁的同时设置过期时间
  * `set <key> <value> nx ex seconds`

### 代码实现

```java
//1 声明一个uuid ,将做为一个value 放入我们的key所对应的值中
String uuid = UUID.randomUUID().toString();
//2 定义一个锁：lua 脚本可以使用同一把锁，来实现删除！
String skuId = "25"; // 访问skuId 为25号的商品 100008348542
String locKey = "lock:" + skuId; // 锁住的是每个商品的数据

// 3 获取锁
Boolean lock = redisTemplate.opsForValue().setIfAbsent(locKey, uuid, 3, TimeUnit.SECONDS);

// 第一种： lock 与过期时间中间不写任何的代码。
// redisTemplate.expire("lock",10, TimeUnit.SECONDS);//设置过期时间
// 如果true
if (lock) {
    // 执行的业务逻辑开始
    // 获取缓存中的num 数据
    Object value = redisTemplate.opsForValue().get("num");
    // 如果是空直接返回
    if (StringUtils.isEmpty(value)) {
        return;
    }
    // 不是空 如果说在这出现了异常！ 那么delete 就删除失败！ 也就是说锁永远存在！
    int num = Integer.parseInt(value + "");
    // 使num 每次+1 放入缓存
    redisTemplate.opsForValue().set("num", String.valueOf(++num));
    /*使用lua脚本来锁*/
    // 定义lua 脚本
    String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
    // 使用redis执行lua执行
    DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>();
    redisScript.setScriptText(script);
    // 设置一下返回值类型 为Long
    // 因为删除判断的时候，返回的0,给其封装为数据类型。如果不封装那么默认返回String 类型，
    // 那么返回字符串与0 会有发生错误。
    redisScript.setResultType(Long.class);
    // 第一个要是script 脚本 ，第二个需要判断的key，第三个就是key所对应的值。
    redisTemplate.execute(redisScript, Arrays.asList(locKey), uuid);
} else {
    // 其他线程等待
    try {
        // 睡眠
        Thread.sleep(1000);
        // 睡醒了之后，调用方法。
        testLockLua();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```

### 总结

为了确保分布式锁可用，我们至少要确保锁的实现同时**满足以下四个条件**

1. 互斥性。在任意时刻，只有一个客户端能持有锁
2. 不会发生死锁。即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁
3. 解铃还须系铃人。加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了
4. 加锁和解锁必须具有原子性

# 新功能

## ACL

> Redis ACL是Access Control List（访问控制列表）的缩写，该功能允许根据可以执行的命令和可以访问的键来限制某些连接
>
> Redis 6 则提供ACL的功能对用户进行更细粒度的权限控制
>
> 1. 接入权限：用户名和密码
> 2. 可以执行的命令
> 3. 可以操作的KEY

### 命令

* 展现用户权限列表

  * `acl list`

    ![](.\img\129.jpg)

* 查看添加权限指令类别
  
* `acl cat [options]`
  
* 查看当前用户
  
  * `acl whoami`
* 新建用户
  
* `acl setuser username on >password acl权限`
  
* 切换用户
  
  * `auth username password`

