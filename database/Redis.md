# Redis

## 一、NOSQL

- 解决性能问题

### 1.1 session问题

- 存储到客户端（安全性问题）
- session复制（浪费空间）
- noSql数据库
  - 无需IO

### 1.2 IO问题

- 缓存数据库

### 1.3 概述

- NoSQL = Not Only SQL 泛指**非关系型数据库**
- 不依赖于业务逻辑方式存储，使用**key-value**模式存储
- 特点：
  - 不遵循SQL标准
  - 不支持ACID（事务的四个特性，但支持事务）
  - 远超于SQL的性能

### 1.4 NoSQL适用场景

- 对数据高并发的读写
- 海量数据的读写
- 对数据高可扩展的

### 1.5 NoSQL不适用场景

- 需要事务支持
- 基于sql的结构化查询储存，处理复杂的关系，需要**即席**查询
- 用不着sql的和用了sql也不行的情况，考虑NoSql

### 1.6 常见NoSQL数据库

- Memcache

  - ![image-20211025211557630](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211025211557630.png)

- Redis

  - ![image-20211025211608198](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211025211608198.png)

  - 注：持久化：是否存储到硬盘

- MongoDB

  - ![image-20211025211648882](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211025211648882.png)

### 1.7 行式存储数据库（大数据时代）

#### 1.7.1 行式数据库

![image-20211025212007987](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211025212007987.png)

#### 1.7.2 列式数据库

![image-20211025212041547](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211025212041547.png)

## 二、Redis简介

- Redis是一个开源的key-value存储系统
- 它还支持除key-value等很多类型
- 支持原子性
- Redis支持各种不同方式的排序
- 数据存储在内存中，还会周期性的写入磁盘
- master-slave（主从）同步

### 2.1 应用场景

#### 2.1.1 配合关系数据库做高速缓存

![image-20211025212558090](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211025212558090.png)

#### 2.1.2 多样的数据结构存储持久化数据

![image-20211025212620382](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211025212620382.png)

### 2.2 安装

#### 2.2.1 后台启动设置

- 修改redis.conf文件中的daemonize no改为yes
- `redis-server ./redis.conf`

#### 2.2.2 关闭

- 在cli中使用shutdown
- kill -9

### 2.3 Redis相关知识

#### 2.3.1 6379端口

- Alessia **Merz** 用6379可以打出

#### 2.3.2 常用知识

- 默认16个数据库，从0开始，默认使用0号库
- `select <dbid>`切换数据库，如`select 8`
- 统一密码，所有库密码均相同

#### 2.3.2 单线程+多路IO复用

- 串行 vs 多线程+锁 vs 单线程+多路IO复用

![image-20211025213950192](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211025213950192.png)

## 三、Redis常用五大数据类型

- 字符串		String
- 列表            List
- 集合            Set
- 哈希            Hash
- 有序集合    Zset

### 3.1 Redis-Key

- `set key value`增加key-value
- `keys *`查看当前库所有key（key*1）
- `exists key`判断某个key是否存在
- `type key`查看你的key是什么类型
- `del key`删除指定key数据
- <font color="red">`unlink key`根据value选择非阻塞删除</font>：仅将keys从keyspace元数据中删除，真正的删除会在后续异步操作
- `expire key 10`10秒种：给指定的key设置过期事件
- `ttl key`查看还有多少秒过期，-1表示永不过期，-2表示已过期
- `dbsize`查看当前数据库的key的数量
- `flushdb`清空当前库
- `flushall`通杀全部库

### 3.2 Redis-String

- 一个key对应一个value
- 二进制安全
- 一个Redis中字符串value最多可以是512M

#### 3.2.1 常用命令

- `set key value`添加键值对（若key相同则覆盖value）
  - `*NX`当key不存在，则可以将key-value添加
  - `*XX`当key存在时，则可以将key-value添加，与NX互斥
  - `*EX `    key的超时秒数
  - `*PX`    key的超时毫秒数，与EX互斥

- `get key`通过key取value
- `append key`将给定的value追加到原值的末尾
- `strlen key`获取value的长度
- `setnx key value`只有在key不存在时，设置key的值
- `incr key`将key中储存的数字值增1，只能对数字值操作，若为空则新增值为1
- `decr key`将key中储存的数字值减1，只能对数字值操作，若为空则新增值为1
- `incrby key int`增加自定义步长
- `decrby key int`减少自定义步长

#### 3.2.2 原子性

- 使用incr/decr时，将会出现原子性操作
- 指不会被线程调度机制打断
- 一旦开始，就一直运行到结束，中间不会有任何线程切换
- 单线程中，操作只能发生与指令之间
- 多线程中，操作可以被其他线程打断
- Redis单命令的原子性主要得益于Redis的单线程

- `mset key value key value`同时设置多个key-value
- `mget key value key value`同时获取多个value
- `msetnx key value key value`同时设置多个key-value，当且仅当给定的所有key都不存在
- 原子性：有一个失败则全部失败
- `getrange key start end`获取值的范围，类似于substring
- `setrange key start value`覆写从start开始的字符串，索引从0开始
- `setex key time value`设置键的同时设置过期时间，单位s
- `getset key value`以新换旧，设置新值的同时获得旧值

#### 3.2.3 数据结构-简单的动态字符串

- 内部结构类似于Java的ArrayList，采取预分配冗余空间的方式来减少内存的频繁分配

![image-20211026113758700](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211026113758700.png)

- 字符串长度小于1M时，扩容为加倍现有空间
- 字符串长度大衣1M时，扩容会扩1M空间
- 字符串最大长度为512M

### 3.3 Redis-List

#### 3.3.1 简介

- Redis列表是简单的字符串列表，按照插入顺序排序
- 你可以添加一个元素到头部或者尾部
- 底层为双向列表，对两端的操作性能很高，通过索引操作中间节点性能会很差

![image-20211026114350725](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211026114350725.png)

- 头尾相连

#### 3.3.2 常用命令

- `lpush/rpush key value value value`向列表左边/右边插入一个或多个值
- `lpop/rpop key`从左边/右边吐出一个值。<font color="red">值在键在，值光键亡</font>
- `rpoplpush key1 key2`从key1右边吐出一个值插入到key2左边
- `lrange key start end`按照索引下标获得元素（从左到右）
- `lindex key value`按照索引下标获得元素 
- `llen  key`获取列表长度
- `linsert key before/after value newvalue`在value前/后插入newvalue
- `lrem key n value`从左边删除n个value
- `lset key index value`将列表key下表为value的值替换为value

#### 3.3.3 数据结构-quickList

- 列表元素较少时，使用一块连续的内存，这个结构时ziplist（压缩列表）
- 数据量大时才会改成quicklist
  - 普通链表附加指针太大，Redis将链表和ziplist组合为量quicklist，将多个ziplist使用双向指针串联

![image-20211026145628896](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211026145628896.png)

### 3.4 Redis-Set

- 集合

#### 3.4.1 简介

- Redis-Set
- 无序集合，底层为一个value为null的hash表，所以添加、删除、查找的复杂度都是O(1)

#### 3.4.2 常用命令

- `sadd key value value`将一个或多个元素加入到集合的key中，已经存在的元素会被忽略
- `smembers key`取出所有值
- `sismember key value`判断集合中是否含有该value值，有1，没有则为0
- `scard key`返回该集合的所有元素个数
- `srem key value value`删除集合中的某个元素
- `spop key`随机吐出一个值
- `srandmember key n`随机从该集合中取出n个值，不会从集合中删除
- `smove source destination value`把集合中一个值从一个集合移动到另一个集合
- `sinter key key`返回交集
- `sunion key key`返回并集
- `sdiff key key`返回差集，key1有到，key2没有的

#### 3.4.3 数据结构

- Set数据结构时dict字典，字典是用hash表实现的
- 内部结构用的是hash结构，所有的value都指向同一个内部值

### 3.5 Redis-Hash

#### 3.5.1 简介

可以理解为key-key-value

- Redis hash是一个键值对集合
- Redis hash是一个String类型的field和value的映射表，hash特别适合用于存储对象
- 类似于Java里面的Map<String,Object>

- 有两种存储方式：

![image-20211026174140298](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211026174140298.png)

#### 3.5.2 常用命令

- `hset key field value`给key集合中的field键赋值value
- `hget key field`从key中的field取出value
- `hmset key1 value1 key2 value2 key3 value3`批量设置
- `hexists key field`查看哈希表key中给定的field是否存在
- `hkeys key`列出该hash集合所有的field
- `hvals key`列出该hash集合中所有value
- `hincrby key field increment`为哈希表key中的域field的值加上增量  1  -1
- `hsetnx key field value`当field不存在时赋值

#### 3.5.3 数据结构

- ziplist            压缩列表   长度较短 个数较少
- hashtable     哈希表

### 3.6 Redis-Zset

#### 3.6.1 简介

- 有序集合，没有重复元素的字符串集合
- 每个成员都关联了一个**评分**，这个评分从低到高的方式排序集合中的成员。
- **集合成员唯一，但评分可以重复**

#### 3.6.2 常用命令

- `zadd key score value score value`添加元素
- `zrange key start stop withscores`返回有序集key，下标在start-stop之间，带WITHSCORES会一同返回score
- `zrangebyscore key min max [withscores] [limit offset count]`返回有序集key中所有score值介于min和max之间包括minmax的成员
- `zrevrangebyscore key min max [withscores] [limit offset count]`倒序返回
- `zincrby key increment value`为元素score加上增量
- `zrem key value`删除该集合，为指定集的元素
- `zcount key min max`统计该集合，分数区间内的元素个数
- `zrank key value`返回该值在集合中的排名，从0开始

#### 3.6.3 数据结构

- SortedSet(zset)是Redis提供的一个非常特别的数据结构，一方面它等于Java的数据结构Map<String,Double>,可以给每一个元素value赋予一个权重score
- 它还类似于TreeSet，内部的元素会按照权重score进行排序，可以得到每个元素的名次，还可以通过score的范围来获取元素的列表
- zset底层使用了两个数据结构
  - hash 作用是关联元素value和权重score，保障元素value的唯一性，可以通过元素value找到响应的score值
  - 跳跃表，目的在于给元素value排序，根据score的范围获取元素列表

#### 3.6.4 跳跃表

- 简介
  - 有序集合在生活中比较常见，根据对学生排名，根据得分对玩家排名
  - 对于有序集合的底层实现，可以用数组、平衡树、链表等
  - 数组不便插入、删除
  - 平衡树或红黑树虽然效率高但结构复杂
  - 链表查询遍历效率低
  - Redis使用的是跳跃表，效率堪比红黑树，实现比红黑树简单



![image-20211026215848177](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211026215848177.png)

![image-20211026220004274](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211026220004274.png)

- 即关键节点

## 四、Redis的发布和订阅

### 4.1 什么是发布和订阅

- 信息通信模式：发送者发送消息，订阅者接收消息
- Redis客户端可以订阅任意数量的频道

### 4.2 发布订阅命令行实现

- `subscribe channal`订阅频道
- `publish channel message`给channel发送message

![截屏2021-10-27 上午9.46.28](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-27%20%E4%B8%8A%E5%8D%889.46.28.png)

## 五、Redis新数据类型

### 5.1 Bitmaps

#### 5.1.1 简介

- 合理使用位操作
- Bitmaps本质不是数据类型，实际上是字符串（key-value），但它可以对字符串的位进行操作
- 可以吧Bitmaps看作一个以位为单位的数组，数组的每个单元只储存0和1，下标称为偏移量

#### 5.1.2 命令

- `setbit key offset value`
  - offset 偏移量
  - ![image-20211027095548738](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211027095548738.png)

![截屏2021-10-27 上午9.56.26](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-27%20%E4%B8%8A%E5%8D%889.56.26.png)

![截屏2021-10-27 上午9.56.48](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-27%20%E4%B8%8A%E5%8D%889.56.48.png)

- - 很多用户id以一个指定数字开头，直接将用户id和Bitmaps的偏移量对应势必会造成浪费分，通常是setbit时将用户id减去这个指定数字
  - 第一次初始化Bitmaps时，如果偏移量特别大，那么初始化将比较慢，可能会造成Redis阻塞

- `getbit key offset`获取某个偏移量的值
  - ![截屏2021-10-27 上午9.59.51](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-27%20%E4%B8%8A%E5%8D%889.59.51.png)

- `bitcount key`求和
  - 可加参数start end

- `bitop`与或非差
  - `bitop and/or/not/xor destkey key
  - ![image-20211027100424069](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211027100424069.png)

#### 5.1.3 Bitmaps和set对比

![image-20211027100542452](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211027100542452.png)

- 若存储内容少，则不建议使用Bitmaps

### 5.2 HyperLogLog

#### 5.2.1 简介

- 统计方案，去重
- 基数（非重复值）统计

#### 5.2.2 命令

- `pfadd key element`添加指定元素到HyperLogLog中,自动去重
- `pfcount key`计算HLL的近似基数
- `pfmerge destkey sourcekey`合并存储在一个HLL中

### 5.3 Geospatial

- 对GEO的支持，即地理信息的支持，2维坐标经纬度
- `geoadd key longitude latitude member`添加经度、纬度、名称，可同时增加多个
- `geopos key member`获取某个地区的坐标值
- `geodist key m1 m2 m`获取两个位置的直线距离(最后一位为单位m/km/ft/mi)
- `groradius key longitude latitude radius m`给定的经纬度为中心找出某一半径内的元素

## 六、Jedis操作Redis

- 类似于JDBC操作数据库

### 6.1 导入依赖

```xml
<!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.7.0-RC1</version>
</dependency>
```

### 6.2 链接Redis注意事项

- 防火墙	`systemctl stop/disable firewalld.service`
- 配置bind注释，保护模式改no

### 6.3 测试

```java
Jedis jedis = new Jedis("127.0.0.1",6379);
String ping = jedis.ping();
System.out.println(ping);
```

### 6.4 Jedis-API

- Jedis类支持所有的Redis方法

- ```java
  Jedis jedis = new Jedis("127.0.0.1",6379);
  Set<String> keys = jedis.keys("*");
  for (String key : keys) {
      System.out.println(key);
  }
  //输出 keys *
  ```

- set keys get tll exists mset 
- lrange
- sadd smembers srem
- hset hget hmset hmget
- zadd zrange

## 七、 手机验证码

### 7.1 需求

- 输入手机号，点击发送后随机生成一个6位数字码，2分钟内有效
- 输入验证码，点击验证，返回成功或者失败
- 每个手机号每天只能输入3次

### 7.2 实现

- 生成随机6位-Random
- 2分钟-Redis ttl 120s
- 验证
- 3次 incr

## 八、SpringBoot整合Redis

### 8.1 引入依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-redis -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-data-redis</artifactId>
   <version>2.5.6</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.apache.commons/commons-pool2 -->
<dependency>
   <groupId>org.apache.commons</groupId>
   <artifactId>commons-pool2</artifactId>
   <version>2.11.1</version>
</dependency>
```

### 8.2 配置文件

```yml
spring:
  redis:
    host: 127.0.0.1
    port: 6379
    database: 0
    timeout: 1800000
    lettuce:
      pool:
        #最大连接数，负值无限制
        max-active: 20
        #最大等待时间，负值无限制
        max-wait: -1
        #最大/小空闲连接
        max-idle: 5
        min-idle: 0
```

### 8.3 配置类

- 固定写法

```java
package com.example.springboot_redis;

import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.cache.CacheManager;
import org.springframework.cache.annotation.CachingConfigurerSupport;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.RedisSerializationContext;
import org.springframework.data.redis.serializer.RedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

import java.time.Duration;

/**
 * @author li
 */
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
    public CacheManager cacheManager(RedisConnectionFactory factory){
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        //解决查询缓存转换异常问题
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        //配置序列化（解决乱码问题），过期时间600s
        RedisCacheConfiguration configuration = RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofSeconds(600)).serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer)).serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer)).disableCachingNullValues();
        RedisCacheManager cacheManager = RedisCacheManager.builder(factory).cacheDefaults(configuration).build();
        return cacheManager;
    }

}

```

### 8.4 测试

```java
package com.example.springboot_redis;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/test")
public class TestController {

    @Autowired
    private StringRedisTemplate redisTemplate;

    @GetMapping("/testRedis")
    public String testRedis(){
        redisTemplate.opsForValue().set("test","abc");
        String test = redisTemplate.opsForValue().get("test");
        return test;
    }
}

//测试结果
abc
```

## 九、Redis 事务

### 9.1 定义

- Redis事务是一个单独的隔离操作：事务中的所有命令都会序列化，按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。
- Redis事务的主要作用就是**串联多个命令**防止别的命令操作

### 9.2 Multi、Exec、discard

![image-20211027191523835](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211027191523835.png)

- 从Multi开始，输入的命令会依次进入命令队列中，但不会执行，直到输入Exec后，Redis会将之前的命令队列中的命令依次执行
- 组队过程中可以使用discard放弃组队

![截屏2021-10-27 下午7.16.29](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-27%20%E4%B8%8B%E5%8D%887.16.29.png)

![截屏2021-10-27 下午7.17.14](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-27%20%E4%B8%8B%E5%8D%887.17.14.png)

### 9.3 事务的错误处理

- 某个命令出现了报告错误，执行时整个的所有队列都会被取消

![截屏2021-10-27 下午7.18.56](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-27%20%E4%B8%8B%E5%8D%887.18.56.png)

- 执行阶段某个命令出现了错误，只有错误的命令不会被执行，其他都会执行，而且不会回滚

### 9.4 事务的冲突问题

#### 9.4.1 悲观锁

- 锁对象被使用时其他人不能对其进行操作

#### 9.4.2 乐观锁

- 通过版本号确认自己操作的是不是对象现在的状态，进而防止出现冲突问题

![image-20211027192620317](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211027192620317.png)

#### 9.4.3 watch key [key..]

- 执行multi前，先执行watch key1 [key2]，可以监视一个或多个key，在事务执行前若这个key被其他命令改动，那么事务将被打断
- 终端1

![image-20211027193457038](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211027193457038.png)

- 终端2

![image-20211027193502250](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211027193502250.png)

- 相当于为key加了乐观锁

#### 9.4.4 unwatch

- 取消对key的监视

### 9.5 Redis事务三特性

- 单独的隔离性
  - 事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中不会被其他客户端发送来的命令请求打断。
- 没有隔离级别的概念
  - 队列中的命令没有提交之前都不会实际被执行，因为事务提交前任何指令都不会被实际执行
- 不保证原子性
  - exec阶段有命令执行失败，其后的命令会继续执行，并不会回滚

## 十、事务与锁-秒杀案例

### 10.1 需求

![image-20211027194050376](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211027194050376.png)

### 10.2 准备

- 结构

![image-20211027194328691](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211027194328691.png)

### 10.3 主要代码

```java
public boolean doSecKill(String uid,String prodid) throw IOException{
  //1. uid和prodid非空判断
  if(uid == null||prodid == null){
    return false;
  }
  //2. 连接redis
  Jedis jedis = new Jedis("127.0.0.1",6389);
  //3. 拼接key
  //3.1 库存key
  String kcKey = "sk:"+prodid + ":qt";
  //3.2 秒杀成功用户key
  String userKey = "sk:"prodid + ":user";
  //4. 获取库存，本身为空即为秒杀还未开始
 	String kc = jedit.get(kcKey);
  if(kc == null){
    System.out.println("秒杀还未开始");
    jedis.close();
    return false;
  }
  //5. 判断用户是否已经秒杀成功了
  if(jedis.sismember(userKey,uid)){
    System.out.println("已经秒杀过了");
    jedis.close();
   	return false;
  }
  //6. 如果库存=0，秒杀结束
  if(kc == 0){
    System.out.println("秒杀结束");
    jedis.close();
    return false;
  }
  //7. 秒杀过程
  //7.1 库存-1
  jedis.decr(kcKey);
  //7.2 秒杀成功用户的id添加到已秒杀成功列表中
  jedis.sadd(userKey,uid);
  System.out.println("秒杀成功");
  jedis.close();
  
  return true;
}
```

### 10.4 并发工具-ab工具

- 工具名：httpd-tools
- ab -n 1000 -c 100 -p ~/posetfile -T application/x-www-form-urlencoded 网址/
  - posetfile中加入参数 prodid=0101&
  - -n 请求到数量
  - -c 并发数量
  - -p 配置文件
  - -T content type

- 使用后存在结束后又成功的情况，库存-3
- 存在并发问题

### 10.5  解决并发问题

- 超卖，超时
- 超时：Redis不能同时处理过多请求，有部分请求就存在超时的可能

#### 10.5.1 连接池

- 将连接好的实例反复使用
- 通过参数管理连接的行为
- 工具类

```java
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

public class JedisPoolUtil {
    private static volatile JedisPool jedisPool = null;
    private JedisPoolUtil(){

    }

    public static JedisPool getJedisPool(){
        if (null == jedisPool){
            synchronized (JedisPoolUtil.class){
                if (null == jedisPool){
                    JedisPoolConfig poolConfig = new JedisPoolConfig();
                    poolConfig.setMaxTotal(200);
                    poolConfig.setMaxIdle(32);
                    poolConfig.setMaxWaitMillis(100*1000);
                    poolConfig.setBlockWhenExhausted(true);
                    poolConfig.setTestOnBorrow(true);

                    jedisPool = new JedisPool(poolConfig,"127.0.0.1",6379,60000);
                }
            }
        }
        return jedisPool;
    }

    public static void release(JedisPool jedisPool, Jedis jedis){
        if (null != jedis){
            jedisPool.returnResource(jedis);
        }
    }
}
```

- 连接jedis对象的位置改为

```java
JedisPool jedisPool = JedisPoolUtil.getJedisPool();
Jedis.jedis = jedisPool.getResource();
```

#### 10.5.2 超卖问题

- 通过乐观锁
- 在获取库存前增加监视库存

```java
jedis.watch(kcKey);
```

- 秒杀过程改为事务处理

```java
Transaction multi = jedis.multi();
//组队
multi.decr(kcKey);
multi.sadd(userKey,uid);
//执行
List<Object> results = multi.exec();
if(results == null || results.size() == 0){
  System.out.println("秒杀失败");
  jedis.close();
  return false;
}
```

### 10.6 库存遗留问题

#### 10.6.1 分析

- 乐观锁导致的库存遗留问题
- 乐观锁检测到版本号不同就会中断操作

#### 10.6.2 解决

- Lua脚本

![image-20211027202805095](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211027202805095.png)

- 可以实现类悲观锁

```lua
local userid=KEYS[1];
local prodid=KEYS[2];
local qtkey="sk:"..prodid..":qt";
local userskey="sk:"..prodid..":usr";
local userExists=redis.call("sismember",usersKey,userid);
if tonumber(userExists)==1 then
  return2;
end
local num=redis.call("get",qtkey);
if tonumber(nums)<=1 then
  return0;
else
  redis.call("decr",qtkey);
  redis.call("sadd",usersKey,userid);
end
return 1;
```

```java
String shal = jedis.scriptLoad(secKillScript);
Object result = jedis,evalsha(shal,2,userid,prodid);
```

![image-20211027203540806](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211027203540806.png)

## 十一、Redis持久化

- 写入磁盘即为持久化

- RDB    Redis DataBase
- AOF    Append Of File

### 11.1 RDB

#### 11.1.1 RDB简介

- 在指定的时间间隔将内存中的数据集快照写入磁盘，也就是Snapshot快照
- 恢复时将快照文件直接读入内存

#### 11.1.2 备份是如何进行的

- Redis会单独创建(fork)一个子进行来进行持久化，会先将数据写入到一个临时文件中，待持久化过程结束了，再用这个临时文件替换上次持久化好的文件
- 整个过程主进程不进行任何IO操作，这就保证了极高的性能，如果需要进行大量的数据恢复，对于数据完整性不是非常敏感，RDB的方式要比AOF方式更加高效。
- RDB的缺点是最后一次持久化后的数据可能会丢失
- ![截屏2021-10-28 下午3.21.39](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-28%20%E4%B8%8B%E5%8D%883.21.39.png)

#### 11.1.3 Fork

- Fork的作用是复制一个与当前进程一样的进程。
- 在Linux中，fork()会产生一个和父进程完全相同的字进程，但是子进程在此后多会exec系统调用，出于效率考虑Linux引入了“写时复制技术”

#### 11.1.4 dump.rdb文件

- 启动文件路径下会创建此文件

#### 11.1.5 配置文件：

- rdbchecksum检查完整性，会有大约10%的性能损耗 默认`rdbchecksum yes`
- save 秒   默认上1分钟修改1万次，或5分钟修改10次，或15分钟修改一次，禁用给save传入空字符串
- ![截屏2021-10-28 下午3.23.42](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-28%20%E4%B8%8B%E5%8D%883.23.42.png)
- bgsave:Redis在后台异步进行快照操作，快照同时还可以响应客户端请求
- 可以通过lastsave命令获取最后一次成功执行快照的时间

#### 11.1.6 RDB备份

#### 11.1.7 过程

- 先通过config get dir查询rdb文件的目录
- 将*.rdb的文件拷贝到别的地方
- 数据到达设定的临界值时，比如`save 20 3`(20s中有大于等于3条数据)

#### 11.1.8 优势

- 适合大规模的数据恢复
- 对数据完整性和一致性要求不高更适合使用
- 节约磁盘空间
- 恢复速度快



![截屏2021-10-28 下午3.27.32](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-28%20%E4%B8%8B%E5%8D%883.27.32.png)

#### 11.1.9 劣势

- Fork使用时将内存中的数据克隆了一份，空间消耗增加一倍
- 虽然Redis在fork时使用了写时拷贝技术，但是数据量庞大的时候还是会影响性能
- 备份为周期性，如果在关掉时没有到达周期备份，则自上个周期起的快照数据将会丢失

#### 11.1.10 停止

- redis-cli config set save ""动态停止RDB
- #save空值

### 11.2、AOF

#### 11.2.1 AOF简介

- Append Only File
- 顾名思义，以日志的形式来记录每个写操作（增量保存），将Redis执行过的所有写指令记录，读不计，只许追加文件但不可以改写文件，redis启动初会读取该文件重新构建数，换而言之，redis重启会根据日志文件从头到位执行数据恢复工作

#### 11.2.2 AOF持久化流程

- 客户端的请求会被apend追加到AOF缓冲区内
- AOF缓冲区根据AOF持久化策略[always,eversec,no]将操作sync同步到磁盘的AOF文件中
- AOF文件大小超过重写策略或手动重写时，会对AOF文件rewrite重写，压缩AOF文件容量
- Redis服务重启时，会重新load加载AOF文件中的写操作达到数据恢复的目的

#### 11.2.3 AOF开启

- AOF默认是不开启
- 配置文件默认为appendonly.aof
  - `appendonly yes`
  - `appendfilename "appendonly.aof"`

- AOF文件的保存路径同RDB的路径一致

#### 11.2.4 若RDB和AOF同时开启

- 若两者同时开启，此时Redis会遵循AOF，保证数据完整性

#### 11.2.5 AOF启动/修复/恢复

- AOF备份与RDB一样，拷贝文件-拷贝文件
- 正常恢复
  - 修改默认的appendonly no，改为yes
  - 拷贝aof
  - 拷贝回aof后重启Redis
- 异常恢复
  - 如遇到AOF文件损坏，使用`/usr/loacl/bin/redis-check-aox-fix appendonly.aof`恢复
  - 备份坏的AOF
  - 重启Redis

#### 11.2.6 AOF同步频率

- `appendfsync always`始终同步，对Redis的写入立刻计入日志，性能差，但数据完整性好
- `appendfsync everysec`每秒同步，如果宕机本秒数据可能丢失
- `appendfsync no`Redis不主动同步，把同步时机交由操作系统

#### 11.2.7 Rewrite 压缩

- 由于AOF使用的是append即追加操作，文件将越来越大，重写机制会在文件大小超过阈值时启动内容压缩，保留可以恢复数据的最小指令集，可以使用命令`bgrewriteaof`
- 原理：fork出一个新进程来将文件重写
  - redis4.0后的重写是将rdb的快照以二进制的形式附在aof头部，作为已有的历史数据，替换掉追加操作
  - `no-appendfsync-on-write`=no
  - `auto-aof-rewrite-percentage`设置重写基准值，文件达到100%开始重写。
  - `auto-aof-rewrite-min-size`设置重写基准值，最小为64M
  - 70-重写》50-》100 -重写〉    重写后会重新记录大小
  - 重写同样使用写时复制技术，fork
    - 遍历遍历redis内存数据到临时文件，同步写入缓冲区和重写缓冲区保证数据完整性
    - fork完成后发送信号给主进程，主进程更新信息，此时将缓冲区数据追加入新的aof
    - 覆盖原文件，完成重写

#### 11.2.8 优势

- 备份机制稳健，丢失数据概率更低
- 可读到日志文件，可以处理错误操作

#### 11.2.9 劣势

- 追加的文件占用空间更多
- 恢复备份的速度慢
- 每次写入都同步会产生性能压力
- 存在个别BUG，造成无法恢复

### 11.3 Which One

- 官方推荐两个都启用
- 对数据不敏感可以单独使用RDB
- 不建议单独用AOF，可能出现BUG
- 单纯内存缓存（非持久化）可以都不用

## 十二、主从复制

![image-20211029004232079](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211029004232079.png)

- 读写分离
- 容灾快速恢复
- 一主多从

### 12.1 一主两从

- 创建多个redis-server
- 复写conf
  - include 公共文件
  - pidfile redis.pid位置
  - port 6379
  - dbfilename dump6379.rdb
  - Appendonly 关掉或者换名字
  - //开启daemonize（守护进程）
- `info replication`查看主从机状态
- 从机执行`slaveof ip port`
  - ![截屏2021-10-29 上午1.09.38](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-29%20%E4%B8%8A%E5%8D%881.09.38.png)
  - ![截屏2021-10-29 上午1.10.04](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-29%20%E4%B8%8A%E5%8D%881.10.04.png)
  - 此时已经变为从机状态
  - 此时主机查看状态![截屏2021-10-29 上午1.08.50](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-29%20%E4%B8%8A%E5%8D%881.08.50.png)
  - 此时在主服务器中所有的写操作都会同步到从机中，而此时从机不能使用任何写操作
- 配置完成后如果有一台从服务器挂掉了，若重启，此时从服务器需要重新配置变为从服务器，且重新配置之后，主服务器会将现有的数据逐条同步给从服务器
- 若主服务器挂掉了，从服务器不会变主服务器，只是在状态内为down
- 

### 12.2 复制原理

- 从服务器连接上主服务器后，会向主服务器发送消息请求数据同步
- 主服务器接到请求后，会把数据持久化，然后将rdb文件发给从服务器
- 主服务器每次写操作后，会主动同步从服务器

### 12.3 薪火相传

- 类似于公司管理机制从服务器可以继续管理其他从服务器
- 若一个从服务器挂掉，则无法继续向下同步

### 12.4 反客为主

- 当一个master宕机后，后面直系的slave可以立即提升为master
- `slaveof no one`立刻变为主机

### 12.5 哨兵模式

- 反客为主的自动版，能够后台监视主机是否故障，如果故障则根据投票数自动将从库转换为主库

![image-20211029012723834](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211029012723834.png)

- 新建`sentinel.conf`文件
- 配置哨兵
  - `sentinel monitor mymaster ip port num`num表示至少多少个哨兵同意迁移
- 启动哨兵
  - redis自带的redis-benchmark工具`redis-sentinel conf文件路径`启动哨兵
  - 默认端口为26379
  - 会自动获取主机下的直系从机
- 工作状态
  - 当哨兵发现主机挂掉时，选择一台从机作为新的主服务器
  - 告知其他所有重服务器重新设置主服务器
  - 若此时原主服务器重启了，哨兵会发送`slaveof`命令，原主服务器会变为从服务器，新帝登基，老帝称臣
  - 存在复制延迟
- 优先级别（由高到低）
  - `slave-priority num`num为优先级（redis.config）值越小优先级越高
  - 偏移量：指获取原主机数据最全的
  - runid：redis实例启动后会随机生成一个40位的id，值越小优先级越高

- 原主机重启后会自动恢复为主机

## 十三、Redis集群

### 13.1 问题

- 容量不足，Redis应该如何扩容
- 并发写操作，Redis应该如何分担
- 主从模式，薪火相传，主机宕机导致的ip变化，应用程序需要修改ip，端口等
- 早期通过**代理主机**对方式解决
  - ![image-20211029144321241](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211029144321241.png)
- Redis3.0提供了方案，即**无中心化集群**配置
  - 集群可以把任意一服务器作为入口，上述效果可以实现使用6台服务器完成

### 13.2 什么是集群

- Redis集群实现了对Redis的水平扩容，启动N个redis节点，将数据分布在这N个节点中，每个节点存储1/N
- Redis通过分区来提供一定程序对可用性，即时集群有一部分节点无法进行通信，集群也可以继续处理命令请求

### 13.3 实现集群

- 使用6个Redis实例
- redis cluster 配置修改
  - `cluster-enabled yes`打开集群模式
  - `cluster-config-file xxx`设置配置文件
  - `cluster-node-timeout 15000`设定节点失联事件，超过该事件集群自动进行主从切换
  - ![image-20211029145649801](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211029145649801.png)
- 启动服务
- 集群启动
  - 此处使用真实ip
  - `redis-cli --cluster create --cluster -replicas 1 ip:prt...`
  - --replicas 1 最简单方式，一台主机一台从机
  - ![image-20211029151551195](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211029151551195.png)

- 连接 redis-cli -c -p xxx

## 十四、应用问题

- 缓存穿透
- 缓存击穿
- 缓存雪崩

### 14.1 缓存穿透

- 简介
  - key对应的数据源并不存在，每次针对此key的请求从缓存获取不到，请求会压到数据源，进而可能压垮数据源
  - ![image-20211029152411141](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211029152411141.png)
  - 应用服务器压力变大
  - redis命中率降低
  - 一直查询数据库
  - 缓存并没有使用到，直接把数据库干蹦掉
  - - redis查询不到数据库
    - 出现很多非正常url访问
- 解决方案
  - 一个一定不存在缓存及数据库的数据，由于缓存是不命中时被动写的，出于容错考虑，如果存储层查不到数据则不写入缓存，这将导致整个不存在的数据每次请求都要到存储层去查询，失去了缓存的意义
  - 解决：
    - 对空值缓存：如果一个查询返回的数据为空（不管数据是否不存在）。直接将空结果进行缓存，设置过期事件很短，如不超过5分钟
    - 设置可访问白名单：使用bitmaps类型可以定义一个访问名单，名单id作为bitmaps的偏移量，每次访问和bitmap里的id进行比较，如果id不再bitmaps里，进行拦截，不允许访问
    - 采用布隆过滤器：检索一个元素是否在一个集合中，空间效率和查询时间都远远高过一般算法，但可能出错，将所有可能存在的数据哈希到一个足够大的bitmaps中，一个一定不存在的数据会被这个bitmaps拦截到，从而避免了对底层数据存储系统的查询压力
    - 进行实时监控：当发现Redis的命中率开始急速降低，需要排查访问对象和访问的数据，配合运维人员设置黑名单阻止访问

### 14.2 缓存击穿

- 问题
  - key对应的数据存在，但是在redis中过期，若此时有大量请求，这些请求发现缓存过期一般会从后端数据库加载数据并回设到缓存，但这个时候大并发可能会把后端数据库压垮
  - ![image-20211029153700567](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211029153700567.png)
  - 访问压力瞬间增加
  - redis正常运行，但数据库已经崩溃了
- 解决方案
- - key可能在某些时间点被超高并发访问，即热点数据
  - 解决：
    - 预先设置热门数据：redis高峰访问前，把热门数据提取存入redis中，加大这些热门数据key点时长
    - 实时调整：现场监控哪些数据热门，实时调整key的过期时长
    - 使用锁：
      - 在缓存失效的时候不立即去load db
      - 使用缓存工具带成功操作返回值的操作，如Redis的SETNX去set一个mutex key
      - 当操作返回成功时，再进行load db操作，并回设缓存，最后删除mutex key
      - 当操作返回失败， 证明有线程在load db，当线程睡眠一段时间再重试整个get缓存的方法
  - ![image-20211029154307853](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211029154307853.png)

### 14.3 缓存雪崩

![image-20211029154438560](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211029154438560.png)

- 原理与缓存击穿一样，不同的是雪崩是有很多key缓存，而前者则是某一个key
  - 数据库压力变大，数据库崩溃
  - 在极少的时间端，出现大量key集中过期的情况

- 解决
  - 构建多级缓存架构：nginx缓存+redis缓存+其他缓存
  - 使用锁或队列：用枷锁或队列的方式保证不会有大量线程对数据库一次进行读写，从而避免失效时有大量并发请求落在数据库上，不适用于高并发情况
  - 设置过期标志更新缓存：记录缓存数据是否过期（设置提前量），如果过期会触发通知另外的线程在后台去更新实际key的缓存
  - 将缓存失效时间分散开：在原有失效时间的基础上增加一个随机值，这样每一个缓存过期的重复率就会降低，就很难引发集体失效的事件

### 14.4 分布式锁

- 单体单机部署的系统被演化成分布式集群系统，由于分布式系统多线程、多进程并且分布在不同机器上，这使得原单机部署情况下的并发控制锁策略失效，单纯的Java API并不能提供分布式锁的能力。JVM不能夸系统控制
- 分布式锁主流方案：
  - 基于数据库实现分布式锁
  - 基于缓存（Redis等）
  - 基于Zookeeper
- 各个分布式锁解决方案都有各自的优缺点
  - 性能：Redis最高
  - 可靠性：Zookeeper最高

#### 14.4.1 使用Redis实现分布式锁

- `SETEX`命令 key second value
  - 只有当此key释放之后才能重新对这个key操作
  - del释放
  - 有忘记释放的危险
  - 设置一个过期事件：eg：`expire key 10`10s过期
- `set key value nx ex times`上锁的同时设置过期事件

![image-20211030224612979](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211030224612979.png)

#### 14.4.2 springboot实现

```java
		@GetMapping("testLock")
    public void testLock() {
        boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111");
        if (lock) {
            Object value = redisTemplate.opsForValue().get("num");
            if (StringUtils.isEmpty(value)) {
                return;
            }
            int num = Integer.parseInt(value + "");
            redisTemplate.opsForValue().set("num", String.valueOf(++num));
            redisTemplate.delete("lock");
        } else {
            try{
                Thread.sleep(100);
                testLock();
            }catch (InterruptedException e){
                e.printStackTrace();
            }
        }
    }
```

- 测试
  - `ab -n 1000 -c 100 http://127.0.0.1:8080/test/testLock`

![截屏2021-10-30 下午10.41.44](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-30%20%E4%B8%8B%E5%8D%8810.41.44.png)

- 并发共1000次

![截屏2021-10-30 下午10.43.11](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-30%20%E4%B8%8B%E5%8D%8810.43.11.png)

- 设置key的过期

`boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111",3, TimeUnit.SECONDS);`

- 再次测试依旧正确，表示使用Redis构建分布式锁成功

#### 14.4.3 UUID防误删

![image-20211030224947860](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211030224947860.png)

- 使用UUID

![image-20211030225027535](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211030225027535.png)

![image-20211030225342844](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211030225342844.png)

- 局部代码

```java
String uuid = UUID.randomUUID().toString();
boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", uuid,3, TimeUnit.SECONDS);

if (redisTemplate.opsForValue().get("lock").equals(uuid)){
  redisTemplate.delete("lock");
}
```

#### 14.4.4 LUA保证原子性

- 删除操作缺乏原子性
  - 删除前到达过期时间
  - 删除了其他对象的锁

```java
String skuId = "25";
String locKey = "lock:" + skuId;
String script = "if redis.call('get',KEYS[1]) == ARGV[1] then return redis.call('del',KEYS[1]) else return 0 end";
DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>();
redisScript.setScriptText(script);
redisScript.setResultType(Long.class);
redisTemplate.execute(redisScript,Arrays.asList(locKey),uuid);
```

#### 14.4.5 保证锁可用

- 互斥性：在任一时刻，只有一个客户端能维持锁
- 不会发生死锁：即时有一个客户端在持有锁的期间崩溃没有释放锁，也能保证后续其他客户端正常加锁
- 解铃还需系铃人：加锁解锁必须是同一个客户端，不能把别的客户端的锁解除
- 原子性：加锁解锁原子性

## 十五、新特性

### 15.1 ACL

#### 15.1.1 简介

- Redis ACL Access Control List
- 该功能允许根据可以执行的命令和可访问的键来限制某些连接
- Redis5之前Redis的安全规则只有密码空值，还有通过rename来调整高危命令如flushdb，keys *，shutdown等，Redis为ACL功能对用户进行更精细的权限控制：
  - 接入权限：用户名和密码
  - 用户可执行命令
  - 用户可执行操作的key

#### 15.1.2 命令

- `acl list` 展现用户权限列表
  - ![截屏2021-10-29 下午3.57.43](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-29%20%E4%B8%8B%E5%8D%883.57.43.png)
  - 第二位：用户名
  - 第三位：是否启用密码
  - 第四位：密码
  - 第五位：可执行操作key
  - 最后一位：可执行命令
- `acl cat`查看添加权限指令级别
  - ![截屏2021-10-29 下午4.01.27](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-29%20%E4%B8%8B%E5%8D%884.01.27.png)
  - `acl cat xxx`具体部分
- `acl setuser`添加用户
  - ![截屏2021-10-29 下午4.02.56](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-29%20%E4%B8%8B%E5%8D%884.02.56.png)
  - ![截屏2021-10-29 下午4.03.39](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-10-29%20%E4%B8%8B%E5%8D%884.03.39.png)
  - off代表用户未启用
  - ![image-20211029160538892](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211029160538892.png)
  - 对get命令可用
- `acl whoami`查看当前用户
- `auth user password`切换用户

### 15.2 IO多线程

#### 15.2.1 简介

- Redis6支持多线程，但不是完全多线程
- IO多线程指客户端交互部分的网络IO交互处理模块多线程，而非执行命令多线程。Redis执行命令仍为单线程

#### 15.2.2 原理

- 仅用来处理网络数据读写和协议解析，执行命令仍为单线程
- 默认不开启
  - `io-threads-do-reads yes`
  - `io-threads 4`线程数量

### 15.3 工具支持cluster

- ruby环境，Redis5将redis-trib.rb功能集成到redis-cli
- redis-benchmark工具开始支持cluster模式，通过多线程对多个分片进行压测

### 15.4 Redis新功能持续关注

- RESP3通信协议：优化服务端与客户端之间的通信
- Client side caching客户端缓存，基于RESP3实现缓存功能，将数据cache到客户端，减少tcp交互
- Proxy集群代理模式：让Cluster拥有像单实例一样的介入方式，降低使用门槛
- Modules API：Redis可以变成一个框架，利用Modules构建不同的系统，不需要进行BSD许可
