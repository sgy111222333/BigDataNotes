# 一. Kafka概述

Kafka是一个分布式的基于发布-订阅模式的消息队列 (Message Queue), 主要用于大数据处理领域. 

Kafka是一个分布式流处理平台. 

### 点对点模式(一对一)

消费者主动拉去数据, 消息收到后MQ里的数据删除

### 发布/订阅模式(一对多)

消费者消费数据之后, MQ里的数据不删除

LinkedIn公司开发

### **为什么要用Kafka?**

1. **削峰填谷**, 避免流量震荡, 避免摧毁下游系统	(TPS过高的秒杀场景, 水坝)

2. 发送方和接收方**松耦合**, 减少系统间不必要的交互

# 二. Kafka概念

### 消息(Record)

这里的消息就是指Kafka处理的主要对象

### 主题(Topic)

时承载消息的逻辑容器, 每条发布到Kafka集群的消息就是发给topic的; 可以为每个业务, 每个应用甚至每类数据都创建专属的主题 (不同Topic的消息分开存贮) .

### 生产者(Producer)

向主题发布消息的客户端应用程序, 生产者通常持续不断地向一个或多个主题发送消息. 

### 消费者(Consumer)

订阅这些主题消息的客户端应用程序. 和生产者类似, 消费者也能够同时订阅多个主题的消息. 

生产者和消费者统称客户端(Clients), 可以同时运行多个生产者和消费者实例, 这些实例会不断地生产和消费Kafka集群中的多个主题. 

### Broker(经纪人)

Kafka的服务端由Broker服务进程构成, 一个Kafka集群由多个Broker组成, 负责接受和处理客户端发送过来的请求, 以及对消息进行持久化. 通常将不同Broker放到不同的机器上, 实现了高可用.

### 副本(Replica)

实现高可用的另一个手段就是备份机制(Replication). 备份数量可以配置.

副本分类: Leader Replica 和 Follower Replica, 前者对外与客户端交互, 后者仅追随前者, 不予外界交互

而MySQL的从库可以进行读操作

### 分区(Partition)

副本可以保证数据的持久化或不丢失, 但无法解决扩展性问题. 

机器数应多于分区数

### 位移(offset)

生产者向分区写入消息, 每条消息在分区的位置信息由一个叫位移的数据来表征. 

分区位移总是从0开始, 

### 消费者组(Consumer Group)

### 重平衡(Rebalance)

一个分区在同一时间只能被一个消费者消费(除非是不同的消费者组),  

在同一个消费者组内, 只有该消费者挂了, 才能换一个消费者

### 消费者位移(Consumer Offset)



### 如何持久化



### **小结** 



## 四个核心API

Producer API

consumer API

stream API

connecter API

## 细说Topic



# 三. Kafka环境搭建

## Zookeeper在Kafka中的作用：

### Broker注册

### Topic注册

# 四. Kafka种类

### 1. Apache Kafka

### 2. Confluent Kafka

### 3. Cloudera/Hortonworks Kafka

**cloudera Kafka 简称 CDK**

# 五. Kafka版本

### 如Kafka_2,12-2.6.0



# 六. Kafka特性

### 1. 消息持久化

### 2. 高吞吐量

### 3. 扩展性

### 4. 多客户端支持

### 5. Kafka Streams

### 6. 安全机制

### 7. 数据备份

### 8. 消息支持



# 七. Kafka体系结构

# 八. Kafka关键机制

## 控制器

## 复制

Kafka把自己描述成: 分布式的, 可分区的, 可复制的提交日志服务. 

复制功能是Kafka架构的核心.

Kafka使用topic来组织数据, 每个topic的每个partition有N个副本(replicas), 其中N为Default Replication Factor, 默认为1.通过多副本机制实现故障自动转移, 当Kafka集群中有broker失效情况下仍然保证服务可用.

### AR=ISR+OSR

只有ISR才有资格竞选leader

### LEO 和 HW

只能消费HW以内的数据, 也就是说**必须备份之后才能消费**

![image-20220301163126183](C:\Users\sgy11\AppData\Roaming\Typora\typora-user-images\image-20220301163126183.png)

两种策略: 定期删除和定期压缩

想用delete.retention.ms, 必须先把cleanup.policy默认的delete改为compact



### 日志压缩

压缩后 每个Key只保留最新Value

# 九. Kafka生产者

## 发送消息的主要步骤

## 创建生产者

### 3个必要属性

bootstrap.servers

key.serializer

value.serializer

### 可能的错误

- 可重试错误: 连接错误, 无主(no leader), KafkaProducer可以被配置为自动重试 ,多次重试不行后抛出重试异常

- 无法通过重试解决: "消息太大"异常, 这类错误KafkaProducer不会重试, 直接抛出异常

## 生产者的配置

### **1. acks**

指定了必须有多少分区副本收到消息, 生产者才认为消息写入是成功的

acks=0 : 

acks=1 : 

acks=all : 

副本个数为1时, 设为all还是可能丢数据

### **2. min.insync.replicas**

最小同步副本数, 

### 3. reties

生产者从服务器收到的错误可能是临时性的错误(如no leader), 这种情况下retries可以决定生产者可以重发消息的次数

### 4. batch.size

当有多个消息需要被发送到同一个分区时, 生产者会把它们放在同一个**批次**里. 该参数指定了一个批次可以使用的内存大小(按字节数计算).

### 5. linger.ms

该参数指定了生产者在发送批次之前等待更多消息加入批次的时间. 让生产者在发送批次之前等一会儿, 使更多消息加入这个批次, 虽然增加了延迟, 但也提高了吞吐量

### 6. client.id

可以是任意字符串, 默认为""(空), 服务器用它来识别消息来源, 还可以用在日志和配额指标里.

### 7.max.in.flight.requests.per.connection

默认为5, 设置为1才可以保证发生重试时顺序不乱

### 8. request.timeout.ms

生产者在发送数据时等待服务器返回响应的时间, 默认30秒

如果等待响应超时, 生产者要么重试, 要么抛出异常

### 9. max.block.ms

指定了在调用 send() 方法或使用 partitionsFor() 方法获取元数据时生产者的阻塞时间. 

### 10. max.request.size

生产者发送的请求大小, 默认1MB. 同时Broker对可接收的消息最大值也有限制(message.max.bytes), 这两个参数最好匹配.

### 11. buffer.memory

指定生产者内存缓冲区的大小,默认32MB. 如果应用程序发送消息的速度大于从缓冲区发送到服务器的速度, 换导致空间不足. 这时 send() 方法调用要么被阻塞, 要么抛出异常. 

## 小结

### 1. 关于KafkaProducer的发送过程简图

### 引申参数配置

max.request.size < message.max.bytes < fetch.max.bytes







# 十. Kafka消费者

## 重平衡

### 1. 什么叫重平衡

重平衡期间, 所有的消费者都不能消费消息, 会造成整个消费组短暂不可用

### 2. 重平衡发生实际

订阅主题数发送变化		主题分区发送变化		消费端的消费组成员发生变化(消费者处理消息超时, 心跳超时)

### 3. 重平衡流程

## 创建消费者

### 4个必要属性

bootstrap.servers

key.deserializer

value.deserializer

group.id

## 消费者的配置

### 1. fetch.min.bytes(定量)

消费者从服务器获取记录的最小字节数, 默认为1字节

如果没有很多可用数据, 但消费者的CPU使用率却很高, 那么就需要把该属性的值设得比默认值大.

如果消费者的数量比较多, 把该属性的值设置得大一点可以降低broker 的工作负载.

### 2. fetch.max.wait.ms(定时)

默认100ms

### 3. max.partition.fetch.bytes

从每个分区里返回给消费者的最大字节数, 默认1MB, 应大于生产者的max.message.size

### 4. session.timeout.ms

会话超时, 默认10秒, 消费者于服务器断开连接超过10秒, 就被认为死亡

heartbeat.interval.ms, 心跳间隔, 默认为3秒, 必须小于session.timeout.ms

### 5. auto.offset.reset

消费者读取没有偏移量的分区或偏移量无效的情况下, 该如何处理

默认从下一条最新数据开始(latest), 或从现存最老数据开始(earliest)

### 6. enable.auto.commit

自动提交偏移量(默认true), 通过auto.commit.interval.ms控制提交频率

### **7.partition.assignment.strategy**

分区会被分配给群组里的消费者, 指定分配策略 (RangeAssignor或RoundRobinAssignor)

### 8. max.poll.records

控制单次调用 poll() 方法能返回的记录数量

### 9. request.timeout.ms

默认305秒, 超时仍未收到响应, 

### 10. max.poll.timeout.ms

调用poll()间隔最大值, 默认300秒

应保证request.timeout.ms大于max.poll.timeout.ms

### 关于不提交offset的额外说明

**消费者offset**

消费者offset会在客户端存储一份, 用于记录当前消费到的位置, 保证平常消费数据不重复, 消费者不挂

**提交offset**

就是提交到服务器端的消费者offset, 用于记录消费者挂掉或重平衡时消费到的位置, 也就是重启后应该从哪里继续

**如果从未提交过offset, 只要此程序不挂, 就不会有重复消费, 但它重启或重平衡后,会重复消费没提交offset的数据**

# 十一. Kafka命令

**zk对Kafka的作用: ** **注册broker, topic, consumer 选举控制器**

```shell
# 如果在/usr/bin下没有快捷方式，可以
cd /opt/cloudera/parcels/KAFKA/lib/kafka/bin
# 使用时将下述命令中的zookeeper以及bootstrap-server替换成对应的zk和kafka集群的节点
kafka-topics --zookeeper zk-1:2181,zk-2:2181,zk-3:2182 # zk主机名写错报UnknownHostException
			 --list		# 查看所有topic
			 #[--topic "主题名" --describe				 # 查看具体topic]
			 
```

查看消费者组

```shell
kafka-consumer-groups	--new-consumer		# 新接口,不用zk用卡夫卡自己
						--bootstrap-server kafka-1:9092,kafka-2,kafka-3:9092 #kafka主机名
						--list
						
kafka-consumer-groups	--zookeeper zk-1:2181,zk-2:2181,zk-3:2182 # 老接口,用zk
						--bootstrap-server kafka-1:9092,kafka-2,kafka-3:9092
						--list				
```

**LAG(滞后)=LEO(最大值)-CURRENT(当前值)**





查看topic中的数据条数

```shell
kafka-run-class kafka.tools.GetOffsetShell	--broker-list kafka-1:9092
											--topic "主题名"
											--time -2  # -2最小值,-1最大值LEO,两个做差得现有值
```

8. 查看某一个消费组对某一个topic的消费情况

9. 创建topic

```shell
```

10. 向指topic生产数据

```shell
kafka-console-producer 
```

11. 1 消费实时数据

```shell
kafka-console-consumer 
```

11. 2 消费所有数据 (数据太多慎用, 可以重定向到文件, 以便ctrl+c)

```shell
kafka-console-consumer 
```

11. 3 从指定offset开始消费数据

```shell
kafka-console-consumer 
```

12. 修改topic分区（**只增不减**）（分区数应小于服务器台数）

```shell
```

13. **设置topic保留时间**

```shell
```

14. 1 创建topic

```shell
```

14. 2 创建topic

```shell
```

14. 3 创建topic

```shell
```

15. 创建topic

```shell
```

16. 创建topic

```shell
```

17. 创建topic

```shell

```

18. 创建topic

```shell

```

19. 创建topic

```shell

```

### 为topic增加副本数

#### 先查看当前副本情况

```shell


```

#### 创建json文件

```json
```

#### 







Kafka数据在磁盘中的存储位置: 查看配置项log.dir

/var/local/kafka/data

# 十二. Kafka线上集群部署

### CPU和内存

都不太消耗，内存4G就够

### 磁盘

机械盘就够，原因：

- Kafka是顺序读写，机械盘随机慢，顺序快

- 即使机械盘易损坏，软件层面的备份也能解决
- RAID也不是很有必要，Kafka已有冗余机制和负载均衡

需要多少磁盘空间（磁盘用量最好不超过50%）：

要考虑：每天消息数、每条消息大小、备份数、消息留存时间、压缩比

如果不加磁盘，哪个topic占空间大，就修改它的保留时间

### 网络带宽

1台万兆网卡的机器（Kafka占其中2333兆）每小时能抗0.955TB的数据（我自己算的）

如果总流量是10TB每小时，且每个有三个副本，则大约需要30台机器才够





# 十三. Kafka扩展

# 十四. Kafka企业级配置