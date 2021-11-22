# Kafka

## 一、概述

- kafka是一个分布式的基于发布、订阅模式的消息队列，主要应用于大数据实时处理领域

### 1.1 消息队列

- ![image-20211102191140432](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211102191140432.png)
- ![image-20211102191624597](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211102191624597.png)
- 好处
  - 解藕
  - 可恢复性
  - 缓冲
    - 生产>消费
  - 灵活&峰值处理能力
  - 异步通信

### 1.2 消息队列的两种模式

- 点对点模式
- 不可复用

![image-20211103211858554](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211103211858554.png)

- 发布/订阅模式

![image-20211103211942823](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211103211942823.png)

### 1.3 kafka基础架构

![image-20211103215029350](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211103215029350.png)

- kafka是分布式的
- 每个存储块是分区的
- Leader => Follower
- 若Leader宕机，Follower可以提升为Leader
- 一个分区只能被同一个消费者组中的不同消费者消费，提高并发能力
  - 若消费者组中仅有一个消费者，则同时消费上图的两个Topic，提高消费能力
- 最优情况为消费者组中有多少消费者，就有多少集群主机
- 储存在磁盘，而非内存

## 二、安装部署

-   版本0.11.0.0

### 2.1 配置文件

-   server.properties
    -   broker.id 唯一整数
    -   log.dir 暂时存储数据的位置
    -   log.retention.hours 过期时间
    -   ![截屏2021-11-10 09.19.25](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-10%2009.19.25.png)
    -   此处应对zookeeper进行修改，zookeeper集群的主机都应填入,逗号隔开

### 2.2 简单启动

-   kafka依赖于zookeeper，需要先启动zookeeper

-   ![截屏2021-11-10 09.23.57](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-10%2009.23.57.png)
-   ![截屏2021-11-10 09.25.27](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-10%2009.25.27.png)
-   ![截屏2021-11-10 09.25.52](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-10%2009.25.52.png)



-   启动命令为 kafka-server-start.sh server.properties
-   需要后台启动，使用守护进程参数 bin/kafka-server-start.sh -daemon config/server.properties

![截屏2021-11-10 09.41.37](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-10%2009.41.37.png)

### 2.3 群启脚本 shell

```shell
#!/bin/bash

case $1 in
"start"){
	for i in 192.168.6.138 192.168.6.139 192.168.6.140
	do
		echo ---------------------------
		ssh $i "/usr/local/kafka-0.11/bin/kafka-server-start.sh -daemon /usr/local/kafka-0.11/config/server.properties"
	done
};;

"stop"){
	for i in 192.168.6.138 192.168.6.139 192.168.6.140
	do
		echo ---------------------------
		ssh $i "/usr/local/kafka-0.11/bin/kafka-server-stop.sh"
	done
};;

esac
```

### 2.4 kafka命令行操作

-   查看所有topic
    -   kafka-topics.sh --zookeeper 192.168.6.138:2181 **--list**

-   创建topic
    -   kafka-topics.sh **--create** --zookeeper 192.168.6.138:2181 --topic xxx --replication-factor 3 --partitions 1 --topic first
        -   topic 定义topic名
        -   -- replication- factor 定义副本数<集群内主机数
        -   -- partitions 定义分区数
-   删除topic
    -   kafka-topics.sh --zookeeper 192.168.6.138:2181 **--delete** --topic first
        -   需要server.properties中设置delete.topic.enable=true 否则只是标记删除
-   发送消息
    -   kafka-console-producer.sh --broker-list 192.168.6.138:9092 --topic first
-   消费消息
    -   kafka-console-consumer.sh --zookeeper 192.168.6.138:2181 --topic first
    -   bin/kafka-console-consumer.sh --topic first --bootstrap-server 192.168.6.128:9092
        -   --from-beginning 7天内
-   查看某个topic的详细
    -   kafka-topics.sh --zookeeper 192.168.6.138:2181 --describe --topic first
-   修改分区数
    -   kafka-topics.sh --zookeeper 192.168.6.138:2181 --alter first --partitions 6

-   zookeeper中出现的其他节点，均为kafka创建的节点

## 三、Kafka架构

![image-20211110193802074](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211110193802074.png)

### 3.1 Kafka工作流程以及文件存储机制

![image-20211111095518355](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211111095518355.png)

-   kafka中消息是以topic分类的，消费者和生产者均面向topic的
-   topic是逻辑上的概念，而partition（分区）是物理上的概念

-   log文件如果超过1g，则会新创建一个
-   index文件实现快速定位

![image-20211111100446792](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211111100446792.png)

-   kafka引入了分片和索引机制，将每个partition分为多个segment
-   index和log文件的后半部分为当前segment的第一条消息的offset（偏移量）

![image-20211111100657906](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211111100657906.png)

### 3.2 Kafka生产者

#### 3.2.1 分区策略

-   分区的原因
    -   方便在集群中扩展：每个Partition可以通过调整以适应它所在的机器，而一个topic又可以有多个Partition组成，因此集群就可以适应任意大小的数据了
    -   可以提高并发：因为可以以Partition为单位读写了
-   分区的原则
    -   需要将producer发送的数据封装成一个ProducerRecord对象
    -   ![image-20211111101500801](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211111101500801.png)
        -   指明partition的情况下，直接讲指明的值作为partition值
        -   没有指明partition值但有Key的情况下，将key的hash值域topic的partition数进行取余得到partition值
        -   两者都没有的情况下，第一次调用时随机生成一个整数，后面每次调用都在这个整数上自增，将这个值与topic可用的partition总数取余得到partition值，即round-robin算法

#### 3.2.2 数据可靠性保证

-   topic的每个partition收到producer发送的数据后，都需要向producer发送ack（acknowledgement），如果producer收到ack，就会进行下一轮的发送，否则重发数据。

![image-20211111103343859](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211111103343859.png)

![image-20211111102713288](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211111102713288.png)

-   kafka选择了全部完成才发送ack
-   **ISR**
    -   第二种方案下如果leader收到数据，所有follower开始同步数据，但有一个follower因为某种故障无法与leader同步，leader就会一直等待它同步才能发送ack
    -   为了解决这个问题，Leader维护了一个动态的in-sync replica set（ISR），意为和Leader保持同步的Follower集合。当ISR中的follower完成数据同步后，leader就会给follower发送ack，如果follow而长时间未向leader同步数据，就会被踢出ISR，该时间由replica.lag.time.max.ms设定。当leader发生故障还可以从isr中选择新的LEADER
    -   入选条件：如消息差值，时间差。新版本中只保留了时间差：有zk保证了消息条数，如果生产者生产的速度超过了同步速度，则isr全被踢出去了，因此消息差不适用
-   ack应答机制
    -   对于某些不重要数据，可靠性要求不高，能够容忍少量丢失的，没有必要等ISR中所有的follower全部同步成功
    -   kafka为用户提供了三种可靠性级别，用户可用根据可靠性和延迟的要求进行权衡来选择
        -   acks参数配置
            -   0：不等待ack，延迟最低，但当broker故障时可能丢失数据
            -   1：只等待leader写完的ack，但如果follower同步之前leader故障，可能会丢失数据
            -   -1（all）：等待leader和follower全部落盘之后才会返回ack，但如果在leader发送ack之前leader出现故障，可能会造成数据重复

-   故障处理细节
    -   HW：High Watermark 消费者可见的最大值
    -   LEO：Log End Offset
    -   ![image-20211112081621423](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211112081621423.png)
    -   如果没有高损位，此时用户获取到的数据不一致
    -   follower故障：发生故障会被临时踢出isr，恢复后follower读取上次的HW并将高于HW的部分戒掉，从leader进行同步，等该follower的LEO大于该partition的HW，即follower追上leader，就可以重新加入isr了
    -   leader故障：新官上任三把火，新的leader上线后，其余的follower会把各自log中超过HW的部分截取掉
    -   只能保证副本之间的数据一致性，不能保证数据不丢失或不重复

#### 3.2.3 Exactly Once语义

-   将服务器的ack设置为-1，可以保证Producer到Server之间不会丢数据，即AtLeast Once语句。
-   若设置为0，可以保证生产者每条消息只会被发送一次，即At Most Once语句

-   AtLeast Once可以保证数据不丢失，但是不能保证数据不重复。相对的，At Most Once能保证数据不重复，但是不能保证数据不丢失。但是对于一些很重要的信息比如交易数据，即不能丢失也不能重复，即Exactly Once语句。
-   在0.11后的版本增加了一项新的特性：幂等性，即不论Producer向Server发送多少次重复数据，Server端只会持久化一条。
-   幂特性结合AtLeast Once即构成了Exactly Once语义。

-   要启用幂等性只需要将Producer的参数中enable.idompotence设置为true。
-   幂等性开启后Producer在初始化时会生成一个PID，Broker会对<PID，Partition，SeqNumber>做缓存，当有相同PID提交时，Broker只会持久化一条
-   但是PID重启就会发生变化，因此幂等性不能跨会话。

### 3.3 消费者

#### 3.3.1 消费方式

-   consumer采用pull（拉）模式从broker中读取数据。
-   push（推）模式很难适应消费速录不同的消费者，因为消息发送速率是broker决定的。它的目标是尽可能以最快的速度传递消息，但是这样很容易造成consumer来不及处理信息，典型的表现就是拒绝服务以及网络拥塞。而pull模式则可以根据consumer的消费能力以适应当前的速率消费信息
-   pull模式不足之处是，如果kafka没有数据，消费者可能会陷入循环中，一直返回空数据。针对这一点，kafka的消费者在消费数据时会传入一个时长参数timeout，如果当前没有数据可供消费，consumer会等待一段时间之后再返回，这段时长即为timeout。

#### 3.3.2 分区分配策略

-   一个consumer group中有多个consumer，一个topic有多个partition，所以必然会涉及到partition分配问题，即确定哪个partition由哪个consumer来消费
-   当消费者中消费者数量发生变化时就会触发
-   kafka有两种策略：
    -   RoundRobin 轮询 按组分
        -   ![截屏2021-11-12 10.46.42](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-12%2010.46.42.png)
        -   当成一个整体来轮询
        -   要保证当前消费者组中所有的消费组消费的主体必须一样
    -   Range   范围 按主题分 默认
        -   ![截屏2021-11-12 16.55.47](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-12%2016.55.47.png)
        -   可能会导致消费不对等的情况

#### 3.3.3 offset的维护

-   消费者组唯一确定一个offset   Group+Topic+partition
-   由于consumer在消费过程中可能出现断电宕机等故障，consumer恢复后，需要从故障前的位置继续消费，所以consumer需要实时记录自己消费到了哪个offset，以便故障恢复后继续使用
-   观察zk和kafka中的数据
    -   观察zk
        -   谁先注册，谁是controller
        -   ![image-20211115102658633](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211115102658633.png)
        -   /borkers     kafka集群
        -   /borkers/ids    集群id
        -   /consumer   消费者组
        -   ![image-20211115102850587](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211115102850587.png)
        -   ![image-20211115103016244](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211115103016244.png)
        -   ![image-20211115103052137](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211115103052137.png)
        -   默认是1s之后才会提交offset数据
        -   消费数据之后会增加offset
        -   按照组-主题-分区
    -   观察kafka本地
        -   ![image-20211115103357541](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211115103357541.png)

#### 3.3.4 消费者组案例

![image-20211115104316413](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211115104316413.png)

-   同一时间只有一个消费者接收消息
-   消费者组中消费者多于分区后
-   同一个消费者组加入新的消费者后，若超过分区数，会调用`kafka.consumer.RangeAssignor`对组成员重新分配

### 3.4 kafka高效读写数据

-   kafka是分区策略
-   单体速度也快

#### 3.4.1 顺序写磁盘

-   kafka的producer生产数据，要写到log文件中，写的过程是一只追加到文件末尾顺序写。省去了大量磁头寻址的时间。

#### 3.4.2 零复制技术

-   ![image-20211115105503514](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211115105503514.png)
-   直接从OS层面读取文件

### 3.5 zookeeper在kafka中的作用

-   Kafka集群中会有一个broker被选举为Controller，负责管理集群broker的上下线，所以要topic的分区副本分配和leader选举等工作
-   Controller的管理工作都是依赖于zk的
-   选举流程
    -   启动时先到先得，后来的会周期性的查看controller是否存在，若此时正好发现controller空了，就将自己提升为controller
    -   leader选举

![image-20211115105951673](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211115105951673.png)

#### 3.6 分区分配策略Range再分析

-   优先看谁订阅了，再看组

![image-20211115110509013](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211115110509013.png)

### 3.7 Kafka事务

-   事务可以保证kafka在Exactly Once语义的基础上，生产者和消费者可以跨分区和会话
-   由于不同的Producer的id不同，若写入同样的数据会导致各个分区中数据不一致，在0.11版本引入了事务支持

#### 3.7.1 Producer事务

-   为了跨分区跨会话的事务，需要引入一个全局唯一的Transaction ID，并将Producer获得的PID和Transaction ID绑定。这样Producer重启后就可以通过正在进行的Transaction ID 获得原来的PID
-   为了管理Transaction，Kafka引入了一个新的组件Transaction Coordinator。Producer就是通过和Transaction Coordinator交互获得Transaction ID 对应的任务状态。Transaction Coordinator还负责将事务所有写入kafka的一个内部topic，这样即使整个服务重启，由于事务状态得到保存，进行中的事务可以得到恢复从而继续进行。

#### 3.7.2 Consumer事务

-   上述事务机制主要是从Producer方面考虑，对于消费者而言，事务的保证就会相对较弱，尤其是无法保证Commit的信息被精准消费。这是由于Consumer可以通过offset访问任何信息，而且不同的Segment File生命周期不同，同一事务的消息可能会出现重启后被删除的情况

## 四、kafka API

### 4.1 Producer API

#### 4.1.1 消息发送流程

-   kafka的producer发送消息采用的是异步发送的方式。在消息发送的过程中，涉及到了两个线程-min线程和Sender线程，以及一个线程共享变量-RecordAccumulator。main线程将消息发送给RecordAccumulator，Sender线程不断从RecordAccumulator中拉去信息发送到Kafka brker中

![image-20211115235716705](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211115235716705.png)

-   Interceptors中为String-》Partitioner为byte
-   拦截器-序列化器-分区器
-   batch.size：只有数据达到这个值时，sender才会发送数据
-   linger.ms：若数据迟迟未达到限定值，sender在等待这个时间后就会直接发送数据

#### 4.1.2 异步发送API

-   引入依赖

```xml
<dependency>
  <groupId>org.apache.kafka</groupId>
  <artifactId>kafka-clients</artifactId>
  <version>0.11.0.0</version>
</dependency>
```

```java
				//kafka生产者配置信息
        Properties properties = new Properties();
        //指定连接的kafka集群
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,"192.168.6.128:9092");
        //ack应答级别
        properties.put("ack","all");
        //重试次数
        properties.put("retries",3);
        //批次大小
        properties.put("batch.size",16384);
        //等待时间
        properties.put("linger.ms",1);
        //RecordAccumulator缓冲区大小  32M
        properties.put("buffer.memory",33554432);

        properties.put("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        properties.put("value.serializer","org.apache.kafka.common.serialization.StringSerializer");

        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
        for (int i=0;i<10;i++){
            producer.send(new ProducerRecord<>("first","li:"+i));
        }

        producer.close();
```

![截屏2021-11-16 10.28.37](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-16%2010.28.37.png)

-   带回调函数的producer

```java
import org.apache.kafka.clients.producer.*;

import java.util.Properties;

public class CallBack {
    public static void main(String[] args) {

        Properties properties = new Properties();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,"192.168.6.128:9092");
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,"org.apache.kafka.common.serialization.StringSerializer");
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,"org.apache.kafka.common.serialization.StringSerializer");

        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
        for (int i = 0;i<10;i++){
            producer.send(new ProducerRecord<>("first", "li:" + i), (recordMetadata, e) -> {
                if (e == null){
                    System.out.println(recordMetadata.partition()+"--"+recordMetadata.offset());
                }
            });
        }
        producer.close();
    }
}
```



![截屏2021-11-16 15.17.15](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-16%2015.17.15.png)

-   自定义分区器

```java
import org.apache.kafka.clients.producer.Partitioner;
import org.apache.kafka.common.Cluster;

import java.util.Map;

/**
 * @author li
 */
public class MyPartitioner implements Partitioner {
    @Override
    public int partition(String s, Object o, byte[] bytes, Object o1, byte[] bytes1, Cluster cluster) {
        return 0;
    }

    @Override
    public void close() {

    }

    @Override
    public void configure(Map<String, ?> map) {

    }
}
```

-   引入，由properties引入

![截屏2021-11-16 16.48.17](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-16%2016.48.17.png)

```java
properties.put(ProducerConfig.PARTITIONER_CLASS_CONFIG,"com.li.producer.MyPartitioner");
```

#### 4.1.3 同步发送API

-   同步发送的意思就是，一条消息发送后，会阻塞当前线程，直至返回ack
-   send对象返回的是一个Future对象，根据Future对象的特点，可以实现同步发送效果，只需要在调用Future对象的get方法即可。

```java
Future<RecordMetadata> future = producer.send(new ProducerRecord<>("first","li:"+i));
//会阻塞前面的线程
RecordMetadata recordMetadata = future.get();
```

-   针对于单台机器，可以保证数据有序且不丢失

### 4.2 Consumer API

```java
import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;

import java.util.Arrays;
import java.util.Properties;

public class MyConsumer {
    public static void main(String[] args) {
        //kafka生产者配置信息
        Properties properties = new Properties();
        properties.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG,"192.168.6.128:9092");
        //开启自动提交
        properties.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG,true);
        //自动提交的延迟
        properties.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG,"1000");
        //反序列化
        properties.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG,"org.apache.kafka.common.serialization.StringDeserializer");
        properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG,"org.apache.kafka.common.serialization.StringDeserializer");
        //消费者组
        properties.put(ConsumerConfig.GROUP_ID_CONFIG,"bigdata");
        
      
        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(properties);
        while (true){
           	//设置topic
            consumer.subscribe(Arrays.asList("first","second"));
            ConsumerRecords<String, String> poll = consumer.poll(100);
            for(ConsumerRecord record:poll){
                System.out.println(record.key()+"--"+record.value());
            }
        }
    }
}
```

-   若设置topic时，topic不存在，不会强制关闭程序，会正常订阅，但是会在log4j中报警告

#### 4.2.1 重置offset

-   在配置中指定

```java
public static final String AUTO_OFFSET_RESET_CONFIG = "auto.offset.reset";
    public static final String AUTO_OFFSET_RESET_DOC = "What to do when there is no initial offset in Kafka or if the current offset does not exist any more on the server (e.g. because that data has been deleted): <ul><li>earliest: automatically reset the offset to the earliest offset<li>latest: automatically reset the offset to the latest offset</li><li>none: throw exception to the consumer if no previous offset is found for the consumer's group</li><li>anything else: throw exception to the consumer.</li></ul>";
//如果Kafka中没有初始偏移量，或者服务器上不再存在当前偏移量（例如，因为该数据已被删除），该怎么办：<ul>earliest<li>最早：自动将偏移量重置为最早偏移量设置为earliest：自动将偏移量重置为最新偏移量latest：如果没有为消费者组找到以前的偏移量，则向消费者抛出异常
```

-   消费者更换组会生效
-   消费者重启后寻找当前offset会生效
-   默认为`latest`

```java
//设置offset(重置为earliest）
properties.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG,"earliest");
```

#### 4.2.2 消费者保存offset

```java
//开启自动提交
properties.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG,true);
```

-   此配置会设置自动提交offset为true，此时访问数据后会更新offset，若设置为false，则会导致

#### 4.2.3 手动提交

-   自动提交时，程序还未处理完，此时若客户端关闭，会导致消费者丢数据
-   同步提交

```java
consumer.commitsync();
```

- 异步提交

```java
consumer.conmmitAsync(new OffsetCommitCallBack(){
  @Override
  public void onComplete(Map<TopicPartition,OffsetAndMetadata> offsets,Exception e){
    if(e != null){
      System.err.println("Commit failed for" + offsets);
    }
  }
});
```

#### 4.2.4 数据漏消费和重复消费

-   处理时间短，offset未提交客户端即关闭，下次启动就会重复消费数据

#### 4.2.5 自定义存储offset

-   0.9版本之前offset存储在zookeeper中
-   0.9版本之后offset存储在kafka的内置topic中，除此之外还可以自定义存储offset
-   offset的维护是相当繁琐的，还需要考虑消费者的Rebalace（重新定位）
-   当有新的消费者加入消费者组，已有的消费者推出消费者组或锁订阅的主题的分区发生变化就会触及到分区的重新分配，Rebalance
-   消费者发送Rebalance后，每个分区的消费者就会发生变化。因此消费者首先要获取到自己被重新分配到的分区，并且定位到每个分区最新提交的offset位置继续消费。
-   要实现自定义存储offset，需要借助ConsumerRebalanceListener

![截屏2021-11-17 10.58.07](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-17%2010.58.07.png)

## 五、自定义拦截器  Interceptor

-   对于producer而言，interceptor可以让用户在发送消息前以及producer回调逻辑前有机会对消息做一些定制化需求，比如修改消息等。同时producer允许用户指定多个interceptor按序作用于同一条消息从而形成一个拦截链。
-   org.apache.kafka.clients.producer.ProducerInterceptor
    -   configure(configs)
        -   获取配置信息和初始化数据时使用
    -   onSend(ProducerRecord)
        -   封装进send方法中，Producer确保在消息被序列化以及计算分区前调用该方法。用户可以在该方法中对戏消息做任何操作，但最好保证不要修改消息所属的topic和分区，否则会影响目标分区的计算
    -   onAcknowledgement(RecordMetadate,Exception):
        -   该方法会在消息从RecordAccumulator成功发送到Kafka Broker之后，或者在发送过程中失败时调用。并且通常都是在producer中回调逻辑触发之前。此方法运行于producer的IO线程中，如果过于复杂的逻辑会拖慢producer消息的发送效率
    -   close()
        -   关闭interceptor
        -   由于有些操作是附加在其他进程中的，因此要手动消除

```java
package com.li.interceptor;


import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.clients.producer.ProducerRecord;

import java.util.ArrayList;
import java.util.Properties;

/**
 * @author li
 */
public class InterceptorProducer {
    public static void main(String[] args) throws InterruptedException {
        //kafka生产者配置信息
        Properties properties = new Properties();
        //指定连接的kafka集群
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "192.168.6.128:9092");
        //ack应答级别
        properties.put("ack", "all");
        //重试次数
        properties.put("retries", 3);
        //批次大小
        properties.put("batch.size", 16384);
        //等待时间
        properties.put("linger.ms", 1);
        //RecordAccumulator缓冲区大小  32M
        properties.put("buffer.memory", 33554432);

        properties.put(ProducerConfig.PARTITIONER_CLASS_CONFIG, "com.li.producer.MyPartitioner");


        ArrayList<String> interceptors = new ArrayList<>();
        interceptors.add("com.li.interceptor.TimeInterceptor");
        interceptors.add("com.li.interceptor.CounterInterceptor");

        properties.put(ProducerConfig.INTERCEPTOR_CLASSES_CONFIG,interceptors);

        properties.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        properties.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
        for (int i = 0; i < 10; i++) {
            producer.send(new ProducerRecord<>("first", "li:" + i));
        }

        Thread.sleep(100);

        producer.close();
    }
}

```

```java
package com.li.interceptor;

import org.apache.kafka.clients.producer.ProducerInterceptor;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.clients.producer.RecordMetadata;

import java.util.Map;

/**
 * @author li
 */
public class CounterInterceptor implements ProducerInterceptor<String, String> {
    int success = 0;
    int error = 0;

    @Override
    public void configure(Map<String, ?> configs) {

    }

    @Override
    public ProducerRecord<String, String> onSend(ProducerRecord<String, String> record) {
        return record;
    }

    @Override
    public void onAcknowledgement(RecordMetadata metadata, Exception exception) {
        if (metadata != null) {
            success++;
        } else {
            error++;
        }
    }

    @Override
    public void close() {
        System.out.println("success:" + success);
        System.out.println("error:" + error);
    }
}
```

```java
package com.li.interceptor;

import org.apache.kafka.clients.producer.ProducerInterceptor;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.clients.producer.RecordMetadata;

import java.util.Map;

/**
 * @author li
 */
public class TimeInterceptor implements ProducerInterceptor<String,String> {
    @Override
    public void configure(Map<String, ?> configs) {

    }

    @Override
    public ProducerRecord<String, String> onSend(ProducerRecord<String, String> record) {
        //取出数值
        String value = record.value();
        //创建新的ProducerRecord对象
        return new ProducerRecord<>(record.topic(), record.partition(), record.key(), System.currentTimeMillis() + "," + value);
    }

    @Override
    public void onAcknowledgement(RecordMetadata metadata, Exception exception) {

    }

    @Override
    public void close() {

    }
}
```



